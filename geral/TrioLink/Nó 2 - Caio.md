#### Desafio 2 — Nó MPU6050 + Camada de Comunicação

Responsável pelo nó com o sensor MPU6050 (acelerômetro e giroscópio). Além de integrar o próprio sensor e publicar/assinar os tópicos da cascata, este aluno é dono do schema MQTT e da segurança da comunicação, entregando uma biblioteca cliente simples (ex: uma função de publicar/assinar já com a segurança embutida) para os outros dois nós usarem em vez de cada um implementar o próprio wrapper MQTT
