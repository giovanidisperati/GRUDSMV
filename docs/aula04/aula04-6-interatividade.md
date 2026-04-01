---
layout: aula
title: "6. Interagindo com o APP!"
parent:  Aula 04 - Começando o desenvolvimento mobile com React Native!
nav_order: 6
---

## **6. Interatividade com Inputs e Eventos**

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