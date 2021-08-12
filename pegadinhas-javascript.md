# Pegadinhas JavaScript

> Referências:
>
> * <https://wtfjs.com>
> * <http://zero.milosz.ca>
> * <https://github.com/denysdovhan/wtfjs>
> * <https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html>
> * <https://dorey.github.io/JavaScript-Equality-Table/>

## Coercion

JavaScript é uma linguagem desenvolvida com uma premissa de facilitar algumas coisas. Uma dessas facilitações se dá em como a linguagem trabalha com conversões de tipos durante a execução de algumas operações. Porém essa ajuda muitas vezes pode acabar nos atrapalhando caso não saibamos como funciona.

Um dos processos utilizados durante essas operações tem o nome de [_Type coercion_](https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion). Esse processo é basicamente uma conversão dos valores utilizados na operação antes mesmo de realizá-la. Por exemplo para comparar um número e um texto, os dois valores são convertidos automaticamente para texto. Com os dois valores convertidos, o motor da linguagem pode realizar a comparação.

Contudo, como dito anteriormente, essa conversão automática pode produzir alguns resultados inesperados. Um exemplo clássico é realizar uma operação de _soma_ entre `true` e `false`, onde o resultado é: `1`!

```js
true + false // 1
```

Vamos realizar algumas operações onde o resultado pode ser considerado inesperado:

* `15 + '3'` // '153'
* `'true' == true` // false
* `null + ''` // 'null'
* `['texto'] == 'texto'` // true
* `new Date(0) - 1` // -1
* `true + 'true'` // 'truetrue'
* `[] + {}` // '[object Object]'
* `[] + []` // ''
* `[] * []` // 0

## Invocação incorreta de funções

JavaScript é uma linguagem bastante permissiva. Como pudemos ver na sessão anterior, é possível até realizar operações em praticamente qualquer tipo de dado. Contudo, essa liberdade vem, novamente, com muitos riscos. Agora vamos ver alguns problemas ao consumir funções incorretamente.

Existem algumas funções disponibilizadas pela própria linguagem ou pelo _motor_ de execução da linguagem. Esse _motor_ pode ser o Node ou o próprio navegador. Essas funções _nativas_ existem para não precisarmos criar _do zero_ algoritmos amplamente utilizados, como por exemplo saber que dia é hoje (`new Date()`). Vamos utilizar algumas como exemplo de utilização incorreta.

### `new Date`

Uma função muito comum é, como citada anteriormente, obter a data de hoje. Para isso, usamos a função `new Date()`:

```js
new Date();
```

Essa mesma função também pode ser usada para criarmos um [objeto do tipo `Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) com uma data específica:

```js
new Date('2021/12/31'); // 2021-12-31T03:00:00.000Z
```

Mas, como (quase) tudo em JavaScript, podemos informar qualquer valor. Por exemplo, qual seria o retorna dessa mesma função quando passamos `true`?

```js
new Date(true); // 1970-01-01T00:00:00.001Z
```

### `Array.some`

Em JavaScript, listas são representadas com [`Array`s](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array). Todo `array` possui algumas funções úteis para validar ou manipular o seu conteúdo.

Por exemplo, vamos criar uma lista de nomes e usar a função `some` presente em qualquer `array` para verificar se um nome está presente:

```js
const nomes = ['Adriana', 'José', 'Maria'];
nomes.some('Maria'); // Uncaught TypeError: Maria is not a function
```

Ao executar o código o retorno é bem inesperado: um erro. `Uncaught TypeError: Maria is not a function`. Isso acontece porque a função `some` deve receber uma outra função onde é declarado o algoritmo de comparação. O correto então seria:

```js
const nomes = ['Adriana', 'José', 'Maria'];
nomes.some((nome) => nome === 'Maria'); // true
```

Agora sim, o retorno é o esperado: `true`.

### Função customizada (nova)

Pudemos ver como é possível executar funções nativas de maneira incorreta. Mas JavaScript também nos permite criar funções com algoritmos desenvolvidos por nós mesmo. Um exemplo clássico é um algoritmo para verificarmos se um número informado é par.

Vamos criar essa função:

```js
function par(numero) {
    const sobraDivisaoPorDois = numero % 2;
    if (sobraDivisaoPorDois === 0) {
        return true;
    }
    return false;
}
```

Executando essa função com algum número, temos o resultado esperado:

```js
par(10); // true
par(2); // true
par(1); // false
par(5); // false
par(6); // true
```

Porém conseguimos executar essa mesma função informando qualquer tipo de valor, tendo resultados às vezes inesperados:

```js
par('10'); // true
par('5'); // false
par(''); // true
par(true); // false
par(false); // true
par([]); // true
par({}); // false
par(null); // true
par(undefined); // false
```

## Erros de digitação

Em JavaScript, assim como a maioria das linguagens de programação, podemos criar funções, variáveis e até objetos. Vimos anteriormente criar uma função. Agora vamos alterar algumas coisas na função `par`:

```js
function par(numero) {
    const sobraDivisaoPorDois = numeroo % 2;
    if (sobraDivisaoPorDois === 0) {
        return true;
    }
    return false;
}
```

Executando, temos o seguinte:

```js
par(2); // Uncaught ReferenceError: numeroo is not defined
```

Nesse exemplo, erramos o nome da variável `numero` e, ao executar, um erro é informado. Mas somente ao executar.

Agora vamos alterar a função para receber um objeto de parâmetros ao invés de apenas o número. Apesar de nesse exemplo não fazer sentido, esse padrão é muito comum no dia-a-dia. Vamos alterar para a função receber um objeto chamado `parametros` com uma propriedade chamada `numero`. O código ficaria assim:

```js
function par(parametros) {
    const numeroInformado = parametros.numro;
    const sobraDivisaoPorDois = numeroInformado % 2;
    if (sobraDivisaoPorDois === 0) {
        return true;
    }
    return false;
}
```

Agora vamos executar a função passando um objeto contendo a propriedade `numero` preenchida com o valor a ser verificado:

```js
par({ numero: 5 }); // false
par({ numero: 2 }); // false
```

Podemos ver que não acontece um erro na execução. Porém, a função está retornando `false` para o número 2. Isso aconteceu porque simplesmente digitamos incorretamente o nome da propriedade `numero` dentro da função `par`. Na primeira linha está escrito `parametros.numro` ao invés de `parametros.numero`. Então vamos corrigir:

```js
function par(parametros) {
    const numeroInformado = parametros.numero;
    const sobraDivisaoPorDois = numeroInformado % 2;
    if (sobraDivisaoPorDois === 0) {
        return true;
    }
    return false;
}
```

Executando a função novamente temos o retorno correto:

```js
par({ numero: 5 }); // false
par({ numero: 2 }); // true
```
