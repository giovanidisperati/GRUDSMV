---
layout: aula
title: "3. Modularização e Boas Práticas"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 3
---

## **3. Modularização e Boas Práticas**

Conforme o projeto cresce, manter uma estrutura organizada é importante para a manutenibilidade e escalabilidade. Já iniciamos essa organização por meio de diretórios, mas é necessário entender como avançar nesse sentido.

Podemos citar como boas práticas: a **Separação de Componentes**, o uso de uma **Camada de Serviço**, a definição de **Funções Utilitárias** e definição de **Tipos Globais**.

Nesse sentido, à medida que a aplicação cresce, precisamos reforçar a organização para garantir manutenção e escala. Em nossa implementação, continuamos separando **componentes de interface reutilizáveis** (como o `PokemonCard`) dentro de `src/components`, enquanto **telas completas** — `PokedexScreen`, `PokemonDetailsScreen` — permanecem em `src/screens`. Se uma tela ficar muito carregada, podemos dividi-la em subcomponentes, que podem "morar" numa subpasta própria dessa tela (exemplo: `src/screens/Pokedex/components`) ou, se forem realmente genéricos, subir para `src/components`. Um bom exemplo seria transformar a seção de detalhes do Pokémon em um `PokemonDetailsView` independente!

Além disso, nossa comunicação com serviços externos continua centralizada em **`services/api.ts`**. Sempre que precisarmos de novos dados, criamos ali funções que devolvem `Promise` com tipos definidos — por exemplo `Promise<Pokemon[]>` ou `Promise<PokemonDetailed>` — o que impede surpresas em tempo de execução. Funções utilitárias puras, como `capitalize`, ficam em **`utils/format.ts`**; esse arquivo também é o melhor lugar para futuras formatações de datas, números ou outras strings.

Por fim, todo o contrato de tipos continua em **`src/types`**. Lá já temos `Navigation.ts`, que descreve rotas e parâmetros; qualquer novo modelo global, como um futuro `PokemonDetailed`, deve ser criado na mesma pasta para manter o autocompletar e as verificações de tempo de compilação funcionando em todo o projeto.

Seguir essa divisão — componentes reaproveitáveis, telas, serviços, utilitários e tipos globais — garante um código modular, facilita o trabalho em equipe e torna as próximas funcionalidades mais fáceis de integrar. Isso contudo, diz respeito à decisões de "baixo nível", ao Design concreto da nossa aplicação. Vamos ver agora como proceder em relação às decisões de alto nível: a arquitetura de nossa aplicação!

---
