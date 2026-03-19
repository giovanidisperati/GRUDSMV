---
layout: aula
title: "7. Buscando Pokémons na PokéAPI!"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 7
---

## **7. Aplicação prática: buscando dados na PokéAPI**

Agora que entendemos o funcionamento de `useState` e `useEffect`, é hora de aplicá-los juntos para criar uma pequena aplicação que:

* Consulta a PokéAPI;
* Exibe dados básicos de um Pokémon;
* Permite ao usuário digitar um nome e buscar dinamicamente.

Essa será nossa **primeira tela funcional com React**, e ela marcará o início da construção da nossa Pokédex (que evoluíremos, posteriormente, para React Native). 🎯

### 🧪 **Objetivo**

Construir um componente que:

1. Recebe o nome de um Pokémon via campo de texto;
2. Ao clicar em "Buscar", consulta a PokéAPI;
3. Exibe o nome, altura, peso e tipos do Pokémon;
4. Mostra uma mensagem de erro caso o nome seja inválido.

### 🧱 **Passo a passo: estrutura base**

Vamos usar a seguinte estrutura de pastas. Como montaremos o projeto primeiramente com React Web, vamos usar o Vite para criá-lo. O **Vite** é uma ferramenta de build moderna e super rápida para projetos web que oferece **inicialização instantânea** e **atualizações rápidas durante o desenvolvimento**, ideal para projetos React, Vue, entre outros. Não se preocupe muito: só vamos utilizá-lo nesse momento do curso, em que estamos vendo os conceitos de React. Após isso retomaremos o React Native e não precisaremos utilizar o Vite.

Vamos criar o projeto com:

```bash
npm create vite@latest pokedex-react -- --template react-ts
```

Ou, estiver usando o `yarn`:

```bash
yarn create vite pokedex-react --template react-ts
```

Vamos acessar o diretório do projeto com

```bash
cd pokedex-react
```

E depois instalar as dependências com 

```bash
npm install
```

Após isso, vamos criar a pasta de componentes

```bash
mkdir src/components
```

Nossa estrutura de diretórios ficará a seguinte

```
pokedex-react/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   └── Pokedex.tsx      ← Componente principal
│   │   └── Pokedex.css      ← Estilização do componente 
│   ├── App.tsx              ← Componente raiz
│   └── main.tsx             ← Ponto de entrada
├── package.json
├── tsconfig.json
└── vite.config.ts (se usar Vite)
```

Feito isso, vamos ver como ficará nosso código!

### 🧱 **Componente `Pokedex.tsx`**

Crie o arquivo `src/components/Pokedex.tsx`:

```tsx
import React, { useState } from "react";
import "./Pokedex.css";

// Definindo o tipo com base no json para simplificar a implementação
type Pokemon = {
  name: string;
  height: number;
  weight: number;
  sprites: {
    front_default: string | null;
  };
  types: Array<{
    type: { name: string };
  }>;
};

export default function Pokedex() {
  const [nome, setNome] = useState("");
  const [carregando, setCarregando] = useState(false);

  const [pokemon, setPokemon] = useState<Pokemon | null>(null);
  const [erro, setErro] = useState("");

  const buscarPokemon = async () => {
    if (!nome.trim()) return;

    setCarregando(true);
    setErro("");
    setPokemon(null);

    try {
      const resposta = await fetch(
        `https://pokeapi.co/api/v2/pokemon/${nome.toLowerCase()}`
      );
      if (!resposta.ok) throw new Error("Pokémon não encontrado");

      // Convertemos o JSON dizendo ao TS que ele tem formato Pokemon 
      const dados: Pokemon = await resposta.json();
      setPokemon(dados);
    } catch (e) {
      setErro("Pokémon não encontrado 😢");
    } finally {
      setCarregando(false);
    }
  };

  return (
    <div className="pokedex-container">
      <h2 className="pokedex-title">🔎 Pokédex</h2>

      <input
        className="pokedex-input"
        type="text"
        placeholder="Digite o nome do Pokémon"
        value={nome}
        onChange={(e) => setNome(e.target.value)}
      />

      <button className="pokedex-button" onClick={buscarPokemon}>
        Buscar
      </button>

      {carregando && <p className="pokedex-loading">Carregando...</p>}
      {erro && <p className="pokedex-error">{erro}</p>}

      {pokemon && (
        <div className="pokedex-card">
          <h3 className="pokedex-name">{pokemon.name}</h3>
          {pokemon.sprites.front_default && (
            <img
              src={pokemon.sprites.front_default}
              alt={pokemon.name}
              className="pokedex-image"
            />
          )}
          <p>
            <strong>Altura:</strong> {pokemon.height * 10} cm
          </p>
          <p>
            <strong>Peso:</strong> {pokemon.weight / 10} kg
          </p>
          <p>
            <strong>Tipos:</strong>{" "}
            {pokemon.types.map((t) => t.type.name).join(" / ")}
          </p>
        </div>
      )}
    </div>
  );
}
```

No código acima, começamos importando dois itens da biblioteca React – o objeto-módulo padrão **React** e, entre chaves, o **useState**. Essa sintaxe é chamada *named import*: quando a biblioteca exporta várias entidades, escolhemos pelo nome apenas as que nos interessam (aqui, o hook useState), o que torna o código mais claro. Em seguida carregamos o CSS local para aplicar estilos de forma local ao componente.

Logo abaixo definimos **Pokemon**, um *type alias* do TypeScript que descreve exatamente o formato do objeto retornado pela PokéAPI: nome, altura, peso, um caminho para a miniatura e uma lista de tipos. Declarar esse tipo permite ao compilador verificar acesso a propriedades inexistentes e habilita auto-completar durante a escrita do código.

Dentro do componente funcional **Pokedex** declaramos quatro estados usando o hook *useState*. Cada chamada a useState devolve um par `[valor, setValor]`; armazenamos cada par em constantes porque o ponteiro para o valor e a função de atualização nunca mudam (o conteúdo muda, mas a referência permanece). Assim:

* **nome / setNome** guarda o texto do input.
* **carregando / setCarregando** indica se há requisição em andamento.
* **pokemon / setPokemon** armazena o resultado da busca; o tipo `Pokemon | null` deixa explícito que o estado começa vazio.
* **erro / setErro** guarda mensagens de falha para exibir ao usuário.

Todas as variáveis foram declaradas com **const** porque em JavaScript/TypeScript `const` congela apenas a referência, não o conteúdo interno. Como não reatribuiremos novos pares de estado, faz sentido usar `const` para sinalizar essa imutabilidade estrutural.

"Professor, mas isso é esquisito, hein?". Calma! Vamos reforçar o entendimento. 👽🤓

Aqui em JavaScript/TypeScript, **`const` não congela o universo inteiro** – ela só diz que o **apelido** (a referência) não vai ser trocado **durante aquela execução** da função.

Quando o React re-renderiza o componente, ele **chama a função `Pokedex` de novo** do zero. Isso significa que - dentro dessa chamada - o código faz:

```ts
const [nome, setNome] = useState("");
```

e cria **novas** variáveis locais chamadas `nome` e `setNome`. Na execução anterior elas já “morreram”; estas são outras, fresquinhas. Então não há nenhuma violação do `const`: cada versão da variável vive apenas durante o render em que foi criada.

Além disso, o que realmente guarda o estado é uma estrutura interna do React.
Quando chamamos `setNome("Pikachu")`, **não mudamos a variável `nome` diretamente**; pedimos ao React:

> “Guarde ‘Pikachu’ como o novo estado, por favor.”

O React salva isso lá dentro, agenda um novo render, e *na próxima chamada* da função ele devolve o par `[ "Pikachu", setNome ]`. A nossa variável `nome` continua de “const” intacta - ela simplesmente começa a vida já valendo `"Pikachu"`.

Ou seja, se dentro do **mesmo** ciclo de execução (isto é, enquanto o código daquela função ainda está rodando) tentarmos reatribuir uma variável declarada com `const`, será disparado um erro de tempo de execução:

```ts
const pokemon = "Pikachu";
pokemon = "Charmander";   // TypeError: Assignment to constant variable.
```

O motor JavaScript impede a reatribuição porque `const` fixa o **apelido** (`nome`) ao primeiro valor (ou referência) que você deu para ele naquela execução. Agora, no caso do React, quando você quer mudar o valor “Pikachu” para Charmander”, **não** faz `pokemon = "Charmander"`; você chama `setPokemon("Charmander")`. Isso grava o novo valor num “cofre” interno do React e agenda outro render. Na próxima vez que a função componente for invocada, o React cria uma **nova** variável `const pokemon`, já iniciada como `"Charmander"`, sem jamais ter quebrado a regra de imutabilidade dentro de um único ciclo.

Resumindo: **`const` garante imutabilidade da **referência** dentro daquela execução;** o estado muda por fora, entre execuções, e o React devolve o novo valor quando a função roda novamente. Não há, portanto, nenhuma contradição, apenas um "truque" elegante do ciclo de renderização!

Beleza, reforçado isso, vamos continuar a explicação!

A função **buscarPokemon** concentra a lógica de busca. É uma arrow-function assinalada a uma constante – novamente indicando que o identificador não mudará. Ela é marcada *async* para que possamos usar `await` e escrever código assíncrono com aparência sequencial: chamamos `fetch`, esperamos a resposta, transformamos em JSON e só então seguimos. Esse estilo evita a pirâmide de *then()* e torna o fluxo de erro mais linear com `try/catch`. A primeira linha da função aborta se o usuário enviou campo vazio (`!nome.trim()`). Depois mudamos o estado para o ciclo “carregando”: limpamos erros, limpamos resultado anterior e ativamos o "spinner". Quando a `fetch` resolve, verificamos `resposta.ok`. Caso contrário, lançamos erro para cair no `catch`. No caminho feliz, tipamos o JSON como `Pokemon` para que o TypeScript passe a enxergar o objeto com o formato correto e então gravamos em **pokemon**; no `finally` desligamos o indicador de carregamento.

Já a **renderização condicional**, na parte do `return` que está abaixo da função **buscarPokemon**, é construída só com JSX: primeiro a estrutura fixa (título, input e botão). O atributo `onChange` do input usa `setNome` para sincronizar cada tecla com o estado; o botão dispara `buscarPokemon`. Em seguida, três blocos condicionais: se `carregando` verdadeiro mostramos “Carregando…”, se `erro` possuir texto mostramos o erro, e se `pokemon` estiver preenchido exibimos o card. O card utiliza as informações já validadas pelo tipo: nome em título, imagem se existir, altura (convertida para centímetros), peso (em quilos) e a lista de tipos concatenada. Quando qualquer estado muda via `setX`, o React refaz a função de componente, calcula uma nova árvore virtual e aplica ao DOM apenas as diferenças, garantindo a reatividade.

Por fim, a palavra-chave **export default** indica que este arquivo exporta uma entidade principal – o componente Pokedex. Quem importar o caminho `./Pokedex` receberá essa função. Com isso o componente entra na árvore de renderização da aplicação, e toda a lógica descrita acima passa a reger a interface.

### 💃 **Estilo `Pokedex.css`**

```css
.pokedex-container {
  padding: 20px;
  max-width: 500px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
  text-align: center;
  color: #333;
}

.pokedex-title {
  font-size: 24px;
  margin-bottom: 16px;
}

.pokedex-input {
  padding: 10px;
  width: 100%;
  margin-bottom: 10px;
  border-radius: 4px;
  border: 1px solid #ccc;
  font-size: 16px;
}

.pokedex-button {
  padding: 10px 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

.pokedex-loading {
  margin-top: 10px;
  color: #888;
}

.pokedex-error {
  margin-top: 10px;
  color: red;
  background: #ffe0e0;
  padding: 8px;
  border-radius: 4px;
}

.pokedex-card {
  margin-top: 20px;
  background-color: #f8f8f8;
  padding: 16px;
  border-radius: 8px;
  text-align: left;
}

.pokedex-name {
  text-transform: capitalize;
  margin-bottom: 10px;
}

.pokedex-image {
  display: block;
  margin: 0 auto 10px;
}
```

### 🧩 **Usando no `App.tsx`**

```tsx
import React from "react";
import Pokedex from "./components/Pokedex";

export default function App() {
  return (
    <div>
      <Pokedex />
    </div>
  );
}
```

**Para evitar conflitos de estilo, não se esqueça de comentar a linha `import './index.css'` em seu arquivo `main.tsx`**.

Depois disso, basta iniciar o ambiente de desenvolvimento:

```bash
npm run dev
```

E a aplicação será servida em **[http://localhost:5173](http://localhost:5173)** – abra essa URL no navegador para ver a Pokédex em funcionamento. 🤩

Se quiser acompanhar uma breve demonstração do código acima, confira o vídeo **“PokéDex em React”**:

[Assista no YouTube – PokéDex em React](https://youtu.be/E2qN0vF5GXU)

### Em resumo …

Este exercício reúne todos os conceitos centrais que vimos nesta aula. O vídeo acima mostra, passo a passo, como o código foi construído – uma ótima forma de reforçar o aprendizado antes de avançarmos! 😊