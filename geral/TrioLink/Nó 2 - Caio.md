#### Desafio 2 — Nó MPU6050 + Camada de Comunicação

Responsável pelo nó com o sensor MPU6050 (acelerômetro e giroscópio). Além de integrar o próprio sensor e publicar/assinar os tópicos da cascata, este aluno é dono do schema MQTT e da segurança da comunicação, entregando uma biblioteca cliente simples (ex: uma função de publicar/assinar já com a segurança embutida) para os outros dois nós usarem em vez de cada um implementar o próprio wrapper MQTT


- - - 

# MPU6050 
O MPU6050 é um sensor de movimento de 6 eixos (IMU - _Inertial Measurement Unit_) desenvolvido pela InvenSense, que integra um giroscópio de 3 eixos e um acelerômetro de 3 eixos no mesmo chip, além de um processador digital de movimento (DMP) embutido.

![[Pasted image 20260908160834.png|141]]

|**Pino**|**Tipo**|**Função / Descrição**|
|---|---|---|
|**VCC**|Alimentação|Entrada de energia (geralmente 3,3V a 5V devido ao regulador integrado).|
|**GND**|Alimentação|Conexão de terra / referência (0V).|
|**SCL**|Entrada|Linha de clock para a comunicação I2C principal com o microcontrolador.|
|**SDA**|Entrada / Saída|Linha de dados para a comunicação I2C principal com o microcontrolador.|
|**XDA**|Entrada / Saída|Linha de dados do barramento I2C auxiliar (usado para conectar sensores externos, como magnetômetros).|
|**XCL**|Saída|Linha de clock do barramento I2C auxiliar.|
|**AD0**|Entrada|Seletor de endereço I2C. Conectado ao GND define o endereço como `0x68`; conectado ao VCC define como `0x69`.|
|**INT**|Saída|Pino de interrupção para notificar o microcontrolador quando novos dados estão prontos para leitura.|
