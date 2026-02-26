---
layout: aula
title: "3 & 4. Componentes e Props"
parent: Aula 03 - React: Componentes, Props e Hooks
nav_order: 2
---

## **2. JSX: JavaScript com aparência de HTML**

Uma das primeiras coisas que chama atenção no React é a **mistura entre código JavaScript e marcação visual**, algo que à primeira vista pode parecer estranho, mas que se revela extremamente poderoso. Essa sintaxe híbrida se chama **JSX** (JavaScript XML).

### 🧬 **2.1 O que é JSX e como ele se relaciona com Componentes?**

JSX é uma extensão de sintaxe para JavaScript que nos permite escrever **elementos da interface de forma semelhante ao HTML**, diretamente dentro do código JavaScript (ou TypeScript). É crucial entender que o JSX não é compreendido nativamente pelos navegadores; por isso, ele é **convertido (transpilado)** para chamadas da função `React.createElement` durante o processo de build.

Por exemplo, o seguinte código JSX:

```jsx
const elemento = <h1>Olá, mundo!</h1>;
```

É transformado internamente em algo como:

```js
const elemento = React.createElement("h1", null, "Olá, mundo!");
```

Esse comportamento permite que possamos **escrever interfaces de forma declarativa**. Em essência, o JSX é "açúcar sintático" sobre `React.createElement`, tornando a criação de **componentes React** mais intuitiva. Com ele, conseguimos expressar visualmente a estrutura da interface dentro do código, facilitando o raciocínio visual (você "vê" a interface no código) e reduzindo a distância entre lógica e apresentação.

É importante frisar: JSX **não é HTML**, mas **parece com HTML** e descreve a interface. Entender que JSX é uma **abstração convertida em código JavaScript** ajuda a evitar confusões comuns, como tentar usar atributos do HTML padrão (por exemplo, `class` em vez de `className`) ou esquecer que por trás da sintaxe existe uma estrutura de componentes. Toda interface construída com React será composta com JSX, onde cada tag é um componente React (ou um elemento JSX simples), podendo ser **combinada, personalizada e estilizada** com facilidade.

### 🧠 **2.2 JSX no React Native: Adaptando para o Mobile**

A versatilidade do JSX se estende ao desenvolvimento mobile com React Native. No entanto, em aplicações React Native, você não utilizará tags HTML como `div`, `span` ou `h1`. Em vez disso, empregará **componentes nativos adaptados**, como:

* `<View>` – equivalente a uma `div` ou `container`
* `<Text>` – para exibir textos
* `<Image>` – para imagens
* `<Button>` – para botões (simples)

Esses componentes são usados da mesma forma que usamos tags HTML com JSX, o que demonstra como o aprendizado de JSX é **facilmente aplicável ao desenvolvimento mobile**. Veja um exemplo de como esses componentes formam uma interface:

```tsx
<View>
  <Text>Bem-vindo à Pokédex</Text>
</View>
```

No dispositivo móvel, este trecho de código resultaria na exibição do texto "Bem-vindo à Pokédex" na tela. O componente `<View>` atua como um contêiner (geralmente invisível, a menos que estilos como cor de fundo ou bordas sejam aplicados) que organiza seus elementos filhos. Dentro dele, o componente `<Text>` é responsável por renderizar a frase especificada. É a combinação desses componentes nativos que constrói a interface visual do aplicativo! Mais adiante, na próxima Aula, veremos isso com mais profundidade. 😊

### ✅ **2.3 Regras importantes do JSX**

Para trabalhar efetivamente com JSX, algumas regras fundamentais devem ser seguidas:

#### 1\. **Apenas um elemento pai**

Todo bloco JSX precisa estar envolvido por **um único elemento pai**. Isso ocorre porque o JSX precisa ser convertido em uma única chamada de função.

```jsx
// ❌ Erro
return (
  <p>Item 1</p>
  <p>Item 2</p>
);

// ✅ Correto
return (
  <div>
    <p>Item 1</p>
    <p>Item 2</p>
  </div>
);
```

Alternativamente, podemos usar o **fragmento vazio** `<>...</>`:

```jsx
return (
  <>
    <p>Item 1</p>
    <p>Item 2</p>
  </>
);
```

#### 2\. **Expressões JavaScript entre chaves `{}`**

Para usar variáveis, chamadas de função ou qualquer lógica JavaScript dentro do JSX, envolvemos a expressão com `{}`:

```jsx
const nome = "Pikachu";

return <p>Nome do Pokémon: {nome}</p>;
```

#### 3\. **Atributos são camelCase e sem aspas duplas para expressões**

Em JSX, os atributos são definidos com camelCase (não com hífen, como em HTML), e seus valores devem ser passados como expressões `{}`, exceto em strings literais:

```jsx
<div style={{ padding: 20 }} />
<img src={imagem} alt="Pokémon" />
<button onClick={() => alert("Clicou!")}>Buscar</button>
```

#### 4\. **Elementos precisam ser fechados corretamente**

JSX exige **elementos autocontidos** (como `<Image />`, `<Input />`) ou com abertura e fechamento explícitos:

```jsx
// ✅ Corretos
<p>Olá</p>
<img src="..." alt="Imagem" />
```

### 💡 **2.4 Exemplo prático com JSX**

Vamos consolidar o que aprendemos com um exemplo prático em um componente React:

```jsx
export default function App() {
  const nome = "Charmander";
  const tipo = "fogo";

  return (
    <div style={{ padding: 20 }}>
      <p style={{ fontSize: 18, fontWeight: "bold" }}>
        Pokémon: {nome}
      </p>
      <p>Tipo: {tipo}</p>
    </div>
  );
}
```
Este é um componente funcional em React chamado App. Ele:

1. Declara duas variáveis com const: nome e tipo, contendo as strings "Charmander" e "fogo".

2. Retorna uma interface JSX com uma <div> contendo dois parágrafos:

 - O primeiro exibe em negrito o nome do Pokémon, utilizando interpolação de variável: Pokémon: {nome}.

 - O segundo mostra o tipo do Pokémon: Tipo: {tipo}.

3. Aplica estilização inline diretamente no JSX com o atributo style, usando sintaxe de objeto JavaScript.

💡 Isso demonstra como dados dinâmicos podem ser facilmente exibidos na interface, e como o JSX permite misturar marcação HTML com lógica JavaScript de forma declarativa e legível.

### **2.5 Em resumo...**

O JSX é a porta de entrada para a construção de interfaces em React e React Native. Ele combina a expressividade do JavaScript com a clareza visual de uma linguagem de marcação. Entender suas regras e saber como combiná-lo com lógica dinâmica é o primeiro passo para criar interfaces ricas e interativas. Ah, e não se preocupe: **no final do Aula vamos construir a PokéDex completa** 🤩

---
