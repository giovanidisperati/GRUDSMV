---
layout: aula
title: "6 & 7. Conclusão e Exercícios"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 5
---

## **6. Conclusão e Próximos Passos**

Nesta aula, demos um passo importante na transição de um protótipo para uma aplicação mais robusta e com melhor experiência de usuário.

Aprendemos a superar as limitações do estado local utilizando a **Context API**, o que nos permitiu criar um estado de "Favoritos" que é compartilhado e consistente em todas as telas da nossa Pokédex. Fomos além, garantindo que as escolhas do usuário não sejam perdidas; com o **AsyncStorage**, implementamos a persistência de dados, fazendo com que a lista de favoritos sobreviva ao fechamento do aplicativo.

E, talvez o mais importante, conectamos a teoria com a prática ao aplicar o padrão de arquitetura **MVVM**. Vimos como um **Hook Customizado** (`useFavorites`) funciona como um *ViewModel*, encapsulando a lógica de negócio e tornando nossos componentes de *View* mais limpos, declarativos e testáveis.

As técnicas que você aprendeu aqui — gerenciamento de estado global, persistência e arquitetura desacoplada — são a base para construir funcionalidades complexas em qualquer aplicativo real, como sistemas de login, carrinhos de compra, configurações de usuário e muito mais!

Para ter acesso ao código implementado nessa aula, acesse o [Branch **Aula06** – PokedexApp](https://github.com/giovanidisperati/PokedexApp/tree/Aula06).

Posteriormente vou disponibilizar uma Branch "Aula06 - MVVM" para mostrar como ficaria a implementação "purista" (seguindo a definição teórica de maneira mais estrita) do MVVM com esse nosso aplicativo React Native.

-----

## **7. Exercícios Práticos**

Essa semana não teremos exercícios, podem aproveitar o feriado 🤩

Lembrando que há atividades do Projeto a serem feitas. Na próxima semana, retornamos com os exercícios!
