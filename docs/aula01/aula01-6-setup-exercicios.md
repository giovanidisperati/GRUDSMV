---
layout: aula
title: "7. Setup, Conclusão e Exercícios"
parent: Aula 01 - Panorama, Arquitetura e Setup Mobile
nav_order: 6
---

## 6. TypeScript: Segurança e Manutenibilidade em Aplicações Mobile  

O **TypeScript** é uma linguagem de programação de código aberto desenvolvida pela Microsoft que estende o JavaScript, adicionando **tipagem estática opcional**, interfaces, enums, generics e outros recursos. Sua principal vantagem é a **detecção antecipada de erros** durante o desenvolvimento, reduzindo bugs em tempo de execução e melhorando a **manutenibilidade** e **escalabilidade** de projetos.  

#### **Principais Benefícios do TypeScript no React Native**  
1. **Tipagem Estática**  
   - Define tipos explícitos para variáveis, funções e componentes, permitindo que IDEs como **VSCode** e **WebStorm** ofereçam **autocompletar inteligente**, verificação em tempo real e sugestões mais precisas.  
   - Exemplo:  
     ```typescript
     interface User {
       id: number;
       name: string;
       email: string;
     }
     
     const getUser = (id: number): User => { ... };
     ```  
   - Evita erros comuns como acessar propriedades inexistentes ou passar argumentos incorretos.  

2. **Melhor Documentação e Clareza do Código**  
   - Tipos e interfaces servem como documentação embutida, facilitando a **onboarding de novos desenvolvedores** em projetos grandes.  
   - Reduz a necessidade de comentários excessivos, pois os tipos já indicam o formato esperado dos dados.  

3. **Refatoração Segura**  
   - Ao alterar uma interface ou tipo, o compilador **identifica todos os lugares onde a mudança impacta**, evitando erros de inconsistência.  
   - Ferramentas como o **"Rename Symbol"** no VSCode funcionam com muito mais precisão.  

4. **Integração com Bibliotecas JavaScript**  
   - A maioria das bibliotecas populares do React Native (como **React Navigation**, **Redux**, **Axios**) possui **definições de tipo** (`@types/nome-da-biblioteca`), garantindo autocompletar e verificação de erros mesmo em código de terceiros.  

5. **Suporte a Recursos Modernos**  
   - TypeScript inclui suporte a **ES6+** (arrow functions, async/await, destructuring) e adiciona recursos como:  
     - **Generics** (para funções e componentes reutilizáveis).  
     - **Decorators** (usados em frameworks como **MobX** e **NestJS**).  
     - **Union Types e Intersection Types** para maior flexibilidade na tipagem.  

#### **Desafios e Considerações**  
- **Curva de Aprendizado**: Desenvolvedores acostumados com JavaScript puro podem levar algum tempo para se adaptar à sintaxe de tipos.  
- **Configuração Inicial**: Requer um **`tsconfig.json`** para definir regras de compilação (ex: `strict: true` para máxima segurança).  
- **Overhead em Projetos Pequenos**: Em aplicações muito simples, a tipagem pode parecer desnecessária, mas seu valor se torna evidente em projetos em crescimento.  

#### **Ou seja...**  
TypeScript **não é apenas uma "camada extra"**, mas uma ferramenta que **aumenta a robustez** do desenvolvimento mobile, especialmente em projetos de longo prazo. Sua adoção no React Native tem crescido significativamente, tornando-se um padrão em muitas empresas de tecnologia.

Por esse motivo, vamos aproveitar essa oportunidade para também abordar essa tecnologia! 😁

---

## 7. Certo! Agora sim, ao setup... 

Agora que justificamos nossas escolhas, a ideia é finalizar essa primeira aula já **vendo algo funcionar** no navegador ou no celular, sem instalar tudo que existe de uma vez. Para isso, basta configurar **Node.js**, **Git** e o **Expo CLI**. Emulador Android, Android Studio ou Xcode podem ficar para mais tarde, quando quisermos depurar algo específico ou gerar um build de produção. Abaixo vamos detalhar o processo de instalação das tecnologias que iremos utilizar.

#### 7.1 Pré-requisitos em comum — Node.js e Git

1. **Node.js (LTS 18 +)**

   * *Windows / Linux*: baixe a versão LTS no site oficial ou use o **nvm** para gerenciar versões:

     ```bash
     # Linux
     curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
     nvm install --lts
     ```
   * Confirme a instalação:

     ```bash
     node -v   # v18.x
     npm -v    # v9.x ou superior
     ```

2. **Git**

   * *Windows*: instale pelo [Git-for-Windows](https://git-scm.com/download/win).
   * *Linux (Debian/Ubuntu)*: `sudo apt update && sudo apt install git -y`
   * Verifique: `git --version`

> **Pronto!** Com esses dois itens você já consegue criar um projeto Expo, instalar dependências e ver o app rodando no browser ou no celular com Expo Go.

#### 7.2 Instalando o Expo CLI

* Use primeiro via **`npx`** (instalação local); só instale globalmente se preferir rapidez depois.

| Sistema | Comando sugerido                    | Observações                                                                                 |
| ------- | ----------------------------------- | ------------------------------------------------------------------------------------------- |
| Windows | `npm install -g expo-cli`           | Abra o PowerShell como administrador se houver erros de permissão.                          |
| Linux   | `npx create-expo-app@latest my-app` | Se quiser global: `npm install -g expo-cli`. Instale `make g++ python3` se o npm solicitar. |



#### 7.3 Criando seu primeiro app Expo (sem emulador)

1. **Escolha uma pasta de trabalho**:

   ```bash
   mkdir ~/meus-apps && cd ~/meus-apps
   ```

2. **Gere um projeto do zero**:

   ```bash
   npx create-expo-app@latest hello-grudsmv
   cd hello-grudsmv
   ```

   * Ao ser perguntado, selecione o template "blank (TypeScript)". Isso garantirá que o projeto já venha configurado com suporte a TypeScript, incluindo os arquivos .tsx, tsconfig.json e os tipos necessários do React Native.

   * ✅ Não se preocupe: mesmo que você não tenha o TypeScript instalado globalmente, o template já cuida disso. Basta escolher o “blank (TypeScript)” e tudo funcionará normalmente!

3. **Instale dependências** (se o script não fez isso):

   ```bash
   npm install
   ```

4. **Inicie o Metro Bundler**:

   ```bash
   npx expo start
   ```

   * Pressione **w** para abrir no navegador;
   * **t** para usar *tunnel* e escanear o QR Code com o **Expo Go**;
   * **a** ou **i** se já tiver um emulador Android/iOS rodando.

Se a tela **“Welcome to React Native”** aparecer, deu tudo certo — seu ambiente está pronto para as próximas aulas!

#### 7.4 Quando (e por que) instalar o emulador Android ou o Xcode?

| Preciso agora? | Recurso                  | Quando instalar                                              |
| -------------- | ------------------------ | ------------------------------------------------------------ |
| ❌              | **Android Studio + AVD** | Só se quiser depurar sem aparelho físico ou gerar APK local. |
| ❌              | **Xcode (macOS)**        | Apenas em Mac e se precisar compilar para iOS nativo.        |

> Lembre-se: o **Expo Go** cobre a maioria dos testes iniciais. Deixe os setups pesados para quando surgirem necessidades específicas.



#### 7.5 Editor VS Code e extensões

* **Download**: [VS Code](https://code.visualstudio.com/)
* Extensões recomendadas: **React Native Tools**, **ESLint**, **TypeScript React**


#### 7.6 Checklist rápido

| Item                              | Obrigatório agora | Instalar depois |
| --------------------------------- | ----------------- | --------------- |
| Node.js + npm                     | ✅                 | —               |
| Git                               | ✅                 | —               |
| Expo CLI ou `npx create-expo-app` | ✅                 | —               |
| Expo Go (app no celular)          | ❌ (opcional)      | Se quiser!               |
| Android Studio / AVD              | ❌                 | Quando precisar |
| Xcode / simulador iOS             | ❌                 | Quando precisar |

Com esse setup enxuto você já consegue criar, editar e rodar suas próprias aplicações React Native via Expo — o resto nós adicionaremos conforme as necessidades surgirem ao longo da disciplina.

Antes de começarmos, entretanto, é preciso ver um pouquinho de TypeScript, para nos familiarizarmos!

---

## **8. Conclusão**

A escolha pelo uso de **React Native com Expo** se justifica não apenas por sua curva de aprendizagem mais suave, mas também pelo forte ecossistema e pelo equilíbrio entre produtividade e desempenho. A introdução do **TypeScript** complementa essa abordagem com rigor tipológico e maior segurança em tempo de desenvolvimento.

A partir da próxima aula, começaremos nosso primeiro projeto funcional com Expo e React Native, configurando o ambiente, estruturando pastas e criando a primeira tela. 🤓

## **9. Exercícios de aquecimento em TypeScript**

#### Exercício 01 – `arrayUtils.js`

Neste exercício, você irá implementar três funções utilitárias em JavaScript moderno (ES6+), com foco em manipulação de arrays de objetos — uma habilidade comum no desenvolvimento com React Native.

Abaixo há três funções já prontas. Adicione-as a um arquivo arrayUtils.js:

```js
// unique([1,2,2]) → [1,2]
export const unique = arr => [...new Set(arr)];

// groupBy([{tipo:'A'},{tipo:'B'}],'tipo') → {A:[…], B:[…]}
export const groupBy = (arr, key) =>
  arr.reduce((acc, obj) => {
    (acc[obj[key]] = acc[obj[key]] || []).push(obj);
    return acc;
  }, {});

// sumBy([{valor:10},{valor:5}], 'valor') → 15
export const sumBy = (arr, key) =>
  arr.reduce((total, obj) => total + (obj[key] ?? 0), 0);
```

Após isso, escreva um arquivo `index.js` que utilize essas funções. Use `console.log` para demonstrar o funcionamento de cada função com ao menos dois exemplos distintos por função. Comente também o código, para entender como ele funciona!

#### Exercício 02 – `arrayUtils.ts`

Migre o código do exercício anterior para TypeScript. Para isso, implemente o código em um arquivo chamado `arrayUtils.ts`, declarando **interfaces** e **genéricos** onde couber. Em seguida, crie um arquivo `tsconfig.json` com a configuração `"strict": true` para ativar a verificação mais rigorosa do compilador.

Para realizar este exercício, será necessário ter o TypeScript instalado no projeto. Você pode instalá-lo localmente com:

```bash
npm install typescript --save-dev
```

Depois, gere o arquivo de configuração com:

```bash
npx tsc --init
```

Para rodar os testes diretamente no terminal precisaremos também do `ts-node`, sendo necessário instalá-lo com o comando abaixo:

```bash
npm install ts-node --save-dev
```

Garanta que, ao executar o comando abaixo, **nenhum erro seja exibido**:

```bash
npx tsc --noEmit
```

**Dica** `--noEmit`: diz ao compilador para não gerar nenhum arquivo .js — ou seja, somente verificar o código, sem produzir saídas. Se aparecerem erros no terminal, é sinal de que o código contém problemas de tipagem ou sintaxe, e você deve corrigi-los.

Ao final, o código deverá estar corretamente tipado e validado pelo compilador. 

#### Exercício 03 – `pokedex.ts`

Neste exercício, você irá construir um pequeno programa de linha de comando (CLI – *Command Line Interface*) utilizando **TypeScript**, que consulta dados da [PokéAPI](https://pokeapi.co/) e exibe informações básicas de um Pokémon.

Criar um arquivo `pokedex.ts` que:

1. **Recebe como argumento um nome ou ID de Pokémon**, passado diretamente no terminal.

   * Para isso, utilize `process.argv[2]`, que é o terceiro item do array `process.argv`:

     * `process.argv` é um array que armazena os argumentos passados na execução de um script via Node.js.
     * `process.argv[0]` → caminho do executável `node`.
     * `process.argv[1]` → caminho do arquivo `.ts` executado.
     * `process.argv[2]` → **primeiro argumento útil passado pelo usuário**.
     * Exemplo de uso:

       ```bash
       ts-node pokedex.ts pikachu
       ```

       Nesse caso, `process.argv[2]` terá o valor `"pikachu"`.

2. **Faz uma requisição `fetch`** para a URL:
   `https://pokeapi.co/api/v2/pokemon/{id_or_name}`


3. **Exibe no terminal o seguinte formato resumido de dados**:

   ```
   Pikachu – 0.4 m – 6 kg – Electric
   ```

   * Inclua: nome capitalizado, altura (em metros), peso (em kg) e o(s) tipo(s) do Pokémon.

4. **Trata erros** apropriadamente:

   * Se o Pokémon não existir, exiba uma mensagem amigável como:
     `❌ Pokémon não encontrado!`
   * Se houver falha de conexão, exiba:
     `⚠️ Erro de rede. Tente novamente.`

#### 🛠️ Dicas e Considerações:

* Para usar `fetch` no Node.js, você pode:

  * Utilizar o pacote `node-fetch`, instalando com:

    ```bash
    npm install node-fetch
    ```
* Usando Node.js v18+, `fetch` já é nativo.
* Bônus: tente utilizar `async/await` para lidar com chamadas assíncronas.
* Adicione comentários explicando o que cada parte faz.
