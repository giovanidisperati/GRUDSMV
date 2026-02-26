---
layout: aula
title: "3 & 4. React Native – Escolha e Arquitetura"
parent: Aula 01 - Panorama, Arquitetura e Setup Mobile
nav_order: 3
---

## 3. E quais tecnologias iremos utilizar?

Diante desse panorama, optaremos na disciplina pela adoção da abordagem híbrida. O uso de frameworks como o React Native, aliado ao Expo, permite que a construção de aplicações móveis completas, com acesso a recursos nativos e experiência real de desenvolvimento multiplataforma, sem a complexidade inerente ao desenvolvimento nativo ou as limitações das aplicações web. 😊

Além disso, essa escolha viabiliza um aprendizado mais fluido, com menor barreira de entrada e maior foco na lógica da aplicação, na integração com APIs e na experiência do usuário — competências centrais para o desenvolvimento de soluções móveis em contextos educacionais e profissionais. Essa abordagem, portanto, representa um equilíbrio eficaz entre profundidade técnica, aplicabilidade prática e acessibilidade didática.

React Native é um framework de código aberto lançado pelo Facebook (Meta) em 2015, criado para permitir o desenvolvimento de aplicações móveis utilizando **JavaScript** ou **TypeScript**, com base na mesma lógica de componentes usada no React para web.

A filosofia do React Native é abstrair a camada de visualização (UI), permitindo que o desenvolvedor escreva componentes React, os quais são então renderizados como elementos nativos: por exemplo, o componente `<Button />` é convertido para `UIButton` no iOS e `Button` no Android. Essa conversão é feita por uma ponte de comunicação interna entre o JavaScript e o código nativo.

---
