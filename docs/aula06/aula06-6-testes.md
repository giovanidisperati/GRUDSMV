---
layout: aula
title: "6. Primeiros Passos com Testes"
parent: Aula 06 - Context API, AsyncStorage e MVVM
nav_order: 6
---

## **6. Por falar em testabilidade: nosso primeiro teste com Jest**

Na seção anterior, afirmamos que separar a lógica de negócio da interface torna o código testável. É uma afirmação comum em arquitetura de software — mas afirmações merecem evidências. Vamos produzi-las agora.

Esta seção não tem a pretensão de ser um curso de testes. O objetivo é único e específico: mostrar, com código rodando, que a separação de responsabilidades que fizemos não é apenas organização — ela tem consequências práticas e verificáveis. Testar o hook completo (`useFavorites` com `AsyncStorage` e `Provider`) exige técnicas de *mocking* que veremos em uma aula dedicada. Por ora, vamos até onde conseguimos chegar sem cerimônia.

### **6.1. Configurando o Jest no projeto Expo**

O Expo já vem com Jest pré-configurado. Para verificar, abra o `package.json` e confirme que há uma entrada `jest` e que `jest-expo` está nas dependências de desenvolvimento. Se não estiver, instale:

```bash
npx expo install jest-expo --save-dev
npx expo install @types/jest --save-dev
```

E certifique-se de que o `package.json` contém:

```json
{
  "scripts": {
    "test": "jest"
  },
  "jest": {
    "preset": "jest-expo"
  }
}
```

Para rodar os testes:

```bash
npm test
```

Por convenção, arquivos de teste ficam em uma pasta `__tests__` na raiz do projeto, ou junto ao arquivo que testam com a extensão `.test.ts`. Usaremos a pasta `__tests__`:

```
PokedexApp/
├── __tests__/
│   ├── format.test.ts
│   └── favorites.test.ts
├── src/
│   ├── utils/
│   │   └── format.ts
│   └── contexts/
│       └── FavoritesContext.tsx
```

### **6.2. Primeiro teste: `capitalize`**

Vamos começar com a função mais simples do projeto: `capitalize` em `utils/format.ts`.

```typescript
// utils/format.ts
export const capitalize = (s: string) => s.charAt(0).toUpperCase() + s.slice(1);
```

Esta função não depende de React, de navegação, de API ou de estado. Ela recebe uma string e devolve uma string. É o alvo perfeito para um primeiro teste: zero setup, zero mocks, resultado imediato.

Crie o arquivo `__tests__/format.test.ts`:

```typescript
// __tests__/format.test.ts
import { capitalize } from '../src/utils/format';

describe('capitalize', () => {

  it('coloca a primeira letra em maiúsculo', () => {
    expect(capitalize('pikachu')).toBe('Pikachu');
  });

  it('mantém as letras restantes como estão', () => {
    expect(capitalize('chARIZard')).toBe('CHARIZard');
  });

  it('não quebra com string vazia', () => {
    expect(capitalize('')).toBe('');
  });

});
```

Rode `npm test` e você verá algo assim:

```
PASS  __tests__/format.test.ts
  capitalize
    ✓ coloca a primeira letra em maiúsculo
    ✓ mantém as letras restantes como estão
    ✓ não quebra com string vazia
```

**Anatomia de um teste Jest:**

- `describe`: agrupa testes relacionados sob um nome comum — aqui, todos os testes da função `capitalize`.
- `it` (ou `test`): descreve um comportamento específico em linguagem natural. A convenção é que a frase faça sentido como "it *[faz algo]*".
- `expect(...).toBe(...)`: a asserção. `expect` recebe o valor real; `.toBe` compara com o valor esperado usando igualdade estrita (`===`).

Três testes, três verificações, três linhas verdes. Isso é um teste.

### **6.3. Segundo teste: a lógica pura de favoritos**

Agora vamos um passo além. Lembra que afirmamos que a lógica de `addFavorite`, `removeFavorite` e `isFavorite` é, em essência, uma operação sobre arrays? Vamos isolar e verificar essa lógica diretamente — sem renderizar componentes, sem Provider, sem AsyncStorage.

O ponto arquitetural aqui é preciso: porque separamos a lógica da UI, conseguimos extraí-la e testá-la de forma completamente isolada. Se essa lógica estivesse misturada no JSX de um componente, teríamos que renderizar tudo para verificar qualquer coisa.

Crie o arquivo `__tests__/favorites.test.ts`:

```typescript
// __tests__/favorites.test.ts

// Extraímos e testamos as funções puras de manipulação de favoritos.
// Elas vivem dentro do FavoritesProvider, mas a lógica em si
// é independente de React e de AsyncStorage — opera apenas sobre arrays.

// As três operações que o useFavorites expõe, reimplementadas como funções puras:
const isFavorite = (favorites: number[], id: number): boolean =>
  favorites.includes(id);

const addFavorite = (favorites: number[], id: number): number[] =>
  favorites.includes(id) ? favorites : [...favorites, id];

const removeFavorite = (favorites: number[], id: number): number[] =>
  favorites.filter(favId => favId !== id);


describe('lógica de favoritos', () => {

  describe('isFavorite', () => {
    it('retorna true quando o id está na lista', () => {
      expect(isFavorite([1, 4, 7], 4)).toBe(true);
    });

    it('retorna false quando o id não está na lista', () => {
      expect(isFavorite([1, 4, 7], 25)).toBe(false);
    });

    it('retorna false para uma lista vazia', () => {
      expect(isFavorite([], 1)).toBe(false);
    });
  });

  describe('addFavorite', () => {
    it('adiciona um id à lista', () => {
      expect(addFavorite([1, 4], 7)).toEqual([1, 4, 7]);
    });

    it('não adiciona duplicados', () => {
      expect(addFavorite([1, 4, 7], 4)).toEqual([1, 4, 7]);
    });

    it('adiciona a uma lista vazia', () => {
      expect(addFavorite([], 1)).toEqual([1]);
    });
  });

  describe('removeFavorite', () => {
    it('remove um id da lista', () => {
      expect(removeFavorite([1, 4, 7], 4)).toEqual([1, 7]);
    });

    it('não altera a lista se o id não existe', () => {
      expect(removeFavorite([1, 4, 7], 25)).toEqual([1, 4, 7]);
    });

    it('retorna lista vazia ao remover o único elemento', () => {
      expect(removeFavorite([1], 1)).toEqual([]);
    });
  });

});
```

Rode `npm test` e você verá:

```
PASS  __tests__/favorites.test.ts
  lógica de favoritos
    isFavorite
      ✓ retorna true quando o id está na lista
      ✓ retorna false quando o id não está na lista
      ✓ retorna false para uma lista vazia
    addFavorite
      ✓ adiciona um id à lista
      ✓ não adiciona duplicados
      ✓ adiciona a uma lista vazia
    removeFavorite
      ✓ remove um id da lista
      ✓ não altera a lista se o id não existe
      ✓ retorna lista vazia ao remover o único elemento
```

**Uma observação sobre `.toBe` vs `.toEqual`:**

No teste de `capitalize` usamos `.toBe` — comparação estrita, ideal para primitivos como strings e números. Nos testes de favoritos usamos `.toEqual` para os arrays — ele compara a estrutura e os valores do objeto, não a referência em memória. Dois arrays `[1, 7]` criados separadamente são `toEqual`, mas não são `toBe`.

### **6.4. O que acabamos de provar**

Os nove testes acima verificam o comportamento das três operações centrais do nosso sistema de favoritos sem uma única linha de JSX, sem um Provider, sem mock de AsyncStorage, sem renderização de componente.

Isso não aconteceu por acaso. Aconteceu porque na seção 4 decidimos que a lógica de negócio viveria separada da interface. A testabilidade que descrevemos teoricamente na seção 5.4 está evidenciada aqui de forma concreta.

### **6.5. O que fica para uma aula dedicada**

Testar o hook `useFavorites` *completo* — com o `FavoritesProvider` real, com o `AsyncStorage` sendo chamado de verdade — exige técnicas que ainda não vimos:

- **Mocking de módulos nativos:** o `AsyncStorage` acessa funcionalidades nativas do dispositivo que não existem no ambiente de teste (Node.js). Precisamos substituí-lo por uma implementação simulada com `jest.mock`.
- **`renderHook`:** para testar um hook em isolamento, sem montar um componente completo ao redor dele, usamos `renderHook` da `@testing-library/react-native`.
- **Testes assíncronos:** como o carregamento de favoritos é assíncrono, os testes precisam aguardar a resolução das Promises com `waitFor`.

Essas técnicas formam um conjunto coeso que merece tratamento dedicado. Por ora, o importante é que a fundação está lançada: você sabe o que é um teste, como escrevê-lo e — mais importante — por que a arquitetura que construímos torna escrever testes uma tarefa natural e não uma batalha contra o próprio código.