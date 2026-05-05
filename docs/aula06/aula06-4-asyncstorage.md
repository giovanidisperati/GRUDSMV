---
layout: aula
title: "4. Persistência com AsyncStorage"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 4
---

## **4. Tornando o Estado Persistente com AsyncStorage**

No tópico anterior, resolvemos o desafio do estado isolado. Nossa lista de favoritos agora é global e consistente entre as telas. No entanto, ainda enfrentamos a segunda limitação: os **dados são voláteis**.

Se o usuário fechar e reabrir a Pokédex, toda a sua lista de favoritos terá desaparecido. Para uma aplicação profissional, essa é uma falha grave na experiência do usuário.

Para tornar nossos dados permanentes, precisamos salvá-los no armazenamento local do dispositivo. No ecossistema React Native, a ferramenta padrão para isso é o **`@react-native-async-storage/async-storage`**, comumente chamado de **AsyncStorage**.

Ele funciona como um sistema de armazenamento de chave-valor onde todas as operações (salvar, ler, remover) são **assíncronas**, retornando `Promises`. O AsyncStorage armazena apenas dados no formato de **string** — para salvar arrays ou objetos, precisaremos convertê-los com `JSON.stringify` ao escrever e `JSON.parse` ao ler.

### **4.1. Por que Salvar Dados Localmente?**

Um dos pilares de uma boa experiência do usuário em aplicativos móveis é a **continuidade**. O usuário espera que o aplicativo "lembre" de suas preferências e personalizações entre diferentes sessões.

Como vimos, o estado gerenciado pelo `useState` ou pela `Context API` vive apenas na memória RAM do dispositivo. No momento em que o sistema operacional fecha o aplicativo, todo o nosso trabalho é perdido. O usuário pode passar vários minutos selecionando seus Pokémons favoritos apenas para descobrir, ao reabrir o app, que a lista está vazia. Essa experiência frustrante é o que a **persistência de dados locais** resolve.

O **AsyncStorage** oferece uma API de armazenamento de chave-valor, ideal para dados não-estruturados ou de pequeno a médio volume, como preferências de usuário, configurações do app ou, no nosso caso, uma lista de IDs de favoritos. Não se trata de um banco de dados relacional como o SQLite, é algo mais simples e direto.

É fundamental entender que todas as operações com o AsyncStorage são **assíncronas**: as funções para ler ou salvar dados retornam `Promises`, e devemos sempre usar `async/await` para lidar com elas.

### **4.2. Instalando e Conhecendo o `@react-native-async-storage/async-storage`**

A forma recomendada de instalação em projetos Expo é:

```bash
npx expo install @react-native-async-storage/async-storage
```

Este comando garante que será instalada uma versão compatível com a versão do Expo SDK do projeto.

#### Entendendo a API do AsyncStorage

O `AsyncStorage` possui três métodos principais:

**1. `setItem(key, value)`**

Para salvar um dado. Ambos os argumentos devem ser strings.

```javascript
const listaDeIds = [1, 4, 7];
await AsyncStorage.setItem('@PokedexApp:favorites', JSON.stringify(listaDeIds));
```

**2. `getItem(key)`**

Para ler um dado salvo. Retorna uma `Promise` que resolve para o valor em string, ou `null` se a chave não existir.

```javascript
const dadosSalvos = await AsyncStorage.getItem('@PokedexApp:favorites');

if (dadosSalvos !== null) {
  const listaDeIds = JSON.parse(dadosSalvos);
  console.log(listaDeIds); // [1, 4, 7]
}
```

**3. `removeItem(key)`**

Para remover um item do armazenamento.

```javascript
await AsyncStorage.removeItem('@PokedexApp:favorites');
```

### **4.3. Integrando a Persistência ao `FavoritesContext`**

Com o `AsyncStorage` instalado, vamos conectar sua funcionalidade ao nosso `FavoritesProvider`. A lógica se divide em dois momentos:

1.  **Ao iniciar a aplicação:** ler do `AsyncStorage` e carregar a lista salva para o estado.
2.  **Sempre que a lista for alterada:** salvar a nova versão no `AsyncStorage`.

Para executar essas ações no momento certo, usaremos dois `useEffect`s com responsabilidades distintas.

### **4.3.1. Carregando os Dados na Inicialização (`useEffect` de leitura)**

O primeiro efeito roda **apenas uma vez** quando o `FavoritesProvider` é montado — indicado pelo array de dependências vazio `[]`. Ele verifica se há favoritos salvos e os carrega para o estado.

Introduzimos também um estado `loading` para saber quando essa leitura inicial terminou. Esse estado será essencial na próxima seção.

```typescript
// Dentro do componente FavoritesProvider...
const [favorites, setFavorites] = useState<number[]>([]);
const [loading, setLoading] = useState(true); // true até a leitura inicial terminar

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
      console.error('Falha ao carregar os favoritos do armazenamento.', e);
    } finally {
      // 3. Independentemente do resultado, finaliza o estado de carregamento
      setLoading(false);
    }
  }

  loadFavorites();
}, []); // Array vazio: roda apenas uma vez, na montagem do componente
```

O bloco `finally` garante que `setLoading(false)` seja chamado independentemente de sucesso ou falha. Isso é necessário para que a aplicação saiba quando pode deixar de exibir um indicador de carregamento — veremos isso em breve.

### **4.3.2. Salvando as Alterações Automaticamente (`useEffect` de escrita)**

O segundo efeito é acionado sempre que o estado `favorites` muda. A condição `if (!loading)` nele é o detalhe mais importante de toda a integração.

```typescript
useEffect(() => {
  // Condição crítica: só salva APÓS o carregamento inicial ter terminado.
  // Sem isso, haveria uma condição de corrida:
  //   - O useEffect de leitura ainda não terminou de buscar os dados do disco.
  //   - Este useEffect de escrita já seria disparado com o array vazio [] inicial.
  //   - Resultado: o array vazio sobrescreveria os favoritos reais do usuário.
  if (!loading) {
    async function saveFavorites() {
      try {
        await AsyncStorage.setItem(FAVORITES_KEY, JSON.stringify(favorites));
      } catch (e) {
        console.error('Falha ao salvar os favoritos no armazenamento.', e);
      }
    }
    saveFavorites();
  }
}, [favorites, loading]); // Roda quando favorites ou loading mudam
```

O array de dependências `[favorites, loading]` garante que este efeito seja reavaliado quando o carregamento terminar — habilitando o mecanismo de salvamento — e também toda vez que o usuário favoritar ou desfavoritar um Pokémon.

### **4.4. O `FavoritesContext` Completo**

Após todas as integrações, abaixo está o código final e definitivo do arquivo `src/contexts/FavoritesContext.tsx`. Repare que enquanto `loading` for `true`, o `Provider` retorna um `ActivityIndicator` em vez de `null`.

Retornar `null` durante o carregamento inicial causaria uma tela completamente em branco — o que é uma experiência ruim, especialmente em dispositivos mais lentos. O `ActivityIndicator` comunica ao usuário que algo está acontecendo. Como o AsyncStorage é muito rápido (opera em milissegundos), na prática o spinner raramente aparecerá por mais de um instante.

```typescript
// src/contexts/FavoritesContext.tsx

import React, {
  createContext,
  useState,
  useEffect,
  ReactNode,
  useContext,
} from 'react';
import { ActivityIndicator } from 'react-native';
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

// null como valor inicial garante que o guard do useFavorites funcione de verdade.
// Um objeto vazio ({}) seria truthy e o if (!context) nunca dispararia.
export const FavoritesContext = createContext<FavoritesContextData | null>(null);

export const FavoritesProvider = ({ children }: { children: ReactNode }) => {
  const [favorites, setFavorites] = useState<number[]>([]);
  const [loading, setLoading] = useState(true);

  // Efeito 1: CARREGAR os favoritos do disco na inicialização
  useEffect(() => {
    async function loadFavorites() {
      try {
        const storedFavorites = await AsyncStorage.getItem(FAVORITES_KEY);
        if (storedFavorites) {
          setFavorites(JSON.parse(storedFavorites));
        }
      } catch (e) {
        console.error('Falha ao carregar os favoritos do armazenamento.', e);
      } finally {
        setLoading(false);
      }
    }
    loadFavorites();
  }, []);

  // Efeito 2: SALVAR os favoritos no disco sempre que a lista mudar
  useEffect(() => {
    if (!loading) {
      async function saveFavorites() {
        try {
          await AsyncStorage.setItem(FAVORITES_KEY, JSON.stringify(favorites));
        } catch (e) {
          console.error('Falha ao salvar os favoritos no armazenamento.', e);
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

  // Enquanto carrega os dados do disco, exibe um spinner em vez de tela em branco
  if (loading) {
    return <ActivityIndicator size="large" color="#e53935" />;
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
  // Como o contexto foi criado com null, este guard funciona de verdade:
  // se useFavorites for chamado fora de um FavoritesProvider, o erro será lançado.
  if (!context) {
    throw new Error('useFavorites deve ser usado dentro de um FavoritesProvider');
  }
  return context;
}
```

A virtude principal desta implementação é o **encapsulamento total da lógica de persistência**. Nossos componentes continuam interagindo apenas com o hook `useFavorites`, completamente alheios ao fato de que os dados estão sendo salvos no `AsyncStorage`. Essa separação de responsabilidades é o que separa uma aplicação de protótipo de uma aplicação pronta para produção: se no futuro decidirmos usar um banco de dados mais robusto, a única parte do código que precisará de alteração será o `FavoritesProvider` — a interface do usuário permanecerá intacta.