 ---
 
 **Instalando o MySQL direto no Ubuntu**
```bash
# Atualize a lista de pacotes
sudo apt update

# Instale o servidor do MySQL
sudo apt install mysql-server -y
```

Após a instalação, o serviço iniciará automaticamente. Você pode acessar o banco pela primeira vez como administrador digitando:
```bash
sudo mysql
```

**Dados que configurei o banco:**
- **Host:** `localhost`
- **Port:** `3306` (já vem padrão)
- **User:** `root`
- **Password:** ' ' (sem senha)

# O que fazer se eu "quebrar" o MySQL?
Se você fizer alguma configuração errada nos arquivos internos do MySQL e ele parar de funcionar, a solução no Ubuntu é extremamente simples. Você não precisa formatar o PC, basta desinstalar e reinstalar o MySQL limpo com dois comandos no terminal:

```bash
# Remove o MySQL e apaga todas as configurações antigas dele
sudo apt purge mysql-server mysql-common mysql-client -y
sudo apt autoremove -y

# Instala ele novinho do zero
sudo apt install mysql-server -y
```

---