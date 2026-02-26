---
layout: aula
title: "5. Componentização e Reutilização"
parent: Aula 04 - Interfaces, Estilização e Listas no React Native
nav_order: 3
---

## **3. Estilização com StyleSheet**

A aparência dos componentes visuais em React Native é definida por meio de objetos JavaScript que descrevem propriedades de estilo semelhantes às do CSS. Embora a ideia geral seja familiar para quem já trabalhou com a web, existem diferenças significativas de sintaxe, comportamento e organização dos estilos em ambientes móveis.

A ferramenta principal utilizada para estilização é o `StyleSheet.create`, que permite definir estilos de forma estruturada, segura e eficiente em termos de performance. A seguir, vamos examinar como ele funciona, por que é recomendado e quais boas práticas podem ser adotadas para manter a coesão visual do projeto.

### **3.1 O que é o `StyleSheet.create` e por que utilizá-lo**

O módulo `StyleSheet`, fornecido pelo React Native, permite criar objetos de estilo de maneira otimizada. O método `create()` é utilizado para declarar estilos que serão reaproveitados entre renderizações, garantindo maior previsibilidade e organização do código.

O `StyleSheet.create` é a forma recomendada de definir estilos no React Native, e seu uso traz uma série de benefícios tanto em termos de organização quanto de performance da aplicação. Ao invés de escrever objetos de estilo diretamente dentro dos componentes, podemos encapsular todas as regras visuais em um único local, com nomeação clara e validação automática.

Uma das principais vantagens é a **validação estática**: erros de digitação em nomes de propriedades ou valores incompatíveis com o esperado são identificados ainda durante o desenvolvimento, o que evita bugs sutis em tempo de execução. Além disso, como os objetos criados com `StyleSheet.create` são **pré-processados** pelo framework, eles consomem menos recursos e não são recriados a cada renderização, o que contribui para uma **melhor performance**.

Outro ponto positivo é a **separação entre lógica e aparência**, que torna o código mais legível e fácil de manter. Ao agrupar os estilos em blocos específicos, conseguimos ter uma visão mais clara da estrutura visual da interface. Isso também reforça o uso de **boas práticas**, como a modularização e o reaproveitamento de estilos entre diferentes componentes.

Veja um exemplo de uso:

```tsx
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  titulo: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#333',
  },
  container: {
    padding: 16,
    backgroundColor: '#f9f9f9',
  },
});
```

Esses estilos podem ser aplicados diretamente aos componentes por meio da propriedade `style`, como em `<Text style={styles.titulo}>` ou `<View style={styles.container}>`. Essa abordagem melhora a consistência visual e simplifica eventuais ajustes na aparência do app.

### **3.2 Estilos inline e com `StyleSheet`: diferenças e usos adequados**

Outro ponto a destacar é que é possível aplicar estilos diretamente no componente, usando objetos JavaScript de forma literal:

```tsx
<Text style={{ fontSize: 20, color: 'blue' }}>Texto</Text>
```

Ou ainda, referenciando um objeto de estilos criado com `StyleSheet`:

```tsx
<Text style={styles.titulo}>Texto</Text>
```

Ambas as abordagens são válidas, mas apresentam diferenças importantes em termos de desempenho, clareza e reaproveitamento. Vamos sintetizar isso abaixo:

| Critério           | Estilo Inline                 | `StyleSheet.create`                  |
| ------------------ | ----------------------------- | ------------------------------------ |
| Organização visual | Pouco estruturado             | Centralizado e modular               |
| Reaproveitamento   | Limitado                      | Amplo, entre componentes             |
| Validação de erros | Ausente                       | Presente (durante o desenvolvimento) |
| Performance        | Recria o objeto a cada render | Reutiliza objeto otimizado           |

O uso de estilos inline pode ser útil em situações pontuais, como testes rápidos ou ajustes locais. No entanto, para projetos em produção, recomenda-se a utilização do `StyleSheet.create`, que fornece maior controle e previsibilidade.

### **3.3 Estratégias para organização e reaproveitamento de estilos**

Visto que a manutenção visual de uma aplicação tende a se tornar mais complexa à medida que ela cresce, adotar uma abordagem modular para os estilos é importante. Vejamos algumas estratégias e boas práticas abaixo.

#### 1. Estilos separados por componente

É uma boa prática organizar os estilos de cada componente em arquivos próprios. Isso contribui para o isolamento de responsabilidades e facilita a leitura.

Imagine que temos um componente chamado `BotaoPrimario`. Em vez de misturar lógica e estilo no mesmo arquivo (`BotaoPrimario.tsx`), criamos um arquivo separado chamado `BotaoPrimario.styles.ts` para armazenar seus estilos. A estrutura do diretório fica assim:

```bash
src/
  components/
    BotaoPrimario.tsx
    BotaoPrimario.styles.ts
```

Dentro do arquivo de estilos, usamos o `StyleSheet.create` para organizar as regras visuais de forma clara:

```ts
// BotaoPrimario.styles.ts
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  botao: {
    backgroundColor: '#6200ee',
    padding: 12,
    borderRadius: 8,
  },
  texto: {
    color: 'white',
    fontWeight: '600',
    textAlign: 'center',
  },
});
```

Esses estilos são então importados e utilizados dentro do componente `BotaoPrimario.tsx`, mantendo a lógica e a apresentação separadas, mas integradas. Isso permite, por exemplo, que uma equipe trabalhe em paralelo na lógica de um componente e em seu estilo, sem conflitos e com mais clareza no controle das mudanças.


#### 2. Definição de temas reutilizáveis

Outra prática importante para manter a consistência visual em aplicações React Native é a **centralização dos estilos globais** — como cores, tamanhos de fonte e espaçamentos — em um arquivo de tema. Essa estratégia permite que todas as telas e componentes da aplicação compartilhem os mesmos valores visuais, promovendo padronização e facilitando ajustes futuros.

Para isso, criamos um arquivo chamado `theme.ts`, geralmente localizado na pasta `src/`, que exporta constantes com os principais estilos reutilizáveis. Um exemplo de organização pode ser:

```ts
// src/theme.ts
export const COLORS = {
  primary: '#6200ee',
  background: '#f9f9f9',
  text: '#333',
  error: '#d32f2f',
};

export const FONT_SIZES = {
  small: 14,
  medium: 18,
  large: 24,
};
```

Essas constantes são então importadas dentro dos arquivos de estilo (`.styles.ts`) dos componentes, como mostra o exemplo a seguir:

```ts
import { COLORS, FONT_SIZES } from '../theme';

export const styles = StyleSheet.create({
  titulo: {
    color: COLORS.text,
    fontSize: FONT_SIZES.large,
  },
});
```

Com isso, sempre que precisarmos alterar uma cor ou ajustar um tamanho de fonte na aplicação inteira, basta modificar o valor correspondente no arquivo `theme.ts`. Esse padrão melhora a manutenção, evita repetições desnecessárias e fortalece a identidade visual do app ao longo do tempo.


#### 3. Combinação condicional de estilos

O React Native permite aplicar múltiplos estilos a um mesmo componente utilizando **arrays de estilos**. Essa abordagem é bastante útil para aplicar combinações de estilos fixos e dinâmicos, de acordo com o estado da aplicação ou com alguma lógica condicional.

No exemplo abaixo, temos dois estilos sendo aplicados a um componente `Text`: `styles.texto` e `styles.destaque`. Ambos são utilizados de forma conjunta e sempre serão aplicados:

```tsx
<Text style={[styles.texto, styles.destaque]}>
  Destaque
</Text>
```

Por outro lado, é possível também aplicar estilos **condicionalmente**. Veja o caso a seguir, em que o estilo `styles.textoAtivo` só será aplicado se a variável `ativo` for verdadeira:

```tsx
<Text style={[styles.texto, ativo && styles.textoAtivo]}>
  Texto interativo
</Text>
```

Essa técnica é útil porque o React Native ignora automaticamente qualquer valor `false` dentro do array de estilos, tornando o código limpo e expressivo. Isso significa que, se `ativo` for `false`, apenas `styles.texto` será aplicado ao componente. É uma maneira elegante de controlar o visual da interface com base em variáveis de estado ou props sem precisar escrever estruturas condicionais mais complexas.

#### ✨ Considerações finais

A estilização com `StyleSheet` é um ponto de grande importância na construção de interfaces consistentes, acessíveis e bem organizadas. Algumas práticas recomendadas incluem:

* **Evitar valores fixos dispersos** (como cores e tamanhos): prefira constantes compartilhadas.
* **Organizar os estilos por componente**: facilita o isolamento e a reutilização.
* **Utilizar o `StyleSheet.create` sempre que possível**, evitando estilos inline em larga escala.
* **Adotar um tema global**: melhora a coerência visual e permite ajustes rápidos em todo o projeto.

Para projetos maiores, é possível integrar bibliotecas como [NativeWind](https://www.nativewind.dev/) ou [Styled Components](https://styled-components.com/) para lidar com temas, responsividade e composição de estilos — temas que serão explorados nas próximas aulas.

Com a base de estilização consolidada, seguimos agora para o modelo de layout mais utilizado no React Native: o **Flexbox**, responsável por controlar alinhamentos, espaçamentos e organização responsiva dos elementos na tela.

---
