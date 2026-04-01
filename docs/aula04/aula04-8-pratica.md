---
layout: aula
title: "8. Mãos à obra!"
parent: Aula 04 - Interfaces, Estilização e Listas no React Native
nav_order: 8
---

## 8. Mãos à obra!

Chegamos ao momento de consolidar todo o conhecimento adquirido nesta aula sobre a construção de interfaces visuais com React Native! Depois de explorarmos os fundamentos da UI, desde a estrutura com `View` e `Text`, passando pela estilização com `StyleSheet` e o layout com Flexbox, até a criação de componentes reutilizáveis com `props` e a gestão da interatividade com inputs e eventos, é hora de aplicar esses conceitos em um projeto prático.

Nesta seção, colocaremos a mão na massa para desenvolver uma versão mobile (um pouquinho diferente) da nossa "Pokedex", que já exploramos nas aulas anteriores. Este projeto prático não só revisitará os conceitos da Aula 03 sobre React, como também integrará de forma coesa os componentes visuais, a busca de dados de uma API externa, a tipagem com TypeScript e a organização de um projeto mobile que vimos ao longo desta aula.

Nessa versão da PokeDex a ideia é mostrar uma lista de pokemóns como cards e fazer a filtragem através de um input de texto. Vamos lá!

### 📁 Estrutura do Projeto

Vamos estruturar nosso projeto da seguinte forma:

```
PokedexApp/
├── App.tsx
├── assets/
├── components/
│   └── PokemonCard.tsx
├── screens/
│   └── PokedexScreen.tsx
├── services/
│   └── api.ts
├── types/
│   └── Pokemon.ts
├── utils/
│   └── format.ts
└── tsconfig.json
```

Esta organização promove a modularidade e facilita a manutenção e escalabilidade do projeto e esta é a estrutura de pastas sugerida para o seu aplicativo Pokedex.

Ela organiza o código de forma lógica:

* **`App.tsx`**: Ponto de entrada principal do aplicativo.
* **`assets/`**: Para armazenar imagens estáticas, fontes, etc. (Atualmente vazia, mas é uma boa prática tê-la).
* **`components/`**: Contém componentes reutilizáveis da interface do usuário (UI).
    * **`PokemonCard.tsx`**: Componente para exibir as informações de um único Pokémon.
* **`screens/`**: Contém os componentes que representam as diferentes telas do aplicativo.
    * **`PokedexScreen.tsx`**: A tela principal que exibirá a lista de Pokémons.
* **`services/`**: Módulos responsáveis pela comunicação com APIs externas ou serviços.
    * **`api.ts`**: Contém a lógica para buscar dados da PokeAPI.
* **`types/`**: Definições de tipos TypeScript para garantir a consistência dos dados.
    * **`Pokemon.ts`**: Define as interfaces para os dados dos Pokémons.
* **`utils/`**: Funções utilitárias que podem ser usadas em várias partes do aplicativo.
    * **`format.ts`**: Exemplo de um utilitário para formatação de strings.
* **`tsconfig.json`**: Arquivo de configuração do TypeScript para o projeto.

### ✅ Como criar o projeto

Para iniciar, você utilizaria o Expo CLI, uma ferramenta que simplifica o desenvolvimento de aplicativos React Native.

Primeiro, crie o projeto com um template TypeScript:

```
npx create-expo-app PokedexApp -t expo-template-blank-typescript
```

Este comando gera a estrutura básica do projeto já configurada para TypeScript.

Em seguida, navegue para o diretório do projeto:

```
cd PokedexApp
```

Depois, instale as dependências adicionais que serão úteis:

```
npm install axios react-native-safe-area-context
```

* **`axios`**: É um cliente HTTP popular baseado em Promises, usado aqui para fazer requisições à PokeAPI.
* **`react-native-safe-area-context`**: Ajuda a garantir que o conteúdo da interface do usuário não seja sobreposto por elementos do sistema operacional (como a barra de status ou o "notch" em alguns dispositivos). 

Para executar o projeto na versão web, também temos que instalar:

```
npx expo install react-dom react-native-web @expo/metro-runtime
```

E para rodar:

```
npx expo start
```

Vamos agora ao código-fonte de cada componente! 👽

### 📁 `App.tsx`
```tsx
import React from 'react';
import { SafeAreaProvider } from 'react-native-safe-area-context';
import { PokedexScreen } from './screens/PokedexScreen';

export default function App() {
  return (
    <SafeAreaProvider>
      <PokedexScreen />
    </SafeAreaProvider>
  );
}
```

Este é o componente raiz do seu aplicativo.

Ele importa `React` para a criação de componentes e o `SafeAreaProvider` de `react-native-safe-area-context`. Este último é envolvido em torno do conteúdo principal do aplicativo, garantindo que os componentes filhos possam usar o `SafeAreaView` ou hooks relacionados para ajustar o layout e evitar áreas de entalhe do dispositivo ou barras de sistema. A `PokedexScreen`, importada da pasta `screens`, é a tela inicial que será renderizada. A função `App` retorna a `PokedexScreen` encapsulada pelo `SafeAreaProvider`. 

Essencialmente, este arquivo configura o ponto de entrada básico e a gestão da área segura para o aplicativo.

### 🟦 `screens/PokedexScreen.tsx`
```tsx
import React, { useEffect, useState } from 'react';
import { View, Text, FlatList, TextInput, StyleSheet } from 'react-native';
import { getPokemons, getPokemonDetails } from '../services/api';
import { Pokemon } from '../types/Pokemon';
import { PokemonCard } from '../components/PokemonCard';

export const PokedexScreen = () => {
  const [pokemons, setPokemons] = useState<Pokemon[]>([]);
  const [search, setSearch] = useState('');

  useEffect(() => {
    const fetchData = async () => {
      const list = await getPokemons(30); // primeiros 30 pokemons
      const details = await Promise.all(list.map(p => getPokemonDetails(p.url)));
      setPokemons(details);
    };
    fetchData();
  }, []);

  const filtered = pokemons.filter(p => p.name.includes(search.toLowerCase()));

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Pokédex</Text>
      <TextInput
        placeholder="Buscar pokémon..."
        style={styles.input}
        onChangeText={setSearch}
      />
      <FlatList
        data={filtered}
        keyExtractor={item => item.id.toString()}
        numColumns={2}
        renderItem={({ item }) => <PokemonCard pokemon={item} />}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, paddingTop: 60, paddingHorizontal: 16 },
  title: { fontSize: 32, fontWeight: 'bold', marginBottom: 12 },
  input: {
    backgroundColor: '#f1f1f1',
    padding: 10,
    borderRadius: 8,
    marginBottom: 20,
  },
});
```

Este componente representa a tela principal da Pokédex! 

Ele importa as dependências necessárias, incluindo `React` com os hooks `useEffect` e `useState`, componentes do React Native como `View`, `Text`, `FlatList` e `TextInput`, além das funções de serviço `getPokemons` e `getPokemonDetails`, o tipo `Pokemon` e o componente `PokemonCard`. O estado do componente é gerenciado por `pokemons`, um array para os dados detalhados dos Pokémons, e `search`, uma string para o termo de busca. 

O hook `useEffect` é responsável por buscar os dados quando o componente é montado: ele chama `getPokemons(30)` para obter uma lista inicial e, em seguida, usa `Promise.all` com `getPokemonDetails` para buscar os detalhes de cada Pokémon, atualizando o estado `pokemons`. A busca é implementada filtrando o array `pokemons` com base no estado `search`, resultando no array `filtered` que considera a busca case-insensitive. Na renderização, um `View` principal contém o título "Pokédex", um `TextInput` para a busca (que atualiza o estado `search` via `onChangeText`), e uma `FlatList`.

A `FlatList` exibe os Pokémons filtrados em duas colunas (`numColumns={2}`), utilizando o `PokemonCard` para renderizar cada item. As chaves dos itens são extraídas dos IDs dos Pokémons. Os estilos visuais são definidos usando `StyleSheet.create`, incluindo um `paddingTop: 60` no contêiner principal, que pode ser uma forma de evitar a barra de status, embora o uso de `SafeAreaContext` seja mais robusto.

### 🟨 `components/PokemonCard.tsx`
```tsx
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';
import { Pokemon } from '../types/Pokemon';

interface Props {
  pokemon: Pokemon;
}

export const PokemonCard = ({ pokemon }: Props) => {
  return (
    <View style={styles.card}>
      <Image source={{ uri: pokemon.image }} style={styles.image} />
      <Text style={styles.name}>{pokemon.name}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    flex: 1,
    backgroundColor: '#e0e0e0',
    margin: 8,
    padding: 12,
    borderRadius: 12,
    alignItems: 'center',
  },
  image: { width: 80, height: 80 },
  name: { marginTop: 8, fontWeight: 'bold' },
});
```

Este é um componente de UI reutilizável, projetado para exibir as informações básicas de um único Pokémon em um formato de "cartão".

Ele importa `React`, componentes visuais do React Native como `View`, `Text`, `Image` e `StyleSheet`, além do tipo `Pokemon` para definir as props esperadas. A interface `Props` especifica que o componente `PokemonCard` deve receber uma propriedade `pokemon` do tipo `Pokemon`.

Só para relembrar o que vimos na aula anterior: Em TypeScript, uma **interface** descreve o formato obrigatório de um objeto e, neste componente, ela garante em tempo de compilação que `PokemonCard` receba exatamente uma prop `pokemon` conforme o contrato definido em `Pokemon`👆🤓

O componente funcional `PokemonCard` recebe essa prop `pokemon` desestruturada e retorna uma `View` principal estilizada como um cartão. Dentro deste cartão, um componente `Image` exibe a imagem do Pokémon, obtendo a URL de `pokemon.image`, e um componente `Text` mostra o nome do Pokémon (`pokemon.name`). Os estilos, definidos em `StyleSheet.create`, incluem `card` para o contêiner (com `flex: 1` para distribuição igual em layouts de grade, cor de fundo, margens, preenchimento, bordas arredondadas e alinhamento centralizado), `image` para definir as dimensões da imagem, e `name` para estilizar o texto do nome. O foco deste componente é a apresentação visual concisa de um Pokémon.

### 🟧 `services/api.ts`
```tsx
import axios from 'axios';
import { Pokemon, PokemonListItem } from '../types/Pokemon';

const API_BASE = 'https://pokeapi.co/api/v2';

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
```

Este módulo encapsula toda a lógica de comunicação com a PokeAPI. 

Ele importa `axios` para realizar requisições HTTP e os tipos `Pokemon` e `PokemonListItem` do arquivo `types/Pokemon.ts`. Uma constante `API_BASE` armazena a URL base da PokeAPI para centralizar essa informação. A função assíncrona `getPokemons` aceita um `limit` numérico e retorna uma `Promise` que resolve para um array de `PokemonListItem`.

Ela faz uma requisição GET ao endpoint `/pokemon` da API, utilizando o `limit`, e retorna `res.data.results`, que contém a lista de Pokémons com nome e URL para detalhes. A outra função assíncrona, `getPokemonDetails`, aceita uma `url` específica de um Pokémon e retorna uma `Promise` que resolve para um objeto `Pokemon` com detalhes completos. Ela faz uma requisição GET para a URL fornecida e mapeia a resposta da API para a estrutura `Pokemon`, extraindo `id`, `name`, a imagem (`res.data.sprites.front_default`) e os tipos (mapeando o array `res.data.types` para obter apenas os nomes dos tipos, usando `(t: any)` que poderia ser mais tipado para segurança).

Este arquivo abstrai as chamadas de API, mantendo os componentes de tela mais limpos.

### 🟪 `types/Pokemon.ts`
```tsx
export interface PokemonListItem {
  name: string;
  url: string;
}

export interface Pokemon {
  id: number;
  name: string;
  image: string;
  types: string[];
}
```

Este arquivo define as estruturas de dados, através de interfaces TypeScript, usadas no aplicativo para os Pokémons.

A interface `PokemonListItem` descreve a estrutura de um item na lista inicial de Pokémons retornada pela API, contendo `name` (o nome do Pokémon) e `url` (a URL para seus detalhes completos). A interface `Pokemon` define a estrutura de um objeto Pokémon com seus detalhes, incluindo `id` (identificador numérico), `name` (nome), `image` (URL da imagem) e `types` (um array de strings representando os tipos do Pokémon, como `["grass", "poison"]`). 

O uso dessas interfaces TypeScript ajuda a garantir a consistência dos dados em todo o aplicativo, auxiliando na detecção de erros durante o desenvolvimento e melhorando a legibilidade do código.

### 🟪 (Opcional) `utils/format.ts`
```tsx
export const capitalize = (s: string) => s.charAt(0).toUpperCase() + s.slice(1);
```

Este é um arquivo opcional destinado a conter funções utilitárias genéricas. A função `capitalize`, exportada aqui, aceita uma string `s` como argumento. Ela retorna uma nova string onde o primeiro caractere é convertido para maiúscula (`s.charAt(0).toUpperCase()`) e concatenado com o restante da string original (`s.slice(1)`). 

Embora não esteja diretamente em uso nos componentes fornecidos, serve como um bom exemplo de uma função utilitária que poderia ser empregada, por exemplo, para formatar os nomes dos Pokémons para exibição. 😊

### Resumindo tudo!

Nesse exercício vimos os conceitos principais sendo aplicados. Além disso, já conseguimos fazer um app bem completinho, com uso de API externa, interação com usuário e estilização!

Para consolidar o conhecimento, vamos fazer alguns exercícios! (sim, sim... eu sei, mas fazer o quê, né?)