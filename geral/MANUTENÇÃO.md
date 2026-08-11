
# PROBLEMAS FREQUÊNTES
---
### **Liga mas não dá vídeo**

- **Memórias:** Limpar os contatos  da RAM com borracha  branca ou álcool isopropílico e testar um pente por vez em slots diferentes.

- **Tela e Cabo Flat:** Em notebooks, conectar um monitor externo via HDMI isola o problema na hora. Se der vídeo na TV/monitor, a placa-mãe está boa e o defeito é na tela ou no flat.

- **BIOS:** O primeiro teste é o _Clear CMOS_ (remover a bateria CR2032 da placa por alguns minutos para resetar as configurações de fábrica).
##### ***PARA O Clear CMOS***
**1. Preparação e Segurança (Passo Obrigatório)**
- Desligue o computador completamente.
- Retire o cabo de força da fonte da tomada (no caso de notebooks, remova também a bateria principal, se for removível).
- **Descarregue a energia residual:** Pressione o botão de ligar (_Power_) da máquina por 10 a 15 segundos com tudo fora da tomada. Isso esvazia os capacitores da placa-mãe.

**2. Localização e Remoção da Bateria**
- Abra a tampa lateral do gabinete (Desktop) ou a tampa inferior (Notebook).
- Procure a bateria redonda, prateada e achatada (modelo CR2032).
- **Em Desktops:** Ela fica presa em um soquete circular. Pressione a travinha metálica na lateral do soquete com o dedo ou uma espátula plástica. A bateria vai saltar do encaixe.
- **Em Notebooks:** Pode estar em um soquete comum ou envolvida por uma fita isolante preta presa à placa por um conector de 2 fios finos (preto e vermelho). Se for por fios, desencaixe o plugue da placa com cuidado.

**3. Tempo de Espera**
- Mantenha a máquina sem a bateria por **5 a 10 minutos**. Esse tempo garante que o chip da BIOS perca totalmente a alimentação e apague as configurações salvas.

**4. Reinstalação e Reconfiguração**
- Coloque a bateria de volta no soquete com o **lado positivo (+)**, onde ficam as escritas, voltado para cima.
- Feche o computador, conecte o cabo de energia e ligue o equipamento.

> **O que esperar ao ligar:** A placa-mãe dará um boot mais demorado e exibirá uma tela preta com mensagens como _"CMOS Checksum Error"_ ou _"Press F1 to Run Setup"_. Isso indica que o reset funcionou! Pressione a tecla indicada para entrar na BIOS, ajuste a data e a hora corretas e pressione **F10** para salvar e reiniciar.



---

### **PC não liga (Teste da Fonte ATX)**
  
 **Fio Verde com o Preto:** Diagnóstico correto! Esse é o famoso "teste do clipe de papel" (liga o pino `PS_ON` ao aterramento `GND`). Desconectar a fonte de todos os componentes antes deste teste, que é feito no cabo flat principal

>Observação técnica: Se a fonte ligar com o clipe, significa que o circuito primário funciona, mas não garante 100% que ela esteja entregando a voltagem correta sob carga pesada (para isso usa-se um multímetro ou testador de fonte).

---

### **Wireless não funciona (Placa de Rede)**

1. Verificar se a placa de rede não foi desativada via tecla de atalho (`Fn + F-key`).
2. Checar se as antenas internas (fios preto e branco) estão bem encaixadas na plaquinha Wi-Fi.
3. Reinstalar ou atualizar o driver de rede.

---


### **Wi-fi ou Ethernet não funciona**

No Windows, pesquise por **CMD** -> clique com o botão direito e selecione **Executar como Administrador** -> digite os comandos

```
netsh winsock reset 
netsh int ip reset 
ipconfig /flushdns 
```

>**Importante:** A alteração só entra em vigor após **reiniciar o computador**. Se após esse procedimento e o reboot a internet não voltar, a causa raiz deixa de ser o sistema operacional e passa a ser driver incorreto, hardware defeituoso ou problema no próprio roteador. Estes comandos NÃO apagam as senhas de Wi-Fi salvas.

$-$ o que cada linha faz e representa:
- `netsh winsock reset`: Restaura a integração dos programas com a rede
- `netsh int ip reset`: Reseta as configurações do protocolo de internet (TCP/IP) para o padrão de fábrica
- `ipconfig /flushdns`: Apaga a memória temporária de endereços de sites (DNS) para resolver falhas de carregamento

---

### **Teclado não funciona**

**Teste rápido:** Tente entrar na BIOS ao ligar o PC. Se funcionar lá, o problema é software/driver. Se não funcionar, é hardware. Em notebooks, desconectar o flat do teclado, limpar com álcool isopropílico e reencaixar.

---

### **Não carrega a bateria**
Testar o carregador com um multímetro para ver se ele entrega a tensão descrita na etiqueta (ex: 19V).

Inspecionar o **DC Jack** (conector de energia da placa) para ver se há folga ou pino quebrado.
 
 Testar a saúde do componente (baterias possuem vida útil limitada em ciclos).




---

#### ***REFERÊNCIAS***
[CURSO - manutenção de notebooks](https://youtu.be/05U9x_7geNg?si=dIuKZs1NSNK4KjQc)

