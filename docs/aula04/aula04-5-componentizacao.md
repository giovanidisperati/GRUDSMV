---
layout: aula
title: "5. Componentização"
parent:  Aula 04 - Começando o desenvolvimento mobile com React Native!
nav_order: 5
---

## **5. Componentização e Reutilização de UI com Props**

Interfaces modernas em React Native são construídas a partir da composição de componentes reutilizáveis, que funcionam como pequenas unidades visuais com comportamento próprio. Cada componente pode receber **props** (propriedades) como parâmetros de entrada e, a partir disso, renderizar uma saída personalizada — seja ela visual, funcional ou ambas.

Essa abordagem torna o código mais legível, reutilizável e modular. Além disso, contribui diretamente para a escalabilidade e manutenção da aplicação, que são requisitos indispensáveis em projetos reais.

Nesta seção, exploraremos gradualmente como criar componentes reutilizáveis com props, tipá-los com TypeScript, estruturar estilos de forma clara, compor interfaces com `children` e organizar arquivos e pastas de maneira sustentável!

### **5.1 O que é um componente**

Em React Native, um **componente** é uma função (ou classe) que usualmente retorna elementos visuais, como `View`, `Text`, `Image`, `TextInput` ou componentes personalizados. É importante destacar, entretanto, que um componente não precisa necessariamente renderizar algo visível: ele pode retornar `null`, ou apenas encapsular lógica e contexto. Além disso, o retorno pode ser um único elemento, um array ou mesmo um fragmento (`<>…</>`).

Desde a introdução dos **Hooks**, componente de função tornou-se a forma recomendada; componentes de classe continuam válidos, mas são cada vez menos usados em código novo e, por isso, vamos adotar a forma de componentes funcionais por padrão. 

Componentes podem ser utilizados várias vezes em uma interface, com diferentes valores de entrada, sem duplicar lógica ou estilo. Por exemplo:

```tsx
import { Text } from 'react-native';

export function Saudacao() {
  return <Text>Olá, mundo!</Text>;
}
```

Esse componente poderia ser inserido em qualquer parte da aplicação, promovendo consistência e economia de código - não que você queira ficar repetindo "Olá, mundo!" em vários lugares da sua aplicação, mas você entendeu a ideia, né? 👽

### **5.2 Props: propriedades que personalizam um componente**

Os **props** (propriedades) são o principal mecanismo para deixar um componente configurável: quem utiliza o componente passa valores da mesma forma que envia argumentos para uma função, e esses valores determinam tanto o que será exibido na interface quanto o comportamento interno do componente. Em TypeScript, declaramos a forma dessas propriedades numa `interface` (ou `type`), o que garante segurança de tipo e documentação automática.

```tsx
interface Props {
  nome: string; // a tela que usar <Saudacao> precisa fornecer essa string
}

export function Saudacao({ nome }: Props) {
  return <Text>Olá, {nome}!</Text>;
}
```

Na prática, basta instanciar o componente e fornecer o valor desejado:

```tsx
<Saudacao nome="Camila" />
```

Ao receber `nome="Camila"`, o componente renderiza “Olá, Camila!”. Se você reutilizar `<Saudacao>` em outra parte da aplicação com `nome="Bruno"`, o texto exibido mudará sem que seja preciso duplicar lógica ou alterar o componente original. Dessa forma, props tornam os componentes **dinâmicos, previsíveis e reutilizáveis**, adaptando-se ao contexto onde forem inseridos.

### **5.3 Criando um componente reutilizável: Botão personalizado**

Para evitar duplicação de código quando precisamos de vários botões com aparência idêntica, mas rótulos e comportamentos distintos, criamos um **componente reutilizável**. Ele expõe duas props: o texto exibido no botão e a função disparada no toque. Assim, a lógica de clique fica desacoplada do estilo e podemos reutilizar o mesmo layout em qualquer parte do app.

### Definição da interface das props

```tsx
interface BotaoProps {
  titulo: string;        // rótulo exibido no botão
  onPress: () => void;   // ação executada ao toque
}
```

### Estrutura do componente

```tsx
import { Text, TouchableOpacity, StyleSheet } from 'react-native';

export function BotaoPrimario({ titulo, onPress }: BotaoProps) {
  return (
    <TouchableOpacity style={styles.botao} onPress={onPress}>
      <Text style={styles.texto}>{titulo}</Text>
    </TouchableOpacity>
  );
}

const styles = StyleSheet.create({
  botao: {
    backgroundColor: '#6200ee',
    paddingVertical: 12,
    paddingHorizontal: 20,
    borderRadius: 8,
  },
  texto: {
    color: '#fff',
    fontWeight: 'bold',
    textAlign: 'center',
  },
});
```

### Utilização

```tsx
<BotaoPrimario titulo="Salvar" onPress={() => alert('Salvo!')} />
```

Com essa abordagem, basta trocar as props para adaptar o botão a novos contextos, enquanto o estilo e a estrutura permanecem consistentes em todo o aplicativo.

### **5.4 Props opcionais e valores padrão**

Nem sempre todas as propriedades precisam ser obrigatórias. Quando queremos oferecer personalização opcional — por exemplo, permitir que o chamador escolha a cor de fundo de um botão sem exigir esse valor — definimos a prop como opcional, usando o sufixo `?` na interface TypeScript. Depois, dentro do componente, aplicamos um **valor padrão** caso a prop não seja fornecida.

```tsx
interface BotaoProps {
  titulo: string;          // texto visível no botão
  onPress: () => void;     // ação disparada no toque
  corFundo?: string;       // cor de fundo opcional
}
```

Durante a renderização, usamos o operador de coalescência nula (`??`) para cair em uma cor padrão quando `corFundo` não é enviada:

```tsx
<TouchableOpacity
  style={[styles.botao, { backgroundColor: corFundo ?? '#6200ee' }]}
  onPress={onPress}
>
  <Text style={styles.texto}>{titulo}</Text>
</TouchableOpacity>
```

Assim, o componente funciona perfeitamente mesmo sem a prop `corFundo`, mas dá ao desenvolvedor liberdade para alterar a aparência sempre que necessário, sem duplicar um novo estilo ou componente.

### **5.5 Composição com `children`: componentes contêiner**

Em alguns casos, o componente precisa servir apenas como **contêiner** para outros elementos, delegando ao chamador a liberdade de decidir o que será exibido dentro dele. Nesses cenários recorremos à prop especial `children`, que representa todo o conteúdo colocado entre a tag de abertura e a de fechamento do componente. Ao tipar `children` como `React.ReactNode`, garantimos que qualquer elemento React — texto, imagens, outros componentes ou mesmo fragmentos — possa ser passado livremente. Considere o exemplo abaixo:

```tsx
interface CardProps {
  children: React.ReactNode; // qualquer conteúdo interno
}

export function Card({ children }: CardProps) {
  return <View style={styles.card}>{children}</View>;
}
```

O uso é direto: basta envolver os elementos desejados dentro de `<Card> ... </Card>`.

```tsx
<Card>
  <Text>Conteúdo do card</Text>
</Card>
```

O padrão acima transforma `Card` em um **invólucro visual**: ele cuida apenas do “quadro” — bordas, sombra, espaçamento, cor de fundo, etc. — e exibe, na região interna, tudo o que for passado como `children`. Quando você escreve

```tsx
<Card>
  <Text>Conteúdo do card</Text>
</Card>
```

Ou seja: o React substitui a palavra-chave `children` pelo elemento `<Text>` fornecido. Na renderização, o que aparece na tela é uma caixa (`View`) estilizada pelas regras de `styles.card` — por exemplo, um retângulo com fundo claro, cantos arredondados e sombra — contendo o texto “Conteúdo do card” centralizado (ou alinhado conforme o estilo aplicado).

### **5.6 Estilização com `StyleSheet.create`**

Sempre que criamos um componente com intenção de reaproveitá-lo em várias telas, vale agrupar todas as regras visuais em um objeto gerado por `StyleSheet.create`. Essa centralização traz três vantagens importantes: o **React Native valida** nomes de propriedades e tipos em tempo de desenvolvimento, os objetos de estilo são **pré-processados e compartilhados** (evitando recriação a cada render) e o código fica **mais legível**, pois a lógica do componente não se mistura com detalhes de cor, margem ou tipografia.

```tsx
const styles = StyleSheet.create({
  botao: {
    backgroundColor: '#ff5722',
    padding: 12,
    borderRadius: 6,
  },
  texto: {
    color: '#fff',
    fontSize: 16,
    textAlign: 'center',
  },
});
```

Mantendo o objeto `styles` em um arquivo separado (por exemplo `Botao.styles.ts`), garantimos isolamento de responsabilidades: quem ajusta o visual modifica apenas o arquivo de estilos, enquanto a lógica de clique e as props continuam intactas no componente principal. Isso simplifica refatorações, facilita colaboração entre designers e desenvolvedores e preserva a consistência visual do aplicativo.

### **5.7 Organização em arquivos e pastas**

Uma boa organização de pastas evita que o código se transforme em um amontoado difícil de navegar à medida que novos recursos são adicionados. No React Native, um padrão simples e eficaz é criar **uma pasta para cada componente**, contendo, no mínimo, o arquivo de código-fonte e seu arquivo de estilos. Desse modo, tudo que diz respeito a um componente fica reunido no mesmo lugar.

```
src/
  components/
    BotaoPrimario/
      index.tsx   ← lógica e markup do botão
      styles.ts   ← regras visuais do botão
    Card/
      index.tsx
      styles.ts
```

Com essa convenção, qualquer pessoa da equipe localiza rapidamente onde alterar visual ou comportamento, evita duplicações acidentais e pode reaproveitar os componentes em outras telas sem esforço. Além disso, ferramentas de versionamento (Git, por exemplo) exibem mudanças de forma mais clara, tornando o fluxo de revisão e manutenção muito mais ágil.


### **5.8 Exemplo integrado: `CardDeProduto`**

Um exemplo prático que une todos os conceitos anteriores é o componente `CardDeProduto`, que exibe nome, preço e status de disponibilidade de um item. Vejamos o código abaixo:

```tsx
interface Props {
  nome: string;
  preco: number;
  disponivel?: boolean;          // se omitido, assume true
}

export function CardDeProduto({ nome, preco, disponivel = true }: Props) {
  return (
    <View style={styles.container}>
      <Text style={styles.nome}>{nome}</Text>
      <Text style={styles.preco}>R$ {preco.toFixed(2)}</Text>
      <Text style={[styles.status, { color: disponivel ? 'green' : 'red' }]}>
        {disponivel ? 'DISPONÍVEL' : 'INDISPONÍVEL'}
      </Text>
    </View>
  );
}
```

O objeto de estilos, criado com `StyleSheet.create`, isola toda a aparência do cartão:

```tsx
const styles = StyleSheet.create({
  container: {
    padding: 12,
    margin: 8,
    borderRadius: 8,
    backgroundColor: '#f5f5f5',
  },
  nome: {
    fontWeight: 'bold',
    fontSize: 16,
  },
  preco: {
    fontSize: 14,
    marginTop: 4,
  },
  status: {
    marginTop: 8,
    fontWeight: '600',
  },
});
```

Esse é um exemplo de componente de apresentação que concentra, em poucas linhas, vários conceitos já discutidos: tipagem de props, estilos centralizados, renderização condicional e reaproveitamento de UI. Ele recebe três propriedades — `nome`, `preco` e a flag opcional `disponivel` — e transforma esses dados em um pequeno cartão informativo. 🤩

### Em suma

Aprendemos a:

* Criar componentes funcionais reutilizáveis com React Native;
* Declarar e tipar props, inclusive opcionais;
* Utilizar composição com `children`;
* Estilizar com `StyleSheet` de forma limpa e eficiente;
* Organizar a estrutura de projeto de maneira modular.

Essa combinação de técnicas será de grande importância nas próximas aulas, porque os aplicativos que construiremos daí em diante se apoiarão nesses padrões para evoluir com qualidade e manutenibilidade.