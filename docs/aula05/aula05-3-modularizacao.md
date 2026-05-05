---
layout: aula
title: "3. Modularização e Boas Práticas"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 3
---

## **3. Modularização e Boas Práticas**

Quando um projeto nasce, ele raramente nasce grande. O problema é que ele cresce. E, na maioria das vezes, cresce mais rápido do que a estrutura que o sustenta foi pensada para suportar. A questão não é *se* o projeto vai ficar complexo, mas *se* a sua organização vai sobreviver a essa complexidade com dignidade.

Nas últimas aulas, já tomamos decisões importantes nessa direção ao separar componentes de UI, telas, serviços e tipos em diretórios distintos. Mas separar em pastas é condição necessária, não suficiente. O que garante a saúde de longo prazo de um projeto é a disciplina de aplicar, dentro de cada pasta e em cada arquivo, um conjunto de práticas que tornam o código modular, coeso, fracamente acoplado e testável, independentemente do seu tamanho.

Podemos citar como boas práticas: a **Separação de Componentes**, o uso de uma **Camada de Serviço**, a definição de **Funções Utilitárias** e a definição de **Tipos Globais**.

Em nossa implementação, continuamos separando **componentes de interface reutilizáveis** (como o `PokemonCard`) dentro de `src/components`, enquanto **telas completas**, `PokedexScreen`, `PokemonDetailsScreen`, permanecem em `src/screens`. Se uma tela ficar muito carregada, podemos dividi-la em subcomponentes, que podem "morar" numa subpasta própria dessa tela (exemplo: `src/screens/Pokedex/components`) ou, se forem realmente genéricos, subir para `src/components`. Um bom exemplo seria transformar a seção de detalhes do Pokémon em um `PokemonDetailsView` independente.

Para tornar isso concreto, a estrutura de diretórios que adotamos na Pokédex até o momento se parece com isso:

```
PokedexApp/
├── App.tsx                           # Ponto de entrada: configuração de navegação
├── src/
│   ├── components/                   # Componentes de UI reutilizáveis entre telas
│   │   └── PokemonCard.tsx           # Exibe um Pokémon; não sabe de onde vieram os dados
│   ├── screens/                      # Telas completas (cada uma = uma rota)
│   │   ├── PokedexScreen.tsx         # Lista de Pokémons + campo de busca
│   │   └── PokemonDetailsScreen.tsx  # Detalhes de um Pokémon específico
│   ├── services/                     # Comunicação com APIs externas
│   │   └── api.ts                    # Funções que retornam Promise<T> com tipos definidos
│   ├── types/                        # Contratos de dados globais (interfaces e tipos)
│   │   ├── Navigation.ts             # RootStackParamList — rotas tipadas
│   │   └── Pokemon.ts                # Tipos Pokemon e PokemonDetailed
│   └── utils/                        # Funções puras sem dependência de framework
│       └── format.ts                 # capitalize, formatDate, etc.
```

Cada camada tem uma responsabilidade clara e uma **regra de dependência implícita**: `components` e `screens` podem usar `services`, `types` e `utils`, mas o inverso jamais deve ocorrer: um serviço nunca deve importar um componente de tela. Quando essa regra é violada, o acoplamento começa a corroer a estrutura.

Além disso, nossa comunicação com serviços externos continua centralizada em `services/api.ts`. Sempre que precisarmos de novos dados, criamos ali funções que devolvem `Promise` com tipos definidos, por exemplo `Promise<Pokemon[]>` ou `Promise<PokemonDetailed>`, o que impede surpresas em tempo de execução. Funções utilitárias puras, como `capitalize`, ficam em `utils/format.ts`. Esse arquivo também é o melhor lugar para futuras formatações de datas, números ou outras strings.

Por fim, todo o contrato de tipos continua em `src/types`. Lá já temos `Navigation.ts`, que descreve rotas e parâmetros. Assim, qualquer novo modelo global, como um futuro `PokemonDetailed`, deve ser criado na mesma pasta para manter o autocompletar e as verificações de tempo de compilação funcionando em todo o projeto.

Seguir essa divisão, com componentes reaproveitáveis, telas, serviços, utilitários e tipos globais, garante um código modular, facilita o trabalho em equipe e torna as próximas funcionalidades mais fáceis de integrar. Isso, contudo, diz respeito mais às decisões de "baixo nível": o design concreto e localizado de cada peça da aplicação. Essas decisões de design e as decisões de arquitetura não são camadas separadas e independentes, na verdade elas se influenciam mutuamente. A organização de pastas que acabamos de ver é, em grande medida, uma *consequência* de decisões arquiteturais mais profundas: quem pode depender de quem, onde vivem as regras de negócio, quem tem autoridade para mudar o estado da tela. É exatamente sobre essas decisões mais fundamentais que falaremos a seguir.