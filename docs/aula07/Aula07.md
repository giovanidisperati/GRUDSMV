---
layout: aula
title: Aula 07 - Construindo o To-do List
nav_order: 9
has_children: true
permalink: /aula07
---

# Aula 07 – Construção da API de Gerenciamento de Tarefas (To-Do List)

Na Aula 06, evoluímos a nossa API aplicando boas práticas como o uso de `ResponseEntity`, a organização da documentação com Swagger/OpenAPI, a injeção de dependência via construtor e a introdução dos primeiros testes automatizados. Agora, nesta aula, desenvolveremos a nossa **API de Gerenciamento de Tarefas** (To-Do List) seguindo os princípios RESTful e aplicando todos os conceitos que vimos até aqui. Vamos, a partir dessa aula, dar continuidade nos conceitos vistos anteriormente por meio da resolução do exercício do To-Do List.

Daremos ênfase na correta separação de responsabilidades em camadas, na implementação de boas práticas de validação, na construção de DTOs e no tratamento adequado de exceções.

A proposta é desenvolver uma API REST que permita que usuários criem, consultem, atualizem e excluam tarefas pessoais. Cada tarefa possuirá atributos como título, descrição, prioridade, categoria, data limite, entre outros.

As funcionalidades obrigatórias incluem:
- CRUD completo para tarefas.
- Pesquisa de tarefas por categoria.
- Marcação de tarefas como concluídas.
- Paginação e ordenação dos resultados.
- Validação de campos obrigatórios e regras de negócio.
- Tratamento global de exceções.
- Testes unitários e funcionais dos endpoints.

Neste exercício, poderíamos ter seguido a mesma abordagem adotada na Aula 06, onde organizamos nosso código apenas com Controller → Repository → Model → DTO. No entanto, para tornar nossa arquitetura ainda mais robusta e alinhada às boas práticas, incluiremos também uma camada de serviço (@Service). Essa nova camada será responsável por isolar a lógica de negócio, promovendo melhor organização, reutilização de código e maior facilidade na manutenção e nos testes.





## 4. Conclusão

Chegamos ao final da nossa Aula 07 — e, com ela, estruturamos e implementamos uma **API RESTful completa** para gerenciamento de tarefas, aplicando conceitos fundamentais que temos explorado nas últimas semanas!

Nesta aula, você viu na prática:

- Como organizar um projeto em **camadas bem definidas** (model, repository, service, controller, dto, exception, config);
- A **importância da camada Service** para centralizar a lógica de negócio e promover manutenibilidade e testabilidade;
- A construção e utilização de **DTOs** para proteger e padronizar as entradas e saídas da nossa API;
- O uso de **ModelMapper** para facilitar a conversão entre objetos internos e externos;
- A implementação de um **tratamento global de exceções** robusto e padronizado;
- E a criação de **testes automatizados** (unitários e funcionais) para garantir a qualidade e a confiabilidade da aplicação.

Mais do que apenas codar, exercitamos um olhar **crítico e consciente** sobre a arquitetura da aplicação — refletindo sobre *por que* certas escolhas são feitas (como a inclusão da camada de serviço) e *quando* elas de fato agregam valor.

Fica cada vez mais evidente que dominar desenvolvimento de APIs não se resume a conhecer frameworks ou decorar anotações — é sobre **saber estruturar soluções que sejam limpas, compreensíveis, escaláveis e evolutivas**.

É isso que queremos deixar claro: não se trata de aprender a fazer APIs com uso do Spring Boot, se trata de entender os fundamentos. O Java e o Spring são meios, não fim. Todos os conceitos e discussões que temos trazido são transferíveis para outras linguagens e frameworks. 

E tenha sempre em mente: **o melhor código é aquele que é fácil de entender, de testar e de melhorar**. E todos os princípios aplicados nesta aula caminham nesse sentido.

Vamos explorar, na próxima aula, os conceitos autenticação, autorização e segurança de APIs — levando nossas aplicações para um nível mais próximo do mundo real. 🚀

E claro... já sabem o que vamos ter, né? 

---

## Desafios 🏋️‍♂️

Para consolidar ainda mais os conceitos vistos e introduzir **novas práticas**, você deverá implementar as seguintes melhorias no projeto atual como exercício:

### 1. Implementar Autenticação de Usuários

- **Crie uma funcionalidade de registro de novos usuários** (`UserRegistrationDTO`, `UserController`, etc.).
- **Implemente autenticação** via **JWT (JSON Web Token)**:
  - Crie endpoints para login e geração de token (por exemplo, `/api/auth/login`).
  - Configure o projeto para aceitar apenas requisições autenticadas em nossos endpoints de tarefas.
  
Com isso, nossa API se tornará mais realista, pois será necessário fazer login para manipular as tarefas!

☀️ **DICA**
- Assista o vídeo a seguir, da Giuliana Bezerra, para ter uma explicação inicial de como fazer essa implementação. Vídeo **muito** didático e com uso de ferramentas modernas para facilitar nossa vida: [https://www.youtube.com/watch?v=kEJ8a1w4a2Q](https://www.youtube.com/watch?v=kEJ8a1w4a2Q)

### 2. Relacionar Tarefas a Usuários

- **Associe cada tarefa a um usuário específico** no momento da criação.
- **Garanta que apenas o dono da tarefa** possa visualizar, atualizar, concluir ou excluir suas próprias tarefas.
  
Esse exercício reforçará a prática de regras de negócio e de segurança de acesso aos dados, que é fundamental no desenvolvimento de APIs RESTful seguras.

### 3. Implementar Controle de Acesso com Papéis (Roles)

- **Crie roles de usuário**: `USER` e `ADMIN`.
- **Permita que apenas ADMINs possam consultar todas as tarefas** (endpoint `/api/tasks`).
- **Usuários comuns (`USER`) só devem poder gerenciar suas próprias tarefas**.
  
Esse controle de papéis é essencial para implementar autorização baseada em responsabilidades — uma prática indispensável em aplicações reais.