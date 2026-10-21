
>retirar uma página poco referenciada e onde eu tenho cache eu tenho esse peoblema de memoria chache da CPU e cache de página em servidor web

==🔴veja as vantagen se problemas de cada opção==
# Algoritmo ótimo de substituição
não tem ele mas ele é a base quanto mais os outros se aproximar desse, melhor! ele é a utopi a meta

# NUR - Não Usada Recentemente
- usa bits de referencia (R) e modificação (M)

não referenciada, não modificada : melhor vitma
referenciada, não modificada
não referenciada modificada
referenciada modificada: pior vitma

# FIFO e segunda chance
remove a página mais antiga e descarta 

# Algoritmo do relógio
é um fifo com lista duplamente encadeada e é circular

# LUR - menos usada recentemente
ruim pq se foi usado recenemente é bem provável que seja usado de novo entt fica meio esquisito esse descarte 

***CUSTO:***
possui custo computacional alto
a cada referencia a memoria a lista tem que ser atualizada
viável sócom hardware dedicado