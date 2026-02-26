---
layout: aula
title: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 7
has_children: true
permalink: /aula06
---

# **Aula 06 – Estado Global, Persistência de Dados e Arquitetura MVVM na Prática**

Depois de cinco aulas, vimos desde os fundamentos do Javascript/TypeScript até a construção de interfaces com React Native. Evoluímos de uma listagem simples de Pokémons para uma Pokédex navegável, e agora vamos dar um passo além. Até aqui, focamos em componentes isolados e na navegação básica; nessa aula começaremos a encarar problemas típicos de aplicativos “de verdade”: compartilhar informações entre telas, manter dados salvos mesmo depois que o app fecha e organizar melhor a lógica para não virar bagunça quando o projeto crescer.

-----

### **1. Introdução: O Próximo Nível da Nossa Aplicação**

Nas aulas anteriores, construímos uma Pokédex funcional, evoluindo de uma única tela para uma aplicação com navegação e uma estrutura de arquivos bem definida.

Aprendemos a buscar dados de uma API com `useEffect` e a gerenciar o estado local de componentes com `useState`. Na última aula, mergulhamos na teoria de arquitetura de software, discutindo padrões como MVC, MVP e MVVM, que nos preparam para pensar em como organizar aplicações complexas.

Para ter acesso ao código implementado na última aula (antes dos exercícios), acesse o [Branch **Aula05** – PokedexApp](https://github.com/giovanidisperati/PokedexApp/tree/Aula05).

No estágio atual nossa aplicação, apesar de simples, está longe de ser perfeita:

  * **A performance é impactada por um problema de N+1 nas chamadas de API.** A função `getPokemons` busca a lista e depois faz uma requisição individual para cada Pokémon, resultando em 31 chamadas de rede apenas para carregar a primeira página, com 30 Pokémons. Isso é conhecido como um problema N+1, onde para buscar uma lista de N itens, você acaba fazendo N+1 requisições à rede. Desnecessário dizer que isso torna nosso carregamento inicial lento e consome um volume desnecessário de dados;

  * **A funcionalidade de busca é limitada.** O campo de busca na `PokedexScreen` filtra apenas os Pokémon que já foram carregados na memória do aplicativo. Um usuário não consegue encontrar um Pokémon que ainda não tenha aparecido na rolagem infinita, o que limita a utilidade da busca;

Além disso, também há certa redundância de dados, onde a tela de detalhes (`PokemonDetailsScreen`) busca novamente informações que já foram carregadas na tela anterior, como nome e imagem, causando uma chamada de API desnecessária.

Todas essas questões merecem atenção e serão devidamente resolvidas em momento oportuno. Trazer luz a essas questões meramente nos mostra o quanto temos que nos esmerar quando queremos construir uma aplicação correta e que, de fato, possa ser colocada em produção!

Por ora, entretanto, vamos "assumir nosso débito técnico" e avançar com os aspectos conceituais do desenvolvimento da nossa aplicação, que possui limitações importantes que são comuns em projetos que estão amadurecendo:

  * **A primeira limitação é que o estado é local**: Por exemplo, se quisermos marcar um Pokémon como favorito em uma tela, essa informação não estará disponível em outra.
  * **Além disso, os dados são voláteis**: Se o usuário fechar e reabrir o aplicativo, todas as interações (exemplo: uma lista de favoritos), serão perdidas.

Nesta aula, vamos partir desse ponto e adereçar essas limitações, abordando dois pilares do desenvolvimento de aplicativos robustos:

1.  **Gerenciamento de Estado Global:** Como compartilhar dados entre componentes e telas que não têm uma relação direta de pai e filho.
2.  **Persistência de Dados Locais:** Como salvar informações no dispositivo para que elas sobrevivam entre as sessões do aplicativo.

Para isso, implementaremos a funcionalidade de "Favoritos" em nossa Pokédex (por essa introdução nem dava para saber que faríamos isso, né? kkk).

Ah! E também faremos isso aplicando na prática os conceitos de arquitetura da aula anterior: vamos aproveitar e já transformar a teoria do MVVM em código!

Um vídeo será linkado no Moodle para relembrarmos onde paramos na aula anterior.

### **1.1. Recapitulando a Jornada: Da Interface à Navegação e Teoria de Arquitetura (Aula 05)**

Até aqui, nossa jornada no desenvolvimento mobile foi marcada por uma evolução clara e progressiva. Partimos dos fundamentos do React na Aula 03, onde aprendemos a gerenciar o estado local com `useState` e a buscar dados externos de APIs com `useEffect`. Em seguida, na Aula 04, aplicamos esse conhecimento para construir nossa primeira interface visual com React Native, dominando componentes como `View`, `Text`, `FlatList` e aprendendo a organizar o layout da tela com Flexbox e `StyleSheet`.

Na Aula 05, demos um salto significativo ao implementar a navegação com a biblioteca `react-navigation`, transformando nossa Pokédex de tela única em uma aplicação com um fluxo de múltiplas telas, onde o usuário pode navegar de uma lista para uma tela de detalhes. Além da parte técnica, iniciamos uma discussão sobre **arquitetura de software**, explorando os padrões MVC, MVP e MVVM e a importância da **separação de responsabilidades** para criar um código que seja manutenível e escalável.

Chegamos, portanto, a um ponto onde temos uma aplicação que não apenas exibe dados, mas também permite que o usuário navegue entre diferentes contextos. É exatamente essa nova complexidade que revela as limitações que abordaremos a seguir.

### **1.2. O Problema Atual: Estado Local Isolado e Dados Voláteis**

A arquitetura que construímos, embora funcional, expõe duas fraquezas fundamentais que impedem nosso aplicativo de oferecer uma experiência de usuário coesa e duradoura.

**1. Estado Local Isolado**

O hook `useState` que utilizamos até agora cria um estado que "vive e morre" dentro de um único componente. Ele não foi projetado para ser compartilhado facilmente através de telas que não têm uma relação direta.

Imagine que o usuário está na `PokemonDetailsScreen` e clica em um botão para favoritar o Charmander. Como a `PokedexScreen` (a tela da lista) saberá que deve exibir uma estrela ⭐ ao lado do Charmander? Atualmente, ela não tem como saber. A informação sobre o "favorito" está presa dentro da tela de detalhes. Essa comunicação entre telas distantes exigiria passar dados e funções através de múltiplas camadas de `props`, um padrão conhecido como ***prop drilling*** que rapidamente se torna complexo e insustentável.

**2. Dados Voláteis**

Qualquer estado que mantemos em memória com `useState` é, por natureza, **volátil**. Se o usuário gastar tempo montando sua lista de Pokémons favoritos, ele espera que essa lista esteja lá quando ele reabrir o aplicativo no dia seguinte. Com nossa implementação atual, toda a lista de favoritos desaparece no momento em que o app é fechado.

Isso quebra a confiança do usuário e torna qualquer funcionalidade de personalização inútil a longo prazo, fazendo com que a aplicação pareça incompleta ou pouco profissional.

Esses dois desafios — **estado isolado** e **dados voláteis** — são exatamente o que vamos resolver nesta aula. Primeiro, aprenderemos a criar um estado global e, em seguida, a torná-lo permanente.

### **1.3. Objetivos da Aula: Implementar um sistema de "Favoritos" global, persistente e utilizando os conceitos de MVVM na prática**

Para superar os desafios do estado isolado e da volatilidade dos dados, vamos construir um sistema de "Favoritos" completo para a nossa Pokédex.

Ao final desta aula, teremos abordado:

  * **Criar um estado global** utilizando a **Context API** do React, permitindo que a lista de favoritos seja acessível e modificável de qualquer tela do nosso aplicativo.
  * **Integrar o AsyncStorage** para salvar os favoritos do usuário no dispositivo, garantindo que a informação não se perca quando o aplicativo for fechado e reaberto.
  * **Aplicar na prática o padrão de arquitetura MVVM**, refatorando nossa lógica de estado em um **Custom Hook** (`useFavorites`). Este hook atuará como nosso *ViewModel*, separando a lógica de negócio da nossa interface (a *View*).

Passemos, então, ao conteúdo! 🦾

-----

Use o índice na barra ao lado para navegar nos tópicos da Aula.
