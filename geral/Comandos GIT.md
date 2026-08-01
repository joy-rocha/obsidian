# Comandos básicos de git no MINGW64 (terminal)

| git init              | transforma sua pasta atual em um repositório git, gerando uma branch principal master   | usamos apenas uma vez por projeto                                                                                                                                  |
| --------------------- | --------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| git branch -M main    | isso está RENOMEANDO a branch principal para ‘main’                                     |                                                                                                                                                                    |
| git status            | mostra os arquivos dentro do stage, em vermelho as novas alterações                     |                                                                                                                                                                    |
| git add               | envia pro stage as alterações do código para serem commitadas                           | git add nome_arquivo<br><br>-  envia o arquivo para o stage<br>    <br><br>git add . <br><br>- joga todos os arquivos alterados de uma vez só para dentro do stage |
| git commit -m         | tira a foto daquela versão do código                                                    | git commit -m "mensagem aqui"                                                                                                                                      |
| git remote add origin | Faz a ponte inicial entre a pasta do seu computador e o repositório na nuvem do GitHub. | Você só roda esse comando uma única vez no início do projeto para o Git saber para onde deve enviar os arquivos quando você pedir.                                 |
| git push              | envia os commits para o github                                                          | usamos a forma reduzida depois de definir a rota dos pushes                                                                                                        |
| git push -u origin    | envia os commits para o github                                                          | Na primeiríssima vez que você envia algo, você usa o comando longo com -u origin main.                                                                             |
| git branch            | cria uma cópia da main onde vc pode mexer que a manin ta segura, cria um ramo           | git branch nome_branch                                                                                                                                             |
| git merge             | une as alterações de uma branch com a main                                              | git merge nova-funcao                                                                                                                                              |
| git checkout -b       | serve para criar uma branch nova e já pular para dentro dela.                           | -b serve para criar a nova branch, se não colocar ele ele só entra dentro da branch <br><br>git checkout nome_branch                                               |

| origin        | É o apelido padrão do seu link do GitHub (para onde o código vai)                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| main / master | master é a branch principal, tipo a main                                                                                                                      |
| -u            | cria uma espécie de "ponte fixa" que conecta a sua máquina ao GitHub. Das próximas vezes, você só vai precisar digitar git push. pq já criou uma rota         |
| stage         | é uma área de preparação do git que empacota ele empacota as modificações dos seus arquivos para transformá-las em um único commit antes de enviar (dar push) |

  
# 1) CRIAR UM NOVO REPOSITÓRIO

git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/joy-rocha/grafos.git
git push -u origin main
 
 **OU**

git init
echo "# Meu Projeto" >> README.md
git add README.md
git commit -m "first commit"
git branch -m main

# 2) USAR UM REPOSITÓRIO EXISTENTE  

git remote add origin https://github.com/joy-rocha/grafos.git
git branch -M main
git push -u origin main

# 3) PARA CADA ALTERAÇÃO QUE EU QUEIRA SALVAR PRECISO DE:  

git add .
git commit -m "sua mensagem aqui"
git push

# 4)  MERGE, JUNÇÃO DA MAIN COM BRANCH

- para esse comando funcionar, temos que estar na main! aí juntamos com um merge e damos push para atualizar o github

git checkout main
git merge nome_da_sua_branch
git push



