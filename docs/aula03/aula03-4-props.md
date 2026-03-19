---
layout: aula
title: "4. Entendendo as Props"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 4
---

## **4. Props: passando dados entre componentes**

Se os **componentes** são as peças reutilizáveis da interface, as **props** (propriedades) são os **dados que os tornam dinâmicos** e personalizáveis.

### 🎁 **O que são props?**

**Props** são argumentos que você passa para os componentes — exatamente como parâmetros de uma função. Elas permitem que o mesmo componente se comporte de formas diferentes dependendo dos valores recebidos.

```tsx
function Saudacao(props: { nome: string }) {
  return <p>Olá, {props.nome}!</p>;
}
```

Ao usar o componente:

```tsx
<Saudacao nome="Camila" />
<Saudacao nome="Pedro" />
```

Teremos o resultado: 
```
Olá, Camila!
Olá, Pedro!
```

### 🛠️ **4.1 Refatorando o `PokemonCard` com props**

Antes, nosso `PokemonCard` exibia sempre o mesmo Pokémon. Agora vamos torná-lo reutilizável, permitindo exibir qualquer Pokémon:

#### `src/components/PokemonCard.tsx`

```tsx
import React from "react";

type Props = {
  nome: string;
  tipo: string;
};

export function PokemonCard({ nome, tipo }: Props) {
  return (
    <div className="pokemon-card" style={{ 
      marginBottom: "16px", 
      padding: "12px", 
      border: "1px solid #ddd", 
      borderRadius: "8px",
      backgroundColor: "#f8f8f8"
    }}>
      <h3>{nome}</h3>
      <p>Tipo: {tipo}</p>
    </div>
  );
}
```

Esse código faz duas coisas importantes:

1. **Define um tipo chamado `Props`**, que representa o formato esperado das propriedades recebidas pelo componente `PokemonCard`. Nesse caso, ele exige duas `props`:

   * `nome`: do tipo `string`
   * `tipo`: também do tipo `string`

2. Na declaração do componente, usamos a **desestruturação** de `props` e **informamos o tipo esperado** com `: Props`. Ou seja:

```tsx
{ nome, tipo }: Props
```

significa que:

* O componente está recebendo um objeto com propriedades `nome` e `tipo`
* E esse objeto deve seguir o formato definido no tipo `Props`.

💡 Isso é importante por três motivos:

* Garante **verificação de tipo automática** durante o desenvolvimento (menos erros!).
* Facilita o **autocompletar** em editores como VS Code.
* Torna o código **mais legível e documentado**, pois deixa explícito o que o componente espera receber.

"Ah, mas eu preciso declarar esse `type Props`?". Assim, precisar PRECISAR você não precisa, você pode fazer da **Forma inline**, que funciona, mas é menos organizada:

```tsx
export function PokemonCard({ nome, tipo }: { nome: string; tipo: string }) {
  // ...
}
```

A desvantagem é que fica mais verboso e difícil de ler, especialmente com mais props Além disso, não dá para reutilizar o tipo facilmente e essa forma também não separa responsabilidades (temos o tipo misturado com lógica do componente 😐).

Ou seja, o `type Props` não é tecnicamente obrigatório, mas é **boa prática** porque:

* Separa responsabilidades.
* Melhora a legibilidade.
* Facilita a manutenção do código.

Beleza, entendido isso, como fica nosso `App.tsx`?

#### No `App.tsx`:

```tsx
import React from "react";
import { PokemonCard } from "./components/PokemonCard";

export default function App() {
  return (
    <div className="container" style={{ 
      maxWidth: "800px", 
      margin: "0 auto", 
      padding: "20px" 
    }}>
      <h1>Minha Pokedex</h1>
      <div className="pokemon-list">
        <PokemonCard nome="Pikachu" tipo="Elétrico" />
        <PokemonCard nome="Bulbasaur" tipo="Grama/Veneno" />
        <PokemonCard nome="Charmander" tipo="Fogo" />
      </div>
    </div>
  );
}
```

Observe como o mesmo componente `PokemonCard` é reutilizado três vezes, cada um exibindo informações diferentes.

### 📦 **4.2 Importante: Props são somente leitura**

Um componente **não pode modificar suas props diretamente**. Elas são imutáveis — isso garante previsibilidade no comportamento e respeita o modelo unidirecional de dados do React.

```tsx
// ❌ Incorreto - Tentando modificar props
function Contador(props: { valor: number }) {
  props.valor = props.valor + 1; // Erro! Props são somente leitura
  return <p>Contagem: {props.valor}</p>;
}

// ✅ Correto - Usando props como somente leitura
function Contador(props: { valor: number }) {
  return <p>Contagem: {props.valor}</p>;
}
```

Essa característica é fundamental para o funcionamento do React e será complementada pelo conceito de estado, que veremos na próxima seção. 😉

### 🧩 **4.3 Desestruturação de props**

Como vimos no exemplo do `PokemonCard`, uma prática comum em React é desestruturar as props para facilitar o acesso e tornar o código mais limpo:

```tsx
// Sem desestruturação
function Perfil(props: { nome: string; idade: number }) {
  return (
    <div>
      <h2>{props.nome}</h2>
      <p>Idade: {props.idade} anos</p>
    </div>
  );
}

// Com desestruturação (mais limpo)
function Perfil({ nome, idade }: { nome: string; idade: number }) {
  return (
    <div>
      <h2>{nome}</h2>
      <p>Idade: {idade} anos</p>
    </div>
  );
}
```

Lembre-se: vimos a sintaxe de desestruturação do JavaScript na Aula 02. 😊

### 👶 **4.4 Props padrão (default props)**

Você pode definir valores padrão para props que não são obrigatórias:

```tsx
type BotaoProps = {
  texto: string;
  cor?: string; // O '?' indica que é opcional
};

function Botao({ texto, cor = "blue" }: BotaoProps) {
  return (
    <button style={{ 
      backgroundColor: cor, 
      color: "white", 
      padding: "8px 16px", 
      border: "none", 
      borderRadius: "4px" 
    }}>
      {texto}
    </button>
  );
}

// Uso:
// <Botao texto="Clique aqui" /> → Botão azul (valor padrão)
// <Botao texto="Enviar" cor="green" /> → Botão verde
```

Isso permite criar componentes flexíveis que funcionam bem mesmo quando o desenvolvedor não fornece todos os valores.

### 👨‍👩‍👧‍👦 **4.5 A prop especial `children`**

React tem uma prop especial chamada `children` que permite compor componentes de forma mais flexível, criando "wrappers" ou "containers":

```tsx
type CardProps = {
  titulo: string;
  children: React.ReactNode;
};

function Card({ titulo, children }: CardProps) {
  return (
    <div style={{ 
      border: "1px solid #ddd", 
      borderRadius: "8px", 
      padding: "16px", 
      marginBottom: "16px",
      boxShadow: "0 2px 4px rgba(0,0,0,0.1)"
    }}>
      <h2>{titulo}</h2>
      <div>{children}</div> 
    </div>
  );
}

// Uso:
function App() {
  return (
    <div>
      <Card titulo="Bem-vindo!">
        <p>Este é um exemplo de conteúdo filho.</p>
        <button>Clique aqui</button>
      </Card>
      
      <Card titulo="Informações">
        <p>Você pode colocar qualquer conteúdo aqui.</p>
        <ul>
          <li>Item 1</li>
          <li>Item 2</li>
        </ul>
      </Card>
    </div>
  );
}
```

O que ocorre aqui é que quando usamos um componente React como uma **tag com abertura e fechamento**, tudo que for colocado **entre essas tags** é automaticamente atribuído à prop especial `children`.

No caso de `titulo="Informações"` esse é um **atributo comum** passado como uma prop chamada `titulo`. Ele será atribuído ao parâmetro `titulo` da função `Card`:

```tsx
<h2>{titulo}</h2> // Vai renderizar: <h2>Informações</h2>
```

Já o **O conteúdo entre as tags `<Card>...</Card>`**, todo esse bloco:

```tsx
<p>Você pode colocar qualquer conteúdo aqui.</p>
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
</ul>
```

Será atribuído à prop **`children`**! Ou seja, internamente ele será tratado como um **elemento JSX** (ou múltiplos elementos agrupados) e passado como:

```tsx
<div>
  <p>Você pode colocar qualquer conteúdo aqui.</p>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```

Esse conteúdo será então exibido na linha:

```tsx
<div>{children}</div> // Vai renderizar o parágrafo e a lista, que foram atribuídos à prop Children!
```

Isso que dizer que o componente `Card` acabará sendo renderizado como algo assim no DOM final:

```html
<div class="card">
  <h2>Informações</h2>
  <div>
    <p>Você pode colocar qualquer conteúdo aqui.</p>
    <ul>
      <li>Item 1</li>
      <li>Item 2</li>
    </ul>
  </div>
</div>
```

Em suma:

* `titulo="Informações"` é uma **prop explícita**, como qualquer outra.
* O conteúdo **dentro das tags** é atribuído automaticamente à prop **`children`**.
* Essa é a base da **composição de interfaces no React**, permitindo criar componentes flexíveis que encapsulam tanto estrutura quanto conteúdo. 🤓

Portanto, a prop `children` permite criar componentes de layout reutilizáveis que podem envolver qualquer conteúdo.

### 🧠 **4.6 E por que props são importantes?**

Como vimos, são importantes pois:

* Permitem **reutilização de componentes com dados diferentes**
* Estimulam o design de componentes **puros e previsíveis**
* Facilitam o uso de **componentes compostos**, que recebem filhos ou configurações por props
* Estão na base de qualquer **interface dinâmica** com React
* Promovem a **separação de responsabilidades** entre componentes


### 🔄 **4.7 Passando props entre componentes**

Além de tudo que vimos até aqui, as props podem ser passadas de um componente pai para um componente filho, criando uma hierarquia de dados. Considere o código a seguir:

```tsx
function Aplicativo() {
  const usuario = {
    nome: "Ana",
    cargo: "Desenvolvedora"
  };
  
  return (
    <div>
      <h1>Dashboard</h1>
      <Cabecalho 
        nomeUsuario={usuario.nome} 
        cargo={usuario.cargo} 
      />
    </div>
  );
}

function Cabecalho({ nomeUsuario, cargo }: { nomeUsuario: string; cargo: string }) {
  return (
    <header style={{ 
      backgroundColor: "#f0f0f0", 
      padding: "10px", 
      marginBottom: "20px" 
    }}>
      <h2>Olá, {nomeUsuario}</h2>
      <p>Cargo: {cargo}</p>
    </header>
  );
}
```

Aqui, a função `Aplicativo` representa o componente principal da aplicação. Dentro dela, é criada uma constante `usuario`, que contém um **objeto com dois dados**: `nome` e `cargo`.

Em seguida, dentro do `return`, a estrutura JSX define a interface que será renderizada: um título (`<h1>Dashboard</h1>`) e a **inserção de um componente filho**, chamado `Cabecalho`.

No momento em que o componente `Cabecalho` é chamado, são passadas duas **props**:

```tsx
<Cabecalho 
  nomeUsuario={usuario.nome} 
  cargo={usuario.cargo} 
/>
```

Aqui, o valor `"Ana"` é passado para a prop `nomeUsuario`, e `"Desenvolvedora"` é passado para a prop `cargo`.

O **resumo visual do fluxo** é

```
Componente Aplicativo (pai)
  ├── Cria objeto "usuario"
  └── Chama <Cabecalho nomeUsuario="Ana" cargo="Desenvolvedora" />

Componente Cabecalho (filho)
  └── Recebe props { nomeUsuario, cargo }
        ↓
     Renderiza <h2> e <p> com os dados recebidos
```

Essa abordagem permite que os dados fluam de cima para baixo na árvore de componentes. 😊

### ✅ **4.8 Conclusão!**

Props são fundamentais para criar **componentes reutilizáveis e personalizáveis**. Em React, quase tudo gira em torno da **composição de componentes com dados variados**.

Portanto, ao dominar o conceito de props, você consegue:
- Criar componentes genéricos que podem ser reutilizados em diferentes contextos
- Passar dados de um componente pai para seus filhos
- Construir interfaces modulares e bem organizadas
- Implementar padrões de design que facilitam a manutenção do código

Na próxima seção, exploraremos um tema central na construção de aplicativos interativos: o **gerenciamento de estado local com `useState`**.