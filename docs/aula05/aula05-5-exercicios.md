---
layout: aula
title: "5. Conclusão e Exercícios"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 5
---

## 5. Conclusão

Nesta aula, nossa Pokédex deu um salto de qualidade, evoluindo de uma aplicação de tela única para um sistema de navegação interativo. A implementação da navegação com `react-navigation` não foi apenas um exercício técnico; foi o catalisador que nos forçou a pensar sobre como gerenciar a complexidade à medida que um aplicativo cresce.

Partimos da necessidade de exibir detalhes de um Pokémon e, no processo, aprendemos a:
* **Estruturar a navegação em pilha**, um dos fluxos mais comuns em aplicativos móveis.
* **Tipar nossas rotas e parâmetros** com `RootStackParamList`, garantindo segurança e previsibilidade ao passar dados entre telas.
* **Transformar componentes estáticos em interativos**, fazendo com que o `PokemonCard` se tornasse um ponto de entrada para uma nova experiência.
* **Organizar a lógica de busca de dados**, expandindo nossa camada de serviço (`api.ts`) para lidar com novas requisições de forma limpa e isolada.

Também tivemos uma introdução aos **padrões de arquitetura**, ferramentas conceituais para guiar nossas decisões, e entendemos que a escolha não é sobre encontrar a "bala de prata", mas sim sobre aplicar princípios sólidos de **separação de responsabilidades**, **testabilidade** e **manutenibilidade** para construir software robusto e manutenível.

Com essa base arquitetural e uma aplicação que já navega e busca dados de forma organizada, estamos preparados para os próximos desafios — seja gerenciando um **estado global** (como uma lista de Pokémons favoritos que funcione em todo o app), otimizando o desempenho com interações mais avançadas ou **persistindo dados localmente** para uso offline. As fundações que construímos aqui serão essenciais (Já sabe o que vamos ver nas próximas aulas, né? 🤩)

E, como já sabem, agora é hora dos nossos...

## Exercícios

### **Exercício 1: Análise Crítica da Arquitetura Atual**

Avalie a estrutura de código existente da Pokédex, de forma a identificar pontos fortes e fracos, e praticar a habilidade de ler e analisar criticamente uma base de código. Caso você não tenha conseguido fazer os exercícios da Aula04, faça o checkout para o branch `Aula05` do projeto da Pokédex, disponível em [Repositório no GitHub – PokedexApp](https://github.com/giovanidisperati/PokedexApp/), que contém a implementação completa da navegação e dos exercícios da aula anterior.

Dedique um tempo para navegar por todos os arquivos e diretórios (`screens`, `components`, `services`, `types`, `utils`). Após analisar o projeto, crie um documento de texto ou markdown chamado `ANALISE_ARQUITETURA.md` e responda às seguintes questões de forma detalhada:

1. **Estrutura de Diretórios:** A organização atual dos arquivos em `screens`, `components`, `services`, etc., é clara para você? Você mudaria algum arquivo de lugar? Por quê?

2. **Componentização:** O `PokemonCard` é um bom exemplo de componente reutilizável? Analise a tela `PokemonDetailsScreen`. Que partes dela você extrairia para um novo componente reutilizável para manter a tela mais limpa?

3. **Gerenciamento de Estado e Lógica:**
    * Observe a `PokedexScreen`. Onde a lógica de busca e filtragem de dados está localizada?
    * Observe a `PokemonDetailsScreen`. Onde está a lógica para buscar os detalhes de um Pokémon específico?
    * Você considera essa abordagem (lógica de estado e de dados dentro dos componentes de tela) sustentável para um aplicativo que continua crescendo? Quais são os prós e contras que você observa?

4. **Pontos Fortes e Fracos:** Baseado em sua análise, liste pelo menos **dois pontos fortes** (o que foi bem feito) e **dois pontos fracos** (o que poderia ser melhorado) na arquitetura atual da aplicação. Justifique cada ponto.

**Caso você tenha conseguido fazer a implementação dos exercícios**, faça essa análise crítica com base no repositório que você submeteu como entrega da Aula04.

### **Exercício 2: Proposta de Refatoração para MVP ou MVVM**

Aqui a ideia é aplicar os conhecimentos teóricos sobre os padrões de arquitetura MVP e MVVM, planejando uma refatoração estrutural da Pokédex sem, necessariamente, implementá-la por completo.

Para isso, escolha **um** dos padrões de arquitetura a seguir para a Pokédex: **MVP** ou **MVVM**. Com base na sua escolha, crie um documento de texto ou markdown chamado `PROPOSTA_REFATORACAO.md` e descreva como você reestruturaria a tela `PokedexScreen`. Sua proposta deve incluir:

1. **Padrão Escolhido:** Declare qual padrão você escolheu (MVP ou MVVM) e justifique brevemente por que o considera uma boa opção para este aplicativo.

2. **Nova Estrutura de Arquivos:** Desenhe a nova estrutura de diretórios para a tela da Pokédex. Mostre onde os novos arquivos (como `PokedexPresenter.ts` ou `usePokedexViewModel.ts`) estariam localizados.

*Por exemplo:*
```
PokedexApp/
├─ screens/
│  └─ Pokedex/
│     ├─ PokedexScreen.tsx      (View)
│     ├─ PokedexPresenter.ts    (Presenter)
│     └─ IPokedexView.ts        (Contrato/Interface)
...
```

3. **Divisão de Responsabilidades:** Para a tela `PokedexScreen`, descreva claramente:
    * **(Se MVP) O que ficaria na `View` (`PokedexScreen.tsx`)?** Quais métodos o `Presenter` chamaria nela (defina a interface `IPokedexView`)?
    * **(Se MVP) O que ficaria no `Presenter`?** Quais lógicas (busca de dados, filtro, formatação) ele conteria?
    * **(Se MVVM) O que ficaria na `View` (`PokedexScreen.tsx`)?** Como ela consumiria o `ViewModel`?
    * **(Se MVVM) O que ficaria no `ViewModel` (ex: `usePokedexViewModel`)?** Quais estados (`list`, `isLoading`) e funções (`setSearchQuery`) ele exporia?

4. **Fluxo de Dados:** Descreva em texto ou com um diagrama simples o fluxo de uma interação do usuário. Por exemplo: "O que acontece passo a passo quando o usuário digita no campo de busca na nova arquitetura?".

*Exemplo de passo a passo para MVVM:*
> 1. Usuário digita na `TextInput` da `View`.
> 2. O `onChangeText` da `View` chama a função `setSearchQuery` exposta pelo `ViewModel`.
> 3. Dentro do `ViewModel`, a mudança no estado de busca dispara um `useEffect` que...
> 4. ... etc.

Este exercício não exige a implementação do código, mas sim o planejamento e a documentação da arquitetura, que é uma habilidade importante para qualquer desenvolvedor. 🧠

> "Pô, professor, pra que eu preciso saber disso?"
> Eu já disse: as inteligências artificiais programam cada vez melhor. O nosso diferencial está em analisar criticamente as decisões de design e arquitetura, entendendo "o que" deve ser feito e "por quê". Saber apenas "como" implementar não basta, por isso precisamos exercitar o raciocínio crítico desde cedo!

# **Bom trabalho! 🔨**