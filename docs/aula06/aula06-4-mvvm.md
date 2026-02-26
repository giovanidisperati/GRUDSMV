---
layout: aula
title: "5. Refatoração e Padrão MVVM"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 4
---

## **5. Refatoração e Arquitetura: Aplicando o Padrão MVVM na Prática**

Até agora, focamos na implementação técnica para resolver nossos problemas: usamos a `Context API` para o estado global e o `AsyncStorage` para a persistência. No entanto, sem perceber, a estrutura que criamos ao fazer isso nos alinhou naturalmente com um dos padrões de arquitetura mais importantes para aplicações React: o **MVVM (Model-View-ViewModel)**.

Na Aula 05, discutimos o MVVM de forma teórica, como um modelo para separar as responsabilidades da nossa aplicação. Agora, vamos analisar como o nosso sistema de favoritos já é uma implementação prática desse padrão. As peças se encaixam da seguinte forma:

  * **Model:** Continua sendo nossa camada de serviços (`services/api.ts`), responsável por buscar os dados brutos da PokéAPI.
  * **View:** São nossos componentes de interface, como o `PokemonCard` e a `PokedexScreen`. A responsabilidade deles é apenas exibir os dados e capturar as interações do usuário.
  * **ViewModel:** É o nosso hook customizado `useFavorites`! Ele é o intermediário que prepara os dados para a `View` e contém toda a lógica de negócio (adicionar, remover, salvar no disco), sem nunca conhecer os detalhes da interface.

Nesta seção, vamos aprofundar essa conexão, explorando como essa arquitetura torna nosso código mais limpo, testável e fácil de evoluir.

### **5.1. Revisitando o MVVM: Hooks Customizados como *ViewModels***

Na Aula 05, introduzimos o padrão de arquitetura MVVM (Model-View-ViewModel) como um modelo ideal para aplicações reativas, como as que construímos com React Native. Naquela ocasião, mencionamos que a filosofia do MVVM se alinha perfeitamente com o uso de **Hooks Customizados** para servir como o *ViewModel*.

Antes de passarmos ao uso dos Hooks Customizados, entretanto, é importante abordarmos esse conceito (como prometido na aula anterior!)

#### **O que é um Hook Customizado?**

Até agora, nós utilizamos Hooks que o próprio React nos fornece, como `useState` e `useEffect`. No entanto, o verdadeiro poder do ecossistema de Hooks está na capacidade de **criar os nossos próprios**.

Um **Hook Customizado** é, essencialmente, uma **função JavaScript reutilizável** cujo nome, por convenção, sempre começa com a palavra `use` (ex: `useFavorites`). A sua principal característica é que ela pode **chamar outros Hooks** dentro dela (como `useState`, `useEffect` ou até mesmo outros hooks customizados).

**Por que criar um Hook Customizado?**

O objetivo é **extrair e compartilhar lógica com estado** entre diferentes componentes.

Imagine que vários componentes no nosso app precisam buscar dados de uma API. Em cada um deles, provavelmente repetiríamos a mesma lógica:

  * Um `useState` para guardar os dados.
  * Um `useState` para controlar o estado de `loading`.
  * Um `useState` para armazenar uma mensagem de `error`.
  * Um `useEffect` para fazer a chamada `fetch` quando o componente monta.

Isso é repetitivo e propenso a erros. Com um Hook Customizado, como um hipotético `useFetch(url)`, poderíamos encapsular toda essa lógica em um só lugar. Assim, em vez de reescrever tudo, um componente simplesmente faria:

```javascript
const { data, loading, error } = useFetch('https://pokeapi.co/api/v2/pokemon/pikachu');
```

O componente recebe os dados prontos para usar, sem se preocupar com os detalhes de como eles foram buscados ou gerenciados.

**As Regras de Ouro dos Hooks Customizados:**

1.  **O nome deve começar com `use`:** Isso não é apenas uma convenção. É o que permite que o linter do React verifique se as "Regras dos Hooks" (como não chamar hooks dentro de condicionais) estão sendo seguidas.
2.  **Eles compartilham lógica, não estado:** Cada vez que um componente diferente chama o mesmo hook customizado, ele recebe sua **própria versão isolada do estado**. O `useFetch` para o "Pikachu" e o `useFetch` para o "Charmander" terão seus próprios estados de `data`, `loading` e `error`, independentes um do outro.

É exatamente por essa capacidade de encapsular lógica e estado que os Hooks Customizados se tornam a ferramenta perfeita para implementar o padrão **ViewModel** no React. O hook se torna o nosso ViewModel, e o componente que o utiliza se torna a nossa View.

#### **Uso dos Hooks Customizados para refatoração da aplicação em MVVM**

Vamos relembrar rapidamente as responsabilidades de um *ViewModel* no padrão MVVM:

  * Ele prepara e expõe os dados do *Model* para a *View*.
  * Ele contém a lógica de apresentação e o estado da UI (como `isLoading`, `errorMessage`, etc.).
  * Ele expõe "comandos" (funções) que a *View* pode invocar.
  * Ele não tem nenhuma referência direta à *View*, o que o torna testável e reutilizável.

Agora, vamos analisar o hook `useFavorites` que criamos. Ele cumpre exatamente todas essas responsabilidades:

  * **Expõe Dados e Estado:** O nosso hook gerencia os estados internos (`favorites`, `loading`) e os expõe para qualquer componente que o consuma.
  * **Contém a Lógica de Negócio:** Toda a lógica complexa — como adicionar, remover, checar se um item é favorito e, mais importante, a interação com o `AsyncStorage` — está contida dentro do `FavoritesProvider` e é gerenciada pelo hook. A *View* não tem ideia de como essa mágica acontece.
  * **Expõe Comandos (Funções):** O hook expõe "comandos" na forma das funções `addFavorite` e `removeFavorite`. Os componentes da *View* simplesmente invocam essas funções em resposta a eventos do usuário (como um `onPress`), sem se preocupar com a implementação.
  * **É Desacoplado da View:** O hook `useFavorites` não importa nem conhece nenhum componente visual. Ele poderia ser usado na `PokedexScreen`, no `PokemonCard` ou em qualquer outra tela. Essa independência é o que o torna um *ViewModel* perfeito: ele é reutilizável e pode ser testado de forma isolada.

Portanto, ao criar um hook customizado que encapsula um estado (seja local ou de um contexto) e a lógica para manipulá-lo, estamos, na prática, implementando o padrão ViewModel. Essa é uma das formas mais poderosas e elegantes de estruturar aplicações React Native, resultando em um código mais organizado e fácil de manter.

### **5.2. Criando o `useFavorites`: Nosso Primeiro *ViewModel***

A partir do trabalho que acabamos de desenvolver, estabelecemos a conexão teórica: um Hook Customizado pode funcionar como um *ViewModel*. Agora, vamos formalizar isso e analisar como nosso hook `useFavorites`, que já está em nosso arquivo `src/contexts/FavoritesContext.tsx`, é, na prática, o nosso primeiro ViewModel.

Vamos rever o código do nosso hook:

```typescript
// Em src/contexts/FavoritesContext.tsx...

export function useFavorites(): FavoritesContextData {
  const context = useContext(FavoritesContext);
  if (!context) {
    throw new Error('useFavorites deve ser usado dentro de um FavoritesProvider');
  }
  return context;
}
```

Este simples hook encapsula toda a complexidade de interagir com o `FavoritesContext`. Qualquer componente que o chame não precisa saber sobre `useContext` ou sobre a estrutura interna do provedor. Ele apenas pede: "me dê a lógica de favoritos".

Ele cumpre perfeitamente o papel do ViewModel, como discutido na Aula 05:

  * **Expõe o estado:** Através da propriedade `favorites`.
  * **Expõe os comandos:** Através das funções `addFavorite`, `removeFavorite` e `isFavorite`.

Lembre-se do exemplo hipotético da Aula 05, onde imaginamos um `usePokedexViewModel`. O nosso `useFavorites` é a primeira implementação real desse conceito em nosso projeto. Estamos efetivamente separando a lógica de estado do componente de visualização.

Ao adotar este padrão, a lógica de negócio se torna centralizada e reutilizável. A seguir, veremos o resultado direto dessa organização: nossos componentes de *View* se tornarão muito mais simples, limpos e focados apenas em sua tarefa de apresentação.

### **5.3. A *View* Simplificada: Componentes Limpos e Declarativos e a Abordagem do "Component-as-ViewModel"**

O grande benefício de encapsular nossa lógica no hook `useFavorites` é que nossos componentes de interface — as *Views*, no jargão do MVVM — se tornam incrivelmente mais simples e declarativos.

Como discutido na Aula 05, a responsabilidade da *View* é apenas **exibir os dados** que recebe e **delegar as ações do usuário** para o *ViewModel*. Ela não precisa saber *como* as coisas funcionam por baixo dos panos.

Vamos ver como o nosso `PokemonCard` fica após adotar o `useFavorites`:

```typescript
// components/PokemonCard.tsx
import React from 'react';
import { View, Text, Image, StyleSheet, TouchableOpacity } from 'react-native';
import { useNavigation } from '@react-navigation/native';
import { Pokemon } from '../types/Pokemon';
import { RootStackParamList } from '../types/Navigation';
import { NativeStackNavigationProp } from '@react-navigation/native-stack';
import { capitalize } from '../utils/format';

// 1. Apenas importamos o hook, que é a nossa interface com a lógica
import { useFavorites } from '../contexts/FavoritesContext';

type PokemonCardNavigationProp = NativeStackNavigationProp<RootStackParamList, 'PokemonDetails'>;

export const PokemonCard = ({ pokemon }: { pokemon: Pokemon }) => {
  const navigation = useNavigation<PokemonCardNavigationProp>();

  // 2. Consumimos o ViewModel para obter o estado e os comandos
  const { addFavorite, removeFavorite, isFavorite } = useFavorites();
  const favorite = isFavorite(pokemon.id);

  // 3. A função de handle apenas delega a ação para o ViewModel
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
        <TouchableOpacity onPress={handleToggleFavorite} style={styles.favoriteButton}>
          <Text style={styles.favoriteIcon}>{favorite ? '⭐' : '☆'}</Text>
        </TouchableOpacity>
        
        <Image source={{ uri: pokemon.image }} style={styles.image} />
        <Text style={styles.name}>{capitalize(pokemon.name)}</Text>
      </View>
    </TouchableOpacity>
  );
};

// ... (estilos permanecem os mesmos)
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

Observe como o `PokemonCard` agora ficou mais limpo. Ele não tem `useState`, não tem `useEffect` e não tem conhecimento sobre `AsyncStorage`. Ele apenas:

1.  Pergunta ao `useFavorites` se o Pokémon atual é um favorito.
2.  Exibe uma estrela com base na resposta.
3.  Chama a função correspondente (`addFavorite` ou `removeFavorite`) quando o usuário clica.

A mesma lógica se aplica à tela `PokemonDetailsScreen`. O componente `PokemonDetailsScreen` também utiliza o `useFavorites` para interagir com a lógica de favoritos, mantendo-se sincronizado com o estado global.

**Uma Nota Importante sobre a "View" em React Native e o MVVM:**

É fundamental reconhecer que, em React Native, a linha entre **View** e **ViewModel** pode ser mais fluida do que em implementações puristas de MVVM em outras plataformas. Isso ocorre porque os componentes funcionais do React, como `PokemonCard` e `PokemonDetailsScreen`, são naturalmente responsáveis por **ambas as tarefas**: definir a interface do usuário (o JSX) *e* conter a lógica de apresentação e o estado local necessário para essa interface (através de `useState` e `useEffect`, por exemplo, para o carregamento dos detalhes do Pokémon).

Essa abordagem é comumente referida como **"Component-as-ViewModel"** ou uma interpretação **pragmática** do MVVM. Em vez de ter uma classe de ViewModel totalmente separada e uma classe de View totalmente separada, o próprio componente React atua como a View que renderiza o JSX *e* como a ViewModel que gerencia o estado e a lógica específica da tela (como o `isLoading`, `error`, e o `pokemon` carregado na `PokemonDetailsScreen`).

A chave para manter a separação de responsabilidades no MVVM, mesmo com essa fusão de View e ViewModel em um único componente React, está em:

1.  **Delegar a lógica de negócios complexa e compartilhada para Hooks Customizados ou Contextos específicos (como `useFavorites`):** Isso garante que essa lógica possa ser reutilizada e testada isoladamente, sem acoplar-se a componentes de UI específicos.
2.  **Manter a lógica do componente de tela (View/ViewModel) focada apenas na apresentação e no estado necessário para *aquela* tela específica:** Evitando que ele se torne um "Deus Objeto" que lida com tudo.

Essa simplicidade torna o componente reutilizável e fácil de testar visualmente. A mesma lógica se aplica à tela `PokemonDetailsScreen`, que também pode usar o `useFavorites` da mesma forma para se manter sincronizada.

Essa separação clara entre a *View* (o que o usuário vê) e o *ViewModel* (a lógica por trás) é a essência do MVVM e a chave para construir aplicações que sejam fáceis de entender e economicamente viáveis de se modificar. Em sequência, vamos explorar os benefícios diretos dessa arquitetura em termos de testabilidade e manutenção.

### **5.4. Benefícios Visíveis: Testabilidade e Separação de Responsabilidades (SoC)**

A refatoração que fizemos, separando nossa lógica no hook `useFavorites` (nosso *ViewModel*), não é apenas uma questão de organização. Ela traz dois benefícios práticos imediatos que são pilares no desenvolvimento de software de qualidade: a **testabilidade** e uma clara **separação de responsabilidades**.

#### Separação de Responsabilidades (SoC) na Prática

Com a nova estrutura, cada parte da nossa aplicação tem um papel único e bem definido:

  * **View (`PokemonCard`, `PokedexScreen`):** Sua única tarefa é exibir a interface e notificar o *ViewModel* sobre as ações do usuário. Ela é "burra" e não sabe como os favoritos são salvos ou gerenciados.
  * **ViewModel (`useFavorites`):** Contém toda a lógica de negócio — adicionar, remover, persistir dados. Ele serve os dados e as funções para a `View`, mas não se importa com a aparência dela (se é um botão, um texto, etc.).

Essa separação torna o código muito mais fácil de entender. Se houver um bug visual, sabemos que o problema está na *View*. Se os favoritos não estão sendo salvos corretamente, o problema está no *ViewModel*. Isso simplifica drasticamente a depuração e a manutenção.

#### Testabilidade Aprimorada

Este é talvez o benefício mais poderoso. Como nosso *ViewModel* (`useFavorites`) é um hook customizado — que é apenas uma função JavaScript — podemos testar toda a sua lógica de forma isolada, sem precisar renderizar um único componente visual.

Por exemplo, podemos escrever testes automatizados para verificar:

  * Se `addFavorite` realmente adiciona um ID ao array.
  * Se `removeFavorite` o remove corretamente.
  * Se `isFavorite` retorna `true` ou `false` como esperado.
  * Se a lógica de interação com o `AsyncStorage` está funcionando.

Isso está perfeitamente alinhado com o que vimos na Aula 05: o MVVM proporciona uma testabilidade altíssima porque a lógica de apresentação e de negócio é completamente desacoplada da camada de renderização.

Ao adotar esse padrão, não estamos apenas escrevendo código que funciona hoje, mas construindo uma base sólida que pode crescer, ser modificada e, acima de tudo, ser testada com confiança, garantindo a qualidade da nossa aplicação a longo prazo.

-----
