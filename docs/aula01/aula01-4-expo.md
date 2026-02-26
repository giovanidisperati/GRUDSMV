---
layout: aula
title: "5. Expo: Facilitador no Desenvolvimento Mobile"
parent: Aula 01 - Panorama, Arquitetura e Setup Mobile
nav_order: 4
---

## 4. React Native - Abrangência e Arquitetura

Desde seu lançamento em 2015, o React Native se tornou uma das tecnologias móveis mais adotadas no mundo. Empresas como **Meta (Facebook), Instagram, Walmart, Uber Eats, Discord, Coinbase e Shopify** utilizam o framework em suas aplicações móveis. Seu apelo está na capacidade de entregar interfaces nativas com um único código JavaScript/TypeScript, o que reduz custos e acelera o desenvolvimento.

Segundo o relatório de 2023 da Stack Overflow, React Native aparece entre os 10 frameworks mais populares entre desenvolvedores. E de acordo com a plataforma de contratação StackShare, mais de 18 mil empresas já registraram seu uso público da tecnologia.

Além disso, a comunidade em torno do React Native é extremamente ativa: há milhares de bibliotecas compatíveis, inúmeros tutoriais, cursos e contribuições constantes da Meta e da comunidade open source. Isso significa que quem aprende React Native encontra não só demanda no mercado, mas também uma base sólida de suporte para evoluir.

Outra grande vantagem do React Native é sua **semelhança conceitual com o React para Web**. Ambos utilizam **JSX** para descrever a interface, **componentes funcionais** como blocos reutilizáveis de UI e **hooks** como `useState` e `useEffect` para controle de estado e efeitos colaterais. Isso significa que quem aprende React Native desenvolve competências altamente transferíveis para projetos web modernos com React.js — e vice-versa. A principal diferença está nos componentes de interface: em vez de `<div>` e `<span>`, usamos `<View>`, `<Text>` e `<ScrollView>`, que são traduzidos internamente para elementos nativos do Android ou iOS.

A arquitetura do React Native é composta por três *threads* principais:

* **JS Thread**: executa o código JavaScript do aplicativo.
* **Shadow Thread**: calcula o layout da interface usando o motor de layout Yoga.
* **Native Thread**: é responsável por desenhar os componentes na tela, utilizando as APIs nativas de cada sistema.

A comunicação entre essas camadas se dava inicialmente por meio da **Bridge**, uma ponte assíncrona baseada em JSON. Contudo, essa abordagem impunha um gargalo na performance. A nova arquitetura do React Native substitui a Bridge pelo **JSI (JavaScript Interface)**, que permite comunicação síncrona e direta entre os threads, melhorando o desempenho e reduzindo a latência nas atualizações de UI.

Além disso, a nova arquitetura incorpora:

* **Hermes**: motor de execução JavaScript otimizado para dispositivos móveis.
* **Fabric**: nova engine de renderização com melhor aproveitamento de atualizações de UI.
* **TurboModules**: módulos nativos carregados sob demanda, otimizando o uso de recursos.

Abaixo um benchmark de performance mostrando a diferença entre a arquitetura antiga e atual do React Native.

| Cenário       | Dispositivo     | Novo (ms) | Antigo (ms) | Melhoria (%) |
|---------------|------------------|-----------|-------------|--------------|
| 1500 Views    | Pixel 4          | 258       | 282         | ~8%          |
| 5000 Views    | Pixel 4          | 1045      | 1088        | ~4%          |
| 1500 Views    | iPhone 12 Pro    | 117       | 137         | ~15%         |
| 5000 Views    | iPhone 12 Pro    | 266       | 435         | ~39%         |


Ou seja, o React Native não apenas oferece uma curva de aprendizagem acessível e uma forte sinergia com o desenvolvimento web moderno, como também já provou sua maturidade técnica e capacidade de atender a uma ampla gama de aplicações. Antes do React Native, frameworks como PhoneGap e Ionic tentaram resolver o desafio do desenvolvimento multiplataforma, mas com resultados menos satisfatórios. 

Por todos os motivos dispostos acima, seguiremos na disciplina com essa escolha tecnológica. E apesar das vantagens do React Native, sua configuração inicial pode ser um desafio. É nesse ponto que o Expo entra como uma solução acessível! ✍️🧑‍💻

---
