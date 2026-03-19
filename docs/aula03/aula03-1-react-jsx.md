---
layout: aula
title: "1. Introdução ao React e JSX"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 1
---

## **1. Introdução ao React**

### 📱 **1.1 React como base para o React Native**

Apesar de React Native usar componentes visuais diferentes (como `View`, `Text`, `Button`, em vez de `div`, `span`, `input`), **a lógica que sustenta os dois é a mesma**:

* Ambos usam **JSX** para descrever a interface de forma declarativa;
* Ambos se estruturam em **componentes reutilizáveis**;
* Ambos gerenciam dados com **estado (`useState`)** e respondem a mudanças com **efeitos (`useEffect`)**;
* Ambos tratam a interface como uma **função pura do estado atual**.

Portanto, **dominar os fundamentos do React** é um passo obrigatório para criar aplicações robustas, escaláveis e compreensíveis com React Native.

### 🧠 **1.2 Pensando em interfaces como funções do estado**

O React propõe uma forma diferente de construir interfaces: **declarativa e orientada a dados**.

Antes dele, a maior parte das interfaces era construída com **manipulações diretas do DOM**, instrução por instrução, como em um algoritmo imperativo. Com React, dizemos *"como a interface deve ser"* com base em seus **dados**, e a biblioteca se encarrega de mantê-la sincronizada com o estado do sistema.

Por exemplo:

```js
<p>
  {contador > 0 ? "Contador Ativo" : "Zerado"}
</p>
```

O React **reavaliará essa expressão automaticamente sempre que `contador` mudar**. Essa abordagem declarativa torna o código mais legível e previsível, especialmente à medida que a interface cresce.

##### 🤯 **1.3 Vamos comparar com jQuery**

Imagine que você estivesse usando jQuery para fazer a mesma coisa:

```html
<p id="status"></p>
```

```js
let contador = 0;

function atualizarStatus() {
  if (contador > 0) {
    $("#status").text("Contador Ativo");
  } else {
    $("#status").text("Zerado");
  }
}

// toda vez que o contador mudar, você precisa lembrar de chamar isso:
contador++;
atualizarStatus();
```

Na abordagem com jQuery, **você precisa lembrar de atualizar manualmente o DOM** toda vez que o valor mudar. Isso funciona, mas conforme a aplicação cresce, manter o DOM em sincronia com os dados se torna **uma tarefa difícil e sujeita a erros**.

> 🔍 **1.4 O que é DOM?**
> DOM é a sigla para *Document Object Model* — ou Modelo de Objeto de Documento. Ele representa a estrutura da página HTML como uma árvore de elementos que pode ser manipulada via JavaScript.
> Se quiser aproveitar para relembrar esse conceito, veja esta explicação da MDN:
> 👉 [https://developer.mozilla.org/pt-BR/docs/Web/API/Document\_Object\_Model](https://developer.mozilla.org/pt-BR/docs/Web/API/Document_Object_Model)

Com React, **essa sincronização é automática**. Você apenas define *como a interface deve parecer com base nos dados atuais*, e o React faz o trabalho pesado de atualização para você. Isso é o que chamamos de **programação declarativa** — e é uma das maiores revoluções trazidas por essa biblioteca.

### ⚙️ **1.5 Componentes, props e estado**

Nas próximas seções vamos nos aprofundar nos três pilares do React:

1. **Componentes** – funções que retornam a interface.
2. **Props** – parâmetros externos passados aos componentes.
3. **Estado (`useState`)** – dados internos que controlam o que é exibido.

Juntos, esses conceitos permitem criar aplicações que **se atualizam automaticamente com base nos dados**, sem precisar manipular a tela “na mão”.

É importante ter em mente que **React é sobre composição**.

Componentes em React são como **blocos de Lego**: pequenos, reutilizáveis e fáceis de encaixar. Criamos componentes que representam partes da interface (como um botão, uma lista, um card), e os **combinamos** para formar telas completas. Esse princípio de **composição** é central no React e será **amplamente utilizado no React Native**, onde montaremos telas como `HomeScreen`, `LoginForm`, entre outros, a partir de blocos menores.

E até aqui, exploramos JavaScript, TypeScript e até criamos uma pequena Pokedex via linha de comando. Mas, para transformar essa lógica em **interfaces visuais interativas em aplicativos mobile**, precisamos entender:

* Como **criar componentes reutilizáveis**;
* Como **passar dados entre componentes** (props);
* Como **manter a interface sincronizada com dados internos** (estado);
* Como **reagir a eventos do ciclo de vida** (efeitos colaterais).

Esses são os fundamentos da programação em React e dominá-los é essencial antes de trabalhar com botões, listas, imagens e chamadas a API no mundo mobile com React Native. 🧑‍💻

