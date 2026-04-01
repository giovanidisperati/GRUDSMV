---
layout: aula
title: "4. Layout com Flexbox!"
parent: Aula 04 - Interfaces, Estilização e Listas no React Native
nav_order: 4
---

## **4. Layout com Flexbox no React Native**

Diferentemente do desenvolvimento web, onde há múltiplos modelos de layout (como grid, float e table), no React Native o modelo adotado para posicionamento e distribuição de elementos é o **Flexbox**. Isso torna seu domínio fundamental para a construção de interfaces.

O React Native utiliza o **Yoga Layout**, que implementa a especificação Flexbox quase integralmente. Na prática, isso significa que todo fluxo normal de layout — alinhamento, distribuição de espaço e dimensionamento proporcional — passa pelas regras do Flexbox, embora ainda possamos recorrer a `position: 'absolute'` para casos pontuais. A sintaxe é quase idêntica à da Web, mas há diferenças de valores padrão e de suporte a algumas propriedades. Por exemplo, o eixo principal (`flexDirection`) assume **`column`** por padrão, não `row`; `flexShrink` começa em **0** (na Web é 1) e o atalho `flex` aceita apenas um número simples. Para mais informações, é importante consultar a documentação oficial do [Flexbox no React Native](https://reactnative.dev/docs/flexbox).

Embora o Flexbox no React Native siga a lógica do Flexbox do CSS, há simplificações na sua implementação e algumas adaptações necessárias ao ambiente mobile. A seguir, veremos como utilizá-lo na prática por meio das principais propriedades aplicáveis tanto ao container quanto aos seus itens.

### **4.1 Fundamentos do Flexbox**

Flexbox é um sistema de layout unidimensional que organiza elementos filhos dentro de um container em **linha** (horizontal) ou **coluna** (vertical), de forma flexível e adaptável ao espaço disponível.

No React Native, as principais propriedades relacionadas ao Flexbox são aplicadas por meio do objeto `style` da `View` que atua como container:

* `flexDirection`
* `justifyContent`
* `alignItems`
* `flex`
* `flexWrap`

Essas propriedades controlam tanto o eixo principal (horizontal ou vertical) quanto o eixo secundário (perpendicular ao principal), além do comportamento de crescimento, quebra de linha e alinhamento dos elementos. Vamos explorá-las!

### **4.2 Propriedades do Container Flex**

#### `flexDirection`: define a direção dos itens

Essa propriedade determina se os elementos filhos da `View` serão organizados em linha ou coluna.

```tsx
<View style={{ flexDirection: 'row' }}>
  {/* elementos lado a lado */}
</View>

<View style={{ flexDirection: 'column' }}>
  {/* elementos empilhados verticalmente (padrão) */}
</View>
```

Os valores possíveis para essa propriedade são:

* `'row'`: da esquerda para a direita
* `'column'`: de cima para baixo (valor padrão)
* `'row-reverse'` ou `'column-reverse'`: distribuições invertidas

#### `justifyContent`: alinha os itens no eixo principal

Essa propriedade alinha os elementos ao longo do eixo definido por `flexDirection`. Se `flexDirection` for `'row'`, o eixo principal é horizontal; se for `'column'`, é vertical.

```tsx
<View style={{ justifyContent: 'center' }}>
  {/* centraliza os filhos no eixo principal */}
</View>
```

Valores comuns para o `justifyContent` são:

| Valor           | Comportamento                                    |
| --------------- | ------------------------------------------------ |
| `flex-start`    | Alinha no início do eixo principal               |
| `center`        | Centraliza os elementos                          |
| `flex-end`      | Alinha no final do eixo                          |
| `space-between` | Espaço igual entre os elementos                  |
| `space-around`  | Espaço igual em torno de cada elemento           |
| `space-evenly`  | Espaço igual entre, antes e depois dos elementos |

#### `alignItems`: alinha os itens no eixo secundário

Enquanto `justifyContent` trata do eixo principal, `alignItems` atua no eixo perpendicular. Ele define como os itens são distribuídos transversalmente.

```tsx
<View style={{ alignItems: 'center' }}>
  {/* centraliza os filhos no eixo secundário */}
</View>
```

Valores comuns para o `alignItems` são:

| Valor        | Descrição                                           |
| ------------ | --------------------------------------------------- |
| `flex-start` | Alinha os itens no início do eixo secundário        |
| `center`     | Centraliza os itens no eixo secundário              |
| `flex-end`   | Alinha os itens ao final                            |
| `stretch`    | Estica os itens para preencher o container (padrão) |
| `baseline`   | Alinha com base na linha de base do texto           |

#### `flexWrap`: controle de quebra de linha

Quando os itens excedem a largura (ou altura) do container, `flexWrap` define se eles devem quebrar a linha.

```tsx
<View style={{ flexWrap: 'wrap', flexDirection: 'row' }}>
  {/* os filhos quebrarão linha se necessário */}
</View>
```

Os valores possíveis para `flexWrap` são:

* `'nowrap'` (padrão): tudo na mesma linha
* `'wrap'`: permite quebra em múltiplas linhas
* `'wrap-reverse'`: quebra em sentido inverso

### **4.3 Propriedades aplicadas aos itens**

#### `flex`: define a proporção do espaço ocupado

A propriedade `flex` indica quanto espaço um item ocupará em relação aos outros itens dentro do mesmo container.

```tsx
<View style={{ flex: 1 }} />
<View style={{ flex: 2 }} />
```

Neste exemplo, o segundo item ocupará o dobro da largura do primeiro, respeitando a soma total do espaço disponível.

Um valor `flex: 1` indica que o item ocupará todo o espaço restante dentro do container.

#### `alignSelf`: alinhamento individual

Usada para sobrescrever o `alignItems` apenas para um item específico. Ideal para aplicar uma exceção de alinhamento dentro do grupo.

```tsx
<View style={{ alignSelf: 'center' }}>
  {/* este item será centralizado, independentemente dos demais */}
</View>
```

### **4.4 Dicas práticas para trabalhar com Flexbox**

Antes de aplicar Flexbox em seus layouts, vale ter em mente algumas recomendações que ajudam a tirar o máximo proveito do sistema e evitam armadilhas comuns. Seguindo as boas práticas abaixo, você consegue construir interfaces responsivas de forma mais previsível:

* Tenha clareza sobre qual é o eixo principal (`flexDirection`) e qual é o eixo secundário.
* Utilize `flex: 1` para expandir elementos, principalmente em containers que devem ocupar todo o espaço da tela.
* Combine `justifyContent` com `alignItems` para centralizações completas.
* Evite usar valores fixos quando possível. Flexbox proporciona comportamentos adaptativos que funcionam melhor em diferentes tamanhos de tela.

### ✨ Resumão

Flexbox é o sistema base de layout do React Native. Seu funcionamento simples permite estruturar e distribuir componentes de maneira adaptável e precisa. Ao dominar as propriedades do Flexbox e combiná-las com boas práticas de organização de estilos, você terá mais controle sobre o comportamento visual dos seus aplicativos. Isso garante interfaces que se ajustam bem a diferentes tamanhos de tela, mantendo consistência e legibilidade.

Abaixo resumimos as principais propriedades do FlexBox no React Native 🤠

| Categoria                       | Propósito                                        | Propriedades–chave                                                 |
| ------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------ |
| Direção do fluxo                | Define o eixo principal                          | `flexDirection` (`row`, `row-reverse`, `column`, `column-reverse`) |
| Distribuição no eixo principal  | Espaça ou centraliza itens                       | `justifyContent` (`flex-start`, `center`, `space-between`, etc.)   |
| Alinhamento no eixo transversal | Controla como os itens “cruzam” o eixo principal | `alignItems` e `alignSelf`                                         |
| Crescimento / encolhimento      | Divide o espaço disponível ou resolve overflow   | `flex`, `flexGrow`, `flexShrink`, `flexBasis`                      |
| Quebra de linha                 | Permite várias linhas                            | `flexWrap` (`nowrap`, `wrap`)                                      |
| Várias linhas                   | Distribuição das linhas geradas por `wrap`       | `alignContent`                                                     |
| Espaços (RN ≥ 0.72)             | Gaps uniformes entre linhas/colunas              | `rowGap`, `columnGap`, `gap`                    |
