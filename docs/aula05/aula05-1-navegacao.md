---
layout: aula
title: "1. Introdução à Navegação com React Navigation"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 1
---

## **1. Introdução à Navegação com React Navigation**

Aplicativos móveis raramente se limitam a uma única tela. A capacidade de transitar entre diferentes seções, exibir informações detalhadas ou guiar o usuário por um fluxo de tarefas é fundamental. No universo React Native, uma das bibliotecas mais populares e robustas para gerenciar a navegação é a **React Navigation**.

Ela oferece diversas estratégias de navegação (pilha, abas, gaveta) e é altamente configurável. Nesta aula, focaremos na navegação em pilha (*stack navigation*), ideal para cenários onde uma nova tela é colocada sobre a anterior, permitindo ao usuário "voltar" (como ao abrir um item de uma lista para ver seus detalhes).

**Antes de começar, é preciso ter o código do exercício da Aula passada**. Caso você não o tenha feito, ele pode ser encontrado no link a seguir: [Repositório no GitHub - Pokedéx da Aula04](https://github.com/giovanidisperati/PokedexApp/tree/Aula04)

Certique-se de "puxar" a branch Aula04 para sua máquina.

Vamos agora começar a abordar o React Navigation a partir do Desafio 01 da Aula anterior, que solicitava **(1)** instalar o pacote `@react-navigation/native` e suas dependências específicas do Expo, **(2)** criar um *stack navigator* no `App.tsx` definindo a tela **Pokedex** como rota inicial e adicionando a nova rota **PokemonDetails**, **(3)** declarar o tipo `RootStackParamList` para garantir que os parâmetros de navegação sejam verificados em tempo de compilação, **(4)** implementar a `PokemonDetailsScreen` para exibir informações detalhadas do Pokémon selecionado, e **(5)** transformar cada cartão da lista na Pokédex em um `TouchableOpacity` que, ao ser pressionado, chame `navigation.navigate('PokemonDetails', { pokemonId })`.

Ao cumprir essas etapas, converteremos nossa aplicação de tela única em um fluxo completo de navegação.

### **1.1 Instalação das dependências**

Para começar, precisamos adicionar a React Navigation e suas dependências ao nosso projeto `PokedexApp` (criado na Aula 04). Abra o terminal na raiz do projeto e execute:

```bash
npm install @react-navigation/native @react-navigation/native-stack
```

E também as dependências de suporte:

```bash
npx expo install react-native-screens react-native-safe-area-context
```

Esses pacotes são:
* `@react-navigation/native`: O núcleo da biblioteca.
* `@react-navigation/native-stack`: Fornece a navegação em pilha (estilo nativo).
* `react-native-screens`: Otimiza o uso de memória para telas nativas.
* `react-native-safe-area-context`: A mesma biblioteca que usamos na Aula 04 para lidar com áreas seguras, essencial para a React Navigation.

### **1.2 Configuração inicial no `App.tsx`**

Com as dependências instaladas, vamos configurar o contêiner de navegação e nossa pilha de telas no arquivo `App.tsx`.

Primeiro, precisamos definir os tipos das nossas rotas. Isso garante que estamos passando os parâmetros corretos entre as telas e nos dá um ótimo autocomplete! 

Crie um novo arquivo types/Navigation.ts e coloque nele o código a seguir, como mostrado na estrutura abaixo:

```
PokedexApp/
├─ App.tsx
├─ screens/
│  ├─ PokedexScreen.tsx
│  └─ PokemonDetailsScreen.tsx
└─ types/
   └─ Navigation.ts      ← aqui fica o código abaixo
```

E a seguir o `Navigation.ts`

```typescript
export type RootStackParamList = {
  Pokedex: undefined; // A tela Pokedex não recebe parâmetros
  PokemonDetails: { pokemonId: number }; // A tela de detalhes recebe o ID do Pokémon como parâmetro
};
```

Agora, alteramos o `App.tsx`:

```tsx
// App.tsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SafeAreaProvider } from 'react-native-safe-area-context';

import { PokedexScreen } from './screens/PokedexScreen';
import { PokemonDetailsScreen } from './screens/PokemonDetailsScreen'; // Nova tela
import { RootStackParamList } from './types/Navigation'; // Importando nossos tipos

// Criamos a pilha de navegação com os tipos definidos
const Stack = createNativeStackNavigator<RootStackParamList>();

export default function App() {
  return (
    <SafeAreaProvider>
      <NavigationContainer>
        <Stack.Navigator initialRouteName="Pokedex">
          <Stack.Screen 
            name="Pokedex" 
            component={PokedexScreen} 
            options={{ title: 'Pokédex' }} // Título da tela na barra de navegação
          />
          <Stack.Screen 
            name="PokemonDetails" 
            component={PokemonDetailsScreen} 
            options={{ title: 'Detalhes do Pokémon' }} 
          />
        </Stack.Navigator>
      </NavigationContainer>
    </SafeAreaProvider>
  );
}
```

**E o que fizemos aqui?**

Para entender o código acima, vamos separar a explicação em cinco etapas.

1. Importamos `NavigationContainer` e `createNativeStackNavigator`.

A configuração inicial da navegação no arquivo `App.tsx` estabelece a fundação para a transição entre múltiplas telas na aplicação Pokédex. Primeiramente, foi realizada a importação dos módulos da biblioteca React Navigation: `NavigationContainer`, que serve como o contêiner raiz para gerenciar o estado da navegação, e `createNativeStackNavigator`, uma função que possibilita a criação de um navegador baseado em pilha, onde novas telas são colocadas sobre as anteriores, mimetizando o comportamento de navegação nativo das plataformas móveis. Adicionalmente, o `SafeAreaProvider` da biblioteca `react-native-safe-area-context` foi definido como o componente mais externo para garantir que a interface do usuário respeite as áreas seguras do dispositivo, como entalhes e barras de sistema, por exemplo.

2. Definimos `RootStackParamList` para tipar nossas rotas e seus parâmetros.

Aqui é onde ocorre a definição explícita dos tipos das rotas e seus respectivos parâmetros através da criação de uma interface TypeScript denominada `RootStackParamList`. Esta interface, localizada em `types/Navigation.ts`, especifica que a rota `Pokedex` não espera nenhum parâmetro (`undefined`), enquanto a rota `PokemonDetails` requer um parâmetro obrigatório `pokemonId` do tipo `number`. Essa tipagem estática serve para prevenirmos erros em tempo de execução ao passar dados entre telas, além de habilitar o suporte a autocompletar e verificações durante o desenvolvimento. Em outras palavras, pense no **`RootStackParamList`** como um “contrato” que diz, para cada tela da navegação, quais dados ela recebe. Se você tentar abrir **`PokemonDetails`** sem mandar `pokemonId`, o TypeScript já avisa no editor. Se digitar um id como string (`"42"`) em vez de número, ele avisa também. Em resumo, é só uma forma de “tipar” as rotas para pegar erros antes mesmo de rodar o app e ainda ganhar autocomplete enquanto programa.

3.  Criamos uma instância `Stack` usando `createNativeStackNavigator<RootStackParamList>()`.

Com os tipos definidos, uma instância do navegador de pilha, nomeada `Stack`, foi criada utilizando `createNativeStackNavigator<RootStackParamList>()`. Pense nesta função como uma **fábrica** que nos entrega os componentes `Stack.Navigator` e `Stack.Screen` para montarmos nossa navegação. Além disso, hooks como `useRoute` se tornam automaticamente cientes dos tipos de dados que a tela receberá. Em resumo, essa linha de código transforma nosso navegador de uma ferramenta genérica para uma estrutura de navegação especializada e inteligente, que previne uma classe inteira de bugs antes mesmo de rodarmos a aplicação.

4.  Envolvemos toda a aplicação com `NavigationContainer`.

Posteriormente, no componente `App`, a estrutura de navegação foi efetivamente montada. O `NavigationContainer` foi utilizado para envolver toda a lógica de navegação. Dentro dele, o `Stack.Navigator` foi empregado para declarar o conjunto de telas que compõem a pilha de navegação. A propriedade `initialRouteName="Pokedex"` designou a `PokedexScreen` como a tela a ser exibida inicialmente quando o aplicativo é carregado.

5.  Usamos `Stack.Navigator` para definir quais telas fazem parte da nossa pilha.
    * `initialRouteName="Pokedex"`: Define `PokedexScreen` como a tela inicial.
    * Cada `Stack.Screen` mapeia um nome de rota para um componente e permite configurar opções, como o título (`options={{ title: '...' }}`).

Cada tela da pilha foi definida por meio do componente `Stack.Screen`. Para a rota nomeada `"Pokedex"`, o componente `PokedexScreen` (previamente existente) foi associado, e através da propriedade `options`, um título "Pokédex" foi configurado para aparecer na barra de navegação dessa tela. De forma similar, uma nova rota `"PokemonDetails"` foi configurada para renderizar o componente `PokemonDetailsScreen` (uma nova tela a ser criada), com o título "Detalhes do Pokémon" especificado para sua barra de navegação. Esse mapeamento entre nomes de rotas, componentes de tela e opções de apresentação é o que permite ao `NavigationContainer` gerenciar qual tela está ativa e como ela deve ser exibida. Com essa estrutura implementada, a nossa Pokédex evoluiu de uma aplicação de tela única para uma aplicação capaz de apresentar múltiplas visualizações, especificamente uma lista de Pokémons e uma tela dedicada para os detalhes de um Pokémon individual!

---
