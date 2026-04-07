---
layout: aula
title: "4. Arquiteturas Comuns em Aplicativos Móveis"
parent: Aula 05 - Navegação, Modularização e Arquiteturas
nav_order: 4
---

## **4. Arquiteturas Comuns em Aplicativos Móveis**

Além dessa questão de organização em diretórios, temos, até agora, construído nossa aplicação de forma intuitiva, focando na componentização e na separação de responsabilidades (UI, serviços, tipos). À medida que os aplicativos se tornam maiores e mais complexos, contudo, pensar em padrões de arquitetura pode ajudar a gerenciar essa complexidade, melhorar a testabilidade e a manutenibilidade.

Quando falamos em **padrões de arquitetura** estamos descrevendo receitas reconhecidas pela comunidade para organizar o código de uma aplicação. Eles surgiram depois de muitos projetos repetirem os mesmos problemas (acoplamento excessivo, telas com “código-espaguete”, testes difíceis etc) e perceberem que determinadas divisões de responsabilidade funcionavam melhor. Esses padrões não são bibliotecas nem frameworks: são **modelos mentais** que dizem *“coloque esta parte dos dados aqui, aquela parte da interface ali, e deixe a ponte entre as duas em outro lugar”*. Por fornecerem um “esqueleto”, reduzem discussões sobre estrutura, evitam reinventar a roda e deixam claro onde cada novo pedaço de lógica deve entrar.

Vamos dar uma olhada rápida em alguns padrões arquiteturais comuns em aplicativos móveis e como eles podem se relacionar com o React Native.

### **4.1 O que são Padrões de Arquitetura?**

Como mencionamos, padrões de arquitetura oferecem uma estrutura organizacional para o código, garantindo que as responsabilidades — como regras de negócio, lógica de apresentação e a interface do usuário (UI) — sejam claramente separadas.

Vamos explorar três dos padrões mais influentes no desenvolvimento de aplicações mobile: **MVC**, **MVP** e **MVVM**.

### MVC (Model-View-Controller)

O MVC é um dos padrões mais antigos e fundamentais, estabelecendo a base para a separação de responsabilidades.

* **Conceito Central:** A interação do usuário na **View** é capturada pelo **Controller**, que aciona atualizações no **Model**. As mudanças no Model, por sua vez, notificam a View para que ela se atualize e exiba os novos dados.

* **Responsabilidades dos Componentes:**
    * **Model:** Representa o coração da aplicação. Contém os dados (ex: uma lista de Pokémons), as regras de negócio (ex: validações, cálculos) e a lógica de acesso a dados (ex: chamadas de API). O Model é completamente independente da UI.
    * **View:** É a camada de apresentação visual. Sua única função é exibir os dados fornecidos pelo Model e encaminhar as ações do usuário (cliques, digitação) para o Controller. Idealmente, não possui lógica de negócio.
    * **Controller:** Atua como o intermediário. Ele recebe as entradas do usuário a partir da View, processa-as (invocando a lógica de negócio no Model) e, por fim, seleciona qual View deve ser renderizada em resposta.

Nesse sentido, o **Fluxo de Interação** seria algo como:
    1.  O usuário digita uma String no campo de busca na tela (`View`).
    2.  A `View` (o componente React Native) não filtra a lista diretamente. Ela invoca uma função do `Controller`.
    3.  O `Controller` recebe a chamada, interage com o `Model` para solicitar a filtragem dos dados.
    4.  O `Model` retorna a lista filtrada para o `Controller`.
    5.  O `Controller` então passa essa lista de volta para a `View`, que simplesmente a renderiza.

Em frameworks modernos como o React, a distinção entre View e Controller muitas vezes se torna turva. Um componente React (por exemplo, nossa `PokedexScreen.tsx`) frequentemente contém tanto a marcação JSX (`View`) quanto os manipuladores de eventos e a lógica de estado (`useEffect`, `useState`), que são papéis do `Controller`. Isso pode levar ao famoso problema do **"Massive View-Controller"**, onde o componente se torna gigantesco, complexo e difícil de testar, pois a lógica de apresentação está fortemente acoplada ao framework de renderização.

Portanto, o uso do MVC em aplicações Mobile com React Native é **ideal** para projetos pequenos, protótipos rápidos ou cenários onde a simplicidade e a velocidade de desenvolvimento inicial são mais importantes que a testabilidade rigorosa.

### MVP (Model-View-Presenter)

Já o MVP surgiu como uma evolução do MVC, com o objetivo principal de resolver o problema do acoplamento entre a View e o Controller, aumentando a testabilidade.

O conceito central é que a lógica de apresentação é movida do Controller para uma nova camada chamada **Presenter**. O Presenter se comunica com a View através de uma **interface (contrato)**, tornando a View completamente "burra".

Isso pode parecer contraintuitivo: afinal, a View não é a camada de apresentação? A chave para entender o MVP está em diferenciar **"Lógica de Apresentação"** de **"Lógica de Renderização"**. A **View** fica responsável apenas pela *Lógica de Renderização*: ela sabe *como* desenhar componentes na tela (botões, listas), aplicar estilos e capturar eventos brutos do usuário (`onClick`, `onChangeText`). Ela é o "o quê" e o "como" da parte visual.

Já a **"Lógica de Apresentação"**, que é a responsabilidade do **Presenter**, é a camada de decisão que responde ao "porquê" e ao "quando". Isso inclui gerenciar o estado da UI (decidir *quando* mostrar um `loading`), formatar dados para exibição (transformar um objeto `Date` em uma string "10/06/2025") e aplicar regras condicionais (decidir se um botão de 'Salvar' deve estar habilitado ou desabilitado). Em suma, o Presenter é o cérebro que orquestra a cena, enquanto a View é o palco que apenas exibe o que o cérebro comanda. Ao mover essa camada de decisão para uma classe pura, a testamos de forma isolada, sem depender de renderização.

Nesse âmbito, as **responsabilidades dos componentes** passam a ser:
  * **Model:** Permanece o mesmo do MVC, contendo dados e regras de negócio.
  * **View:** Torna-se uma camada passiva. Ela implementa uma interface definida pelo Presenter e sua única tarefa é chamar os métodos do Presenter em resposta a eventos de UI e renderizar os dados que o Presenter lhe entrega. Ela não toma decisões.
  * **Presenter:** Orquestra tudo. Ele busca dados do Model, aplica a lógica de apresentação (formatação de datas, textos, etc.) e invoca métodos na View (via interface) para atualizar a UI (ex: `view.showLoading()`, `view.displayPokemons(...)`). Como o Presenter depende de uma abstração (`IView`) e não de uma implementação concreta, ele pode ser testado em total isolamento, sem a necessidade de renderizar um único pixel.

Se estivéssemos falando da nossa Pokédex, o **fluxo de interação** seria algo como:

  1.  A `PokedexScreen` (`View`) é criada e instancia o `PokedexPresenter`, passando a si mesma como a implementação da interface `IPokedexView`.
  2.  O usuário digita "Charizard" no campo de busca. A `View` não faz nada além de notificar o Presenter: `presenter.onSearchQueryChanged('Charizard')`.
  3.  O `Presenter` recebe a chamada, pede ao `Model` a lista de Pokémons filtrada.
  4.  O `Model` retorna os dados brutos.
  5.  O `Presenter` formata esses dados (ex: capitaliza os nomes) e chama os métodos da interface implementada pela View: `view.showLoading()`, `view.displayFilteredPokemons(formattedData)`, `view.hideLoading()`.
  6.  A `View` simplesmente obedece a essas chamadas, atualizando seu estado interno para re-renderizar.

O uso **ideal** para essa arquitetura se dá em aplicações corporativas onde a **testabilidade unitária** da lógica de apresentação é um fator de grande importância, pois permite que as regras de UI sejam validadas independentemente do framework visual, facilitando a manutenção e a migração de plataforma.

### MVVM (Model-View-ViewModel)

Já o MVVM é o padrão mais moderno dos três, projetado para se beneficiar de frameworks que suportam **Data Binding** (vinculação de dados), como é o caso do React com Hooks. 😊

O conceito central desse padrão é o **ViewModel**, que expõe dados e comandos aos quais a **View** se "conecta" (bind). Quando os dados no ViewModel mudam, a View reage e se atualiza automaticamente, sem a necessidade de intervenção explícita como no MVP.

Nesse sentido, a **responsabilidades dos componentes** passa a ser:
    * **Model:** Sem alterações, continua responsável pelos dados e regras de negócio.
    * **View:** A camada visual. Ela é responsável por sua própria lógica de UI (animações, transições) e se conecta (binds) às propriedades e comandos expostos pelo ViewModel. A comunicação é majoritariamente declarativa.
    * **ViewModel:** É o intermediário que prepara e expõe os dados do Model para a View de uma forma que ela possa consumir facilmente. Ele não tem nenhuma referência à View. Em vez disso, ele expõe **propriedades observáveis** (como estados do React) e **comandos** (funções que a View pode invocar). Qualquer lógica de apresentação ou estado da UI (como `isLoading`, `errorMessage`) vive aqui.

O **fluxo de interação** na nossa Pokédex seria algo como:
    1.  O componente `PokedexScreen` (`View`) utiliza um hook customizado: `const { list, isLoading, searchQuery, setSearchQuery } = usePokedexViewModel();`. Fiquem tranquilos: ainda abordaremos os hooks customizados.
    2.  A View "amarra" (binds) seus elementos a essas propriedades: um `FlatList` renderiza a `list`, um `ActivityIndicator` é exibido quando `isLoading` é `true`, e o `TextInput` tem seu valor vinculado a `searchQuery` e seu `onChangeText` ao `setSearchQuery`.
    3.  Quando o usuário digita, o `onChangeText` invoca `setSearchQuery`.
    4.  Dentro do `usePokedexViewModel`, a alteração em `searchQuery` dispara um `useEffect` que executa a lógica de filtragem, busca novos dados e atualiza os estados `list` e `isLoading`.
    5.  Como a `View` está "observando" `list` e `isLoading`, o React a re-renderiza automaticamente para refletir o novo estado: ou seja, a View não precisou ser informada explicitamente!

Esse padrão de arquitetura é **ideal** para aplicações ricas em interatividade e com muitos estados de UI. É o padrão mais alinhado com a filosofia de frameworks reativos como React, Vue e Angular, promovendo um código declarativo, reutilizável (o mesmo ViewModel pode ser usado por múltiplas Views) e testável.

### Tabela Comparativa

| Característica | MVC (Model-View-Controller) | MVP (Model-View-Presenter) | MVVM (Model-View-ViewModel) |
| :--- | :--- | :--- | :--- |
| **Acoplamento View-Lógica** | Alto (View e Controller são interdependentes) | Baixo ( desacoplados via interface/contrato) | Muito Baixo (desacoplados via data binding) |
| **Lógica de Apresentação** | No Controller, frequentemente misturada na View. | No Presenter. | No ViewModel. |
| **Referência da Lógica para a View** | O Controller manipula diretamente a View. | O Presenter possui uma referência à View (via interface). | O ViewModel não tem nenhuma referência à View. |
| **Testabilidade** | Moderada (difícil testar a UI) | Alta (Presenter é 100% testável) | Altíssima (ViewModel é 100% testável) |
| **Mecanismo de Comunicação** | Chamadas de método diretas. | Métodos definidos em um contrato (interface). | Data Binding e Comandos. |

### E qual Padrão Escolher?

A escolha não é sobre qual é o "melhor", mas qual é o mais **adequado para o seu contexto**:

* Use **MVC** para começar rápido, em projetos simples ou provas de conceito, onde a estrutura formal pode ser um exagero.
* Adote **MVP** quando a testabilidade rigorosa da lógica de apresentação é um requisito inegociável e você precisa de uma separação clara, mas ainda não está em um ecossistema totalmente reativo.
* Prefira **MVVM** quando sua aplicação é construída sobre um framework reativo (como React Native, que estamos utilizando!), possui múltiplos estados de UI que precisam ser gerenciados e compartilhados, e a equipe busca um código declarativo e com alta capacidade de reuso.

Independentemente da escolha, o princípio fundamental prevalece: o **Model** encapsula os dados e as regras, a **View** exibe o que recebe, e uma **camada intermediária** bem definida orquestra a comunicação. Essa disciplina garante que sua aplicação possa evoluir de forma organizada, robusta e preparada para o futuro.

Lembre-se: não existe "bala de prata". A escolha depende do tamanho da equipe, da complexidade do projeto e da familiaridade da equipe com os padrões. Para muitos projetos React Native, uma arquitetura baseada em componentes, com lógica de apresentação em Custom Hooks e serviços bem definidos para o Model, oferece um bom equilíbrio de simplicidade, testabilidade e escalabilidade, incorporando princípios do MVVM.

O importante é sempre buscar:
* **Separação de Responsabilidades (SoC):** Cada parte do código tem um papel claro.
* **Testabilidade:** Conseguir testar a lógica de negócios e de apresentação independentemente da UI.
* **Manutenibilidade:** Facilitar a compreensão, modificação e extensão do código.

Nossa Pokédex, mesmo sem aderir rigidamente a um desses padrões formais, já aplica o princípio da separação de responsabilidades ao ter componentes de UI, uma camada de serviço para dados e tipos definidos. Conforme ela evoluir, poderemos introduzir Custom Hooks e outras técnicas para aprimorar ainda mais sua estrutura e refatorá-la, eventualmente, para um desses padrões!
