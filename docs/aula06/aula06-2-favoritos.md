---
layout: aula
title: "3. Implementando Favoritos com Context API"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 2
---

## **3. Implementando o Gerenciamento de Favoritos com a Context API**

Agora que entendemos a teoria por trás da Context API — criar, prover e consumir um estado — é hora de colocar a mão na massa. Vamos implementar nosso sistema de "Favoritos" na Pokédex, aplicando esses conceitos para resolver o problema da comunicação entre telas.

Nosso plano de ação será dividido em três etapas claras:

1.  Primeiro, vamos **criar** nosso contexto. Isso envolverá definir a "forma" dos nossos dados globais (quais informações ele guardará e quais funções ele oferecerá) e inicializar o contexto com `createContext`.
2.  Em seguida, vamos **prover** esse estado para toda a aplicação. Faremos isso criando um componente `Provider` que irá gerenciar o estado dos favoritos e o envolverá em torno da nossa estrutura de navegação no `App.tsx`.
3.  Por fim, vamos **consumir** o estado em nossos componentes. Modificaremos o `PokemonCard` e o `PokemonDetailsScreen` para que eles possam ler a lista de favoritos e chamar as funções para adicionar ou remover um Pokémon, tudo através do hook `useContext`.

### **3.1. Estruturando o Contexto: Definição da Interface e Criação do `FavoritesContext`**

O primeiro passo para criar nosso estado global é definir exatamente o que ele irá conter e quais operações ele permitirá. No nosso caso, queremos gerenciar uma lista de IDs de Pokémons favoritos.

Para manter nosso projeto organizado, vamos criar uma nova pasta `src/contexts` e, dentro dela, um novo arquivo chamado `FavoritesContext.tsx`.

Dentro deste arquivo, a primeira coisa que faremos é definir uma interface TypeScript para o nosso contexto. Essa interface é o "contrato": ela descreve a forma dos dados e das funções que nosso contexto irá fornecer.

```typescript
interface FavoritesContextData {
  favorites: number[]; // Armazenaremos os IDs dos Pokémons favoritos
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}
```

Analisando o contrato, temos:

  * `favorites: number[]`: Uma lista contendo apenas o `id` de cada Pokémon favoritado.
  * `addFavorite`: Uma função para adicionar um novo `id` à lista.
  * `removeFavorite`: Uma função para remover um `id` da lista.
  * `isFavorite`: Uma função auxiliar para verificar rapidamente se um `id` já está na lista, retornando `true` or `false`.

Com o contrato definido, agora podemos criar o contexto em si usando a função `createContext` do React.

```typescript
// src/contexts/FavoritesContext.tsx
import React, { createContext } from 'react';

// 1. Definindo a interface (o contrato)
interface FavoritesContextData {
  favorites: number[];
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}

// 2. Criando o contexto com o contrato e um valor padrão
export const FavoritesContext = createContext<FavoritesContextData>({} as FavoritesContextData);
```

Aqui, passamos nossa interface `FavoritesContextData` como um tipo genérico (`<...>`) para que o `createContext` saiba qual é a forma dos dados. O `({} as FavoritesContextData)` é uma maneira de fornecer um valor inicial que satisfaça o TypeScript, já que o valor real e funcional será injetado pelo `Provider`, que criaremos a seguir. Estamos essencialmente dizendo ao TypeScript: "Confie em mim, quando este contexto for usado, ele terá todas essas propriedades".

Com nosso contexto criado e devidamente tipado, o próximo passo é criar o `Provider`, o componente que irá de fato gerenciar o estado e disponibilizá-lo para o resto da aplicação.

### **3.2. Criando o Provedor: O Componente `FavoritesProvider` e seu estado interno**

Criamos o `FavoritesContext`! No entanto, ele é apenas um "recipiente" vazio, um contrato. Agora, precisamos criar o componente que irá, de fato, gerenciar o estado e "prover" (fornecer) esse estado para a nossa aplicação. Esse componente é o nosso `Provider`.

O `FavoritesProvider` será um componente React normal que fará o seguinte:

1.  Usará o hook `useState` para armazenar a lista de IDs dos Pokémons favoritos.
2.  Implementará a lógica das funções que definimos na interface `FavoritesContextData` (`addFavorite`, `removeFavorite`, `isFavorite`).
3.  Usará o `<FavoritesContext.Provider>` para passar o estado e as funções para seus componentes filhos.

Vamos adicionar este código ao nosso arquivo `src/contexts/FavoritesContext.tsx`, logo abaixo da criação do contexto:

```typescript
// src/contexts/FavoritesContext.tsx
import React, { createContext, useState, ReactNode } from 'react';

// 1. A interface (contrato) que já tínhamos
interface FavoritesContextData {
  favorites: number[];
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}

// 2. O contexto que já tínhamos
export const FavoritesContext = createContext<FavoritesContextData>({} as FavoritesContextData);

// 3. O novo componente Provider
export const FavoritesProvider = ({ children }: { children: ReactNode }) => {
  // Estado que irá armazenar os IDs dos pokémons favoritos
  const [favorites, setFavorites] = useState<number[]>([]);

  // Função para adicionar um favorito, garantindo que não haja duplicados
  const addFavorite = (pokemonId: number) => {
    if (!favorites.includes(pokemonId)) {
      setFavorites(prevFavorites => [...prevFavorites, pokemonId]);
    }
  };

  // Função para remover um favorito
  const removeFavorite = (pokemonId: number) => {
    setFavorites(prevFavorites => prevFavorites.filter(id => id !== pokemonId));
  };

  // Função para checar se um pokémon é favorito
  const isFavorite = (pokemonId: number) => {
    return favorites.includes(pokemonId);
  };

  // Retornamos o Provider do nosso contexto, passando o estado e as funções no 'value'
  return (
    <FavoritesContext.Provider 
      value={{ favorites, addFavorite, removeFavorite, isFavorite }}
    >
      {children}
    </FavoritesContext.Provider>
  );
};
```

Pode parecer que há muitos detalhes nesse código, mas é importante notar que ele segue as práticas mais modernas e recomendadas para o desenvolvimento com React e TypeScript. Toda a lógica está contida em um **componente funcional** e utiliza **hooks**, que é a abordagem padrão no ecossistema React atual por sua clareza e flexibilidade.

Um dos conceitos mais importantes aplicados aqui é a **imutabilidade**. Observe que nunca modificamos o array de favoritos diretamente. Para adicionar um item, usamos `setFavorites(prev => [...prev, pokemonId])`, que cria um **novo array** em vez de mutar o original com `.push()`. Da mesma forma, `.filter()` para remover um item também retorna uma nova cópia. O uso da forma de callback (`prev => ...`) é outra boa prática que garante que nossas atualizações sejam baseadas no estado mais recente, evitando bugs em cenários mais complexos.

A **tipagem forte com TypeScript**, através da interface `FavoritesContextData` e da declaração `useState<number[]>()`, nos dá segurança, autocompletar e um "contrato" explícito do que nosso contexto oferece. Por fim, a estrutura do `FavoritesProvider` que recebe e renderiza `{children}` é o padrão idiomático para criar componentes provedores que podem "abraçar" qualquer parte da nossa aplicação, tornando a ferramenta flexível e desacoplada.

Com nosso `FavoritesProvider` pronto e bem estruturado, o próximo passo é posicioná-lo no topo da nossa árvore de componentes, em `App.tsx`, para que todas as telas tenham acesso a ele.

### **3.3. Disponibilizando o Estado: Integrando o `FavoritesProvider`**

Criamos nosso `FavoritesProvider`, mas, por enquanto, ele não está sendo usado em lugar nenhum. Para que o estado global de favoritos seja acessível por todas as as telas da nossa aplicação, precisamos "envolver" nossa árvore de componentes com ele.

O local ideal para fazer isso é no ponto mais alto da nossa aplicação, que é o arquivo `App.tsx`. Ao posicionar o `Provider` no topo, garantimos que qualquer componente filho — não importa o quão aninhado esteja — possa se conectar e consumir o contexto.

Vamos abrir o arquivo `App.tsx` e fazer duas pequenas alterações:

1.  Importar nosso `FavoritesProvider`.
2.  Envolver o `NavigationContainer` com o `FavoritesProvider`.

O arquivo `App.tsx` ficará assim:

```typescript
// App.tsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SafeAreaProvider } from 'react-native-safe-area-context';

import { PokedexScreen } from './screens/PokedexScreen';
import { PokemonDetailsScreen } from './screens/PokemonDetailsScreen';
import { RootStackParamList } from './types/Navigation';

// Importamos nosso novo Provider
import { FavoritesProvider } from './contexts/FavoritesContext';

const Stack = createNativeStackNavigator<RootStackParamList>();

export default function App() {
  return (
    <SafeAreaProvider>
      {/* O Provider envolve a navegação e todas as telas */}
      <FavoritesProvider>
        <NavigationContainer>
          <Stack.Navigator initialRouteName="Pokedex">
            <Stack.Screen 
              name="Pokedex" 
              component={PokedexScreen} 
              options={{ title: 'Pokédex' }}
            />
            <Stack.Screen 
              name="PokemonDetails" 
              component={PokemonDetailsScreen} 
              options={{ title: 'Detalhes do Pokémon' }} 
            />
          </Stack.Navigator>
        </NavigationContainer>
      </FavoritesProvider>
    </SafeAreaProvider>
  );
}
```

Esta simples alteração no `App.tsx` é, na verdade, um passo arquitetural fundamental, e é importante entender por que essa abordagem segue as melhores práticas modernas. Ao posicionar o `FavoritesProvider` envolvendo o `NavigationContainer`, estamos aplicando o princípio de **centralização do estado**. Isso reforça a **separação de responsabilidades**: nosso `App.tsx` agora atua como uma **raiz de composição**, cujo único trabalho é orquestrar os diferentes provedores que dão superpoderes à nossa aplicação (`SafeAreaProvider` para layout, `FavoritesProvider` para estado, `NavigationContainer` para rotas).

Esse padrão é bastante **escalável**, pois se no futuro precisarmos de um estado global para autenticação ou temas, bastaria adicionar um novo `Provider` nesta mesma estrutura. Como resultado, nossas telas se tornam **desacopladas** da implementação do estado, facilitando a manutenção e os testes.

Com nosso estado agora sendo provido de forma central, a etapa final é aprender a "sintonizar" essa frequência em nossos componentes e, de fato, consumir os dados. É o que faremos a seguir.

### **3.4. Consumindo o Estado: Utilizando o Contexto nos Componentes**

Com nosso `FavoritesProvider` posicionado corretamente no `App.tsx`, o estado global de favoritos já está "no ar". Agora, qualquer componente dentro da nossa aplicação pode "sintonizar" e usar esses dados. Vamos ver como fazer isso na prática, modificando nossos componentes para que eles interajam com a lista de favoritos.

Para manter nosso código limpo e reutilizável, a melhor prática é criar um **hook customizado** que encapsula a chamada `useContext`. Isso torna o consumo do contexto mais simples e legível.

Adicione o seguinte hook ao final do seu arquivo `src/contexts/FavoritesContext.tsx`:

```typescript
// Adicionar ao final de src/contexts/FavoritesContext.tsx
import { useContext } from 'react';
// ... (código anterior do arquivo) ...

// Hook customizado para consumir o contexto de favoritos
export function useFavorites(): FavoritesContextData {
  const context = useContext(FavoritesContext);

  // Garante que o hook só seja usado dentro de um FavoritesProvider
  if (!context) {
    throw new Error('useFavorites deve ser usado dentro de um FavoritesProvider');
  }

  return context;
}
```

Agora, em vez de importar `useContext` e `FavoritesContext` em cada componente, podemos simplesmente importar e usar nosso novo hook `useFavorites`.

#### Modificando o `PokemonCard.tsx`

Vamos alterar nosso card para que ele mostre um ícone de estrela e permita ao usuário favoritar ou desfavoritar um Pokémon.

```typescript
// components/PokemonCard.tsx
import React from 'react';
import { View, Text, Image, StyleSheet, TouchableOpacity } from 'react-native';
import { Pokemon } from '../types/Pokemon';
import { useNavigation } from '@react-navigation/native';
import { NativeStackNavigationProp } from '@react-navigation/native-stack';
import { RootStackParamList } from '../types/Navigation';
import { capitalize } from '../utils/format';
import { useFavorites } from '../contexts/FavoritesContext'; // 1. Importar o hook

type PokemonCardNavigationProp = NativeStackNavigationProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonCard = ({ pokemon }: { pokemon: Pokemon }) => {
  const navigation = useNavigation<PokemonCardNavigationProp>();
  
  // 2. Consumir o contexto de favoritos
  const { addFavorite, removeFavorite, isFavorite } = useFavorites();
  const favorite = isFavorite(pokemon.id);

  const handleToggleFavorite = () => {
    if (favorite) {
      removeFavorite(pokemon.id);
    } else {
      addFavorite(pokemon.id);
    }
  };

  return (
    <TouchableOpacity
      onPress={() => navigation.navigate('PokemonDetails', { pokemonId: pokemon.id })}
      style={styles.touchableCard}
    >
      <View style={[styles.cardInner, favorite && styles.cardFavorite]}>
        {/* Ícone de favorito no topo do card */}
        <TouchableOpacity onPress={handleToggleFavorite} style={styles.favoriteButton}>
          <Text style={styles.favoriteIcon}>{favorite ? '⭐' : '☆'}</Text>
        </TouchableOpacity>
        
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
    backgroundColor: '#f0f0f0',
    padding: 12,
    borderRadius: 12,
    alignItems: 'center',
    flex: 1,
    position: 'relative', // Para posicionar o botão de favorito
  },
  cardFavorite: {
    backgroundColor: '#fffbe6', // Cor de fundo para favoritos
    borderColor: '#facc15',
    borderWidth: 2,
  },
  image: { width: 80, height: 80 },
  name: { marginTop: 8, fontWeight: 'bold' },
  favoriteButton: {
    position: 'absolute',
    top: 8,
    right: 8,
    zIndex: 1,
  },
  favoriteIcon: {
    fontSize: 24,
  },
});
```

#### Modificando o `PokemonDetailsScreen.tsx`

Para demonstrar o poder do estado global, vamos adicionar a mesma funcionalidade à tela de detalhes.

```typescript
// screens/PokemonDetailsScreen.tsx
import React, { useEffect, useState } from 'react';
import { View, Text, Image, StyleSheet, ActivityIndicator, ScrollView, TouchableOpacity } from 'react-native';
import { RouteProp, useRoute } from '@react-navigation/native';
import { RootStackParamList } from '../types/Navigation';
import { Pokemon } from '../types/Pokemon';
import { getPokemonById } from '../services/api';
import { capitalize } from '../utils/format';
import { useFavorites } from '../contexts/FavoritesContext'; // 1. Importar o hook

type PokemonDetailsScreenRouteProp = RouteProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonDetailsScreen = () => {
  const route = useRoute<PokemonDetailsScreenRouteProp>();
  const { pokemonId } = route.params;

  const [pokemon, setPokemon] = useState<Pokemon | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  
  // 2. Consumir o contexto de favoritos
  const { addFavorite, removeFavorite, isFavorite } = useFavorites();
  
  // Verifica se o pokémon atual é um favorito (só executa se 'pokemon' existir)
  const favorite = pokemon ? isFavorite(pokemon.id) : false;

  const handleToggleFavorite = () => {
    if (pokemon) {
      if (favorite) {
        removeFavorite(pokemon.id);
      } else {
        addFavorite(pokemon.id);
      }
    }
  };

  useEffect(() => {
    const fetchDetails = async () => {
      try {
        setIsLoading(true);
        setError(null);
        const details = await getPokemonById(pokemonId);
        setPokemon(details);
      } catch (err) {
        setError('Falha ao carregar detalhes do Pokémon.');
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
      {/* 3. Botão para favoritar */}
      <TouchableOpacity onPress={handleToggleFavorite} style={styles.favoriteButton}>
        <Text style={styles.favoriteIcon}>{favorite ? '⭐' : '☆'}</Text>
      </TouchableOpacity>

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

// 4. Adicionar novos estilos e ajustar os existentes
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
    textAlign: 'center',
    marginTop: 20,
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
    textTransform: 'capitalize',
  },
  favoriteButton: {
    position: 'absolute',
    top: 20,
    right: 20,
    zIndex: 1,
  },
  favoriteIcon: {
    fontSize: 32,
  },
});
```

Pronto! Com essas modificações, os componentes `PokemonCard` e `PokemonDetailsScreen` agora compartilham o mesmo estado de favoritos. Se você favoritar um Pokémon na tela de detalhes, a estrela aparecerá imediatamente na lista da `PokedexScreen` (e vice-versa). O problema do estado isolado está resolvido!

É fundamental notar que a abordagem utilizada para consumir o contexto, através do hook customizado `useFavorites`, representa um padrão moderno e altamente recomendado. Em vez de cada componente chamar `useContext(FavoritesContext)` diretamente, nós criamos essa camada de abstração que torna o código mais profissional e manutenível. Isso desacopla nossos componentes da implementação específica do contexto; se um dia decidirmos trocar a forma como o estado é gerenciado, só precisamos alterar o hook `useFavorites`, e todos os componentes que o utilizam continuarão funcionando sem nenhuma modificação.

Essa prática reforça a **Separação de Responsabilidades (SoC)** e nos alinha com o padrão **MVVM** discutido na aula anterior. Nossos componentes visuais (`PokemonCard`, `PokemonDetailsScreen`) atuam como a *View*: sua única preocupação é exibir a interface de forma **declarativa** (mostrando '⭐' se `favorite` for `true`) e delegar as ações do usuário. O hook `useFavorites` funciona como nosso *ViewModel*: ele encapsula a lógica de negócio e fornece os dados e as funções necessárias para a *View*.
A verificação `if (!context)` dentro do hook é outra prática de desenvolvimento defensivo que previne erros e torna o debugging mais simples.

Portanto, a solução implementada não apenas resolve o problema de compartilhar estado, mas o faz de uma maneira que promove um código mais limpo, desacoplado, testável e fácil de dar manutenção — todos os pilares de uma aplicação React Native moderna e bem-arquiteturada.

-----
