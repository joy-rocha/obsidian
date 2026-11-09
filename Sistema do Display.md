- Os módulos `Bsp_*` cuidam do hardware.
- Os módulos `Decode*` interpretam os dados recebidos.
- `Imagem.c/.h` montam o conteúdo visual das telas.
- O `main.c` coordena a inicialização e o ciclo principal: MQTT, decodificação, atualização dos dados, botões e interface.
- O `Makefile` compila e vincula todos os módulos e bibliotecas necessários.

---
``` c
ASSERT/
├── Main.c
├── Makefile
│
├── BspDisplay.c
├── BspDisplay.h
├── Bsp_Button.c
├── Bsp_Button.h
│
├── Imagem.c
├── Imagem.h
│
├── DecodeBmp.c
├── DecodeMpu.c
├── Decoders.h
│
├── MqttWrapper.c
└── MqttWrapper.h
```

```bash

dentro de assert na rapi5 execute:
sudo apt install -y build-essential liblgpio-dev libmosquitto-dev libcjson-dev
make clean
make
ls
./monitor


caminho do projeto no pc: /home/joycinha/Músicas/ASSERT

```