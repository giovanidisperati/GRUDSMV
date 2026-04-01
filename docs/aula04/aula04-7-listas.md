---
layout: aula
title: "7. Trabalhando com listas"
parent:  Aula 04 - Começando o desenvolvimento mobile com React Native!
nav_order: 7
---

## **7. Listas, Arrays e Renderização com `FlatList`**

Listas são um dos elementos **mais recorrentes em interfaces de aplicativos móveis**. Seja exibindo produtos, mensagens, notificações ou tarefas, **exibir dados de forma performática e organizada** é uma competência base em React Native.

Nesta seção, vamos explorar:

1. Como renderizar listas com `.map()` e `ScrollView`;
2. As limitações dessas abordagens e por que usar `FlatList`;
3. Como utilizar `FlatList` de forma eficiente e tipada;
4. Como aplicar estilos e chaves únicas;
5. Como personalizar renderizações com `ListHeaderComponent`, `ItemSeparatorComponent` e `ListEmptyComponent`;
6. Como aplicar boas práticas de performance.

### **7.1 Renderizando listas com `.map()` e `ScrollView`**

Para exibir listas de dados em React Native, uma abordagem comum é utilizar o método `.map()`, nativo do JavaScript. Este método permite percorrer arrays e transformar cada um de seus elementos em componentes React. Por exemplo, para um array de nomes, poderíamos fazer:

```tsx
const nomes = ['Ana', 'Bruno', 'Carlos'];

{nomes.map((nome, index) => (
  <Text key={index}>{nome}</Text>
))}
```

No código acima, cada nome no array `nomes` é transformado em um componente `Text`, e a propriedade `key` é fundamental para que o React possa identificar e gerenciar cada elemento da lista de forma eficiente.

Contudo, se a lista for extensa, o uso direto de `.map()` pode acarretar em problemas de performance. Isso ocorre porque todos os elementos da lista são renderizados de uma só vez, mesmo aqueles que não estão visíveis na tela, o que pode sobrecarregar a interface.

#### Usando `ScrollView` com `.map()`

Para permitir a rolagem de conteúdo que excede o tamanho da tela, podemos envolver a lista gerada pelo `.map()` com o componente `ScrollView`.

```tsx
import { ScrollView, Text } from 'react-native';

<ScrollView>
  {nomes.map((nome, index) => (
    <Text key={index}>{nome}</Text>
  ))}
</ScrollView>
```

Embora o `ScrollView` adicione a funcionalidade de rolagem, ele não resolve o problema fundamental da renderização de todos os itens de uma vez. Portanto, esta combinação é ideal apenas para listas pequenas, geralmente com menos de aproximadamente 20 itens, onde o impacto na performance não é significativo.

### **7.2 A solução: `FlatList`**

Para listas mais longas ou com requisitos de performance mais rigorosos, o componente `FlatList` é a solução ideal no React Native. Ele foi projetado especificamente para renderizar listas de dados de forma eficiente, oferecendo diversas vantagens: `FlatList` renderiza apenas os itens que estão atualmente visíveis na tela, além de uma pequena quantidade fora dela, implementando o conceito de "lazy loading". Isso significa que, à medida que o usuário rola a lista, novos itens são renderizados sob demanda e itens que saem da tela podem ser desalocados, economizando memória e poder de processamento. Além disso, `FlatList` oferece recursos nativos de rolagem tanto vertical quanto horizontal, permite a personalização de cabeçalhos, rodapés e componentes para listas vazias, é compatível com tipagem TypeScript para os dados da lista e, crucialmente, lida muito bem com listas grandes, dinâmicas e que exigem alta performance.

#### Exemplo básico com `FlatList`

Veja um exemplo simples de como utilizar o `FlatList` para renderizar o mesmo array de nomes:

```tsx
import { FlatList, Text } from 'react-native';

const nomes = ['Ana', 'Bruno', 'Carlos'];

<FlatList
  data={nomes}
  renderItem={({ item }) => <Text>{item}</Text>}
  keyExtractor={(item) => item}
/>
```

Neste exemplo, `data` recebe o array de dados, `renderItem` é uma função que define como cada item (`item`) será transformado em um componente (neste caso, um `Text`), e `keyExtractor` é uma função que retorna uma chave única para cada item (aqui, o próprio nome, assumindo que são únicos).

### **7.3 Propriedades essenciais de `FlatList`**

O `FlatList` possui diversas propriedades para customizar seu comportamento e aparência. As mais essenciais incluem:
* `data`: Esta propriedade recebe o array de dados que será exibido na lista.
* `renderItem`: Uma função que recebe um objeto contendo o item individual e seu índice, e retorna o componente React que deve ser renderizado para aquele item.
* `keyExtractor`: Uma função que recebe um item da lista e seu índice, e deve retornar uma string única que servirá como chave para aquele item. Serve para a otimização da renderização.
* `ListEmptyComponent`: Permite especificar um componente React a ser exibido quando o array `data` estiver vazio.
* `ItemSeparatorComponent`: Utilizado para renderizar um componente entre cada item da lista, como uma linha divisória.
* `ListHeaderComponent`: Permite renderizar um componente no topo da lista, antes do primeiro item.

### **7.4 Lista de objetos com tipagem**

Frequentemente, as listas são compostas por objetos mais complexos. Para garantir a consistência e aproveitar os benefícios do TypeScript, podemos definir uma interface para os objetos da lista. Vamos considerar um exemplo com uma lista de produtos, onde cada produto tem um `id`, `nome` e `preco`:

```tsx
interface Produto {
  id: number;
  nome: string;
  preco: number;
}
```

Com a interface `Produto` definida, podemos criar nosso array de produtos tipado:

```tsx
const produtos: Produto[] = [
  { id: 1, nome: 'Caderno', preco: 15 },
  { id: 2, nome: 'Caneta', preco: 5 },
  { id: 3, nome: 'Borracha', preco: 3 },
];
```

### Exibição com `FlatList`

Para exibir esta lista de produtos usando `FlatList`, faríamos:

```tsx
<FlatList
  data={produtos}
  renderItem={({ item }) => (
    <Text>
      {item.nome} – R$ {item.preco.toFixed(2)}
    </Text>
  )}
  keyExtractor={(item) => item.id.toString()}
/>
```
Na função `renderItem`, acessamos as propriedades `nome` e `preco` de cada `item`. Para `keyExtractor`, usamos o `id` do produto, convertendo-o para string com `item.id.toString()`, pois a chave extraída precisa ser uma string.

### **7.5 Customizando o item da lista com componente**

Para organizar melhor o código e permitir maior reutilização, é uma boa prática criar um componente separado para renderizar cada item da lista. Seguindo o exemplo dos produtos, podemos criar um componente `ItemProduto`:

```tsx
interface ProdutoProps {
  produto: Produto; // Usamos a mesma interface Produto definida anteriormente
}

function ItemProduto({ produto }: ProdutoProps) {
  return (
    <View style={styles.card}>
      <Text style={styles.nome}>{produto.nome}</Text>
      <Text style={styles.preco}>R$ {produto.preco.toFixed(2)}</Text>
    </View>
  );
}
```

Este componente `ItemProduto` recebe um `produto` como prop e o exibe dentro de uma `View` estilizada. Agora, podemos usar este componente na prop `renderItem` do nosso `FlatList`:

```tsx
<FlatList
  data={produtos}
  renderItem={({ item }) => <ItemProduto produto={item} />} // Usando o componente ItemProduto
  keyExtractor={(item) => item.id.toString()}
/>
```

Essa abordagem torna o `FlatList` mais legível e o componente `ItemProduto` pode ser estilizado e complexificado independentemente.

### **7.6 Adicionando componentes extras à lista**

O `FlatList` permite adicionar elementos visuais além dos próprios itens da lista, como cabeçalhos, separadores e um componente para quando a lista está vazia.

Para adicionar um **cabeçalho**, utilizamos a propriedade `ListHeaderComponent`. Ela espera uma função que retorna o componente a ser exibido no topo:
```tsx
<FlatList
  // ... outras props
  ListHeaderComponent={() => <Text style={styles.titulo}>Produtos Disponíveis</Text>}
/>
```

Para inserir um **separador** entre os itens, usamos `ItemSeparatorComponent`. Similarmente, ele recebe uma função que retorna o componente separador:
```tsx
<FlatList
  // ... outras props
  ItemSeparatorComponent={() => <View style={styles.separador} />}
/>
```

E para exibir uma mensagem ou componente quando a lista não possui itens (ou seja, o array `data` está vazio), usamos `ListEmptyComponent`:
```tsx
<FlatList
  data={[]} // Exemplo com lista vazia
  // ... outras props
  ListEmptyComponent={() => <Text style={{ textAlign: 'center' }}>Nenhum item disponível</Text>}
/>
```

### **7.7 Scroll horizontal com `horizontal`**

Por padrão, o `FlatList` rola verticalmente. Para criar uma lista com rolagem horizontal, basta adicionar a propriedade `horizontal` (que é um booleano e, quando presente, assume `true`):

```tsx
<FlatList
  data={produtos}
  horizontal // Habilita a rolagem horizontal
  renderItem={({ item }) => <ItemProduto produto={item} />}
  keyExtractor={(item) => item.id.toString()}
/>
```

Isso é particularmente útil para implementar carrosséis de imagens, listas de destaque, seções de "stories" ou qualquer outro layout que se beneficie da navegação lateral.

### **7.8 Estilizando a lista**

A estilização dos itens da lista, bem como de seus componentes extras (cabeçalho, separador), é feita utilizando `StyleSheet`, da mesma forma que qualquer outro componente React Native. Abaixo, um exemplo de como os estilos para os componentes `ItemProduto`, título e separador poderiam ser definidos:

```tsx
const styles = StyleSheet.create({
  titulo: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 8,
  },
  card: {
    backgroundColor: '#f2f2f2',
    padding: 12,
    borderRadius: 8,
    marginVertical: 4, // Para listas verticais
    // marginHorizontal: 4, // Poderia ser usado para listas horizontais
  },
  nome: {
    fontSize: 16,
  },
  preco: {
    color: 'green',
  },
  separador: {
    height: 1,
    backgroundColor: '#ccc',
    marginVertical: 4,
  },
});
```

Esses estilos seriam aplicados aos respectivos componentes dentro de `renderItem`, `ListHeaderComponent`, `ItemSeparatorComponent`, etc.

### **7.9 Dicas de performance com `FlatList`**

Para garantir que seu `FlatList` funcione da maneira mais performática possível, especialmente com grandes volumes de dados, algumas práticas são recomendadas:

* Sempre utilize a propriedade `keyExtractor` para fornecer chaves únicas e estáveis para cada item. Isso ajuda o React a otimizar as re-renderizações, evitando atualizações desnecessárias.
* Evite criar componentes ou funções inline diretamente na prop `renderItem` (ou em outras props que esperam componentes/funções), pois isso pode levar à recriação dessas funções a cada renderização. Se possível, extraia a lógica de `renderItem` para uma função separada ou um componente memorizado.
* Para listas muito grandes, considere o uso das propriedades `initialNumToRender` (para controlar quantos itens são renderizados no lote inicial) e `maxToRenderPerBatch` (para controlar quantos itens são renderizados por lote durante a rolagem). Ajustar esses valores pode otimizar o tempo de carregamento inicial e a suavidade da rolagem.
* Reiterando, evite a combinação de `ScrollView` com `.map()` para listas grandes, pois a renderização de todos os itens de uma vez pode travar a interface do usuário.
* Se os itens da sua lista forem componentes complexos e você perceber problemas de performance mesmo com as otimizações acima, considere usar `React.memo` para memorizar os componentes de item, evitando que eles sejam re-renderizados se suas props não mudarem.

