---
layout: aula
title: "5. Resolvendo exercícios em Javascript!"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 5
---

## 5. Resolução dos Exercícios da Aula 01

Para consolidar o entendimento dos conceitos abordados até aqui — como desestruturação, arrow functions, operadores spread/rest, métodos de array e fundamentos do TypeScript — vamos agora retomar os exercícios propostos ao final da Aula 01. A ideia é analisar passo a passo suas resoluções, destacando como as práticas de JavaScript e os recursos de tipagem do TypeScript contribuem para um código mais alinhado com o estilo adotado no desenvolvimento com React Native. 😊


### ✅ Exercício 01 – `arrayUtils.js`

Este exercício propôs a análise de três funções utilitárias para manipulação de arrays: `unique`, `groupBy` e `sumBy`. Vamos agora revisá-las e entendê-las, relacionando sua lógica com os conceitos trabalhados nesta segunda aula.

#### 📌 Função `unique`

```js
export const unique = arr => [...new Set(arr)];
```

A função `unique` recebe um array e retorna um novo array contendo apenas os valores únicos. Isso é feito com o uso da estrutura de dados `Set`, que elimina automaticamente os elementos duplicados, e do operador **spread (`...`)**, que expande os valores do `Set` de volta em um array. Trata-se de uma abordagem declarativa e imutável para filtragem de duplicatas.

Agora imagine, por exemplo, que você tem uma lista de nomes que podem conter repetições:

```js
const nomes = ["Ana", "Carlos", "Ana", "João", "Carlos"];
const nomesUnicos = unique(nomes);

console.log(nomesUnicos);
// Saída: ["Ana", "Carlos", "João"]
```

Ao usarmos a função `unique`, a estrutura `Set` elimina automaticamente os elementos duplicados, e o operador spread `...` cria um novo array com os valores únicos. Esse padrão é útil para garantir que elementos repetidos sejam removidos de forma simples.

#### 📌 Função `groupBy`

```js
export const groupBy = (arr, key) =>
  arr.reduce((acc, obj) => {
    (acc[obj[key]] = acc[obj[key]] || []).push(obj);
    return acc;
  }, {});
```

A função `groupBy` agrupa os elementos do array de acordo com uma propriedade informada. Isso é feito com o método `reduce`, que constrói um novo objeto onde cada chave representa um grupo e os valores são arrays de objetos correspondentes. A construção condicional `acc[obj[key]] = acc[obj[key]] || []` assegura que a chave exista antes de fazer o `push`. Essa é uma aplicação típica do `reduce` para transformação de dados estruturados.

Para entender o funcionamento de forma mais concreta, suponha que temos um array de objetos representando produtos, e queremos agrupá-los por categoria:

```js
const produtos = [
  { nome: "Banana", categoria: "Frutas" },
  { nome: "Maçã", categoria: "Frutas" },
  { nome: "Cenoura", categoria: "Legumes" },
  { nome: "Alface", categoria: "Verduras" },
  { nome: "Brócolis", categoria: "Verduras" }
];
```

Se aplicarmos a função `groupBy` passando o array `produtos` e a chave `"categoria"`:

```js
const agrupados = groupBy(produtos, "categoria");
console.log(agrupados);
```

O resultado será:

```js
{
  Frutas: [
    { nome: "Banana", categoria: "Frutas" },
    { nome: "Maçã", categoria: "Frutas" }
  ],
  Legumes: [
    { nome: "Cenoura", categoria: "Legumes" }
  ],
  Verduras: [
    { nome: "Alface", categoria: "Verduras" },
    { nome: "Brócolis", categoria: "Verduras" }
  ]
}
```

Assim, o passo a passo da função será:

1. Começa com um objeto vazio `{}`.
2. Para cada item do array, verifica o valor da chave `categoria`.
3. Se esse valor ainda não existir como chave no acumulador `acc`, cria uma nova entrada com array vazio.
4. Adiciona o objeto atual ao array correspondente.

A linha crítica é esta:

```js
acc[obj[key]] = acc[obj[key]] || [];
```

Ela garante que, se `acc[obj[key]]` for `undefined`, ele será inicializado como `[]` antes de receber `.push(obj)`. 

#### 📌 Função `sumBy`

```js
export const sumBy = (arr, key) =>
  arr.reduce((total, obj) => total + (obj[key] ?? 0), 0);
```

A função `sumBy` soma os valores de uma determinada propriedade em um array de objetos. Utiliza o `reduce` para acumular o total e o operador de **coalescência nula (`??`)** para lidar com casos em que o valor da propriedade pode ser `undefined` ou `null`, evitando falhas silenciosas e garantindo que a soma seja precisa.

Imagine uma lista de produtos e você quer somar todos os preços:

```js
const produtos = [
  { nome: "Caneta", preco: 2.5 },
  { nome: "Caderno", preco: 15 },
  { nome: "Borracha" } // preço ausente
];

const total = sumBy(produtos, "preco");

console.log(total);
// Saída: 17.5
```

A função percorre os objetos com `reduce`, somando os valores da chave `preco`. Caso a chave esteja ausente, o operador de coalescência nula `??` garante que o valor somado será `0`. 


#### ✅ Em síntese

A resolução deste exercício aplica alguns dos conceitos centrais do JavaScript que foram revisados nesta aula: o **operador spread**, o uso de **funções arrow**, a **imutabilidade** na transformação de dados, o poder expressivo do método **reduce**, além da **acessibilidade dinâmica de propriedades** com `obj[key]`. Além disso, vimos a aplicação da **coalescência nula (`??`)** como forma de garantir segurança na leitura de propriedades.

Essas três funções também exemplificam o paradigma da **programação funcional**, pois operam sobre dados de forma pura e previsível, retornando novos valores sem modificar os originais. 

Sim, eu sei que não havíamos visto nada disso quando pedi os exercícios a vocês  — mas, agora, com a explicação, fica mais fácil entender e relacionar as ideias, né? Essa é justamente a proposta: apresentar desafios e, em seguida, esclarecer os fundamentos. 🤩

### ✅ Exercício 02 – Migração para `arrayUtils.ts`

Neste exercício, o desafio foi migrar as funções utilitárias do JavaScript puro para uma versão **tipada com TypeScript**, utilizando recursos como generics, inferência de tipos e validação estática. Vamos revisar as versões finais de cada função, relacionando-as com os conceitos apresentados nesta segunda aula.

#### 📌 Função `unique`

```ts
export const unique = <T>(arr: T[]): T[] => [...new Set(arr)];
```

A função `unique` tipada com TypeScript segue a mesma lógica da versão anterior, mas agora utiliza o tipo genérico `<T>` para garantir que o array recebido e o array retornado mantenham o mesmo tipo. Isso permite que a função seja reutilizável com qualquer tipo de dado — seja `string`, `number`, ou até mesmo objetos complexos.

Exemplo com strings:

```ts
const nomes = ["Ana", "Carlos", "Ana", "João"];
const nomesUnicos = unique(nomes);

console.log(nomesUnicos);
// Saída: ["Ana", "Carlos", "João"]
```

Aqui, o TypeScript infere corretamente que `nomesUnicos` é um `string[]`, e fornece autocompletar e validação de tipos.

#### 📌 Função `groupBy`

```ts
export const groupBy = <T, K extends keyof T>(
  arr: T[],
  key: K
): Record<string, T[]> =>
  arr.reduce((acc, obj) => {
    const k = String(obj[key]);
    acc[k] = acc[k] || [];
    acc[k].push(obj);
    return acc;
  }, {} as Record<string, T[]>);
```

A função `groupBy` agora está tipada com dois parâmetros genéricos: `T`, representando o tipo dos objetos do array, e `K`, que representa a chave usada para agrupar. O uso de `K extends keyof T` garante que apenas chaves válidas do tipo `T` possam ser utilizadas, evitando erros comuns.

Suponha que temos um array de produtos e queremos agrupá-los por categoria:

```ts
type Produto = { nome: string; categoria: string };

const produtos: Produto[] = [
  { nome: "Banana", categoria: "Frutas" },
  { nome: "Maçã", categoria: "Frutas" },
  { nome: "Cenoura", categoria: "Legumes" }
];

const agrupados = groupBy(produtos, "categoria");

console.log(agrupados);
```

Saída esperada:

```ts
{
  Frutas: [
    { nome: "Banana", categoria: "Frutas" },
    { nome: "Maçã", categoria: "Frutas" }
  ],
  Legumes: [
    { nome: "Cenoura", categoria: "Legumes" }
  ]
}
```

A tipagem com `Record<string, T[]>` informa ao compilador e ao desenvolvedor que o retorno será um objeto onde as chaves são strings e os valores são arrays de elementos do tipo `T`.

#### 📌 Função `sumBy`

```ts
export const sumBy = <T>(arr: T[], key: keyof T): number =>
  arr.reduce((total, obj) => total + (Number(obj[key]) || 0), 0);
```

A função `sumBy` foi tipada para aceitar qualquer tipo de objeto `T`, mas exige que a `key` seja uma propriedade válida (`keyof T`). A conversão explícita com `Number()` garante que o retorno sempre seja numérico, mesmo que a propriedade contenha `string` ou `undefined`.

Suponha que temos um array de despesas:

```ts
type Despesa = { item: string; valor?: number };

const despesas: Despesa[] = [
  { item: "Transporte", valor: 20 },
  { item: "Almoço", valor: 35 },
  { item: "Café" }
];

const total = sumBy(despesas, "valor");

console.log(total);
// Saída: 55
```

Mesmo com uma propriedade ausente (`valor`), a função continua funcionando com segurança graças ao uso de `Number(obj[key]) || 0`.

#### ✅ Em síntese

A migração para TypeScript reforça os conceitos de **tipagem genérica**, **uso de `keyof` e `extends` para validação de chaves**, e o emprego de **utility types** como `Record`. Além disso, os exemplos mostraram como o TypeScript melhora a experiência do desenvolvedor, fornecendo validação estática, sugestões automáticas e proteção contra erros comuns.

Com isso, não apenas reforçamos os recursos da linguagem, mas também mantivemos o compromisso com **funções puras, reutilizáveis e previsíveis** — um dos pilares da programação funcional que estamos adotando com React Native.

E sim, como aconteceu no exercício anterior, talvez no momento da proposta as tipagens parecessem desafiadoras — mas agora que revisitamos os fundamentos, começa a fazer mais sentido, né? 

## ✅ Exercício 03 – `pokedex.ts`: Consulta à PokéAPI via CLI

Neste terceiro exercício, desenvolvemos uma pequena aplicação de linha de comando (CLI) em TypeScript para consultar dados de um Pokémon usando a [PokéAPI](https://pokeapi.co/). A proposta serviu para aplicar diversos conceitos de JavaScript moderno e TypeScript em um contexto prático e mais próximo do "mundo real": leitura de argumentos via terminal, chamadas assíncronas com `fetch`, tratamento de exceções, manipulação de dados com `.map()` e `.join()`, além da organização do código em funções puras e tipadas.

#### 📌 Etapa 1 – Captura do argumento via terminal

```ts
const entrada = process.argv[2];

if (!entrada) {
  console.log("❌ Informe um nome ou ID de Pokémon.");
  process.exit(1);
}
```

Utilizamos o array `process.argv` para capturar o valor passado na linha de comando (por exemplo: `pikachu`). A verificação `if (!entrada)` garante que o programa só continue se o usuário fornecer um argumento válido.

#### 📌 Etapa 2 – Função principal com `async/await`

```ts
async function buscarPokemon(idOuNome: string): Promise<void> {
  const url = `https://pokeapi.co/api/v2/pokemon/${idOuNome.toLowerCase()}`;

  try {
    const resposta = await fetch(url);

    if (!resposta.ok) {
      if (resposta.status === 404) {
        console.log("❌ Pokémon não encontrado!");
      } else {
        console.log("⚠️ Erro na API. Código:", resposta.status);
      }
      return;
    }

    const dados = await resposta.json();

    const nome: string = dados.name;
    const altura: number = dados.height / 10; // em metros
    const peso: number = dados.weight / 10;   // em kg
    const tipos: string = dados.types
      .map((tipo: any) => tipo.type.name)
      .join(" / ");

    console.log(`${capitalize(nome)} – ${altura} m – ${peso} kg – ${tipos}`);
  } catch {
    console.log("⚠️ Erro de rede. Tente novamente.");
  }
}
```

A função `buscarPokemon` é assíncrona (`async`) e retorna `void`. Ela faz a chamada à API usando `fetch`, trata erros com `try/catch`, e formata os dados para exibição. É importante notar que em JavaScript, operações assíncronas — como chamadas de API, leitura de arquivos ou interações com banco de dados — **não retornam imediatamente um valor**, mas sim uma **Promise**, que representa um valor que *ainda não está disponível*, mas será resolvido no futuro.

Assim, a palavra-chave `async` transforma a função `buscarPokemon` em uma função assíncrona que sempre retorna uma `Promise`. O tipo `Promise<void>` indica que a função **não retorna um valor útil** (como `string` ou `number`), mas apenas finaliza seu trabalho de forma assíncrona.

A grande vantagem de usar `async/await` é a **clareza do código**: podemos escrever chamadas assíncronas como se fossem síncronas, evitando encadeamentos confusos com `.then()` e `.catch()`. O uso de `await` **pausa a execução da função até que a Promise seja resolvida**, permitindo trabalhar com os dados como se já estivessem disponíveis — o que é muito útil em funções como `buscarPokemon`, onde cada etapa depende da anterior.

Já a linha seguinte, com `.map(...).join(" / ")`, demonstra a aplicação de métodos de array em um contexto real para construir uma string de tipos do Pokémon (ex: `"electric"` ou `"fire / flying"`).

#### 📌 Função auxiliar `capitalize`

```ts
function capitalize(texto: string): string {
  return texto.charAt(0).toUpperCase() + texto.slice(1);
}
```

Essa função transforma a primeira letra da string em maiúscula — exemplo de função pura, reaproveitável e tipada.

#### 📌 Etapa 3 – Execução

```ts
buscarPokemon(entrada);
```

Com isso, iniciamos o programa passando o argumento capturado no terminal.

#### 🧪 Exemplo de uso

```bash
npx ts-node pokedex.ts pikachu
```

Saída esperada:

```
Pikachu – 0.4 m – 6 kg – electric
```

### 🧾 Código completo – `pokedex.ts`

```ts
const entrada = process.argv[2];

if (!entrada) {
  console.log("❌ Informe um nome ou ID de Pokémon.");
  process.exit(1);
}

function capitalize(texto: string): string {
  return texto.charAt(0).toUpperCase() + texto.slice(1);
}

async function buscarPokemon(idOuNome: string): Promise<void> {
  const url = `https://pokeapi.co/api/v2/pokemon/${idOuNome.toLowerCase()}`;

  try {
    const resposta = await fetch(url);

    if (!resposta.ok) {
      if (resposta.status === 404) {
        console.log("❌ Pokémon não encontrado!");
      } else {
        console.log("⚠️ Erro na API. Código:", resposta.status);
      }
      return;
    }

    const dados = await resposta.json();

    const nome: string = dados.name;
    const altura: number = dados.height / 10;
    const peso: number = dados.weight / 10;
    const tipos: string = dados.types
      .map((tipo: any) => tipo.type.name)
      .join(" / ");

    console.log(`${capitalize(nome)} – ${altura} m – ${peso} kg – ${tipos}`);
  } catch {
    console.log("⚠️ Erro de rede. Tente novamente.");
  }
}

buscarPokemon(entrada);
```

#### ✅ Em síntese

Este exercício reuniu vários aspectos centrais da programação moderna com TypeScript:

* Uso de **funções assíncronas (`async/await`)**
* Manipulação de dados com `.map()` e `.join()` — reforçando o paradigma funcional abordado na Seção 1.7
* Tratamento de erros com `try/catch`, promovendo resiliência do código
* Tipagem explícita de variáveis, parâmetros e retorno de funções
* Funções auxiliares puras (`capitalize`) para promover clareza e reutilização

Além disso, introduzimos o uso de APIs públicas e da interface de linha de comando (CLI) — temas muito presentes em projetos reais, inclusive para ferramentas internas ou utilitários de integração.

A essa altura, pode parecer que o desafio era grande para o momento em que ele foi proposto... e talvez fosse mesmo! 😅 Mas acredito na capacidade de vocês, e a elaboração de tarefas desafiadoras é importante. Essa abordagem — propondo antes e explicando depois — ajuda a transformar a experiência prática (o famosos _quebrar a cabeça_) em aprendizado consolidado.

"Ah, professor, mas eu não consigo..."

**CONSEGUE SIM‼️** 🗣️📢

### Conclusão 

Nesta aula, fizemos mais do que uma revisão de JavaScript moderno e uma introdução técnica ao TypeScript — **preparamos o terreno para o desenvolvimento de aplicações reais em React Native com segurança, clareza e produtividade**.

Cada recurso abordado tem um papel direto na prática que teremos a seguir:

* **O uso de `let` e `const`**, com escopo de bloco e previsibilidade, é essencial para evitar efeitos colaterais indesejados na manipulação de estado;
* **A desestruturação de arrays e objetos** será onipresente na interação com props, hooks e retornos de APIs;
* **Arrow functions** são padrão em handlers, callbacks e componentes funcionais — e seu comportamento com `this` evita muitos erros silenciosos;
* **Template literals** permitem compor strings dinâmicas para interfaces, mensagens e URLs de requisição de forma legível e expressiva;
* **Métodos como `map`, `filter` e `reduce`** serão a base da renderização de listas, filtragem de dados e construção de estados derivados;
* **A tipagem com TypeScript**, incluindo tipos primitivos, interfaces, generics e utility types, **não é um luxo, mas uma necessidade prática** para prevenir erros e melhorar a comunicação entre as partes do sistema;
* Finalmente, os exercícios propostos e resolvidos **mostraram que podemos aplicar tudo isso em problemas reais**, como criar utilitários reutilizáveis, validar dados, e integrar com APIs.

Em resumo: **toda essa base teórica e prática é o alicerce do desenvolvimento com React Native**. A partir da próxima aula, começaremos a abordar o desenvolvimento mobile _de fato_. E faremos tudo isso de forma tipada, modular e segura — exatamente porque dominamos os fundamentos revistos aqui.

Se você achava que essa aula era só “revisão”... agora dá pra ver que **ela é o pilar da nossa fluência no ecossistema mobile moderno**. 🚀

Antes da próxima Aula, entretanto, sabem o que é interessante para consolidar todo esse conhecimento?

Sim, é isso mesmo: exercícios!