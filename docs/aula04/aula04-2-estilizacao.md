---
layout: aula
title: "2. Construção de interfaces!"
parent:  Aula 04 - Começando o desenvolvimento mobile com React Native!
nav_order: 2
---

## **2. Componentes Visuais Fundamentais**

A construção de interfaces no React Native parte do uso de componentes visuais que representam os elementos básicos da tela: textos, botões, campos de entrada, imagens, entre outros. Esses componentes são mapeados para os elementos nativos de cada sistema operacional (Android ou iOS), garantindo desempenho e comportamento visual adequado em diferentes plataformas.

Esta seção apresenta os principais componentes utilizados na criação de interfaces, com foco em suas propriedades mais relevantes, boas práticas de uso e exemplos ilustrativos escritos em TypeScript.

### **2.1 Principais componentes: `View`, `Text`, `TextInput`, `Image`, `Button` e `TouchableOpacity`**

#### 📦 `View`: estrutura e agrupamento de elementos

O componente `View` é utilizado como container visual para agrupar e estruturar outros elementos na interface. Ele é essencial para organizar o layout por meio de empilhamento, alinhamento e espaçamento dos componentes filhos.

```tsx
import { View } from 'react-native';

<View style={{ padding: 16, backgroundColor: '#eee' }}>
  {/* outros elementos aqui dentro */}
</View>
```

No exemplo acima a `View` mostra como criar um container visual com preenchimento interno de 16 pixels e fundo cinza claro. A `View` aqui, evidentemente, é o componente-base para estruturação da interface no React Native e funciona como um agrupador de outros elementos visuais. Seu papel se assemelha ao da tag `div` no HTML, sendo fundamental para organizar o layout e aplicar estilos como espaçamentos, alinhamentos e cores de fundo.

As principais propriedades da `View` são:

* `style`: objeto ou array de objetos que define o estilo visual do container (cores, bordas, espaçamento, etc.).
* `flexDirection`: define a orientação dos filhos (`'row'` para horizontal ou `'column'` para vertical).
* `justifyContent`: alinha os elementos filhos ao longo do eixo principal (por exemplo, `'center'`, `'space-between'`).
* `alignItems`: alinha os elementos no eixo secundário (por exemplo, `'stretch'`, `'center'`).
* `padding`, `margin`: definem os espaçamentos interno e externo do container, respectivamente.

Ao utilizar `View`, é possível estruturar a tela em blocos visuais reutilizáveis e bem organizados, seguindo princípios de composição.

#### 📝 `Text`: exibição de conteúdo textual

O componente `Text` é responsável por renderizar qualquer tipo de texto na interface. No React Native, todo conteúdo textual deve estar obrigatoriamente contido dentro de um componente `Text`, mesmo quando estiver inserido dentro de uma `View`.

```tsx
import { Text } from 'react-native';

<Text style={{ fontSize: 20, color: 'blue' }}>
  Olá, React Native!
</Text>
```

Aqui o `Text` exibe a frase “Olá, React Native!” com tamanho de fonte 20 e cor azul. O `Text` também permite estilizações como negrito, alinhamento, espaçamento entre linhas e até a composição de trechos com diferentes estilos em uma mesma frase.

As principais propriedades do `Text` são:

* `style`: personaliza a aparência do texto (tamanho da fonte, cor, alinhamento, espaçamento, etc.).
* `numberOfLines`: define o número máximo de linhas que o texto pode ocupar; útil para textos longos em espaços limitados.
* `ellipsizeMode`: define como o texto é cortado quando excede o número de linhas permitido (`'tail'`, `'middle'`, `'head'`).
* `textAlign`: permite alinhar o texto à esquerda, centro ou direita.

Além disso, o componente `Text` permite a composição de trechos com diferentes estilos dentro de um mesmo parágrafo, tornando-o útil para textos ricos e dinâmicos.

#### ✏️ `TextInput`: entrada de dados pelo usuário

Para permitir que o usuário digite informações, utilizamos o componente `TextInput`. Ele pode ser usado em formulários, campos de login, filtros de busca, entre outros.

```tsx
import { TextInput } from 'react-native';

<TextInput
  placeholder="Digite seu nome"
  value={nome}
  onChangeText={setNome}
  style={{ borderWidth: 1, padding: 8, borderRadius: 4 }}
/>
```

Acima temos um exemplo de uso do `TextInput`: um campo de entrada de texto que exibe uma sugestão (“Digite seu nome”) enquanto está vazio. Seu valor é controlado pela variável `nome`, e ele chama a função `setNome` sempre que o usuário digita algo. O campo também possui bordas, preenchimento interno e cantos arredondados, definidos por meio de seu `style`. É importante mencionar que esse componente deve ser combinado com `useState` para controle do conteúdo digitado.

As principais propriedades do `TextInput` são:

* `placeholder`: texto exibido quando o campo está vazio, como sugestão de preenchimento.
* `value`: valor atual do campo de entrada.
* `onChangeText`: função chamada sempre que o texto for alterado.
* `secureTextEntry`: oculta o texto digitado, ideal para campos de senha.
* `keyboardType`: define o tipo de teclado a ser exibido, como `email-address`, `numeric`, `phone-pad`, entre outros.
* `multiline`: permite que o campo aceite múltiplas linhas.

Em resumo, com `TextInput` é possível controlar tanto o conteúdo quanto o comportamento do campo, utilizando eventos e estados da aplicação.

#### 🖼️ `Image`: carregamento e exibição de imagens

O componente `Image` permite exibir imagens tanto locais quanto remotas. Ele é amplamente utilizado para ilustrações, avatares, logotipos, ícones e outros elementos gráficos.

```tsx
import { Image } from 'react-native';

<Image
  source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
  style={{ width: 50, height: 50 }}
/>
```

E para imagens locais:

```tsx
<Image
  source={require('./assets/logo.png')}
  style={{ width: 100, height: 100 }}
/>
```

Acima o exemplo do componente `Image`, utilizado para exibir imagens tanto locais quanto hospedadas em URLs. No primeiro exemplo, uma imagem remota é exibida com largura e altura de 50 pixels. No segundo, uma imagem local é carregada usando `require`. Em ambos os casos, a definição explícita de tamanho é obrigatória.

As principais propriedades do `Image` são:

* `source`: define a origem da imagem (com `require()` ou com `uri`).
* `style`: controla altura, largura, bordas e arredondamentos.
* `resizeMode`: define como a imagem se ajusta dentro do espaço disponível. Valores comuns incluem `'cover'`, `'contain'`, `'stretch'`.

Lembrando: a correta definição de largura e altura é obrigatória para que a imagem seja renderizada.

#### 🎯 `Button`: acionamento de ações simples

O componente `Button` representa um botão básico com comportamento nativo da plataforma. Ele é indicado para casos simples onde não há necessidade de personalização visual.

```tsx
import { Button } from 'react-native';

<Button title="Enviar" onPress={() => alert('Ação executada')} />
```

O componente `Button` mostrado cria um botão simples com o rótulo “Enviar” e executa uma ação (no caso, exibir um alerta) ao ser pressionado. Ele oferece uma solução rápida e nativa para ações básicas, mas tem limitações quanto à personalização visual. Por isso, é mais indicado para situações simples ou prototipagem inicial.

As principais propriedades do `Button` são:

* `title`: texto exibido no botão.
* `onPress`: função executada quando o botão for pressionado.
* `color`: define a cor do botão (limitação: comportamento varia entre Android e iOS).

> Observação importante: o componente `Button` não nos fornece muita flexibilidade de estilização. Para botões personalizados, portanto, utilizamos o componente `TouchableOpacity`.

#### 👆 `TouchableOpacity`: área interativa com personalização

O `TouchableOpacity` é um componente de toque que permite personalizar completamente o conteúdo visual e oferece feedback ao usuário ao reduzir a opacidade temporariamente quando pressionado.

```tsx
import { TouchableOpacity, Text } from 'react-native';

<TouchableOpacity
  onPress={() => alert('Pressionado')}
  style={{ backgroundColor: '#6200ee', padding: 12, borderRadius: 8 }}
>
  <Text style={{ color: 'white', textAlign: 'center' }}>Entrar</Text>
</TouchableOpacity>
```

O `TouchableOpacity`, acima, envolve um `Text` estilizado com fundo roxo e texto branco centralizado, e exibe um alerta ao toque. Esse componente permite total controle visual e oferece feedback ao usuário ao reduzir temporariamente sua opacidade quando pressionado. Como mencionado, é muito utilizado em botões customizados, cartões interativos e áreas clicáveis em geral.

As principais propriedades do `TouchableOpacity` são:

* `onPress`: função executada ao toque.
* `style`: permite estilizar totalmente o botão.
* `activeOpacity`: define o nível de opacidade ao toque (padrão: `0.2`).

Importante: o `TouchableOpacity` pode conter qualquer outro componente em seu interior, como `Text`, `Image` ou até ícones, sendo ideal para a criação de botões visuais consistentes com a identidade do app.

### **2.2 Propriedades comuns entre componentes**

Vistos os principais blocos de construção da interface, passamos agora a algumas propriedades que aparecem com frequência nos diferentes componentes e que contribuem diretamente para a organização visual e comportamental do app.

#### 🎨 `style`

A propriedade `style` permite aplicar regras de apresentação aos componentes. Ao contrário do CSS tradicional, os estilos em React Native são definidos com objetos JavaScript, podendo ser inseridos diretamente ou encapsulados com `StyleSheet.create()`.

```tsx
<Text style={{ fontWeight: 'bold', color: '#333' }}>
  Texto em destaque
</Text>
```

Usar `StyleSheet.create()` é recomendado, pois melhora a legibilidade do código e a performance da aplicação.

#### 🧭 Eventos: `onPress`, `onChangeText` e similares

Eventos são propriedades que recebem funções de callback e são executados em resposta a interações do usuário.

```tsx
<Button title="Sair" onPress={handleLogout} />
<TextInput onChangeText={(texto) => setTextoAtual(texto)} />
```

Essas propriedades permitem capturar o comportamento do usuário e atualizar a interface dinamicamente.

### **2.3 Boas práticas: organização, reaproveitamento e tipagem**

À medida que a aplicação cresce, é importante manter o código bem organizado e fácil de manter. Algumas práticas simples ajudam a evitar repetições, garantir legibilidade e permitir que diferentes partes da equipe possam colaborar de forma eficiente. A seguir, destacamos algumas dessas boas práticas adotadas em projetos com React Native.

A primeira é a criação de **componentes reutilizáveis**. Em vez de repetir blocos de código semelhantes em várias partes da interface, podemos agrupá-los em componentes próprios e reutilizáveis, como `BotaoPrimario` ou `CampoTexto`. Isso evita redundância, facilita ajustes e torna o código mais limpo.

Outra prática importante é manter os **estilos centralizados** com o uso do `StyleSheet.create()`. Ao invés de espalhar estilos inline pelos componentes, centralizar as definições em objetos nomeados melhora a organização e contribui para a padronização visual do app.

Por fim, o uso de **tipagem com TypeScript** ajuda a tornar o desenvolvimento mais seguro e previsível. Ao definir a estrutura das props com `interface` ou `type`, garantimos que os componentes sejam usados corretamente e evitamos erros em tempo de execução.

Um exemplo prático pode ser visto no componente abaixo:

```tsx
interface PropsBotao {
  titulo: string;
  onPress: () => void;
}

export function BotaoPrimario({ titulo, onPress }: PropsBotao) {
  return (
    <TouchableOpacity onPress={onPress} style={styles.botao}>
      <Text style={styles.texto}>{titulo}</Text>
    </TouchableOpacity>
  );
}
```

Esse botão pode ser reutilizado em diversos lugares da aplicação, com títulos e ações diferentes, mantendo o mesmo estilo visual e comportamento consistente. Essa abordagem modular facilita a manutenção e a evolução da interface conforme o projeto avança!