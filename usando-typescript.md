# Usando TypeScript

> Referências:
>
> * <https://www.typescriptlang.org/docs/handbook/intro.html>
> * <https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html>

Agora vamos ver o real assunto a ser tratado hoje: TypeScript.

## Evitando pegadinhas

Antes de vermos como a linguagem funciona, vamos ver como ela nos ajuda a evitar os problemas mencionados anteriormente. Aquelas pegadinhas que a JavaScript nos permite cair.

### Invocação incorreta de funções

O primeiro exemplo utilizado foi criar um objeto `Date` incorretamente. Então já vamos começar escrevendo o mesmo código funcional:

```ts
new Date();
new Date('2021/12/31'); // 2021-12-31T03:00:00.000Z
```

Esse código já funcionava em JavaScript "puro", agora vejamos passando `true`:

```ts
new Date(true); // Error: No overload matches this call.
```

TypeScript nos avisa que `true` não é um parâmetro válido!

### `Array.some`

Indo ao próximo exemplo, vamos criar uma lista de nomes e procurar por `'Maria'`:

```ts
const nomes = ['Adriana', 'José', 'Maria'];
nomes.some('Maria'); // Error: Argument of type 'string' is not assignable to parameter of type
```

Novamente, TypeScript já nos avisando antecipadamente.

### Função customizada (nova)

Por último, vamos ver como o TypeScript nos ajudaria em funções criadas por nós mesmos. Utilizaremos a função `par` criada anteriormente. Mas vamos colocar algumas funcionalidades do próprio TypeScript para nos ajudar na construção e consumo dessa função:

```ts
function par(numero: number): boolean {
    const sobraDivisaoPorDois = numero % 2;
    if (sobraDivisaoPorDois === 0) {
        return true;
    }
    return false;
}
```

Mudamos a função para deixar claro que o parâmetro `numero` deve ser do tipo `number`, um numérico. Com isso, ao executar a função com qualquer outro tipo de valor temos um feedback da linguagem:

```ts
par('10'); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```

## Funcionalidades da TypeScript

Após vermos como TypeScript nos ajuda durante o desenvolvimento, vamos entender algumas de suas funcionalidades.

### Variáveis

Começando por variáveis. Em JavaScript, existem três formas para declaração de variáveis: `var`, `let` e `const`. Em TypeScript não é diferente, também temos as mesmas três formas.

`let` e `const` são formas relativamente novas, introduzidas para resolver alguns problemas inerentes a `var`. Vamos ver algumas confusões do `var` e assim entendermos o porquê de `let` e `const` serem criados.

#### Confusões do `var` em TypeScript

A primeira característica _confusa_ de `var` é a possibilidade de declarar a mesma variável várias vezes. Ou, em outras palavras, declarar múltiplas variáveis com o mesmo nome:

```ts
var x = 10;
var x = 15; // Variável `x` foi criada novamente
```

Além disso, podemos atribuir um valor a uma variável antes mesmo de ela ter sido declarada:

```ts
x = 15; // Atribuindo valor à variável `x` antes de sua declaração
var x; // Declaração da variável
```

> Curiosidade: Essa característica está ligada a como funciona [Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting) em JavaScript.

Normalmente, o maior ponto de confusão diz respeito ao escopo de uma variável declarada com `var`. Com escopo queremos dizer em qual parte do código a variável pode ser usada. Em diversas linguagens o escopo é por bloco (exemplo: `if`) onde a variável foi declarada, mas `var` em TypeScript tem o escopo por função.

Por exemplo, podemos declarar uma variável dentro de um `if` e usá-la fora:

```ts
if (false) {
  var x = 15; // Declarando variável dentro do `if`
}
x = 10; // Acessando variável
```

Isso acontece porque, como dito acima, o escopo de `var` é _por função_ e não _por bloco_. Para ficar mais claro, usemos como exemplo o mesmo código acima envolvido por uma função:

```ts
function myFunction(): number {
  if (false) {
    var x = 15; // Declarando variável dentro do `if`
  }
  x = 10; // Variável pode ser acessada em qualquer parte da função `myFunction`
  return x;
}
```

Como pudemos ver, `var` em TypeScript tem alguns pontos de confusão que, se não bem entendidos, podem trazer problemas ao código. Não é incomum introduzirmos erros no programa por conta disso. Além disso, desenvolvedores com conhecimento em outras linguagens não esperam esses comportamentos. Na tentativa de resolver esses problemas, `let` e `const` foram introduzidos.

#### `let` to the rescue

Basicamente, o `let` funciona como o `var` mas com esses problemas corrigidos. Com isso, geralmente, devemos deixar de usar `var` e utilizarmos apenas `let` e `const`. Vamos recapitular os pontos levantados anteriormente e ver como `let` se comporta.

Vamos começar declarando a mesma variável duas vezes:

```ts
let x = 10;
let x = 15; // Error: Cannot redeclare block-scoped variable 'x'
```

Ao executar esse código, o compilador do TypeScript informa que a variável já havia sido declarada anteriormente.

Próximo ponto, atribuir um valor a uma variável antes de sua declaração:

```ts
x = 15; // Error: Block-scoped variable 'x' used before its declaration
let x;
```

Novamente o compilador mostra um erro, onde é apontado o uso da variável antes da sua declaração.

Agora um dos maiores pontos de confusão no `var`. Escopo baseado em função. O que acontece se pegarmos o mesmo código usado anteriormente trocando `var` por `let`?

```ts
function myFunction(): number {
  if (false) {
    let x = 15;
  }
  x = 10; // Error: Cannot find name 'x'
  return x;
}
```

Aqui é evidenciada a diferença de escopo entre `let` e `var`. No exemplo acima a variável `x` foi declarada dentro de um bloco de código (`if`), estando disponível apenas neste bloco. Isso se deve ao fato de `let` ter o escopo _por bloco_, não por função.

#### A vida nem sempre é uma `const`

Assim como `let`, `const` se comporta da mesma maneira explicada no tópico anterior. Seu escopo também é por bloco, não é possível redeclaração e nem utilizá-la antes da declaração. A diferença entre os dois está na atribuição. Em `const`, atribuição só pode ser feita uma única vez.

Por exemplo, com `let` podemos alterar o valor de uma variável indefinidamente:

```ts
let x = 10; // Iniciada com 10
x = 15; // Alterando para 15
```

Com `const`, isso não é mais possível. Uma vez criada, seu valor não pode ser alterado:

```ts
const x = 10;
x = 15; // Error: Cannot assign to 'x' because it is a constant
```

É importante ressaltar que apesar de a atribuição só poder ser realizada uma vez, `const` não torna o valor [imutável](https://segredo.dev/o-que-e-imutabilidade/). Caso o valor da variável for um objeto, suas propriedades ainda podem ser alteradas:

```ts
const person = {
  age: 23,
};
person.age = 30; // É possível alterar o valor de uma propriedade normalmente
```

Uma boa prática de programação é não reutilizar uma mesma variável para diferentes significados. Trazendo uma maior legibilidade ao código. `const`, apesar de não ser de fato uma constante, ajuda a reforçar esse conceito, por não permitir esse reuso. Com isso, podemos sempre codificar com `const`, usando `let` apenas onde for realmente necessário.

### Tipos

Vimos como criar variáveis, mas TypeScript também nos permite ditar qual é o formato da informação a ser armazenado nessa variável. Esse aspecto também ajuda a legibilidade do código e pode antecipar muitos problemas.

Alguns tipos presentes na linguagem, são:

* `string`
* `number`
* `boolean`
* `Arrays`
* `any`, `unknown`
* Inferência
* `|` (union type)

#### Tipos primitivos

`string` é o tipo que representa qualquer texto. Seja ele um nome, uma frase, uma letra, etc. No exemplo abaixo, declaramos a variável `personName` com o tipo `string` e o TypeScript nos informando um erro ao tentar atribuir um número a ela, mas funcionando corretamente ao atribuir um nome:

```ts
let personName: string;
personName = 'Maria';
personName = 15; // Error: Type 'number' is not assignable to type 'string'
```

`number`, como o próprio nome infere, diz respeito a números. Outras linguagens possuem outros tipos de numéricos, como _Integer_ e _Float_, mas TypeScript só possui `number`:

```ts
let personAge: number;
personName = 15;
```

`boolean` só pode receber dois valores: `true` ou `false`. Basicamente informando se um valor é verdadeiro ou falso:

```ts
let isDeveloper: boolean;
isDeveloper = true;
isDeveloper = false;
```

#### `Array`

`Array` é um tipo especial, significando uma lista de alguma coisa. O `Array` pode ser uma lista de `string`s:

```ts
let people: Array<string>;
people = ['Maria', 'Joana', 'Lucas'];
```

Ou uma lista de qualquer outro tipo:

```ts
let ages: Array<number>;
let booleans: Array<boolean>;
```

#### `any` e `unknown`

Existem situações onde não sabemos o tipo de uma variável. Para esses cenários TypeScript nos permite utilizar `any` ou `unknown`. Os dois tipos podem ser usados para atribuir qualquer valor à variável:

```ts
let anyValue: any;
anyValue = 'Maria';
anyValue = 42;
anyValue = false;

let alsoAnyValue: unknown;
alsoAnyValue = 'Maria';
alsoAnyValue = 42;
alsoAnyValue = false;
```

Apesar de aparentemente esses dois tipos fazerem a mesma coisa, tem uma diferença crucial. `any` traz toda a permissividade de JavaScript para TypeScript. Por exemplo, vamos criar uma função que recebe dois valores e retorna a soma entre eles:

```ts
function sum(n1: any, n2: any): number {
    return n1 + n2;
}
```

Como os dois parâmetros são do tipo `any`, podemos executar a função informando até mesmo uma `string`:

```ts
sum('Ronaldo', 9); // 'Ronaldo9'
```

Como os parâmetros são do tipo `any`, podemos fazer uma operação de soma (`+`) mesmo sem saber de fato o tipo da variável. Isso remove toda a vantagem do TypeScript e nos coloca novamente como se estivéssemos escrevendo JavaScript puro.

Ao usar `unknown`, durante a construção da função o próprio TypeScript nos obriga a verificar o tipo da variável antes mesmo de fazer a operação de soma. Isso porque uma operação de soma só pode ser realizada em tipos numéricos:

```ts
function sum(n1: unknown, n2: unknown): number {
    return n1 + n2; // Error: Object is of type 'unknown'
}
```

Para remover o erro, precisamos antes verificar o tipo da variável. Além de também sermos obrigados a pensar o que fazer caso o tipo recebido não seja o esperado:

```ts
function sum(n1: unknown, n2: unknown): number {
  if (typeof n1 === 'number' && typeof n2 === 'number') {
    return n1 + n2;
  }
  return 0;
}
```

Como aprendizado, devemos sempre colocar o tipo correto. Mas quando realmente não soubermos, usar `unknown` é muito mais seguro.

#### Inferência

Uma funcionalidade muito interessante do TypeScript é a inferência de tipo. Essa funcionalidade permite à linguagem detectar automaticamente o tipo de uma variável de acordo com o valor atributo a ela na sua criação.

Voltando ao exemplo onde criamos a variável `personName`, poderíamos fazer assim:

```ts
let personName: string = 'Maria';
personName = 15; // Error: Type 'number' is not assignable to type 'string'
```

Mas como já estamos atribuindo um valor em conjunto com a declaração da variável, não é necessário declarar o seu tipo. Ficando assim:

```ts
let personName = 'Maria';
personName = 15; // Error: Type 'number' is not assignable to type 'string'
```

O próprio TypeScript determinar o tipo `string` para a variável inferindo a partir da atribuição.

### Funções

Funções são uma parte principal em qualquer software. Muito utilizadas para criarmos rotinas reutilizáveis, ou até de uso único. Assim mantendo, entre outras coisas, a legibilidade do código.

Assim como em JavaScript, as funções em TypeScript podem receber parâmetros e retornar algo de valor ao serem executadas. Porém a tipagem provida por TypeScript faz a construção de uma função ser mais facilitada, assim como sua execução. Isso porque conseguimos escrever a função já definindo quais os tipos de parâmetros de entrada e de saída.

Por exemplo a nossa função de soma criada anteriormente, pode ser definida recebendo dois parâmetros numéricos e retornar também um número:

```ts
function sum(n1: number, n2: number): number {
    return n1 + n2;
}
```

Vamos entender essa estrutura. Uma função é formada quando usamos a palavra `function`, seguida do seu nome (`sum`), seus parâmetros (`n1` e `n2`) e o seu tipo de retorno (`number`). Caso a função só executa sem retornar nada, o tipo de retorno deve ser `void`.

```ts
function doesNothing(param: string): void {
    // I do nothing
}
```

Dentro de uma função, podemos criar as variáveis necessárias para a rotina em questão. Por exemplo, a função `sum` poderia ter armazenado o valor da soma antes de retorná-lo:

```ts
function sum(n1: number, n2: number): number {
    const result = n1 + n2;
    return result;
}
```

Variáveis podem ser úteis para momentos de maior complexidade. Por exemplo, uma função onde é retornado o valor da soma dividido por dois poderia criar uma variável para ajudar nisso:

```ts
function sumAndDivideByTwo(n1: number, n2: number): number {
    const sum = n1 + n2;
    const sumDividedByTwo = sum / 2;
    return sumDividedByTwo;
}
```

Funções também podem ser compostas, uma executando a outra. Normalmente é melhor criarmos diversas funções com responsabilidades únicas, ao invés de uma única função fazendo várias coisas. Para exemplificar, vamos dividir essa função anterior em duas:

```ts
function sum(n1: number, n2: number): number {
    return n1 + n2;
}
function divideByTwo(n: number): number {
    return n / 2;
}

function sumAndDivideByTwo(): number {
    const total = sum(2, 4);
    const half = divideByTwo(total);
    return half;
}
```

#### Funções anônimas

Assim como tudo em JavaScript, uma função também pode ser armazenada em uma variável. Por exemplo, ao invés de criarmos a função com o nome `sum`, podemos armazená-la em uma variável de nome `sum`:

```ts
const sum = function(n1: number, n2: number): number {
    return n1 + n2;
}
const total = sum(2, 4);
```

A forma de executar a função não muda, porém agora ela se tornou uma função _anônima_, sem nome. Funções anônimas são muito usadas em cenários um pouco mais avançados.

#### Arrow functions

Até agora vimos o modo _tradicional_ de criar funções. Mas também é possível criar o que chamamos de `Arrow Function`. Esse nome se dá ao fato de a função ficar semelhante a uma flecha. Vamos ao exemplo da função `sum` novamente:

```ts
const sum = (n1: number, n2: number): number => {
    return n1 + n2;
}
const total = sum(2, 4);
```

Nesse modo, não é necessário escrever `function` no começo da função. Porém é preciso colocar o símbolo `=>` antes de _abrir_ a função com as chaves. Caracterizando o formato de flecha. Essa forma é interessante porque é possível simplificar ainda mais a construção da função.

Arrow functions que possuam apenas uma linha, acabam não precisando de um corpo. Não sendo necessário delimitar com chaves (`{` e `}`). Com isso, podemos escrever a função dessa forma:

```ts
const sum = (n1: number, n2: number): number => n1 + n2;
```

Além de não precisar das chaves, também não é necessário escrever `return`. Isso porque uma arrow function tem o `return` implícito.

Mas, funções com múltiplas linhas ainda precisam das chaves e do eventual `return`:

```ts
const sum = (n1: number, n2: number): number => {
    const total = n1 + n2;
    return total;
}
```

### Classes

Desde a versão 2015 (ES2015), JavaScript permite a criação de classes tal como em outras linguagens. Em TypeScript, as classes podem ser ainda mais úteis. Se assemelhando bastante ao visto em Java, C# e afins.

Exemplo de uma classe vazia:

```ts
class Person {}
```

E, para criarmos um objeto dessa classe, basta utilizarmos o operador `new`:

```ts
const person = new Person();
```

> Interessante colocar os nomes das classes sempre começando por maiúsculo. No padrão `PascalCase`.

As classes são principalmente compostas por:

* Atributos
* Métodos
* Construtor

#### Atributos

Os atributos da classe podem ser considerados a parte principal de uma classe. São eles que definem a especificidade daquela classe e dos objetos a serem criados a partir dela.

Por exemplo, toda pessoa pode ter um nome e idade. Aqui, essas características passam a ser atributos:

```ts
class Person {
    name: string;
    age: number;
}
```

Com esses atributos definidos na classe, ao instanciar um objeto do tipo `Person` podemos definir os valores desses atributos:

```ts
const me = new Person();
me.name = 'Wiley';
me.age = 18;
```

> Como podemos ver, os atributos de um objeto são acessados através de um ponto (`.`) e o nome desse atributo.

#### Métodos

Outra parte importante das classes são seus métodos. São basicamente as ações possíveis de serem executados por um objeto do tipo de uma classe. Por exemplo, como uma pessoa pode caminhar, nossa classe `Person` poderia ter um método chamado `walk`:

```ts
class Person {

    name: string;
    age: number;

    walk(): void {}
}
```

Métodos são basicamente funções dentro do escopo de uma classe. Podendo ser executadas apenas quando se instancia um objeto dessa classe. E, assim como os atributos, os métodos são acessados através do ponto (`.`) seguido do seu nome:

```ts
const me = new Person();
me.walk();
```

Como o método `walk` não retorna valor, seu tipo é `void`. Mas um método falar poderia retornar uma palavra, portanto poderia ser `string`:

```ts
class Person {

    name: string;
    age: number;

    walk(): void {}
    talk(): string {}
}
```

Toda classe pode ter quantos atributos e métodos forem necessários. Mas sempre tomando cuidado para não criarmos classes muito grandes, tornando o código confuso e difícil de manter.

#### Construtores

Agora a parte talvez um pouco mais complexa de uma classe. Toda classe possui um construtor, mesmo que implicitamente. Usando novamente a classe `Person` como exemplo, vamos criar um construtor vazio:

```ts
class Person {
    constructor() {}
}
```

Como o construtor está vazio, ele poderia ser suprimido. Assim como tínhamos feito anteriormente.

Mas construtores são muito úteis para conseguirmos definir parâmetros na criação de um objeto de uma classe. Por exemplo, ao especificarmos no construtor o recebimento de nome e idade, um objeto já pode definir esses valores durante sua criação:

```ts
class Person {

    name: string;
    age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }
}

const me = new Person('Wiley', 18);
```

Um ponto interessante visto nesse exemplo é a utilização do `this`. Essa é uma palavra reservada que especifica o próprio objeto. Com o `this`, é possível manipular os valores contidos dentro do objeto.

Por exemplo, podemos fazer o método `talk` retornar o nome e a idade através de uma frase:

```ts
class Person {

    name: string;
    age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    talk(): string {
        return `Oi, meu nome é ${this.name} e tenho ${this.age} anos`;
    }
}

const me = new Person('Wiley', 18);
```

#### Modificadores de acesso

Uma adição interessante que TypeScript traz é podermos explicitar qual é o nível de acesso para atributos e outros membros de uma classe. Com eles, podemos tornar um atributo visível apenas dentro da própria classe, não sendo possível vê-lo fora.

Tomando novamente `Person` como exemplo, podemos criar um atributo para armazenarmos o sobrenome da pessoa de maneira privada. Para isso, basta _anotar_ o atributo com o modificador de acesso `private`:

```ts
class Person {
    private lastName: string;
}

const me = new Person();
me.lastName = 'Marques'; // Error: Property 'lastName' is private and only accessible within class 'Person'.
```

Por ser `private` e não ser possível atribuir um valor após a criação do objeto, podemos fazer esse valor ser recebido através do construtor e referenciar internamente na classe onde quisermos. Aqui, vamos usar no método `talk`:

```ts
class Person {

    name: string;
    age: number;

    private lastName: string;

    constructor(name: string, age: number, lastName: string) {
        this.name = name;
        this.age = age;
        this.lastName = lastName;
    }

    talk(): string {
        return `Oi, meu nome é ${this.name} ${this.lastName} e tenho ${this.age} anos`;
    }
}

const me = new Person('Wiley', 18, 'Marques');
me.talk(); // 'Oi, meu nome é Wiley Marques e tenho 18 anos'
```

Um outro modificador presente é o chamado de `public`. Ele é basicamente o inverso do `private`, tudo anotado com `public` é acessível fora da classe. Uma diferença interessante é que ele é opcional. Ou seja, qualquer atributo ou método de uma classe não anotado com qualquer modificador, é automaticamente `public`.

Para deixarmos tudo explícito, a classe `Person` ficaria assim:

```ts
class Person {

    public name: string;
    public age: number;

    private lastName: string;

    constructor(name: string, age: number, lastName: string) {
        this.name = name;
        this.age = age;
        this.lastName = lastName;
    }

    public talk(): string {
        return `Oi, meu nome é ${this.name} ${this.lastName} e tenho ${this.age} anos`;
    }
}
```

Eu, particularmente, gosto de deixar tudo explícito, deixando o código mais claro. Mas o código pode ficar grande e não agradar a todos. Para ajudar, podemos usar uma funcionalidade do TypeScript chamada _Parameter Properties_.

Apesar do nome complexo, é bem simples. É uma forma de suprimirmos parte do código necessário para definir atributos quando estes também são recebidos no construtor. Indo direto ao código, a classe `Person` ficaria assim:

```ts
class Person {

    constructor(public name: string, public age: number, private lastName: string) {}

    public talk(): string {
        return `Oi, meu nome é ${this.name} ${this.lastName} e tenho ${this.age} anos`;
    }
}
```

Ao invés de criarmos os atributos, no construtor receber os seus valores e atribuir com `this`. Suprimimos tudo e deixamos o TypeScript fazer tudo automaticamente. Para deixar um pouco mais claro, podemos colocar quebra de linha após cada parâmetro no construtor:

```ts
class Person {

    constructor(
        public name: string,
        public age: number,
        private lastName: string,
    ) {}

    public talk(): string {
        return `Oi, meu nome é ${this.name} ${this.lastName} e tenho ${this.age} anos`;
    }
}
```

> Podemos até deixar uma vírgula no final, TypeScript também permite isso!
