Vou começar abrindo o zip para ver a estrutura e os erros.

A causa foi o meu teste, que sobrescreveu o `CFLAGS` e perdeu o `-Iinclude`. Mesmo assim, vou deixar o Makefile imune a isso.

Compilou sem nenhum aviso. Agora limpo os artefatos, escrevo um README curto e gero o zip.

O zip `TrioLink_corrigido.zip` está pronto. Compilei aqui sem nenhum aviso, mas com uma versão falsa da `lgpio`, então ainda não testei no Raspberry Pi. Os testes de hardware (display, botões, GPIO) dependem de você.

**O que causava os erros**

O `Makefile` antigo, dentro de `tela/`, pedia `cJSON.c`, `DecodeBMP.c` e `DecodeMPU.c`. Esses arquivos estavam na pasta `cod/`, e dois deles com extensão maiúscula (`.C`), que o `make` não encontra. Por isso aparecia "No rule to make target" e uma cascata de erros.

**Nova organização (pasta `tela/`)**

```
tela/
├── Makefile
├── src/      main.c ui.c gfx.c lcd.c buttons.c DecodeBMP.c DecodeMPU.c
├── include/  ui.h gfx.h lcd.h buttons.h Decoders.h
├── lib/      cJSON.c cJSON.h
├── fonts/    Ubuntu-Regular.ttf (fonte de reserva)
└── site/     igual ao anterior
```

As dependências seguem o seu diagrama: `main` usa `ui`, `gfx`, `lcd` e os decoders, que usam o `cJSON`. O `buttons.c` continua no projeto porque o `main.c` depende dele.

**Outras correções**

- `.C` virou `.c` e os headers foram para `include/`.
- `DecodeMPU.c` não incluía `Decoders.h` e não checava se `accel.x/y/z` existiam, então um JSON incompleto derrubava o programa. Agora ele retorna `NULL`, e o `DecodeBMP.c` também confere se os campos são números.
- Se o caminho da fonte DejaVu não existir, o `main.c` tenta outros caminhos e a fonte da pasta `fonts/`. Você também pode usar `TRIOLINK_FONT=/caminho/fonte.ttf`.
- O novo `Makefile` guarda os `.o` em `build/` e recompila sozinho quando um `.h` muda. Os avisos do cJSON ficam silenciados.

**Para testar no Pi**

```
sudo apt install build-essential pkg-config libfreetype-dev liblgpio-dev fonts-dejavu-core
unzip TrioLink_corrigido.zip && cd TrioLink/tela
make
./monitor              # ou ./monitor --btn-debug para testar os botões
```

Se der erro de permissão no GPIO, use `sudo ./monitor`.

Não alterei a pasta `cod/` (os arquivos Python e o `cJSON` duplicado), nem apliquei o `PATCH_main.c.txt` do site. O `monitor` ainda não grava o `/tmp/triolink.json` que o site lê. Se quiser, aplico esse patch.

Se aparecer algum erro na compilação, me mande a saída do `make`.

----

https://gemini.google.com/u/1/app/c5c4c47248475869?utm_source=app_launcher&utm_medium=owned&utm_campaign=base_all