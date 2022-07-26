# Forkify App

Este é o projeto final de um curso de JavaScript. A aplicação foi criada do absoluto zero, usando todos os conhecimentos, técnicas e guidelines adquiridos ao longo do curso.

## Objetivo

Criar uma aplicação onde o usuário pudesse pesquisar receitas, visualizá-las, ajustar as receitas para preparar o número de porções desejado, salvar receitas para visualizar posteriormente e também adicionar novas receitas.

## Como rodar

O resultado final do projeto está [hospedado aqui](https://forkify-migliori.netlify.app/).

Para testar, basta acessar! Mas se quiser rodar da sua máquina, instale as dependências:

        > npm install

Depois, é só rodar o servidor:

        > npm start

Se o navegador não abrir automaticamente, é só abrir http://localhost:1234.

Pra pesquisar uma receita na aplicação, é preciso usar termos em inglês. Uma lista com todos os termos que podem ser pesquisados está disponível [nesse link](https://forkify-api.herokuapp.com/phrases.html).

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

Essa, sem dúvidas, foi uma das partes mais desafiadoras, porém sei que foi também uma das mais engrandecedoras. Aqui, depois de estudar um pouco sobre arquitetura de software, segui o padrão MVC conforme apresentado no curso. Na próxima parte, coloquei um diagrama para exemplificar.

---

### Desenvolvimento 1/10: Primeiras requests e views

Comecei o projeto pela parte principal: buscar uma receita na API. Nesse primeiro momento, usei a função _fetch()_ com o endpoint + um ID retirado da documentação, direto no código mesmo.

Com a request e a conversão do arquivo JSON para um objeto dando certo, eu tinha uma receita exemplo pra começar a trabalhar na renderização desse objeto no DOM.

![Renderização exemplo](./assets/renderiza%C3%A7%C3%A3o-exemplo.png)

Por fim, simulei uma busca real fazendo a aplicação escutar os eventos de _load_ e _hashchange_ para diferentes IDs de receita e só então fazer o fetch com o ID retirado do DOM.

### Desenvolvimento 2/10: Refatorando para MVC

Todo o código, até aqui, estava em um único arquivo. Comecei a refatorar o código pra respeitar a arquitetura MVC. Esse é um diagrama da implementação que foi feita nessa parte:

![Arquitetura receitas](./assets/arquitetura-exemplo-receitas.jpeg)

O arquivo que estava trabalhando passou a ser o _model.js_, e criei o arquivo _controller.js_ para ser o controlador e o arquivo _recipeView.js_ para ser a view responsável pela renderização das receitas. Em seguida, reorganizei todo o código feito até o momento da seguinte forma:

#### model.js

Esse módulo passou a ser o responsável por toda a interação com a API e pelas regras de negócio. É composto pela variável _state_ e funções que a manipulam. Tanto _state_ quanto as funções do _model.js_ são exportadas pra que o controlador possa acessá-las.

#### recipeView.js

Esse módulo foi a primeira view da aplicação. É a _class RecipeView {...}_, que contém as variáveis e métodos necessários para manipular o DOM e pegar informações dele, quando preciso. Exportar a própria classe permitiria que ela fosse manipulada e possiblitaria erros e bugs. Então, em vez disso, esse módulo exporta uma nova instância de _RecipeView_.

#### controller.js

De forma resumida, é a ponte entre o _model_ e as _views_.

Esse módulo passou a ser o responsável pela lógica da aplicação em si (não pelas regras de negócio), e controla tanto _model.js_ quanto as _views_. É basicamente o "decision maker" da aplicação. Ele importa todo o _model.js_ e também uma instância de cada _view_ (por enquanto só de _RecipeView_).

Ele inicia a aplicação, lida com os eventos escutados pelas _views_ invocando os métodos de _model.js_. Também usa o objeto _state_ (de _model.js_) e seus atributos para invocar métodos das _views_ pra que o DOM seja manipulado de acordo.

### Desenvolvimento 3/10: Aplicando o padrão Publisher-Subscriber

Pra manter a estrutura adequada, a parte de escutar os eventos precisava ficar na _recipeView_, enquanto quem de fato lidaria com esses eventos seriam as funções do _controller_, que invocariam os devidos métodos de _model.js_.

Porém, as funções que deveriam ser chamadas pelos eventos escutados pela _recipeView_ estavam em _controller.js_, e não na _recipeView_. Como a _recipeView_ poderia chamar uma função que, para a _recipeView_, não estava definida?

Para isso, implementamos o padrão **Publisher-Subscriber**. O **Publisher**, nesse caso, é o método _recipeView.addHandlerRender(handler)_. Esse método é chamado pela função _init()_ do _controller_ e passa a função _controlRecipes_ como argumento, fazendo dela o **Subscriber**.

O que acontece ao inicializar o app então é:

1. _init()_ chama _recipeView.addHandlerRender(controlRecipes)_
2. _recipeView.addHandlerRender()_ escuta os eventos de _load_ e _hashchange_ e chama a função _controlRecipes()_ quando ocorrem
3. _controlRecipes()_ pega o ID presente na URL e chama _model.loadRecipe(id)_
4. _model.loadRecipe()_ faz a requisição pra API e atribui o objeto que recebe dela a _model.state.recipe_
5. _controlRecipes()_ chama _recipeView.render(model.state.recipe)_
6. _recipeView.render()_ renderiza o objeto recebido no DOM

### Desenvolvimento 4/10: config.js e helpers.js

Um detalhe legal no meio do processo foi a criação desses 2 arquivos pra literalmente configurar e auxiliar o processo de desenvolvimento.

O _config.js_ exporta variáveis fundamentais que são usadas por muitos (ou todos) componentes da aplicação, como a URL da API, por exemplo.

O _helpers.js_ exporta funções que, da mesma forma, são comuns a vários componentes da aplicação.

### Desenvolvimento 5/10: Implementando recurso de pesquisa

Seguindo o mesmo esquema da _recipeView.js_, criei _searchView.js_ e _resultsView.js_. Responsáveis pelas _views_ da barra de pesquisa e da barra lateral onde os resultados da pesquisa devem ser mostrados, respectivamente.

A partir daqui, podemos pesquisar palavras-chave e visualizar todos os resultados na barra à esquerda. Como a renderização das receitas já estava feita, também é possível selecionar qualquer resultado da lista para visualizá-lo.

### Desenvolvimento 6/10: Melhorando a cadeia de protótipos (herança entre classes)

Nesse ponto, já existiam 3 _views_ que tinham métodos e atributos comuns entre si. Então, extraí esses elementos das _views_ e criei _View.js_ pra ser a parent class delas.

Agora, _RecipeView_, _SearchView_ e _ResultsView_ extendiam _View_. Os métodos e atributos comuns a todas as _views_ passaram a ser herdados de sua parent class, e cada _view_ manteve suas especificidades, evitando código duplicado.

### Desenvolvimento 7/10: Acrescentando paginação e possibilidade de alterar porções

Aqui surgiram duas novas _views_, que são _paginationView.js_ e _previewView.js_, seguindo a arquitetura e o padrão Publisher-Subscriber como as anteriores.

_paginationView.js_ calcula o número de páginas de acordo com os resultados da busca, e valida em qual página o _model.state_ se encontra, para então construir e renderizar o HTML adequado.

_previewView.js_ foi criada pra funcionar como uma _child class_ de _resultsView.js_ e _bookmarksView.js_. Isso porque o HTML gerado em ambas as _views_ era exatamente o mesmo. Sendo assim, o método _generateMarkup_ dessas _views_ invoca _previewView.render(data, render)_.

Esse método recebe o objeto com as informações a serem renderizadas (_data_) e, se o parâmetro _render_ for **true**, constrói e renderiza o HTML no DOM. Já se _render_ for **false**, retorna o HTML pra que ele seja renderizado depois, no momento correto.

Sobre alterar as porções, não tem nada muito especial. As quantidades dos ingredientes apenas são recalculadas de acordo com o novo número de porções.

### Desenvolvimento 8/10: Implementando um algoritmo simples pra atualização do DOM

Pensando na otimização do app, construímos uma lógica para que o DOM só fosse manipulado onde necessário.

Com esse algoritmo, em vez de usar o método _render()_ das _views_ pra "apagar" e "reconstruir" os códigos HTML toda vez, passaríamos a usar o método _update()_.

Esse novo método compara as _nodeLists_ do DOM com as _nodeLists_ geradas pelo método _generateMarkup()_ e substitui somente aquelas em que houve alguma mudança relevante para o funcionamento da aplicação.

### Desenvolvimento 9/10: Salvando receitas com localStorage

Agora já era possível marcar e desmarcar receitas, e na próxima etapa implementaremos a criação de receitas. Pra deixar esses recursos um pouco mais realistas, é importante que essas informações persistam.

Assim, o usuário pode fechar a aplicação e acessá-la posteriormente, e ainda assim visualizar as receitas marcadas/criadas por ele antes. Pra isso, usei a API de localStorage pra armazenar a array _bookmarks_. As funções _addBookmark()_ e _deleteBookmark()_ manipulam a array e setam o item no localStorage em seguida.

Adicionei também a função _init()_ pra fazer o caminho oposto e pegar a array _bookmarks_ do localStorage pra que ela pudesse ser renderizada no DOM.

### Desenvolvimento 10/10: Criação de receitas e developer key

Por fim, criei _addRecipeView.js_ pra implementar a criação de receitas por parte do usuário.

Nessa _view_, pegamos as informações inseridas nos campos para criar um objeto com a mesma estrutura dos objetos recebidos da API. A única diferença é que, para esses objetos, adicionaremos uma _developer key_ como uma de suas propriedades. Em seguida, enviamos esse objeto pra API com um método POST, e a API devolve esse mesmo objeto.

Após receber o objeto de volta, ele é renderizado no DOM e marcado automaticamente. A _developer key_ adicionada passa a compor também a URL que usamos nas requests pra API, retornando somente as receitas já existentes na API e as receitas criadas pelo próprio usuário (com a mesma _key_).

## Considerações finais

Esse projeto foi, de longe, o projeto mais complexo que já construí. Apesar de ter sido feito como parte de um curso, compreender toda a lógica, os padrões e estruturas da arquitetura, e cada parte da aplicação foi bastante desafiador!

A aplicação foi criada com HTML, CSS e JavaScript puro, usando a arquitetura MVC. Aprendi e pratiquei um pouco mais alguns princípios e práticas como:

- Manipulação do DOM
- JavaScript Assíncrono (Promises, async/await e AJAX)
- Componentização, modularização e reutilização de código
- Orientação a objetos e herança entre objetos
- API de localStorage
- Arquitetura MVC e padrão Publisher-Subscriber
- Gerenciadores de pacotes e bundlers (NPM e Parcel)
- Polyfilling
- E muitas outras coisas...

Realmente, foram muitos conhecimentos novos. Fiquei bastante orgulhoso do resultado! Se você leu até aqui, muito obrigado mesmo pela atenção :)
