- processos possuem um espaço de memoria diferente (não compartilham memória)

**ATENÇÃO:**
-tem o lance pra instalar lá, a biblioteca, o gcc tbm
-tem que aprender comandos de terminal linux
-estude a teoria e aplique a pratica no roteiro
-tudo que fizer lança no github

**PID** - código identificador do processo criado
**PPID** - código identificador do processo PAI 
# COMANDO USADOS NA AULA
**waith(NULL)** - espera que todos os procesos filhos acabarem pra chamar o pai
**fork()** - cria (duplica) um processo
**execlp()** - sobrescreve um codigo objeto em um determinado processo
**getpid()** - pega o identificador 
**getppid()** - pega o identificador do pai
**sleep()** - time de espera

./lab
cat /sla/status | -10
### **zumbi e órfão**
-  zumbi: é quando o filho terminou o que tinha que fazer mas o pai não chamou o wait, entt a droga do processo fica ocupando o espaço na tabela de processos

-  órfão: é quando o processo pai termina antes do filho


