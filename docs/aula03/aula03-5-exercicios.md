---
layout: aula
title: "5. Hooks - useState e useEffect"
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

---

## **6. useEffect: efeitos colaterais e ciclo de vida dos componentes**

Até agora, aprendemos a criar componentes e gerenciar seu estado interno com `useState`. Mas e quando precisamos **interagir com o mundo exterior** ao nosso componente? É aí que entra o **hook `useEffect`**.

### 🔄 **6.1 O que são "efeitos colaterais" no React?**

Em programação funcional (princípio por trás do React), os componentes devem ser funções **puras**: recebem props, usam estado e retornam elementos. Vimos um pouquinho disso na aula anterior, lembra? Porém, aplicações reais, em muitos casos, fogem desse fluxo esperado.

Nesse caso, podemos considerar que **efeitos colaterais** são ações que acontecem **fora do fluxo normal de renderização**, como:

* Buscar dados de uma API
* Modificar o título da página
* Configurar assinaturas de eventos
* Interagir com o localStorage
* Iniciar e limpar temporizadores

O hook `useEffect` nos permite executar esses efeitos de forma **controlada e previsível**.

### 🧪 **6.2 Sintaxe básica do useEffect**

O `useEffect` é uma função especial do React que permite rodar um trecho de código quando algo acontece no ciclo de vida do componente — como quando ele aparece na tela ou quando alguma informação muda. Ele recebe dois parâmetros:

1. Uma função com o código que queremos executar

2. Um array com as variáveis que, ao mudarem, disparam o efeito

```tsx
import React, { useEffect } from "react";

function MeuComponente() {
  useEffect(() => {
    console.log("Olá! Estou na tela!");

    return () => {
      console.log("Tchau! Estou saindo da tela.");
    };
  }, []);

  return <div>Olá mundo</div>;
}
```

* A primeira parte `console.log("O componente apareceu na tela!")` vai rodar **assim que o componente aparece**.
* A segunda parte (a que começa com `return`) vai rodar **quando o componente sair da tela**.
* Os `[]` no final significam: **"só execute isso uma vez"**, quando o componente aparece.

Ou seja, neste exemplo, o efeito será executado **apenas uma vez** quando o componente for montado, e a função de limpeza será chamada quando o componente for removido da tela. Em suma:

* `useEffect` é usado para **fazer algo quando o componente aparece ou muda**.
* O `[]` indica que o efeito só deve acontecer uma vez. Se colocássemos variáveis ali dentro, o efeito rodaria sempre que elas mudassem.


### 🕒 **6.3 Controlando quando o efeito é executado**

O `useEffect` é como uma **função que fica esperando o momento certo para agir**. Mas... **quando é esse momento?**

A resposta está em um detalhe importante: o **array de dependências** (`[]`) que colocamos logo depois da função!

### 📌 Existem três comportamentos diferentes, dependendo desse array:

#### ✅ 1. Quando o array está vazio (`[]`):

```tsx
useEffect(() => {
  console.log("O componente foi exibido na tela!");
}, []);
```

* Esse efeito **só roda uma vez**, assim que o componente **aparece pela primeira vez**.
* É como dizer: “Faça isso **só no começo**.”
* Muito usado para **carregar dados**, **iniciar timers**, ou **conectar APIs**.

#### 🔁 2. Quando você coloca uma variável dentro do array (`[contador]`):

```tsx
useEffect(() => {
  console.log(`O contador mudou para: ${contador}`);
}, [contador]);
```

* O efeito vai rodar **no começo** e depois **toda vez que `contador` mudar**.
* É como dizer: “Faça isso **sempre que essa informação mudar**.”
* Ideal para **reagir a atualizações** de dados específicos, como uma busca que depende de um filtro.

#### ⚠️ 3. Quando você **não coloca o array**:

```tsx
useEffect(() => {
  console.log("O componente foi renderizado!");
});
```

* O efeito vai rodar **toda vez que o componente for atualizado**, até por pequenos motivos.
* Isso pode causar **efeitos repetidos e desnecessários** se você não tiver cuidado.
* Só use esse formato quando **você quiser que o efeito aconteça o tempo todo**.

### 📈 Por que isso importa?

Escolher corretamente **quais variáveis vão no array de dependências** evita:

* **Rodar efeitos sem necessidade**
* **Repetir chamadas de API**
* **Deixar a aplicação lenta**
* Ou pior: **entrar em loops infinitos de renderização**

Entendido? Entendido, então vamos continuar! 🤠

### 📦 **6.4 Buscando dados de APIs**

Um dos usos mais comuns do `useEffect` é buscar dados de APIs quando um componente é montado:

```tsx
import React, { useState, useEffect } from "react";

function PokemonInfo() {
  const [pokemon, setPokemon] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Indicamos que estamos carregando
    setLoading(true);
    
    // Fazemos a requisição à API
    fetch("https://pokeapi.co/api/v2/pokemon/pikachu")
      .then(response => {
        // Verificamos se a resposta está ok
        if (!response.ok) {
          throw new Error("Falha ao buscar dados do Pokémon");
        }
        return response.json();
      })
      .then(data => {
        // Armazenamos os dados no estado
        setPokemon(data);
        setLoading(false);
      })
      .catch(err => {
        // Tratamos possíveis erros
        setError(err.message);
        setLoading(false);
      });
  }, []); // Array vazio = executa apenas uma vez na montagem

  // Renderização condicional baseada no estado
  return (
    <div className="pokemon-card" style={{ 
      padding: "20px", 
      border: "1px solid #ddd",
      borderRadius: "8px",
      maxWidth: "400px",
      margin: "0 auto"
    }}>
      <h2>Informações do Pokémon</h2>
      
      {loading ? (
        <div className="loading" style={{ 
          textAlign: "center", 
          padding: "20px" 
        }}>
          <div className="spinner" style={{ 
            display: "inline-block",
            width: "30px",
            height: "30px",
            border: "3px solid #f3f3f3",
            borderTop: "3px solid #3498db",
            borderRadius: "50%",
            animation: "spin 1s linear infinite"
          }}></div>
          <style>{`
            @keyframes spin {
              0% { transform: rotate(0deg); }
              100% { transform: rotate(360deg); }
            }
          `}</style>
          <p>Carregando...</p>
        </div>
      ) : error ? (
        <div className="error" style={{ 
          color: "red", 
          padding: "10px",
          backgroundColor: "#ffebee",
          borderRadius: "4px" 
        }}>
          <p>Erro: {error}</p>
        </div>
      ) : pokemon && (
        <div className="pokemon-details">
          <h3 style={{ textTransform: "capitalize" }}>{pokemon.name}</h3>
          {pokemon.sprites?.front_default && (
            <img 
              src={pokemon.sprites.front_default} 
              alt={pokemon.name}
              style={{ display: "block", margin: "0 auto" }}
            />
          )}
          <div style={{ marginTop: "10px" }}>
            <p><strong>Altura:</strong> {pokemon.height / 10}m</p>
            <p><strong>Peso:</strong> {pokemon.weight / 10}kg</p>
            <p><strong>Tipos:</strong> {
              pokemon.types?.map(t => t.type.name).join(", ")
            }</p>
          </div>
        </div>
      )}
    </div>
  );
}
```

No exemplo acima, fazemos uma requisição para buscar os dados do Pikachu. Para isso, usamos três variáveis de estado:

* `pokemon`: guarda os dados recebidos da API
* `loading`: indica se a requisição ainda está em andamento
* `error`: armazena uma mensagem de erro, se algo der errado

Essas três variáveis controlam o que aparece na tela, dependendo do estado da requisição.

Já no `useEffect`, fazemos o seguinte:

1. Colocamos `loading` como `true` para indicar que estamos buscando dados.
2. Usamos `fetch()` para acessar a API.
3. Se a resposta estiver ok, transformamos ela em JSON e salvamos no estado.
4. Se der erro (por exemplo, se a API estiver fora do ar), capturamos esse erro e guardamos a mensagem em `error`.
5. Em ambos os casos, desativamos o `loading`.

Importante: como usamos um array de dependências vazio (`[]`), isso significa que o efeito **só será executado uma vez**, quando o componente for exibido pela primeira vez. ☝️🤓

Já na tela, a interface será **renderizada com base no estado atual**:

* Se `loading` for verdadeiro, mostramos uma animação de carregamento.
* Se `error` tiver uma mensagem, mostramos a mensagem de erro em destaque.
* Se tudo der certo, mostramos os dados do Pokémon, como nome, imagem, altura, peso e tipos.

Esse padrão é muito usado no desenvolvimento de aplicações reais com React, porque nos permite lidar com diferentes situações de forma clara e controlada: **carregando**, **erro** ou **dados disponíveis**.

Este exemplo demonstra um padrão comum:
1. Definimos estados para os dados, carregamento e erros
2. Usamos `useEffect` para buscar dados quando o componente monta
3. Atualizamos os estados conforme a requisição progride
4. Renderizamos diferentes UIs baseadas no estado atual

Legal, né?! Agora vemos claramente como as coisas vão se encaixando. 😊 

### ⚠️ **6.5 useEffect e async/await**

Outro ponto importante a destacar é que o corpo da função passada para `useEffect` **não pode ser assíncrono diretamente**. Isso ocorre porque o React espera que a função retorne uma função de limpeza ou nada, não uma Promise.

```tsx
// ❌ ERRADO: useEffect não aceita funções async diretamente
useEffect(async () => {
  const response = await fetch("https://api.exemplo.com/dados");
  const data = await response.json();
  setDados(data);
}, []);

// ✅ CORRETO: Declare uma função async dentro do efeito
useEffect(() => {
  async function fetchData() {
    try {
      const response = await fetch("https://api.exemplo.com/dados");
      const data = await response.json();
      setDados(data);
    } catch (error) {
      setError(error.message);
    } finally {
      setLoading(false);
    }
  }
  
  fetchData();
}, []);
```

Este padrão permite usar a sintaxe mais limpa do `async/await` enquanto respeita as regras do `useEffect`.

### 🧹 **6.6 Limpando efeitos (cleanup)**

Alguns efeitos precisam ser "limpos" quando o componente é desmontado ou antes de serem executados novamente. Isso evita comportamentos indesejados, como múltiplos temporizadores ativos, vazamento de memória ou escutas de eventos duplicadas.

Algumas situações típicas em que usamos essa limpeza incluem:

* Cancelar requisições em andamento
* Remover event listeners (ouvinte de eventos)
* Limpar `setInterval` ou `setTimeout`
* Cancelar assinaturas de WebSocket ou serviços externos

Para isso, usamos um **retorno dentro do `useEffect`**. Esse retorno é **uma função** que será automaticamente chamada pelo React **no momento certo**:

* Antes de o efeito ser executado novamente (quando alguma dependência muda)
* Ou quando o componente for removido da tela (desmontado)

Sintaticamente, você pode identificar a função de limpeza por este padrão:

```tsx
useEffect(() => {
  // Efeito principal
  ...

  // Função de limpeza (return dentro do useEffect)
  return () => {
    // Código que desfaz o efeito
  };
}, [/* dependências */]);
```

O `return () => { ... }` **não é um return do componente**, mas sim da função passada ao `useEffect`. Ele diz ao React: “Quando for a hora de limpar, execute isso aqui”.

Vejamos um exemplo abaixo:

```tsx
function Cronometro() {
  const [segundos, setSegundos] = useState(0);
  
  useEffect(() => {
    console.log("⏱️ Iniciando cronômetro");
    
    // Configuramos o temporizador
    const intervalo = setInterval(() => {
      setSegundos(s => s + 1);
    }, 1000);
    
    // Retornamos uma função de limpeza
    return () => {
      console.log("⏱️ Limpando cronômetro");
      clearInterval(intervalo);
    };
  }, []);
  
  return (
    <div style={{ 
      textAlign: "center", 
      padding: "20px",
      border: "1px solid #ddd",
      borderRadius: "8px",
      maxWidth: "300px",
      margin: "20px auto"
    }}>
      <h2>Cronômetro</h2>
      <p style={{ fontSize: "2rem", fontWeight: "bold" }}>
        {segundos} segundos
      </p>
    </div>
  );
}
```

Nesse caso a função de limpeza é chamada:
1. Antes de executar o efeito novamente (se as dependências mudaram)
2. Quando o componente é desmontado (removido da UI)

### 🔍 **6.7 Exemplo prático: Pesquisa com debounce**

Vamos criar um componente de pesquisa que busca Pokémon à medida que o usuário digita, mas com um pequeno atraso para evitar muitas requisições:

```tsx
import React, { useState, useEffect } from "react";

function PokemonSearch() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  
  // Este efeito será executado quando 'query' mudar
  useEffect(() => {
    // Não pesquisamos se a query estiver vazia
    if (!query.trim()) {
      setResults([]);
      return;
    }
    
    // Definimos loading como true
    setLoading(true);
    
    // Criamos um timer para esperar que o usuário pare de digitar
    const timer = setTimeout(() => {
      fetch(`https://pokeapi.co/api/v2/pokemon?limit=100`)
        .then(res => res.json())
        .then(data => {
          // Filtramos os resultados que contêm a query
          const filteredResults = data.results.filter(
            pokemon => pokemon.name.includes(query.toLowerCase())
          ).slice(0, 5); // Limitamos a 5 resultados
          
          setResults(filteredResults);
          setLoading(false);
        })
        .catch(err => {
          console.error("Erro na busca:", err);
          setLoading(false);
        });
    }, 500); // 500ms de debounce
    
    // Limpamos o timer se a query mudar antes do tempo
    return () => clearTimeout(timer);
  }, [query]);
  
  return (
    <div style={{ maxWidth: "400px", margin: "0 auto", padding: "20px" }}>
      <h2>Buscar Pokémon</h2>
      
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Digite o nome do Pokémon..."
        style={{
          width: "100%",
          padding: "10px",
          fontSize: "16px",
          borderRadius: "4px",
          border: "1px solid #ddd",
          marginBottom: "20px"
        }}
      />
      
      {loading && <p>Buscando...</p>}
      
      {results.length > 0 ? (
        <ul style={{ 
          listStyle: "none", 
          padding: 0,
          border: "1px solid #eee",
          borderRadius: "4px" 
        }}>
          {results.map(pokemon => (
            <li 
              key={pokemon.name}
              style={{
                padding: "10px 15px",
                borderBottom: "1px solid #eee",
                textTransform: "capitalize"
              }}
            >
              {pokemon.name}
            </li>
          ))}
        </ul>
      ) : query && !loading && (
        <p>Nenhum Pokémon encontrado com "{query}"</p>
      )}
    </div>
  );
}
```

Neste código acima, criamos um pequeno componente de busca que consulta a PokéAPI conforme o usuário digita o nome de um Pokémon. No entanto, para **evitar que a API seja chamada a cada letra digitada**, usamos uma técnica chamada **debounce**. Esse componente:

* Exibe um campo de texto para o usuário digitar.
* Espera **meio segundo (500ms)** após a última tecla antes de fazer a requisição.
* Filtra os resultados localmente, mostrando até 5 Pokémon que contenham o texto digitado.
* Cancela a busca anterior se o usuário digitar algo novo antes do tempo acabar.
* Mostra mensagens de carregamento ou de erro quando apropriado.

Para isso usamos o `useEffect`, que executa uma ação toda vez que uma variável (ou conjunto de variáveis) muda. Neste exemplo, usamos `useEffect` para "ouvir" mudanças na variável `query`, que representa o texto digitado pelo usuário.

Quando `query` muda:

1. **Um `setTimeout` de 500ms é iniciado**. Ele aguarda meio segundo antes de realizar a requisição.
2. Se o usuário digitar novamente **antes do tempo acabar**, o `useEffect` é executado de novo, **cancelando o timeout anterior com `clearTimeout`**.
3. Isso evita várias requisições desnecessárias e melhora a performance — comportamento conhecido como **debounce manual**. 👽

Para entender isso melhor, imagine que o usuário começa a digitar "pikachu". A cada letra (`p`, `pi`, `pik`...), o React **reinicia o timer de 500ms**. Somente quando o usuário **parar de digitar por pelo menos meio segundo**, a requisição é enviada.

Se a busca for bem-sucedida, os resultados são filtrados localmente e armazenados no estado `results`. Se houver erro, é tratado no `catch`. O campo `loading` controla o que deve ser exibido na tela: uma mensagem "Buscando..." ou os resultados, por exemplo.

Esse código mostra vários conceitos importantes:

* **Estado local com `useState`**: Armazena o texto da busca (`query`), os resultados (`results`) e se a busca está em andamento (`loading`).
* **Renderização condicional**: O componente exibe mensagens ou listas **com base nos valores de estado**, como `loading`, `results.length` e `query`.
* **Limpeza de efeito (`return () => { ... }`)**: Garante que o efeito anterior seja cancelado antes que o novo comece, evitando conflitos ou buscas duplicadas.

### 🧠 **6.8 Regras importantes do useEffect**

Para usar `useEffect` corretamente, lembre-se destas regras:

1. **Sempre inclua todas as dependências usadas dentro do efeito**
   ```tsx
   // Se você usa 'userId' dentro do efeito, inclua-o nas dependências
   useEffect(() => {
     fetchUserData(userId);
   }, [userId]);
   ```

2. **Evite dependências que mudam frequentemente** para prevenir loops infinitos

3. **Use a forma funcional do setState** quando atualizar estado baseado em valor anterior
   ```tsx
   useEffect(() => {
     const timer = setInterval(() => {
       setCount(c => c + 1); // Forma funcional é mais segura
     }, 1000);
     return () => clearInterval(timer);
   }, []);
   ```

4. **Separe efeitos com propósitos diferentes** em chamadas distintas de `useEffect`
   ```tsx
   // Um efeito para buscar dados do usuário
   useEffect(() => {
     fetchUserData(userId);
   }, [userId]);
   
   // Outro efeito para atualizar o título da página
   useEffect(() => {
     document.title = `Perfil de ${userName}`;
   }, [userName]);
   ```

### ✅ **6.9 Conclusão da Seção**

O `useEffect` é um hook fundamental que nos permite sincronizar nossos componentes React com sistemas externos, como APIs, eventos do navegador e temporizadores.

Pontos-chave para lembrar:

* Use `useEffect` para executar código que não está diretamente relacionado à renderização
* O array de dependências controla quando o efeito é executado
* Retorne uma função de limpeza quando necessário para evitar vazamentos de memória
* Separe efeitos com propósitos diferentes
* Não use `async` diretamente na função passada para `useEffect`

Com `useState` e `useEffect`, você já tem as ferramentas básicas para criar componentes interativos e dinâmicos que se comunicam com o mundo exterior. Entendendo isso, entendemos a base do React. Estamos prontos, agora, para finalmente entrar nos conceitos do React Native na próxima aula!

Antes disso, na próxima seção, vamos aplicar esses conhecimentos em um projeto prático, construindo uma aplicação completa que integra tudo o que aprendemos até agora.

---

## **7. Aplicação prática: buscando dados na PokéAPI**

Agora que entendemos o funcionamento de `useState` e `useEffect`, é hora de aplicá-los juntos para criar uma pequena aplicação que:

* Consulta a PokéAPI;
* Exibe dados básicos de um Pokémon;
* Permite ao usuário digitar um nome e buscar dinamicamente.

Essa será nossa **primeira tela funcional com React**, e ela marcará o início da construção da nossa Pokédex (que evoluíremos, posteriormente, para React Native). 🎯

### 🧪 **Objetivo**

Construir um componente que:

1. Recebe o nome de um Pokémon via campo de texto;
2. Ao clicar em "Buscar", consulta a PokéAPI;
3. Exibe o nome, altura, peso e tipos do Pokémon;
4. Mostra uma mensagem de erro caso o nome seja inválido.

### 🧱 **Passo a passo: estrutura base**

Vamos usar a seguinte estrutura de pastas. Como montaremos o projeto primeiramente com React Web, vamos usar o Vite para criá-lo. O **Vite** é uma ferramenta de build moderna e super rápida para projetos web que oferece **inicialização instantânea** e **atualizações rápidas durante o desenvolvimento**, ideal para projetos React, Vue, entre outros. Não se preocupe muito: só vamos utilizá-lo nesse momento do curso, em que estamos vendo os conceitos de React. Após isso retomaremos o React Native e não precisaremos utilizar o Vite.

Vamos criar o projeto com:

```bash
npm create vite@latest pokedex-react -- --template react-ts
```

Ou, estiver usando o `yarn`:

```bash
yarn create vite pokedex-react --template react-ts
```

Vamos acessar o diretório do projeto com

```bash
cd pokedex-react
```

E depois instalar as dependências com 

```bash
npm install
```

Após isso, vamos criar a pasta de componentes

```bash
mkdir src/components
```

Nossa estrutura de diretórios ficará a seguinte

```
pokedex-react/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   └── Pokedex.tsx      ← Componente principal
│   │   └── Pokedex.css      ← Estilização do componente 
│   ├── App.tsx              ← Componente raiz
│   └── main.tsx             ← Ponto de entrada
├── package.json
├── tsconfig.json
└── vite.config.ts (se usar Vite)
```

Feito isso, vamos ver como ficará nosso código!

### 🧱 **Componente `Pokedex.tsx`**

Crie o arquivo `src/components/Pokedex.tsx`:

```tsx
import React, { useState } from "react";
import "./Pokedex.css";

// Definindo o tipo com base no json para simplificar a implementação
type Pokemon = {
  name: string;
  height: number;
  weight: number;
  sprites: {
    front_default: string | null;
  };
  types: Array<{
    type: { name: string };
  }>;
};

export default function Pokedex() {
  const [nome, setNome] = useState("");
  const [carregando, setCarregando] = useState(false);

  const [pokemon, setPokemon] = useState<Pokemon | null>(null);
  const [erro, setErro] = useState("");

  const buscarPokemon = async () => {
    if (!nome.trim()) return;

    setCarregando(true);
    setErro("");
    setPokemon(null);

    try {
      const resposta = await fetch(
        `https://pokeapi.co/api/v2/pokemon/${nome.toLowerCase()}`
      );
      if (!resposta.ok) throw new Error("Pokémon não encontrado");

      // Convertemos o JSON dizendo ao TS que ele tem formato Pokemon 
      const dados: Pokemon = await resposta.json();
      setPokemon(dados);
    } catch (e) {
      setErro("Pokémon não encontrado 😢");
    } finally {
      setCarregando(false);
    }
  };

  return (
    <div className="pokedex-container">
      <h2 className="pokedex-title">🔎 Pokédex</h2>

      <input
        className="pokedex-input"
        type="text"
        placeholder="Digite o nome do Pokémon"
        value={nome}
        onChange={(e) => setNome(e.target.value)}
      />

      <button className="pokedex-button" onClick={buscarPokemon}>
        Buscar
      </button>

      {carregando && <p className="pokedex-loading">Carregando...</p>}
      {erro && <p className="pokedex-error">{erro}</p>}

      {pokemon && (
        <div className="pokedex-card">
          <h3 className="pokedex-name">{pokemon.name}</h3>
          {pokemon.sprites.front_default && (
            <img
              src={pokemon.sprites.front_default}
              alt={pokemon.name}
              className="pokedex-image"
            />
          )}
          <p>
            <strong>Altura:</strong> {pokemon.height * 10} cm
          </p>
          <p>
            <strong>Peso:</strong> {pokemon.weight / 10} kg
          </p>
          <p>
            <strong>Tipos:</strong>{" "}
            {pokemon.types.map((t) => t.type.name).join(" / ")}
          </p>
        </div>
      )}
    </div>
  );
}
```

No código acima, começamos importando dois itens da biblioteca React – o objeto-módulo padrão **React** e, entre chaves, o **useState**. Essa sintaxe é chamada *named import*: quando a biblioteca exporta várias entidades, escolhemos pelo nome apenas as que nos interessam (aqui, o hook useState), o que torna o código mais claro. Em seguida carregamos o CSS local para aplicar estilos de forma local ao componente.

Logo abaixo definimos **Pokemon**, um *type alias* do TypeScript que descreve exatamente o formato do objeto retornado pela PokéAPI: nome, altura, peso, um caminho para a miniatura e uma lista de tipos. Declarar esse tipo permite ao compilador verificar acesso a propriedades inexistentes e habilita auto-completar durante a escrita do código.

Dentro do componente funcional **Pokedex** declaramos quatro estados usando o hook *useState*. Cada chamada a useState devolve um par `[valor, setValor]`; armazenamos cada par em constantes porque o ponteiro para o valor e a função de atualização nunca mudam (o conteúdo muda, mas a referência permanece). Assim:

* **nome / setNome** guarda o texto do input.
* **carregando / setCarregando** indica se há requisição em andamento.
* **pokemon / setPokemon** armazena o resultado da busca; o tipo `Pokemon | null` deixa explícito que o estado começa vazio.
* **erro / setErro** guarda mensagens de falha para exibir ao usuário.

Todas as variáveis foram declaradas com **const** porque em JavaScript/TypeScript `const` congela apenas a referência, não o conteúdo interno. Como não reatribuiremos novos pares de estado, faz sentido usar `const` para sinalizar essa imutabilidade estrutural.

"Professor, mas isso é esquisito, hein?". Calma! Vamos reforçar o entendimento. 👽🤓

Aqui em JavaScript/TypeScript, **`const` não congela o universo inteiro** – ela só diz que o **apelido** (a referência) não vai ser trocado **durante aquela execução** da função.

Quando o React re-renderiza o componente, ele **chama a função `Pokedex` de novo** do zero. Isso significa que - dentro dessa chamada - o código faz:

```ts
const [nome, setNome] = useState("");
```

e cria **novas** variáveis locais chamadas `nome` e `setNome`. Na execução anterior elas já “morreram”; estas são outras, fresquinhas. Então não há nenhuma violação do `const`: cada versão da variável vive apenas durante o render em que foi criada.

Além disso, o que realmente guarda o estado é uma estrutura interna do React.
Quando chamamos `setNome("Pikachu")`, **não mudamos a variável `nome` diretamente**; pedimos ao React:

> “Guarde ‘Pikachu’ como o novo estado, por favor.”

O React salva isso lá dentro, agenda um novo render, e *na próxima chamada* da função ele devolve o par `[ "Pikachu", setNome ]`. A nossa variável `nome` continua de “const” intacta - ela simplesmente começa a vida já valendo `"Pikachu"`.

Ou seja, se dentro do **mesmo** ciclo de execução (isto é, enquanto o código daquela função ainda está rodando) tentarmos reatribuir uma variável declarada com `const`, será disparado um erro de tempo de execução:

```ts
const pokemon = "Pikachu";
pokemon = "Charmander";   // TypeError: Assignment to constant variable.
```

O motor JavaScript impede a reatribuição porque `const` fixa o **apelido** (`nome`) ao primeiro valor (ou referência) que você deu para ele naquela execução. Agora, no caso do React, quando você quer mudar o valor “Pikachu” para Charmander”, **não** faz `pokemon = "Charmander"`; você chama `setPokemon("Charmander")`. Isso grava o novo valor num “cofre” interno do React e agenda outro render. Na próxima vez que a função componente for invocada, o React cria uma **nova** variável `const pokemon`, já iniciada como `"Charmander"`, sem jamais ter quebrado a regra de imutabilidade dentro de um único ciclo.

Resumindo: **`const` garante imutabilidade da **referência** dentro daquela execução;** o estado muda por fora, entre execuções, e o React devolve o novo valor quando a função roda novamente. Não há, portanto, nenhuma contradição, apenas um "truque" elegante do ciclo de renderização!

Beleza, reforçado isso, vamos continuar a explicação!

A função **buscarPokemon** concentra a lógica de busca. É uma arrow-function assinalada a uma constante – novamente indicando que o identificador não mudará. Ela é marcada *async* para que possamos usar `await` e escrever código assíncrono com aparência sequencial: chamamos `fetch`, esperamos a resposta, transformamos em JSON e só então seguimos. Esse estilo evita a pirâmide de *then()* e torna o fluxo de erro mais linear com `try/catch`. A primeira linha da função aborta se o usuário enviou campo vazio (`!nome.trim()`). Depois mudamos o estado para o ciclo “carregando”: limpamos erros, limpamos resultado anterior e ativamos o "spinner". Quando a `fetch` resolve, verificamos `resposta.ok`. Caso contrário, lançamos erro para cair no `catch`. No caminho feliz, tipamos o JSON como `Pokemon` para que o TypeScript passe a enxergar o objeto com o formato correto e então gravamos em **pokemon**; no `finally` desligamos o indicador de carregamento.

Já a **renderização condicional**, na parte do `return` que está abaixo da função **buscarPokemon**, é construída só com JSX: primeiro a estrutura fixa (título, input e botão). O atributo `onChange` do input usa `setNome` para sincronizar cada tecla com o estado; o botão dispara `buscarPokemon`. Em seguida, três blocos condicionais: se `carregando` verdadeiro mostramos “Carregando…”, se `erro` possuir texto mostramos o erro, e se `pokemon` estiver preenchido exibimos o card. O card utiliza as informações já validadas pelo tipo: nome em título, imagem se existir, altura (convertida para centímetros), peso (em quilos) e a lista de tipos concatenada. Quando qualquer estado muda via `setX`, o React refaz a função de componente, calcula uma nova árvore virtual e aplica ao DOM apenas as diferenças, garantindo a reatividade.

Por fim, a palavra-chave **export default** indica que este arquivo exporta uma entidade principal – o componente Pokedex. Quem importar o caminho `./Pokedex` receberá essa função. Com isso o componente entra na árvore de renderização da aplicação, e toda a lógica descrita acima passa a reger a interface.

### 💃 **Estilo `Pokedex.css`**

```css
.pokedex-container {
  padding: 20px;
  max-width: 500px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
  text-align: center;
  color: #333;
}

.pokedex-title {
  font-size: 24px;
  margin-bottom: 16px;
}

.pokedex-input {
  padding: 10px;
  width: 100%;
  margin-bottom: 10px;
  border-radius: 4px;
  border: 1px solid #ccc;
  font-size: 16px;
}

.pokedex-button {
  padding: 10px 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

.pokedex-loading {
  margin-top: 10px;
  color: #888;
}

.pokedex-error {
  margin-top: 10px;
  color: red;
  background: #ffe0e0;
  padding: 8px;
  border-radius: 4px;
}

.pokedex-card {
  margin-top: 20px;
  background-color: #f8f8f8;
  padding: 16px;
  border-radius: 8px;
  text-align: left;
}

.pokedex-name {
  text-transform: capitalize;
  margin-bottom: 10px;
}

.pokedex-image {
  display: block;
  margin: 0 auto 10px;
}
```

### 🧩 **Usando no `App.tsx`**

```tsx
import React from "react";
import Pokedex from "./components/Pokedex";

export default function App() {
  return (
    <div>
      <Pokedex />
    </div>
  );
}
```

**Para evitar conflitos de estilo, não se esqueça de comentar a linha `import './index.css'` em seu arquivo `main.tsx`**.

Depois disso, basta iniciar o ambiente de desenvolvimento:

```bash
npm run dev
```

E a aplicação será servida em **[http://localhost:5173](http://localhost:5173)** – abra essa URL no navegador para ver a Pokédex em funcionamento. 🤩

Se quiser acompanhar uma breve demonstração do código acima, confira o vídeo **“PokéDex em React”**:

[Assista no YouTube – PokéDex em React](https://youtu.be/E2qN0vF5GXU)

### Em resumo …

Este exercício reúne todos os conceitos centrais que vimos nesta aula. O vídeo acima mostra, passo a passo, como o código foi construído – uma ótima forma de reforçar o aprendizado antes de avançarmos! 😊

---

## **8. Considerações finais, boas práticas e próximos passos**

Ao longo desta aula, demos um passo essencial rumo ao desenvolvimento de aplicações móveis com React Native: **entendemos como funciona a arquitetura baseada em componentes** e **criamos nosso primeiro app funcional**, consumindo uma API real. 🚀

Agora é hora de **refletir sobre o que aprendemos**, identificar boas práticas que podemos aplicar desde o início e visualizar como evoluir essa estrutura nas próximas aulas, onde iniciaremos com React Native.

Nessa aula aprendemos nesta aula:

* Como **React funciona com componentes reutilizáveis**, e como isso impacta diretamente a organização de interfaces em apps mobile;
* A sintaxe e os conceitos do **JSX**, que tornam possível combinar HTML e JavaScript;
* O uso de **props** para comunicação entre componentes;
* Como utilizar **`useState`** para criar e modificar valores reativos dentro da interface;
* Como utilizar **`useEffect`** para lidar com efeitos colaterais — como chamadas à API;
* Como construir **um pequeno app funcional com React**;
* Como utilizar um serviço externo (PokéAPI) e exibir dados de forma responsiva;
* Como organizar visualmente a aplicação e manter clareza no fluxo lógico.

Um ponto importante para reforçar é que devemos seguir boas práticas desde o início. Mesmo em exemplos simples como o que desenvolvemos hoje, já é possível ver algumas boas práticas:

| Boas práticas                        | Por que aplicar?                                             |
| ------------------------------------ | ------------------------------------------------------------ |
| Usar `useState` de forma clara       | Ajuda a isolar os estados e entender melhor a lógica da tela |
| Tratar erros nas chamadas à API      | Melhora a experiência do usuário e evita falhas silenciosas  |
| Separar componentes em arquivos      | Favorece a organização e a reutilização do código            |
| Utilizar nomes descritivos           | Melhora a leitura do código e ajuda na colaboração em equipe |
| Declarar variáveis com `const`       | Torna claro que o valor não será reatribuído diretamente     |

Além disso, usamos **tipagem com TypeScript** para garantir segurança e previsibilidade no desenvolvimento.

Lembre-se: o React é a base sobre a qual construíremos aplicações móveis com React Native. Entender bem seus princípios — componentes, estado, efeitos — é o que permitirá que você crie interfaces ricas e funcionais. 

Se quiser aprofundar ainda mais o que vimos hoje, você pode explorar:

* [Documentação oficial do React](https://react.dev/learn/)
* [Documentação do Hook `useState`](https://react.dev/reference/react/useState)
* [Documentação do Hook `useEffect`](https://react.dev/reference/react/useEffect)
* [PokéAPI](https://pokeapi.co/) — para experimentar com mais endpoints
* E claro: revise o código da Pokédex e tente modificar algo por conta própria! ✨

Na **próxima aula**, vamos mergulhar na **construção de apps com React Native**. E o melhor: já com base no que aprendemos hoje.

Antes disso, teremos, como sempre...

---

## 🔧 9. Exercícios

### Exercício – Melhorando a Pokédex!

### 🎯 **Objetivo Geral**

A partir do código base da Pokédex implementado em aula, você deverá realizar melhorias funcionais e visuais que envolvem o uso de `props`, `useState`, `useEffect`, estilização separada e organização de componentes.

### 📌 **Tarefas obrigatórias**

1. **Refatore o componente principal (`Pokedex.tsx`)** para que ele **utilize um componente `PokeCard`**. Este novo componente deverá receber os dados do Pokémon via `props` e exibi-los com base no que já foi implementado (nome, altura, peso, tipos e imagem).

2. **Adicione a funcionalidade de “favoritar” um Pokémon**:

   * Crie um botão no `PokeCard` que permita marcar ou desmarcar um Pokémon como favorito.
   * Use o `useState` dentro do `PokeCard` para armazenar esse estado.
   * Exiba um ⭐ ao lado do nome se o Pokémon estiver marcado como favorito.

3. **Estilize a aplicação utilizando o arquivo `Pokedex.css` e crie um novo `PokeCard.css`**:

   * Separe as responsabilidades visuais entre os arquivos CSS de `Pokedex` e `PokeCard`.
   * Melhore o layout centralizando a tela, ajustando cores e tornando os elementos visualmente mais amigáveis.

4. **Utilize o `useEffect` para exibir uma mensagem no console toda vez que um novo Pokémon for carregado**:

   * Exemplo de mensagem: `"Pokémon Bulbasaur carregado com sucesso!"`.

### 💡 Dicas

* **Crie o `PokeCard.tsx` em `src/components`**.
* No `useEffect`, adicione `pokemon` como dependência para escutar mudanças nos dados.
* Lembre-se de que props são apenas leitura. O estado de favorito deve ser local ao `PokeCard`.
* Mantenha a legibilidade do código: separe lógica, apresentação e estilo de forma limpa.

### ✅ **Critérios de avaliação**

| Critério                                 | Peso |
| ---------------------------------------- | ---- |
| Uso correto de `props` e `PokeCard`      | 3.0  |
| Implementação da lógica de favorito      | 2.0  |
| Uso de `useEffect` para log de evento    | 2.0  |
| Organização do CSS em arquivos separados | 1.5  |
| Organização geral e clareza do código    | 1.5  |

### 🌟 Desafio (extra)

* **Permita buscar vários Pokémons em sequência** e mostre todos abaixo, em cards separados.
* **Armazene os favoritos em `localStorage`** usando `useEffect`.
