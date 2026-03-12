---
layout: aula
title: "6. Hora de praticar!"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 6
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
