# Dominando Node.js

### O que é Node.js?

O Node.js pode ser definido como um ambiente de execução Javascript server-side.

Isso significa que com o Node.js é possível criar aplicações Javascript para rodar
como uma aplicação standalone em uma máquina, não dependendo de um browser para a
execução, como estamos acostumados.

Obs.: O Node é uma plataforma, o que é diferente de ser uma linguagem ou um framework.
Plataforma é uma tecnologia onde vai hospedar aplicações que podem ser frameworks (Express)
que utiliza a linguagem JavaScript

### Características

- [Google v8](#o-google-v8)
- [Single-thread](#o-nodejs-é-single-thread)
- [Non-blocking I/O](#non-blocking-io)
- [Arquitetura Event-loop](#arquitetura-event-loop)

#### O Google V8

O [V8](https://v8.dev/) é uma engine criada pela Google para ser usada no browser chrome.
Em 2008 a Google tornou o V8 open source e passou a chamá-lo de Chromium project.
Essa mudança possibilitou que a comunidade entendesse a engine em sí, além de
compreender como o javascript é interpretado e compilado por esta.

O javascript é uma linguagem interpretada, o que o coloca em desvantagem quando comparado com
linguagens compiladas, pois cada linha de código precisa ser interpretada enquanto o código é
executado. O V8 compila o código para linguagem de máquina, além de otimizar drasticamente a
execução usando heurísticas, permitindo que a execução seja feita em cima do código compilado
e não interpretado.

Fonte: [Node.js: V8, Single thread e I/O não bloqueante. - Waldemar Neto](https://walde.co/2016/11/27/node-js-v8-single-thread-e-o-que-e-esse-event-loop-afinal/)

#### O Node.js é single thread

A primeira vista o modelo single thread parece não fazer sentido, qual seria a vantagem de
limitar a execução da aplicação em somente uma thread? Linguagens como Java, PHP e Ruby
seguem um modelo onde cada nova requisição roda em uma thread separada do sistema
operacional. Esse modelo é eficiente mas tem um custo de recursos muito alto, nem sempre é
necessário todo o recurso computacional aplicado para executar uma nova thread.
O Node.js foi criado para solucionar esse problema, usar programação assíncrona e
recursos compartilhados para tirar maior proveito de uma thread.

O cenário mais comum é um servidor web que recebe milhões de requisições por segundo;
Se o servidor iniciar uma nova thread para cada requisição vai gerar um alto custo de
recursos e cada vez mais será necessário adicionar novos servidores para suportar a
demanda. O modelo assíncrono single thread consegue processar mais requisições
concorrentes do que o exemplo anterior, com um número bem menor de recursos.
Ser single thread não significa que o Node.js não usa threads internamente, para
entender mais sobre essa parte devemos primeiro entender o conceito de Non-blocking I/O.

Fonte: [Node.js: V8, Single thread e I/O não bloqueante. - Waldemar Neto](https://walde.co/2016/11/27/node-js-v8-single-thread-e-o-que-e-esse-event-loop-afinal/)

#### Non-blocking I/O

Essa talvez seja a característica mais poderosa do Node.js, trabalhar de forma
não bloqueante facilita a execução paralela e o aproveitamento de recursos.
Para entender melhor, vamos pensar em um exemplo comum do dia a dia. Imagine que
temos uma função que realiza várias ações, como por exemplo: uma operação matemática,
ler um arquivo de disco, e transformar o resultado em uma String. Em linguagens
bloqueantes como PHP, Ruby e etc, cada ação será executada apenas depois que a
ação anterior for encerrada, no exemplo citado a ação de transformar a String
terá que esperar uma ação de ler um arquivo de disco, que pode ser uma operação
pesada, certo?
Vamos ver um exemplo de forma síncrona, ou seja, bloqueante:

```
const fs = require('fs');
let fileContent;
const someMath = 1+1;


try {
  fileContent = fs.readFileSync('big-file.txt', 'utf-8');
  console.log('file has been read');
} catch (err) {
  console.log(err);
}

const text = `The sum is ${ someMath }`;
console.log(text);
```

Nesse exemplo, a última linha de código com o console.log terá que esperar a função
readFileSync do module de file system executar, mesmo não possuindo ligação alguma
com o resultado da leitura do arquivo.

Esse é o problema que o Node.js se propôs a resolver, possibilitar que ações não
dependentes entre sí sejam desbloqueadas. Para solucionar isso o Node.js depende
de uma funcionalidade chamada high order functions que basicamente possibilitam
passar uma função por parâmetro para outra função, assim como uma variável, as
funções passadas como parâmetro serão executadas posteriormente, como no exemplo
a seguir:

```
const fs = require('fs');


const someMatch = 1+1;


fs.readFile('big-file.txt', 'utf-8', function (err, content) {
if (err) {
return console.log(err)
}


console.log(content)
})

const text = `The response is ${ someMatch }`;
console.log(text);

```

No exemplo acima usamos a função readFile do módulo file system, assíncrona por
padrão. Para que seja possível executar alguma ação quando a função terminar de
ler o arquivo é necessário passar uma função por parâmetro, essa função será chamada
automaticamente quando a função readFile finalizar a leitura.
Funções passadas por parâmetro para serem chamadas quando a ação é finalizada são
chamadas de callbacks. No exemplo acima o callback recebe dois parâmetros injetados
automaticamente pelo readFile: err, que em caso de erro na execução irá possibilitar
o tratamento do erro dentro do callback, e content que é a resposta da leitura do arquivo.
Para entender como o Node.js faz para ter sucesso com o modelo assíncrono é necessário
entender também o Event Loop.

Fonte: [Node.js: V8, Single thread e I/O não bloqueante. - Waldemar Neto](https://walde.co/2016/11/27/node-js-v8-single-thread-e-o-que-e-esse-event-loop-afinal/)

#### Arquitetura Event-loop

Os programas passam a maior parte do tempo lendo ou escrevendo no disco, ou melhor,
esperando a sua vez de ler e escrever, uma vez que é um recurso lento e concorrido.
Quando não estamos nesse processo de ir ao disco, estamos enviando ou recebendo bytes
da rede, que é outro processo igualmente demorado.

A latência média para o ping do site google.com (um dos mais rápidos do planeta)
é de 44 milisegundos. Ou seja, enviar um ping para o Google demora o mesmo tempo
que uma CPU necessita para executar 88 milhões de operações. Então, quando estamos
fazendo uma chamada a um recurso na Internet, poderíamos estar fazendo cerca de
88 milhões de coisas diferentes na CPU.

O Node usa um princípio semelhante ao da função setTimeout(func, x) do Javascript,
onde a função passada como primeiro parâmetro é delegada para outra thread executar
após x milisegundos, liberando a thread principal para continuar seu fluxo de execução.

##### Event loop

É uma arquitetura baseada em eventos (Rotas na maioria das vezes).

Call Stack - Uma pilha de eventos (função que foi disparada pelo código) e o
Node processa atravez de um loop eterno vendo se tem alguma função que foi disparada
pela aplicação.

![Event Loop Node.js](https://github.com/MateusCastro/Rocketseat-Bootcamp-GoStack/blob/master/markdowns/images/event-loop-node.jpg)

Falamos muito que o Node é sigle-thread, que seria o Event Loop. Quando chega uma
requisição, a mesma chama um evento (o Node é baseado em evento) que vai para a fila
de eventos (Event Queue), o primeiro evento da fila vai entrar no Event Loop
(sigle-thread do Node) que irá conferir se é rápido de processar, caso seja verdade,
ele processa e devolve, então o Event Loop é liberado, caso não seja rápido, vai
para o Thread pool em background e libera o Event loop. Quando o processamento da
Thread pool é finalizado, ele volta pra Event Queue para ser tratado novamente no
Event Loop que irá devolver a resposta da requisição.

O que o Node manda para o pool de threads? Tudo que é demorado. Esperar uma ação
no sistema de arquivos, esperar uma requisição em rede, esperar outros processos
serem finalizados são exemplos de processos enviados para o pool de threads.

Pelo fato do Node não abrir uma thread por requisição, mas sim compartilhar um pool
de threads, ele se torna mais eficiente no consumo de recursos. Por isso que geralmente
uma aplicação em Node consome menos recursos do que uma aplicação em PHP, por exemplo.

##### Características

O motor V8 (que é js) é Single Thread, por isso o Node tem uma thread principal.

O Event Loop ultiliza uma biblioteca C++ open-source chamada [libuv](https://libuv.org/)
que é multi thread, é pra ela que o V8 delega as requisições de baixo nível. JavaScript
não conecta em banco de dados, por exemplo, mas C++ conecta. Então o V8 recebe em
JS e delega à biblioteca libuv que conecta com o banco.

Referência e para mais informações sobre Event Loop:
[Entendendo o Node.js Event Loop - LuizTools](https://www.luiztools.com.br/post/entendendo-o-nodejs-event-loop/)

### Express

O micro framework ultilizado pelo curso é o Express, que é um framework Node que pode ser comparado com o Laravel para PHP, ele cria abstrações de rotas, middlewares e muitas outras funções para facilitar a criação tanto de API's quanto SPA's.

#### Instalação

Para instalar o express bastar usar o comando:

```
yarn add express
```

#### Primeiros passos

Comece importando o express.

```
const express = require('express');
```

Para instanciar o express:

```
const server = express();
```

E finalmente, para rodar o node, configure uma porta

```
server.listen(3000);
```

#### Configuração de rotas

Uma rota consiste em um método HTTP, um caminho (uma string ou uma expressão regular), middlewares e uma função callback (que será chamada quando o evento ocorrer).

Veja o exemplo:

```
chamarExpress.verboHTTP(rota(string), middlewares, funçãoASerExecutada)
```

Rota: é uma string que representa o caminho da url.

funçãoASerExecutada: Toda função a ser executada quando a rota for chamada recebe dois parâmetros: o primeiro é todos os dados vindo da requisição (geralmente chamado de rec) e o segundo terá todas as informações necesssárias para retornar uma resposta para o cliente (geralmente chamado de res)

Middleware: Funções de Middleware são funções que tem acesso ao objeto de solicitação (req), o objeto de resposta (res), e a próxima função de middleware no ciclo solicitação-resposta do aplicativo. A próxima função middleware é comumente denotada por uma variável chamada next.

Fonte e para mais informações sobre Middleware: [Usando middlewares do Express - Express.js](https://expressjs.com/pt-br/guide/using-middleware.html)

Código exemplo configurando o Express, criando rotas e utilizando middlewares:

```
// Importando Express
const express = require('express');

// Instanciando express
const server = express();

// variável que representa todos os projetos
const projects = [];
// variável que guarda a quantidade de requisições
let count = 0

// Middleware que regista a quantidade de requisições
function qtdRequest(req, res, next) {
  count++
  console.log(`Total de requisições: ${count}`)
  return next()
}

// Função que usará o Middleware
server.use(qtdRequest)

// Middleware que checa se o projeto existe
function checkUserExists(req, res, next) {
  const { id } = req.params;
  const index = projects.findIndex(item => item.id == id);

  if (index === -1) {
    return res.status(400).json({ error: "Project does not exists" });
  }

  return next();
}

// Retorna os projetos
server.get('/projects', (req, res) => {
  return res.json(projects);
})

// Adiciona um novo projeto
server.post('/projects', (req, res) => {
  const { id, title } = req.body;
  const project = { id, title, tasks: [] };

  projects.push(project);
  return res.json(projects);
})

// Inicia o Express na porta 3030
server.listen(3030)
```

Confira o código completo no github clicando [aqui](https://github.com/MateusCastro/bootcamp-gostack-desafio-01)