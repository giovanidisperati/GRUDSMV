---
layout: aula
title: "3. Implementando Favoritos com Context API"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 3
---

## **3. Implementando o Gerenciamento de Favoritos com a Context API**

Agora que entendemos a teoria por trás da Context API, criar, prover e consumir um estado, é hora de colocarmos esses conhecimentos em prática. Vamos implementar nosso sistema de "Favoritos" na Pokédex, aplicando esses conceitos para resolver o problema da comunicação entre telas.

Nosso plano de ação será dividido em três etapas claras:

1.  Primeiro, vamos **criar** nosso contexto, definindo a "forma" dos nossos dados globais e inicializando o contexto com `createContext`.
2.  Em seguida, vamos **prover** esse estado para toda a aplicação, criando um componente `Provider` que irá gerenciar o estado dos favoritos e envolverá nossa estrutura de navegação no `App.tsx`.
3.  Por fim, vamos **consumir** o estado em nossos componentes. Modificaremos o `PokemonCard` e o `PokemonDetailsScreen` para que possam ler a lista de favoritos e chamar as funções para adicionar ou remover um Pokémon.

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

Analisando o contrato:

  * `favorites: number[]`: uma lista contendo apenas o `id` de cada Pokémon favoritado.
  * `addFavorite`: uma função para adicionar um novo `id` à lista.
  * `removeFavorite`: uma função para remover um `id` da lista.
  * `isFavorite`: uma função auxiliar para verificar rapidamente se um `id` já está na lista.

Com o contrato definido, agora podemos criar o contexto em si. Repare em um detalhe importante na chamada ao `createContext` abaixo:

```typescript
// src/contexts/FavoritesContext.tsx
import React, { createContext, useContext } from 'react';

// 1. Definindo a interface (o contrato)
interface FavoritesContextData {
  favorites: number[];
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}

// 2. Criando o contexto com valor inicial null
//    Usamos null, não um objeto vazio, para que o guard de segurança
//    do useFavorites (seção 3.4) funcione de verdade.
export const FavoritesContext = createContext<FavoritesContextData | null>(null);
```

Por que `null` em vez de `{} as FavoritesContextData`? Porque em JavaScript, um objeto vazio (`{}`) é um valor **truthy**. Se inicializássemos o contexto com `{}`, o guard `if (!context)` que criaremos no hook `useFavorites` nunca seria disparado, mesmo que alguém chamasse o hook fora de um `Provider`. Ao usar `null` como valor padrão, o guard funciona de verdade: se não houver `Provider` acima na árvore, o contexto será `null` e o erro será lançado com a mensagem clara que definirmos.

Com nosso contexto criado e devidamente tipado, o próximo passo é criar o `Provider`.

### **3.2. Criando o Provedor: O Componente `FavoritesProvider` e seu estado interno**

O `FavoritesProvider` será um componente React que irá:

1.  Usar o hook `useState` para armazenar a lista de IDs dos Pokémons favoritos.
2.  Implementar a lógica das funções da interface `FavoritesContextData`.
3.  Usar o `<FavoritesContext.Provider>` para passar o estado e as funções para seus componentes filhos.

Vamos adicionar este código ao arquivo `src/contexts/FavoritesContext.tsx`:

```typescript
// src/contexts/FavoritesContext.tsx
import React, { createContext, useState, useContext, ReactNode } from 'react';

interface FavoritesContextData {
  favorites: number[];
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}

export const FavoritesContext = createContext<FavoritesContextData | null>(null);

export const FavoritesProvider = ({ children }: { children: ReactNode }) => {
  const [favorites, setFavorites] = useState<number[]>([]);

  // Adiciona um favorito, garantindo que não haja duplicados
  const addFavorite = (pokemonId: number) => {
    if (!favorites.includes(pokemonId)) {
      setFavorites(prevFavorites => [...prevFavorites, pokemonId]);
    }
  };

  // Remove um favorito
  const removeFavorite = (pokemonId: number) => {
    setFavorites(prevFavorites => prevFavorites.filter(id => id !== pokemonId));
  };

  // Checa se um pokémon é favorito
  const isFavorite = (pokemonId: number) => {
    return favorites.includes(pokemonId);
  };

  return (
    <FavoritesContext.Provider
      value={{ favorites, addFavorite, removeFavorite, isFavorite }}
    >
      {children}
    </FavoritesContext.Provider>
  );
};
```

Dois conceitos importantes aplicados aqui:

**Imutabilidade:** nunca modificamos o array de favoritos diretamente. Para adicionar um item, usamos `setFavorites(prev => [...prev, pokemonId])`, que cria um **novo array** em vez de mutar o original com `.push()`. Da mesma forma, `.filter()` para remover um item retorna uma nova cópia. O uso da forma de callback (`prev => ...`) garante que nossas atualizações sejam baseadas no estado mais recente, evitando bugs em cenários concorrentes.

**Tipagem forte com TypeScript:** a interface `FavoritesContextData` e a declaração `useState<number[]>()` nos dão segurança, autocompletar e um contrato explícito do que nosso contexto oferece.

### **3.3. Disponibilizando o Estado: Integrando o `FavoritesProvider`**

Para que o estado global de favoritos seja acessível por todas as telas, precisamos "envolver" nossa árvore de componentes com o `FavoritesProvider` no `App.tsx`:

```typescript
// App.tsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SafeAreaProvider } from 'react-native-safe-area-context';

import { PokedexScreen } from './screens/PokedexScreen';
import { PokemonDetailsScreen } from './screens/PokemonDetailsScreen';
import { RootStackParamList } from './types/Navigation';
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

Ao posicionar o `FavoritesProvider` envolvendo o `NavigationContainer`, o `App.tsx` passa a atuar como uma **raiz de composição**: seu único trabalho é orquestrar os diferentes provedores que dão superpoderes à nossa aplicação (`SafeAreaProvider` para layout, `FavoritesProvider` para estado, `NavigationContainer` para rotas). Se no futuro precisarmos de um estado global para autenticação ou temas, bastaria adicionar um novo `Provider` nesta mesma estrutura.

### **3.4. Consumindo o Estado: Utilizando o Contexto nos Componentes**

Com o `FavoritesProvider` posicionado corretamente, o estado global já está "no ar". A melhor prática para consumir um contexto é criar um **hook customizado** que encapsula a chamada `useContext`. Isso evita que cada componente precise importar diretamente tanto o `useContext` quanto o próprio `FavoritesContext`, e, mais importante, garante que o contexto seja usado apenas dentro de um `Provider`.

Adicione o seguinte hook ao final do arquivo `src/contexts/FavoritesContext.tsx`:

```typescript
// Adicionar ao final de src/contexts/FavoritesContext.tsx

export function useFavorites(): FavoritesContextData {
  const context = useContext(FavoritesContext);

  // Como inicializamos o contexto com null (não com {}),
  // este guard funciona de verdade: se alguém chamar useFavorites
  // fora de um FavoritesProvider, o contexto será null e o erro será lançado.
  if (!context) {
    throw new Error('useFavorites deve ser usado dentro de um FavoritesProvider');
  }

  return context;
}
```

#### Modificando o `PokemonCard.tsx`

Vamos alterar nosso card para que mostre uma estrela e permita ao usuário favoritar ou desfavoritar um Pokémon:

```typescript
// components/PokemonCard.tsx
import React from 'react';
import { View, Text, Image, StyleSheet, TouchableOpacity, Pressable } from 'react-native';
import { Pokemon } from '../types/Pokemon';
import { useNavigation } from '@react-navigation/native';
import { NativeStackNavigationProp } from '@react-navigation/native-stack';
import { RootStackParamList } from '../types/Navigation';
import { capitalize } from '../utils/format';
import { useFavorites } from '../contexts/FavoritesContext';

type PokemonCardNavigationProp = NativeStackNavigationProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonCard = ({ pokemon }: { pokemon: Pokemon }) => {
  const navigation = useNavigation<PokemonCardNavigationProp>();
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
        {/*
          Usamos Pressable (não TouchableOpacity) para o botão de favorito
          porque ele está aninhado dentro de outro TouchableOpacity.
          No Android, dois TouchableOpacity aninhados podem ter comportamento
          imprevisível (o toque no ícone pode propagar e acionar também a
          navegação do card pai). O Pressable lida com isso corretamente
          nas duas plataformas.
        */}
        <Pressable
          onPress={handleToggleFavorite}
          style={styles.favoriteButton}
          hitSlop={8}
        >
          <Text style={styles.favoriteIcon}>{favorite ? '⭐' : '☆'}</Text>
        </Pressable>

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
    position: 'relative',
  },
  cardFavorite: {
    backgroundColor: '#fffbe6',
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

Para demonstrar o poder do estado global, adicionamos a mesma funcionalidade à tela de detalhes:

```typescript
// screens/PokemonDetailsScreen.tsx
import React, { useEffect, useState } from 'react';
import {
  View, Text, Image, StyleSheet, ActivityIndicator,
  ScrollView, TouchableOpacity,
} from 'react-native';
import { RouteProp, useRoute } from '@react-navigation/native';
import { RootStackParamList } from '../types/Navigation';
import { Pokemon } from '../types/Pokemon';
import { getPokemonById } from '../services/api';
import { capitalize } from '../utils/format';
import { useFavorites } from '../contexts/FavoritesContext';

type PokemonDetailsScreenRouteProp = RouteProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonDetailsScreen = () => {
  const route = useRoute<PokemonDetailsScreenRouteProp>();
  const { pokemonId } = route.params;

  const [pokemon, setPokemon] = useState<Pokemon | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const { addFavorite, removeFavorite, isFavorite } = useFavorites();
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

const styles = StyleSheet.create({
  container: { padding: 20, alignItems: 'center' },
  centered: { flex: 1, justifyContent: 'center', alignItems: 'center' },
  errorText: {
    flex: 1, textAlign: 'center', marginTop: 20, color: 'red', fontSize: 16,
  },
  image: {
    width: 200, height: 200, marginBottom: 16,
    backgroundColor: '#f0f0f0', borderRadius: 100,
  },
  name: { fontSize: 28, fontWeight: 'bold', marginBottom: 8 },
  idText: { fontSize: 16, color: '#666', marginBottom: 16 },
  typesContainer: { marginBottom: 16, alignItems: 'center' },
  sectionTitle: { fontSize: 20, fontWeight: '600', marginBottom: 8 },
  typeText: {
    fontSize: 16, marginHorizontal: 4, paddingVertical: 4, paddingHorizontal: 8,
    backgroundColor: '#ddd', borderRadius: 4, marginBottom: 4, textTransform: 'capitalize',
  },
  favoriteButton: { position: 'absolute', top: 20, right: 20, zIndex: 1 },
  favoriteIcon: { fontSize: 32 },
});
```

Pronto! Com essas modificações, `PokemonCard` e `PokemonDetailsScreen` compartilham o mesmo estado de favoritos. Se você favoritar um Pokémon na tela de detalhes, a estrela aparecerá imediatamente na lista da `PokedexScreen`, e vice-versa. O problema do estado isolado está resolvido.

A abordagem de consumir o contexto através do hook customizado `useFavorites` desacopla nossos componentes da implementação específica do contexto. Se um dia decidirmos trocar a forma como o estado é gerenciado, só precisamos alterar o hook! Todos os componentes que o utilizam continuarão funcionando sem modificação. Isso reforça a **Separação de Responsabilidades** e nos alinha com o padrão **MVVM**: nossos componentes visuais atuam como a *View* (exibem a interface de forma declarativa e delegam ações) e o `useFavorites` funciona como o *ViewModel* (encapsula a lógica de negócio).