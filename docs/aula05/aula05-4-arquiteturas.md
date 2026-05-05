---
layout: aula
title: "4. Arquiteturas Comuns em Aplicativos Móveis"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 4
---

## **4. Arquiteturas Comuns em Aplicativos Móveis**

Todo sistema de software começa com uma decisão, mesmo que implícita: como organizaremos o conhecimento do problema dentro do código? Essa decisão — que é, antes de tudo, uma decisão de **arquitetura** — vai muito além de escolher quais bibliotecas usar ou em quais pastas salvar os arquivos. Ela define como as responsabilidades serão distribuídas entre as partes do sistema, como as mudanças futuras serão absorvidas sem provocar colapso e, principalmente, como outros desenvolvedores (ou você mesmo, seis meses depois) vão conseguir *ler* o que foi escrito e entender *a intenção por trás do código*.

Arquitetura de software é, em última instância, a arte de tornar intenções explícitas. Um sistema sem arquitetura pensada não é neutro: ele simplesmente acumula as decisões implícitas de quem o escreveu sob pressão e cansaço. Com o tempo, essas decisões não-declaradas transformam o código em um labirinto onde cada mudança exige coragem, não raciocínio.

Existe uma tensão crescente no mercado de tecnologia que vale nomear diretamente: **escrever código está se tornando uma habilidade cada vez menos exclusiva**. Ferramentas como GitHub Copilot, Cursor, Claude e GPT já são capazes de gerar funções, componentes e até módulos inteiros a partir de uma descrição em linguagem natural. A Shopify anunciou que o Copilot já escreve mais de 25% do seu código novo. A Amazon relatou que o CodeWhisperer reduz em até 57% o tempo de implementação de tarefas rotineiras.

O que isso significa para você, que está se formando agora? Significa que o código em si — a linha digitada, o loop implementado, o componente renderizado — está se tornando commodity. O que **não** se commoditiza é a capacidade de entender *por que* uma estrutura é mais robusta do que outra, *quando* uma decisão arquitetural cria dívida técnica, e *como* expressar com clareza as fronteiras de um sistema para que ele escale sem virar caos. A IA pode escrever um `useEffect` para você. Ela não pode decidir se aquele `useEffect` deveria estar na tela, num hook customizado ou num serviço separado — e entender essa diferença é exatamente o que o mercado vai esperar cada vez mais.

É dentro desse contexto que esta seção se insere de forma estratégica. Vocês estão construindo um projeto mobile real. E a tentação natural — especialmente com ferramentas de IA disponíveis — será sair implementando funcionalidades o quanto antes. Os conceitos a seguir existem para orientar essa lógica: **a implementação de código-fonte é subordinada às decisões de arquitetura, não o contrário**.

### **4.1 O que são Padrões de Arquitetura?**

Quando falamos em **padrões de arquitetura**, estamos descrevendo receitas reconhecidas pela comunidade para organizar o código de uma aplicação. Eles surgiram depois de muitos projetos repetirem os mesmos problemas — acoplamento excessivo, telas com "código-espaguete", testes difíceis — e perceberem que determinadas divisões de responsabilidade funcionavam melhor. Esses padrões não são bibliotecas nem frameworks: são **modelos mentais** que dizem algo como *"coloque esta parte dos dados aqui, aquela parte da interface ali, e deixe a ponte entre as duas em outro lugar"*.

É importante deixar claro que esses padrões **não são apenas sobre pastas**. A reorganização de diretórios que vimos na seção anterior é consequência, não causa. O que um padrão arquitetural define, antes de qualquer estrutura de arquivos, são as **fronteiras lógicas do sistema**: quem pode conhecer quem, quem tem permissão de chamar quem, e quem é o único responsável por uma determinada decisão. Uma tela que importa diretamente o `axios` e manipula o estado local no mesmo `useEffect` que formata datas não está apenas "mal organizada em pastas" — ela está **violando fronteiras** que tornam o código acoplado, frágil e intestável. Padrões de arquitetura são a linguagem que usamos para tornar essas fronteiras conscientes e explícitas, em vez de acidentais e invisíveis.

Por fornecerem um "esqueleto", reduzem discussões sobre estrutura, evitam reinventar a roda e deixam claro onde cada novo pedaço de lógica deve entrar.

Vamos explorar os três padrões mais influentes no desenvolvimento de aplicações mobile: **MVC**, **MVP** e **MVVM**. A ideia desses padrões é oferecer uma estrutura organizacional para o código, garantindo que as responsabilidades — como regras de negócio, lógica de apresentação e a interface do usuário (UI) — sejam claramente separadas.

### MVC (Model-View-Controller)

O MVC é um dos padrões mais antigos e fundamentais, estabelecendo a base para a separação de responsabilidades.

* **Conceito Central:** A interação do usuário na **View** é capturada pelo **Controller**, que aciona atualizações no **Model**. As mudanças no Model, por sua vez, notificam a View para que ela se atualize e exiba os novos dados. Esse padrão é frequentemente encontrado em frameworks web como Rails e Laravel — e nesse contexto, ele funciona bem, porque o *ciclo HTTP age como uma fronteira natural*: a requisição chega ao Controller no servidor, o Model é consultado, e uma nova View é renderizada e enviada de volta ao cliente. View e Controller raramente se fundem porque vivem em camadas fisicamente separadas pelo protocolo.

  No mobile, essa fronteira não existe. Tudo roda no mesmo processo, no mesmo dispositivo, sem um ciclo de requisição-resposta para impor separação. Em iOS com UIKit, a própria Apple nomeou sua classe central de `UIViewController` — fundindo View e Controller num único objeto por design. O termo **"Massive View-Controller"** não é uma crítica genérica ao MVC: é uma referência direta a esse problema estrutural do desenvolvimento mobile, onde o componente de tela acumula JSX, chamadas de API, lógica de estado e regras de formatação sem que nada na arquitetura impeça isso. É exatamente para resolver essa ausência de fronteira natural que o MVP e o MVVM existem.

* **Responsabilidades dos Componentes:**
    * **Model:** Representa o coração da aplicação. Contém os dados (ex: uma lista de Pokémons), as regras de negócio (ex: validações, cálculos) e a lógica de acesso a dados (ex: chamadas de API). O Model é completamente independente da UI.
    * **View:** É a camada de apresentação visual. Sua única função é exibir os dados fornecidos pelo Model e encaminhar as ações do usuário (cliques, digitação) para o Controller. Idealmente, não possui lógica de negócio.
    * **Controller:** Atua como o intermediário. Ele recebe as entradas do usuário a partir da View, processa-as (invocando a lógica de negócio no Model) e, por fim, seleciona qual View deve ser renderizada em resposta.

**Fluxo de Interação:**
1. O usuário digita uma String no campo de busca na tela (`View`).
2. A `View` não filtra a lista diretamente. Ela invoca uma função do `Controller`.
3. O `Controller` recebe a chamada, interage com o `Model` para solicitar a filtragem dos dados.
4. O `Model` retorna a lista filtrada para o `Controller`.
5. O `Controller` passa essa lista de volta para a `View`, que simplesmente a renderiza.

Vale deixar claro antes de seguir: **o MVC não é um padrão ruim**. Ele é um padrão com um contexto de uso bem definido. Em projetos pequenos, protótipos e provas de conceito, a estrutura adicional dos padrões seguintes pode ser um exagero que custa mais do que entrega. O problema do MVC não é conceitual — é que ele **não fornece mecanismos ativos para evitar** que a View e o Controller se fundam com o tempo, especialmente em ecossistemas como o React, onde o paradigma funcional e os hooks tornam essa fusão o caminho de menor resistência.

Para exemplificar, tomemos um contador simples — intencionalmente trivial, para que o foco recaia sobre a estrutura, não sobre a lógica de domínio:

```typescript
// CounterScreen_MVC.tsx — o "Massive View-Controller" em ação
export default function CounterScreen() {
  const [count, setCount] = useState(0); // estado aqui = responsabilidade do Controller

  const handleIncrement = () => setCount(c => c + 1); // lógica aqui = problema em escala
  const handleReset = () => setCount(0);

  return (
    <View>
      <Text>{count}</Text>
      <Button title="+" onPress={handleIncrement} />
      <Button title="Reset" onPress={handleReset} />
    </View>
  );
}
```

Para um contador, isso parece inofensivo — e é. O problema surge quando essa mesma lógica começa a incluir chamadas HTTP, validações, formatações de dados e regras condicionais. Cada nova responsabilidade que entra nesse componente aumenta o acoplamento entre "o que aparece na tela" e "como os dados são processados". Em poucos sprints, o que era um componente de 30 linhas vira um arquivo de 300 linhas onde ninguém mais tem coragem de mexer sem quebrar outra coisa. Pior: como a lógica está emaranhada no JSX, escrever um teste unitário para ela exige simular toda a renderização — o que é lento, frágil e caro.

### MVP (Model-View-Presenter)

Já o MVP surgiu como uma evolução do MVC, com o objetivo principal de resolver o problema do acoplamento entre a View e o Controller, aumentando a testabilidade.

O conceito central é que a lógica de apresentação é movida do Controller para uma nova camada chamada **Presenter**. O Presenter se comunica com a View através de uma **interface (contrato)**, tornando a View completamente "burra".

Isso pode parecer contraintuitivo: afinal, a View não é a camada de apresentação? A chave para entender o MVP está em diferenciar **"Lógica de Apresentação"** de **"Lógica de Renderização"**. A **View** fica responsável apenas pela *Lógica de Renderização*: ela sabe *como* desenhar componentes na tela (botões, listas), aplicar estilos e capturar eventos brutos do usuário (`onClick`, `onChangeText`). Ela é o "o quê" e o "como" da parte visual.

Já a **"Lógica de Apresentação"**, que é a responsabilidade do **Presenter**, é a camada de decisão que responde ao "porquê" e ao "quando". Isso inclui gerenciar o estado da UI (decidir *quando* mostrar um `loading`), formatar dados para exibição (transformar um objeto `Date` em uma string "10/06/2025") e aplicar regras condicionais (decidir se um botão de 'Salvar' deve estar habilitado ou desabilitado). Em suma, o Presenter é o cérebro que orquestra a cena, enquanto a View é o palco que apenas exibe o que o cérebro comanda. Ao mover essa camada de decisão para uma classe pura, podemos testá-la de forma isolada, sem depender de renderização.

* **Responsabilidades dos Componentes:**
  * **Model:** Permanece o mesmo do MVC, contendo dados e regras de negócio.
  * **View:** Torna-se uma camada passiva. Ela implementa uma interface definida pelo Presenter e sua única tarefa é chamar os métodos do Presenter em resposta a eventos de UI e renderizar os dados que o Presenter lhe entrega. Ela não toma decisões.
  * **Presenter:** Orquestra tudo. Ele busca dados do Model, aplica a lógica de apresentação e invoca métodos na View (via interface) para atualizar a UI (ex: `view.showLoading()`, `view.displayPokemons(...)`). Como o Presenter depende de uma abstração (`IView`) e não de uma implementação concreta, ele pode ser testado em total isolamento, sem a necessidade de renderizar um único pixel.

**Fluxo de Interação na Pokédex:**
1. A `PokedexScreen` (`View`) é criada e instancia o `PokedexPresenter`, passando a si mesma como a implementação da interface `IPokedexView`.
2. O usuário digita "Charizard" no campo de busca. A `View` apenas notifica: `presenter.onSearchQueryChanged('Charizard')`.
3. O `Presenter` recebe a chamada e pede ao `Model` a lista filtrada.
4. O `Model` retorna os dados brutos.
5. O `Presenter` formata esses dados e chama: `view.showLoading()`, `view.displayFilteredPokemons(formattedData)`, `view.hideLoading()`.
6. A `View` simplesmente obedece, atualizando seu estado para re-renderizar.

O uso **ideal** para essa arquitetura se dá em aplicações corporativas onde a **testabilidade unitária** da lógica de apresentação é um requisito inegociável.

Observe como o mesmo contador é implementado em MVP — e preste atenção especial ao que deixa de existir dentro da tela:

```typescript
// ICounterView.ts — o contrato: o que a View "promete" saber fazer
export interface ICounterView {
  showCount(value: number): void;
}

// CounterPresenter.ts — lógica pura de apresentação, sem nenhuma dependência de UI
export class CounterPresenter {
  private count = 0;
  constructor(private view: ICounterView) {}

  onIncrement() {
    this.count++;
    this.view.showCount(this.count); // empurra o resultado para a View via contrato
  }
  onReset() {
    this.count = 0;
    this.view.showCount(this.count);
  }
}
```

```typescript
// CounterScreen_MVP.tsx — View "burra": renderiza e delega, nada mais
export default function CounterScreen() {
  const [count, setCount] = useState(0);
  const presenter = useRef(
    new CounterPresenter({ showCount: setCount }) // injeta a View via contrato
  ).current;

  return (
    <View>
      <Text>{count}</Text>
      <Button title="+" onPress={() => presenter.onIncrement()} />
      <Button title="Reset" onPress={() => presenter.onReset()} />
    </View>
  );
}
```

> **Nota sobre o paradigma:** Se você observou uma certa estranheza em ver uma `class` sendo instanciada dentro de um componente funcional React, seu instinto está correto. O MVP é um padrão que nasceu e floresceu em ecossistemas orientados a objetos — como o Android com Java ou Kotlin — onde classes são a forma nativa de organizar código. Em React, que é fundamentalmente funcional, a adaptação exige esse tipo de compromisso: usamos `useRef` para garantir que o Presenter seja instanciado apenas uma vez, sem violar as regras dos hooks. O resultado funciona, mas carrega uma tensão de paradigmas que o MVVM, como veremos a seguir, resolve de forma muito mais natural para o ecossistema React Native.

O ganho aqui é arquitetural, não visual — o app continua igual na tela. O que muda é que agora é possível testar o `CounterPresenter` com um simples mock: `new CounterPresenter({ showCount: jest.fn() })`. Nenhum componente React precisa ser renderizado. A lógica vive numa classe TypeScript pura, que pode ser instanciada e testada como qualquer outro objeto.

### MVVM (Model-View-ViewModel)

Já o MVVM é o padrão mais moderno dos três, projetado para se beneficiar de frameworks que suportam **Data Binding** (vinculação de dados), como é o caso do React com Hooks.

O conceito central desse padrão é o **ViewModel**, que expõe dados e comandos aos quais a **View** se "conecta" (*bind*). Quando os dados no ViewModel mudam, a View reage e se atualiza automaticamente, sem a necessidade de intervenção explícita como no MVP.

* **Responsabilidades dos Componentes:**
    * **Model:** Sem alterações, continua responsável pelos dados e regras de negócio.
    * **View:** A camada visual. Ela é responsável por sua própria lógica de UI (animações, transições) e se conecta (*binds*) às propriedades e comandos expostos pelo ViewModel. A comunicação é majoritariamente declarativa.
    * **ViewModel:** É o intermediário que prepara e expõe os dados do Model para a View de uma forma que ela possa consumir facilmente. Ele não tem nenhuma referência à View. Em vez disso, ele expõe **propriedades observáveis** (como estados do React) e **comandos** (funções que a View pode invocar). Qualquer lógica de apresentação ou estado da UI (como `isLoading`, `errorMessage`) vive aqui.

**Fluxo de Interação na Pokédex:**
1. O componente `PokedexScreen` (`View`) utiliza um hook customizado: `const { list, isLoading, searchQuery, setSearchQuery } = usePokedexViewModel();`. Fiquem tranquilos: ainda abordaremos os hooks customizados.
2. A View *amarra* (*binds*) seus elementos a essas propriedades: um `FlatList` renderiza a `list`, um `ActivityIndicator` é exibido quando `isLoading` é `true`, e o `TextInput` tem seu valor vinculado a `searchQuery` e seu `onChangeText` ao `setSearchQuery`.
3. Quando o usuário digita, o `onChangeText` invoca `setSearchQuery`.
4. Dentro do `usePokedexViewModel`, a alteração em `searchQuery` dispara um `useEffect` que executa a lógica de filtragem, busca novos dados e atualiza os estados `list` e `isLoading`.
5. Como a `View` está "observando" `list` e `isLoading`, o React a re-renderiza automaticamente — a View não precisou ser informada explicitamente!

Esse padrão é **ideal** para aplicações ricas em interatividade e com muitos estados de UI. É o padrão mais alinhado com a filosofia de frameworks reativos como React, Vue e Angular, promovendo um código declarativo, reutilizável (o mesmo ViewModel pode ser usado por múltiplas Views) e testável.

Veja como o contador se transforma em MVVM — e note dois detalhes fundamentais: o `CounterScreen` não possui um único `useState` próprio, e o `useCounterViewModel` não importa nenhuma dependência do React Native. Esses dois fatos não são acidentes de estilo — são evidências de que as fronteiras arquiteturais estão sendo respeitadas:

```typescript
// useCounterViewModel.ts — ViewModel como Custom Hook
// Note: não há nenhum import de 'react-native' aqui.
// Este hook poderia ser usado numa web app React sem alterar uma linha.
export function useCounterViewModel() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(c => c + 1);
  const reset = () => setCount(0);

  return { count, increment, reset }; // expõe estado observável + comandos
}
```

```typescript
// CounterScreen_MVVM.tsx — View declarativa: zero useState, zero lógica
export default function CounterScreen() {
  const { count, increment, reset } = useCounterViewModel(); // bind declarativo

  return (
    <View>
      <Text>{count}</Text>
      <Button title="+" onPress={increment} />
      <Button title="Reset" onPress={reset} />
    </View>
  );
}
```

A `CounterScreen` virou literalmente uma declaração de intenção: "quero mostrar `count`, e quando o usuário apertar '+', quero `increment`". Ela não sabe como o contador funciona, não sabe onde o estado vive, não sabe se a lógica consulta uma API ou um valor local. E é exatamente esse desconhecimento proposital que a torna simples, reutilizável e resistente a mudanças futuras. Quando a Pokédex evoluir para ter um `usePokedexViewModel`, a tela seguirá exatamente esse mesmo princípio — toda a orquestração de dados, filtros e estados de carregamento ficará no hook, e a tela cuidará apenas de renderizar o que receber.

### Tabela Comparativa

| Característica | MVC (Model-View-Controller) | MVP (Model-View-Presenter) | MVVM (Model-View-ViewModel) |
| :--- | :--- | :--- | :--- |
| **Acoplamento View-Lógica** | Alto (View e Controller são interdependentes) | Baixo (desacoplados via interface/contrato) | Muito Baixo (desacoplados via data binding) |
| **Lógica de Apresentação** | No Controller, frequentemente misturada na View. | No Presenter. | No ViewModel. |
| **Referência da Lógica para a View** | O Controller manipula diretamente a View. | O Presenter possui uma referência à View (via interface). | O ViewModel não tem nenhuma referência à View. |
| **Testabilidade** | Moderada (difícil testar a UI) | Alta (Presenter é 100% testável) | Altíssima (ViewModel é 100% testável) |
| **Mecanismo de Comunicação** | Chamadas de método diretas. | Métodos definidos em um contrato (interface). | Data Binding e Comandos. |

### E qual Padrão Escolher?

A escolha não é sobre qual é o "melhor", mas qual é o mais **adequado para o seu contexto**:

* Use **MVC** para começar rápido, em projetos simples ou provas de conceito, onde a estrutura formal pode ser um exagero.
* Adote **MVP** quando a testabilidade rigorosa da lógica de apresentação é um requisito inegociável e você ainda não está num ecossistema totalmente reativo.
* Prefira **MVVM** quando sua aplicação é construída sobre um framework reativo (como React Native!), possui múltiplos estados de UI que precisam ser gerenciados e compartilhados, e a equipe busca um código declarativo e com alta capacidade de reuso.

Independentemente da escolha, o princípio fundamental prevalece: o **Model** encapsula os dados e as regras, a **View** exibe o que recebe, e uma **camada intermediária** bem definida orquestra a comunicação. Lembre-se: não existe "bala de prata". Para muitos projetos React Native, uma arquitetura baseada em componentes, com lógica de apresentação em Custom Hooks e serviços bem definidos para o Model, oferece um bom equilíbrio de simplicidade, testabilidade e escalabilidade — incorporando os princípios do MVVM sem a cerimônia de uma implementação formal.

O importante é sempre buscar:
* **Separação de Responsabilidades (SoC):** Cada parte do código tem um papel claro.
* **Testabilidade:** Conseguir testar a lógica de negócios e de apresentação independentemente da UI.
* **Manutenibilidade:** Facilitar a compreensão, modificação e extensão do código.

Nossa Pokédex, mesmo sem aderir rigidamente a um desses padrões formais, já aplica o princípio da separação de responsabilidades ao ter componentes de UI, uma camada de serviço para dados e tipos definidos. Conforme ela evoluir, poderemos introduzir Custom Hooks e outras técnicas para aprimorar ainda mais sua estrutura e refatorá-la, eventualmente, para um desses padrões!

### **Sua vez! Aquecimento: Refatoração MVC → MVVM** ⚡

Você acabou de ver o mesmo contador implementado em três arquiteturas diferentes na seção de Arquiteturas. Antes de mergulhar na análise da Pokédex e nos exercícios da Aula, vamos consolidar esse entendimento na prática com um exercício rápido.

**Tarefa (~15 minutos):** Pegue o componente `CounterScreen_MVC.tsx` apresentado na seção anterior e refatore-o para o padrão **MVVM**, criando dois arquivos:

1. `useCounterViewModel.ts` — o Custom Hook que concentra toda a lógica de estado.
2. `CounterScreen.tsx` — a View refatorada, que **não deve conter nenhum `useState` diretamente**.

**Critério de validação:** Se ao ler o seu `CounterScreen.tsx` refatorado for possível entender imediatamente o que a tela exibe e quais ações o usuário pode executar — sem precisar ler o hook — você acertou. A View deve ser lida como uma especificação, não como uma implementação.

**Implementação adicional (para quem terminar antes):** Adicione ao ViewModel um terceiro comando, `decrement`, e um estado derivado `isZero` que retorna `true` quando o contador está em zero. Use `isZero` na View para desabilitar o botão de decremento (`disabled={isZero}`). Observe como a View continua sem lógica condicional própria — ela apenas consome uma propriedade que o ViewModel calculou.

> Este exercício **não precisa ser commitado** no repositório. É um aquecimento, com possível uso em sala de aula a depender do andamento da turma!