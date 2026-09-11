É um pacote que encapsula a região crítica ... ele toma conta de quem dorme e acorda na hora certa

$-$ Problema: a linguagem já tem que nascer com a palavra chave monitores

#### atenção: 
>somente um processo deve estar ativo dentro do monitor, é assim que ele garante a exclusão mútua

#### Wiat/Signal: 
são diretivas para dizer quando o bufferta vazio ou cheio

---
# Troca de Mensagem
- se dê erro de retornar um aviso de erro ou dormir
- confirmação de recebimento (ack)
- send: envia uma mensagem
- recive: recebe uma mensagem

$-$ Quando a mensagem é enviada e não recebe a mensagem de confirmação ele reenvia a mesma mesagem até que receba a confirmação.

$-$ Já quando o receptor lê duas mensagem iguais ele descarta uma delas pois identifica que esta já foi lida