---
layout: aula
title: "8. Concluindo..."
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 8
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