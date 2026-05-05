---
layout: aula
title: "5. Refatoração e Padrão MVVM"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 5
---

## **5. Refatoração e Arquitetura: Aplicando o Padrão MVVM na Prática**

Até agora, focamos na implementação técnica: usamos a `Context API` para o estado global e o `AsyncStorage` para a persistência. Sem perceber, a estrutura que criamos ao fazer isso nos alinhou naturalmente com o padrão **MVVM** que discutimos na Aula 05. As peças se encaixam da seguinte forma:

  * **Model:** Nossa camada de serviços (`services/api.ts`), responsável por buscar os dados brutos da PokéAPI.
  * **View:** Os componentes de interface — `PokemonCard`, `PokedexScreen`, `PokemonDetailsScreen` — cuja responsabilidade é exibir os dados e capturar as interações do usuário.
  * **ViewModel:** O hook customizado `useFavorites`. Ele é o intermediário que prepara os dados para a *View* e contém toda a lógica de negócio, sem nunca conhecer os detalhes da interface.

Nesta seção, vamos aprofundar essa conexão e entender como essa arquitetura torna o código mais limpo, testável e fácil de evoluir.

### **5.1. O que são Hooks Customizados?**

Na Aula 05, mencionamos que a filosofia do MVVM se alinha perfeitamente com o uso de **Hooks Customizados** para servir como o *ViewModel*. Antes de passarmos a como isso funciona na prática com o `useFavorites`, é importante entendermos o conceito em si.

Até agora, utilizamos hooks fornecidos pelo próprio React (`useState`, `useEffect`). No entanto, o verdadeiro poder do ecossistema de hooks está na capacidade de **criar os nossos próprios**.

Um **Hook Customizado** é uma função JavaScript reutilizável cujo nome, por convenção, sempre começa com `use` (ex: `useFavorites`). Sua principal característica é que ela pode **chamar outros hooks** dentro dela — como `useState`, `useEffect` ou até outros hooks customizados.

**Por que criar um Hook Customizado?**

O objetivo é **extrair e compartilhar lógica com estado** entre diferentes componentes. Imagine que vários componentes no nosso app precisassem buscar dados de uma API. Em cada um deles, repetiríamos a mesma lógica:

  * Um `useState` para guardar os dados.
  * Um `useState` para controlar o estado de `loading`.
  * Um `useState` para armazenar uma mensagem de `error`.
  * Um `useEffect` para fazer a chamada quando o componente monta.

Isso é repetitivo e propenso a erros. Com um Hook Customizado como um hipotético `useFetch(url)`, poderíamos encapsular toda essa lógica em um só lugar. Assim, qualquer componente simplesmente faria:

```javascript
const { data, loading, error } = useFetch('https://pokeapi.co/api/v2/pokemon/pikachu');
```

O componente recebe os dados prontos para usar, sem se preocupar com os detalhes de como foram buscados ou gerenciados.

**As Regras de Ouro dos Hooks Customizados:**

1.  **O nome deve começar com `use`:** Isso não é apenas uma convenção. É o que permite que o linter do React verifique se as "Regras dos Hooks" (como não chamar hooks dentro de condicionais) estão sendo seguidas corretamente.
2.  **Eles compartilham lógica, não estado:** Cada componente que chama o mesmo hook recebe sua **própria versão isolada do estado**. O `useFetch` para o "Pikachu" e o `useFetch` para o "Charmander" terão seus próprios `data`, `loading` e `error`, completamente independentes. Isso é diferente de um Contexto, onde o estado é compartilhado — um detalhe importante que veremos na seção seguinte.

É exatamente por essa capacidade de encapsular lógica que os Hooks Customizados se tornam a ferramenta perfeita para implementar o *ViewModel* no React.

### **5.2. `useFavorites` como ViewModel: teoria encontra prática**

Na Aula 05, imaginamos um `usePokedexViewModel` hipotético como exemplo de ViewModel implementado via Custom Hook. O nosso `useFavorites` é a primeira implementação real desse conceito no projeto. Vamos analisar como ele cumpre cada responsabilidade que definimos para um ViewModel:

| Responsabilidade do ViewModel | Como `useFavorites` cumpre |
| :--- | :--- |
| Expor dados e estado observável | Expõe `favorites` |
| Expor comandos que a View invoca | Expõe `addFavorite`, `removeFavorite`, `isFavorite` |
| Conter toda a lógica de negócio | Gerencia a interação com `AsyncStorage`, deduplicação, persistência |
| Não ter referência direta à View | Não importa nenhum componente de UI; pode ser usado em qualquer tela |

O código do hook em si é enganosamente simples:

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

Qualquer componente que o chame não precisa saber sobre `useContext`, sobre o `FavoritesProvider` ou sobre o `AsyncStorage`. Ele apenas pede: "me dê a lógica de favoritos" — e recebe de volta um conjunto de dados e funções prontos para usar.

O resultado direto disso é que nossos componentes de *View* se tornam incrivelmente mais limpos e declarativos. Veja como o `PokemonCard` interage com os favoritos após adotar o `useFavorites`:

```typescript
// components/PokemonCard.tsx (trecho relevante)

// 1. Apenas importamos o hook — essa é a nossa única conexão com a lógica
import { useFavorites } from '../contexts/FavoritesContext';

export const PokemonCard = ({ pokemon }: { pokemon: Pokemon }) => {
  // 2. Consumimos o ViewModel para obter o estado e os comandos
  const { addFavorite, removeFavorite, isFavorite } = useFavorites();
  const favorite = isFavorite(pokemon.id);

  // 3. A função de handle apenas delega a ação para o ViewModel;
  //    o card não sabe como a adição ou remoção funciona por dentro
  const handleToggleFavorite = () => {
    if (favorite) { removeFavorite(pokemon.id); }
    else          { addFavorite(pokemon.id); }
  };

  return (
    // ...
    // 4. A renderização é puramente declarativa: exibe o que o ViewModel informa
    <Text>{favorite ? '⭐' : '☆'}</Text>
    // ...
  );
};
```

O `PokemonCard` não tem `useState`, não tem `useEffect` e não tem conhecimento sobre `AsyncStorage`. Ele apenas pergunta ao ViewModel o que deve exibir e delega as ações. Se amanhã decidirmos salvar os favoritos numa nuvem em vez do `AsyncStorage`, o `PokemonCard` não precisará mudar uma linha — apenas o `FavoritesProvider` interno ao hook será alterado.

### **5.3. MVVM no React é Pragmático, não Purista**

É fundamental reconhecer que, em React Native, a linha entre *View* e *ViewModel* é mais fluida do que em implementações clássicas do MVVM em plataformas como Android com Java. Essa não é uma falha — é uma característica do paradigma funcional reativo, e entendê-la evita uma confusão comum.

Componentes como `PokemonDetailsScreen` são naturalmente responsáveis por **duas coisas ao mesmo tempo**: definir a interface (o JSX) *e* gerenciar o estado local necessário para ela (`useState` para `pokemon`, `isLoading`, `error`). Essa fusão de View e ViewModel no mesmo componente é chamada de **"Component-as-ViewModel"** e é a abordagem padrão, pragmática e idiomática do React.

O ponto importante é que isso **não viola o MVVM** — ele apenas o adapta ao paradigma funcional. A chave para manter a separação de responsabilidades é:

1.  **Lógica de negócio compartilhada ou complexa → Hook Customizado ou Contexto:** Tudo que precisa ser reutilizado entre telas ou que envolve regras não-triviais (como a persistência de favoritos) sai do componente e vai para um hook. É o que fizemos com `useFavorites`.
2.  **Lógica específica daquela tela → dentro do componente:** O estado de `isLoading` e `error` da `PokemonDetailsScreen` não faz sentido em nenhum outro lugar. Mantê-lo no componente é a decisão correta, não uma concessão.

A divisão que fizemos no projeto pode ser visualizada assim:

```
PokemonDetailsScreen.tsx        → View + ViewModel local (carregamento, erro)
useFavorites (FavoritesContext) → ViewModel compartilhado (favoritos, persistência)
services/api.ts                 → Model (dados brutos da API)
```

Para projetos React Native, esta interpretação pragmática, componentes gerenciam seu próprio estado de UI local enquanto hooks customizados centralizam a lógica compartilhada, é o que a comunidade considera boas práticas, e é o que separa um protótipo de um produto sustentável.

### **5.4. Benefícios Visíveis: Testabilidade e Separação de Responsabilidades**

A estrutura que construímos traz dois benefícios práticos imediatos que são pilares no desenvolvimento de software de qualidade.

**Separação de Responsabilidades (SoC) na Prática**

Com a nova estrutura, cada parte tem um papel único e bem definido:

  * **View (`PokemonCard`, `PokedexScreen`):** exibe a interface e notifica o ViewModel sobre ações do usuário. Não sabe como os favoritos são salvos ou gerenciados.
  * **ViewModel (`useFavorites`):** contém a lógica de negócio — adicionar, remover, persistir. Serve os dados para a View, mas não se importa com a aparência dela.

Essa separação torna o código muito mais fácil de depurar. Se houver um bug visual, o problema está na *View*. Se os favoritos não estão sendo salvos corretamente, o problema está no *ViewModel*. Sem essa separação, essas duas categorias de problema estariam misturadas no mesmo arquivo.

**Testabilidade Aprimorada**

Este é talvez o benefício mais poderoso. Como o `useFavorites` é essencialmente uma função JavaScript — não um componente com JSX — podemos testar toda a sua lógica de forma isolada, sem renderizar um único elemento visual. É possível escrever testes automatizados para verificar:

  * Se `addFavorite` realmente adiciona um ID ao array sem duplicar.
  * Se `removeFavorite` o remove corretamente.
  * Se `isFavorite` retorna `true` ou `false` como esperado.
  * Se a interação com o `AsyncStorage` funciona corretamente, inclusive sob falhas simuladas.

Isso está perfeitamente alinhado com o que vimos na Aula 05: o MVVM proporciona testabilidade alta porque a lógica de negócio é completamente desacoplada da camada de renderização. Ao adotar esse padrão, não estamos apenas escrevendo código que funciona hoje, mas construindo uma base que pode crescer, ser modificada e testada com confiança — garantindo a qualidade da aplicação a longo prazo.