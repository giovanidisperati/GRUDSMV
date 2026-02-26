---
layout: aula
title: "3. Componentes"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 3
---

## **3. Componentes: a base de tudo no React**

Como mencionamos anteriormente, em React, **componentes são a unidade fundamental de construção da interface**. Eles permitem dividir a UI em partes reutilizáveis, organizadas, independentes e fáceis de manter.

### 🧩 **3.1 O que é um componente?**

Um **componente** React é essencialmente uma função JavaScript (ou uma classe, em abordagens mais antigas) que **retorna JSX** para descrever o que deve ser renderizado na tela. Cada componente pode receber dados de entrada, chamados **props** (propriedades), e produzir uma saída visual — geralmente um ou mais elementos de interface.

```tsx
// Exemplo de um componente funcional simples em React
function Saudacao() {
  return <p>Olá, mundo!</p>; 
}
```

Assim como funções comuns, componentes preferencialmente devem ser:

  * Pequenos e focados (ex: um botão customizado, um campo de entrada estilizado).
  * Combinados para formar componentes maiores e mais complexos (ex: um card de produto, um formulário de login, uma tela completa).
  * Reutilizados em diversas partes da aplicação com diferentes dados.

### 🛠️ **3.2 Criando e usando componentes**

Podemos criar componentes em arquivos separados para melhor organização ou, para exemplos simples, diretamente no arquivo principal da aplicação (como `App.tsx` ou `index.tsx`).

#### Exemplo básico de um componente `PokemonCard`:

```tsx
// Componente PokemonCard
function PokemonCard() {
  return (
    <div style={{ border: '1px solid #ccc', padding: '10px', margin: '10px', borderRadius: '5px' }}>
      <p><strong>Nome:</strong> Pikachu</p>
      <p><strong>Tipo:</strong> Elétrico</p>
    </div>
  );
}
```

#### E usá-lo dentro do componente principal:

```tsx
// Supondo que este seja o seu componente App principal
export default function App() {
  return (
    <div style={{ padding: '20px' }}>
      <h1>Minha Pokédex</h1>
      <PokemonCard />
      <PokemonCard /> {/* Podemos reutilizar o componente 😊 */}
    </div>
  );
}
```

🔁 Essa estrutura pode ser reutilizada para exibir diferentes Pokémon. Para isso, utilizamos **props**, que nos permitem passar dados para os componentes, tornando-os dinâmicos. Veremos como fazer isso em detalhes na próxima seção.

### 📄 **3.3 Estrutura recomendada de diretórios**

Para organizar melhor projetos maiores, é uma prática comum criar componentes em seus próprios arquivos e agrupá-los em um diretório específico, como `src/components`. Por exemplo, ao construir nossa PokéDex teríamos algo como:

```
pokedex/
├── public/
├── src/
│   ├── components/
│   │   └── PokemonCard.tsx
│   ├── App.tsx
│   └── index.tsx  // ou main.tsx, dependendo da configuração
└── package.json
```

No arquivo `src/components/PokemonCard.tsx` teríamos:

```tsx
import React from 'react'; 
// Boa prática importar React, embora opcional em versões mais recentes com o novo JSX transform

// Definimos e exportamos o componente PokemonCard
export function PokemonCard() {
  return (
    <div style={{ border: '1px solid #ccc', padding: '10px', margin: '10px', borderRadius: '5px' }}>
      <p><strong>Nome:</strong> Pikachu</p>
      <p><strong>Tipo:</strong> Elétrico</p>
    </div>
  );
}
```

Este arquivo define um componente React funcional chamado `PokemonCard`. Neste caso, o componente PokemonCard retorna um elemento div com alguns estilos básicos para parecer um "card", contendo dois parágrafos (<p>) que exibem informações fixas sobre um Pokémon (Nome e Tipo). A declaração export function torna este componente disponível para ser usado em outros arquivos do nosso projeto!

E no `App.tsx` (ou onde você for usar o componente):

```tsx
import React from 'react';
import { PokemonCard } from './components/PokemonCard'; // Importamos o componente

export default function App() {
  return (
    <div style={{ padding: '20px' }}>
      <h1>Minha Pokédex</h1>
      <PokemonCard />
      {/* Você pode adicionar mais instâncias de PokemonCard aqui */}
    </div>
  );
}
```

Já aqui temos o ponto de entrada ou um componente pai da nossa aplicação, e estamos integrando (ou "usando") o `PokemonCard`. Primeiro, importamos o componente `PokemonCard` usando a sintaxe `import { PokemonCard } from './components/PokemonCard';`. Isso diz ao nosso código onde encontrar a definição do componente que criamos.

Em seguida, dentro do JSX retornado pelo componente App, simplesmente usamos o componente importado como se fosse uma tag HTML personalizada: `<PokemonCard />`. O React entende essa sintaxe e renderiza o HTML/DOM que o componente PokemonCard retorna no local onde ele foi usado dentro do App.
Essa abordagem modular, onde criamos pequenas peças de UI (componentes como PokemonCard) e depois as usamos para construir interfaces maiores (como o App), exemplifica a base do desenvolvimento com React, promovendo reusabilidade e organização do código. 😉

### 🧠 **3.4 Por que componentes são importantes?**

Como vimos, a componentização é um dos pilares do React e traz diversos benefícios:

  * **Separação de responsabilidades (Separation of Concerns):** Cada componente gerencia sua própria lógica e renderização, tornando o código mais fácil de entender e manter. Uma parte da interface pode ser construída e testada de forma isolada.
  * **Reutilização:** Componentes podem ser usados múltiplas vezes na mesma aplicação ou até mesmo em projetos diferentes, economizando tempo e esforço. Por exemplo, um componente de `Botao` customizado pode ser usado em vários formulários e seções do site.
  * **Testabilidade e Manutenção:** Isolar a funcionalidade em componentes menores facilita a escrita de testes unitários e a depuração. Alterações em um componente têm menos probabilidade de afetar outras partes da aplicação.
  * **Composição e Escalabilidade:** Interfaces complexas podem ser construídas compondo componentes menores, como blocos de Lego. Isso torna a aplicação mais escalável e gerenciável à medida que cresce.
  * **Produtividade da Equipe:** Diferentes desenvolvedores podem trabalhar em componentes distintos simultaneamente, melhorando a produtividade da equipe.

### **✅ 3.5 Conclusão**

Compreender o conceito de **componentes** é essencial para qualquer desenvolvimento com React. Eles são mais do que blocos visuais — são estruturas lógicas reutilizáveis que tornam o código modular, testável e escalável. A partir do próximo tópico, veremos como **passar dados para os componentes** usando **props**, o que nos permitirá criar componentes verdadeiramente dinâmicos e reutilizáveis, como o nosso `PokemonCard` exibindo informações de diferentes Pokémon. 🤠

---
