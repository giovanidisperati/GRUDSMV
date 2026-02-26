---
layout: aula
title: "1 & 2. View e Componentes Visuais"
parent: Aula 04 - Interfaces, Estilização e Listas no React Native
nav_order: 1
---

## **1.1 O papel da View: containers e composição de layout**

No React Native, todos os elementos visuais são baseados em componentes. O mais fundamental deles é o `View`, que serve como **container genérico** para agrupar e posicionar outros componentes na tela. É possível imaginar o `View` como o "div" do React Native — ou, em termos mais conceituais, como um bloco visual que organiza outros blocos.

Considere o código abaixo:

```tsx
import { View, Text } from 'react-native';

export default function App() {
  return (
    <View>
      <Text>Olá, mundo!</Text>
    </View>
  );
}
```

Nesse exemplo, o componente `App` renderiza uma `View` que contém um `Text`. Mesmo sem estilo algum, esse é o ponto de partida de qualquer layout em React Native.

Também é importante ter em mente que a construção de telas em React Native é baseada na **composição de componentes**. Ou seja, em vez de criar estruturas grandes e monolíticas, o ideal é dividir a interface em partes menores e reutilizáveis. Essa abordagem modular traz legibilidade, reutilização e facilita a manutenção do código.

### **1.2 Ciclo visual de construção de interfaces**

Criar a parte visual de um app usualmente não é um processo linear, que acontece de uma vez. Assim como nas demais atividades de desenvolvimento de Software, a abordagem iterativa é comum: testes, ajustes e refinamentos vão acontecendo aos poucos e intercaladamente, o que ajuda a obtermos resultados mais sólidos e funcionais. Abaixo estão as etapas mais comuns desse processo:

1. **Rascunho ou prototipação da tela**
   Antes de abrir o editor de código, é boa prática a prototipação de uma ideia da interface. Podemos fazer isso em ferramentas específicas e mais complexas, como o **Figma**, que permite criar interfaces mais detalhadas e até interativas, ou o **Adobe XD**. Também podemos trabalhar com ferramentas mais simples como o **Balsamiq Wireframes** (ferramenta paga, mas com Trial), Canva ou **Frame0** (em versão beta, mas, até o momento, gratuita!), que simula o estilo de um rascunho desenhado à mão e é ótimo para organizar ideias de layout! Em última instância, pode até ser feito à mão no papel - o importante é termos uma ideia geral do que vamos querer fazer.

2. **Montagem da estrutura com componentes visuais**
   Com base no esboço, começa-se a montar a tela com os blocos básicos do React Native: `View` para agrupar elementos, `Text` para exibir textos, `Image` para imagens, e assim por diante. O foco aqui é só estruturar a tela, sem se preocupar com estilo.

3. **Aplicação de estilos com `StyleSheet.create()`**
   Depois de montar a estrutura, o próximo passo é aplicar estilos para posicionar os elementos, definir cores, espaçamentos e fontes. O React Native usa o `StyleSheet.create()` para organizar os estilos de forma clara e reaproveitável.

4. **Testes no Expo em diferentes tamanhos de tela**
   Com a interface pronta, é importante testar como ela se comporta em celulares de tamanhos variados. O **Expo Go**, além de ser leve e fácil de usar, permite ver o app rodando direto no celular. Assim, dá pra perceber rapidamente se algo ficou espremido, desalinhado ou cortado. Além disso, podemos usar o **Expo** para web e visualizar em diferentes tamanhos de dispositivos por meio do modo mobile dos browsers.

5. **Ajustes de responsividade e refinamento visual**
   Por fim, vem a parte dos ajustes finos: ajustar margens, fontes, cores e tornar o layout mais flexível, para que funcione bem em vários tipos de dispositivos. Aqui vale usar propriedades como `flex`, `padding`, `margin` e recursos como `Dimensions`, `Platform` e até bibliotecas como **react-native-responsive-dimensions** para facilitar, as quais veremos posteriormente no curso.

Esse ciclo — rascunho, estrutura, estilo, teste e ajuste — costuma se repetir várias vezes ao longo do desenvolvimento. Trabalhar assim, de forma incremental, ajuda a identificar problemas cedo e garantir que o app fique funcional desde o começo. Um ponto importante a se considerar é a UX (*user experience*). Nas aulas da disciplina de IHC (Interação humano-computador) vocês já devem ter visto sobre o tema, mas trataremos mais a diante sobre isso também nessa disciplina de desenvolvimento para dispositivos móveis.

### 🚀 **Vamos começar pelo começo?**

Para acompanhar esta aula com exemplos práticos, vamos criar um projeto novo usando Expo e TypeScript com o seguinte comando:

```bash
npx create-expo-app aula04-layouts --template
```

Escolha o template `blank (TypeScript)` quando solicitado.

Em seguida, acesse o diretório do projeto:

```bash
cd aula04-layouts
```

E inicie o servidor de desenvolvimento:

```bash
npx expo start
```

A partir desse ponto, vamos escrever e testar o código da aula diretamente neste projeto. Todos os exemplos seguintes podem ser copiados e colados no arquivo `App.tsx`, ou em novos componentes criados dentro de uma pasta `src/components`.

### 🧪 Deseja testar os exemplos também no navegador?

Se quiser rodar o projeto no modo **web** (pressionando `w` no terminal), você precisará instalar algumas dependências específicas do React Native for Web. Basta executar o comando abaixo:

```bash
npx expo install react-dom react-native-web @expo/metro-runtime
```

Após isso, reinicie o servidor com `npx expo start` e pressione `w` para abrir a aplicação no navegador.

Tudo rodando aí em suas respectivas máquinas? 🧑‍💻

Então daremos início à exploração dos componentes visuais fundamentais do React Native, como `Text`, `Button`, `TextInput` e `TouchableOpacity`, entendendo suas propriedades e como tipá-los corretamente em TypeScript. Utilize o projeto que você acabou de gerar acima para ir testando, ao longo da aula, os componentes e conceitos que serão abordados. 😊

---
