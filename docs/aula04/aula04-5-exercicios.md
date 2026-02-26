---
layout: aula
title: "8 & 9. Mãos à Obra e Exercícios"
parent: Aula 04 - Interfaces, Estilização e Listas no React Native
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

---

# **6. Interatividade com Inputs e Eventos**

Um aplicativo mobile que não responde ao usuário... não é um app 😅. Nesta seção, vamos aprender como:

1. Capturar eventos de toque (taps, cliques);
2. Trabalhar com campos de texto;
3. Controlar o valor de inputs com estados;
4. Aplicar validação básica de entrada;
5. Combinar interatividade com componentização e TypeScript.

Esses conceitos compõem a base para **formulários**, **autenticação**, **cadastros**, **buscas** e diversas funcionalidades de apps reais.

### **6.1 Capturando eventos de toque com `Button` e `TouchableOpacity`**

Botões são elementos interativos que disparam ações. A forma mais simples de capturar um clique é com o componente `Button`:

```tsx
import { Button } from 'react-native';

<Button title="Enviar" onPress={() => alert('Enviado!')} />
```

O evento de toque é tratado pela prop `onPress`, que espera uma função a ser executada.

Já para maior controle de estilo, usamos `TouchableOpacity`, como vimos anteriormente.

```tsx
import { TouchableOpacity, Text } from 'react-native';

<TouchableOpacity onPress={() => alert('Toque!')}>
  <Text>Toque Aqui</Text>
</TouchableOpacity>
```

Esse componente **não tem estilo próprio**, e deve ser **combinado com um `<Text>` ou `<View>`** com aparência de botão.

### **6.2 Criando botões reutilizáveis com eventos**

Quando precisamos repetir um mesmo botão em várias telas, a melhor estratégia é extrair o layout e o feedback visual para um **componente de alto nível**. Assim, cada tela envia apenas o rótulo e a ação, enquanto o estilo, a opacidade de toque e as boas práticas de acessibilidade ficam centralizadas em um único lugar. Considere o exemplo abaixo:

```tsx
import { Text, TouchableOpacity, StyleSheet } from 'react-native';

interface BotaoProps {
  /** Texto que aparecerá no centro do botão */
  titulo: string;
  /** Função disparada no toque */
  onPress: () => void;
}

export function BotaoPrimario({ titulo, onPress }: BotaoProps) {
  return (
    <TouchableOpacity
      style={styles.botao}
      activeOpacity={0.7}           /* feedback de toque */
      accessibilityRole="button"    /* acessibilidade nativa */
      accessibilityLabel={titulo}   /* leitor de tela */
      onPress={onPress}
    >
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

Para utilizá-lo, basta adicionar o componente:

```tsx
<BotaoPrimario
  titulo="Cadastrar"
  onPress={() => console.log('Cadastro enviado')}
/>
```

Com essa abordagem, você mantém **consistência visual** em todo o app, facilita ajustes de design futuro (basta alterar `styles.botao`) e garante que cada novo botão já venha com acessibilidade e feedback tátil configurados de fábrica.

### **6.3 Capturando texto com `TextInput`**

O `TextInput` é o ponto-de-partida para qualquer formulário: ele renderiza um **campo editável** que já lida com foco, teclado virtual e acessibilidade nativa.

```tsx
import { TextInput } from 'react-native';

<TextInput
  placeholder="Digite seu nome"
  keyboardType="default"   // numérico, e-mail, phone-pad…
  autoCapitalize="words"   // controla capitalização automática
  returnKeyType="done"     // muda o rótulo da tecla “Enter”
  style={{ borderWidth: 1, borderRadius: 4, padding: 8 }}
/>
```

Nesse componente temos essas props que costumamos utilizar:

| Prop                      | Para que serve                                                                     |
| ------------------------- | ---------------------------------------------------------------------------------- |
| `value` / `onChangeText`  | transformam o campo em **input controlado** com `useState`                         |
| `placeholder`             | mostra uma sugestão enquanto o usuário não digitou nada                            |
| `secureTextEntry`         | esconde o texto (senhas)                                                           |
| `keyboardType`            | escolhe o teclado ideal: `email-address`, `numeric`, `phone-pad`, etc.             |
| `autoCapitalize`          | liga/desliga capitalização automática (`none`, `sentences`, `words`, `characters`) |
| `maxLength` / `multiline` | limita caracteres ou permite múltiplas linhas                                      |

Com apenas essas opções você já cobre a maioria dos cenários de login, busca, formulários de cadastro e qualquer campo de texto que precise no aplicativo.

### **6.4 Controlando inputs com `useState`**

Para que o campo de texto reflita imediatamente o que o usuário digita — e vice-versa — transformamos o `TextInput` em um **input controlado**. O truque é armazenar o conteúdo em um estado React e atualizar esse estado a cada digitação. Vejamos o exemplo abaixo:

```tsx
import { useState } from 'react';
import { TextInput, Text } from 'react-native';

export default function FormNome() {
  const [nome, setNome] = useState(''); // estado inicial vazio

  return (
    <>
      <TextInput
        value={nome} // o que está na tela vem do estado
        onChangeText={setNome} // cada digitação atualiza o estado
        placeholder="Digite seu nome"
        style={{ borderWidth: 1, borderRadius: 4, padding: 8 }}
      />

      <Text style={{ marginTop: 8 }}>
        Você digitou: {nome || '—'}
      </Text>
    </>
  );
}
```

1. `useState('')` cria a variável de estado `nome` e a função `setNome`.
2. A prop `value={nome}` faz o `TextInput` **exibir** o valor guardado no estado.
3. A prop `onChangeText={setNome}` grava, a cada tecla, o novo texto no estado.

Esse ciclo garante sincronização perfeita entre interface e dados internos, permitindo validações em tempo real, formatação automática (por exemplo, maiúsculas) ou até mesmo envio de requisições conforme o usuário digita. 🧑‍💻


### **6.5 Tipando estados com TypeScript**

Quando o dado que chega do teclado **não é string**, precisamos converter o texto digitado para o tipo correto — neste exemplo abaixo, `number`.  Usando generics do `useState`, tipamos o estado como `number | null` e, a cada alteração, transformamos o texto em número com `Number(...)`. Veja o código abaixo:

```tsx
import { useState } from 'react';
import { TextInput, Text } from 'react-native';

export default function CampoIdade() {
  const [idade, setIdade] = useState<number | null>(null);

  return (
    <>
      <TextInput
        keyboardType="numeric" // exibe teclado numérico
        value={idade?.toString() ?? ''} // mostra idade ou string vazia
        onChangeText={(t) => setIdade(Number(t))} // converte para número
        placeholder="Idade"
        style={{ borderWidth: 1, borderRadius: 4, padding: 8 }}
      />

      <Text style={{ marginTop: 8 }}>
        {idade !== null ? `Você tem ${idade} anos` : 'Informe sua idade'}
      </Text>
    </>
  );
}
```

Nesse código:

* **`keyboardType="numeric"`** já força um teclado especializado, reduzindo erros.
* `idade?.toString() ?? ''` garante que o campo mostre uma string vazia quando o estado ainda é `null`.
* No `onChangeText`, **`Number(t)`** converte a entrada para número; caso o usuário apague tudo, o resultado vira `0` ou `NaN`, e você pode tratar isso com validação adicional caso necessário.

Em resumo: sempre converta explicitamente o texto o tipo correto — isso evita avisos do TypeScript e bugs de comparação mais adiante.

### **6.6 Validação básica e feedback ao usuário**

A lógica de validação costuma ser executada **no clique do botão** ou **à medida que o usuário digita**. 

Considere o componente abaixo, onde adotamos a primeira estratégia (validar ao clique do botão), disparando a checagem quando o usuário pressiona **Validar**.

```tsx
import { useState } from 'react';
import { View, Text, TextInput, StyleSheet } from 'react-native';
import { BotaoPrimario } from './components/BotaoPrimario'; // supondo que já exista

export function FormularioEmail() {
  /* ---------- 1) estado + lógica ---------- */
  const [email, setEmail] = useState('');
  const [erro,  setErro ] = useState('');

  function validarEmail() {
    if (!email.includes('@')) {
      setErro('E-mail inválido');
    } else {
      setErro('');
      alert('E-mail válido!');
    }
  }

  /* ---------- 2) interface que usa o estado ---------- */
  return (
    <View style={styles.container}>
      <TextInput
        value={email}
        onChangeText={setEmail}
        placeholder="Digite seu e-mail"
        keyboardType="email-address"
        style={styles.input}
      />

      {/* Mostra a mensagem só quando há erro */}
      {erro !== '' && <Text style={styles.mensagemErro}>{erro}</Text>}

      <BotaoPrimario titulo="Validar" onPress={validarEmail} />
    </View>
  );
}

/* ---------- estilos ---------- */
const styles = StyleSheet.create({
  container: { padding: 20 },
  input: {
    borderWidth: 1,
    borderColor: '#999',
    padding: 8,
    borderRadius: 4,
    marginBottom: 12,
  },
  mensagemErro: { color: 'red', marginBottom: 8 },
});
```

O que acontece na tela é o seguinte:

1. **O usuário digita**: cada alteração no `TextInput` invoca `setEmail`, mantendo o estado `email` sincronizado (input controlado).
2. **Aperta “Validar”**: `validarEmail` roda. Se não houver “@” na string, a função grava a mensagem *“E-mail inválido”* em `erro`; caso contrário, limpa o erro e mostra um `alert`.
3. **Renderização**: o bloco `{erro !== '' && …}` só aparece quando `erro` contém texto; assim, a mensagem em vermelho surge exatamente nos casos inválidos e some depois que o e-mail é considerado válido.

### **6.7 Organizando um formulário simples com tipagem**

Quando o formulário reúne vários campos, é prático guardar tudo em **um único estado-objeto**. Tipamos esse estado com uma interface (`Usuario`) para manter o TypeScript alinhado e, a cada mudança, espalhamos o objeto atual (`...form`) e sobrescrevemos apenas o campo alterado.

```tsx
// 1) contrato de tipagem
interface Usuario {
  nome: string;
  idade: number;
}

// 2) estado inicial do formulário
const [form, setForm] = useState<Usuario>({
  nome: '',
  idade: 0,
});
```

Para atualizar cada campo, no `onChangeText` de cada `TextInput`, usamos o **spread operator** para copiar o estado atual e ajustamos somente a propriedade que mudou:

```tsx
{/* campo nome */}
<TextInput
  value={form.nome}
  onChangeText={(texto) =>
    setForm((prev) => ({ ...prev, nome: texto }))
  }
/>

{/* campo idade */}
<TextInput
  value={String(form.idade)}
  keyboardType="numeric"
  onChangeText={(texto) =>
    setForm((prev) => ({ ...prev, idade: Number(texto) }))
  }
/>
```

* `setForm((prev) => …)` garante que pegaremos a versão mais recente do estado, evitando condições de corrida.
* `Number(texto)` converte a string digitada para número, mantendo a consistência do tipo `idade: number` definido em `Usuario`.

Dessa forma, cada `TextInput` controla apenas o seu pedaço do objeto sem perder as demais informações já preenchidas.

### **6.8 Exemplo completo – formulário de cadastro**

O componente abaixo reúne tudo o que vimos até agora: estados controlados, validação básica, tipagem e um botão reaproveitável. Ele exibe dois `TextInput`s (nome e idade), valida os dados e, se tudo estiver correto, devolve uma mensagem de sucesso.

```tsx
import { useState } from 'react';
import { View, Text, TextInput, StyleSheet } from 'react-native';
import { BotaoPrimario } from '../components/BotaoPrimario'; // supondo que já exista

export function CadastroUsuario() {
  const [nome,  setNome]   = useState(''); // estado controlado para o campo nome
  const [idade, setIdade]  = useState(''); // guardamos como string para facilitar o binding
  const [mensagem, setMsg] = useState(''); // feedback ao usuário

  /** Executa quando o botão é pressionado */
  function cadastrar() {
    // 1) validação de preenchimento
    if (!nome.trim() || !idade.trim()) {
      setMsg('Preencha todos os campos!');
      return;
    }

    // 2) validação de tipo numérico
    const idadeNum = Number(idade);
    if (Number.isNaN(idadeNum)) {
      setMsg('Idade inválida — use apenas números');
      return;
    }

    // 3) tudo certo ➜ feedback positivo
    setMsg(`Usuário ${nome} (${idadeNum}) cadastrado com sucesso!`);
  }

  return (
    <View style={styles.container}>
      <TextInput
        placeholder="Nome"
        value={nome}
        onChangeText={setNome}
        style={styles.input}
      />

      <TextInput
        placeholder="Idade"
        value={idade}
        onChangeText={setIdade}
        keyboardType="numeric"
        style={styles.input}
      />

      <BotaoPrimario titulo="Cadastrar" onPress={cadastrar} />

      {mensagem !== '' && (
        <Text style={styles.feedback}>{mensagem}</Text>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    padding: 20,
  },
  input: {
    borderWidth: 1,
    borderColor: '#999',
    borderRadius: 4,
    padding: 10,
    marginBottom: 12,
  },
  feedback: {
    marginTop: 10,
    fontWeight: '600',
  },
});
```

Na prática, o que acontece aqui é:

1. **Digitando** em cada `TextInput`, o valor entra nos respectivos estados (`nome` e `idade`) — inputs controlados.
2. **Pressionando** o botão, a função `cadastrar()`:
   * verifica se há campos vazios;
   * converte a idade de string para número e testa se é válida;
   * exibe uma mensagem de erro ou sucesso no mesmo componente.
3. O texto de feedback aparece logo abaixo do botão, atualizando-se em tempo real sempre que o usuário tenta cadastrar novamente.

### **6.9 Boas práticas**

Para que formulários sigam fluindo sem dores de cabeça, vale adotar algumas regras simples — elas poupam refatorações futuras e garantem uma experiência de uso consistente:

* **Mantenha cada campo como “input controlado”.**  Vincule o valor do `TextInput` a um estado criado com `useState`. Assim, qualquer digitação do usuário é refletida imediatamente na árvore de componentes, permitindo validações em tempo real e exibição de mensagens de erro no mesmo render.

* **Valide antes de processar ou enviar dados.**  Nunca presuma que o que veio do teclado está correto. Verifique campos obrigatórios, tipos (número, e-mail, CPF…) e regras de negócio. Se algo falhar, mostre feedback claro; se tudo passar, prossiga com a ação (salvar em backend, navegar, etc.).

* **Tipagem é sua amiga.**  Declare o formato dos estados e das props com TypeScript (`string`, `number`, tipos literais ou estruturas mais complexas). O compilador pega inconsistências na hora e evita bugs de conversão ou comparação em tempo de execução.

* **Extraia componentes reutilizáveis.**  Se você repetir o mesmo “bloco” (ex.: botão primário, campo de texto com rótulo, seletor de data), transforme-o em um componente isolado. Isso elimina duplicação de estilo e lógica, além de deixar o código mais legível.

* **Escolha o `keyboardType` correto.**  Ajuste o teclado ao contexto:
  * `email-address` para e-mails
  * `numeric` ou `number-pad` para números
  * `phone-pad` para telefones
    Essa mudança simples reduz erros de digitação e melhora a experiência do usuário.

Em síntese:

| Prática                            | Explicação                                                         |
| ---------------------------------- | ------------------------------------------------------------------ |
| Controlar os inputs via `useState` | Permite rastrear o que o usuário está digitando                    |
| Validar antes de processar         | Garante dados corretos antes de enviar                             |
| Tipar estados e entradas           | Evita erros de tipo em tempo de execução                           |
| Extrair componentes                | Reutiliza lógica e estilo com `TextInput`, `Button`, `Label`, etc. |
| Usar `keyboardType` apropriado     | Ex: `"email-address"`, `"numeric"` melhora a UX                    |

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

---

## 8. Mãos à obra!

Chegamos ao momento de consolidar todo o conhecimento adquirido nesta aula sobre a construção de interfaces visuais com React Native! Depois de explorarmos os fundamentos da UI, desde a estrutura com `View` e `Text`, passando pela estilização com `StyleSheet` e o layout com Flexbox, até a criação de componentes reutilizáveis com `props` e a gestão da interatividade com inputs e eventos, é hora de aplicar esses conceitos em um projeto prático.

Nesta seção, colocaremos a mão na massa para desenvolver uma versão mobile (um pouquinho diferente) da nossa "Pokedex", que já exploramos nas aulas anteriores. Este projeto prático não só revisitará os conceitos da Aula 03 sobre React, como também integrará de forma coesa os componentes visuais, a busca de dados de uma API externa, a tipagem com TypeScript e a organização de um projeto mobile que vimos ao longo desta aula.

Nessa versão da PokeDex a ideia é mostrar uma lista de pokemóns como cards e fazer a filtragem através de um input de texto. Vamos lá!

### 📁 Estrutura do Projeto

Vamos estruturar nosso projeto da seguinte forma:

```
PokedexApp/
├── App.tsx
├── assets/
├── components/
│   └── PokemonCard.tsx
├── screens/
│   └── PokedexScreen.tsx
├── services/
│   └── api.ts
├── types/
│   └── Pokemon.ts
├── utils/
│   └── format.ts
└── tsconfig.json
```

Esta organização promove a modularidade e facilita a manutenção e escalabilidade do projeto e esta é a estrutura de pastas sugerida para o seu aplicativo Pokedex.

Ela organiza o código de forma lógica:

* **`App.tsx`**: Ponto de entrada principal do aplicativo.
* **`assets/`**: Para armazenar imagens estáticas, fontes, etc. (Atualmente vazia, mas é uma boa prática tê-la).
* **`components/`**: Contém componentes reutilizáveis da interface do usuário (UI).
    * **`PokemonCard.tsx`**: Componente para exibir as informações de um único Pokémon.
* **`screens/`**: Contém os componentes que representam as diferentes telas do aplicativo.
    * **`PokedexScreen.tsx`**: A tela principal que exibirá a lista de Pokémons.
* **`services/`**: Módulos responsáveis pela comunicação com APIs externas ou serviços.
    * **`api.ts`**: Contém a lógica para buscar dados da PokeAPI.
* **`types/`**: Definições de tipos TypeScript para garantir a consistência dos dados.
    * **`Pokemon.ts`**: Define as interfaces para os dados dos Pokémons.
* **`utils/`**: Funções utilitárias que podem ser usadas em várias partes do aplicativo.
    * **`format.ts`**: Exemplo de um utilitário para formatação de strings.
* **`tsconfig.json`**: Arquivo de configuração do TypeScript para o projeto.

### ✅ Como criar o projeto

Para iniciar, você utilizaria o Expo CLI, uma ferramenta que simplifica o desenvolvimento de aplicativos React Native.

Primeiro, crie o projeto com um template TypeScript:

```
npx create-expo-app PokedexApp -t expo-template-blank-typescript
```

Este comando gera a estrutura básica do projeto já configurada para TypeScript.

Em seguida, navegue para o diretório do projeto:

```
cd PokedexApp
```

Depois, instale as dependências adicionais que serão úteis:

```
npm install axios react-native-safe-area-context
```

* **`axios`**: É um cliente HTTP popular baseado em Promises, usado aqui para fazer requisições à PokeAPI.
* **`react-native-safe-area-context`**: Ajuda a garantir que o conteúdo da interface do usuário não seja sobreposto por elementos do sistema operacional (como a barra de status ou o "notch" em alguns dispositivos). 

Para executar o projeto na versão web, também temos que instalar:

```
npx expo install react-dom react-native-web @expo/metro-runtime
```

E para rodar:

```
npx expo start
```

Vamos agora ao código-fonte de cada componente! 👽

### 📁 `App.tsx`
```tsx
import React from 'react';
import { SafeAreaProvider } from 'react-native-safe-area-context';
import { PokedexScreen } from './screens/PokedexScreen';

export default function App() {
  return (
    <SafeAreaProvider>
      <PokedexScreen />
    </SafeAreaProvider>
  );
}
```

Este é o componente raiz do seu aplicativo.

Ele importa `React` para a criação de componentes e o `SafeAreaProvider` de `react-native-safe-area-context`. Este último é envolvido em torno do conteúdo principal do aplicativo, garantindo que os componentes filhos possam usar o `SafeAreaView` ou hooks relacionados para ajustar o layout e evitar áreas de entalhe do dispositivo ou barras de sistema. A `PokedexScreen`, importada da pasta `screens`, é a tela inicial que será renderizada. A função `App` retorna a `PokedexScreen` encapsulada pelo `SafeAreaProvider`. 

Essencialmente, este arquivo configura o ponto de entrada básico e a gestão da área segura para o aplicativo.

### 🟦 `screens/PokedexScreen.tsx`
```tsx
import React, { useEffect, useState } from 'react';
import { View, Text, FlatList, TextInput, StyleSheet } from 'react-native';
import { getPokemons, getPokemonDetails } from '../services/api';
import { Pokemon } from '../types/Pokemon';
import { PokemonCard } from '../components/PokemonCard';

export const PokedexScreen = () => {
  const [pokemons, setPokemons] = useState<Pokemon[]>([]);
  const [search, setSearch] = useState('');

  useEffect(() => {
    const fetchData = async () => {
      const list = await getPokemons(30); // primeiros 30 pokemons
      const details = await Promise.all(list.map(p => getPokemonDetails(p.url)));
      setPokemons(details);
    };
    fetchData();
  }, []);

  const filtered = pokemons.filter(p => p.name.includes(search.toLowerCase()));

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Pokédex</Text>
      <TextInput
        placeholder="Buscar pokémon..."
        style={styles.input}
        onChangeText={setSearch}
      />
      <FlatList
        data={filtered}
        keyExtractor={item => item.id.toString()}
        numColumns={2}
        renderItem={({ item }) => <PokemonCard pokemon={item} />}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, paddingTop: 60, paddingHorizontal: 16 },
  title: { fontSize: 32, fontWeight: 'bold', marginBottom: 12 },
  input: {
    backgroundColor: '#f1f1f1',
    padding: 10,
    borderRadius: 8,
    marginBottom: 20,
  },
});
```

Este componente representa a tela principal da Pokédex! 

Ele importa as dependências necessárias, incluindo `React` com os hooks `useEffect` e `useState`, componentes do React Native como `View`, `Text`, `FlatList` e `TextInput`, além das funções de serviço `getPokemons` e `getPokemonDetails`, o tipo `Pokemon` e o componente `PokemonCard`. O estado do componente é gerenciado por `pokemons`, um array para os dados detalhados dos Pokémons, e `search`, uma string para o termo de busca. 

O hook `useEffect` é responsável por buscar os dados quando o componente é montado: ele chama `getPokemons(30)` para obter uma lista inicial e, em seguida, usa `Promise.all` com `getPokemonDetails` para buscar os detalhes de cada Pokémon, atualizando o estado `pokemons`. A busca é implementada filtrando o array `pokemons` com base no estado `search`, resultando no array `filtered` que considera a busca case-insensitive. Na renderização, um `View` principal contém o título "Pokédex", um `TextInput` para a busca (que atualiza o estado `search` via `onChangeText`), e uma `FlatList`.

A `FlatList` exibe os Pokémons filtrados em duas colunas (`numColumns={2}`), utilizando o `PokemonCard` para renderizar cada item. As chaves dos itens são extraídas dos IDs dos Pokémons. Os estilos visuais são definidos usando `StyleSheet.create`, incluindo um `paddingTop: 60` no contêiner principal, que pode ser uma forma de evitar a barra de status, embora o uso de `SafeAreaContext` seja mais robusto.

### 🟨 `components/PokemonCard.tsx`
```tsx
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';
import { Pokemon } from '../types/Pokemon';

interface Props {
  pokemon: Pokemon;
}

export const PokemonCard = ({ pokemon }: Props) => {
  return (
    <View style={styles.card}>
      <Image source={{ uri: pokemon.image }} style={styles.image} />
      <Text style={styles.name}>{pokemon.name}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    flex: 1,
    backgroundColor: '#e0e0e0',
    margin: 8,
    padding: 12,
    borderRadius: 12,
    alignItems: 'center',
  },
  image: { width: 80, height: 80 },
  name: { marginTop: 8, fontWeight: 'bold' },
});
```

Este é um componente de UI reutilizável, projetado para exibir as informações básicas de um único Pokémon em um formato de "cartão".

Ele importa `React`, componentes visuais do React Native como `View`, `Text`, `Image` e `StyleSheet`, além do tipo `Pokemon` para definir as props esperadas. A interface `Props` especifica que o componente `PokemonCard` deve receber uma propriedade `pokemon` do tipo `Pokemon`.

Só para relembrar o que vimos na aula anterior: Em TypeScript, uma **interface** descreve o formato obrigatório de um objeto e, neste componente, ela garante em tempo de compilação que `PokemonCard` receba exatamente uma prop `pokemon` conforme o contrato definido em `Pokemon`👆🤓

O componente funcional `PokemonCard` recebe essa prop `pokemon` desestruturada e retorna uma `View` principal estilizada como um cartão. Dentro deste cartão, um componente `Image` exibe a imagem do Pokémon, obtendo a URL de `pokemon.image`, e um componente `Text` mostra o nome do Pokémon (`pokemon.name`). Os estilos, definidos em `StyleSheet.create`, incluem `card` para o contêiner (com `flex: 1` para distribuição igual em layouts de grade, cor de fundo, margens, preenchimento, bordas arredondadas e alinhamento centralizado), `image` para definir as dimensões da imagem, e `name` para estilizar o texto do nome. O foco deste componente é a apresentação visual concisa de um Pokémon.

### 🟧 `services/api.ts`
```tsx
import axios from 'axios';
import { Pokemon, PokemonListItem } from '../types/Pokemon';

const API_BASE = 'https://pokeapi.co/api/v2';

export async function getPokemons(limit: number): Promise<PokemonListItem[]> {
  const res = await axios.get(`${API_BASE}/pokemon?limit=${limit}`);
  return res.data.results;
}

export async function getPokemonDetails(url: string): Promise<Pokemon> {
  const res = await axios.get(url);
  return {
    id: res.data.id,
    name: res.data.name,
    image: res.data.sprites.front_default,
    types: res.data.types.map((t: any) => t.type.name),
  };
}
```

Este módulo encapsula toda a lógica de comunicação com a PokeAPI. 

Ele importa `axios` para realizar requisições HTTP e os tipos `Pokemon` e `PokemonListItem` do arquivo `types/Pokemon.ts`. Uma constante `API_BASE` armazena a URL base da PokeAPI para centralizar essa informação. A função assíncrona `getPokemons` aceita um `limit` numérico e retorna uma `Promise` que resolve para um array de `PokemonListItem`.

Ela faz uma requisição GET ao endpoint `/pokemon` da API, utilizando o `limit`, e retorna `res.data.results`, que contém a lista de Pokémons com nome e URL para detalhes. A outra função assíncrona, `getPokemonDetails`, aceita uma `url` específica de um Pokémon e retorna uma `Promise` que resolve para um objeto `Pokemon` com detalhes completos. Ela faz uma requisição GET para a URL fornecida e mapeia a resposta da API para a estrutura `Pokemon`, extraindo `id`, `name`, a imagem (`res.data.sprites.front_default`) e os tipos (mapeando o array `res.data.types` para obter apenas os nomes dos tipos, usando `(t: any)` que poderia ser mais tipado para segurança).

Este arquivo abstrai as chamadas de API, mantendo os componentes de tela mais limpos.

### 🟪 `types/Pokemon.ts`
```tsx
export interface PokemonListItem {
  name: string;
  url: string;
}

export interface Pokemon {
  id: number;
  name: string;
  image: string;
  types: string[];
}
```

Este arquivo define as estruturas de dados, através de interfaces TypeScript, usadas no aplicativo para os Pokémons.

A interface `PokemonListItem` descreve a estrutura de um item na lista inicial de Pokémons retornada pela API, contendo `name` (o nome do Pokémon) e `url` (a URL para seus detalhes completos). A interface `Pokemon` define a estrutura de um objeto Pokémon com seus detalhes, incluindo `id` (identificador numérico), `name` (nome), `image` (URL da imagem) e `types` (um array de strings representando os tipos do Pokémon, como `["grass", "poison"]`). 

O uso dessas interfaces TypeScript ajuda a garantir a consistência dos dados em todo o aplicativo, auxiliando na detecção de erros durante o desenvolvimento e melhorando a legibilidade do código.

### 🟪 (Opcional) `utils/format.ts`
```tsx
export const capitalize = (s: string) => s.charAt(0).toUpperCase() + s.slice(1);
```

Este é um arquivo opcional destinado a conter funções utilitárias genéricas. A função `capitalize`, exportada aqui, aceita uma string `s` como argumento. Ela retorna uma nova string onde o primeiro caractere é convertido para maiúscula (`s.charAt(0).toUpperCase()`) e concatenado com o restante da string original (`s.slice(1)`). 

Embora não esteja diretamente em uso nos componentes fornecidos, serve como um bom exemplo de uma função utilitária que poderia ser empregada, por exemplo, para formatar os nomes dos Pokémons para exibição. 😊

### Resumindo tudo!

Nesse exercício vimos os conceitos principais sendo aplicados. Além disso, já conseguimos fazer um app bem completinho, com uso de API externa, interação com usuário e estilização!

Para consolidar o conhecimento, vamos fazer alguns exercícios! (sim, sim... eu sei, mas fazer o quê, né?)

---

## 9. Exercícios

Concentre-se nestes 5 desafios para elevar o nível da sua Pokédex, aplicando conceitos importantes de robustez, experiência do usuário e funcionalidade.

**Exercício 1: Robustez na Busca de Dados e Feedback ao Usuário**

Atualmente, a busca inicial de dados não informa ao usuário o que está acontecendo e pode falhar silenciosamente.
* **Sua tarefa:**
    1.  **Indicador de Carregamento:** Na `PokedexScreen`, adicione um estado `isLoading`. Ele deve ser `true` enquanto os dados iniciais são buscados e `false` ao final. Enquanto `isLoading` for `true`, exiba um `ActivityIndicator` ou uma mensagem "Carregando Pokémons...".
    2.  **Tratamento de Erros:** Modifique as funções em `services/api.ts` para usar `try...catch`. Se a busca inicial em `PokedexScreen` falhar, atualize um novo estado de erro e exiba uma mensagem amigável (ex: "Falha ao carregar Pokémons. Verifique sua conexão.").

**Exercício 2: Melhorando a Experiência da Lista e da Busca**

Uma lista vazia ou uma busca sem resultados pode deixar o usuário confuso.
* **Sua tarefa:**
    1.  Na `PokedexScreen`, utilize a propriedade `ListEmptyComponent` da `FlatList`.
    2.  Este componente deve exibir mensagens contextuais:
        * Se a busca (`search`) estiver preenchida e não houver resultados: "Nenhum Pokémon encontrado para '{termo_buscado}'".
        * Se a lista inicial `pokemons` estiver vazia após a tentativa de carregamento (e não estiver mais carregando): "Nenhum Pokémon para exibir no momento."

**Exercício 3: Expandindo a Descoberta com "Carregamento Infinito"**

Limitar a Pokédex a apenas 30 Pokémons restringe a exploração. Vamos permitir que o usuário carregue mais Pokémons conforme rola a tela.
* **Sua tarefa:**
    1.  Adicione um estado para o `offset` (ou página) na `PokedexScreen` e modifique `getPokemons` em `services/api.ts` para aceitar e usar esse `offset`.
    2.  Crie uma função `loadMorePokemons` em `PokedexScreen` que busque a próxima leva de Pokémons e os adicione à lista existente.
    3.  Use a propriedade `onEndReached` da `FlatList` para chamar `loadMorePokemons`.
    4.  **Desafio extra:** Adicione um indicador de carregamento no rodapé da lista (usando `ListFooterComponent`) enquanto mais Pokémons são buscados e evite chamadas múltiplas se uma já estiver em andamento.

**Exercício 4: Refinamento Visual e de Layout**

Pequenos ajustes podem fazer uma grande diferença na apresentação e usabilidade.
* **Sua tarefa:**
    1.  **Capitalização:** Importe e utilize a função `capitalize` de `utils/format.ts` no `PokemonCard.tsx` para exibir o nome de cada Pokémon com a primeira letra maiúscula.
    2.  **Área Segura Dinâmica:** Na `PokedexScreen.tsx`, substitua o `paddingTop: 60` fixo. Utilize o hook `useSafeAreaInsets` de `react-native-safe-area-context` para aplicar um `paddingTop` dinâmico, garantindo que o layout se adapte corretamente a diferentes dispositivos.

**Desafio 1: Aprofundando com uma Tela de Detalhes do Pokémon**

Ver apenas o card é legal, mas que tal uma tela dedicada para cada Pokémon?
* **Sua tarefa:**
    1.  Instale e configure uma biblioteca de navegação (como React Navigation: `@react-navigation/native` e `@react-navigation/stack`).
    2.  Crie uma nova tela `PokemonDetailScreen.tsx`.
    3.  Faça com que, ao pressionar um `PokemonCard` (transforme-o em um `TouchableOpacity`), o aplicativo navegue para `PokemonDetailScreen`, passando o ID ou o objeto do Pokémon selecionado como parâmetro.
    4.  Na `PokemonDetailScreen`, exiba o nome, a imagem em tamanho maior e os tipos do Pokémon recebido.
    5.  **Desafio extra:** Na `PokemonDetailScreen`, busque e exiba informações adicionais como altura, peso e uma breve descrição (a PokeAPI oferece um endpoint para espécies que contém descrições 😉).

# **Bom trabalho! 🔨**
