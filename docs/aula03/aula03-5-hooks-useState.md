---
layout: aula
title: "5. O hook useState"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 5
---

## **5. useState: armazenando e atualizando estados locais**

Ao desenvolver aplicações interativas, uma necessidade logo se torna evidente: **armazenar e atualizar valores que mudam ao longo do tempo** — como inputs do usuário, itens de uma lista ou contadores. Para conseguir gerenciar o estado das nossas aplicações, precisamos usar o **hook `useState`**.

Um **Hook** é uma função especial que permite que componentes funcionais acessem recursos internos do React, como o controle de estado, efeitos colaterais, contexto e muito mais. Antes da introdução dos Hooks, esses recursos só estavam disponíveis em componentes baseados em classes, o que tornava o código mais verboso e menos flexível. Com os Hooks, tornou-se possível escrever componentes mais simples, organizados como funções, mas ainda assim capazes de lidar com comportamentos complexos e dinâmicos.

O termo "hook" vem da ideia de que essas funções se conectam (“hook into”) aos mecanismos internos do React. Por exemplo, ao utilizar o hook `useState`, o componente passa a “se conectar” ao sistema de gerenciamento de estado da biblioteca. Isso permite que ele armazene e atualize valores que podem mudar ao longo do tempo — como entradas do usuário, resultados de cálculos ou dados recebidos de uma API — de forma reativa, ou seja, mantendo a interface sincronizada automaticamente com os dados. 

Portanto, vamos agora "dissecar" o **`useState`**, um dos recursos mais fundamentais para criar interfaces dinâmicas.

### 🧠 **5.1 O que é "estado" em uma aplicação React?**

O **estado (state)** é qualquer informação que **pode mudar** enquanto a aplicação está em execução, e que precisa provocar uma **atualização visual** quando isso acontece.

Por exemplo:

* O texto digitado em um campo de formulário
* Uma lista de tarefas exibida na tela
* A resposta de uma API
* O tema claro/escuro selecionado pelo usuário
* O botão clicado mais recentemente

Sem estado, nossas interfaces seriam estáticas e não responderiam às ações do usuário. O estado é o que torna nossas aplicações **interativas e dinâmicas**.

### 🔧 **5.2 Como usar o `useState`**

O hook `useState` permite criar uma **variável de estado** associada a um componente. Ele retorna um par de valores: o **estado atual** e uma **função para atualizá-lo**.

#### Exemplo: contador simples

```tsx
import React, { useState } from "react";

export default function Contador() {
  // Declaramos uma variável de estado chamada "contador"
  const [contador, setContador] = useState(0);

  return (
    <div style={{ 
      padding: "20px", 
      maxWidth: "300px", 
      margin: "0 auto", 
      textAlign: "center",
      border: "1px solid #ddd",
      borderRadius: "8px"
    }}>
      <h2>Exemplo de Estado</h2>
      <p style={{ fontSize: "24px" }}>Contador: {contador}</p>
      <button 
        onClick={() => setContador(contador + 1)}
        style={{
          padding: "8px 16px",
          backgroundColor: "#4CAF50",
          color: "white",
          border: "none",
          borderRadius: "4px",
          cursor: "pointer"
        }}
      >
        Incrementar
      </button>
    </div>
  );
}
```

Neste exemplo, cada vez que o botão é clicado, o valor do contador aumenta e a interface é atualizada automaticamente.

No trecho `useState(0)`, o número `0` é o **valor inicial** do estado `contador`. Isso significa que, quando o componente `Contador` for renderizado pela primeira vez, o valor de `contador` começará em zero.

Esse valor inicial só é utilizado na **primeira renderização**. Depois disso, o valor de `contador` será atualizado dinamicamente com base nas interações do usuário — como quando clicamos no botão e executamos `setContador(contador + 1)` para incrementar.

Se **não usássemos o `useState`**, o valor exibido na tela **não seria atualizado automaticamente** quando o botão fosse clicado. Em React, a única forma de fazer com que a interface *reaja a mudanças de valor e se re-renderize* é por meio de **estado** (`useState`) ou outros **hooks de ciclo de vida**. Variáveis comuns (como `let contador = 0`) **não fazem o React re-renderizar o componente** quando mudam.

Por exemplo, se fizéssemos algo assim:

```tsx
let contador = 0;

function incrementar() {
  contador++;
  console.log(contador); // O valor mudaria, mas a interface não atualiza!
}
```

O valor de `contador` até aumentaria no console, mas **a interface na tela continuaria exibindo o valor antigo**, porque o React não sabe que algo mudou. O `useState` é quem **informa ao React que o componente precisa ser re-renderizado**. Ao chamar `setContador(...)`, o React atualiza o valor e redesenha o componente com base no novo estado. Por isso, ele é essencial para criar **interfaces reativas e dinâmicas**.

**"Éééééé... entendi não, desculpa aí prof 😑"**. Calma! Vamos por parte.

### ✍️ **5.3 Entendendo a parte mais importante**

Vamos analisar a parte mais importante do código:

```tsx
const [contador, setContador] = useState(0);
```

* `contador`: o **valor atual** do estado (inicialmente `0`)
* `setContador`: a **função que atualiza** esse estado
* `useState(0)`: define o **valor inicial** do estado como `0`

Esta sintaxe utiliza a **desestruturação de arrays** do JavaScript, que permite extrair valores de um array para variáveis separadas.

A cada vez que `setContador` é chamada, o componente **re-renderiza** com o novo valor. Este é o ciclo de vida básico do React:

1. Estado muda → 2. Componente re-renderiza → 3. UI atualiza

É basicamente isso!

### 🧼 **5.4 Imutabilidade importa**

Como mencionamos acima ao falar sobre componentes, um conceito fundamental no React é que você **nunca deve alterar o estado diretamente**:

```tsx
// ❌ ERRADO: Modificando o estado diretamente
contador++;           
contador = contador + 1; 
```

Use sempre a função de atualização fornecida pelo `useState`:

```tsx
// ✅ CORRETO: Usando a função de atualização
setContador(contador + 1); 
```

Isso garante que o React:
1. Saiba que houve uma mudança
2. Agende uma re-renderização do componente
3. Atualize corretamente a interface

A imutabilidade é um princípio central no React e ajuda a evitar bugs difíceis de rastrear.

### 🧪 **5.5 Estados com diferentes tipos de dados**

Um outro ponto importante é que `useState` pode trabalhar com qualquer tipo de dado do JavaScript. Por exemplo:

#### String:

```tsx
const [nome, setNome] = useState("Ash");

// Em um formulário:
<input 
  type="text"
  value={nome}
  onChange={(e) => setNome(e.target.value)}
  style={{ padding: "8px", marginRight: "8px" }}
/>
<p>Nome: {nome}</p>
```

Neste exemplo, `useState("Ash")` inicializa o estado com uma string: `"Ash"`. A variável `nome` representa o valor atual, e `setNome` é usada para atualizá-lo.

O campo `<input>` está "controlado" pelo React — ou seja, seu valor depende do estado `nome`. Sempre que o usuário digita algo, o evento `onChange` é disparado e a função `setNome` atualiza o estado com o novo valor vindo de `e.target.value`. Assim, à medida que o usuário digita, o parágrafo `<p>` abaixo reflete automaticamente o novo nome. Esse é o padrão para trabalhar com **inputs controlados** em React.

#### Array:

```tsx
const [pokemons, setPokemons] = useState<string[]>([]);

// Adicionando um item:
<div>
  <button onClick={() => setPokemons([...pokemons, "Pikachu"])}>
    Adicionar Pikachu
  </button>
  <ul>
    {pokemons.map((pokemon, index) => (
      <li key={index}>{pokemon}</li>
    ))}
  </ul>
</div>
```

Neste caso, o estado `pokemons` é um **array de strings**, iniciado como vazio (`[]`). O botão "Adicionar Pikachu" atualiza o array chamando `setPokemons([...pokemons, "Pikachu"])`, o que cria uma **nova cópia do array atual com "Pikachu" adicionado ao final**.

Abaixo do botão, usamos `.map()` para percorrer e exibir cada Pokémon da lista em um item `<li>`. O uso de `key={index}` garante uma chave única para cada item, ajudando o React a renderizar de forma eficiente.

Este padrão é típico quando lidamos com **listas dinâmicas**.

#### Objeto:

```tsx
const [pokemon, setPokemon] = useState({ nome: "", tipo: "" });

// Atualizando o objeto:
<div>
  <button 
    onClick={() => setPokemon({ nome: "Bulbasaur", tipo: "Grama/Veneno" })}
  >
    Selecionar Bulbasaur
  </button>
  {pokemon.nome && (
    <div>
      <p>Nome: {pokemon.nome}</p>
      <p>Tipo: {pokemon.tipo}</p>
    </div>
  )}
</div>
```

Aqui, usamos `useState` para armazenar um **objeto com duas propriedades**: `nome` e `tipo`. Inicialmente, ambas são strings vazias.

Ao clicar no botão, o objeto é atualizado com os dados de um Pokémon específico (`Bulbasaur`). Em seguida, o componente renderiza essas informações, **mas somente se o nome do Pokémon estiver preenchido**, graças à verificação `pokemon.nome && (...)`. Esse padrão é comum quando armazenamos **entidades com múltiplos atributos**, como usuários, produtos ou pokémons.

### 💡 **5.6 Atualizando arrays ou objetos**

É importante reforçar que `useState` **substitui completamente o valor antigo** pelo novo. Isso significa que, ao trabalhar com objetos e arrays, você precisa preservar os dados existentes que deseja manter. É por isso que codificamos os exemplos acima como fizemos. Vamos ver isso ponto a ponto.

#### Adicionando um item a um array:

```tsx
// ❌ ERRADO: Isso modifica o array original
const [itens, setItens] = useState<string[]>([]);
itens.push("Novo item"); // Mutação direta!

// ✅ CORRETO: Cria um novo array com todos os itens anteriores + o novo
setItens([...itens, "Novo item"]);

// Alternativa usando o callback form:
setItens(prev => [...prev, "Novo item"]);
```

#### Atualizando uma propriedade de um objeto:

```tsx
const [usuario, setUsuario] = useState({ nome: "Ana", idade: 25 });

// ❌ ERRADO: Modifica apenas uma propriedade, perdendo as outras
setUsuario({ nome: "Carlos" }); // Perdemos a propriedade 'idade'!

// ✅ CORRETO: Cria um novo objeto preservando propriedades existentes
setUsuario({ ...usuario, nome: "Carlos" });

// Alternativa usando o callback form:
setUsuario(prev => ({ ...prev, nome: "Carlos" }));
```

O operador spread (`...`) é essencial para trabalhar com estados imutáveis no React.

### 🔄 **5.7 Forma de callback para atualizações**

Quando a atualização do estado depende do valor anterior, é mais seguro usar a **forma de callback**:

```tsx
// Pode causar problemas em certas situações
setContador(contador + 1);

// Mais seguro, sempre usa o valor mais recente
setContador(valorAnterior => valorAnterior + 1);
```

Isso é especialmente importante quando várias atualizações acontecem em sequência:

```tsx
function incrementarTresVezes() {
  // ❌ PROBLEMA: Todas as chamadas usam o mesmo valor inicial
  setContador(contador + 1); // Se contador = 0, define para 1
  setContador(contador + 1); // Se contador = 0, define para 1 novamente!
  setContador(contador + 1); // Se contador = 0, define para 1 pela terceira vez!
  
  // Resultado: contador = 1 (não 3!)
}

function incrementarTresVezesCorretamente() {
  // ✅ CORRETO: Cada chamada usa o resultado da anterior
  setContador(prev => prev + 1); // 0 → 1
  setContador(prev => prev + 1); // 1 → 2
  setContador(prev => prev + 1); // 2 → 3
  
  // Resultado: contador = 3
}
```

### 🧩 **5.8 Aplicação prática: favoritar Pokémon**

Vamos criar um componente que permite favoritar/desfavoritar um Pokémon:

```tsx
import React, { useState } from "react";

function PokemonCard({ nome, tipo }: { nome: string; tipo: string }) {
  const [favorito, setFavorito] = useState(false);

  return (
    <div style={{ 
      border: "1px solid #ddd", 
      borderRadius: "8px", 
      padding: "16px",
      margin: "8px 0",
      backgroundColor: favorito ? "#fff9c4" : "white"
    }}>
      <h3>{nome} {favorito && "⭐"}</h3>
      <p>Tipo: {tipo}</p>
      <button
        onClick={() => setFavorito(!favorito)}
        style={{
          backgroundColor: favorito ? "#f44336" : "#4CAF50",
          color: "white",
          border: "none",
          padding: "8px 16px",
          borderRadius: "4px",
          cursor: "pointer"
        }}
      >
        {favorito ? "Remover dos favoritos" : "Adicionar aos favoritos"}
      </button>
    </div>
  );
}

export default function App() {
  return (
    <div style={{ maxWidth: "500px", margin: "0 auto", padding: "20px" }}>
      <h1>Minha Pokedex</h1>
      <PokemonCard nome="Pikachu" tipo="Elétrico" />
      <PokemonCard nome="Bulbasaur" tipo="Grama/Veneno" />
      <PokemonCard nome="Charmander" tipo="Fogo" />
    </div>
  );
}
```

Neste exemplo:
1. Cada card tem seu próprio estado `favorito` independente
2. Clicar no botão alterna o valor entre `true` e `false`
3. A aparência do card muda com base no estado (cor de fundo e texto do botão)

Esse padrão de alternância (`true` ↔ `false`) é muito comum em interfaces interativas.

> 💡 **Dica:** você pode testar o código acima rapidamente no navegador usando o [PlayCode.io](https://playcode.io/react), uma IDE online que suporta React com hot reload.
> Basta copiar e colar os códigos dessa seção em `App.jsx` e depois importá-lo no `index.jsx`, garantindo que a importação do `App` no `index.jsx` seja feita corretamente:
>
> ```js
> import App from './App.jsx';
> ```
>
> Isso ajuda a visualizar o funcionamento desse componente React sem precisar configurar nada localmente.
>Ah! Não se esqueça que aqui estamos usando TypeScript e o PlayCode, por padrão, usa JavaScript. Basta remover a tipagem do cabeçalho do componente PokemonCard, deixando como mostrado abaixo, que o exemplo vai funcionar direitinho na IDE online 🤩
>
> ```js
> function PokemonCard({ nome, tipo }) {
> ```

Entendido isso, passemos à próxima seção!

### 📝 **5.9 Controlando formulários com useState**

Um uso muito comum do `useState` é controlar campos de formulário:

```tsx
import React, { useState } from "react";

function FormularioPokemon() {
  const [nome, setNome] = useState("");
  const [tipo, setTipo] = useState("");
  const [enviado, setEnviado] = useState(false);
  
  function handleSubmit(e: React.FormEvent) {
    e.preventDefault();
    setEnviado(true);
    // Aqui você poderia enviar os dados para um servidor
  }
  
  return (
    <div style={{ maxWidth: "400px", margin: "0 auto" }}>
      <h2>Cadastrar Novo Pokémon</h2>
      
      {enviado ? (
        <div style={{ 
          padding: "16px", 
          backgroundColor: "#dff0d8", 
          borderRadius: "4px" 
        }}>
          <p>Pokémon cadastrado com sucesso!</p>
          <p><strong>Nome:</strong> {nome}</p>
          <p><strong>Tipo:</strong> {tipo}</p>
          <button onClick={() => {
            setNome("");
            setTipo("");
            setEnviado(false);
          }}>Cadastrar outro</button>
        </div>
      ) : (
        <form onSubmit={handleSubmit} style={{ display: "flex", flexDirection: "column" }}>
          <div style={{ marginBottom: "16px" }}>
            <label htmlFor="nome" style={{ display: "block", marginBottom: "8px" }}>
              Nome:
            </label>
            <input
              id="nome"
              type="text"
              value={nome}
              onChange={(e) => setNome(e.target.value)}
              required
              style={{ width: "100%", padding: "8px" }}
            />
          </div>
          
          <div style={{ marginBottom: "16px" }}>
            <label htmlFor="tipo" style={{ display: "block", marginBottom: "8px" }}>
              Tipo:
            </label>
            <input
              id="tipo"
              type="text"
              value={tipo}
              onChange={(e) => setTipo(e.target.value)}
              required
              style={{ width: "100%", padding: "8px" }}
            />
          </div>
          
          <button 
            type="submit"
            style={{
              padding: "10px 16px",
              backgroundColor: "#007bff",
              color: "white",
              border: "none",
              borderRadius: "4px",
              cursor: "pointer"
            }}
          >
            Cadastrar
          </button>
        </form>
      )}
    </div>
  );
}
```

No exemplo acima, os campos do formulário estão sendo **controlados com `useState`** porque o React está sendo usado para **manter o valor de cada campo sincronizado com o estado da aplicação**. Esse padrão é conhecido como **componente controlado**.

Logo no início do componente `FormularioPokemon`, são declaradas três variáveis de estado usando `useState`: `nome`, `tipo` e `enviado`. As duas primeiras armazenam os valores digitados pelo usuário nos campos do formulário, enquanto a terceira indica se o formulário já foi enviado.

Cada `<input>` possui um atributo `value`, que está vinculado diretamente a uma dessas variáveis de estado (`nome` ou `tipo`). Isso significa que o valor exibido dentro do campo não é gerenciado pelo navegador, e sim **controlado pelo React**. Sempre que o usuário digita algo, o evento `onChange` é disparado e executa a função `setNome` ou `setTipo`, atualizando o estado correspondente com o novo valor digitado. Essa atualização provoca uma nova renderização do componente com o valor atualizado — garantindo que a interface esteja sempre sincronizada com o estado interno. 🧑‍💻

Por fim, o botão "Cadastrar outro" redefine o estado de `nome`, `tipo` e `enviado`, permitindo que o formulário volte ao estado inicial. Isso é feito chamando as funções `setNome("")`, `setTipo("")` e `setEnviado(false)`, o que limpa os campos e reexibe o formulário, demonstrando novamente o poder de manter tudo controlado via estado.

Esse controle fino traz diversas vantagens. Por exemplo, o código pode mostrar mensagens em tempo real, limpar os campos após o envio, impedir o envio se os dados forem inválidos, e até mesmo aplicar formatações automáticas (como converter o texto para maiúsculas). Além disso, como os dados do formulário estão no estado da aplicação, eles podem ser facilmente reutilizados ou enviados para um servidor no momento do envio, como indicado na função `handleSubmit`.

Ou seja, este padrão, conhecido como **componentes controlados**, permite:
1. Acessar os valores dos campos a qualquer momento
2. Validar entradas em tempo real
3. Formatar dados automaticamente
4. Implementar lógica condicional baseada nos valores

### ✅ **5.10 Conclusão da Seção**

O `useState` é o **hook mais básico e essencial do React**. Ele permite que seus componentes **mantenham memória** ao longo do tempo e **respondam a interações do usuário**.

Com ele, é possível:

* Criar componentes interativos e dinâmicos
* Controlar campos de formulário
* Gerenciar dados locais sem necessidade de bibliotecas externas
* Implementar interfaces que respondem às ações do usuário

Pontos importantes a lembrar:
1. Nunca modifique o estado diretamente, sempre use a função de atualização
2. Ao atualizar objetos e arrays, crie novas cópias usando o operador spread (`...`)
3. Use a forma de callback quando a atualização depender do valor anterior
4. Cada componente pode ter múltiplos estados independentes

Na próxima seção, veremos o próximo passo natural: como **executar efeitos colaterais** — como chamadas de API — com o `useEffect`.
