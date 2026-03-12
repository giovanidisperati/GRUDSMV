---
layout: aula
title: "4. Aprofundando em TypeScript"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 4
---

## 4. Objetos e Interfaces

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

## 4.1 Enums, Tipos Literais e Discriminação de Tipos

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

## 4.2 Generics: Reutilização com Segurança

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

## 4.3 Utilitários de Tipo (Utility Types)

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


