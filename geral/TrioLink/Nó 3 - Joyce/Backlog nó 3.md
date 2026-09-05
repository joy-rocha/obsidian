## 3.1 Tabela de Backlogs

| ID Backlog    | User Story                                                                                           | Critérios de aceitação                                                                                                                                                               | Prioridade | STATUS |
| ------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | ------ |
| EP-01 / US-01 | Assinar o tópico MQTT fusion/bmp mpu utilizando a camada/wrapper de comunicação padronizada do grupo | Conectar com sucesso ao Broker MQTT, utilizar as credenciais/mecanismo de segurança configurados e receber as mensagens em tempo real no tópico fusion/bmp_mpu.                      | Alta       |        |
| EP-01 / US-02 | Realizar o parser e validação do payload JSON do Nó 2                                                | Decodificar campos de temperatura, pressão, aceleração e timestamp; Tratar erros de pacotes corrompidos ou mal formatados sem interromper a execução do serviço.                     | Alta       |        |
| EP-01 / US-03 | Algoritmo de processamento para determinação do estado final do sistema                              | Processar dados recebidos e aplicar as regras de negócio combinadas e classificar o estado final do sistema (Normal, Alerta ou Crítico).                                             | Alta       |        |
| EP-01 / US-04 | Implementar rotina de timeout e resiliência à falhas do Nó 2                                         | Detectar parada de envio do Nó 2 através de um timer limite; Manter a aplicação rodando de forma estável sem travar; Alterar o estado do sistema para "Sem Sinal" ou "Offline".      | Alta       |        |
| EP-01 / US-05 | Publicar o estado consolidado no tópico fusion/final                                                 | Formatar o pacote de saída conforme o schema combinado e publicar periodicamente no tópico fusion/final para consumo da interface.                                                   | Média      |        |
| EP-02 / US-06 | Habilitar suporte ao barramento SPI e display TFT 2.4" na imagem Yocto                               | Incluir drivers e suporte a SPI/display na receita Yocto; Garantir inicialização e reconhecimento da tela durante o boot.                                                            | Alta       |        |
| EP-02 / US-07 | Construir o layout do dashboard gráfico para resolução 320x240                                       | Desenhar a estrutura base com cabeçalho, áreas para valores e caixa de status, garantindo legibilidade e boa distribuição dos elementos visuais na tela.                             | Alta       |        |
| EP-02 / US-08 | Renderizar dados em tempo real                                                                       | Atualizar leituras numéricas na tela a cada mensagem processada                                                                                                                      | Alta       |        |
| EP-02 / US-09 | Exibir sinalização visual de erro/perda de conexão no display.                                       | Alterar a interface para modo de alerta visual ao disparar o timeout de dados do EP-01/US-04 e restaurar a exibição normal automaticamente após o restabelecimento do fluxo de dados | Média      |        |

## RESUMINDO:
Eu vou receber um Json do "fusion/bmp_mpu" com os dados dos sensores vindo de outros nós, vou precisar converter esses dados para valores numéricos (float/int) e fazer um código de verificação, ele vai crzar ecomparar os dados para saber se o estado é NORMAL ou ALERT. Depois disso, vou enviá-los para o meu display via SPI em tempo real, mantendo a integridade dos dados e em casos de perca de sinal, ou seja, o temporizador chegar a zero, devo manter a conexão MQTT funcionando e exibir no display "SINAL PERDIDO ou OFFLINE" até reconectar (o temporizador reiniciar), aí dps voltar a mostrar os dados normalmente.


---
# Backlog - traduzindo:
**Product Backlog:** É a **lista de afazeres** completa do projeto. Tudo o que precisa ser planejado, programado ou testado até a entrega final fica listado aí.

**Sprint:** É uma **maratona curta de trabalho**. Em vez de tentar fazer o projeto inteiro de uma vez, o tempo é dividido em ciclos fixos (neste caso, de 15 dias). Em cada Sprint, a equipe foca apenas em entregar um pedaço específico do sistema.

**US (User Story / História de Usuário):** É uma **funcionalidade** contada da perspectiva de quem vai usar o sistema.
- Exemplo:_ US-01 pode ser _"Como usuário, quero selecionar o modelo de IA em uma lista para poder testá-lo."_

*RNF (Requisito Não-Funcional):** É uma **regra de desempenho ou limitação técnica**, e não uma tela ou botão.
- Exemplo:_ RNF-03 pode ser _"O programa não pode consumir mais de 2 GB de memória RAM durante a execução."_

**Stack:** É a **caixa de ferramentas** do projeto. Refere-se ao conjunto de linguagens, programas e bibliotecas que foram escolhidos para construir o software. 