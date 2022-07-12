# Forkify App

Este é o projeto final de um curso de JavaScript. A aplicação foi criada do absoluto zero, usando todos os conhecimentos, técnicas e guidelines adquiridos ao longo do curso.

## Objetivo

Criar uma aplicação onde o usuário pudesse pesquisar receitas, visualizá-las, ajustar as receitas para preparar o número de porções desejado, salvar receitas para visualizar posteriormente e também adicionar novas receitas.

## Como rodar

O resultado final do projeto está [hospedado aqui](https://forkify-migliori.netlify.app/).

Para testar, basta acessar. =)

## Como esse projeto foi feito?

A aplicação foi criada com as linguagens básicas do desenvolvimento web (HTML, CSS e JS) e consome uma API de receitas chamada [Forkify](https://forkify-api.herokuapp.com/v2). Esse foi um dos projetos que mais gostei de construir até o momento, então detalhei melhor todo o processo a seguir!

### Planejamento 1/4: User stories

Nessa primeira parte, pra começar a entender como a aplicação seria e o que ela deveria fazer, usei a técnica de **user stories**. Ela consiste, basicamente, em colocar-se na posição de usuário e descrever o app sob a perspectiva dele. Por exemplo:

> Como um usuário, quero pesquisar receitas para encontrar novas ideias de pratos.
>
> Como um usuário, quero escolher o número de porções para poder preparar meus pratos para diferentes números de pessoas.
>
> Como um usuário, quero criar minhas próprias receitas, para mantê-las organizadas no mesmo app.
>
> Como um usuário, quero guardar receitas para poder revisitá-las depois.

### Planejamento 2/4: Features

Com base nas histórias dos usuários, o próximo passo foi transformá-las em features que a aplicação deveria ter:

> #### Pesquisar receitas
>
> - Um input que envia requests para a API com as palavras-chave inseridas
> - Apresentar os resultados da request, com paginação (se necessário)
> - Visualização da receita com o tempo de preparo, porções e ingredientes

> #### Escolher porções
>
> - Atualizar as quantidades de ingredientes de acordo com o número de porções selecionado

> #### Criar receitas
>
> - Campo para o usuário enviar a sua receita
> - Guardar as receitas enviadas automaticamente
> - O usuário pode ver suas próprias receitas, mas outros usuários não podem

> #### Guardar receitas
>
> - Opção de marcar/desmarcar receitas
> - Apresentar uma lista com as receitas marcadas pelo usuário
> - Armazenar as receitas no navegador usando localStorage
> - Quando a página do app for carregada, ler os dados do localStorage e renderizá-los

### Planejamento 3/4: Fluxograma

Com as features em mente, o próximo passo foi estruturá-las de maneira coerente. Para isso, criei um fluxograma do app para me guiar. Vale ressaltar que, ao longo do desenvolvimento do app, o fluxograma foi ajustado e melhorado até chegar nessa versão completa:

![Fluxograma final](./assets/fluxograma-final.jpeg)

### Planejamento 4/4: Arquitetura

Essa, sem dúvidas, foi uma das partes mais desafiadoras, porém sei que foi também uma das mais engrandecedoras. Aqui, depois de estudar um pouco sobre arquitetura de software, segui o padrão MVC conforme apresentado no curso. Esse é um diagrama da implementação que foi feita na parte que mostra as receitas:

![Arquitetura receitas](./assets/arquitetura-exemplo-receitas.jpeg)
