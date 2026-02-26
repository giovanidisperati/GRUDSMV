---
layout: aula
title: "2. Context API – Do Estado Local ao Global"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 1
---

## **2. Do Estado Local ao Global: A Context API do React**

Como vimos, o grande obstáculo para compartilhar informações, como a nossa lista de favoritos, é que o `useState` confina o estado a um único componente. A alternativa seria passar dados de componente em componente (`props`), uma prática que, além de verbosa, acopla desnecessariamente toda a árvore de componentes. É o que chamamos de *prop drilling*.

Para resolver isso de forma elegante, o React nos oferece uma ferramenta poderosa e nativa: a **Context API**.

Pense na Context API como uma espécie de "provedor de dados" global para uma parte da sua aplicação. Em vez de cada componente pedir informações ao seu "pai" direto, ele pode consumir os dados diretamente dessa fonte central, não importa o quão fundo ele esteja na árvore de componentes.

A seguir, vamos desmembrar como criar, prover e consumir um contexto, estabelecendo a base para o nosso sistema de favoritos global.

### **2.1. A Limitação do `useState` e o Desafio do "Prop Drilling"**

O hook `useState` é a ferramenta fundamental para gerenciar o estado *dentro* de um componente. Ele é simples, eficiente e o principal mecanismo que faz com que nossas interfaces reajam e se atualizem. Sua principal característica, que é também sua limitação, é o **escopo local**: o estado declarado com `useState` pertence exclusivamente à instância do componente onde ele foi criado.

Isso funciona perfeitamente para dados que só interessam àquele componente, como o texto de um campo de busca ou o estado de "aberto/fechado" de um modal.

O problema surge quando múltiplas telas ou componentes distantes na hierarquia precisam acessar ou modificar a mesma informação. Se o estado `favoritos` estivesse no componente `App` (o mais alto na hierarquia), como faríamos para que um `PokemonCard` (que pode estar vários níveis abaixo) pudesse adicionar um novo favorito?

A única solução usando apenas `useState` seria passar a função de atualização de estado (`setFavoritos`) como `prop` de pai para filho, repetidamente:

1.  `App` passaria `setFavoritos` para `PokedexScreen`.
2.  `PokedexScreen`, mesmo sem usar a função, a passaria para `FlatList`.
3.  `FlatList` a passaria para cada `PokemonCard`.

Esse processo de "perfurar" a árvore de componentes com propriedades que não são usadas pelos componentes intermediários é conhecido como **Prop Drilling**. Ele traz várias desvantagens:

  * **Código Acoplado e Verboso:** Os componentes intermediários se tornam meros "repassadores" de `props`, tornando o código poluído e difícil de ler.
  * **Difícil Manutenção:** Se a `prop` precisar ser renomeada ou seu formato alterado, é necessário modificar todos os componentes no caminho.
  * **Baixa Reutilização:** Um componente se torna menos reutilizável se ele depende de `props` que só existem para serem passadas adiante.

Fica claro, portanto, que `useState` combinado com a passagem de `props` não é uma solução escalável para gerenciar dados que precisam ser acessados por diversas partes da aplicação. Precisamos de um mecanismo que permita "teletransportar" o estado diretamente para onde ele é necessário, sem intermediários. É exatamente essa a função da Context API, que veremos a seguir.

### **2.2. A Solução Nativa: Introdução à Context API (`createContext`, `Provider`, `useContext`)**

Para evitar o *prop drilling* e fornecer um meio mais limpo e direto de compartilhar estado, o React oferece uma solução nativa: a **Context API**.

Imagine a Context API como uma rede Wi-Fi para seus dados. Você cria um "roteador" (o `Provider`) que transmite um sinal (o estado). Qualquer componente que precise desses dados, não importa onde esteja na árvore de componentes, pode simplesmente "conectar-se" a essa rede (usando `useContext`) para receber o sinal, sem a necessidade de "cabos" (`props`) passando por todos os cômodos (componentes intermediários).

O funcionamento da Context API se baseia em três pilares:

**1. `createContext`**

É a função que inicia o processo. Ela cria um objeto de "contexto" que os componentes irão ler. Pense nisso como criar uma nova "frequência de rádio" ou o "nome da sua rede Wi-Fi".

```javascript
// A criação do contexto geralmente é feita em um arquivo separado
const FavoritesContext = createContext(null);
```

O valor passado para `createContext` (neste caso, `null`) é o valor padrão, usado apenas se um componente tentar consumir o contexto sem que haja um `Provider` acima dele na árvore.

**2. O Componente `Provider`**

Todo contexto criado com `createContext` vem com um componente `Provider`. A função dele é envolver a parte da sua aplicação que precisa ter acesso aos dados globais. Este é o nosso "roteador" ou a "antena transmissora".

Ele aceita uma `prop` obrigatória chamada `value`, onde você passa os dados que deseja disponibilizar para todos os componentes filhos.

```javascript
// Geralmente no topo da aplicação, como em App.tsx
<FavoritesContext.Provider value={{ favorites, addFavorite }}>
  {/* Todos os componentes aqui dentro podem acessar 'favorites' e 'addFavorite' */}
  <MinhaAplicacao />
</FavoritesContext.Provider>
```

**3. O Hook `useContext`**

Esta é a forma como os componentes "se inscrevem" ou "ouvem" o contexto para consumir os dados. É o nosso "rádio" ou o "dispositivo se conectando ao Wi-Fi".

O hook `useContext` recebe o objeto de contexto como argumento (o que foi criado com `createContext`) e retorna o valor que foi passado para a `prop` `value` do `Provider` mais próximo. O mais importante: qualquer componente que use `useContext` será automaticamente re-renderizado sempre que o valor do `Provider` mudar.

```javascript
// Dentro de qualquer componente filho do Provider
const { favorites, addFavorite } = useContext(FavoritesContext);
```

Em resumo, o fluxo é simples:

1.  **Crie** o contexto com `createContext`.
2.  **Proveja** o estado com o componente `<Context.Provider>` em um ponto alto da sua aplicação.
3.  **Consuma** o estado onde for necessário com o hook `useContext`.

Compreendido o plano de ação, vamos para a prática!

-----
