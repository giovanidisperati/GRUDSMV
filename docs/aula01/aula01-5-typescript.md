---
layout: aula
title: "6. TypeScript: Segurança e Manutenibilidade"
parent: Aula 01 - Panorama, Arquitetura e Setup Mobile
nav_order: 5
---

## 5. Expo: Facilitador no Desenvolvimento Mobile

Embora o React Native seja um divisor de águas ao permitir o desenvolvimento híbrido com interface nativa, sua configuração inicial tradicional exigia o uso de ferramentas pesadas como o **Android Studio**, além de lidar com variáveis de ambiente, SDKs e builds nativos. Para iniciantes, isso representava uma barreira considerável. 😬

Foi justamente para superar esse obstáculo que surgiu o **Expo** — uma plataforma que simplifica significativamente o processo de desenvolvimento com React Native. Com ele, é possível criar, testar e compartilhar aplicativos sem precisar instalar um emulador ou configurar um ambiente nativo completo. Basta um navegador, um celular com o app **Expo Go** e poucos comandos no terminal para ver a mágica acontecer. 🎉

Para facilitar a entrada de novos desenvolvedores no ecossistema React Native, o time do Expo criou um conjunto de ferramentas que automatizam o processo de configuração e integração de bibliotecas nativas. 

#### Características Principais:
- **Expo CLI**: Ferramenta de linha de comando que permite criar e gerenciar projetos com facilidade. Com o comando `npx create-expo-app`, é possível iniciar um projeto funcional sem a necessidade imediata de configurar ambientes complexos como Android Studio ou Xcode.
- **Expo Go**: Aplicativo que permite testar projetos em dispositivos físicos sem a necessidade de compilação nativa, ideal para prototipagem rápida.
- **Módulos Pré-configurados**: O Expo oferece uma variedade de módulos prontos para uso, como `expo-camera`, `expo-location`, e `expo-notifications`, que simplificam o acesso a APIs nativas comuns.

#### Vantagens:
1. **Configuração Simplificada**: Elimina a necessidade de lidar diretamente com código nativo em muitos casos, reduzindo a curva de aprendizado para iniciantes.
2. **Desenvolvimento Multiplataforma**: Permite testar apps em dispositivos iOS e Android a partir de sistemas operacionais como Windows ou Linux, sem a necessidade de um Mac para desenvolvimento inicial.
3. **Build na Nuvem**: O serviço EAS Build permite compilar apps para iOS e Android sem configurar ambientes locais complexos, ideal para times que não possuem acesso a máquinas Mac.

#### Desvantagens:
1. **Limitações em APIs Nativas**: Não suporta todas as funcionalidades nativas, como Bluetooth ou processamento em segundo plano avançado, o que pode ser um obstáculo para projetos mais complexos.
2. **Tamanho do Artefato**: Os arquivos gerados (APK/IPA) tendem a ser maiores devido à inclusão de bibliotecas do Expo, mesmo que não sejam utilizadas.
3. **Dependência do Ecossistema Expo**: Atualizações do React Native chegam com atraso no Expo, e a migração para o React Native CLI (via `eject`) pode ser necessária para projetos que exigem maior flexibilidade.

#### Configuração em Windows e Linux:
- **Windows**: 
- Requer Node.js LTS, Git e Python instalados. Em alguns casos, é necessário ajustar políticas de execução no PowerShell (e.g., `Set-ExecutionPolicy Unrestricted`) para instalar o Expo CLI globalmente.
- Problemas comuns incluem permissões de instalação (solucionáveis com `sudo` no Linux ou PowerShell como administrador no Windows).
- **Linux**: 
- Recomenda-se o uso de `nvm` para gerenciar versões do Node.js e evitar conflitos de permissões.
- A instalação via `npm install -g expo-cli` pode exigir ajustes de permissões ou a instalação de dependências adicionais como `watchman`.

#### Quando Usar o Expo?
- **Indicado para**: Iniciantes, MVPs, projetos que não requerem APIs nativas não suportadas ou times que precisam de agilidade no desenvolvimento.
- **Alternativas**: Para projetos avançados, o "Bare Workflow" do Expo ou o React Native CLI permitem acesso total ao código nativo, com o trade-off de maior complexidade na configuração.

#### Migração para o CLI:
Em casos onde o Expo não atende às necessidades do projeto, o comando `npx expo prebuild` gera as pastas nativas (`android` e `ios`), permitindo a transição para o React Native CLI sem perder funcionalidades já implementadas! 🤓

Uma vez que nosso ambiente estiver configurado com React Native e Expo, precisaremos tomar uma decisão importante: **qual linguagem usaremos para programar?** Tanto o JavaScript tradicional quanto o TypeScript são suportados pela stack que escolhemos — e você verá muitos exemplos nas duas linguagens ao longo da internet.

Porém, nesta disciplina, optaremos por usar **TypeScript** como linguagem principal! A seguir, vamos entender o porquê dessa escolha e quais benefícios ela traz para a organização, segurança e escalabilidade do nosso código.

---
