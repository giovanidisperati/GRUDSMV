---
layout: aula
title: "4. Persistência com AsyncStorage"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 3
---

## **4. Tornando o Estado Persistente com AsyncStorage**

No tópico anterior, resolvemos o desafio do estado isolado. Nossa lista de favoritos agora é global e consistente entre as telas. No entanto, ainda enfrentamos a segunda limitação que discutimos na introdução: os **dados são voláteis**.

Isso significa que, se o usuário fechar e reabrir a Pokédex, toda a sua lista de Pokémon favoritos terá desaparecido. Para uma aplicação profissional, essa é uma falha grave na experiência do usuário, pois o trabalho de personalização dele não é respeitado.

Para tornar nossos dados permanentes, precisamos salvá-los no armazenamento local do dispositivo. No ecossistema React Native, a ferramenta padrão para isso é o **`@react-native-async-storage/async-storage`**, comumente chamado de **AsyncStorage**.

Ele funciona como um sistema de armazenamento de chave-valor, e, como o nome sugere, todas as suas operações (salvar, ler, remover) são **assíncronas**, retornando `Promises`. É importante saber que o AsyncStorage armazena apenas dados no formato de **string**. Portanto, para salvar objetos ou arrays, como a nossa lista de favoritos, precisaremos convertê-los para string (usando `JSON.stringify`) e, ao lê-los, fazer o processo inverso (com `JSON.parse`).

Nesta seção, vamos instalar essa biblioteca, integrá-la ao nosso `FavoritesContext` e criar um fluxo onde os favoritos são carregados do disco quando o app abre e salvos automaticamente sempre que o usuário faz uma alteração.

### **4.1. Por que Salvar Dados Localmente? Introdução ao Armazenamento no Dispositivo**

Um dos pilares de uma boa experiência do usuário em aplicativos móveis é a **continuidade**. O usuário espera que o aplicativo "lembre" de suas ações, preferências e personalizações entre diferentes sessões de uso.

Como vimos, o estado gerenciado pelo `useState` ou pela `Context API` vive apenas na memória RAM do dispositivo. Isso significa que no momento em que o sistema operacional fecha o aplicativo (seja por ação do usuário ou para liberar recursos), todo o nosso trabalho é perdido. No nosso caso, o usuário pode passar vários minutos navegando, selecionando cuidadosamente seus Pokémons favoritos, apenas para descobrir que, ao reabrir o app, sua lista de favoritos está vazia. Essa experiência é frustrante e quebra a percepção de que o aplicativo é um espaço pessoal e customizável.

É aqui que entra a **persistência de dados locais**: a capacidade de salvar informações diretamente no armazenamento do dispositivo (seja um celular ou tablet) para que possam ser recuperadas em sessões futuras. No universo React Native, a principal ferramenta para armazenamento simples de dados é o **`@react-native-async-storage/async-storage`** (ou simplesmente **AsyncStorage**).

Ele oferece uma API de armazenamento de **chave-valor**, ideal para dados não-estruturados ou de pequeno a médio volume, como preferências de usuário, tokens de autenticação ou, no nosso caso, uma lista de IDs de favoritos. Não se trata de um banco de dados relacional como o SQLite.

É fundamental entender que todas as operações com o AsyncStorage são **assíncronas**. Isso significa que as funções para ler ou salvar dados retornam `Promises`, e devemos sempre usar `async/await` ou `.then()` para lidar com elas, garantindo que a interface do usuário não seja bloqueada durante as operações de disco.

A seguir, vamos instalar esta biblioteca e prepará-la para ser integrada ao nosso `FavoritesContext`.

### **4.2. Instalando e Conhecendo o `@react-native-async-storage/async-storage`**

Antes de integrarmos a persistência ao nosso contexto, o primeiro passo é adicionar a biblioteca `AsyncStorage` ao nosso projeto. Por se tratar de uma biblioteca que interage com o sistema nativo do dispositivo, a forma recomendada de instalação em projetos Expo é utilizando o comando:

```bash
npx expo install @react-native-async-storage/async-storage
```

Este comando garante que será instalada uma versão da biblioteca que é compatível com a versão do Expo SDK do nosso projeto, evitando possíveis conflitos.

#### Entendendo a API do AsyncStorage

O `AsyncStorage` possui uma API simples e assíncrona baseada em três métodos principais:

**1. `setItem(key, value)`**

Para salvar um dado, usamos `setItem`. Ele recebe dois argumentos: uma `chave` (key) e um `valor` (value). **Ambos devem ser strings.**

```javascript
// Exemplo de como salvar nossa lista de favoritos
// Lembre-se que o array precisa ser convertido para string com JSON.stringify

const listaDeIds = [1, 4, 7];
const dadosEmString = JSON.stringify(listaDeIds);

await AsyncStorage.setItem('@PokedexApp:favorites', dadosEmString);
```

**2. `getItem(key)`**

Para ler um dado salvo, usamos `getItem`, que recebe a `chave` do item desejado. Ele retorna uma `Promise` que resolve para o valor em string, ou `null` se a chave não existir.

```javascript
const dadosSalvos = await AsyncStorage.getItem('@PokedexApp:favorites');

if (dadosSalvos !== null) {
  // O valor volta como string, então precisamos convertê-lo de volta para um array
  const listaDeIds = JSON.parse(dadosSalvos);
  console.log(listaDeIds); // Exibirá: [1, 4, 7]
}
```

**3. `removeItem(key)`**

Para remover um item do armazenamento, usamos `removeItem`, passando a chave que queremos apagar.

```javascript
await AsyncStorage.removeItem('@PokedexApp:favorites');
```

Com a biblioteca instalada e seus métodos básicos compreendidos, estamos prontos para a parte mais importante: integrar essa lógica de salvar e carregar dados diretamente no nosso `FavoritesContext`.

### **4.3. Integrando a Persistência ao `FavoritesContext`**

Com o `AsyncStorage` instalado, o passo final é conectar sua funcionalidade ao nosso `FavoritesContext`. A lógica é a seguinte:

1.  **Ao iniciar a aplicação:** Precisamos verificar se já existe uma lista de favoritos salva no `AsyncStorage`. Se houver, carregamos essa lista para o nosso estado `favorites`.
2.  **Sempre que a lista de favoritos for alterada:** Precisamos salvar a nova versão da lista no `AsyncStorage`, sobrescrevendo a anterior.

Para executar essas ações no momento certo (ao montar o componente e a cada atualização do estado), usaremos o hook `useEffect`.

Vamos modificar nosso arquivo `src/contexts/FavoritesContext.tsx` para incluir essa lógica:

```typescript
// src/contexts/FavoritesContext.tsx
import React, { createContext, useState, useEffect, ReactNode, useContext } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

const FAVORITES_KEY = '@PokedexApp:favorites';

interface FavoritesContextData {
  favorites: number[];
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}

export const FavoritesContext = createContext<FavoritesContextData>({} as FavoritesContextData);

export const FavoritesProvider = ({ children }: { children: ReactNode }) => {
  const [favorites, setFavorites] = useState<number[]>([]);
  // Adicionamos um estado de 'loading' para o carregamento inicial
  const [loading, setLoading] = useState(true);

  // Efeito para CARREGAR os favoritos do AsyncStorage na inicialização
  useEffect(() => {
    async function loadFavorites() {
      try {
        const storedFavorites = await AsyncStorage.getItem(FAVORITES_KEY);
        if (storedFavorites) {
          setFavorites(JSON.parse(storedFavorites));
        }
      } catch (e) {
        console.error("Falha ao carregar os favoritos do armazenamento.", e);
      } finally {
        setLoading(false);
      }
    }
    loadFavorites();
  }, []);

  // Efeito para SALVAR os favoritos no AsyncStorage sempre que a lista mudar
  useEffect(() => {
    // Evitamos salvar no primeiro render, antes dos dados serem carregados
    if (!loading) {
      async function saveFavorites() {
        try {
          await AsyncStorage.setItem(FAVORITES_KEY, JSON.stringify(favorites));
        } catch (e) {
          console.error("Falha ao salvar os favoritos no armazenamento.", e);
        }
      }
      saveFavorites();
    }
  }, [favorites, loading]);

  const addFavorite = (pokemonId: number) => {
    if (!favorites.includes(pokemonId)) {
      setFavorites(prev => [...prev, pokemonId]);
    }
  };

  const removeFavorite = (pokemonId: number) => {
    setFavorites(prev => prev.filter(id => id !== pokemonId));
  };

  const isFavorite = (pokemonId: number) => {
    return favorites.includes(pokemonId);
  };

  // Evita renderizar a aplicação antes que os favoritos sejam carregados
  if (loading) {
    return null;
  }

  return (
    <FavoritesContext.Provider value={{ favorites, addFavorite, removeFavorite, isFavorite }}>
      {children}
    </FavoritesContext.Provider>
  );
};

// Hook customizado para facilitar o consumo do contexto
export function useFavorites(): FavoritesContextData {
  const context = useContext(FavoritesContext);
  if (!context) {
    throw new Error('useFavorites deve ser usado dentro de um FavoritesProvider');
  }
  return context;
}
```

#### O que fizemos aqui?

  * **Estado de Carregamento:** Adicionamos um novo estado `loading` para sabermos quando a busca inicial no `AsyncStorage` terminou. Isso é importante para evitar que a lista vazia inicial (`[]`) seja salva no disco antes de carregarmos os dados reais.
  * **Primeiro `useEffect` (Carregar Dados):** Este efeito roda **apenas uma vez** quando o `Provider` é montado (note o array de dependências vazio `[]`). Ele tenta ler a chave `@PokedexApp:favorites` do disco. Se encontrar dados, usa `JSON.parse` para convertê-los de volta em um array e atualiza nosso estado com `setFavorites`.
  * **Segundo `useEffect` (Salvar Dados):** Este efeito é acionado sempre que o estado `favorites` (ou `loading`) muda. A condição `if (!loading)` garante que só tentaremos salvar os dados **após** o carregamento inicial ter sido concluído. Ele pega o array atual de `favorites`, converte-o para uma string com `JSON.stringify` e o salva no disco.

E está feito! Com esta integração, nosso `FavoritesContext` agora é totalmente persistente. O usuário pode fechar o aplicativo e, ao abri-lo novamente, sua lista de Pokémons favoritos estará exatamente como ele deixou.

É importante destacar que esta implementação não é apenas uma solução que funciona, mas um exemplo de como construir funcionalidades resilientes e bem-arquiteturadas em React Native. A principal virtude desta abordagem é o **encapsulamento total da lógica de persistência**. Nossos componentes, como o `PokemonCard`, continuam a interagir apenas com o hook `useFavorites`, completamente alheios ao fato de que os dados estão sendo salvos no `AsyncStorage`. Essa **separação de responsabilidades** é de grande importância: se no futuro decidirmos usar um banco de dados mais complexo, a única parte do código que precisará de alteração será o `FavoritesProvider`. A interface do usuário permanecerá intacta.

O uso de dois `useEffect`s distintos, um para carregar e outro para salvar, demonstra um **gerenciamento de ciclo de vida claro e deliberado**. A introdução de um estado de `loading` é o que torna a solução **resiliente**: ela previne que a interface exiba um estado inconsistente (uma lista vazia que "pisca" na tela) e, através da verificação `if (!loading)`, evita uma condição de corrida onde o estado inicial vazio poderia sobrescrever os dados já salvos. Além disso, os blocos `try/catch` garantem que, mesmo que a leitura ou escrita no disco falhe, a aplicação não irá quebrar.

Em resumo, a solução implementada lida com a natureza assíncrona do armazenamento, trata possíveis erros e gerencia estados intermediários — detalhes que separam uma aplicação de protótipo de uma aplicação pronta para produção. A seguir, vamos analisar como toda essa estrutura se encaixa no padrão de arquitetura MVVM.

### **4.3.1. Carregando os Dados do `AsyncStorage` na Inicialização do App (`useEffect`)**

A primeira parte da nossa integração com o `AsyncStorage` é a **leitura dos dados**. Queremos que, assim que o nosso `FavoritesProvider` for montado, ele verifique se há uma lista de favoritos salva no disco e, em caso afirmativo, a carregue для o estado da nossa aplicação.

Para executar uma ação apenas uma vez, no momento em que o componente é "montado" (ou seja, aparece na tela pela primeira vez), usamos o hook `useEffect` com um array de dependências vazio (`[]`).

Vamos adicionar o estado de `loading` e o `useEffect` de carregamento ao nosso `FavoritesProvider` no arquivo `src/contexts/FavoritesContext.tsx`:

```typescript
// Dentro do componente FavoritesProvider...
const [favorites, setFavorites] = useState<number[]>([]);
const [loading, setLoading] = useState(true); // Novo estado para o carregamento inicial

useEffect(() => {
  async function loadFavorites() {
    try {
      // 1. Tenta ler o item salvo com a nossa chave
      const storedFavorites = await AsyncStorage.getItem(FAVORITES_KEY);
      
      // 2. Se houver dados, converte de string para array e atualiza o estado
      if (storedFavorites) {
        setFavorites(JSON.parse(storedFavorites));
      }
    } catch (e) {
      console.error("Falha ao carregar os favoritos do armazenamento.", e);
    } finally {
      // 3. Independentemente do resultado, finaliza o estado de carregamento
      setLoading(false);
    }
  }
  
  loadFavorites();
}, []); // O array vazio garante que isso rode apenas uma vez
```

#### Análise do código:

1.  **Leitura Assíncrona:** A linha `await AsyncStorage.getItem(FAVORITES_KEY)` pausa a execução da função `loadFavorites` até que a operação de leitura no disco seja concluída. O resultado será a string salva ou `null` caso a chave não exista.
2.  **Atualização do Estado:** Se `storedFavorites` não for `null`, significa que encontramos dados. Usamos `JSON.parse()` para transformar a string de volta em um array de números e o passamos para `setFavorites`, preenchendo nosso estado global com os dados persistidos.
3.  **Controle de Carregamento:** O bloco `finally` garante que `setLoading(false)` seja chamado independentemente de a operação ter sucesso ou falhar. Isso é necessário para que nossa aplicação saiba quando pode parar de exibir um indicador de carregamento e mostrar o conteúdo principal.

Esta estrutura não é acidental; ela representa um padrão moderno e profissional. A utilização do `useEffect` com um array de dependências vazio (`[]`) é a forma canônica de garantir que a busca no `AsyncStorage` ocorra apenas uma vez. Da mesma forma, a criação de uma função `async` interna é o padrão correto para lidar com operações assíncronas dentro de um efeito. O que realmente eleva a qualidade do código é a sua **robustez**: o bloco `try...catch...finally` nos protege contra falhas na leitura do disco, enquanto o estado de `loading` e a chamada `setLoading(false)` no `finally` garantem uma experiência de usuário fluida, sem telas "congeladas" ou "piscando" com conteúdo incorreto.

Com esta lógica, nossa aplicação já é capaz de carregar o estado persistido. Agora, precisamos implementar a outra metade do ciclo: salvar as alterações de volta para o disco sempre que o usuário favoritar ou desfavoritar um Pokémon.

### **4.3.2. Salvando as Alterações Automaticamente a cada Modificação (`useEffect`)**

Com a lógica de carregamento pronta, precisamos agora implementar a outra metade do ciclo: **salvar os dados**. O objetivo é que, toda vez que o usuário adicionar ou remover um Pokémon dos favoritos, a nova lista seja automaticamente salva no `AsyncStorage`.

Para isso, usaremos outro hook `useEffect`, mas desta vez, vamos configurá-lo para ser executado sempre que o nosso estado `favorites` for modificado. Fazemos isso passando `favorites` no array de dependências.

Adicione este segundo `useEffect` ao nosso `FavoritesProvider`, logo após o primeiro:

```typescript
// Efeito para SALVAR os favoritos no AsyncStorage sempre que a lista mudar
useEffect(() => {
  // 1. Evitamos salvar no primeiro render, antes dos dados serem carregados
  if (!loading) {
    async function saveFavorites() {
      try {
        // 2. Converte o array para string e salva com a nossa chave
        const dadosEmString = JSON.stringify(favorites);
        await AsyncStorage.setItem(FAVORITES_KEY, dadosEmString);
      } catch (e) {
        console.error("Falha ao salvar os favoritos no armazenamento.", e);
      }
    }
    saveFavorites();
  }
}, [favorites, loading]); // 3. O efeito é disparado quando 'favorites' ou 'loading' mudam
```

#### Análise do código:

1.  **Prevenindo a Condição de Corrida (`if (!loading)`):** Esta verificação é muito importante! O primeiro `useEffect` (de carregamento) é assíncrono. Enquanto ele está lendo os dados do disco, o nosso estado `favorites` ainda é um array vazio `[]`. Sem o `if (!loading)`, este segundo `useEffect` (de salvamento) poderia ser executado imediatamente na primeira renderização, pegando o array vazio e o salvando no disco, efetivamente **apagando** os favoritos que estávamos tentando carregar. Ao esperar que `loading` seja `false`, garantimos que só começaremos a salvar as alterações **após** o carregamento inicial ter sido concluído.
2.  **Salvando os Dados:** Dentro da função, convertemos nosso array `favorites` para uma string JSON com `JSON.stringify` e usamos `AsyncStorage.setItem` para gravá-lo no dispositivo sob a nossa chave.
3.  **O Array de Dependências (`[favorites, loading]`):** Este `useEffect` será re-executado sempre que o array `favorites` mudar (um item foi adicionado/removido). Incluímos `loading` na dependência para garantir que a lógica seja reavaliada assim que o carregamento inicial terminar, habilitando o mecanismo de salvamento.

Com os dois efeitos trabalhando em conjunto — um para ler e outro para escrever — nosso `FavoritesContext` agora gerencia um estado que é não apenas global, mas também persistente, criando um ciclo completo e robusto de gerenciamento de dados.

### **4.4. Nosso Código Até Aqui: O `FavoritesContext` Completo**

Após as integrações que fizemos, o nosso arquivo `FavoritesContext.tsx` tornou-se o coração do nosso sistema de favoritos. Ele agora é responsável por gerenciar o estado, compartilhar os dados com toda a aplicação e, crucialmente, persistir essas informações no disco do dispositivo.

Para referência, a estrutura de pastas do nosso projeto agora se parece com isto:

```
pokedex-app/
└── src/
    ├── components/
    ├── contexts/
    │   └── FavoritesContext.tsx  <-- Nosso novo arquivo completo
    ├── screens/
    ├── services/
    └── types/
```

Abaixo está o código completo e final do arquivo `src/contexts/FavoritesContext.tsx`, reunindo tudo o que construímos nos tópicos 3 e 4.

```typescript
// src/contexts/FavoritesContext.tsx

import React, { 
  createContext, 
  useState, 
  useEffect, 
  ReactNode, 
  useContext 
} from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

// Chave única para salvar os dados no AsyncStorage
const FAVORITES_KEY = '@PokedexApp:favorites';

// Interface que define o "contrato" do nosso contexto
interface FavoritesContextData {
  favorites: number[];
  addFavorite: (pokemonId: number) => void;
  removeFavorite: (pokemonId: number) => void;
  isFavorite: (pokemonId: number) => boolean;
}

// Criação do contexto com um valor padrão inicial
export const FavoritesContext = createContext<FavoritesContextData>({} as FavoritesContextData);

// Componente Provedor que gerencia e distribui o estado
export const FavoritesProvider = ({ children }: { children: ReactNode }) => {
  const [favorites, setFavorites] = useState<number[]>([]);
  const [loading, setLoading] = useState(true);

  // Efeito para CARREGAR os favoritos do disco na inicialização
  useEffect(() => {
    async function loadFavorites() {
      try {
        const storedFavorites = await AsyncStorage.getItem(FAVORITES_KEY);
        if (storedFavorites) {
          setFavorites(JSON.parse(storedFavorites));
        }
      } catch (e) {
        console.error("Falha ao carregar os favoritos do armazenamento.", e);
      } finally {
        setLoading(false);
      }
    }
    loadFavorites();
  }, []);

  // Efeito para SALVAR os favoritos no disco sempre que a lista mudar
  useEffect(() => {
    if (!loading) {
      async function saveFavorites() {
        try {
          await AsyncStorage.setItem(FAVORITES_KEY, JSON.stringify(favorites));
        } catch (e) {
          console.error("Falha ao salvar os favoritos no armazenamento.", e);
        }
      }
      saveFavorites();
    }
  }, [favorites, loading]);

  const addFavorite = (pokemonId: number) => {
    if (!favorites.includes(pokemonId)) {
      setFavorites(prev => [...prev, pokemonId]);
    }
  };

  const removeFavorite = (pokemonId: number) => {
    setFavorites(prev => prev.filter(id => id !== pokemonId));
  };

  const isFavorite = (pokemonId: number) => {
    return favorites.includes(pokemonId);
  };

  // Evita renderizar a aplicação antes que os favoritos sejam carregados
  if (loading) {
    return null;
  }

  return (
    <FavoritesContext.Provider value={{ favorites, addFavorite, removeFavorite, isFavorite }}>
      {children}
    </FavoritesContext.Provider>
  );
};

// Hook customizado para facilitar o consumo do contexto
export function useFavorites(): FavoritesContextData {
  const context = useContext(FavoritesContext);
  if (!context) {
    throw new Error('useFavorites deve ser usado dentro de um FavoritesProvider');
  }
  return context;
}
```

Com este arquivo, toda a lógica complexa de estado global e persistência está encapsulada em um só lugar. Nossos componentes de UI podem simplesmente usar o hook `useFavorites` sem se preocupar com os detalhes da implementação.

Agora que temos um sistema de dados funcional, podemos dar um passo atrás e analisar como essa estrutura se encaixa no padrão de arquitetura MVVM, que será o tema da nossa próxima seção.

-----
