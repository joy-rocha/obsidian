
![[Pasted image 20261017164003.png|208]]

---
O **CAN** (_Controller Area Network_) é um protocolo serial veicular e industrial onde um único barramento partilhado interliga todas as unidades de comando. Esse barramento possui os fios `CAN HIGH` e `CAN LOW` que são entrançados para anular interferências eletromagnéticas. Assim, quando um ruído externo afeta ambos os fios na mesma proporção, mantendo a diferença de tensão intacta. Seu funcionamento é baseado na diferença de tenção entre os fios, chamada **Medição Diferencial**. O receptor calcula a tensão através da fórmula:

$$V_{\text{diferencial}} = V_{\text{CAN\_H}} - V_{\text{CAN\_L}}$$

 Com base no valor do cálculo ele:
- **Bit 1 (Estado Recessivo):** Quando ambos os fios ficam na mesma faixa de tensão (diferença próxima de $0\text{V}$), o barramento fica em **estado recessivo** e o circuito lê o bit **1**.

- **Bit 0 (Estado Dominante):** Quando há uma diferença clara de tensão entre os dois fios (aproximadamente $2,5\text{V}$), o barramento entra em **estado dominante** e o circuito lê o bit **0**.