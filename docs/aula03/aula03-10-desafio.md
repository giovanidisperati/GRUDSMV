---
layout: aula
title: "10. Desafio - Construindo seu React!"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 10
---

## Construindo seu próprio React!
*Baseado no artigo original 'Build your own React', publicado por Rodrigo Pombo.*

Ao longo desta aula, você aprendeu a *usar* o React: componentes, props, `useState`, `useEffect`. Agora vamos um nível abaixo: entender como esse motor funciona internamente. Para isso, construiremos uma versão simplificada da biblioteca `Didact`, criada pelo autor Rodrigo Pombo, que tem por ideia demonstrar os princípios do React. Nossa implementação seguirá a arquitetura real do React, sem as otimizações de código de produção mas com os mesmos princípios estruturais.

### O React como problema de estruturas de dados

Antes de listar os mecanismos, vale enxergar o problema pelo ângulo certo: **o React é, em sua essência, um problema de sincronização entre duas estruturas de dados e o DOM**.

A interface de um aplicativo pode ser representada como uma **árvore de nós**, onde cada nó corresponde a um componente ou elemento visual. O React mantém não uma, mas **duas versões dessa árvore em memória** ao mesmo tempo — a árvore atual (o que está no DOM agora) e a árvore em construção (o que o DOM deve se tornar). O trabalho do React é calcular a diferença entre elas e aplicar apenas o necessário.

Cada nó dessa árvore — chamado **fiber** — não é um nó de árvore binária convencional. Ele usa uma representação conhecida como **left-child right-sibling**: em vez de guardar uma lista de filhos, cada fiber armazena apenas três ponteiros:

```
fiber {
  child    → primeiro filho
  sibling  → próximo irmão
  parent   → nó pai
}
```

Essa estrutura transforma qualquer árvore de grau arbitrário em algo percorrível com ponteiros simples, exatamente como uma árvore binária. A travessia segue uma ordem determinística: desce pelo `child`, avança pelo `sibling`, sobe pelo `parent` — garantindo que todo nó seja visitado exatamente uma vez, sem recursão e sem pilha auxiliar explícita.

Cada fiber também carrega um quarto ponteiro, `alternate`, que aponta para sua versão anterior na outra árvore. Isso cria um grafo de duas camadas sobrepostas — a árvore "atual" e a árvore "em progresso" — ligadas nó a nó pelo `alternate`.

Por fim, cada fiber de componente funcional possui um **array de hooks**. Esse array é percorrido por índice a cada renderização: a primeira chamada a `useState` lê o slot 0, a segunda lê o slot 1, e assim por diante. O estado não vive no componente — vive no nó da árvore. O componente é apenas uma função; quem tem memória é a fiber.

Resumindo a estrutura completa:

```
Fiber Tree (árvore left-child right-sibling)
│
├── cada nó (fiber) contém:
│     ├── child / sibling / parent   → navegação na árvore
│     ├── alternate                  → ponteiro para o nó equivalente
│     │                                na outra versão da árvore
│     ├── dom                        → referência ao nó real no DOM
│     ├── effectTag                  → PLACEMENT | UPDATE | DELETION
│     └── hooks[]                    → array de estados locais do componente
│
├── currentRoot   → árvore refletida no DOM agora
└── wipRoot       → árvore sendo calculada (work-in-progress)
```

Com essa estrutura em mente, o ciclo completo de uma atualização de estado é o seguinte:

**1. Disparo** — O usuário chama `setState(action)`. A ação é enfileirada no array `queue` do hook correspondente. Uma nova `wipRoot` é criada apontando para a `currentRoot` como `alternate`, e `nextUnitOfWork` é definido para iniciar o trabalho.

**2. Render Phase (interruptível)** — O Scheduler executa `performUnitOfWork` fiber por fiber, cedendo o controle ao browser entre cada unidade via `requestIdleCallback`. Para cada fiber, a função `reconcileChildren` compara o filho da fiber atual com o filho equivalente na `alternate`, atribuindo `effectTags`. Nenhuma mudança no DOM ocorre aqui.

**3. Commit Phase (atômica)** — Quando não há mais `nextUnitOfWork`, a `wipRoot` está completamente anotada. A `commitRoot` percorre toda a árvore e aplica as mudanças ao DOM de acordo com cada `effectTag`. Só então `currentRoot = wipRoot`, e a árvore em progresso torna-se a árvore atual.

**4. Próximo render** — O ciclo recomeça. A antiga `currentRoot` passa a ser o `alternate` da nova `wipRoot`.

```
setState(action)
     │
     ▼
queue.push(action)
nova wipRoot criada
     │
     ▼
┌─────────────────────────────┐
│  RENDER PHASE (interruptível)│
│  performUnitOfWork           │
│  reconcileChildren           │
│  → anota effectTags          │
└──────────────┬──────────────┘
               │ (nenhuma mudança no DOM)
               ▼
┌─────────────────────────────┐
│  COMMIT PHASE (atômica)      │
│  commitRoot                  │
│  → aplica PLACEMENT          │
│  → aplica UPDATE             │
│  → aplica DELETION           │
└──────────────┬──────────────┘
               │
               ▼
     currentRoot = wipRoot
     (DOM sincronizado)
```

O fluxo de dados no React respeita a direção da árvore:

**Props** descem. Um componente pai passa dados ao filho no momento em que cria a fiber filho — as props são armazenadas no campo `props` da fiber e lidas quando a função do componente é executada. Não há canal de comunicação lateral entre irmãos.

**Estado** fica no nó. Quando `setState` é chamado, a ação é enfileirada na fiber do componente que possui aquele hook. Na próxima render phase, quando a função do componente for executada novamente, `useState` lê o `hookIndex` correspondente, aplica a fila de ações, e retorna o novo valor.

**Efeitos** (`useEffect`) são registrados da mesma forma — como entradas no array de hooks da fiber — mas com um campo adicional que guarda as dependências. O commit verifica se alguma dependência mudou antes de executar o efeito. No Didact que construiremos, implementaremos apenas `useState`; o `useEffect` segue o mesmo padrão estrutural.

O ponto central é este: **nenhuma dessas trocas exige um mecanismo de mensagens ou eventos especiais**. Tudo é leitura e escrita em campos de objetos ligados por ponteiros em uma árvore — o mesmo modelo que você já conhece de estruturas de dados.

O React opera sobre o modelo central `UI = f(state)`. A interface é um reflexo determinístico do estado atual — toda vez que o estado muda, o React recalcula como a UI deve ser e aplica apenas as diferenças ao DOM. Cinco mecanismos tornam isso possível:

| Mecanismo | Responsabilidade |
|---|---|
| **Virtual DOM / Fiber Tree** | Representação da UI em memória |
| **Scheduler** | Controle de *quando* o trabalho acontece |
| **Render Phase** | Cálculo das mudanças via Reconciliação |
| **Commit Phase** | Aplicação atômica ao DOM |
| **Hooks** | Vinculação de estado e efeitos às fibers |


### Sobre este desafio

Como dito, os créditos do trabalho original são de Rodrigo Pombo. Esta é uma adaptação
para o contexto da disciplina de Desenvolvimento Mobile. Todo o código e texto adaptado foi mantido em inglês, pois a imensa maioria das documentações técnicas está nesse idioma. É tarefa de vocês a compreensão/tradução, jáa que saber 'navegar' por documentação técnica em inglês é uma competência necessária.

Este desafio é estruturado em **cinco missões**. Em cada missão, parte do código é fornecida e parte deve ser implementada por você. Ao final, todas as peças devem ser integradas em uma biblioteca `Didact` funcional e entregue no Moodle. Você também deverá apresentar o código. Qualquer parte do código poderá ser questionada durante a apresentação. Nesse desafio não abordaremos o ciclo de vida do useEffect para fins de simplicidade. 

> Importante! A entrega deste desafio não avaliará apenas o código final, mas o seu processo de construção. Vocês deverão versionar o projeto utilizando o Git, criando uma branch específica para cada missão concluída (ex: missao-1, missao-2). Isso garantirá que o histórico da sua resolução fique registrado passo a passo. As instruções detalhadas de como estruturar o repositório estarão na página de entrega da tarefa no Moodle.

Sem mais delongas, vamos começar!

---

## Mission 1: `createElement` and `render`

### 1.1 The `createElement` Function *(provided)*

When you write JSX, build tools like Babel transform it into `React.createElement` calls. An element is simply an object with `type` and `props`.

The function below builds this object tree. For primitive values (like strings), we create a special `TEXT_ELEMENT` node to keep the rendering logic uniform.

```javascript
// Transforms JSX into a plain object representing a UI element.
// Babel calls this function when it compiles JSX like <div id="app" />.
// `type` is the tag name ("div", "h1", etc.) or a component function.
// `props` holds attributes like { id: "app", className: "box" }.
// `...children` collects every nested element as a rest parameter array.
function createElement(type, props, ...children) {
  return {
    type,
    props: {
      ...props, // spread original props (e.g. id, className, event handlers)

      // Normalize children: if a child is already an element object, keep it;
      // if it's a primitive (string, number), wrap it in a TEXT_ELEMENT node.
      // This keeps the render function uniform — it always deals with objects.
      children: children.map(child =>
        typeof child === "object"
          ? child
          : createTextElement(child)
      ),
    },
  }
}

// Creates a virtual node for raw text content (strings and numbers).
// Real React just uses the primitive directly, but Didact wraps it in an object so that render() can handle every node the same way, without special-casing "is this a string or an element?".
// `nodeValue` is the actual DOM property that holds the text content assigning it to a text node is equivalent to node.nodeValue = "Hello".
function createTextElement(text) {
  return {
    type: "TEXT_ELEMENT", // sentinel type; render() checks for this string
    props: {
      nodeValue: text,    // will be assigned directly to the DOM text node
      children: [],       // text nodes never have children
    },
  }
}
```

### 1.2 The `render` Function *(your turn ✏️)*

Now you must transform these element objects into real DOM nodes.

The logic you need to implement:
1. If `element.type` is `"TEXT_ELEMENT"`, create a text node with
   `document.createTextNode("")`; otherwise use
   `document.createElement(element.type)`.
2. Assign all props (except `"children"`) directly to the DOM node.
3. Recursively call `render` for each child, passing the newly created node
   as the container.
4. Append the node to the `container`.

```javascript
function render(element, container) {
  // TODO: implement the steps described above
}
```

> ⚠️ **Note:** This recursive approach has a fundamental flaw — once it starts, it will not stop until the entire tree is rendered. If the tree is large, it blocks the main thread. We will fix this in Mission 2.

### 🧪 Test Your Progress (Mission 1)

Before moving on, let's make sure your `createElement` and naive `render` are working. 

**Step 1:** Create an `index.html` file in the same folder as your JS file (let's assume your JS file is named `didact.js`). It needs a root container and a script tag:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Didact - My own React</title>
</head>
<body>
  <div id="root"></div>
  
  <script src="didact.js"></script>
</body>
</html>
```

**Step 2:** Add the following code at the bottom of your `didact.js` file and open the `index.html` in your browser. 

```javascript
const Didact = { createElement, render };

// We are not using JSX yet, so we write the nested calls manually
const element = Didact.createElement(
  "div",
  { style: "background: salmon; padding: 20px; border-radius: 8px;" },
  Didact.createElement("h1", null, "Mission 1: Success! 🎉"),
  Didact.createElement("p", null, "If you can see this, your DOM creation is working.")
);

const container = document.getElementById("root");
Didact.render(element, container);
```

*Did it render on the screen? Great! Now remove or comment out the Javascript test code from step 2 before starting Mission 2, because our architecture is about to change drastically.*

---

## Mission 2: Concurrent Mode and the Fiber Tree

To stop blocking the main thread, we break the work into small units. After
each unit, the browser may interrupt us if something more urgent needs to run.

### 2.1 The Work Loop *(provided)*

`requestIdleCallback` schedules our loop to run when the main thread is idle.
The `deadline` parameter tells us how much time we have left before yielding
control back to the browser.

```javascript
let nextUnitOfWork = null

function workLoop(deadline) {
  let shouldYield = false
  while (nextUnitOfWork && !shouldYield) {
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork)
    shouldYield = deadline.timeRemaining() < 1
  }

  if (!nextUnitOfWork && wipRoot) {
    commitRoot()
  }

  requestIdleCallback(workLoop)
}

requestIdleCallback(workLoop)
```

### 2.2 DOM Node Creation *(provided)*

This helper creates the actual DOM node for a given fiber. Note that it calls
`updateDom` — a function you will implement in Mission 3.

```javascript
function createDom(fiber) {
  const dom =
    fiber.type === "TEXT_ELEMENT"
      ? document.createTextNode("")
      : document.createElement(fiber.type)

  updateDom(dom, {}, fiber.props)
  return dom
}
```

### 2.3 `performUnitOfWork` *(your turn ✏️)*

This is the core of the fiber scheduler. The function receives a fiber,
processes it, and returns the next fiber to be processed.

The structure below is provided. Your task is to implement the **tree
traversal logic**: given a fiber that has just been processed, determine which
fiber should be processed next (child → sibling → uncle).

```javascript
function performUnitOfWork(fiber) {
  const isFunctionComponent = fiber.type instanceof Function
  if (isFunctionComponent) {
    updateFunctionComponent(fiber)
  } else {
    updateHostComponent(fiber)
  }

  // TODO: return the next unit of work following the order:
  // 1. Return the child, if it exists.
  // 2. Otherwise, walk up the tree looking for a sibling.
  // 3. If no sibling is found at any level, return undefined (we are done).
}

function updateHostComponent(fiber) {
  if (!fiber.dom) {
    fiber.dom = createDom(fiber)
  }
  reconcileChildren(fiber, fiber.props.children)
}
```

### 🧪 Test Your Progress (Mission 2)

Because we are in the middle of a major refactor, we cannot draw elements to the screen right now. We need Mission 3's Commit Phase to do that safely! 

However, the core of Mission 2 is the **tree traversal algorithm** (child → sibling → uncle). We can test if your `performUnitOfWork` is returning the correct next fiber by feeding it a mocked tree and watching the console.

Add this test at the bottom of your file:

```javascript
/* Let's simulate this tree:
      A
     / \
    B   D
   /
  C
*/

// 1. Create fake fibers
const fiberC = { type: "C", props: {} };
const fiberB = { type: "B", props: {}, child: fiberC };
const fiberD = { type: "D", props: {} };
const fiberA = { type: "A", props: {}, child: fiberB };

// 2. Link parents and siblings
fiberC.parent = fiberB;
fiberB.parent = fiberA;
fiberD.parent = fiberA;
fiberB.sibling = fiberD;

// 3. Temporarily mock the update function so it doesn't try to touch the DOM
const originalUpdateHost = updateHostComponent;
updateHostComponent = (fiber) => { 
  console.log("Visiting node:", fiber.type); 
};

// 4. Run your Work Loop logic manually
console.log("--- Starting Fiber Traversal Test ---");
let nextUnit = fiberA;
while (nextUnit) {
  nextUnit = performUnitOfWork(nextUnit);
}
console.log("--- Traversal Finished ---");

// Restore the original function for the next missions
updateHostComponent = originalUpdateHost;
```

*Check your browser's console! If it prints `A`, then `B`, then `C`, then `D` exactly in that order, your Fiber traversal logic is perfect! You successfully implemented the left-child right-sibling iteration without recursion.*

---

## Mission 3: Render and Commit Phases & Reconciliation

We now have a new problem: we are mutating the DOM fiber by fiber. If the browser interrupts us mid-way, the user sees a broken UI. We must separate **calculation** from **DOM mutation**.

### 3.1 The Commit Phase *(provided + your turn)*

Before we look at the code, let's remember why our version 1 of `render` (from Mission 1) was considered *naive*.

In Mission 1, we used a synchronous, recursive approach. The problem with that is that once the rendering started, it couldn't stop until the entire tree was fully built and appended to the DOM. If the component tree was huge, it would block the browser's main thread for a long time, freezing the UI and preventing user interactions or animations.

To fix this, we split the work into two phases:

- 1. Render Phase: Where we calculate the changes (interruptible).

- 2. Commit Phase: Where we actually apply those changes to the DOM (atomic and fast).

We accumulate all work in the wipRoot (work-in-progress root) and only touch the DOM once all fibers have been processed. The new render function below replaces your naive version from Mission 1.


Your Task:

- To ensure you truly understand how this crucial step works, do not just copy and paste the code below. You must transform this into a mini-guide for yourself: add a brief comment line-by-line (or block-by-block) explaining what the code is doing and why.

```javascript
let wipRoot = null
let currentRoot = null
let deletions = null

// Updated render: sets up the wipRoot instead of touching the DOM directly
function render(element, container) {
  wipRoot = {
    dom: container,
    props: { children: [element] },
    alternate: currentRoot,
  }
  deletions = []
  nextUnitOfWork = wipRoot
}

function commitRoot() {
  deletions.forEach(commitWork)
  commitWork(wipRoot.child)
  currentRoot = wipRoot
  wipRoot = null
}

function commitWork(fiber) {
  if (!fiber) return

  let domParentFiber = fiber.parent
  while (!domParentFiber.dom) {
    domParentFiber = domParentFiber.parent
  }
  const domParent = domParentFiber.dom

  if (fiber.effectTag === "PLACEMENT" && fiber.dom != null) {
    domParent.appendChild(fiber.dom)
  } else if (fiber.effectTag === "UPDATE" && fiber.dom != null) {
    updateDom(fiber.dom, fiber.alternate.props, fiber.props)
  } else if (fiber.effectTag === "DELETION") {
    commitDeletion(fiber, domParent)
  }

  commitWork(fiber.child)
  commitWork(fiber.sibling)
}

function commitDeletion(fiber, domParent) {
  if (fiber.dom) {
    domParent.removeChild(fiber.dom)
  } else {
    commitDeletion(fiber.child, domParent)
  }
}
```

### 3.2 `updateDom` *(your turn ✏️)*

This function updates an existing DOM node by comparing old and new props. Because we are *reusing* the DOM node to save performance, we must carefully clean up old data before applying the new state to prevent memory leaks, firing old functions, or displaying outdated UI. 

It must handle **four cases**, in this order:

1. **Remove** event listeners that changed or no longer exist. *(Prevents memory leaks and executing stale closures).* *(Props starting with `"on"` are event listeners; the DOM event name is the prop name lowercased with `"on"` stripped — e.g. `onClick` → `addEventListener("click", ...)`)*
2. **Remove** regular props that no longer exist in the new props. *(Clears outdated attributes from the DOM).*
3. **Set** regular props that are new or have changed. *(Applies the fresh UI state).*
4. **Add** event listeners that are new or have changed. *(Attaches the new interactive callbacks).*

The helper predicates below are provided to make the filtering cleaner:

```javascript
const isEvent     = key => key.startsWith("on")
const isProperty  = key => key !== "children" && !isEvent(key)
const isNew       = (prev, next) => key => prev[key] !== next[key]
const isGone      = (prev, next) => key => !(key in next)

function updateDom(dom, prevProps, nextProps) {
  // TODO: implement the four cases described above
}
```

### 3.3 `reconcileChildren` *(your turn ✏️)*

This function is the heart of React's Diffing Algorithm. Destroying the old DOM and rebuilding it from scratch on every render is incredibly slow. Instead, React compares the new elements with the old fibers (`wipFiber.alternate`) to figure out the minimum number of DOM operations required.

The core heuristic is based on the element's `type` (e.g., `div`, `h1`). Notice that the `while` loop iterates over the new `elements` array and the `oldFiber` siblings **at the same time**. The iteration scaffolding is provided. Your task is to implement the **three comparison cases** that assign the correct `effectTag` to each new fiber, maximizing DOM node recycling:


```javascript
function reconcileChildren(wipFiber, elements) {
  let index = 0
  let oldFiber = wipFiber.alternate && wipFiber.alternate.child
  let prevSibling = null
  // Note: `deletions` is the global array declared in section 3.1.
  // Case 3 will push obsolete fibers into it so commitRoot() can remove them.

  while (index < elements.length || oldFiber != null) {
    const element = elements[index]
    let newFiber = null

    const sameType = oldFiber && element && element.type == oldFiber.type

    // TODO – Case 1: same type → UPDATE
    //   Performance win: The element type is the same, so we recycle the DOM node.
    //   Create a new fiber keeping the existing DOM node, copy the new props, and set the effectTag to "UPDATE".

    // TODO – Case 2: new element, different type → PLACEMENT
    //   Types differ (e.g., morphing an <h1> into a <span>), so we can't recycle.
    //   A new DOM node must be created from scratch.
    //   Create a new fiber with dom: null and set effectTag to "PLACEMENT".

    // TODO – Case 3: old fiber exists, different type → DELETION
    //   The old node is obsolete and must be cleared from the UI.
    //   We don't create a new fiber. Instead, set effectTag to "DELETION"
    //   on the oldFiber and push it to the `deletions` array.

    if (oldFiber) {
      oldFiber = oldFiber.sibling
    }

    if (index === 0) {
      wipFiber.child = newFiber
    } else if (element) {
      prevSibling.sibling = newFiber
    }

    prevSibling = newFiber
    index++
  }
}
```

> 💡 **Hint:** Each new fiber you create must carry: `type`, `props`, `dom`, `parent`, `alternate`, and `effectTag`.


### 🧪 Test Your Progress (Missions 2 & 3)

Missions 2 and 3 refactored our entire rendering engine to use Concurrent Mode and Fibers. Let's test if the Work Loop, the Commit Phase, and the Reconciler (Updates) are working together.

```javascript
const Didact = { createElement, render };
const container = document.getElementById("root");

function updateApp(title, description) {
  const element = Didact.createElement(
    "div",
    { style: "background: lightblue; padding: 20px; border-radius: 8px;" },
    Didact.createElement("h1", null, title),
    Didact.createElement("p", null, description)
  );
  Didact.render(element, container);
}

// 1. Test Initial Render (PLACEMENT)
updateApp("Mission 3: Fiber Tree works! 🌳", "Wait 2 seconds for the update...");

// 2. Test Reconciliation (UPDATE)
setTimeout(() => {
  updateApp("Mission 3: Reconciliation works! 🔄", "The DOM was updated without recreating the wrapper div.");
}, 2000);
```

---

## Mission 4: Function Components and `useState`

HTML host elements are not enough. We need to support function components and stateful hooks.

### 4.1 `updateFunctionComponent` *(provided)*

Function components do not produce a DOM node directly, their children come from calling the function itself. 

But how does `useState` know *where* to save its data, since we don't pass an ID to it (like `useState("count")`)? 

The secret is **call order**. By setting `wipFiber` and `hookIndex` as global variables right before executing the component, they act as a "cursor". When `useState` runs inside the component, it simply checks: *"Which fiber is rendering right now, and what is the current index?"*. 

*(💡 **Trivia:** Remember, this is exactly why React's "Rules of Hooks" forbid putting hooks inside `if` statements or loops! If the call order changes between renders, the cursor gets lost and returns the wrong state).*

```javascript
let wipFiber = null
let hookIndex = null

function updateFunctionComponent(fiber) {
  wipFiber = fiber
  hookIndex = 0
  wipFiber.hooks = []
  
  // When we call fiber.type(fiber.props), any useState inside it 
  // will be able to read the global variables above.
  const children = [fiber.type(fiber.props)]
  reconcileChildren(fiber, children)
}
```

### 4.2 `useState` *(your turn ✏️)*

We know that `useState` returns the current value and a function to update it. Here is where you're going to build that mechanism. 

The trickiest part isn't storing the state, it's handling the updates. Because a user might call `setState` multiple times before the browser has time to render, we don't update the state immediately. Instead, we **queue** the actions and apply them all at once during the next render phase.

Using the description below, implement the `useState` hook:

1. **Retrieve old state:** Look up the hook at position `hookIndex` in the previous fiber (`wipFiber.alternate`), if it exists. *(This is how state persists across renders!)*
2. **Initialize:** Create a new hook object with a `state` (recovered from the old hook, or the `initial` value) and a fresh, empty `queue`.
3. **Process the queue (Batching):** Iterate over the old hook's queue and apply all actions to the `hook.state`. *(Remember: an action can be a function like `c => c + 1` or a direct value).*
4. **The `setState` dispatcher:** Create a `setState` function. When called, it must:
   * Push the new action to the hook's queue.
   * Schedule a re-render by pointing `wipRoot` to the current tree and setting `nextUnitOfWork = wipRoot` *(this wakes up our Work Loop!)*.
5. **Advance the cursor:** Push the new hook to `wipFiber.hooks`, increment the global `hookIndex`, and finally return the familiar `[hook.state, setState]` array.

```javascript
function useState(initial) {
  // TODO: implement based on the steps above
}
```

### 🧪 Test Your Progress (Mission 4)

Now we have function components, which are special because they don't produce DOM nodes directly, but return other elements. Let's test if `updateFunctionComponent` can resolve them.

```javascript
const Didact = { createElement, render };
const container = document.getElementById("root");

// A proper Function Component!
function Greeting(props) {
  return Didact.createElement(
    "h1", 
    { style: "color: green;" }, 
    "Mission 4: Hello, ", 
    props.name, 
    "!"
  );
}

const App = Didact.createElement(Greeting, { name: "Function Components" });
Didact.render(App, container);
```

>If you see a green greeting text, congratulations! Your Fiber tree successfully executed a function component to get its children. You are now ready for the final challenge!

---

## Mission 5: Final Challenge — Assemble and Run Didact 🚀

You now have all the pieces. Your final tasks are:

**5.1 — Assemble the library**

Integrate all functions from Missions 1–4 into a single file `didact.js` and
export the public API:

```javascript
const Didact = {
  createElement,
  render,
  useState,
}
```

**5.2 — Configuring the JSX Transpiler**

Up until now, we have been writing `Didact.createElement(...)` calls by hand. JSX is just syntactic sugar for those calls — but the browser doesn't understand JSX natively, so we need a transpiler to convert it at runtime.

The simplest way to do this without a build pipeline is to load **Babel Standalone** directly from a CDN. Update your `index.html` as follows:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Didact - Mission 5</title>
</head>
<body>
  <div id="root"></div>

  <!-- 1. Load Babel so the browser can understand JSX -->
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <!-- 2. Load your Didact library (plain JS, no JSX here) -->
  <script src="didact.js"></script>

  <!-- 3. Your component file — note type="text/babel" -->
  <script type="text/babel" src="app.js"></script>
</body>
</html>
```

Two things to notice:
- `didact.js` keeps `type="text/javascript"` (the default). It contains no JSX.
- `app.js` uses `type="text/babel"`. This tells Babel Standalone to intercept and transpile that script before it runs.

> ⚠️ **Note: You must serve this folder through a local HTTP server.** Opening `index.html` directly from the filesystem (`file://`) will cause a silent CORS error: Babel tries to fetch `app.js` via XHR, and the browser blocks it. Nothing will appear on screen and the error message in the console is cryptic.
>
> The easiest options:
> - **VS Code**: install the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension → right-click `index.html` → *Open with Live Server*
> - **Node.js**: run `npx serve .` inside the project folder
> - **Python**: run `python3 -m http.server 8080` and open `http://localhost:8080`

Now create `app.js` with the pragma comment on the very first line:

```javascript
/** @jsx Didact.createElement */
```

This pragma is an instruction to Babel: *"whenever you see JSX, call `Didact.createElement` instead of `React.createElement`"*. Without this line, Babel would generate `React.createElement(...)` calls and crash, since we have no React imported.

**5.3 — Build the Counter Component**

With the transpiler configured, implement the counter inside `app.js`:

```javascript
/** @jsx Didact.createElement */

function Counter() {
  const [count, setCount] = Didact.useState(1)
  return (
    <div style="font-family: sans-serif; padding: 40px;">
      <h1>Count: {count}</h1>
      <button onClick={() => setCount(c => c + 1)}>
        + Increment
      </button>
      <button onClick={() => setCount(c => c - 1)}>
        - Decrement
      </button>
    </div>
  )
}

const container = document.getElementById("root")
Didact.render(<Counter />, container)
```

The two buttons stress-test two things simultaneously: that `setState` correctly accepts a **function** as an action (`c => c + 1`), and that successive clicks trigger proper reconciliation without recreating the DOM wrapper.

**🧪 What to verify**

Open `index.html` in the browser and check:

| Behaviour | What it proves |
|---|---|
| Counter appears on screen | Function component resolution + initial render working |
| Click `+` increments the number | `useState` queue + re-render cycle working |
| Click `-` decrements the number | Same, different direction |
| DOM `<div>` is not recreated on each click (inspect with DevTools) | Reconciler is reusing the node via `UPDATE`, not replacing it |

That last check is the most important one. Open DevTools → Elements, click a few times, and watch the `<h1>` content change **without the `<div>` blinking**. If the whole subtree were being torn down and recreated, you'd see the highlight flash. If only the text node updates, your reconciler is doing its job correctly.

---

## Bom trabalho!  