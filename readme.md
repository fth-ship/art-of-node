# A arte do Node 
## Uma introdução ao Node.js

Esse documento é feito para leitores que conhecem um pouco das seguintes coisas:

- uma linguagem de script semelhante ao Javascript, Ruby, Python, Perl e outras. Se você esta começãndo com programação, uma leitura mais tranquila seria o [JavaScript for Cats](http://jsforcats.com/). :cat2:

- git e o github. Eles são ferramentas de colaboração com código aberto e as pessoas na comunidade do node utilizão os mesmos para compartilhar modulos. Você só precisa saber o basico. Aqui tem três execelentes tuturiais de introdução: [1](http://skli.se/2012/09/22/introduction-to-git/), [2](http://zachbruggeman.me/github-for-cats/), [3](http://opensourcerer.diy.org/)

Esse pequeno livro é um trabalho em progresso, mas eu não tenho um trabalho agora. ( Se eu estivesse, eu não teria tempo para escrever este ). Se você gostar dele, por favor considere uma doação via [gittip](https://www.gittip.com/maxogden/) então eu irei escrever mais!

[![donate](donate.png)](https://www.gittip.com/maxogden/)

## Indice de conteúdo 

- [Entendendo node](#understanding)
- [Modulos do núcleo](#core-modules)
- [Callbacks](#callbacks)
- [Events](#events) (não foi escrito ainda)
- [Streams](#streams) (não foi escrito ainda)
- [Modulos e o NPM(Node Package Manager)](#modules) (não foi escrito ainda)
- [Evoluindo de forma granular](#going-with-the-grain)
- [Apps em tempo-real](#realtime) (não foi escrito ainda)

## Entendendo o node 

Node.js é um projeto de código aberto projetado para auxiliar na escrita de programas em JavaScript que conversem com redes, arquivos do sistema ou outro I/O (entrada/saida, leitura/escrita).
É isso o que ele é. Ele é uma simples e estavel plataforma de I/O que encoraja você a escrever modulos para funcionar nele.

Quais são alguns exemplos de I/O? Bom... aqui tem um diagrama de uma aplicação que eu fiz com node e ela mostra varias fontes de I/O:

![diagrama do servidor](server-diagram.png)

If you don't understand all of the different things in the diagram it is completely okay. The point is to show that a single node process (the hexagon in the middle) can act as the broker between all of the different I/O endpoints (orange and purple represent I/O).

Se você não entende todas as diferentes coisas que estão nesse diagrama, fique tranquilo. O ponto é, mostrar um unico nó de processo (o hexagono no meio) pode agir como o corretor entre todos os diferentes pontos finais de I/O (laranja e roxo representam I/O).

Normalmente na construção desses sistemas temas as seguintes situações:

- dificuldade de programar, mas tem um super rendimento com resultados rápidos (igual escrever servidores web do zero em C)
- facil de programar mas não é o ideal em relação a velocidade/robustez (semelhando ao caso de tentar o upload de um arquivo de 5GB e o seu servidor quebrar)

A meta do Node é acertar o balanço entre estes dois: relativamente simples de entender e usar rápido o suficiente para mais casos de uso.

Node não é o tanto dos seguintes itens:

  - Um web framework (semelhante ao Rails ou Django, que são utilizadas para certos tipos de coisas)
  - Uma linguagem de programação (ele use JavaScript mas o node não é uma linguagem)
  
Instead, node is somewhere in the middle. It is:
Em vez disso, node é em algum lugar no meio. Assim:

  - Projetado para ser simples e portanto relativamente simples de entender o uso
  - Normalmente para I/O baseado em programas que precisam ser rápidos e/ou lidar com muitas conexões
  
No baixo nivel, node é descrito como uma ferramente para escrever os seguintes dois maiores tipos de programas:

  - Programas de reder ustilzando protocolos como: HTTP, TCP, UDP, DNS e SSL
  - Programas de leitura e escrita de dados para o arqivos do sistema ou processes/memôria local

O que é um programa baseado em "I/O"? Aqui estão os pontos comuns do I/O:

  - Bancos de dados (e.g. MySQL, PostgreSQL, MongoDB, Redis, CouchDB)
  - APIs (e.g. Twitter, Facebook, Apple Push Notifications)
  - Conexões HTTP/WebSocket (from users of a web app)
  - Arquivos (redimensionamento de imagem, editor de videos, rádio online)

Node faz o I/O de forma assíncrona [asynchronous](http://en.wikipedia.org/wiki/Asynchronous_I/O) para lidar com diferentes situações simultaneas. Por exemplo, se você vai até um fast food e faz o pedido de um cheesebuger você tem de imadiato o pedido feito mas não o lanche, então você espara ele ficar pronto para comer. Neste meio tempo outros pedidos estão sendo feitos na lanchonete para outras pessoas. Imagine que você tenha que esperar o registro do seu cheeseburger, bloqueando
outras pessoas porque o seu pedido tem que ser feito para o deu outra começar enquanto preparam o seu. Isso é chamado de **I/O bloqueante** porque todo o I/O (cozinhar cheeseburgers) acontece um a um enfileirando tudo. O Node, por sua vez é **não-bloqueante**, significando que os pedidos seram feitos e entregues quando estiverem prontos.  

Aqui tem uma lista de coisas divertidas que podem serem feitas com graças ao node por sua natureza não bloqueante:
  
  - Controle [flying quadcopters](http://nodecopter.com)
  - Escrever bots par ao chat IRC
  - Criar [walking biped robots](http://www.youtube.com/watch?v=jf-cEB3U2UQ)

## Módulos do Núcleo 

Primeiro eu recomendo que uma versão do node esteja instalada no seu computador. Um modo fácil para que isso aconteça e visitando [nodejs.org](http://nodejs.org) e clique em `Install`.

Node tem um pequeno grupo de modulos que vem com ele por padrão (comumente chamados como 'núcleo do node') eles são representados por suas API's publicas e você utiliza elas para escrever os seu programas. Para trabalhar com arquivos do sistema existe o módulo `fs` e para redes os módulos são `net` (TCP), `http`, `dgram` (UDP).

Em adição ao `fs` e os módulos de rede existem outros modulos no base do núcleo do node. Lá também temos um modulo assincrono para resolver consultas de DNS chamado `dns`, um módulo para pegar informações especificas do sistema, por exemplo o tmpdir e ele se chama `os`, um outro para alocação de pedaços de binários na memória chamado `buffer`, varios módulos para análise de urls e caminhos (`url`, `querystring`, `path`) entre outros. A maioria dos módulos presentes no núcleo do node suportão
os principais casos de uso dele: escrever rapidamente programas que conversem com os arquivos do sistema ou rede. 

Node lida com I/O com: callbacks, eventos, streams e módulos. Se você aprende como essas quatro coisas trabalham vocês esta habil para ir em quanlquer módulo do core do node a ter um entendimento básico sobre como a interface funciona com ele.

## Callbacks

É um dos tópicos mais importantes para se entender se você tem que entender como usatilizar o node. Quase tudo no node usa callbacks. Eles não foram inventados para o node, somente tem um uso particular com funções do JavaScript.

Callbacks são funções que serão executadas de modo assincrono, ou posteriormente. Ao passo que o código for lido de cima para baixo de modo processual, programas assincronos 
possivelmente executam funções em tempos diferentes baseado na ordem e velocidade em que requisições http ou no trabalho de arquivamento do sistema acontecem.

A diferença pode ser confusa sendo determinada quando uma função é assincrona ou não depende da execução de um grande contexto. Seque um simples exemplo de código assincrono:

```js
var myNumber = 1
function addOne() { myNumber++ } // define a função 
addOne() // executa a função
console.log(myNumber) // mostra na saida padrão o numero 2
```

O código acima define uma função e na linha seguinte chama essa função, sem esperar por nada. Onde ela é chamada imediatamente adicionando 1 a váriavel `myNumber`, então depois que a função foi chamada você espera que o numero seja 2 na váriavel `myNumber`.

Supondo que precisamos armazenar nosso numero em um arquivo chamado `number.txt`:

```js
var fs = require('fs') // `require` é uma função especial do node para requisitar os módulos 
var myNumber = undefined // não sabemos ainda qual valor esta armazenado no arquivo 

function addOne() {
  fs.readFile('./number.txt', function doneReading(err, fileContents) {
    myNumber = parseInt(fileContents)
    myNumber++
  })
}

addOne()

console.log(myNumber) // mostra na saida padrão `undefined` 
```

Porque é temos `undefined` quando pedimos para mostrar o numero dessa vez? Nesse código usamos o método `fs.readFile`, acontece de módo assincrono. Usualmente você tem que se comunicar com os discos rigidos ou redes bem de modo assincrono. Se for somente para acesso a memória ou fazer algo na CPU isso é feito bem de modo sincrono. A razão pela qual fazer o I/O assincrono. É porque o acesso ao disco é 100,000 vezes mais devagar do que comunicar-se com a memória (RAM).

Onde você executa esse programa onde as funções são imediatamente definidas, mas não executa elas imediatamente. Este é um conceito fundamental para entender sobre a programação assincrona. Onde `addOne` é chamada fora de `fs.readFile` e move-se para a proxima tarefa pronta para ser executada e não esperar o disco responder igual a `fs.readFile`. Se não tem nada para executar o node espera por pendencias de operações fs/network terminarem ou pararem de executar saindo da linha de
comando.

Onde `fs.readFile` esta completa para ler o arquivo (talves isso demore millisegundos ou segundos e até mesmo minutos dependendo de quanto o disco é rápipdo) executando a função `doneReading` e dando um erro (claro que se algum tipo de erro acontecer) e o conteúdo do arquivo.

A razão pela qual `undefined` foi mostrado no código acima é que ele é chamado dentro de `console.log` e fora de `fs.readFile` mostrando antes o valor de myNumber e não o conteúdo do arquivo.

Se você tem um pedaço de código que precisa ser executado varias e varias vezes ou um tempo depois, o primeiro passo é colocar esse pedaço de código dentro de uma função. Aonde você podera chamar sem precisar escrever ele em todas as partes que for necessario e nomeando da meneira que fique claro aquilo que esta sendo feito. Isso ajuda a dar nomes descritivos para as funções. 

Callback são somente funções que são executadas de forma tardia. A chave para a compreensão de callbacks é perceber que eles são utilizados quando você não sabe **quando** alguma operação assincrona estara completa, mas você sabe **quando** a se completara - a ultima linha da função assincrona! A ordem de cima-para-baixo que você declara para callbacks isso não é necessariamnete importante, somente a ordem lógica/hierárquica de assentamento do código. Primeiro você divide o código em funções,
e use callbacks para declarar se uma depende da outra função para encerrar.

O método `fs.readFile` é fornecido pelo node no módulo `fs`, é assincrono e acontece quando se tem um logo tempo para finalizar. Considerando o que ele faz: ele vai até o sistema operacional, que por sua vez esta rodando em um disco rígido isso pode ou não estar girando a milhares de rotações por minuto. Então ele tem que usar o lazer para ler os dados e enviar atráves das camadas de comunição do sistema de volta para o seu programa em javascript. Você da ao `fs.readFile` uma função
(conhecida como callback) que sera chamada depois de recuperar os dados do arquivos do sistema. Ela coloca os dados recuperados em uma variavel do javascript e a sua função (callback) com essa variavél, neste caso a variavel se chama `fileContents` porque ela contem o conteudo do arquivo que foi lido. 

Pense em um restaurante exemplo do tutorial lá do inicio. Em muitos restaurantes você pega um numero e coloca em sua mesa e espera por sua comida. Eles são como os callbacks. Isso deixa claro para o servidor a quem chamar quando o cheeseburger estiver pronto.

Colocando `console.log` em uma função e passando ele para um callback.

```js
var fs = require('fs')
var myNumber = undefined

function addOne(callback) {
  fs.readFile('./number.txt', function doneReading(err, fileContents) {
    myNumber = parseInt(fileContents)
    myNumber++
    callback()
  }
}

function logMyNumber() {
  console.log(myNumber)
}

addOne(logMyNumber)
```

Agora a função `logMyNumber` que é passada como argumento se torna a variavél `callback` dentro da função `addOne`. Depois que `fs.readFile` terminar a variavél `callback`
é chamada (`callback()`). Somente funções podem ser chamadas, então se você passar qualquer outra coisa diferente de uma uma função, causara um erro.

Onde uma função é chamada no javascript o código dentro dessa função é imediatamente executado. Neste caso nosso log é executado onde `callback` é atualmente a função `logMyNumber`. Lembre-se, somente se você *definiu* uma função não significa que ela sera executada. Você tem que *chamar* uma função para ela acontecer.

Para quebrar esse exemplo em mais pedaços, aqui tem uma linha do tempo de eventos que acontecem quando o seu programa é executado:

- 1: o código é analisado, isso significa que se existir algum erro de sintaxe o pragrama quebrara e sera apontado aonde isso aconteceu.
- 2: `addOne` sera chamado, onde `logMyNumber` sera passado com uma função chamada `callback`, que é o que precisa ser chamado quando `addOne` estiver completa. Imediatamante disparando o método assincrono `fs.readFile`. Essa parte do programa leva um tempo para terminar.
- 3: com nada para fazer, o node espera por um tempo até o `fs.readFile` encerrar a sua execução.
- 4: `fs.readFile` termina e chama o callbacl, `doneReading`, que incrementa o numer e imediatamente essa função `addOne` passando (seu retorno), `logMyNumber`.

Talves parte mais confusa de se programar com callbacks é como as funções são somente objetos armazenados em variaveis e passadas ao em torno do programa com diferentes nomes. Dando nomes simples e descritivos para suas variaveis faz seu código ser mais legivel para outros. Geralmente falando em programas no node onde você enxerga uma variavel como `callback` ou `cb` você assume ela como uma função. 

Você talves tenha escutado alguns termos como `programação evencionada` ou `ciclo de eventos`. Onde é referenciado da mesma maneira que `fs.readFile` foi implementada. Node primeiramente despacha a operação `fs.readFile` e espera por `fs.readFile` enviar um evento para concluir. Equanto a resposta é esperada o node vai buscando checar outras coisas. Dentro do node há uma lista de coisas a serem feitas mas não informaram ainda, então o ciclo do node acaba e retorna para a lista varias vezes
checando se o que estava sendo processado terminou. Depois do termino ele pega o que foi 'processado', e.g. callbacks que dependem desse termino são chamados. 

Aquie temos uma versão de um pseudocódigo do exemplo acima:

```js
function addOne(thenRunThisFunction) {
  waitAMinute(function waitedAMinute() {
    thenRunThisFunction()
  })
}

addOne(function thisGetsRunAfterAddOneFinishes() {})
```
Imagine que tenha 3 funções assincronas `a`, `b` e `c`. Para cada uma leva-se 1 minuto de execução e depois de terminado elas chamam um callback (que é passado como primeiro argumento). Se você tem que falar para o node 'comece executando a, depois b depois que a terminar, e executar c então b termina' isso passa a ser:

```js
a(function() {
  b(function() {
    c()
  })
})
```
Onde esse código sera executado, `a` é imediatamente executado, um minuto depois que ele teminar e chama `b`, outro minuto depois que ele teminar e chamar `c` e finalmente 
depois que 3 minutos se passaram o node para de executar desde que não tenha mais nada para fazer. Isso é definitivamente a maneira mais elegante para escrever o código acima, mas o ponto é esse se você tem esse código que espera por uma porção de código assincrono terminar onde é expressado essas dependencias colocando seu código em funções
isso é passado ao redor como callbacks.

A projeção do node requer que você pense de modo não-linear. Considerando essa lista de operações:

```
ler um arquivo
processar um arquivo
```

Se você ingenuamente transforma-se isso em um pseudocódigo você teria este resultado:

```
var file = readFile()
processFile(file)
```

Esse tipo de linearidade no código (passo-a-passo, em ordem) não é o modo como o node trabalha. Se esse código fosse executado onde `readFile` e `processFile` estão sendo chamados ao mesmo tempo. Não faria o menor sentido porque `readFile` leva um tempo para completar sua execução. Ao passo que você precisa expressar `processFile` que depende do `readFile` completo. Esta a exata finalidade dos callbacks! E por causa da forma que o JavaScript trabalha você pode escrever dependencias de
diferentes maneiras: 

```js
var fs = require('fs')
fs.readFile('movie.mp4', finishedReading)

function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
}
```

Mas você támbem pode estruturar o código dessa maneira que ira funcionar:

```js
var fs = require('fs')

function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
}

fs.readFile('movie.mp4', finishedReading)
```

Ou até mesmo assim:

```js
var fs = require('fs')

fs.readFile('movie.mp4', function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
})
```

## Events

In node if you require the [events](http://nodejs.org/api/events.html) module you can use the so-called 'event emitter' that node itself uses for all of its APIs that emit things.

Events are a common pattern in programming, known more widely as the ['observer pattern'](http://en.wikipedia.org/wiki/Observer_pattern) or 'pub/sub' (publish/subscribe). Whereas callbacks are a one-to-one relationship between the thing waiting for the callback and the thing calling the callback, events are the same exact pattern except with a many-to-many API.

Here are few common use cases for using events instead of plain callbacks:

- Chat room where you want to broadcast messages to many listeners
- Game server that needs to know when new players connect, disconnect, move, shoot and jump
- Database connector that might need to know when the database connection opens, closes or sends an error

If we were trying to write a module that connects to a chat server using only callbacks it would look like this:

```js
var chatClient = require('my-chat-client')

function onConnect() {
  // have the UI show we are connected
}

function onConnectionError(error) {
  // show error to the user
}

function onDisconnect() {
 // tell user that they have been disconnected
}

function onMessage(message) {
 // show the chat room message in the UI
}

chatClient.connect(
  'http://mychatserver.com',
  onConnect,
  onConnectionError,
  onDisconnect,
  onMessage
)
```

As you can see this is really cumbersome because of all of the functions that you have to pass in a specific order to the `.connect` function. Writing this with events would look like this:

```js
var chatClient = require('my-chat-client').connect()

chatClient.on('connect', function() {
  // have the UI show we are connected
}) 

chatClient.on('connectionError', function() {
  // show error to the user
})

chatClient.on('disconnect', function() {
  // tell user that they have been disconnected
})

chatClient.on('message', function() {
  // show the chat room message in the UI
})
```

This approach is similar to the pure-callback approach but introduces the `.on` method, which subscribes a callback to an event. This means you can choose which events you want to subscribe to from the `chatClient`. You can also subscribe to the same event multiple times with different callbacks:

```js
var chatClient = require('my-chat-client').connect()
chatClient.on('message', logMessage)
chatClient.on('message', storeMessage)

function logMessage(message) {
  console.log(message)
}

function storeMessage(message) {
  myDatabase.save(message)
}
```

MORE EVENTS CONTENT TODO

## Streams

Early on in the project the file system and network APIs had their own separate patterns for dealing with streaming I/O. For example, files in a file system have things called 'file descriptors' so the `fs` module had to have extra logic to keep track of these things whereas the network modules didn't have such a concept. Despite minor differences in semantics like these, at a fundamental level both groups of code were duplicating a lot of functionality when it came to reading data in and out. The team working on node realized that it would be confusing to have to learn two sets of semantics to essentially do the same thing so they made a new API called the `Stream` and made all the network and file system code use it. 

The whole point of node is to make it easy to deal with file systems and networks so it made sense to have one pattern that was used everywhere. The good news is that most of the patterns like these (there are only a few anyway) have been figured out at this point and it is very unlikely that node will change that much in the future.

THE REST IS TODO, in the meantime read the [streams handbook](https://github.com/substack/stream-handbook#introduction)

## Modules

TODO

## Going with the grain

Like any good tool, node is best suited for a certain set of use cases. For example: Rails, the popular web framework, is great for modeling complex [business logic](http://en.wikipedia.org/wiki/Business_logic), e.g. using code to represent real life business objects like accounts, loan, itineraries, and inventories. While it is technically possible to do the same type of thing using node, there would be definite drawbacks since node is designed for solving I/O problems and it doesn't know much about 'business logic'. Each tool focuses on different problems. Hopefully this guide will help you gain an intuitive understanding of the strengths of node so that you know when it can be useful to you.

### What is outside of node's scope?

Fundamentally node is just a tool used for managing I/O across file systems and networks, and it leaves other more fancy functionality up to third party modules. Here are some things that are outside the scope of node:

#### Web frameworks

There are a number of web frameworks built on top of node (framework meaning a bundle of solutions that attempts to address some high level problem like modeling business logic), but node is not a web framework. Web frameworks that are written using node don't always make the same kind of decisions about adding complexity, abstractions and tradeoffs that node does and may have other priorities.

#### Language syntax

Node uses JavaScript and doesn't change anything about it. Felix Geisendörfer has a pretty good write-up of the 'node style' [here](https://github.com/felixge/node-style-guide).

#### Language abstraction

When possible node will use the simplest possible way of accomplishing something. The 'fancier' you make your JavaScript the more complexity and tradeoffs you introduce. Programming is hard, especially in JS where there are 1000 solutions to every problem! It is for this reason that node tries to always pick the simplest, most universal option. If you are solving a problem that calls for a complex solution and you are unsatisfied with the 'vanilla JS solutions' that node implements, you are free to solve it inside your app or module using whichever abstractions you prefer.

A great example of this is node's use of callbacks. Early on node experimented with a feature called 'promises' that added a number of features to make async code appear more linear. It was taken out of node core for a few reasons:

- they are more complex than callbacks
- they can be implemented in userland (distributed on npm as third party modules)

Consider one of the most universal and basic things that node does: reading a file. When you read a file you want to know when errors happen, like when your hard drive dies in the middle of your read. If node had promises everyone would have to branch their code like this:

```js
fs.readFile('movie.mp4')
  .then(function(data) {
    // do stuff with data
  })
  .error(function(error) {
    // handle error
  })
```

This adds complexity, and not everyone wants that. Instead of two separate functions node just uses a single callback function. Here are the rules:

- When there is no error pass null as the first argument
- When there is an error, pass it as the first argument
- The rest of the arguments can be used for anything (usually data or responses since most stuff in node is reading or writing things)

Hence, the node callback style:

```js
fs.readFile('movie.mp4', function(err, data) {
  // handle error, do stuff with data
})
```

#### Threads/fibers/non-event-based concurrency solutions

Note: If you don't know what these things mean then you will likely have an easier time learning node, since unlearning things is just as much work as learning things.

Node uses threads internally to make things fast but doesn't expose them to the user. If you are a technical user wondering why node is designed this way then you should 100% read about [the design of libuv](http://nikhilm.github.com/uvbook/), the C++ I/O layer that node is built on top of.

## Real-time apps

TODO - this section will have a non-contrived, functioning application with a web UI whose architecture will be dissected and discussed.

## License

![CCBY](CCBY.png)

Creative Commons Attribution License (do whatever, just attribute me)
http://creativecommons.org/licenses/by/2.0/

Donate icon is from the [http://thenounproject.com/noun/donate/#icon-No285](Noun Project)
