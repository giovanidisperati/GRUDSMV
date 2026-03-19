---
layout: aula
title: "6. O hook useEffect"
parent: Aula 03 - Introdução ao React (Componentes, Props e Hooks)
nav_order: 6
---

## **6. useEffect: efeitos colaterais e ciclo de vida dos componentes**

Até agora, aprendemos a criar componentes e gerenciar seu estado interno com `useState`. Mas e quando precisamos **interagir com o mundo exterior** ao nosso componente? É aí que entra o **hook `useEffect`**.

### 🔄 **6.1 O que são "efeitos colaterais" no React?**

Em programação funcional (princípio por trás do React), os componentes devem ser funções **puras**: recebem props, usam estado e retornam elementos. Vimos um pouquinho disso na aula anterior, lembra? Porém, aplicações reais, em muitos casos, fogem desse fluxo esperado.

Nesse caso, podemos considerar que **efeitos colaterais** são ações que acontecem **fora do fluxo normal de renderização**, como:

* Buscar dados de uma API
* Modificar o título da página
* Configurar assinaturas de eventos
* Interagir com o localStorage
* Iniciar e limpar temporizadores

O hook `useEffect` nos permite executar esses efeitos de forma **controlada e previsível**.

### 🧪 **6.2 Sintaxe básica do useEffect**

O `useEffect` é uma função especial do React que permite rodar um trecho de código quando algo acontece no ciclo de vida do componente — como quando ele aparece na tela ou quando alguma informação muda. Ele recebe dois parâmetros:

1. Uma função com o código que queremos executar

2. Um array com as variáveis que, ao mudarem, disparam o efeito

```tsx
import React, { useEffect } from "react";

function MeuComponente() {
  useEffect(() => {
    console.log("Olá! Estou na tela!");

    return () => {
      console.log("Tchau! Estou saindo da tela.");
    };
  }, []);

  return <div>Olá mundo</div>;
}
```

* A primeira parte `console.log("O componente apareceu na tela!")` vai rodar **assim que o componente aparece**.
* A segunda parte (a que começa com `return`) vai rodar **quando o componente sair da tela**.
* Os `[]` no final significam: **"só execute isso uma vez"**, quando o componente aparece.

Ou seja, neste exemplo, o efeito será executado **apenas uma vez** quando o componente for montado, e a função de limpeza será chamada quando o componente for removido da tela. Em suma:

* `useEffect` é usado para **fazer algo quando o componente aparece ou muda**.
* O `[]` indica que o efeito só deve acontecer uma vez. Se colocássemos variáveis ali dentro, o efeito rodaria sempre que elas mudassem.


### 🕒 **6.3 Controlando quando o efeito é executado**

O `useEffect` é como uma **função que fica esperando o momento certo para agir**. Mas... **quando é esse momento?**

A resposta está em um detalhe importante: o **array de dependências** (`[]`) que colocamos logo depois da função!

### 📌 Existem três comportamentos diferentes, dependendo desse array:

#### ✅ 1. Quando o array está vazio (`[]`):

```tsx
useEffect(() => {
  console.log("O componente foi exibido na tela!");
}, []);
```

* Esse efeito **só roda uma vez**, assim que o componente **aparece pela primeira vez**.
* É como dizer: “Faça isso **só no começo**.”
* Muito usado para **carregar dados**, **iniciar timers**, ou **conectar APIs**.

#### 🔁 2. Quando você coloca uma variável dentro do array (`[contador]`):

```tsx
useEffect(() => {
  console.log(`O contador mudou para: ${contador}`);
}, [contador]);
```

* O efeito vai rodar **no começo** e depois **toda vez que `contador` mudar**.
* É como dizer: “Faça isso **sempre que essa informação mudar**.”
* Ideal para **reagir a atualizações** de dados específicos, como uma busca que depende de um filtro.

#### ⚠️ 3. Quando você **não coloca o array**:

```tsx
useEffect(() => {
  console.log("O componente foi renderizado!");
});
```

* O efeito vai rodar **toda vez que o componente for atualizado**, até por pequenos motivos.
* Isso pode causar **efeitos repetidos e desnecessários** se você não tiver cuidado.
* Só use esse formato quando **você quiser que o efeito aconteça o tempo todo**.

### 📈 Por que isso importa?

Escolher corretamente **quais variáveis vão no array de dependências** evita:

* **Rodar efeitos sem necessidade**
* **Repetir chamadas de API**
* **Deixar a aplicação lenta**
* Ou pior: **entrar em loops infinitos de renderização**

Entendido? Entendido, então vamos continuar! 🤠

### 📦 **6.4 Buscando dados de APIs**

Um dos usos mais comuns do `useEffect` é buscar dados de APIs quando um componente é montado:

```tsx
import React, { useState, useEffect } from "react";

function PokemonInfo() {
  const [pokemon, setPokemon] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Indicamos que estamos carregando
    setLoading(true);
    
    // Fazemos a requisição à API
    fetch("https://pokeapi.co/api/v2/pokemon/pikachu")
      .then(response => {
        // Verificamos se a resposta está ok
        if (!response.ok) {
          throw new Error("Falha ao buscar dados do Pokémon");
        }
        return response.json();
      })
      .then(data => {
        // Armazenamos os dados no estado
        setPokemon(data);
        setLoading(false);
      })
      .catch(err => {
        // Tratamos possíveis erros
        setError(err.message);
        setLoading(false);
      });
  }, []); // Array vazio = executa apenas uma vez na montagem

  // Renderização condicional baseada no estado
  return (
    <div className="pokemon-card" style={{ 
      padding: "20px", 
      border: "1px solid #ddd",
      borderRadius: "8px",
      maxWidth: "400px",
      margin: "0 auto"
    }}>
      <h2>Informações do Pokémon</h2>
      
      {loading ? (
        <div className="loading" style={{ 
          textAlign: "center", 
          padding: "20px" 
        }}>
          <div className="spinner" style={{ 
            display: "inline-block",
            width: "30px",
            height: "30px",
            border: "3px solid #f3f3f3",
            borderTop: "3px solid #3498db",
            borderRadius: "50%",
            animation: "spin 1s linear infinite"
          }}></div>
          <style>{`
            @keyframes spin {
              0% { transform: rotate(0deg); }
              100% { transform: rotate(360deg); }
            }
          `}</style>
          <p>Carregando...</p>
        </div>
      ) : error ? (
        <div className="error" style={{ 
          color: "red", 
          padding: "10px",
          backgroundColor: "#ffebee",
          borderRadius: "4px" 
        }}>
          <p>Erro: {error}</p>
        </div>
      ) : pokemon && (
        <div className="pokemon-details">
          <h3 style={{ textTransform: "capitalize" }}>{pokemon.name}</h3>
          {pokemon.sprites?.front_default && (
            <img 
              src={pokemon.sprites.front_default} 
              alt={pokemon.name}
              style={{ display: "block", margin: "0 auto" }}
            />
          )}
          <div style={{ marginTop: "10px" }}>
            <p><strong>Altura:</strong> {pokemon.height / 10}m</p>
            <p><strong>Peso:</strong> {pokemon.weight / 10}kg</p>
            <p><strong>Tipos:</strong> {
              pokemon.types?.map(t => t.type.name).join(", ")
            }</p>
          </div>
        </div>
      )}
    </div>
  );
}
```

No exemplo acima, fazemos uma requisição para buscar os dados do Pikachu. Para isso, usamos três variáveis de estado:

* `pokemon`: guarda os dados recebidos da API
* `loading`: indica se a requisição ainda está em andamento
* `error`: armazena uma mensagem de erro, se algo der errado

Essas três variáveis controlam o que aparece na tela, dependendo do estado da requisição.

Já no `useEffect`, fazemos o seguinte:

1. Colocamos `loading` como `true` para indicar que estamos buscando dados.
2. Usamos `fetch()` para acessar a API.
3. Se a resposta estiver ok, transformamos ela em JSON e salvamos no estado.
4. Se der erro (por exemplo, se a API estiver fora do ar), capturamos esse erro e guardamos a mensagem em `error`.
5. Em ambos os casos, desativamos o `loading`.

Importante: como usamos um array de dependências vazio (`[]`), isso significa que o efeito **só será executado uma vez**, quando o componente for exibido pela primeira vez. ☝️🤓

Já na tela, a interface será **renderizada com base no estado atual**:

* Se `loading` for verdadeiro, mostramos uma animação de carregamento.
* Se `error` tiver uma mensagem, mostramos a mensagem de erro em destaque.
* Se tudo der certo, mostramos os dados do Pokémon, como nome, imagem, altura, peso e tipos.

Esse padrão é muito usado no desenvolvimento de aplicações reais com React, porque nos permite lidar com diferentes situações de forma clara e controlada: **carregando**, **erro** ou **dados disponíveis**.

Este exemplo demonstra um padrão comum:
1. Definimos estados para os dados, carregamento e erros
2. Usamos `useEffect` para buscar dados quando o componente monta
3. Atualizamos os estados conforme a requisição progride
4. Renderizamos diferentes UIs baseadas no estado atual

Legal, né?! Agora vemos claramente como as coisas vão se encaixando. 😊 

### ⚠️ **6.5 useEffect e async/await**

Outro ponto importante a destacar é que o corpo da função passada para `useEffect` **não pode ser assíncrono diretamente**. Isso ocorre porque o React espera que a função retorne uma função de limpeza ou nada, não uma Promise.

```tsx
// ❌ ERRADO: useEffect não aceita funções async diretamente
useEffect(async () => {
  const response = await fetch("https://api.exemplo.com/dados");
  const data = await response.json();
  setDados(data);
}, []);

// ✅ CORRETO: Declare uma função async dentro do efeito
useEffect(() => {
  async function fetchData() {
    try {
      const response = await fetch("https://api.exemplo.com/dados");
      const data = await response.json();
      setDados(data);
    } catch (error) {
      setError(error.message);
    } finally {
      setLoading(false);
    }
  }
  
  fetchData();
}, []);
```

Este padrão permite usar a sintaxe mais limpa do `async/await` enquanto respeita as regras do `useEffect`.

### 🧹 **6.6 Limpando efeitos (cleanup)**

Alguns efeitos precisam ser "limpos" quando o componente é desmontado ou antes de serem executados novamente. Isso evita comportamentos indesejados, como múltiplos temporizadores ativos, vazamento de memória ou escutas de eventos duplicadas.

Algumas situações típicas em que usamos essa limpeza incluem:

* Cancelar requisições em andamento
* Remover event listeners (ouvinte de eventos)
* Limpar `setInterval` ou `setTimeout`
* Cancelar assinaturas de WebSocket ou serviços externos

Para isso, usamos um **retorno dentro do `useEffect`**. Esse retorno é **uma função** que será automaticamente chamada pelo React **no momento certo**:

* Antes de o efeito ser executado novamente (quando alguma dependência muda)
* Ou quando o componente for removido da tela (desmontado)

Sintaticamente, você pode identificar a função de limpeza por este padrão:

```tsx
useEffect(() => {
  // Efeito principal
  ...

  // Função de limpeza (return dentro do useEffect)
  return () => {
    // Código que desfaz o efeito
  };
}, [/* dependências */]);
```

O `return () => { ... }` **não é um return do componente**, mas sim da função passada ao `useEffect`. Ele diz ao React: “Quando for a hora de limpar, execute isso aqui”.

Vejamos um exemplo abaixo:

```tsx
function Cronometro() {
  const [segundos, setSegundos] = useState(0);
  
  useEffect(() => {
    console.log("⏱️ Iniciando cronômetro");
    
    // Configuramos o temporizador
    const intervalo = setInterval(() => {
      setSegundos(s => s + 1);
    }, 1000);
    
    // Retornamos uma função de limpeza
    return () => {
      console.log("⏱️ Limpando cronômetro");
      clearInterval(intervalo);
    };
  }, []);
  
  return (
    <div style={{ 
      textAlign: "center", 
      padding: "20px",
      border: "1px solid #ddd",
      borderRadius: "8px",
      maxWidth: "300px",
      margin: "20px auto"
    }}>
      <h2>Cronômetro</h2>
      <p style={{ fontSize: "2rem", fontWeight: "bold" }}>
        {segundos} segundos
      </p>
    </div>
  );
}
```

Nesse caso a função de limpeza é chamada:
1. Antes de executar o efeito novamente (se as dependências mudaram)
2. Quando o componente é desmontado (removido da UI)

### 🔍 **6.7 Exemplo prático: Pesquisa com debounce**

Vamos criar um componente de pesquisa que busca Pokémon à medida que o usuário digita, mas com um pequeno atraso para evitar muitas requisições:

```tsx
import React, { useState, useEffect } from "react";

function PokemonSearch() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  
  // Este efeito será executado quando 'query' mudar
  useEffect(() => {
    // Não pesquisamos se a query estiver vazia
    if (!query.trim()) {
      setResults([]);
      return;
    }
    
    // Definimos loading como true
    setLoading(true);
    
    // Criamos um timer para esperar que o usuário pare de digitar
    const timer = setTimeout(() => {
      fetch(`https://pokeapi.co/api/v2/pokemon?limit=100`)
        .then(res => res.json())
        .then(data => {
          // Filtramos os resultados que contêm a query
          const filteredResults = data.results.filter(
            pokemon => pokemon.name.includes(query.toLowerCase())
          ).slice(0, 5); // Limitamos a 5 resultados
          
          setResults(filteredResults);
          setLoading(false);
        })
        .catch(err => {
          console.error("Erro na busca:", err);
          setLoading(false);
        });
    }, 500); // 500ms de debounce
    
    // Limpamos o timer se a query mudar antes do tempo
    return () => clearTimeout(timer);
  }, [query]);
  
  return (
    <div style={{ maxWidth: "400px", margin: "0 auto", padding: "20px" }}>
      <h2>Buscar Pokémon</h2>
      
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Digite o nome do Pokémon..."
        style={{
          width: "100%",
          padding: "10px",
          fontSize: "16px",
          borderRadius: "4px",
          border: "1px solid #ddd",
          marginBottom: "20px"
        }}
      />
      
      {loading && <p>Buscando...</p>}
      
      {results.length > 0 ? (
        <ul style={{ 
          listStyle: "none", 
          padding: 0,
          border: "1px solid #eee",
          borderRadius: "4px" 
        }}>
          {results.map(pokemon => (
            <li 
              key={pokemon.name}
              style={{
                padding: "10px 15px",
                borderBottom: "1px solid #eee",
                textTransform: "capitalize"
              }}
            >
              {pokemon.name}
            </li>
          ))}
        </ul>
      ) : query && !loading && (
        <p>Nenhum Pokémon encontrado com "{query}"</p>
      )}
    </div>
  );
}
```

Neste código acima, criamos um pequeno componente de busca que consulta a PokéAPI conforme o usuário digita o nome de um Pokémon. No entanto, para **evitar que a API seja chamada a cada letra digitada**, usamos uma técnica chamada **debounce**. Esse componente:

* Exibe um campo de texto para o usuário digitar.
* Espera **meio segundo (500ms)** após a última tecla antes de fazer a requisição.
* Filtra os resultados localmente, mostrando até 5 Pokémon que contenham o texto digitado.
* Cancela a busca anterior se o usuário digitar algo novo antes do tempo acabar.
* Mostra mensagens de carregamento ou de erro quando apropriado.

Para isso usamos o `useEffect`, que executa uma ação toda vez que uma variável (ou conjunto de variáveis) muda. Neste exemplo, usamos `useEffect` para "ouvir" mudanças na variável `query`, que representa o texto digitado pelo usuário.

Quando `query` muda:

1. **Um `setTimeout` de 500ms é iniciado**. Ele aguarda meio segundo antes de realizar a requisição.
2. Se o usuário digitar novamente **antes do tempo acabar**, o `useEffect` é executado de novo, **cancelando o timeout anterior com `clearTimeout`**.
3. Isso evita várias requisições desnecessárias e melhora a performance — comportamento conhecido como **debounce manual**. 👽

Para entender isso melhor, imagine que o usuário começa a digitar "pikachu". A cada letra (`p`, `pi`, `pik`...), o React **reinicia o timer de 500ms**. Somente quando o usuário **parar de digitar por pelo menos meio segundo**, a requisição é enviada.

Se a busca for bem-sucedida, os resultados são filtrados localmente e armazenados no estado `results`. Se houver erro, é tratado no `catch`. O campo `loading` controla o que deve ser exibido na tela: uma mensagem "Buscando..." ou os resultados, por exemplo.

Esse código mostra vários conceitos importantes:

* **Estado local com `useState`**: Armazena o texto da busca (`query`), os resultados (`results`) e se a busca está em andamento (`loading`).
* **Renderização condicional**: O componente exibe mensagens ou listas **com base nos valores de estado**, como `loading`, `results.length` e `query`.
* **Limpeza de efeito (`return () => { ... }`)**: Garante que o efeito anterior seja cancelado antes que o novo comece, evitando conflitos ou buscas duplicadas.

### 🧠 **6.8 Regras importantes do useEffect**

Para usar `useEffect` corretamente, lembre-se destas regras:

1. **Sempre inclua todas as dependências usadas dentro do efeito**
   ```tsx
   // Se você usa 'userId' dentro do efeito, inclua-o nas dependências
   useEffect(() => {
     fetchUserData(userId);
   }, [userId]);
   ```

2. **Evite dependências que mudam frequentemente** para prevenir loops infinitos

3. **Use a forma funcional do setState** quando atualizar estado baseado em valor anterior
   ```tsx
   useEffect(() => {
     const timer = setInterval(() => {
       setCount(c => c + 1); // Forma funcional é mais segura
     }, 1000);
     return () => clearInterval(timer);
   }, []);
   ```

4. **Separe efeitos com propósitos diferentes** em chamadas distintas de `useEffect`
   ```tsx
   // Um efeito para buscar dados do usuário
   useEffect(() => {
     fetchUserData(userId);
   }, [userId]);
   
   // Outro efeito para atualizar o título da página
   useEffect(() => {
     document.title = `Perfil de ${userName}`;
   }, [userName]);
   ```

### ✅ **6.9 Conclusão da Seção**

O `useEffect` é um hook fundamental que nos permite sincronizar nossos componentes React com sistemas externos, como APIs, eventos do navegador e temporizadores.

Pontos-chave para lembrar:

* Use `useEffect` para executar código que não está diretamente relacionado à renderização
* O array de dependências controla quando o efeito é executado
* Retorne uma função de limpeza quando necessário para evitar vazamentos de memória
* Separe efeitos com propósitos diferentes
* Não use `async` diretamente na função passada para `useEffect`

Com `useState` e `useEffect`, você já tem as ferramentas básicas para criar componentes interativos e dinâmicos que se comunicam com o mundo exterior. Entendendo isso, entendemos a base do React. Estamos prontos, agora, para finalmente entrar nos conceitos do React Native na próxima aula!

Antes disso, na próxima seção, vamos aplicar esses conhecimentos em um projeto prático, construindo uma aplicação completa que integra tudo o que aprendemos até agora.