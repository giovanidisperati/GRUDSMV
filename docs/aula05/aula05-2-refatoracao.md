---
layout: aula
title: "2. Refatoração da Pokédex com Navegação"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 2
---

## **2. Refatoração da Pokédex com Navegação**

Com a infraestrutura de navegação devidamente configurada no aplicativo, o passo subsequente é possibilar que o usuário transite da tela de listagem de Pokémons (`PokedexScreen`) para uma tela de visualização detalhada (`PokemonDetailsScreen`). Essa interação será disparada pelo toque em um dos cards de Pokémon exibidos na lista.

### **2.1 Tornando os Cards Clicáveis e Implementando a Navegação**

Para alcançar a interatividade desejada, o componente `PokemonCard.tsx` será modificado. O objetivo é que cada card se torne uma área "tocável" que, ao ser pressionada, acione o mecanismo de navegação para a tela de detalhes do Pokémon correspondente.

Primeiramente, é necessário importar os artefatos essenciais do React Navigation e outros módulos. O hook `useNavigation` é fundamental, pois ele provê acesso ao objeto de navegação da pilha (stack) corrente. Adicionalmente, para garantir a segurança de tipos e o auxílio do TypeScript durante o desenvolvimento, importamos `NativeStackNavigationProp` e a nossa definição customizada de tipos de rota, `RootStackParamList`. O `TouchableOpacity` do React Native é importado para envolver o card, conferindo-lhe a capacidade de resposta ao toque com um feedback visual de opacidade. Finalmente, a função utilitária `capitalize`, desenvolvida anteriormente, é importada para formatar o nome do Pokémon.

```tsx
// components/PokemonCard.tsx
import React from 'react';
import { View, Text, Image, StyleSheet, TouchableOpacity } from 'react-native'; 
import { Pokemon } from '../types/Pokemon'; 
import { useNavigation } from '@react-navigation/native';
import { NativeStackNavigationProp } from '@react-navigation/native-stack';
import { RootStackParamList } from '../types/Navigation';
import { capitalize } from '../utils/format'; 

interface Props {
  pokemon: Pokemon; 
}

type PokemonCardNavigationProp = NativeStackNavigationProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonCard = ({ pokemon }: Props) => {
  const navigation = useNavigation<PokemonCardNavigationProp>();

  const handlePress = () => {
    navigation.navigate('PokemonDetails', { pokemonId: pokemon.id });
  };

  return (
    <TouchableOpacity onPress={handlePress} style={styles.touchableCard}>
      {/* View interna que mantém a estrutura e estilização visual original do card. */}
      <View style={styles.cardInner}>
        <Image source={{ uri: pokemon.image }} style={styles.image} />
        <Text style={styles.name}>{capitalize(pokemon.name)}</Text>
      </View>
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  touchableCard: {
    flex: 1,
    margin: 8,
  },
  cardInner: {
    backgroundColor: '#e0e0e0',
    padding: 12,
    borderRadius: 12,
    alignItems: 'center',
    flex: 1,
  },
  image: { width: 80, height: 80 }, 
  name: { marginTop: 8, fontWeight: 'bold' }, 
});
```

Analisando as **mudanças principais** de forma mais aprofundada, temos as alterações descritas abaixo.

Primeiro, fizemos a importação de `TouchableOpacity` de `react-native`. Lembre-se que falamos sobre esse componente na aula anterior. 🤓

Também fizemos uso do hook `useNavigation`, que é invocado dentro do componente funcional `PokemonCard`. Este hook, fornecido pela biblioteca React Navigation, retorna o objeto `navigation` associado ao navegador mais próximo na árvore de componentes. Este objeto é a interface primária para disparar ações de navegação, como ir para uma nova tela ou voltar. Essa é uma novidade que estamos colocando na aplicação e seu uso vai ficar mais claro e intuitivo conforme prosseguirmos com seu uso na disciplina.

A tipagem do objeto `navigation` é realizada através de `NativeStackNavigationProp<RootStackParamList, 'PokemonDetails'>`. Esta é uma prática recomendada para garantir a segurança de tipo. `RootStackParamList` é um mapa que define todas as rotas da nossa pilha de navegação e os parâmetros que cada rota espera receber (como vimos na seção acima!). Já ao especificar `'PokemonDetails'` como o segundo argumento genérico, informamos ao TypeScript que, ao usar `navigation.navigate` para a rota `'PokemonDetails'`, esperamos (e o autocompletar sugerirá) os parâmetros definidos para `'PokemonDetails'` em `RootStackParamList`.

Já a função `handlePress` é definida para encapsular a lógica de navegação. Quando chamada, ela executa `navigation.navigate('PokemonDetails', { pokemonId: pokemon.id })`, sendo que:
  * O primeiro argumento, `'PokemonDetails'`, é o nome da rota de destino, que deve corresponder exatamente a um dos nomes de tela (`Screen`) configurados no `StackNavigator`.

  * O segundo argumento, `{ pokemonId: pokemon.id }`, é um objeto que representa os parâmetros passados para a tela de destino. A tela `PokemonDetailsScreen` poderá então acessar `pokemonId` através de suas `route.params` para buscar e exibir as informações detalhadas do Pokémon selecionado, possibilitando a implementação do Desafio!

Estruturalmente, o `View` que anteriormente representava a totalidade do card agora é envolvido por um `TouchableOpacity`. Para que a área tocável corresponda visualmente ao card e para que os estilos sejam aplicados corretamente, alguns ajustes foram feitos:
    * O `TouchableOpacity` (`touchableCard`) agora assume a responsabilidade pelo `flex: 1` e pela `margin`. O `flex: 1` é importante se o `PokemonCard` for renderizado dentro de um contêiner flexível (como uma `FlatList` com `numColumns > 1`), permitindo que o card se expanda para ocupar o espaço alocado. A `margin` provê o espaçamento entre os cards. Basicamente, lógica de estilização para que nosso aplicativo não quebre!
    * Já o `View` interno (`cardInner`) retém os estilos visuais como `backgroundColor`, `padding`, `borderRadius` e `alignItems`. É importante notar que `cardInner` também recebe `flex: 1`, o que garante que o conteúdo visual (o `View` interno) se expanda para preencher completamente as dimensões do `TouchableOpacity` pai, assegurando que toda a área visual do card seja, de fato, tocável e responda à interação do usuário.

Com estas modificações, cada `PokemonCard` na lista não apenas exibe informações resumidas, mas também atua como um ponto de entrada interativo para uma visualização mais detalhada, melhorando a usabilidade da Pokédex e nos permitindo trabalhar com cards interativos.

Agora, claro, temos que criar nossa `PokemonDetailsScreen` para poder de fato ver as informações dos Pokémons.

### **2.2 Criando a Tela de Detalhes (`PokemonDetailsScreen.tsx`)**

Agora, vamos criar o arquivo `screens/PokemonDetailsScreen.tsx`. Esta tela receberá o `pokemonId` como parâmetro, buscará os dados detalhados do Pokémon (poderíamos expandir o tipo `Pokemon` ou criar um `PokemonDetail` com mais campos em uma melhoria futura!) e os exibirá.

Sabemos que a `PokeAPI` pode nos dar mais detalhes com base no ID, então vamos refinar nossas funções em `services/api.ts` para buscar detalhes por ID.

Primeiro, atualizamos `services/api.ts`:

```typescript
// services/api.ts
import axios from 'axios';
import { Pokemon, PokemonListItem } from '../types/Pokemon'; //

const API_BASE = 'https://pokeapi.co/api/v2'; //

export async function getPokemons(limit: number): Promise<PokemonListItem[]> {
  const res = await axios.get(`${API_BASE}/pokemon?limit=${limit}`);
  return res.data.results;
}

export async function getPokemonDetails(url: string): Promise<Pokemon> {
  const res = await axios.get(url);
  return {
    id: res.data.id,
    name: res.data.name,
    image: res.data.sprites.front_default,
    types: res.data.types.map((t: any) => t.type.name),
  };
}

export async function getPokemonById(id: number): Promise<Pokemon> { 
  const res = await axios.get(`${API_BASE}/pokemon/${id}`);
  return {
    id: res.data.id,
    name: res.data.name,
    image: res.data.sprites.front_default,
    types: res.data.types.map((t: any) => t.type.name),
    // Aqui poderíamos buscar e mostrar dados opcionais, customizando nosso tipo Pokemon. 
    // Por exemplo, poderíamos buscar:
    // height: res.data.height,
    // weight: res.data.weight,
    // abilities: res.data.abilities.map((a: any) => a.ability.name),
  };
}
```

Agora sim, implementamos nossa `PokemonDetailsScreen.tsx`:

```tsx
// screens/PokemonDetailsScreen.tsx
import React, { useEffect, useState } from 'react';
import { View, Text, Image, StyleSheet, ActivityIndicator, ScrollView } from 'react-native';
import { RouteProp, useRoute } from '@react-navigation/native';
import { RootStackParamList } from '../types/Navigation';
import { Pokemon } from '../types/Pokemon'; 
import { getPokemonById } from '../services/api';
import { capitalize } from '../utils/format'; //

// Tipo para a propriedade route da nossa tela específica
type PokemonDetailsScreenRouteProp = RouteProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonDetailsScreen = () => {
  const route = useRoute<PokemonDetailsScreenRouteProp>();
  const { pokemonId } = route.params; 

  const [pokemon, setPokemon] = useState<Pokemon | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchDetails = async () => {
      try {
        setIsLoading(true);
        setError(null);
        const details = await getPokemonById(pokemonId);
        setPokemon(details);
      } catch (err) {
        setError('Falha ao carregar detalhes do Pokémon.');
        console.error(err);
      } finally {
        setIsLoading(false);
      }
    };

    fetchDetails();
  }, [pokemonId]);

  if (isLoading) {
    return <ActivityIndicator size="large" style={styles.centered} />;
  }

  if (error) {
    return <Text style={styles.errorText}>{error}</Text>;
  }

  if (!pokemon) {
    return <Text style={styles.centered}>Pokémon não encontrado.</Text>;
  }

  return (
    <ScrollView contentContainerStyle={styles.container}>
      <Image source={{ uri: pokemon.image }} style={styles.image} />
      <Text style={styles.name}>{capitalize(pokemon.name)}</Text>
      <Text style={styles.idText}>ID: #{pokemon.id}</Text>
      
      <View style={styles.typesContainer}>
        <Text style={styles.sectionTitle}>Tipos:</Text>
        {pokemon.types.map((type) => (
          <Text key={type} style={styles.typeText}>{capitalize(type)}</Text>
        ))}
      </View>
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  container: {
    padding: 20,
    alignItems: 'center',
  },
  centered: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  errorText: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    color: 'red',
    fontSize: 16,
  },
  image: {
    width: 200,
    height: 200,
    marginBottom: 16,
    backgroundColor: '#f0f0f0', 
    borderRadius: 100,
  },
  name: {
    fontSize: 28,
    fontWeight: 'bold',
    marginBottom: 8,
  },
  idText: {
    fontSize: 16,
    color: '#666',
    marginBottom: 16,
  },
  typesContainer: {
    marginBottom: 16,
    alignItems: 'center',
  },
  sectionTitle: {
    fontSize: 20,
    fontWeight: '600',
    marginBottom: 8,
  },
  typeText: { 
    fontSize: 16,
    marginHorizontal: 4,
    paddingVertical: 4,
    paddingHorizontal: 8,
    backgroundColor: '#ddd',
    borderRadius: 4,
    marginBottom: 4,
  },
  detailsSection: {
    marginTop: 16,
    alignItems: 'flex-start',
    width: '100%',
  }
});
```

Nessa implementação nós:

* Usamos `useRoute<PokemonDetailsScreenRouteProp>()` para acessar os parâmetros passados para esta tela.
* `route.params.pokemonId` nos dá o ID do Pokémon.
* O `useEffect` busca os detalhes do Pokémon usando `getPokemonById` quando o componente é montado ou o `pokemonId` muda.
* Incluímos estados para `isLoading` e `error` para uma melhor experiência do usuário.
* Renderizamos os detalhes básicos do Pokémon. O `ScrollView` é usado caso o conteúdo exceda a altura da tela.

Agora, ao tocar em um Pokémon na `PokedexScreen`, o usuário será levado para `PokemonDetailsScreen`, que exibirá mais informações sobre o Pokémon selecionado! ✨

Abaixo a demonstração de como está nossa Pokédex nesse estágio de implementação:

![Demonstração do app no início da Aula 05](images/pokedex_inicio_aula05.gif)

Repare que **ainda precisamos terminar a implementação das funcionalidades pedidas na Aula 04.** Você conseguiu implementá-las? 🤓

O mais difícil era o Desafio que acabamos de implementar! Então caso não tenha conseguido, tente, agora a partir do código fornecido acima, fazer as implementações das melhorias pedidas nos exercícios 1 a 4 da aula anterior. Irei gravar um vídeo que será disponibilizado no Moodle mostrando a implementação completa (e o código-fonte será disponibilizado), mas tente fazer os exercícios pois a prática é o que leva ao entendimento concreto do que estamos fazendo!

Dito isso, vamos prosseguir para um tema importante que ainda não abordamos de maneira suficiente até aqui: arquitetura e modularização de nossas aplicações mobile.
