---
layout: aula
title: "3. Introdução ao TypeScript"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 3
---

## 3. TypeScript

## 3.1 Tipos Primitivos e Anotação de Tipos

O TypeScript é um superconjunto do JavaScript que adiciona **tipagem estática opcional** à linguagem. Isso significa que podemos **declarar explicitamente o tipo de cada variável, parâmetro de função, retorno e estrutura de dados** — e o compilador verifica se os usos estão coerentes com os tipos declarados.

Essa validação ocorre **em tempo de desenvolvimento**, ajudando a capturar erros antes mesmo de executar o código.

### Tipos primitivos em TypeScript

Os **tipos primitivos** disponíveis em TypeScript são os mesmos do JavaScript, mas com a vantagem de que agora podemos expressá-los de forma clara e explícita:

* `string` — sequência de caracteres
* `number` — números inteiros e decimais (não há distinção entre int e float)
* `boolean` — verdadeiro ou falso
* `null` e `undefined` — valores especiais para ausência
* `bigint` — números inteiros muito grandes (pouco usado em aplicações mobile)
* `symbol` — usado para identificadores únicos (mais avançado)

#### Exemplo básico:

```ts
let nome: string = "Camila";
let idade: number = 28;
let ativo: boolean = true;
```

A sintaxe `nome: string` indica ao TypeScript que a variável `nome` deve conter apenas valores do tipo string. Se tentarmos atribuir outro tipo, o compilador acusará erro:

```ts
nome = 42; // ❌ Erro: number não é atribuível a string
```

### Inferência de tipos

Você **não é obrigado a declarar tipos o tempo todo**. O TypeScript é inteligente o suficiente para **inferir o tipo com base no valor inicial**, e só exige declaração explícita quando não consegue determinar o tipo com segurança.

```ts
let cidade = "São Paulo"; // inferido como string
let pontos = 100;         // inferido como number
```

No entanto, sempre que quiser **documentar a intenção** ou **evitar ambiguidade**, é recomendável declarar os tipos explicitamente — especialmente em variáveis exportadas, parâmetros de função e dados de entrada externos.

### Funções com tipos

Também é possível (e altamente recomendável) **tipar os parâmetros e o valor de retorno das funções**. Isso ajuda o TypeScript a garantir que a função seja usada corretamente em qualquer lugar.

```ts
function saudar(nome: string): string {
  return `Olá, ${nome}`;
}
```

Neste caso:

* `nome: string` define o tipo do parâmetro.
* `: string` após os parênteses indica que a função **retorna uma string**.

Se alguém tentar chamar `saudar(123)`, o TypeScript apontará um erro imediato.

### Tipos `undefined` e `null`

Por padrão, o TypeScript considera que uma variável tipada como `string` ou `number` **não pode receber `undefined` ou `null`**, a menos que seja explicitamente permitido.

```ts
let email: string = "a@b.com";
email = undefined; // ❌ Erro

let telefone: string | undefined;
telefone = undefined; // ✅ permitido por causa da união de tipos
```

Essa distinção é importante para evitar acessos indevidos a variáveis não inicializadas ou ausentes — um problema comum em JavaScript puro.

### Observação sobre `any`

O tipo especial `any` representa **qualquer tipo de dado**, desativando temporariamente a verificação de tipo do TypeScript. Ele deve ser usado com cautela, apenas quando o tipo do dado é realmente desconhecido (ex: dados vindos de APIs sem contrato definido).

```ts
let dado: any = "texto";
dado = 42; // permitido
```

Embora `any` possa parecer prático, seu uso em excesso **anula os benefícios do TypeScript**, e deve ser evitado em código bem estruturado. 👆🏻🤓

### Aplicações práticas em React Native

A tipagem de variáveis é extremamente útil ao desenvolver componentes ou usar hooks:

```tsx
const [contador, setContador] = useState<number>(0);

function incrementar(valor: number): void {
  setContador(prev => prev + valor);
}
```

Ao declarar `useState<number>`, garantimos que o contador será sempre um número, e evitamos erros como:

```ts
setContador("um"); // ❌ Erro: string não é atribuível a number
```

Esse tipo de segurança é particularmente importante quando trabalhamos com **eventos, dados externos e interações com o usuário**.

### Conclusões

Com a tipagem básica do TypeScript, ganhamos **clareza, segurança e confiabilidade**. Ao explicitar os tipos de variáveis e funções, conseguimos capturar erros logo na escrita do código, além de melhorar o autocompletar das IDEs, facilitar refatorações e documentar de forma implícita as regras de negócio.

Essa base é necessária para explorarmos, nas próximas seções, tipos compostos, interfaces, enums, generics e validação estrutural.


---

## 3.2 Tipos Compostos: Arrays, Tuplas e União de Tipos

Após dominar os **tipos primitivos**, é natural expandirmos para **estruturas compostas**, que representam **coleções de dados** ou **valores alternativos possíveis**. No TypeScript, isso é feito com os tipos de **arrays**, **tuplas** e **uniões**.

Esses recursos são essenciais em qualquer aplicação real — inclusive no desenvolvimento mobile com React Native — pois nos permitem modelar listas de dados, respostas de APIs, props opcionais e muito mais.


### Arrays

Arrays em TypeScript podem ser tipados de duas formas equivalentes:

1. **Notação com colchetes**:

   ```ts
   let numeros: number[] = [1, 2, 3];
   ```

2. **Notação genérica**:

   ```ts
   let nomes: Array<string> = ["Ana", "Carlos"];
   ```

Ambas são aceitas, e a escolha entre elas é uma questão de preferência. A notação com colchetes é mais comum e mais legível no contexto do React Native.

#### Acessos e validações

O TypeScript garante que apenas elementos do tipo especificado podem ser inseridos:

```ts
numeros.push(4);      // OK
numeros.push("cinco"); // ❌ Erro: string não é atribuível a number
```

Ao acessar um índice, o TypeScript sabe o tipo esperado:

```ts
const primeiro = nomes[0]; // string
```

### Arrays de objetos

Ao tipar arrays de objetos, usamos o mesmo raciocínio, definindo o tipo dos itens:

```ts
type Produto = { nome: string; preco: number };

const lista: Produto[] = [
  { nome: "Caderno", preco: 10 },
  { nome: "Caneta", preco: 2 }
];
```

Isso permite acessar e manipular dados com autocompletar, validação e segurança — muito útil em listas renderizadas com `FlatList`, por exemplo:

```tsx
<FlatList
  data={lista}
  renderItem={({ item }) => <Text>{item.nome} - R$ {item.preco}</Text>}
/>
```

### Tuplas

Uma **tupla** é um array de tamanho fixo e com tipos diferentes em posições específicas. Ela é útil quando a **ordem e o tipo de cada elemento importam** — por exemplo, quando uma função retorna múltiplos valores com significados distintos.

```ts
const coordenadas: [number, number] = [10.5, 20.3];
```

A variável `coordenadas` só aceita dois números, nessa ordem. Qualquer valor a mais ou tipos errados geram erro:

```ts
coordenadas[0] = 99;       // OK
coordenadas[1] = "alto";   // ❌ Erro
coordenadas[2] = 42;       // ❌ Erro: índice fora do comprimento
```

Tuplas são muito utilizadas em **hooks do React**, como `useState`:

```ts
const [contador, setContador]: [number, (valor: number) => void] = useState(0);
```

### União de Tipos (`|`)

A **união de tipos** permite que uma variável aceite **dois ou mais tipos diferentes**. Esse recurso é especialmente útil para representar situações em que há múltiplas formas válidas de um dado aparecer — por exemplo, resposta de API que pode ser `string` ou `null`, props opcionais ou tipos discriminados.

```ts
let status: "carregando" | "sucesso" | "erro";

status = "carregando"; // OK
status = "falha";      // ❌ Erro: "falha" não é um dos valores permitidos
```

Essa união também pode ser usada entre tipos básicos:

```ts
let valor: number | string;

valor = 42;        // OK
valor = "quarenta"; // OK
valor = true;      // ❌ Erro
```

E em combinações com arrays ou objetos:

```ts
type Resultado = { sucesso: true; dados: string[] } | { sucesso: false; erro: string };

const resposta: Resultado = {
  sucesso: false,
  erro: "Conexão perdida"
};
```

Esse padrão é extremamente útil em TypeScript moderno, e muito comum em **validações, parsing de JSON e controle de fluxo condicional**.

### Conclusões

Os **tipos compostos** são ferramentas poderosas para modelar dados reais com precisão. Arrays permitem representar listas homogêneas, tuplas permitem combinar valores heterogêneos com significado posicional, e uniões trazem flexibilidade com segurança, permitindo expressar alternativas válidas sem perder o controle do tipo.

Esses recursos são a ponte entre a simplicidade dos tipos primitivos e a expressividade de estruturas complexas. Nas próximas seções, vamos expandir ainda mais esse vocabulário com **objetos, interfaces, enums e tipos literais**, que permitem modelar domínios de forma mais clara e robusta.

---
