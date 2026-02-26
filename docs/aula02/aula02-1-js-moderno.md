---
layout: aula
title: "1. JavaScript Moderno (ES6+)"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 1
---

## **1. Revisão de JavaScript Moderno (ES6+)**

## 1.1 Declaração de Variáveis: `let`, `const` e Escopo

Até a padronização trazida pelo ECMAScript 2015 (ES6), a única forma de declarar variáveis em JavaScript era por meio da palavra-chave `var`. Essa forma de declaração apresenta características que hoje são consideradas problemáticas, principalmente pelo escopo limitado a funções e pelo comportamento conhecido como **hoisting**, que pode levar a resultados inesperados e difíceis de depurar. Com a evolução da linguagem e das aplicações web e móveis, tornou-se essencial adotar um modelo mais previsível e seguro. Foi nesse contexto que surgiram `let` e `const`, duas palavras-chave que oferecem controle mais rigoroso sobre escopo e mutabilidade, e que passaram a ser padrão no desenvolvimento moderno — tanto em JavaScript quanto em TypeScript.

### Escopo de função e escopo de bloco

Variáveis declaradas com `var` têm **escopo de função**, ou seja, são visíveis em toda a função onde foram declaradas, mesmo que isso ocorra dentro de um bloco como `if`, `for` ou `while`. Já `let` e `const` têm **escopo de bloco**, ficando visíveis apenas dentro do bloco em que foram definidas. Essa mudança reduz a chance de conflitos de nomes e facilita a manutenção do código.

```javascript
function exemplo() {
  if (true) {
    var a = 1;
    let b = 2;
    const c = 3;
  }

  console.log(a); // 1
  console.log(b); // ReferenceError
  console.log(c); // ReferenceError
}
```

No exemplo acima, `a` permanece acessível fora do bloco `if`, enquanto `b` e `c` não, porque foram declaradas com escopo de bloco. Esse comportamento é mais intuitivo e compatível com o que se espera ao declarar variáveis locais em linguagens como Java, C# ou Swift.

### Hoisting e zona morta temporal

**Hoisting** é o comportamento do JavaScript que consiste em **elevar as declarações de variáveis para o topo do escopo onde foram definidas**, antes mesmo da execução do código. Esse processo ocorre durante a fase de compilação da linguagem, e se aplica a variáveis (`var`, `let`, `const`), funções e classes — embora com regras diferentes para cada caso.

No caso de variáveis declaradas com `var`, o nome da variável é elevado ao topo do escopo da função (ou do escopo global, se fora de uma função) e seu valor inicial é definido como `undefined`. Isso permite que a variável seja acessada antes de sua linha de declaração, embora ainda não tenha recebido o valor atribuído.

```javascript
function exemploVar() {
  console.log(nome); // undefined
  var nome = "João";
}
```

Esse comportamento pode causar confusão, especialmente em funções longas ou mal estruturadas, pois a variável "parece existir" antes mesmo de ser declarada, o que pode levar a interpretações erradas sobre a ordem lógica do código.

Já no caso de `let` e `const`, embora também sofram hoisting (ou seja, seus nomes são registrados internamente no topo do escopo), o acesso a essas variáveis **antes da linha de declaração é bloqueado**. Isso ocorre porque elas permanecem em um estado especial chamado **zona morta temporal** (*temporal dead zone*), que se estende desde o início do bloco até a linha em que a variável é declarada. Qualquer tentativa de acessar a variável nesse período resulta em erro.

```javascript
console.log(contador); // ReferenceError
let contador = 1;
```

```javascript
console.log(apiUrl); // ReferenceError
const apiUrl = "https://meuapp.dev";
```

Esse comportamento mais restritivo torna o código mais seguro e previsível, e é uma das razões pelas quais `let` e `const` são preferidos em JavaScript moderno e TypeScript.

| Tipo    | Sofre hoisting? | Pode ser acessado antes da declaração? | Valor antes da linha de declaração |
| ------- | --------------- | -------------------------------------- | ---------------------------------- |
| `var`   | Sim             | Sim                                    | `undefined`                        |
| `let`   | Sim             | Não                                    | Erro (zona morta temporal)         |
| `const` | Sim             | Não                                    | Erro (zona morta temporal)         |

### Mutabilidade e constância

Outro ponto importante é a **mutabilidade**. Variáveis declaradas com `let` podem ter seus valores reatribuídos, enquanto aquelas declaradas com `const` **não podem**. Isso não significa que `const` cria valores imutáveis, mas sim que a referência não pode ser alterada. Quando se trata de objetos ou arrays, o conteúdo interno ainda pode ser modificado.

```javascript
const usuario = { nome: "Ana" };
usuario.nome = "Maria"; // permitido
usuario = { nome: "João" }; // erro

const lista = [1, 2, 3];
lista.push(4); // permitido
lista = [10, 20]; // erro
```

Esse comportamento é relevante para o React Native, onde frequentemente utilizamos `const` para definir estados e funções auxiliares, mesmo quando o conteúdo pode ser atualizado por meio de mecanismos controlados, como o `useState`. 🧑‍💻

```tsx
const [tarefas, setTarefas] = useState(["Estudar"]);
```

Nesse caso, a referência ao estado (`tarefas`) permanece constante, e seu conteúdo muda indiretamente via `setTarefas`.

### Tipagem em TypeScript

No TypeScript, `let` e `const` seguem exatamente os mesmos comportamentos de escopo e hoisting do JavaScript moderno. A principal diferença está na **tipagem estática**, que permite especificar os tipos das variáveis e obter verificação de erros durante o desenvolvimento.

```ts
let idade: number = 30;
idade = "trinta"; // Erro: string não é atribuível a number
```

Ao usar `const`, o TypeScript é capaz de inferir **valores literais** como tipos específicos. Isso é útil para restringir valores em enums simulados ou parâmetros de configuração.

```ts
const cor = "azul"; // Tipo: "azul"
type CorPrimaria = "vermelho" | "azul" | "amarelo";

const escolhida: CorPrimaria = cor; // válido
const invalida: CorPrimaria = "verde"; // erro
```

Quando declaramos uma variável com `const`, estamos dizendo que ela será **uma constante**: ou seja, **não será possível reatribuir outro valor a ela no futuro**. Isso significa que se você escrever:

```ts
const status = "ativo";
```

o valor `"ativo"` será permanentemente associado a essa variável — não será possível fazer:

```ts
status = "inativo"; // ❌ Erro: não é permitido reatribuir uma constante
```

Portanto, nesse exemplo, `"ativo"` não é apenas uma string qualquer: é **um valor literal imutável** ligado a uma variável constante! No entanto, vale destacar um detalhe importante: quando usamos `const` com objetos ou arrays, o que se torna constante é a **referência** ao objeto — mas o conteúdo interno ainda pode ser alterado.

```ts
const usuario = { nome: "Ana" };
usuario.nome = "Maria"; // ✅ permitido (estamos mudando o conteúdo)
usuario = { nome: "João" }; // ❌ erro (não podemos trocar o objeto)
```

Esse comportamento é consistente com outras linguagens que utilizam o conceito de referência. O que o `const` garante é que **a variável não apontará para outro valor** — mas se o valor for um objeto mutável, ainda será possível alterá-lo internamente.

Essa definição é essencial quando combinamos `const` com os tipos literais do TypeScript, pois a combinação entre **constância de valor** e **tipagem literal** permite definir regras muito precisas de quais valores são aceitos e impedir mudanças acidentais ao longo do código.

Esse recurso contribui para uma codificação mais segura e facilita a detecção precoce de erros, especialmente em projetos grandes ou com múltiplos módulos.

### Diretrizes de uso

A prática mais comum e recomendada é declarar variáveis com `const` sempre que não houver necessidade de reatribuição. Isso torna o código mais previsível e explicita a intenção do desenvolvedor. Use `let` apenas quando for necessário alterar o valor da variável ao longo do tempo, como em contadores ou acúmulos. O uso de `var` deve ser evitado completamente.

Além disso, é importante declarar variáveis no escopo mais restrito possível, para evitar conflitos de nomes e facilitar a manutenção. Em projetos com TypeScript, recomenda-se tipar explicitamente variáveis exportadas, parâmetros de função e objetos mais complexos, mesmo quando o compilador pode inferir os tipos automaticamente.

O domínio desses conceitos é essencial para escrever código limpo, especialmente no contexto do desenvolvimento mobile com React Native, onde a gestão do estado, a composição de componentes e a previsibilidade de escopo são fundamentais. O uso consciente de `let`, `const` e da tipagem em TypeScript forma a base sobre a qual construiremos aplicações móveis confiáveis e fáceis de manter. ✍️

---

## 1.2 Funções Anônimas e Arrow Functions

Em JavaScript, funções podem ser declaradas de diversas formas. Duas delas merecem atenção especial: **funções anônimas** e **arrow functions**. Ambas são muito utilizadas no desenvolvimento com React e React Native, especialmente para definir manipuladores de eventos, funções auxiliares dentro de componentes, ou parâmetros de métodos como `map`, `filter`, `setTimeout`, entre outros.

### Funções anônimas: definição e uso

Uma função anônima é simplesmente uma função **sem nome**, atribuída geralmente a uma variável ou passada como argumento para outra função. Seu uso é comum em situações onde a função será usada apenas naquele contexto específico.

```javascript
const saudacao = function(nome) {
  return `Olá, ${nome}!`;
};

console.log(saudacao("Maria")); // "Olá, Maria!"
```

Essa sintaxe é perfeitamente válida e ainda bastante usada. Porém, com a chegada do ES6, foi introduzida uma nova forma de declarar funções: as **arrow functions**, que se tornaram o padrão moderno, principalmente em código React.

### Arrow functions: sintaxe e vantagens

As **arrow functions** são uma forma mais concisa de escrever funções anônimas, utilizando a seta `=>` para indicar a relação entre os parâmetros e o corpo da função. Veja a reescrita da função anterior como arrow function:

```javascript
const saudacao = (nome) => {
  return `Olá, ${nome}!`;
};
```

Quando a função tem apenas **um parâmetro** e **retorna uma única expressão**, podemos omitir os parênteses e a palavra `return`:

```javascript
const saudacao = nome => `Olá, ${nome}!`;
```

Essa sintaxe compacta melhora a legibilidade, especialmente para funções curtas e sem lógica interna complexa. Ela é muito comum em chamadas de métodos de array:

```javascript
const nomes = ["Ana", "Carlos", "João"];

const cumprimentos = nomes.map(nome => `Olá, ${nome}!`);
console.log(cumprimentos);
// ["Olá, Ana!", "Olá, Carlos!", "Olá, João!"]
```

### Diferença fundamental: o `this`

Apesar da sintaxe mais curta, há uma diferença semântica importante entre funções tradicionais e arrow functions: **o comportamento da palavra-chave `this`**.

Funções tradicionais definem seu **próprio contexto de `this`** no momento em que são chamadas. Já arrow functions **não criam um novo `this`** — elas **herdam o `this` do escopo em que foram definidas**. Isso é útil em muitos cenários, especialmente no React, pois evita a necessidade de fazer *bind* manual do contexto.

Veja a diferença:

```javascript
const obj = {
  nome: "Maria",
  saudacaoTradicional: function() {
    return `Olá, meu nome é ${this.nome}`;
  },
  saudacaoArrow: () => {
    return `Olá, meu nome é ${this.nome}`;
  }
};

console.log(obj.saudacaoTradicional()); // "Olá, meu nome é Maria"
console.log(obj.saudacaoArrow());       // "Olá, meu nome é undefined"
```

No exemplo acima, `saudacaoTradicional` funciona corretamente porque o `this` se refere ao objeto `obj`. Já `saudacaoArrow` falha porque a função flecha foi criada no escopo global (ou de módulo) e, portanto, o `this` não aponta para `obj`, mas sim para o escopo externo — onde `this.nome` é `undefined`.

Por outro lado, **há situações em que essa herança de contexto é justamente o que queremos!**. Em callbacks assíncronos, como em `setTimeout`, `map`, ou manipuladores dentro de métodos de classes, as arrow functions funcionam bem por manterem o `this` do contexto externo:

```javascript
function Pessoa(nome) {
  this.nome = nome;

  this.apresentar = function() {
    setTimeout(() => {
      console.log(`Olá, meu nome é ${this.nome}`);
    }, 1000);
  };
}

const maria = new Pessoa("Maria");
maria.apresentar(); // Após 1 segundo: "Olá, meu nome é Maria"
```

Se usássemos uma função tradicional dentro de `setTimeout`, o `this` deixaria de apontar para a instância `Pessoa`, e o código não funcionaria como esperado. Esse comportamento previsível do `this` nas arrow functions é uma das razões pelas quais elas são muito utilizadas em componentes funcionais e hooks no React. 👨‍🏭

### **Arrow functions com tipos explícitos**

Em TypeScript, você pode (e deve) declarar os tipos de **parâmetros** e do **valor de retorno** de funções arrow. Isso garante que a função seja usada corretamente em diferentes contextos.

```ts
const soma = (a: number, b: number): number => {
  return a + b;
};
```

Se o retorno for inferido corretamente e for simples, o TypeScript permite omitir a anotação, mas em funções reutilizáveis ou exportadas, é uma boa prática **tipar explicitamente** o retorno. ✍️

### **Arrow functions e genéricos**

Arrow functions também podem ser genéricas — úteis, por exemplo, em funções utilitárias:

```ts
const primeiroElemento = <T>(lista: T[]): T => lista[0];

const nomes = ["Ana", "Carlos"];
const primeiro = primeiroElemento(nomes); // tipo inferido: string
```

Isso mostra a **versatilidade de funções flecha em código tipado**, e ajuda a conectar conceitos que serão aprofundados em tópicos posteriores da aula. 

### Aplicações práticas no React Native

Em componentes funcionais com React Native, o uso de arrow functions é muito frequente. Elas são utilizadas tanto na definição de handlers como em callbacks passados para componentes filhos:

```tsx
const MeuBotao = () => {
  const handlePress = () => {
    console.log("Botão pressionado");
  };

  return <Button title="Clique" onPress={handlePress} />;
};
```

Como alternativa, podemos usar a arrow function diretamente no JSX — o que é útil para casos simples, mas deve ser feito com cautela para não gerar funções novas a cada renderização:

```tsx
<Button title="Clique" onPress={() => console.log("Botão pressionado")} />
```

### Ou seja...

Arrow functions são uma evolução das funções anônimas tradicionais. Elas oferecem uma sintaxe mais curta e uma semântica de `this` mais previsível no contexto de componentes e funções de ordem superior. Entender suas diferenças é importante para escrever código limpo, legível e compatível com os padrões modernos do JavaScript e do ecossistema React. 🤓

---

## 1.3 Template Literals: interpolação e multilinha com crases

Até o ES6, a única forma de compor strings em JavaScript era utilizando aspas simples ou duplas, combinadas com o operador de concatenação (`+`). Embora funcional, essa abordagem rapidamente se tornava incômoda e difícil de ler, especialmente ao lidar com múltiplas variáveis ou quebras de linha.

Com a introdução dos **template literals** (ou **template strings**) no ECMAScript 2015, tornou-se possível construir strings **de forma mais clara e expressiva**, utilizando a crase (\`\`\`) no lugar das aspas.

### Interpolação de variáveis

Uma das principais vantagens dos template literals é a **interpolação de expressões**, que permite inserir valores diretamente dentro da string usando `${...}`.

Antes do ES6:

```javascript
const nome = "João";
const idade = 30;
const mensagem = "Olá, meu nome é " + nome + " e eu tenho " + idade + " anos.";
```

Com template literals:

```javascript
const nome = "João";
const idade = 30;
const mensagem = `Olá, meu nome é ${nome} e eu tenho ${idade} anos.`;
```

Além de mais legível, essa forma é menos propensa a erros de concatenação e facilita a leitura do código — algo especialmente importante em interfaces com textos dinâmicos, como labels, notificações ou mensagens formatadas em aplicativos React Native.

### Avaliação de expressões

É possível interpolar não apenas variáveis, mas **qualquer expressão válida em JavaScript**, como chamadas de função, operadores ternários, operações matemáticas, entre outras.

```javascript
const a = 5;
const b = 3;
const resultado = `A soma de ${a} + ${b} é ${a + b}`;
// "A soma de 5 + 3 é 8"
```

Ou, em casos mais elaborados:

```javascript
const nome = "Lucas";
const status = true;
const saudacao = `Bem-vindo, ${nome}. Status: ${status ? "ativo" : "inativo"}.`;
// "Bem-vindo, Lucas. Status: ativo."
```

Esse recurso reduz a necessidade de criar variáveis intermediárias apenas para compor mensagens, tornando o código mais direto e funcional. 🤩

### Strings multilinha

Outro benefício importante é a possibilidade de escrever **strings com múltiplas linhas** sem a necessidade de caracteres especiais (`\n`) ou concatenação entre linhas.

Antes do ES6:

```javascript
const mensagem = "Linha 1\n" +
                 "Linha 2\n" +
                 "Linha 3";
```

Com template literals:

```javascript
const mensagem = `Linha 1
Linha 2
Linha 3`;
```

Esse recurso é útil para exibir mensagens com quebras de linha, gerar trechos de código, construir templates HTML ou armazenar textos longos que precisam manter a formatação original — tudo de forma nativa e sem necessidade de escapes.

### Aplicações no React Native

No desenvolvimento de interfaces móveis com React Native, o uso de template literals aparece frequentemente em:

* Mensagens dinâmicas (`Toast`, `Alert`, `Text`)
* Formatação de dados (como unidades, valores ou nomes)
* Logs e depuração
* Construção de strings de requisição para APIs

Exemplo:

```tsx
const usuario = "Camila";
const saldo = 25.5;

<Text>{`Olá, ${usuario}. Seu saldo atual é R$ ${saldo.toFixed(2)}`}</Text>
```

Neste caso, o uso de template literals evita concatenação manual e garante melhor leitura, além de manter o JSX limpo e expressivo! 😊

### Template Literals em TypeScript

A partir do TypeScript 4.1, é possível criar **tipos compostos dinamicamente** com base em strings — algo especialmente útil para representar **valores formatados, nomes compostos ou chaves de objetos**.

```ts
type Status = "ativo" | "inativo";
type Mensagem = `Usuário está ${Status}`;

const m1: Mensagem = "Usuário está ativo";   // ✅ válido
const m2: Mensagem = "Usuário está ocupado"; // ❌ erro: não é um Status conhecido
```

### **Aplicação prática: composição de nomes ou chaves**

```ts
type NomeCampo = "nome" | "email" | "idade";
type EventoCampo = `onChange${Capitalize<NomeCampo>}`;

const evento: EventoCampo = "onChangeEmail"; // válido
```

Esse recurso se conecta diretamente com padrões usados em formulários, eventos, mensagens e APIs que seguem convenções de nomenclatura. Ele mostra como **o uso de template literals pode ser levado para o sistema de tipos**, oferecendo segurança adicional e **evitando erros de strings “mágicas”** espalhadas pelo código.

### Em suma!

Template literals representam uma evolução significativa na forma de lidar com strings em JavaScript. Eles tornam o código mais legível, expressivo e menos sujeito a erros, além de oferecer suporte nativo para interpolação de valores e strings multilinha. Seu uso é amplamente adotado no desenvolvimento moderno, especialmente quando se trabalha com interfaces dinâmicas e comunicação textual em aplicações React Native.

---
