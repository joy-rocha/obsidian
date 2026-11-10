---

---

---
# Parte 1: BspGpio

```c
#include "BspGpio.h"
#include <stdio.h>
int main(void)
{
bool lvl = false;
printf("init: %d\n", BspGpio_Init());
BspGpio_Read(eBSP_GPIO_BTN_ENTER, &lvl);
printf("enter (1 = solto): %d\n", lvl);
BspGpio_Deinit();
return 0;
}
```

instale:
```bash
sudo apt update && sudo apt install -y libgpiod-dev pkg-config
```

Depois confira a versão, porque o meu código usa a **API v2**:
```bash
pkg-config --modversion libgpiod
```

- Se aparecer **2.x**, está tudo certo.
- Se aparecer **1.x**, o código não compila. No Ubuntu mais antigo o apt instala a v1, e o `BspGpio.c` precisaria ser reescrito para essa versão

compile
```bash
gcc -I../inc teste.c BspGpio.c -o teste $(pkg-config --cflags --libs libgpiod) && ./teste
```

---
# Parte 2: BspBttons

```c
#include "BspGpio.h"
#include "BspButton.h"
#include <stdio.h>
#include <unistd.h>

int main(void)
{
    bspButtonEvent_t event;
    if((BspGpio_Init() == false) || (BspButton_Init() == false))
    {
        printf("falha na inicializacao\n");
        return 1;
    }
    printf("Aperte os botoes (Ctrl+C para sair)\n");
    while(1)
    {
        BspButton_Poll();
        event = BspButton_GetEvent();
        if(event == eBSP_BUTTON_EVENT_SELECT)
        {
            printf("SELECIONAR\n");
        }
        else if(event == eBSP_BUTTON_EVENT_ENTER)
        {
            printf("ENTER\n");
        }
        usleep(dBSP_BUTTON_POLL_PERIOD_MS * 1000);
    }
    return 0;
}
```

```bash
gcc -I../inc teste.c BspGpio.c BspButton.c -o teste $(pkg-config --cflags --libs libgpiod) && ./teste
```

---
# Parte3: Fusion



O `Fusion` conecta ao broker MQTT, faz o parse dos JSONs com cJSON, aplica a regra de staleness e a análise normal/anormal. A lib mosquitto roda em uma thread própria, então os dados ficam protegidos por mutex e a tela só consulta por `Fusion_GetBmp()` e `Fusion_GetMpu()`.

Decisões:

- **Assinaturas:** `sensor/bmp/raw` (QoS 0), `fusion/bmp_mpu` (QoS 1), `status/rasp1` e `status/rasp2` (QoS 1). A tela BMP usa os dados do `sensor/bmp/raw` e a tela MPU usa o objeto `mpu` do `fusion/bmp_mpu`.
- **Publicação:** o Nó 3 publica `status/rasp3` (retido, QoS 1) como ONLINE ao conectar. O broker publica OFFLINE sozinho se ele cair (Last Will). O `fusion/final` ficou de fora porque este projeto só pede a exibição, e o cálculo de velocidade e direção não está especificado.
- **Staleness:** o tempo é medido com o relógio local no momento do recebimento, usando 3x o intervalo esperado (1 s por padrão, ajustável nos defines).
- **Conexão:** `connected` é `true` quando o `Status` recebido é `"ONLINE"`. Se o Nó 3 perder o broker, os dois indicadores voltam para offline.
- **Análise normal/anormal:** faixas simples para ambiente comum, definidas no topo do `Fusion.c`:
    - BMP: temperatura de -10 a 50 °C e pressão de 80000 a 110000 Pa.
    - MPU: módulo da aceleração até 20 m/s² e do giroscópio até 10 rad/s. Valores NaN ou infinitos também são anormais.



Instale as libs e o broker (se ainda não instalou):

```bash
sudo apt install -y libmosquitto-dev libcjson-dev mosquitto mosquitto-clients
sudo systemctl enable --now mosquitto
```

Troque o `teste.c` por este:

```c
#include "Fusion.h"
#include <stdio.h>
#include <unistd.h>

int main(void)
{
    fusionBmp_t bmp;
    fusionMpu_t mpu;

    if(Fusion_Init("localhost", 1883) == false)
    {
        printf("falha no Fusion_Init\n");
        return 1;
    }

    while(1)
    {
        Fusion_GetBmp(&bmp);
        Fusion_GetMpu(&mpu);

        printf("BMP: data=%d on=%d stale=%d saude=%d T=%.2f P=%.1f\n",
               bmp.hasData, bmp.connected, bmp.stale, bmp.health,
               bmp.temperature, bmp.pressure);
        printf("MPU: data=%d on=%d stale=%d saude=%d ax=%.2f ay=%.2f az=%.2f\n\n",
               mpu.hasData, mpu.connected, mpu.stale, mpu.health,
               mpu.accelX, mpu.accelY, mpu.accelZ);

        sleep(1);
    }

    return 0;
}
```

```bash
gcc -I../inc teste.c Fusion.c -o teste $(pkg-config --cflags --libs libcjson libmosquitto) -lpthread && ./teste
```

Em **outro terminal**, publique os dados de exemplo:

```bash
# Status dos nos 1 e 2
mosquitto_pub -t status/rasp1 -q 1 -r -m '{"timestamp":1789335000123,"Status":"ONLINE"}'
mosquitto_pub -t status/rasp2 -q 1 -r -m '{"timestamp":1789335000123,"Status":"ONLINE"}'

# BMP normal
mosquitto_pub -t sensor/bmp/raw -m '{"timestamp":1789335000123,"temperature":25.43,"pressure":101325.2,"altitude":12.7}'

# MPU normal (dentro de fusion/bmp_mpu)
mosquitto_pub -t fusion/bmp_mpu -q 1 -m '{"timestamp":1789335000123,"bmp":{"timestamp":1789335000123,"temperature":25.43,"pressure":101325.2,"altitude":12.7,"stale":false},"mpu":{"timestamp":1789335000123,"accel":{"x":0.12,"y":-0.03,"z":9.78},"gyro":{"x":0.01,"y":-0.02,"z":0.04}}}'

# BMP anormal (100 °C)
mosquitto_pub -t sensor/bmp/raw -m '{"timestamp":1789335000123,"temperature":100.0,"pressure":101325.2,"altitude":12.7}'
```

O esperado:

- Antes de publicar, os dois mostram `data=0 on=0 stale=1`.
- Depois do status, `on=1`.
- Depois do BMP e do MPU normais, `data=1 stale=0 saude=0` (0 = normal).
- Com 100 °C, `saude=1` (anormal).
- Se parar de publicar por mais de 3 s, `stale=1` volta a aparecer, mantendo o último valor.
- Para testar a desconexão, publique `{"timestamp":1,"Status":"OFFLINE"}` em `status/rasp1` e veja `on=0`.

Se os nós 1 e 2 ficarem em outras máquinas, o mosquitto precisa aceitar conexões da rede. Eu explico essa configuração na Parte 5.

Quando testar, me manda **"próxima"** (ou o erro, se aparecer) que eu envio a **Parte 4: BspScreen** (GTK4 com a tela inicial, a tela BMP e a tela MPU).

---
# Parte4: BspScreen

```c
#include "BspGpio.h"
#include "BspButton.h"
#include "BspScreen.h"
#include "Fusion.h"

int main(int argc, char **argv)
{
    int status = 1;

    if((BspGpio_Init() == true)
       && (BspButton_Init() == true)
       && (Fusion_Init("localhost", 1883) == true))
    {
        status = BspScreen_Run(argc, argv);
        Fusion_Deinit();
    }

    BspGpio_Deinit();
    return status;
}
```

```bash
gcc -I../inc teste.c BspGpio.c BspButton.c Fusion.c BspScreen.c -o teste $(pkg-config --cflags --libs gtk4 libgpiod libcjson libmosquitto) -lpthread && ./teste
```

---
