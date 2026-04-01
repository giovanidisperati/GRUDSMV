---
layout: aula
title: "9. Exercícios"
parent: Aula 04 - Interfaces, Estilização e Listas no React Native
nav_order: 9
---

## 9. Exercícios

Concentre-se nestes 5 desafios para elevar o nível da sua Pokédex, aplicando conceitos importantes de robustez, experiência do usuário e funcionalidade.

**Exercício 1: Robustez na Busca de Dados e Feedback ao Usuário**

Atualmente, a busca inicial de dados não informa ao usuário o que está acontecendo e pode falhar silenciosamente.
* **Sua tarefa:**
    1.  **Indicador de Carregamento:** Na `PokedexScreen`, adicione um estado `isLoading`. Ele deve ser `true` enquanto os dados iniciais são buscados e `false` ao final. Enquanto `isLoading` for `true`, exiba um `ActivityIndicator` ou uma mensagem "Carregando Pokémons...".
    2.  **Tratamento de Erros:** Modifique as funções em `services/api.ts` para usar `try...catch`. Se a busca inicial em `PokedexScreen` falhar, atualize um novo estado de erro e exiba uma mensagem amigável (ex: "Falha ao carregar Pokémons. Verifique sua conexão.").

**Exercício 2: Melhorando a Experiência da Lista e da Busca**

Uma lista vazia ou uma busca sem resultados pode deixar o usuário confuso.
* **Sua tarefa:**
    1.  Na `PokedexScreen`, utilize a propriedade `ListEmptyComponent` da `FlatList`.
    2.  Este componente deve exibir mensagens contextuais:
        * Se a busca (`search`) estiver preenchida e não houver resultados: "Nenhum Pokémon encontrado para '{termo_buscado}'".
        * Se a lista inicial `pokemons` estiver vazia após a tentativa de carregamento (e não estiver mais carregando): "Nenhum Pokémon para exibir no momento."

**Exercício 3: Expandindo a Descoberta com "Carregamento Infinito"**

Limitar a Pokédex a apenas 30 Pokémons restringe a exploração. Vamos permitir que o usuário carregue mais Pokémons conforme rola a tela.
* **Sua tarefa:**
    1.  Adicione um estado para o `offset` (ou página) na `PokedexScreen` e modifique `getPokemons` em `services/api.ts` para aceitar e usar esse `offset`.
    2.  Crie uma função `loadMorePokemons` em `PokedexScreen` que busque a próxima leva de Pokémons e os adicione à lista existente.
    3.  Use a propriedade `onEndReached` da `FlatList` para chamar `loadMorePokemons`.
    4.  **Desafio extra:** Adicione um indicador de carregamento no rodapé da lista (usando `ListFooterComponent`) enquanto mais Pokémons são buscados e evite chamadas múltiplas se uma já estiver em andamento.

**Exercício 4: Refinamento Visual e de Layout**

Pequenos ajustes podem fazer uma grande diferença na apresentação e usabilidade.
* **Sua tarefa:**
    1.  **Capitalização:** Importe e utilize a função `capitalize` de `utils/format.ts` no `PokemonCard.tsx` para exibir o nome de cada Pokémon com a primeira letra maiúscula.
    2.  **Área Segura Dinâmica:** Na `PokedexScreen.tsx`, substitua o `paddingTop: 60` fixo. Utilize o hook `useSafeAreaInsets` de `react-native-safe-area-context` para aplicar um `paddingTop` dinâmico, garantindo que o layout se adapte corretamente a diferentes dispositivos.

**Desafio 1: Aprofundando com uma Tela de Detalhes do Pokémon**

Ver apenas o card é legal, mas que tal uma tela dedicada para cada Pokémon?
* **Sua tarefa:**
    1.  Instale e configure uma biblioteca de navegação (como React Navigation: `@react-navigation/native` e `@react-navigation/stack`).
    2.  Crie uma nova tela `PokemonDetailScreen.tsx`.
    3.  Faça com que, ao pressionar um `PokemonCard` (transforme-o em um `TouchableOpacity`), o aplicativo navegue para `PokemonDetailScreen`, passando o ID ou o objeto do Pokémon selecionado como parâmetro.
    4.  Na `PokemonDetailScreen`, exiba o nome, a imagem em tamanho maior e os tipos do Pokémon recebido.
    5.  **Desafio extra:** Na `PokemonDetailScreen`, busque e exiba informações adicionais como altura, peso e uma breve descrição (a PokeAPI oferece um endpoint para espécies que contém descrições 😉).

# **Bom trabalho! 🔨**s