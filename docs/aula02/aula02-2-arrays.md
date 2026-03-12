---
layout: aula
title: "2. Operações com Arrays"
parent: Aula 02 - JavaScript Moderno e TypeScript
nav_order: 2
---

## 2. Desestruturação de Arrays

A **desestruturação** (ou *destructuring*) é um recurso introduzido no ES6 que permite extrair valores de arrays ou propriedades de objetos diretamente em variáveis, usando uma sintaxe concisa e legível. No caso dos **arrays**, a desestruturação é útil quando queremos extrair **valores por posição**, sem precisar acessar manualmente cada índice.

Antes do ES6, extrair valores de um array envolvia chamadas explícitas:

```javascript
const coordenadas = [10, 20];
const x = coordenadas[0];
const y = coordenadas[1];
```

Com a desestruturação de arrays, o código se torna mais direto:

```javascript
const coordenadas = [10, 20];
const [x, y] = coordenadas;

console.log(x); // 10
console.log(y); // 20
```

O lado esquerdo da atribuição define **variáveis posicionais** e o lado direito deve ser um array (ou valor iterável compatível). A ordem importa: o primeiro item do array será atribuído à primeira variável, o segundo à segunda, e assim por diante.

### Pulos de elementos

É possível “pular” elementos do array deixando espaços vazios entre as vírgulas:

```javascript
const cores = ["vermelho", "verde", "azul"];
const [, , terceira] = cores;

console.log(terceira); // "azul"
```

Nesse exemplo, apenas o terceiro elemento foi extraído — os dois primeiros foram ignorados.

### Desestruturação com valores padrão

Em JavaScript, ao fazer desestruturação de arrays, podemos atribuir **valores padrão** às variáveis. Isso significa que, **caso o valor correspondente esteja ausente ou `undefined`**, o valor padrão será utilizado no lugar.

A sintaxe é simples: basta adicionar o sinal de igual (`=`) após o nome da variável, dentro do padrão de desestruturação.

```javascript
const dados = [42];
const [numero, outro = 0] = dados;

console.log(numero); // 42
console.log(outro);  // 0 (valor padrão)
```

Neste exemplo:

* `dados` é um array com apenas um elemento: o número `42`;
* `numero` recebe o primeiro valor do array, ou seja, `42`;
* `outro` tenta receber o **segundo valor** do array — mas como ele não existe (está `undefined`), assume o **valor padrão `0`**.

A sintaxe `[numero, outro = 0]` indica: *"pegue o segundo valor do array; se ele estiver ausente ou for `undefined`, use `0` como valor alternativo"*.

Esse padrão é muito útil em situações como:

* Valores opcionais retornados por uma função;
* Arrays que vêm de fontes externas (ex: APIs);
* Parâmetros de função que usam desestruturação de arrays.

Outro exemplo com mais elementos:

```javascript
const resposta = [undefined, 200];
const [mensagem = "Erro desconhecido", codigo] = resposta;

console.log(mensagem); // "Erro desconhecido"
console.log(codigo);   // 200
```

Nesse caso, a variável `mensagem` recebe o valor `"Erro desconhecido"` porque o primeiro elemento do array é `undefined`.

Esse recurso evita a necessidade de condicionais adicionais (`if` ou `?:`) para verificar valores ausentes, tornando o código mais limpo e simples de escrevermos. 😁

### Uso em funções

A desestruturação pode ser usada diretamente nos **parâmetros de uma função** — algo bastante comum em React Native, especialmente em hooks ou componentes funcionais.

```javascript
function imprimirCoordenadas([x, y]) {
  console.log(`X: ${x}, Y: ${y}`);
}

imprimirCoordenadas([12, 8]); // "X: 12, Y: 8"
```

Com essa sintaxe, evitamos a criação de variáveis intermediárias dentro da função.

### Aplicações práticas no React Native

A desestruturação de arrays aparece com frequência no uso de hooks. O exemplo mais comum é o `useState`:

```tsx
const [contador, setContador] = useState(0);
```

Nesse caso, `useState` retorna um array com dois elementos: o valor atual e a função que o atualiza. A desestruturação permite nomeá-los diretamente, o que melhora a clareza e evita o uso de índices numéricos (`[0]`, `[1]`).

Outro uso comum é em **retornos múltiplos** de funções utilitárias:

```ts
function useData(): [boolean, string] {
  return [true, "dados carregados"];
}

const [carregando, mensagem] = useData();
```

Nesse exemplo, usamos TypeScript para tipar os elementos do array retornado. A desestruturação continua funcionando normalmente e, com os tipos definidos, o editor pode fornecer autocompletar e alertar sobre possíveis erros.

### Considerações sobre TypeScript na desestruturação de arrays

#### **TypeScript infere os tipos dos elementos desestruturados**

Se uma função retorna um array com tipos conhecidos, o TypeScript consegue inferir os tipos corretos ao fazer a desestruturação — e isso vale tanto para arrays simples quanto para retornos de hooks ou utilitários personalizados.

```ts
function useStatus(): [boolean, string] {
  return [true, "Carregando"];
}

const [ativo, mensagem] = useStatus();
// ativo: boolean
// mensagem: string
```

Mesmo que você não escreva os tipos explicitamente durante a desestruturação, o TypeScript entende o tipo de cada posição com base na assinatura da função.

#### **Você também pode tipar os itens desestruturados manualmente!**

Caso esteja lidando com dados de origem externa (como uma API) ou queira ser explícito, é possível declarar os tipos das variáveis desestruturadas individualmente:

```ts
const dados: [number, string, boolean] = [1, "ok", true];
const [id, status, visivel]: [number, string, boolean] = dados;
```

Essa forma é útil quando a fonte de dados não tem tipos confiáveis ou quando se quer reforçar o contrato com outras partes do sistema.

#### **Desestruturação como parâmetro de função também pode ser tipada!**

Você pode tipar os elementos desestruturados diretamente nos parâmetros de uma função:

```ts
function logCoordenadas([x, y]: [number, number]): void {
  console.log(`X: ${x}, Y: ${y}`);
}
```

Esse formato é claro, direto e evita a criação de tipos intermediários quando a estrutura de dados é simples.

#### **Cuidados com arrays heterogêneos‼️**

Ao desestruturar arrays com múltiplos tipos (ex: `[string, number]`), evite tratar o resultado como `any[]` ou `Array<any>`. Sempre prefira **tuplas tipadas** (`[string, number]`), para que o TypeScript possa **validar a ordem e o tipo de cada item individualmente**.

```ts
const tupla: [string, number] = ["idade", 30];
const [rotulo, valor] = tupla;

// rotulo: string, valor: number
```

Isso previne erros como acessar `valor.toUpperCase()` ou somar `rotulo + 1`, que passariam despercebidos se os itens fossem inferidos apenas como `any`.


#### Tá bom, mas por que preciso saber disso?

Esses conceitos tornam-se relevantes quando consideramos o papel da desestruturação no desenvolvimento com React Native e TypeScript. A capacidade do TypeScript de **inferir automaticamente os tipos** dos elementos desestruturados contribui para uma experiência de desenvolvimento mais fluida: o editor fornece autocompletar, verifica tipos em tempo real e evita que erros comuns passem despercebidos. Ao mesmo tempo, a possibilidade de **tipar manualmente os elementos desestruturados** — seja por clareza, por segurança ao lidar com dados externos, ou por necessidade de documentação — permite explicitar contratos entre diferentes partes do sistema e reforçar a previsibilidade do código.

Tipar diretamente os parâmetros em funções que utilizam desestruturação também ajuda a evitar redundâncias e torna o código mais direto, especialmente em funções auxiliares ou callbacks simples. Além disso, quando lidamos com **arrays heterogêneos**, o uso de **tuplas tipadas** em vez de `any[]` é uma prática fundamental para garantir a verificação precisa da ordem e do tipo de cada posição — prevenindo erros sutis e promovendo uma maior robustez na manipulação dos dados.

Entender como o TypeScript lida com desestruturação em diferentes contextos é importante para escrever código expressivo e seguro. Esses recursos não apenas melhoram a legibilidade e a manutenção do código, mas também se alinham aos princípios da programação funcional e da tipagem estática — fundamentos que sustentam aplicações modernas. 


### ✅ Conclusão prática

Em contextos como o React Native, onde muitos hooks e funções retornam arrays com múltiplos valores, esse recurso é não apenas útil, mas basilar para uma escrita fluida e moderna. A desestruturação continua funcionando em TypeScript exatamente como em JavaScript, mas ganha mais poder com a tipagem estática.

Seja em hooks, funções utilitárias ou dados externos, combinar desestruturação com tuplas tipadas é uma forma de manter o código claro, seguro e fácil de manter. 🤓

---

## 2.1 Desestruturação de Objetos

A **desestruturação de objetos** é um recurso introduzido no ES6 que permite extrair valores diretamente de propriedades de objetos em variáveis locais, com uma sintaxe declarativa e mais concisa. Diferentemente da desestruturação de arrays, que usa a **posição** dos elementos, a desestruturação de objetos se baseia em **nomes de propriedades**.

Essa técnica é amplamente usada em código moderno, especialmente em bibliotecas como React, onde props e objetos de estado são frequentemente passados entre funções e componentes.

### Extração direta de propriedades

Antes do ES6, acessar propriedades de um objeto exigia declarações separadas:

```javascript
const usuario = { nome: "Lucas", idade: 28 };

const nome = usuario.nome;
const idade = usuario.idade;
```

Com desestruturação, podemos escrever de forma mais enxuta:

```javascript
const usuario = { nome: "Lucas", idade: 28 };
const { nome, idade } = usuario;

console.log(nome);  // "Lucas"
console.log(idade); // 28
```

As variáveis `nome` e `idade` são criadas automaticamente com os valores correspondentes do objeto. Essa forma é especialmente útil quando se trabalha com objetos grandes, mas apenas algumas propriedades são relevantes para um determinado trecho de código.


### Renomeando variáveis

Às vezes é necessário extrair uma propriedade, mas atribuí-la a uma variável com outro nome. Isso é feito com a sintaxe `propriedade: novoNome`.

```javascript
const produto = { id: 42, preco: 99.9 };
const { preco: valorUnitario } = produto;

console.log(valorUnitario); // 99.9
```

Isso é comum quando o nome da propriedade entra em conflito com outra variável local ou quando desejamos um nome mais expressivo.

### Valores padrão

Também é possível definir **valores padrão** na desestruturação, caso a propriedade não exista no objeto ou esteja `undefined`.

```javascript
const config = { modo: "escuro" };
const { modo, idioma = "pt-BR" } = config;

console.log(modo);   // "escuro"
console.log(idioma); // "pt-BR" (valor padrão)
```

Essa estratégia é muito útil para garantir valores seguros ao trabalhar com dados opcionais, como configurações ou parâmetros de funções.

### Uso em parâmetros de função

Uma das aplicações mais frequentes da desestruturação de objetos é diretamente nos **parâmetros de funções**. Isso permite acessar os dados de entrada já extraídos, sem precisar declarar uma variável intermediária.

```javascript
function mostrarUsuario({ nome, idade }) {
  console.log(`${nome} tem ${idade} anos.`);
}

mostrarUsuario({ nome: "Bruna", idade: 34 });
// "Bruna tem 34 anos."
```

Esse padrão é bastante comum em callbacks, componentes React, hooks e manipuladores de eventos, pois torna o código mais direto e limpo.

### Aplicações em React Native

No contexto do React Native, a desestruturação de objetos é onipresente. Ela aparece, por exemplo:

* Ao extrair props de componentes:

```tsx
const Saudacao = ({ nome }: { nome: string }) => {
  return <Text>Olá, {nome}!</Text>;
};
```

* Ao acessar dados de contextos ou hooks personalizados:

```tsx
const { usuario, sair } = useAuth();
```

* Ou ao lidar com eventos:

```tsx
const handlePress = ({ nativeEvent }: GestureResponderEvent) => {
  console.log(nativeEvent);
};
```

A clareza e concisão da desestruturação ajudam a reduzir código repetitivo, aumentar a legibilidade e deixar explícito o que está sendo utilizado.

### Considerações sobre TypeScript

A desestruturação de objetos funciona exatamente da mesma forma em TypeScript, mas é possível (e recomendado) **tipar os dados desestruturados**, seja dentro da função ou no momento da atribuição. Vejamos abaixo.

#### 1. Tipagem direta na função

```ts
type Usuario = {
  nome: string;
  idade: number;
};

function apresentar({ nome, idade }: Usuario): string {
  return `${nome} tem ${idade} anos.`;
}
```

Aqui, o objeto passado como argumento é desestruturado, e suas propriedades já são validadas segundo o tipo `Usuario`.

#### 2. Tipagem ao desestruturar

```ts
const resposta: { ok: boolean; status: number } = { ok: true, status: 200 };
const { ok, status }: { ok: boolean; status: number } = resposta;
```

Essa abordagem é útil em casos pontuais ou quando se deseja tipar inline, mas em estruturas maiores, o ideal é criar interfaces nomeadas.

### Conclusão

A desestruturação de objetos melhora a legibilidade e reduz a verbosidade ao acessar propriedades específicas. É um recurso central no estilo moderno de escrita JavaScript e uma prática amplamente adotada em aplicações React Native com TypeScript, tanto em componentes quanto em funções utilitárias. A capacidade de combinar desestruturação com tipagem explícita traz clareza, segurança e robustez ao desenvolvimento. 👩‍💻

---

## 2.2 Operador Spread e Rest (`...`)

O operador `...`, introduzido no ES6, serve para dois propósitos distintos, dependendo do contexto:

* Como **spread** (espalhamento), ele **expande** elementos de arrays ou propriedades de objetos.
* Como **rest** (resto), ele **coleta** múltiplos valores em uma única variável.

Apesar de compartilharem a mesma sintaxe, esses dois usos têm comportamentos opostos, mas extremamente úteis — especialmente em operações de composição, cópia e passagem de parâmetros. Seu uso é cotidiano em projetos React Native, desde a manipulação de arrays até a composição de props em componentes.

### Spread: expandindo arrays ou objetos

O uso como **spread** permite **copiar ou combinar estruturas** (arrays ou objetos), espalhando seus elementos onde múltiplos valores são esperados.

#### Arrays

```javascript
const numeros = [1, 2, 3];
const maisNumeros = [...numeros, 4, 5];

console.log(maisNumeros); // [1, 2, 3, 4, 5]
```

Nesse exemplo, `...numeros` insere os elementos do array original dentro de um novo array. Isso é útil para **copiar** arrays (sem referência) ou para **adicionar elementos** em novas estruturas sem mutar o original.

#### Objetos

```javascript
const usuario = { nome: "Ana", idade: 25 };
const usuarioAtualizado = { ...usuario, idade: 26 };

console.log(usuarioAtualizado); // { nome: "Ana", idade: 26 }
```

No caso de objetos, o spread copia todas as propriedades existentes. Se alguma chave for repetida, a nova sobrescreve a anterior — útil para atualizações parciais em estruturas imutáveis, como em `useState`.

### Capturando o restante com o operador `...` (rest syntax)

Além de ser usado para **espalhar elementos** em arrays ou objetos (spread), o operador `...` também pode ser utilizado para **capturar o restante dos elementos** durante uma desestruturação. Esse uso é conhecido como **rest syntax**, ou “sintaxe de captura do restante”.

> ⚠️ **Atenção:** aqui, o termo “rest” não tem nenhuma relação com o padrão arquitetural REST usado em APIs. Trata-se apenas de uma forma abreviada para “restante” — ou seja, os valores que **sobram** após uma desestruturação.

Esse recurso é bastante útil quando queremos extrair apenas uma parte dos dados e armazenar o restante em uma nova variável, tanto em arrays quanto em objetos.

#### Rest em Arrays

No exemplo abaixo, estamos desestruturando o primeiro valor do array e armazenando os demais em uma nova variável chamada `restantes`.

```javascript
const [primeiro, ...restantes] = [10, 20, 30, 40];

console.log(primeiro);   // 10
console.log(restantes);  // [20, 30, 40]
```

Esse padrão é útil em funções que operam sobre listas variáveis ou que desejam processar o primeiro item separadamente dos demais.

#### Rest em Objetos

A mesma ideia vale para objetos. Podemos extrair uma ou mais propriedades específicas e capturar o restante em uma nova variável:

```javascript
const { nome, ...outrosDados } = { nome: "Carlos", idade: 33, ativo: true };

console.log(nome);        // "Carlos"
console.log(outrosDados); // { idade: 33, ativo: true }
```

Esse padrão é comum em manipulação de dados dinâmicos, onde queremos manter algumas informações intactas e trabalhar apenas com o restante.

#### Exemplo em Componentes React Native

No desenvolvimento com React Native, o uso da sintaxe rest é frequente em componentes que recebem múltiplas props:

```tsx
const MeuBotao = ({ titulo, ...props }) => {
  return <Button title={titulo} {...props} />;
};
```

Nesse caso, extraímos apenas a prop `titulo` e repassamos todas as demais para o componente `<Button />`. Isso torna o componente mais flexível e reutilizável, especialmente quando não sabemos todas as props que podem ser recebidas.

### Funções com argumentos variáveis

O operador `...` também é utilizado para declarar funções que aceitam **um número variável de argumentos**. Esse uso é especialmente comum em utilitários matemáticos, construção de logs ou funções que recebem múltiplos parâmetros sem nome fixo.

```javascript
function somar(...numeros) {
  return numeros.reduce((total, n) => total + n, 0);
}

console.log(somar(1, 2, 3, 4)); // 10
```

Nesse caso, `numeros` é um array com todos os argumentos passados. Essa abordagem substitui o uso do objeto `arguments`, oferecendo maior clareza e segurança.

---

### Considerações sobre TypeScript

No TypeScript, a sintaxe rest continua funcionando da mesma forma, mas requer atenção à **tipagem explícita**, principalmente quando há múltiplos tipos envolvidos.

#### Tipagem de arrays com rest

Ao utilizar parâmetros variáveis em funções, podemos indicar o tipo dos elementos com a notação `: tipo[]`:

```ts
function somar(...numeros: number[]): number {
  return numeros.reduce((total, n) => total + n, 0);
}
```

#### Tipagem em objetos com rest

Ao desestruturar objetos, o TypeScript consegue inferir o tipo do restante. Mesmo assim, podemos indicar explicitamente:

```ts
type Usuario = {
  nome: string;
  idade: number;
  ativo: boolean;
};

const { nome, ...resto }: Usuario = {
  nome: "João",
  idade: 30,
  ativo: true,
};
```

Nesse caso, o TypeScript reconhece que `resto` tem tipo `{ idade: number; ativo: boolean }`.

#### Tipos genéricos com rest

É possível usar rest parameters com tipos genéricos para funções reutilizáveis:

```ts
function logarTudo<T extends any[]>(...args: T): void {
  console.log(...args);
}

logarTudo("texto", 42, true); // OK
```

Essa abordagem é útil em funções auxiliares que recebem argumentos variados, mantendo a tipagem precisa e reutilizável.

### Resumindo...

O operador `...` é uma das ferramentas mais versáteis do JavaScript moderno. Seja expandindo estruturas com **spread** ou coletando múltiplos valores quando usado como **rest syntax**, seu uso reduz boilerplate, melhora a legibilidade e permite compor objetos e arrays de forma declarativa. Em aplicações com React Native e TypeScript, seu domínio é importante para manipular props, estados, listas e dados dinâmicos.

---

## 2.3 Métodos de Array: `map`, `filter`, `reduce`, `find`

No desenvolvimento moderno com JavaScript — especialmente em frameworks como React e React Native — há uma forte valorização de princípios da **programação funcional**. Esse paradigma promove o uso de **funções puras**, **imutabilidade**, **composição** e **ausência de efeitos colaterais**, resultando em código mais previsível, legível e fácil de testar.

Os arrays em JavaScript oferecem suporte direto a esse estilo por meio de métodos nativos como `map`, `filter`, `find` e `reduce`. Essas funções de ordem superior permitem transformar, filtrar, localizar ou acumular dados de forma **declarativa**, sem alterar o array original.

Esses métodos são amplamente utilizados em aplicações React Native para:

* Renderizar listas de forma dinâmica;
* Processar dados recebidos de APIs;
* Construir estados derivados com lógica pura;
* Realizar operações como buscas, filtragens e somatórios com clareza e segurança.

Vamos explorar cada um desses métodos, mostrando tanto seu funcionamento quanto sua aplicação prática — sempre com foco no estilo funcional de codificação e nos benefícios que ele traz ao dia a dia do desenvolvimento mobile. 🤠

### `map()` – Transformação de elementos

O método `map()` percorre todos os elementos do array e **retorna um novo array**, onde cada item é o resultado da aplicação de uma função sobre o item original. O tamanho do array resultante é sempre o mesmo que o original.

```javascript
const numeros = [1, 2, 3];
const dobrados = numeros.map(n => n * 2);

console.log(dobrados); // [2, 4, 6]
```

Aqui, cada número foi multiplicado por 2. O array original (`numeros`) permanece inalterado.

#### Exemplo prático: exibindo nomes

```javascript
const usuarios = [
  { nome: "Ana", idade: 25 },
  { nome: "Carlos", idade: 30 }
];

const nomes = usuarios.map(usuario => usuario.nome);
console.log(nomes); // ["Ana", "Carlos"]
```

Essa técnica é comum ao renderizar listas no React:

```tsx
{usuarios.map(usuario => (
  <Text key={usuario.nome}>{usuario.nome}</Text>
))}
```

### `filter()` – Filtragem de elementos

O método `filter()` retorna um novo array com os elementos que **passam em um teste lógico** (retornam `true`). Os elementos que não satisfazem a condição são ignorados.

```javascript
const numeros = [1, 2, 3, 4, 5];
const pares = numeros.filter(n => n % 2 === 0);

console.log(pares); // [2, 4]
```

#### Exemplo prático: usuários ativos

```javascript
const usuarios = [
  { nome: "Ana", ativo: true },
  { nome: "Carlos", ativo: false },
  { nome: "João", ativo: true }
];

const ativos = usuarios.filter(u => u.ativo);
console.log(ativos);
// [
//   { nome: "Ana", ativo: true },
//   { nome: "João", ativo: true }
// ]
```

Essa abordagem é útil, por exemplo, para exibir apenas tarefas incompletas, produtos disponíveis ou itens favoritos.

### `reduce()` – Acumulação de valores

O método `reduce()` executa uma função redutora sobre todos os elementos do array, **acumulando um único valor final**. Ele pode ser usado para somar números, combinar objetos ou compor strings.

```javascript
const numeros = [1, 2, 3, 4];
const soma = numeros.reduce((acumulador, atual) => acumulador + atual, 0);

console.log(soma); // 10
```

A função recebe dois argumentos principais:

* `acumulador`: valor que vai sendo carregado entre iterações
* `atual`: o elemento atual do array

O segundo argumento (`0`) é o valor inicial do acumulador.

#### Exemplo prático: somando totais

```javascript
const compras = [
  { item: "Livro", preco: 30 },
  { item: "Caneta", preco: 5 },
  { item: "Caderno", preco: 15 }
];

const total = compras.reduce((soma, compra) => soma + compra.preco, 0);
console.log(total); // 50
```

Esse padrão é muito usado para **calcular totais** em carrinhos de compras, pontuação de usuários ou estatísticas acumuladas.

### `find()` – Encontrando o primeiro que satisfaz a condição

O método `find()` retorna o **primeiro elemento** do array que satisfaz uma condição. Se nenhum for encontrado, retorna `undefined`. Diferente de `filter()`, que retorna vários, `find()` retorna apenas um item — o primeiro que bater com a lógica.

```javascript
const numeros = [1, 2, 3, 4];
const encontrado = numeros.find(n => n > 2);

console.log(encontrado); // 3
```

#### Exemplo prático: buscar usuário pelo nome

```javascript
const usuarios = [
  { nome: "Ana", id: 1 },
  { nome: "Carlos", id: 2 }
];

const resultado = usuarios.find(u => u.nome === "Carlos");
console.log(resultado); // { nome: "Carlos", id: 2 }
```

Essa técnica é útil para buscar rapidamente um item específico de uma lista — por exemplo, o usuário logado, o produto clicado, ou um item marcado como favorito.

### Relação com Programação Funcional

Como já mencionamos na introdução dessa seção, é importante destacar que os métodos `map`, `filter`, `reduce` e `find` são pilares da **programação funcional** aplicada ao JavaScript. Esse paradigma se baseia em **funções puras, imutabilidade, composição e ausência de efeitos colaterais**. A ideia central é transformar dados por meio de funções, ao invés de modificar estruturas diretamente.

Vimos os exemplos dos métodos acima, mas de qualquer forma vamos aproveitar para entender como eles se relacionam com esses pilares.

#### **Funções puras**

Cada método recebe uma **função de callback** que não deve alterar o estado externo. Em vez disso, ela transforma ou extrai dados com base apenas em seus parâmetros de entrada.

```javascript
const dobrar = n => n * 2;
const resultado = [1, 2, 3].map(dobrar); // [2, 4, 6]
```

A função `dobrar` é pura: dado o mesmo `n`, sempre retorna o mesmo resultado, sem acessar ou modificar nada além de seus argumentos.

#### **Imutabilidade**

Todos esses métodos **não alteram o array original**. Em vez disso, eles produzem **novas estruturas** com base na lógica definida:

```javascript
const numeros = [1, 2, 3];
const pares = numeros.filter(n => n % 2 === 0);

console.log(numeros); // [1, 2, 3] — permanece intacto
console.log(pares);   // [2]
```

Essa característica é essencial em aplicações React e React Native, onde trabalhar com dados imutáveis evita comportamentos inesperados e melhora a previsibilidade da interface.

#### **Composição de funções**

A programação funcional valoriza a **composição** — isto é, o encadeamento de pequenas funções que fazem transformações simples e legíveis:

```javascript
const dados = [1, 2, 3, 4, 5];

const resultado = dados
  .filter(n => n % 2 === 0)      // mantém apenas pares
  .map(n => n * 10)              // multiplica por 10
  .reduce((soma, n) => soma + n, 0); // soma os resultados

console.log(resultado); // 60
```

Esse padrão torna o código mais declarativo e fácil de testar, já que cada etapa é isolada e previsível.

#### **Abstração e reutilização**

As funções passadas a esses métodos são **valores de primeira classe** — ou seja, podem ser armazenadas em variáveis, passadas como parâmetros ou retornadas de outras funções. Isso facilita a reutilização:

```javascript
const isPar = (n: number) => n % 2 === 0;
const emReais = (valor: number) => `R$ ${valor.toFixed(2)}`;

const valores = [10, 15, 22, 7];

const resultado = valores
  .filter(isPar)
  .map(emReais);

console.log(resultado); // ["R$ 10.00", "R$ 22.00"]
```

Essa abordagem segue o princípio da **separação de preocupações**: cada função faz uma coisa apenas, e o sistema final emerge da combinação delas.

### Programação funcional em React Native

Esses princípios são especialmente úteis no desenvolvimento com React Native, por diversos motivos:

* **Componentes funcionais** são o padrão atual — e foram projetados com a ideia de pureza e imutabilidade.
* **Hooks como `useState` e `useReducer`** exigem que os dados sejam transformados sem mutações diretas.
* A **renderização declarativa** do React favorece operações como `.map()` para listas, evitando laços imperativos e mutações.

Exemplo típico:

```tsx
const ListaProdutos = ({ produtos }) => {
  return (
    <FlatList
      data={produtos.filter(p => p.ativo)}
      renderItem={({ item }) => <Text>{item.nome}</Text>}
      keyExtractor={item => item.id.toString()}
    />
  );
};
```

A função `filter` evita lógica de controle manual com `for`, e o código se mantém claro, reutilizável e de fácil manutenção.

### Considerações sobre TypeScript

Além de tudo que mencionamos acima, todos esses métodos funcionam normalmente em TypeScript, mas você pode **aproveitar os tipos genéricos** para obter ainda mais segurança:

#### Inferência automática

```ts
const nomes = ["Ana", "Carlos"];
const maiusculos = nomes.map(n => n.toUpperCase()); // string[]
```

#### Tipagem explícita em callbacks

```ts
type Usuario = { nome: string; idade: number };

const usuarios: Usuario[] = [...];

const maiores = usuarios.filter((u: Usuario) => u.idade >= 18);
```

#### Tipagem no `reduce`

```ts
type Item = { valor: number };

const itens: Item[] = [{ valor: 10 }, { valor: 20 }];

const total = itens.reduce((soma: number, item) => soma + item.valor, 0);
```

Se você não tipar o acumulador, o TypeScript pode inferir incorretamente o tipo, especialmente se o valor inicial for omitido.

### _Costurando_ tudo 🪢

Os métodos `map`, `filter`, `reduce` e `find` são ferramentas poderosas e expressivas para transformar e manipular arrays de forma funcional, declarativa e segura. Dominar essas funções permite escrever código mais conciso, evitar laços imperativos (`for`, `while`) e se alinhar ao estilo moderno de desenvolvimento JavaScript — algo especialmente útil no React Native, onde a manipulação de listas e estados derivados é rotina.

A utilização de métodos como `map`, `filter`, `reduce` e `find` não é apenas uma conveniência sintática — ela representa uma **mudança de estilo**: do imperativo para o funcional. Essa mudança torna o código mais legível, modular e confiável, além de estar perfeitamente alinhada aos princípios do React e do TypeScript moderno. Compreender e adotar essas práticas é um passo essencial para escrever software mais limpo, previsível e escalável.

---

## 2.4 Resumão sobre os Fundamentos Modernos de JavaScript

Nesta primeira parte da aula, revisamos os principais recursos do JavaScript moderno que moldam a forma como escrevemos código atualmente — especialmente no contexto do desenvolvimento com React e React Native.

Recursos como `let` e `const`, desestruturação, template literals e arrow functions nos permitem **expressar intenções de forma mais clara e segura**, enquanto operadores como spread/rest e métodos como `map`, `filter` e `reduce` nos colocam em sintonia com os princípios da **programação funcional**, favorecendo **imutabilidade, composição e previsibilidade**.

Além disso, vimos como essas construções estão presentes o tempo todo na prática cotidiana: em funções de manipulação de estado, passagem de props, renderização de listas, configuração de objetos, e até na maneira como modelamos dados vindos de APIs ou transformamos entradas do usuário.

A familiaridade com essas ferramentas é fundamental — elas formam o vocabulário básico da programação JavaScript moderna e, consequentemente, da base sobre a qual construiremos nossas aplicações móveis com React Native.

Agora que revisitamos os fundamentos modernos do JavaScript, é hora de dar um passo adiante e conhecer o que o **TypeScript** adiciona a essa base. Como dissemos inicialmente, TypeScript não substitui JavaScript — ele **estende** sua sintaxe e funcionalidades, permitindo que possamos **tipar dados, funções e objetos de forma explícita**. Isso nos ajuda a detectar erros antes da execução, documentar melhor nossas intenções e tornar o código mais previsível e confiável.

Na próxima seção, vamos explorar de forma prática os principais recursos da linguagem, como tipos primitivos, interfaces, generics e inferência, sempre com foco em como isso melhora a qualidade do código no desenvolvimento mobile. 🚀