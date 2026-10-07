>O monitor é uma construção de alto nível (um módulo ou pacote) que agrupa variáveis compartilhadas e procedimentos. O código externo não consegue alterar os dados diretamente, apenas por meio das chamadas dos procedimentos do monitor.
	OU SEJA, um pacote que encapsula a região crítica ... ele toma conta de quem dorme e acorda na hora certa

$-$ Problema: a linguagem já tem que nascer com a palavra chave monitores. Os monitores precisam ser um elemento nativo da linguagem de programação, cabendo ao próprio **compilador** garantir a exclusão mútua.

#### atenção: 
>- Somente um processo deve estar ativo dentro do monitor, é assim que ele garante a exclusão mútua.
>- Se um processo já estiver executando um procedimento dentro do monitor, qualquer outro processo que tentar chamar um procedimento do mesmo monitor ficará automaticamente bloqueado na entrada pelo compilador

#### Wiat/Signal: 
**são diretivas para dizer quando o buffer ta vazio ou cheio.**

- **Wait(condicao)** **:** O processo que a chama fica bloqueado e **solta a trava do monitor** temporariamente para que outros possam entrar
- **Signal(condicao)** **:** Acorda um processo que estava esperando naquela condição


---

# Troca de Mensagens (_Message Passing_)

**`send` envia e `receive` recebe. Se der erro, retorna aviso ou dorme.**
>  São as duas primitivas básicas de comunicação em sistemas distribuídos ou sem memória compartilhada. Se um processo chama `receive` e nenhuma mensagem está disponível, ele pode **bloquear (dormir)** até a mensagem chegar, ou a chamada pode **retornar um código de erro/status imediatamente** (modo não-bloqueante)

**Confirmação de recebimento (ACK) e retransmissão em caso de perda.**
>  Como a comunicação pode falhar (seja por perda da mensagem ou perda do sinal de confirmação/ACK), o emissor utiliza um _timer_ e retransmite a mensagem caso não receba a confirmação dentro do tempo limite.

**O receptor descarta mensagens duplicadas.**
>  Se o ACK for perdido no caminho de volta, o emissor retransmitirá a mensagem. Para evitar processar o mesmo comando duas vezes, o receptor usa um **número de sequência** em cada mensagem para identificar e descartar duplicatas.


|Conceito|Mecanismo Principal|Vantagens|Desvantagens / Cuidados|
|:--|:--|:--|:--|
|**Monitores**|Encapsulamento em módulo/classe com exclusão mútua garantida pelo **compilador**. Usa variáveis de condição (`wait`/`signal`).|Remove o risco do programador esquecer de liberar uma trava (mais seguro).|Exige suporte nativo da linguagem de programação (ex: Java `synchronized`, mas ausente em C).|
|**Troca de Mensagens**|Primitivas `send()` e `receive()` mediadas pelo Sistema Operacional.|Funciona tanto em uma mesma máquina quanto em **sistemas distribuídos** sem memória compartilhada.|Exige tratar perda de mensagens com **ACK**, retransmissão e descarte de duplicatas.|

---
