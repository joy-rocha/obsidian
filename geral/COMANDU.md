ifconfig ou ipconfig

invadir servidor
## 🛠️ Passo a Passo para Instalar

## 1. Atualize a lista de pacotes

Antes de baixar o Nmap, atualize o sistema. Isso garante que você vai instalar a versão mais recente.

```bash
sudo apt update
```

## 2. Instale o Nmap

Agora, rode o comando abaixo para baixar e instalar a ferramenta:

```bash
sudo apt install nmap -y
```

---

## ✅ Como testar se deu certo

Após o término da instalação, você pode verificar se tudo funcionou digitando: 

```bash
nmap --version
```

O terminal vai mostrar o número da versão instalada.

---

## 🚀 Exemplos Práticos de Uso

- Descobrir dispositivos na sua rede interna:
```bash
nmap -sn 192.168.1.0/24
```
(Troque o `192.168.1.0` pelo IP padrão da sua rede se for diferente). **`0/24`**. tem que ser o final, vc sbstitui

- Escanear as portas mais comuns de um alvo:
```bash
nmap 192.168.1.1
```

- Descobrir o Sistema Operacional do dispositivo:
```bash
sudo nmap -O 192.168.1.1
```

-  Apenas o número do IP na tela:
```bash
hostname -I
```

- Mostra que tipo de dispositivo é esse IP:
```bash
sudo nmap -O [IP_DO_DISPOSITIVO]
```




# roubar  wifiiiiii

`netsh wlan show profiles` (para listar as redes) `netsh wlan show profile name="NOME-DA-REDE" key=clear` (para exibir a senha na linha _Conteúdo da Chave_).

---

**REFERÊNCIAS**
[localizar_ip](https://localizaip.com/)
[tutorial](https://youtu.be/5jU-qe6jfZQ?si=qH2bxxhRgjl7oH6F)