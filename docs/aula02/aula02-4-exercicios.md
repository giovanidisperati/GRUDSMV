---
layout: aula
title: "5. Exercícios Propostos"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 4
---

## 2.3 Objetos e Interfaces

Em TypeScript, além de tipar valores primitivos, arrays e tuplas, é essencial saber **como declarar e estruturar objetos** com segurança. Objetos estão no centro de praticamente toda aplicação: representam entidades, props, respostas de APIs, estados de componentes e muito mais.

Nesta seção, veremos como **tipar objetos diretamente**, como **criar interfaces reutilizáveis**, e por que isso é fundamental para organização, clareza e manutenção de projetos em React Native.

### Tipagem direta de objetos

Você pode declarar objetos com tipos diretamente, utilizando anotações inline:

```ts
const usuario: { nome: string; idade: number } = {
  nome: "Camila",
  idade: 28
};
```

Essa sintaxe funciona bem para objetos simples e usos pontuais. Porém, à medida que os objetos crescem ou são reutilizados em múltiplos lugares, o código se torna repetitivo. É aí que entram as interfaces.

### Interfaces

Uma **interface** é uma forma de **declarar a estrutura de um objeto com nome**. Ela descreve quais propriedades o objeto deve ter e quais os tipos de cada uma.

```ts
interface Usuario {
  nome: string;
  idade: number;
}

const user: Usuario = {
  nome: "Carlos",
  idade: 30
};
```

Interfaces facilitam a leitura, promovem o reuso e servem como **contrato entre diferentes partes do sistema** — por exemplo, entre o componente que recebe uma prop e aquele que a envia.

### Interfaces em componentes React Native

```tsx
interface Props {
  titulo: string;
  ativo: boolean;
}

const MeuBotao = ({ titulo, ativo }: Props) => {
  return (
    <Button
      title={ativo ? titulo : "Desativado"}
      onPress={() => console.log("Clique")}
    />
  );
};
```

Aqui, `Props` define de forma clara o que o componente espera. Se alguém tentar passar uma prop ausente ou com o tipo errado, o TypeScript acusará o erro imediatamente.

### Propriedades opcionais

Você pode tornar propriedades **opcionais** com o operador `?`. Isso é útil quando uma informação **pode ou não estar presente** — como valores default, flags ou dados carregados de forma assíncrona.

```ts
interface Produto {
  nome: string;
  preco: number;
  descricao?: string;
}

const item: Produto = {
  nome: "Caderno",
  preco: 12.5
  // descrição pode estar ausente
};
```

Ao acessar uma propriedade opcional, o TypeScript exige que você lide com a possibilidade de ela ser `undefined`.

### Leitura e segurança

Interfaces servem como documentação viva. IDEs como VSCode mostram os campos esperados, alertam sobre erros de tipo e oferecem autocompletar com base na interface.

```ts
function exibir(produto: Produto) {
  console.log(produto.nome);
  console.log(produto.descricao?.toUpperCase());
}
```

Aqui usamos o **operador de encadeamento opcional (`?.`)** para acessar `descricao` apenas se ela existir, evitando erros em tempo de execução.

### Interface x Type Alias

Você também pode usar a palavra-chave `type` para criar tipos nomeados. Para objetos simples, `type` e `interface` são equivalentes:

```ts
type Usuario = {
  nome: string;
  idade: number;
};
```

Em geral:

* **`interface`** é mais apropriada para objetos e componentes, podendo ser **extendida**.
* **`type`** é mais flexível e permite criar **uniões, interseções, tipos literais, etc.**

Exemplo de extensão com interface:

```ts
interface Pessoa {
  nome: string;
}

interface Funcionario extends Pessoa {
  salario: number;
}

const f: Funcionario = {
  nome: "Joana",
  salario: 3000
};
```

### Exemplo integrado: lista de tarefas

```ts
interface Tarefa {
  id: number;
  titulo: string;
  concluida: boolean;
}

const tarefas: Tarefa[] = [
  { id: 1, titulo: "Estudar", concluida: false },
  { id: 2, titulo: "Exercício", concluida: true }
];

const pendentes = tarefas.filter(t => !t.concluida);
```

Esse padrão é comum em apps de produtividade, listas de compras, controle de hábitos, etc. Tipar as tarefas com uma interface clara evita problemas como campos ausentes ou inconsistentes.

### Conclusões

A tipagem de objetos com **interfaces** é uma das maiores forças do TypeScript. Ela melhora a organização do código, previne erros, facilita a leitura e reduz retrabalho. Em aplicações React Native, interfaces são indispensáveis para tipar props de componentes, estados de tela, dados externos e objetos de negócio.

A seguir, veremos como trabalhar com **enums, tipos literais e validação por valor**, para tornar nossas estruturas ainda mais expressivas e seguras.

---

## 2.4 Enums, Tipos Literais e Discriminação de Tipos

À medida que nossas aplicações crescem, surgem casos em que precisamos **restringir o valor de uma variável a um conjunto específico de opções válidas** — por exemplo, o status de uma tarefa, a categoria de um produto ou o papel de um usuário no sistema.

Em TypeScript, isso pode ser feito de forma **segura e clara** por meio de três mecanismos complementares:

* **Enums** (enumeradores)
* **Tipos literais**
* **Discriminação de tipos** (ou tipos “tagueados”)

Esses recursos ajudam a representar regras de negócio, validar dados e garantir que nossos objetos sigam formatos válidos — tudo com suporte de autocompletar e checagem em tempo de desenvolvimento.

### Enums (Enumeradores)

Enums são estruturas que **agrupam valores nomeados** e podem ser usados para representar conjuntos fechados, como estados ou categorias.

#### Enum numérico

```ts
enum Status {
  Pendente,
  EmAndamento,
  Concluida
}

let estado: Status = Status.EmAndamento;
console.log(estado); // 1
```

Os valores atribuídos são numéricos por padrão (`0`, `1`, `2`…), mas podemos definir valores literais se preferirmos mais legibilidade:

#### Enum com strings

```ts
enum PapelUsuario {
  Admin = "ADMIN",
  Editor = "EDITOR",
  Leitor = "LEITOR"
}

const permissao: PapelUsuario = PapelUsuario.Editor;
```

Esse padrão é útil para tokens de permissão, rotas, filtros e campos que precisam ser persistidos como texto — por exemplo, em um banco de dados ou localStorage.

#### Uso com objetos e componentes

```tsx
interface Usuario {
  nome: string;
  papel: PapelUsuario;
}

function podeEditar(usuario: Usuario): boolean {
  return usuario.papel === PapelUsuario.Admin || usuario.papel === PapelUsuario.Editor;
}
```

### Tipos Literais

Tipos literais restringem o valor de uma variável a **valores exatos**, usando **strings, números ou booleanos fixos**.

```ts
type Estado = "pendente" | "em_andamento" | "concluida";

let status: Estado = "pendente";

status = "concluida"; // ✅ OK
status = "cancelada"; // ❌ Erro
```

Esse recurso é especialmente útil quando o conjunto de valores válidos é pequeno e não há necessidade de um enum separado. Ele é **mais leve**, **mais fácil de combinar com tipos de união** e **muito utilizado em APIs e tipos de props**.

### Discriminação de Tipos (Tipos Tagueados)

Quando usamos **tipos literais como identificadores internos** de objetos, podemos criar estruturas que permitem ao TypeScript **inferir automaticamente os campos disponíveis** com base em um valor.

Esse padrão é conhecido como **discriminated union** (união discriminada) ou **tagged union**.

#### Exemplo: respostas de API

```ts
type Sucesso = { tipo: "sucesso"; dados: string[] };
type Erro = { tipo: "erro"; mensagem: string };

type Resultado = Sucesso | Erro;

function processar(r: Resultado) {
  if (r.tipo === "sucesso") {
    console.log("Dados:", r.dados);
  } else {
    console.log("Erro:", r.mensagem);
  }
}
```

Esse padrão é muito poderoso porque permite que o TypeScript **refine automaticamente os tipos disponíveis** de acordo com a verificação do valor de `tipo`.

### Comparando Enum e Tipo Literal

| Recurso            | Quando usar                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------- |
| `enum`             | Quando há necessidade de valores nomeados reutilizáveis ou compatíveis com outras linguagens |
| `type` com literal | Quando o conjunto de valores é simples e direto, sem lógica associada                        |

Exemplo com enum:

```ts
enum Tema {
  Claro = "claro",
  Escuro = "escuro"
}
```

Exemplo com tipo literal:

```ts
type Tema = "claro" | "escuro";
```

No contexto do React Native, tipos literais costumam ser preferidos para props e estados locais, enquanto enums são úteis para representar **papéis de usuário, tipos de entidade, modos de operação ou configurações globais**.

### Conclusões

O uso de **enums, tipos literais e estruturas discriminadas** permite que o TypeScript funcione como uma **camada de validação semântica** sobre o JavaScript. Isso facilita a modelagem de regras de negócio, evita valores inválidos, e fornece documentação automática por meio da própria definição dos tipos.

Vamos, agora, aprofundar o uso de **generics, utilitários de tipo e boas práticas de organização** para garantir que nossos tipos se mantenham reutilizáveis, consistentes e fáceis de evoluir ao longo do tempo.

---

## 2.5 Generics: Reutilização com Segurança

**Generics** são um dos recursos mais poderosos do TypeScript, pois permitem escrever **código reutilizável e ao mesmo tempo fortemente tipado**. Eles resolvem um problema clássico: como criar **funções, interfaces ou classes** que funcionem com **diferentes tipos de dados**, mas **sem abrir mão da verificação estática**?

Essa funcionalidade é especialmente útil em funções utilitárias, hooks personalizados, componentes que lidam com dados genéricos e até na modelagem de estruturas como listas, formulários e respostas de API.

### Motivação: o problema do `any`

Suponha que você deseje criar uma função para retornar o primeiro item de um array:

```ts
function primeiro(arr: any[]) {
  return arr[0];
}
```

Essa função funciona — mas como usamos `any`, o TypeScript **perde completamente a noção do tipo** dos dados. Isso anula os benefícios da tipagem estática:

```ts
const nome = primeiro(["Ana", "Carlos"]);
nome.toUpperCase(); // ❌ Erro só em tempo de execução
```

### Solução: uso de Generics

Generics nos permitem **declarar tipos como variáveis de tipo**, e depois **substituí-los de forma automática** com base no uso real da função.

```ts
function primeiro<T>(arr: T[]): T {
  return arr[0];
}
```

* `T` é um **parâmetro de tipo**.
* `T[]` indica que a função recebe um array de elementos do tipo T.
* A função retorna um valor do mesmo tipo T.

Agora, ao usar a função, o TypeScript **infere automaticamente** o tipo com base nos argumentos:

```ts
const nome = primeiro(["Ana", "Carlos"]); // T é string
const numero = primeiro([10, 20, 30]);     // T é number

nome.toUpperCase(); // ✅ OK
numero.toFixed(2);  // ✅ OK
```

A função se tornou **genérica**, mas continua **tipada com precisão** — um equilíbrio perfeito entre flexibilidade e segurança.

### Generics com Objetos

Podemos aplicar generics para funções que manipulam objetos sem perder informação:

```ts
function extrairChave<T, K extends keyof T>(obj: T, chave: K): T[K] {
  return obj[chave];
}

const usuario = { nome: "Luana", idade: 30 };

const valor = extrairChave(usuario, "nome"); // valor: string
```

* `T` representa o tipo do objeto.
* `K` representa uma **chave válida dentro de T**.
* `T[K]` representa o tipo do valor correspondente à chave.

Esse padrão é extremamente útil em bibliotecas, hooks e validações genéricas.

### Generics com React Hooks

Ao criar hooks personalizados, usar generics permite que eles funcionem com qualquer tipo de dado:

```ts
function useLista<T>(inicial: T[]) {
  const [itens, setItens] = useState<T[]>(inicial);

  function adicionar(item: T) {
    setItens(prev => [...prev, item]);
  }

  return { itens, adicionar };
}
```

Uso:

```tsx
const { itens, adicionar } = useLista<string>(["Olá", "Oi"]);
adicionar("Bom dia"); // ✅ OK

const numeros = useLista<number>([1, 2, 3]);
numeros.adicionar(4);
```

O hook continua genérico, mas com **tipagem total** em cada uso.

### Generics em Interfaces

Também é possível criar **interfaces genéricas**, que se adaptam ao tipo de dado fornecido:

```ts
interface ApiResponse<T> {
  sucesso: boolean;
  dados: T;
}

const resposta1: ApiResponse<string[]> = {
  sucesso: true,
  dados: ["a", "b", "c"]
};

const resposta2: ApiResponse<{ id: number; nome: string }> = {
  sucesso: true,
  dados: { id: 1, nome: "Produto" }
};
```

Esse padrão é especialmente útil para representar **respostas de APIs REST**, **resultados paginados**, **estados genéricos**, entre outros.

### Conclusões

**Generics** permitem escrever código flexível **sem sacrificar a segurança de tipos**. Eles são fundamentais para criar funções, hooks, interfaces e componentes **reutilizáveis e confiáveis**, reduzindo repetição e erros. Em React Native com TypeScript, seu uso é altamente recomendado — seja em listas de dados, estados compartilhados, componentes de formulário ou APIs.

Nas próximas seções, vamos conhecer **utilitários de tipos** que tornam essas estruturas ainda mais expressivas, como `Partial`, `Pick`, `Omit`, `Record` e `Readonly`.

---

## 2.6 Utilitários de Tipo (Utility Types)

O TypeScript fornece um conjunto de **utilitários prontos**, conhecidos como **utility types**, que permitem **transformar, adaptar ou derivar tipos existentes** de maneira segura e sem repetição.

Esses utilitários são especialmente úteis para **refatorar código**, **criar variações parciais ou restritas de objetos**, e **gerar estruturas auxiliares** que mantenham a consistência do sistema. Eles são amplamente usados em projetos React/React Native — seja para definir props parciais, omitir campos sensíveis, lidar com formulários ou construir APIs tipadas.

A seguir, vamos ver os mais importantes e como usá-los na prática.

### `Partial<T>`

Converte todas as propriedades de um tipo para **opcionais**.

```ts
interface Usuario {
  nome: string;
  email: string;
  idade: number;
}

const atualizacao: Partial<Usuario> = {
  email: "novo@email.com"
};
```

Esse padrão é comum ao **atualizar dados** parcialmente, como num formulário, num `PATCH`, ou em um `setState`.

### `Required<T>`

Converte todas as propriedades de um tipo para **obrigatórias** (oposto do `Partial`).

```ts
interface Config {
  modo?: string;
  verbose?: boolean;
}

const c: Required<Config> = {
  modo: "escuro",
  verbose: true
};
```

Útil quando queremos forçar o preenchimento de dados em contextos específicos, como em um componente que depende de uma configuração completa.

### `Readonly<T>`

Torna todas as propriedades de um tipo **imutáveis** (não podem ser reatribuídas).

```ts
interface Produto {
  nome: string;
  preco: number;
}

const p: Readonly<Produto> = {
  nome: "Caderno",
  preco: 10
};

p.preco = 15; // ❌ Erro: não é possível modificar um campo readonly
```

Esse utilitário é útil para evitar mutações acidentais — por exemplo, ao trabalhar com objetos que representam dados fixos ou retornos de funções puras.

### `Pick<T, K>`

Cria um tipo que **extrai um subconjunto de propriedades** de outro tipo.

```ts
interface Usuario {
  id: number;
  nome: string;
  senha: string;
}

type UsuarioPublico = Pick<Usuario, "id" | "nome">;

const u: UsuarioPublico = {
  id: 1,
  nome: "Camila"
};
```

Muito útil para **filtrar campos seguros** que podem ser exibidos ou enviados ao front-end.

### `Omit<T, K>`

Faz o oposto de `Pick`: **remove** propriedades do tipo original.

```ts
type UsuarioSemSenha = Omit<Usuario, "senha">;
```

Esse padrão é ideal quando você quer **reaproveitar uma estrutura**, mas excluir informações sensíveis ou desnecessárias.

### `Record<K, T>`

Cria um tipo de **objeto indexado**, em que todas as chaves `K` mapeiam para o tipo `T`.

```ts
type Dias = "seg" | "ter" | "qua";

const agenda: Record<Dias, string> = {
  seg: "reunião",
  ter: "aula",
  qua: "livre"
};
```

Muito útil para tabelas de lookup, dicionários de mensagens, mapeamento de ícones ou rotas de navegação.

### Exemplo prático em React Native

Imagine um hook que recebe uma função de atualização de perfil parcial:

```ts
interface Perfil {
  nome: string;
  bio: string;
  avatar: string;
}

function atualizarPerfil(dados: Partial<Perfil>) {
  // envia apenas os campos alterados para a API
}
```

E em um componente de listagem, você pode esconder campos sensíveis com `Omit`:

```ts
type UsuarioSemAvatar = Omit<Perfil, "avatar">;
```

Ou gerar uma estrutura de estado com `Readonly` para garantir que os dados não sejam reatribuídos acidentalmente.

### Conclusões

Os **utility types** do TypeScript economizam tempo e evitam duplicação, permitindo criar **tipos derivados com segurança**. Eles se tornam ainda mais poderosos quando combinados com generics e interfaces — e são ferramentas indispensáveis para aplicações profissionais com React Native.

Combinados, esses recursos nos permitem modelar dados com precisão, adaptar estruturas conforme o contexto e manter a consistência do sistema mesmo à medida que ele cresce.

---

## 3. Resolução dos Exercícios da Aula 01

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

---

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

---

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

---

### ✅ 4. Conclusão

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

---

## 5. Exercícios Propostos (TypeScript)

Os exercícios a seguir visam solidificar os principais conceitos de TypeScript abordados nesta aula: **tipagem explícita**, **interfaces**, **tipos literais**, **uniões**, **generics** e **utility types**. Vamos reforçar o que vimos pois utilizaremos posteriormente.

Cada exercício pode ser resolvido em um arquivo `.ts` próprio, e verificado com `npx tsc --noEmit` para validação sem geração de arquivos `.js`.

Para entrega, envie um .ZIP ou link do repositório no Moodle.

### 🔧 Exercício 1 – Tipos, interfaces e métodos de array

**Objetivo:** Tipar corretamente dados e funções que operam sobre listas.

1. Crie uma interface `Livro` com os campos:

   * `titulo` (string)
   * `autor` (string)
   * `ano` (number)
   * `disponivel` (boolean)

2. Crie um array `biblioteca: Livro[]` com ao menos 3 livros.

3. Implemente a função `listarTitulosDisponiveis(livros: Livro[]): string[]`
   que retorne apenas os títulos dos livros com `disponivel = true`.

4. Use `filter` e `map` com tipagem apropriada.

### 🔧 Exercício 2 – Tipos literais e união de tipos

**Objetivo:** Representar múltiplos formatos possíveis para um mesmo tipo de dado.

1. Crie dois tipos:

```ts
type Sucesso = { tipo: "sucesso"; dados: string[] };
type Erro = { tipo: "erro"; mensagem: string };
type Resultado = Sucesso | Erro;
```

2. Crie a função `exibirResultado(r: Resultado): void` que:

* Mostra os dados se `r.tipo === "sucesso"`
* Mostra a mensagem de erro se `r.tipo === "erro"`

3. Use `if` com refinamento de tipo (type narrowing).

### 🔧 Exercício 3 – Utility Types (`Omit`, `Partial`, `Readonly`)

**Objetivo:** Criar variações seguras de tipos com base em estruturas existentes.

1. Crie uma interface `Usuario` com os campos:

   * `id: number`
   * `nome: string`
   * `email: string`
   * `senha: string`

2. Crie dois novos tipos:

```ts
type UsuarioSemSenha = Omit<Usuario, "senha">;
type UsuarioAtualizacao = Partial<Usuario>;
```

3. Implemente duas funções:

```ts
function exibirPerfil(u: UsuarioSemSenha): void;
function atualizarUsuario(id: number, dados: UsuarioAtualizacao): void;
```

Use `console.log()` para simular os efeitos.

### 🔧 Exercício 4 – Funções genéricas

**Objetivo:** Criar funções reutilizáveis fortemente tipadas.

1. Implemente a função `obterPrimeiro<T>(lista: T[]): T` que retorna o primeiro item da lista.

2. Use a função com uma lista de `string[]`, depois com `number[]`, e com um tipo personalizado:

```ts
interface Produto {
  nome: string;
  preco: number;
}
```

3. Demonstre o uso correto da inferência de tipos.

### 🔧 Exercício 5 – Tipagem em componentes e props

**Objetivo:** Simular props de componentes com TypeScript.

1. Crie uma interface `PropsBotao` com:

   * `titulo: string`
   * `ativo?: boolean`

2. Implemente a função:

```ts
function renderizarBotao({ titulo, ativo = true }: PropsBotao): string {
  return ativo ? `[ ${titulo} ]` : `( ${titulo} )`;
}
```

3. Teste com diferentes valores.

### ✅ Dica: validação com o TypeScript

Use o comando abaixo para validar os exercícios sem gerar arquivos `.js`:

```bash
npx tsc --noEmit
```
