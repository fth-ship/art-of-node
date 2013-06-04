# A arte do Node 
## Uma introdução ao Node.js

Esse documento é feito para leitores que conhecem um pouco das seguintes coisas:

- uma linguagem de script semelhante ao Javascript, Ruby, Python, Perl e outras. Se você esta começando com programação, uma leitura mais tranquila seria o [JavaScript for Cats](http://jsforcats.com/). :cat2:

- git e o github. Eles são ferramentas de colaboração com código aberto e as pessoas na comunidade do node utilizam os mesmos para compartilhar módulos. Você só precisa saber o básico. Aqui tem três excelentes tutoriais de introdução: [1](http://skli.se/2012/09/22/introduction-to-git/), [2](http://zachbruggeman.me/github-for-cats/), [3](http://opensourcerer.diy.org/)

Esse pequeno livro é um trabalho em progresso, mas eu não tenho um trabalho agora. ( Se eu estivesse trabalhando, eu não teria tempo para escrever este gruia ). Se você gostar dele, por favor considere uma doação via [gittip](https://www.gittip.com/maxogden/) então eu irei escrever mais!

[![donate](donate.png)](https://www.gittip.com/maxogden/)

## Índice analítico 

- [Entendendo o node](#understanding)
- [módulos do núcleo](#core-modules)
- [Callbacks](#callbacks)
- [Eventos](#events) (não foi escrito ainda)
- [Streams](#streams) (não foi escrito ainda)
- [Módulos e o NPM (Node Package Manager)](#modules) (não foi escrito ainda)
- [Evoluíndo de forma granular](#going-with-the-grain)
- [Apps em tempo-real](#realtime) (não foi escrito ainda)

## Entendendo o node 

Node.js é um projeto de código aberto projetado para auxiliar na escrita de programas em JavaScript que conversem com redes, sistema de arquivos ou outro I/O (entrada/saida, leitura/escrita).
É isso o que ele é. Ele é uma simples e estável plataforma de I/O que encoraja você a escrever módulos para funcionar nele.

Quais são alguns exemplos de I/O? Bom... aqui tem um diagrama de uma aplicação que eu fiz com node e ela mostra várias fontes de I/O:

![diagrama do servidor](server-diagram.png)

Se você não entende todas as diferentes coisas que estão nesse diagrama, fique tranquilo. O ponto é, mostrar um unico nó de processo (o hexagono no meio) pode agir como o corredor entre todos os diferentes pontos finais de I/O (laranja e roxo representam I/O).

Normalmente na construção desses sistemas temas as seguintes situações:

- dificuldade de programar, mas tem um super rendimento com resultados rápidos (igual escrever servidores web do zero em C)
- facil de programar mas não é o ideal em relação a velocidade/robustez (semelhando ao caso de tentar o upload de um arquivo de 5GB e o seu servidor quebrar)

A meta do Node é acertar o balanço entre estes dois: relativamente simples de entender e usar rápido o suficiente para mais casos de uso.

Node não é o tanto dos seguintes itens:

  - Um web framework (semelhante ao Rails ou Django, que são utilizadas para certos tipos de coisas)
  - Uma linguagem de programação (ele usa JavaScript mas o node não é uma linguagem)
  
Em vez disso, node é em algum lugar no meio. Assim:

  - Projetado para ser simples e portanto relativamente simples de entender o uso
  - Normalmente para programas baseados em I/O que precisam ser rápidos e/ou lidar com muitas conexões
  
No baixo nivel, node é descrito como uma ferramente para escrever os seguintes dois maiores tipos de programas:

  - Programas de reder ustilzando protocolos como: HTTP, TCP, UDP, DNS e SSL
  - Programas de leitura e escrita de dados para o arqivos do sistema ou processes/memôria local

O que é um programa baseado em "I/O"? Aqui estão os pontos comuns do I/O:

  - Bancos de dados (ex. MySQL, PostgreSQL, MongoDB, Redis, CouchDB)
  - APIs (ex. Twitter, Facebook, Apple Push Notifications)
  - Conexões HTTP/WebSocket (de usuários de um aplicativo web)
  - Arquivos (redimensionamento de imagem, editor de videos, rádio online)

Node faz o I/O de forma assíncrona [asynchronous](http://en.wikipedia.org/wiki/Asynchronous_I/O) para lidar com diferentes situações simultaneas. Por exemplo, se você vai até um fast food e faz o pedido de um cheesebuger você tem de imadiato o pedido feito mas não o lanche, então você espara ele ficar pronto para comer. Neste meio tempo outros pedidos estão sendo feitos na lanchonete para outras pessoas. Imagine que você tenha que esperar o registro do seu cheeseburger, bloqueando outras pessoas porque o seu pedido tem que ser feito para o de outra começar enquanto preparam o seu. Isso é chamado de **I/O bloqueante** porque todo o I/O (cozinhar cheeseburgers) acontece um a um enfileirando tudo. O Node, por sua vez é **não-bloqueante**, significando que os pedidos serão feitos e entregues quando estiverem prontos.  

Aqui tem uma lista de coisas divertidas que podem serem feitas com graças ao node por sua natureza não bloqueante:
  
  - Controle [quadicopteros voadores](http://nodecopter.com)
  - Escrever bots par ao chat IRC
  - Criar [robôs bipedes que andam](http://www.youtube.com/watch?v=jf-cEB3U2UQ)

## Módulos do Núcleo 

Primeiro eu recomendo que uma versão do node esteja instalada no seu computador. Um modo fácil para que isso aconteça e visitando [nodejs.org](http://nodejs.org) e clique em `Install`.

Node tem um pequeno grupo de módulos que vem com ele por padrão (comumente chamados como 'núcleo do node') eles são representados por suas API's publicas e você utiliza elas para escrever os seu programas. Para trabalhar com sistema de arquivos existe o módulo `fs` e para redes os módulos são `net` (TCP), `http`, `dgram` (UDP).

Em adição ao `fs` e os módulos de rede existem outros módulos na base do núcleo do node. Lá também temos um modulo assincrono para resolver consultas de DNS chamado `dns`, um módulo para pegar informações especificas do sistema, por exemplo o tmpdir e ele chama `os`, um outro para alocação de pedaços de binários na memória chamado `buffer`, varios módulos para análise de urls e caminhos (`url`, `querystring`, `path`) entre outros. A maioria dos módulos presentes no núcleo do node suportam os principais casos de uso dele: escrever rapidamente programas que conversem com os sistema de arquivos ou rede. 

Node lida com I/O com: callbacks, eventos, streams e módulos. Se você aprende como essas quatro coisas trabalham vocês esta habil para ir em quanlquer módulo do core do node a ter um entendimento básico sobre como a interface funciona com ele.

## Callbacks

É um dos tópicos mais importantes para se entender se você tem que entender como utilizar o node. Quase tudo no node usa callbacks. Eles não foram inventados para o node, somente tem um uso particular com funções do JavaScript.

Callbacks são funções que serão executadas de modo assincrono, ou posteriormente. Ao passo que o código for lido de cima para baixo de modo processual, programas assincronos possivelmente executam funções em tempos diferentes baseado na ordem e velocidade em que requisições http ou o trabalho de arquivamento do sistema acontecem.

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
var fs = require('fs') // require é uma função especial do node para requisitar os módulos 
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

Porque é que temos `undefined` quando pedimos para mostrar o numero dessa vez? Nesse código usamos o método `fs.readFile`, que acontece de módo assincrono. Usualmente você tem que se comunicar com os discos rigidos ou redes bem de modo assincrono. Se for somente para acesso a memória ou fazer algo na CPU isso é feito bem de modo sincrono. A razão pela qual fazer o I/O assincrono é porque o acesso ao disco é 100,000 vezes mais devagar do que comunicar-se com a memória (RAM).

Onde você executa um programa onde as funções são imediatamente definidas, mas não executa elas imediatamente. Este é um conceito fundamental para entender sobre a programação assincrona. Onde `addOne` é chamada fora de `fs.readFile` e move-se para a proxima tarefa pronta para ser executada e não esperar o disco responder igual a `fs.readFile`. Se não tem nada para executar o node espera por pendências de operações fs/network terminarem ou pararem de executar saindo da linha de comando.

Onde `fs.readFile` esta completa para ler o arquivo (talves isso demore millisegundos ou segundos e até mesmo minutos dependendo de quanto o disco é rápipdo) executando a função `doneReading` e dando um erro (claro que se algum tipo de erro acontecer) e o conteúdo do arquivo.

A razão pela qual `undefined` foi mostrado no código acima é que ele é chamado dentro de `console.log` e fora de `fs.readFile` mostrando o valor anterior de myNumber e não o conteúdo do arquivo.

Se você tem um pedaço de código que precisa ser executado várias e várias vezes ou um tempo depois, o primeiro passo é colocar esse pedaço de código dentro de uma função. Aonde você podera chamar sem precisar escrever ele em todas as partes que for necessário e nomeando da meneira que fique claro aquilo que esta sendo feito. Isso ajuda a dar nomes descritivos para as funções. 

Callback são somente funções que são executadas de forma tardia. A chave para a compreensão de callbacks é perceber que eles são utilizados quando você não sabe **quando** alguma operação assincrona estará completa, mas você sabe **quando** se completará - a ultima linha da função assincrona! A ordem de cima-para-baixo que você declara para callbacks isso não é necessariamnete importante, somente a ordem lógica/hierárquica de assentamento do código. Primeiro divida o código em funções e use callbacks para declarar se uma depende da outra função para encerrar.

O método `fs.readFile` é fornecido pelo node no módulo `fs`, é assincrono e acontece quando se tem um tempo curto para finalizar. Considerando o que ele faz: ele vai até o sistema operacional, que por sua vez esta rodando em um disco rígido isso pode ou não estar girando a milhares de rotações por minuto. Então ele tem que usar o lazer para ler os dados e enviar atráves das camadas de comunição do sistema de volta para o seu programa em javascript. Você da ao `fs.readFile` uma função (conhecida como callback) que sera chamada depois de recuperar os dados do sistema de arquivos. Ela coloca os dados recuperados em uma variavel do javascript e passa para sua função (callback) com essa variavél, neste caso a variavel se chama `fileContents` porque ela contém o conteúdo do arquivo que foi lido. 

Pense no restaurante do exemplo do tutorial lá do inicio. Em muitos restaurantes você pega um número e coloca em sua mesa e espera por sua comida. Eles são como os callbacks. Isso deixa claro para o servidor a quem chamar quando o cheeseburger estiver pronto.

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

Agora a função `logMyNumber` que é passada como argumento se torna a variavél `callback` dentro da função `addOne`. Depois que `fs.readFile` terminar a variavél `callback` é chamada (`callback()`). Somente funções podem ser chamadas, então se você passar qualquer outra coisa diferente de uma uma função, causará um erro.

Onde uma função é chamada no javascript o código dentro dessa função é imediatamente executado. Neste caso nosso log é executado onde `callback` é atualmente a função `logMyNumber`. Lembre-se, somente se você *definiu* uma função não significa que ela será executada. Você tem que *chamar* uma função para ela acontecer.

Para quebrar esse exemplo em mais pedaços, aqui tem uma linha do tempo de eventos que acontecem quando o seu programa é executado:

- 1: o código é analisado, isso significa que se existir algum erro de sintaxe o pragrama quebrará e será apontado aonde isso aconteceu.
- 2: `addOne` será chamado, onde `logMyNumber` será passado com uma função chamada `callback`, que é o que precisa ser chamado quando `addOne` estiver completa. Imediatamante disparando o método assincrono `fs.readFile`. Essa parte do programa leva um tempo para terminar.
- 3: com nada para fazer, o node espera por um tempo até o `fs.readFile` encerrar a sua execução.
- 4: `fs.readFile` termina e chama o callback, `doneReading`, que incrementa o numero e imediatamente chama a função `logMyNumber` contida na variável `callback`.

Talves a parte mais confusa de se programar com callbacks é que as funções são somente objetos armazenados em variáveis e passadas em todo o programa com diferentes nomes. Dando nomes simples e descritivos para suas variáveis faz seu código ser mais legível para outros. Geralmente falando em programas no node onde você enxerga uma variável como `callback` ou `cb` você assume ela como uma função. 

Você talves tenha escutado alguns termos como `programação evencionada` ou `ciclo de eventos`. Onde é referenciado da mesma maneira que `fs.readFile` foi implementada. Node primeiramente despacha a operação `fs.readFile` e espera por `fs.readFile` enviar um evento para concluir. Equanto a resposta é esperada o node vai buscando checar outras coisas. Dentro do node há uma lista de coisas a serem feitas mas não informaram ainda, então o ciclo do node acaba e retorna para a lista várias vezes checando se o que estava sendo processado terminou. Depois do termino ele pega o que foi 'processado', ex. callbacks que dependem desse termino são chamados. 

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
Onde esse código será executado, `a` é imediatamente executado, um minuto depois que ele teminar e chama `b`, outro minuto depois que ele teminar e chamar `c` e finalmente depois que 3 minutos se passaram o node para de executar desde que não tenha mais nada para fazer. Isso é definitivamente a maneira mais elegante para escrever o código acima, mas o ponto é esse, se você tem esse código que espera por uma porção de código assincrono terminar, onde é expressado essas dependencias colocando seu código em funções, isso é passado às outras funções como callbacks.

A projeção do node requer que você pense de modo não-linear. Considerando essa lista de operações:

```
ler um arquivo
processar um arquivo
```

Se você ingênuamente transforma-se isso em um pseudocódigo você teria este resultado:

```
var file = readFile()
processFile(file)
```

Esse tipo de linearidade no código (passo-a-passo, em ordem) não é o modo como o node trabalha. Se esse código fosse executado onde `readFile` e `processFile` estão sendo chamados ao mesmo tempo. Não faria o menor sentido porque `readFile` leva um tempo para completar sua execução. Ao passo que você precisa expressar `processFile` que depende do `readFile` completo. Esta a exata finalidade dos callbacks! E por causa da forma que o JavaScript trabalha você pode escrever dependencias de diferentes maneiras: 

```js
var fs = require('fs')
fs.readFile('movie.mp4', finishedReading)

function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
}
```

Mas você támbem pode estruturar o código dessa maneira que irá funcionar:

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

## Eventos 

No node com o módulo de [eventos](http://nodejs.org/api/events.html) você pode disparar eventos, também chamando de 'emissor de evento', até mesmo o node usa em todas as suas APIs para emitir coisas.

Eventos são padrões comuns de programação, para conhecer melhor procure por ['observer pattern'](http://en.wikipedia.org/wiki/Observer_pattern) ou 'pub/sub' (publicar/assinar). Ao passo que callbacks são uma relação um-para-um entre algo que espera pelo callback e outra parte que chama o callback, eventos seguem o mesmo padrão com exceção de que eles são uma API de muitos-para-muitos.

Aqui temos casos comuns de uso dos eventos ao invés de simples callbacks:

- Uma sala de chat onde você tem um canal de mensagens com muitos ouvintes.
- Servidor de um jogo que necessita saber quando os players se ligam, desligam, movem-se, atiram ou pulam
- Conectores de bancos de dados podem precisar saber onde suas conexões estão abertar, fechadas ou enviando um erro

Se você tentar escrever um servidor de chat que se conecte usando apenas callbacks ele irá parecer com isso:

```js
var chatClient = require('my-chat-client')

function onConnect() {
  // exibe a UI quando conectar-se
}

function onConnectionError(error) {
  // exibe erros para o usuario
}

function onDisconnect() {
 // avisa ao usuario que ele foi desconectado
}

function onMessage(message) {
 // exibe a mensagem na UI da sala
}

chatClient.connect(
  'http://mychatserver.com',
  onConnect,
  onConnectionError,
  onDisconnect,
  onMessage
)
```

Como você pode ver é realmente pesado escrever porque você tem que passar todas as funções em uma ordem especifica para a função `.connect`. Escrevendo isso com eventos irá se parecer com isso:

```js
var chatClient = require('my-chat-client').connect()

chatClient.on('connect', function() {
  // exibe a UI quando conectar-se
}) 

chatClient.on('connectionError', function() {
  // exibe erros para o usuario
})

chatClient.on('disconnect', function() {
  // avisa ao usuario que ele foi desconectado
})

chatClient.on('message', function() {
  // exibe a mensagem na UI da sala
})
```

Esta abordagem é bastante similar a utilização com callbacks-puros mas essa abordagem introduz o método `.on`, onde é atrelado um callback ao evento. Isso significa que você escolhe o que tem que estar assinado para `chatClient`. Você é capaz de assinar o mesmo evento várias vezes com diferentes callbacks:

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

Logo no início do projet, as APIs de sistema de arquivos e redes teve seus próprios padrões de separação para lidar com I/O de streaming. Por exemplo, arquivos em um sistema de arquivos tem propriedades que se chamam 'descritores do arquivo' então o módulo `fs` teve uma lógica adicional enquanto o módulo de rede não teve esse conceito adicionado. Apesar de diferenças menores na semâtica como estas, em um nivel fundamental os grupos de código tem uma grande quantidade de
funcionalidades duplicadas onde fazem a leitura de dados na entrada e saida. O time que esta trabalhando no node percebeu que seria confuso ter que aprender dois conjuntos de semântica, essencialmente, fazendo a mesma coisa, contanto que fizeram uma nova API chamada `Stream` e tudo o que demanda rede e sistema de arquivos usa ela como base.

Toda a questão que o node lida é facilitar a comunicaçãoo com o sistema de arquivos e redes através de um padrão que é utilizado em todos os lugares. A boa notícia é que a maioria dos padrões como esse (há apenas alguns) foram descobertos até este ponto e terão poucas mudanças mesmo que seja quase improvavel que isso aconteça. 

O RESTANTE ESTÁ EM ANDAMENTO, neste meio tempo leia o [streams handbook](https://github.com/fth-ship/stream-handbook)

## Módulos 

TODO

## Evoluindo de módo granular 

Como todo boa ferramenta, o node é bem adequado para certos casos de uso. Por exemplo: Rails, o popular web framework, é ótimo para modelar complexas [lógicas de negócio](http://en.wikipedia.org/wiki/Business_logic), ex. usando código para representar a vida em um plano objetivado que vivemos físicamente como contas, empréstimos, itinerários e inventários. Embora tecnicamente seja possivel fazer o memso utilizando o node, haveria desvantagens claras sabendo que o node é projetado para resolver problemas de I/O e não saber sobre a pate da 'lógica de negócio'. Cada ferramente tem seu foco voltado a resolver diferentes problemas. Esperamos que este guia ajude-o a ganhar uma compreensão intuitiva dos pontos fortes do node e você saberá quando ele será útil.

### O que esta fora do escopo do node?

Fundamentalmente o node é somente usado como ferramenta para gerenciar I/O ao redor do sitema de arquivos e redes, ele deixa outras funcionalidades mais bonitas com módulos de terceiros. Aqui são algumas das coisas fora do escopo do node:

#### Web frameworks

Existem uma boa quantidade de web frameworks construidos em cima do node (framework é um pacote que tenta resolver um problema de alto nível e problemas similares ao modelar a lógica de negócio), mas o node não é um framework para web. Frameworks web são escritos para serem utilizado no node e nem sempre tomam o mesmo tipo de decisões sobre a adição de complexidade, abstração e compreensão que o node faz e podem ter outras prioridades. 

#### Sintaxe da linguagem 

O node usa JavaScript e não muda nada sobre isso. Felix Geisendörfer tem uma belo conteúdo escrito sobre o 'estilo de escrita do node' [aqui](https://github.com/felixge/node-style-guide).

#### Abstração da linguagem 

Quando possivel o node vai usar a maneira mais simples de escrever algo. Código mais 'bonito' faz do seu JavaScript mais complexo e compromissado com vantagens e desvantagens. Programar é difícil, especialmente em JS onde você tem 1000 soluções para o mesmo problema! Essa é a principal razão para o node optar pela simplicidade sempre que possivel e que pode ser uma opção universal. Se você esta resolvendo um problema complexo e você esta insatisfeito com o modo que o node implementa as coisas com 'soluções de JS com gosto de baunilha' sinta-se livre para resolver isso dentro do seu app ou módulo usando qualquer abstrações que você preferir.

Um grande exemplo de como o node usa os callbacks. Logo no inicio foi experimentado a caracteristica chamada 'promessas' que adiciona algumas funcionalidades para fazer o código assincrono parecer mais linear. Ele foi levado para o fora do núcleo do node por algumas razões:

- eles são mais complexos que callbacks
- ele podem ser implementados na userland (distriuído no npm como módulo de terceiros)

Considerando uma das mais universais e básicas ideias que o node faz: ler um arquivo. Onde você lê um arquivo e precisa saber onde os erros acontecem, como exemplo quando o disco rigido morre no meio da sua leitura. Se você tem promessas tudo que você terá é um código como esse:

```js
fs.readFile('movie.mp4')
  .then(function(data) {
    // faça algo com os dados 
  })
  .error(function(error) {
    // lide com o erro 
  })
```

Isso adiciona uma complexidade, desnecessária. No lugar de duas funções separadas o node somente usa uma única função de callback. Aqui temos as régras:

- Quando não existir erros passe null como primeiro argumento
- Quando o existir erro, passar ele como primeiro argumento
- O restante dos argumentos são usados para qualquer coisa (usualmente dados ou respostas já que na maior parte do tempo o node esta lendo ou escrevendo coisas)

Por isso, o node usa o estilo de callback:

```js
fs.readFile('movie.mp4', function(err, data) {
  // lide com o erro, e faça algo com os dados 
})
```

#### Soluções baseadas em Threads/fibers/non-event

Nota: Se você não sabe o que isso tudo significa você terá uma facilidade maior com o tempo para aprender como o node funciona, visto que desaprender coisas leva o mesmo tempo que aprender. 

O node usa threads internamente para fazer coisas de uma forma rápida mas não expõe isso ao usuário. Se você é um usuário técnico e esta perguntando-se o porque ele é projetado desta maneira esta leitura é 100% sobre [A projeção da libuv](http://nikhilm.github.com/uvbook/), que onde a camada de I/O feita em C++ e pela qual o node é concebido.

## Apps em tempo-real 

TODO - esta sessão tera um aplicativo funcional com uma UI web cuja a sua arquitetura será esfacelada e discutida.

## License

![CCBY](CCBY.png)

Creative Commons Attribution License (do whatever, just attribute me)
http://creativecommons.org/licenses/by/2.0/

Donate icon is from the [http://thenounproject.com/noun/donate/#icon-No285](Noun Project)
