# Guia básico de ES6 (ECMAScript 2015)

Em junho de 2015 a [Ecma International](https://en.wikipedia.org/wiki/Ecma_International) finalizou a sexta versão da linguagem de programação ECMAScript  (popularmente chamado de JavaScript). O objetivo deste guia é mostrar as principais features desse update junto com exemplos capazes de ilustrar seu uso.

> Sinta-se livre para contribuir com o projeto!

## Suporte Atual

Por ser uma tecnologia nova o ES6 não funciona nativamente em todos os browsers. É importante avaliar quais são os navegadores que o seu projeto deseja oferecer suporte. De acordo com dados do [Can I use](http://caniuse.com/#search=es6) neste exato momento a maioria dos browsers modernos já possui suporte as principais features do ES6.

## Dando suporte a navegadores antigos

Olhando dados atuais (final de 2016) uma boa parte dos usuários ainda utiliza browsers que não implementaram o ES6 por completo, mas, para nossa a comunidade JavaScript criou uma série de ferramentas (transpilers) que possibilitam a conversão de JavaScript moderno para uma sintaxe mais antiga. O transpiler mais usado no momento é o Babel (anteriormente conhecido como 6to5).

## Babel

O [Babel](https://babeljs.io/) é um programa que transforma JavaScript na sua sintaxe mais recente para ES5, seu funcionamento é bastante similar ao que é feito em linguagens como [TypeScript](https://www.typescriptlang.org/) e [CoffeeScript](http://coffeescript.org/). No começo do projeto o Babel era chamado 6to5, mas como hoje em dia o projeto suporte features do ES7+ foi necessária a mudança para um nome mais apropriado.

### ES5, ES6, ES2015, ES2016, ESNext, ECMAScript Proposals?

Um dos grandes problemas da linguagem era que a sua evolução se dava de forma muito lenta (o ES5 por exemplo foi introduzido em 2009), uma das formas de sanar esta dificuldade foi começar a criar pequenos updates anuais que seriam nomeados com o ano em que a especificação final foi liberada ao invés do padrão antigo de versões seguindo uma ordem numérica simples. Além disso todas as mudanças que ainda não possuem uma especificação final são classificadas como ECMAScript Proposals.

Como esta proposta foi dada quando já se falava sobre ES6 o novo nome não teve uma adesão tão grande, assim como nós chamamos ECMAScript de JavaScript, neste guia ES2015 e ES2016 serão chamados de ES6 e ES7, respectivamente.

## Testando o Babel

Nós podemos brincar com o Babel online [nesta url](http://babeljs.io/repl/), o que é ótimo para ter uma certa noção do código que é gerado, sem a necessidade de montar um ambiente de desenvolvimento que suporte Babel. Por exemplo um código ES6 assim:

```javascript
const hello = 'Hello world!';

console.log(hello);
```

Vai ficar assim:

```javascript
'use strict';

var hello = 'Hello world!';

console.log(hello);
```

## Montando um ambiente de desenvolvimento com Babel

Atualmente o Babel deixou de dar suporte ao babel-standalone o seu projeto que permitia usá-lo apenas colocando uma biblioteca .js em uma tag script. Atualmente devemos usar alguma ferramenta para gerenciar o processo de conversão, neste guia daremos suporte ao Webpack. [Para outras ferramentas veja a documentação oficial do projeto](http://babeljs.io/docs/setup/#installation).

## Webpack

O webpack é um ***module bundler***, um programa que serve para juntar vários recursos da sua aplicação em um só, assim como o [browserfy](http://browserify.org/). A vantagem o webpack sobre outras ferramentas é que com ele na maioria dos casos nós não precisamos usar um software como gulp ou grunt para fazer o processo de build da nossa aplicação. O webpack usa um arquivo que guarda todas as informações a respeito do que deve ser feito com os assets chamado de [webpack.config.js](https://webpack.github.io/docs/configuration.html).

### Tutorial básico de webpack

Para instalar o webpack globalmente abra o seu terminal e digite:

```bash
npm install webpack -g
```

Crie um diretório para guardar o seu projeto e outro chamado **js** para guardar o javascript. Dentro desse diretório inicie um novo projeto node.js com:

```bash
npm init
```

crie um arquivo chamado **index.js**
Dentro de index vamos colocar um código qualquer, por exemplo:

```javascript
console.log('Olá mundo!');
```

Também vamos criar um **index.html** que chamar o nosso JavaScript gerado pelo webpack:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Aprendendo webpack</title>
  </head>
  <body>
    <script type="text/javascript" src='js/build.min.js'/>
  </body>
</html>
```

Depois de salvar o arquivo, podemos abrir o terminal novamente e digitar:

```bash
webpack /js/index.js build.min.js
```

O comando acima vai criar um bundle baseado no que está em index.js e salvar o resultado em build.min.js. Abra index.html no seu navegador para testar.

**Criando um arquivo de configuração**

Como foi dito anteriormente devemos criar um arquivo de configuração para especificar o que deve ser feito durante o processo de build. Crie um arquivo chamado webpack.config.js no seu diretório e coloque o seguinte código:

```javascript
var debug = process.env.NODE_ENV !== "production";
var webpack = require('webpack');

module.exports = {
  context: __dirname,
  devtool: debug ? "inline-sourcemap" : null,
  entry: "./js/app.js",
  output: {
    path: __dirname + "/js",
    filename: "build.min.js"
  },
  plugins: debug ? [] : [
    new webpack.optimize.DedupePlugin(),
    new webpack.optimize.OccurenceOrderPlugin(),
    new webpack.optimize.UglifyJsPlugin({
      mangle: false,
      sourcemap: false
    }),
  ],
};
```

O código pode parecer meio complicado agora, então vamos por partes:
* context: __dirname: **representa onde o webpack deve rodar, neste caso nós usamos __dirname [o que em node quer dizer o diretório atual](https://nodejs.org/docs/latest/api/globals.html)**
*   devtool: debug ? "inline-sourcemap" : null: **verifica se estamos em modo de debug ou não, se estivermos em debug, o webpack vai ativar o inline-sourcemap que facilita o processo de desenvolvimento.**
*   entry: "./js/app.js":
*   output: {
    path: __dirname + "/js",
    filename: "build.min.js"
  }: **indica que o webpack deve criar um bundle em build.min.js e salvá-lo no diretório /js**
* plugins: debug ? [] : [
    new webpack.optimize.DedupePlugin(),
    new webpack.optimize.OccurenceOrderPlugin(),
    new webpack.optimize.UglifyJsPlugin({ mangle: false, sourcemap: false }),
  ]: **Aplica o UglifyJs para mimificar nosso código se não estivermos em desenvolvimento.**

Agora que nós temos um arquivo de configuração, podemos rodar o webpack novamente e ver os resultados, No terminal digite:

```bash
NODE_ENV=production webpack --watch
```

Se tudo ocorreu como planejado teremos um arquivo build.min.js com o código da nossa aplicação mimificado!

**CommonJS**

O objetivo disso tudo obviamente é poder criar vários arquivos .js e no final juntá-los em um só, agora nós podemos fazer isso facilmente.

Crie um arquivo chamado **olamundo.js** e coloque o nosso olá mundo que estava em app nele:

```javascript
console.log('Olá mundo!');
```

Crie outro arquivo chamado **adeusmundo.js** e coloque:

```javascript
console.log('Adeus mundo!');
```

Agora nos vamos chamar os dois arquivos indo pra app.js e fazendo:

```javascript
require('./olamundo.js');
require('./adeusmundo.js');
```

Se abrirmos o index.html vamos ver que build possui o código tanto de adeus quanto de olá, como mágica! Tudo isso acontece graças ao CommonJS, um formato que também é usado no node para criação de modulos. Também vemos que não foi preciso rodar o webpack novamente, isso porque o comando --watch dado do exemplo passado, faz com que o webpack rode automaticamente toda vez que ocorra alguma mudança nos arquivos.

**Webpack dev server**

O webpack também oferece um servidor de desenvolvimento, para instalá-lo digite:

```bash
npm install webpack-dev-server -g
```

E depois rode com:

```bash
webpack-dev-server
```

Se formos para http://localhost:8080/ podemos ver o nosso projeto rodando em um servidor web, a melhor parte disso é que com o comando:

```bash
webpack-dev-server --inline --hot
```

Podemos programar normalmente e deixar o webpack-dev-server dar o refresh na página de forma automática.

### Adicionando o Babel no seu webpack.config

Com a configuração que nós temos agora ainda é impossível usar ES6 e dar suporte a navegadores antigos, para isso temos que adicionar o Babel ao nosso projeto com webpack.

Primeiramente vamos instalar o Babel e as suas dependências:

```bash
npm install babel-core babel-loader node-libs-browser babel-preset-es2015 babel-plugin-transform-decorators-legacy babel-plugin-transform-class-properties babel-preset-stage-0 --save-dev
```

Após a instalação modifique o seu webpack.config.js para:

```javascript
var debug = process.env.NODE_ENV !== "production";
var webpack = require('webpack');

module.exports = {
  context: __dirname,
  devtool: debug ? "inline-sourcemap" : null,
  entry: "./js/app.js",
  module: {
    loaders: [{
      test: /\.js?$/,
      exclude: /(node_modules|bower_components)/,
      loader: 'babel-loader',
      query: {
        presets: ['es2015', 'stage-0'],
        plugins: ['transform-decorators-legacy', 'transform-class-properties'],
      }
    }]
  },
  output: {
    path: __dirname + "/js",
    filename: "build.min.js"
  },
  plugins: debug ? [] : [
    new webpack.optimize.DedupePlugin(),
    new webpack.optimize.OccurenceOrderPlugin(),
    new webpack.optimize.UglifyJsPlugin({
      mangle: false,
      sourcemap: false
    }),
  ],
};
```

Todas as mudanças estão em:

```javascript
module: {
	loaders: [{
		test: /\.js?$/,
		exclude: /(node_modules|bower_components)/,
		loader: 'babel-loader',
		query: {
			presets: ['es2015', 'stage-0'],
			plugins: ['transform-decorators-legacy', 'transform-class-properties'],
		}
	}]
}
```

Vamos entender o que cada coisa nesse exemplo faz:

* test: /\.js?$/: **Procura por todos os arquivos que terminam em .js...**
* exclude: /(node_modules|bower_components)/: **...todos menos os que estão em node_modules ou em bower_components(aka bibliotecas externas)**
* loader: 'babel-loader': **o software que vai executar a ação**
* query: {
             presets: ['es2015', 'stage-0'],
             plugins: ['transform-decorators-legacy', 'transform-class-properties'],
           }: **oferece suporte ao es6, e a algumas features mais modernas como decorators e class properties**

Pronto! Agora nós temos um ambiente capaz de usar o ES6 em produção :-)

## Introdução ao ES6
Ok depois dessa longa introdução, estamos prontos para começar a programar em ECMAScript 6. A partir de agora o tutorial será composto de pequenos exemplos com o objetivo de explicar algumas features da linguagem.

### Declarando variáveis em ES5
Nas versões anteriores do JavaScript podíamos declarar valores de duas formas:

```javascript
    x = 10;
    var y = 12;
```

Quando declaramos uma variável sem **var** o seu escopo se torna global, logo:

```javascript
function foo() {
    x = 10;
}
foo();
console.log(x); // vai mostrar 10 na tela
```

O exemplo acima mostra como declarar variáveis dessa forma é uma má ideia, variáveis globais na maioria dos casos são algo bem ruim. Imagine uma aplicação onde um valor global é atualizado várias vezes e em várias funções diferentes, se houver um bug relacionado a este valor especifico, como vamos saber onde está a origem da falha? Em um projeto grande esse processo vai ser uma imensa perda de tempo, dificultando o trabalho do encarregado de dar manutenção no código. Como regra geral **não use variáveis globais**.

***Para resolver este problema podemos colocar 'use strict' no topo do nosso código. Ex.:***

```javascript
"use strict";

function foo() {
  x = 10;
  console.log(x);
}

foo();
console.log(x); // ERRO: assignment to undeclared variable x
```

Ok mas e o **var**?Bem, usando **var** nós podemos evitar o escopo global logo:

```javascript
function foo() {
    var x = 10;
}
foo();
console.log(x);// ReferenceError: x is not defined
```

**var** parece uma opção muito boa, porém ele funciona de uma forma um diferente do esperado:

```javascript
function foo() {
        if(true) {
            var x = 10;
        }
         console.log(x);
    }

foo(); // 10
```

Usando **var** o escopo de todas as variáveis é a função onde ela foi declarada, é como se todas as variáveis fossem jogadas para o topo da função mesmo que tenham sido declaradas em outro lugar. **var** não chega a ser um problema muito grande mas como ele age de forma muito diferente do que é comum nas demais linguagens, essa ***keyword*** gera muita confusão com os desenvolvedores iniciantes.

### Const e let

Para resolver todos esses problemas de escopo, foram introduzidas duas novas keywords para definir valores: **const** e **let**.

**const**

O **const** permite a criação de constantes, valores que após serem definidos nunca serão mudados:

```javascript
const pi = 3.14;

console.log(pi);

pi = 10; // ERRO
```

Constantes facilitam bastante o entendimento de um código, já que nós podemos ter certeza que um determinado valor nunca vai mudar depois de ser definido. ***Use const sempre que poder.***

#### Maaass...não é tudo uma maravilha :(

Constante em ES6 **não são realmente imutáveis**, **é possível criar um objeto com const e alteras seus valores**:

```javascript
const paises = {
  brasil: "Rio de Janeiro",
  eua: "Washington",
  portugal: "Lisboa",
};

paises.brasil = "Brasilia";

console.log(paises.brasil); // Brasilia
```

Para corrigir isso usamos o Object.freeze(), que "congela" um objeto, impedindo mudanças nos seus valores:

```javascript
const paises = Object.freeze({
  brasil: "Rio de Janeiro",
  eua: "Washington",
  portugal: "Lisboa",
});

paises.brasil = "Brasilia";

console.log(paises.brasil); // "brasil" is read-only
```

Note que mesmo assim não temos 100% de imutabilidade, podemos ainda alterar objetos que estejam dentro desse objeto:

```javascript
const paises = Object.freeze({
  brasil: "Rio de Janeiro",
  eua: "Washington",
  portugal: "Lisboa",
  africaDoSul: {
    a: "Pretória",
    b: "Cidade do Cabo",
    c: "Bloemfontein",
  },
});

paises.africaDoSul.a = "Brasilia";

console.log(paises.africaDoSul.a);// Brasilia :(
```

[A documentação da MDN possui uma gambiarra que resolve isso chamada deepFreeze()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze).

**let**

O let funciona de forma semelhante ao var, mas respeitando o escopo em que foi declarado:

```javascript
  if(true) {
    let a = 10;
  }
  console.log(a); // a is not defined
```

Obviamente, ainda podemos usar var em ES6.

### IIFEs

IIFEs ou ***Immediately Invoked Function Expressions*** são expressões que são executadas assim que são definidas, elas ajudam a criar um escopo mais "privado" no seu código:

```javascript
(function () {
    var comida = 'Tapioca';
    console.log(comida);
}());// vai executar automaticamente e exibir Tapioca

console.log(comida); // Reference Error
```javascript

No ES6 podemos simplesmente escrever:

```javascript
{
    const comida = 'Tapioca';
    console.log(comida);
} // vai executar automaticamente e exibir Tapioca

console.log(comida); // Reference Error
```

### Arrow functions

Em ES5 funções anônimas são definidas assim:

```javascript
var hey = function () {
    console.log('hey');
};
```

Já no ES6 podemos também fazer desta forma:

```javascript
const hey = () => {
    console.log('hey');
}
```

Ou ainda:

```javascript
const hey = () => console.log('hey');
```

**arrow functions e this**

A melhor parte das arrow functions é que elas guardam o contexto this do seu valor léxico. Em ES5 nós tínhamos problemas do tipo:

```javascript
function AfricaDoSul(intro) {
    this.intro = intro;
}

AfricaDoSul.prototype.mostraCapitais = function (lista) {
    return lista.map(function (capital) {
        return this.intro + capital; // ERRO this is undefined
    });
};

var af = new AfricaDoSul('As capitais da Africa do Sul são');
af.mostraCapitais(["Pretória","Cidade do Cabo","Bloemfontein"]);
```

Não podemos ter acesso ao this em mostraCapitais porque ele não está no mesmo contexto do construtor da classe AfricaDoSul, para resolver isso nós podemos usar o método bind para conectar o contexto:

```javascript
function AfricaDoSul(intro) {
    this.intro = intro;
}

AfricaDoSul.prototype.mostraCapitais = function (lista) {
    return lista.map(function (capital) {
        return this.intro + capital;
    }.bind(this));
};

var af = new AfricaDoSul('A capital da Africa do Sul é:');
console.log(af.mostraCapitais(["Pretória","Cidade do Cabo","Bloemfontein"]));// Array [ "A capital da Africa do Sul é:Pretória", "A capital da Africa do Sul é:Cidade do  Cabo", "A capital da Africa do Sul é:Bloemfontein" ]
```

Arrow functions já fazem isso por padrão, sendo assim:

```javascript
function AfricaDoSul(intro) {
    this.intro = intro;
}

AfricaDoSul.prototype.mostraCapitais = function (lista) {
    return lista.map((capital) => this.intro + capital);
};

var af = new AfricaDoSul('A capital da Africa do Sul é:');
console.log(af.mostraCapitais(["Pretória","Cidade do Cabo","Bloemfontein"]));
```

Funciona perfeitamente!

### Classes

O ES6 trouxe uma sintaxe de classes muito parecida com o que vemos em outras linguagens orientadas a objeto como o Java. O exemplo anterior poderia ser feito assim:

```javascript
class AfricaDoSul {
  constructor(intro) {
     this.intro = intro;
  }

    mostraCapitais = function (lista) {
    return lista.map((capital) => this.intro + capital);
  }
}

var af = new AfricaDoSul('A capital da Africa do Sul é:');
console.log(af.mostraCapitais(["Pretória","Cidade do Cabo","Bloemfontein"]));
```

**Getters e Setters**

O ES6 oferece getters e setters nas suas classes:

```javascript
class AfricaDoSul {
  constructor() {
    this._capitais = ["Pretória","Cidade do Cabo","Bloemfontein"];
  }

  get capitais() {
    return this._capitais.map((capital) => this._intro + capital);
  }

  set intro(intro) {
    this._intro = `De acordo com o usuario: ${intro}`;
  }
}

var af = new AfricaDoSul();
af.intro = 'A capital da África do Sul é: ';
console.log(af.capitais);//Array [ "De acordo com o usuário: A capital da África do Sul é: Pretória", "De acordo com o usuário: A capital da África do Sul é: Cidade do Cabo", "De acordo com o usuário: A capital da África do Sul é: Bloemfontein" ]
```

***Herança***

Para fazer herança em ES6 usamos a keyword **extends**

```javascript
class Animal {
  constructor(nome) {
     this.nome = nome;
  }
  nasce(){
    console.log('nasceu');
  }
  reproduz() {
    console.log('reproduziu');
  }
  morre() {
    console.log('morreu');
  }
}

class Human extends Animal{
  constructor(nome) {
    super(nome);
  }
  morre() {
    console.log('no céu tem pão?');
  }
}

const joao = new Human('Joao');
joao.nasce();
joao.reproduz();
joao.morre();
```

> Veja que usamos super no construtor para chamar o construtor da classe mãe (no exemplo de Human, a classe Animal)

### No final ainda é prototype

JavaScript não oferece orientação a objetos clássica como Java, em JavaScript não existem classes, nós definimos funções ou objetos e podemos expandi-los depois de sua criação. Ex:

```javascript
function AfricaDoSul(intro) {
    this.intro = intro;
}
```

É uma função que usaremos para simular o construtor de uma classe. Podemos adicionar métodos a essa classe usando a propriedade prototype:

```javascript
AfricaDoSul.prototype.mostraCapitais = function (lista) {
    return lista.map(function (capital) {
        return this.intro + capital;
    }.bind(this));
};
```

Mesmo que incialmente pareça uma ideia bizarra comparando com a POO clássica, POO com prototype é vista como uma forma de escrever classes mais simples, mais poderosas e menos redundantes. [Veja mais sobre o assunto aqui](http://stackoverflow.com/questions/2800964/benefits-of-prototypal-inheritance-over-classical).

> As classes do ES6 são só ***syntax sugar*** em cima das prototypes, ainda é necessário entender os conceitos por trás disso tudo e até mesmo usar a sintaxe antiga.

### Weakmaps

Veja que no exemplo de getters e setters nós usamos a convenção _nomeDaVariavel para definir valores privados, porém mesmo assim eles ainda podem ser acessados. Para criar valores realmente privados devemos usar WeakMaps:

```javascript
const _nome = new WeakMap();
class Pessoa {
  constructor(nome) {
    _nome.set(this,nome);
  }

  falaNome() {
    return _nome.get(this);
  }
}

const eu = new Pessoa('Lucas');
console.log(eu.falaNome());// Lucas
console.log(_nome.get(this));// undefined
```

### Maps

Maps (também conhecidos como hashmaps ou hashs) são uma estrutura de dados que armazena valores em um formato de chave(key) e valor(value). Ex:

```javascript
"use strict";

var paises = {
  brasil: "Brasilia",
  usa: "DC"
};

paises["usa"];// DC
```

O problema de declarar hashes usando objetos é que objetos permitem o uso de propriedades como __ proto __ ou toString(), que podem tornar nosso código mais inseguro já que qualquer pessoa pode modificar o nossos objetos.

Outro defeito da criação de maps com objetos é que iterar pelos seus elementos envolve uma sintaxe um pouco inconveniente:

```javascript
"use strict";

var paises = {
  brasil: "Brasilia",
  usa: "DC"
};

var listaPaises = Object.keys(paises);

listaPaises.map(function (pais) {
  return console.log(pais);
}); // brasil usa
```

Esses problemas são resolvidos com maps:

```javascript
const paises = new Map();
paises.set('brasil', 'Brasilia');
paises.set('usa', 'DC');

for(const [key, value] of paises) {
  console.log(key, value);
}
//"brasil" "Brasilia"
//"usa" "DC"
```

### Strings

Foram adicionados diversos métodos para strings, entre eles o **includes** e o **repeat**.

**includes**

Em ES5 verificamos se uma string é substring de outra usando **indexOf**:

```javascript
var comida = 'tapioca';
var isSubstring = comida.indexOf('tap', -1) > -1; // se o resultado é > -1 que é substrig
console.log(isSubstring); // true
```

No ES6 podemos usar o método include e evitar a gambiarra do exemplo anterior:

```
const comida = 'tapioca';
console.log(comida.includes('tap')); // true
```

**repeat**

Em ES5 podemos repetir uma string usando um loop:

```javascript
    var creu = 'creu';
    var i = 0;
    while(i < 3) {
      creu += creu;
      i++;
    }

    console.log(creu); // "creucreucreucreucreucreucreucreu"
```

No ES6 podemos usar o método repeat:

```javascript
    console.log('creu'.repeat(3));
```

### Template Literals

Em ES5 strings não podem estar em mais de uma linha:

```javascript
    var comida = 'oi
    tudo bem?';
```

Em ES6 podemos usar template literals para resolver este problema:

```javascript
    const comida = `oi
        tudo bem?`;
```

Template literals também oferecem interpolação de valores, parecido com o que existe no ruby:

```javascript
    const diaDaSemana = 'sexta-feira!';
    const frase = `Hoje é ${diaDaSemana}!`;
    console.log(frase); // Hoje é sexta-feira!!
```

### Deconstructors

Deconstructors permitem extrair valores de objetos e arrays para variáveis de forma mais fácil. A seguinte situação em ES5:

```javascript
var diasDaSemana = ['domingo','segunda','terça','quarta','quinta','sexta'];

var domingo = diasDaSemana[0];
var segunda = diasDaSemana[1];
var terca = diasDaSemana[2];

console.log(domingo);// domingo
console.log(segunda);// segunda
console.log(terca);// terça
```

Pode ser simplificada em ES6 assim:

```javascript
const diasDaSemana = ['domingo','segunda','terça','quarta','quinta','sexta'];
const [domingo, segunda, terca] = diasDaSemana;

console.log(domingo);// domingo
console.log(segunda);// segunda
console.log(terca);// terça
```

Também podemos fazer o mesmo com objetos:

```javascript
const capitais = { brasil: 'Brasilia', eua: 'DC' };

const {brasil, eua} = capitais;

console.log(brasil);// Brasilia
console.log(eua);// DC
```

### Modulos

Lembra do CommonJS (require) que nós vimos quando estávamos montando nosso ambiente com webpack? O ES6 trás a mesma feature nativamente com a keyword **import**

**Exportando um módulo**

Para tornar alguma parte do nosso módulo público devemos exportá-lo com a keyword export:

```javascript
export const comida = 'tapioca';

export function foo() {
    return 'tapioca da boa';
}
```

Imaginando que o exemplo anterior foi salvo em um arquivo tapioca.js podemos usar o modulo tapioca em outro arquivo assim:

```javascript
import * as tapioca from './tapioca.js';

console.log(tapioca.comida);// tapioca
console.log(tapioca.foo());// tapioca da boa
```

Também é possivel pegar importar de forma separada:

```javascript
import comida tapioca from './tapioca.js';

console.log(comida);// tapioca
console.log(tapioca.foo());// ERRO
```

***Export default***

Podemos usar a keyword **default** para definir o que deve ser retornado por padrão no nosso modulo, é sempre uma boa ideia retornar apenas uma coisa no módulo dessa forma deixando explicito o que será exposto publicamente. Ex:

```javascript
const comida = 'tapioca';
const preco = 1.0;
function foo() {
    return `tapioca da boa só ${preco} real`;
}
export defailt const api = {
    comida,
    foo
}
```

Fica possível importar com destructors:

```javascript
import { comida,foo } from './tapioca';
```

Ou mesmo:

```javascript
import tapioca from './tapioca';
tapioca.comida;
tapioca.foo();
```

### Parâmetros opcionais

No ES5 se fosse necessário cria uma função com parâmetros opcionais, teríamos que usar a seguinte gambiarra:

```javascript
function comida(nome) {
    nome = nome || 'tapioca';
    return  nome;
}

console.log(comida());// tapioca
console.log(comida('pizza')); //pizza
```

Em ES6 podemos declarar parâmetros opcionais como no Python:

```javascript
function comida(nome='tapioca') {
    return  nome;
}

console.log(comida());// tapioca
console.log(comida('pizza')); //pizza
```

### Parâmetros Rest

Se tivermos uma função com um número de indefinido de parâmetros devemos fazer o seguinte em ES5:

```javascript
function capitais() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}

capitais('Brasilia');

capitais("Pretória","Cidade do Cabo","Bloemfontein");
```

Já em ES6:

```javascript
function capitais(...args) {
    for (const arg of args) {
        console.log(arg);
    }
}

capitais('Brasilia');

capitais("Pretória","Cidade do Cabo","Bloemfontein");
```

### Operador Spread

É possível concatenar arrays em ES5 com o método concat. Ex.:

```javascript
var capitais = ["Cidade do Cabo", "Bloemfontein"];

var capitaisCompletas = ["Pretória"].concat(capitais);

console.log(capitaisCompletas); //Array [ "Pretória", "Cidade do Cabo", "Bloemfontein" ]
```

É possivel concatenar arrays em ES6 com o operador spread ... Ex.:

```javascript
const capitais = ["Cidade do Cabo","Bloemfontein"];

const capitaisCompletas = ["Pretória", ...capitais];

console.log(capitaisCompletas); //Array [ "Pretória", "Cidade do Cabo", "Bloemfontein" ]
```

### Promises

Em ES5 nós usávamos callbacks (funções passadas como argumento de outra função), isso podia gerar código assim:

```javascript
    func1(function (value1) {
        func2(value1, function (value2) {
            func3(value2, function (value3) {
                func4(value3, function (value4) {
                    func5(value4, function (value5) {
                        // Do something with value 5
                    });
                });
            });
        });
    });
```

Esse é o chamado [callback hell](http://callbackhell.com/), para evitar esse tipo de problema existem as promises. O exemplo anterior com promises ficaria assim:

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

Diversas bibliotecas como a [bluebird](https://github.com/petkaantonov/bluebird) trouxeram promises para o ES5, mas agora com o ES6 podemos usá-las de forma nativa.

**Criando promises**

Promises possuem dois **handlers** resolve(para quando a promise não retorna nenhuma exception) e reject (para quando a promise retorna uma exception):

```javascript
import $ from 'jquery';

const urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/issues/assigned',
  '/api/issues/completed',
  '/api/issues/comments',
  '/api/pullrequests'
];

const promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Faz alguma coisa com o resultado das promises
 });
```

> No exemplo anterior estamos usando o jQuery($) pra fazer requisições AJAX não se esqueça que podemos instalar bibliotecas externas com a ajuda do npm mesmo no front end. Para instalar o jQuery no nosso projeto webpack simplemente use **npm install jquery --save**

### Generators

Outra forma de evitar o callback hell são os generators, eles são funções que podem ser "pausadas" durante sua execução.

```javascript
function* capitais() {
    yield 'Pretoria';
    yield 'Brasilia';
    yield 'DC';
    yield 'Lisboa';
}

var generator = capitais();
console.log(generator.next()); // { value: Pretoria, done: false }
console.log(generator.next()); // { value: Brasilia, done: false }
console.log(generator.next()); // { value: DC done: false }
console.log(generator.next()); // { value: Lisboa, done: false }
```

Generators são ideais para código assíncrono:

```javascript
function* getData() {
    const api1 = yield request('http://some_api/item1');
    const dadosApi1  = JSON.parse(entry1);
    var api2 = yield request('http://some_api/item2');
    var dadosApi2  = JSON.parse(entry2);
}
```

### Async e Await

Async e Await fazem parte do ES7 e são uma ótima forma de fazer código assíncrono:

```javascript
import request from 'request';

function getJSON(url) {
  return new Promise((resolve, reject) => {
    request(url, (error, response, body) => {
      resolve(body);
    });
  });
}

async function main() {
  const data = await getJSON();
  console.log(data); // Vai mostrar os dados da requisição
}

main();
```

Veja que async é a maneira mais fácil e limpa de fazer códigos assíncronos com error handling. Com funções async podemos escrever código assíncrono como se fosse código síncrono, algo inspirado em features similares de linguagens como C#.

## E agora?

O passo mais importante para dominar uma nova linguagem é praticar, agora que você tem uma noção do básico de ECMAScript 6 tente converter projetos antigos ou crie projetos novos do zero, veja como você pode usar o ES6 no seu cotidiano para resolver problemas reais.

## Se aprofundando

- http://exploringjs.com/
- https://es6.io/
- http://www.ecma-international.org/ecma-262/6.0/
- http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts#final_draft

## Fontes e referências

- http://jamesknelson.com/using-es6-in-the-browser-with-babel-6-and-webpack/
- https://medium.com/@dabit3/beginner-s-guide-to-webpack-b1f1a3638460#.ssqsj3bq6
- http://tableless.com.br/introducao-ao-webpack/
- http://stackoverflow.com/questions/484635/are-global-variables-bad
- https://mathiasbynens.be/notes/es6-const
- https://github.com/DrkSephy/es6-cheatsheet
- https://ponyfoo.com/articles/es6-maps-in-depth
