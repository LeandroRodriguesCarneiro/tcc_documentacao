  # API do Chatbot - TCC

## Componentes da aplicação
O projeto é a união dos respositórios de: 
- API do chatbot:
  - Sendo a principal API que permite a interação com o modelo de linguagem;
  - https://github.com/LeandroRodriguesCarneiro/tcc_api_chat;
- API de ingestão de dados:
  - Pipeline de indexação dos documentos ao banco vetorial;
  - https://github.com/LeandroRodriguesCarneiro/tcc_api_ingestao_dados
- API Segurança:
  - Provedor de autenticação e cadastro de usuários;
  - https://github.com/LeandroRodriguesCarneiro/tcc_api_autenticacao;
- Frontend:
  - Interface web da aplicação interligando as API's acima em uma interface unificada;
  - https://github.com/LeandroRodriguesCarneiro/tcc_frontend;
- Infra estrutura:
  - Infraestura de orquestração de containers de cada uma das aplicações acima;
  - https://github.com/LeandroRodriguesCarneiro/tcc_infra_estrutura;
 
## Arquitetura do sistema
A arquitetura do sistema tem o seguinte desenho:
![Texto alternativo](imgs\ArquiteturaTCC.drawio.png)

1. Padrões de Projeto:

Todas as APIs foram implementadas em Python e seguem o padrão arquitetural Model-View-Controller (MVC), adotado com o objetivo de facilitar a manutenção, promover boas práticas de separação de responsabilidades (SoC) e permitir a evolução modular do sistema. No contexto das APIs RESTful desenvolvidas, o MVC foi adaptado da seguinte forma:
Models: Representam as estruturas de dados e refletem as tabelas do banco de dados.
Repositories: Camada responsável pelas operações de persistência (CRUD) e comunicação direta com o banco de dados.
DTOs (Data Transfer Objects): Garantem a correta validação e formatação dos dados de entrada e saída entre as camadas.
Services: Núcleo da lógica de negócio, responsável por orquestrar operações independentes da camada de persistência.
Controllers: Camada responsável por receber requisições HTTP e retornar respostas, delegando a lógica de processamento aos Services.

2. Linguagem e Framework Web

A escolha da linguagem Python baseou-se em seu amplo ecossistema voltado a Processamento de Linguagem Natural (PLN), extração de textos e desenvolvimento web. O framework FastAPI foi selecionado por sua natureza assíncrona (ASGI), alta performance e capacidade de lidar eficientemente com múltiplas requisições concorrentes — uma característica crucial para operações de I/O intensivas, como chamadas a LLMs ou consultas a bancos de dados.

3. Fluxo de Processamento Assíncrono e Mensageria

A funcionalidade de ingestão e processamento de documentos demanda execução assíncrona para evitar bloqueio de recursos durante a indexação de arquivos extensos. Para isso, adotou-se o Apache Kafka como sistema de mensageria.
O Kafka atua como um broker distribuído, assegurando durabilidade, tolerância a falhas e escalabilidade. As APIs publicam solicitações de processamento em tópicos específicos, enquanto consumidores dedicados (Workers) realizam a extração, limpeza e indexação do conteúdo de forma não bloqueante.

4. Componentes de Inteligência Artificial e PLN

O sistema integra diversas bibliotecas e frameworks avançados:
LangChain e LangGraph: Utilizados para construção de agentes conversacionais e gerenciamento de fluxos complexos (stateful), possibilitando orquestração de ferramentas e comportamentos multiagentes.
Docling: Biblioteca para extração de textos e OCR, garantindo alta precisão na geração dos dados utilizados pelo pipeline RAG.
Cohere: Fornece modelos LLMs e embeddings via API. A integração por REST permite alternar entre diferentes fornecedores sem grandes mudanças na arquitetura, mantendo flexibilidade e independência tecnológica.

5. Gerenciamento de Dados

A solução adota uma estratégia híbrida de armazenamento:
PostgreSQL (Relacional): Utilizado para persistência transacional, armazenamento de metadados, usuários e logs. Sua integração com SQLAlchemy e compatibilidade com Docker tornam-no uma escolha robusta e escalável.
ChromaDB (Vetorial): Responsável pelo armazenamento dos embeddings utilizadas para busca semântica no modelo RAG. Sua integração nativa com LangChain possibilita consultas vetoriais eficientes e de baixa latência.


6. Infraestrutura e MLOps

	A infraestrutura foi projetada com práticas de MLOps, contemplando:
Docker: Para empacotamento padronizado dos microsserviços e reprodutibilidade dos ambientes.
Kubernetes: Para orquestração dos containers, oferecendo escalabilidade automática, balanceamento de carga, resiliência (self-healing) e facilidade na implantação contínua.

7. Ferramentas de Desenvolvimento

Visual Studio Code: IDE principal para o desenvolvimento das APIs e scripts.
DBeaver Community: Ferramenta universal de gerenciamento de bancos de dados.
Git: Controle distribuído de versões, permitindo rastreamento e revisão de alterações.
GitHub: Repositório remoto e plataforma de colaboração, possibilitando CI/CD via GitHub Actions, versionamento de releases e gestão de pull requests.

8. Descrição dos Componentes da Arquitetura
   
  -  API de Autenticação

Gerência autenticação e autorização.
Implementa controle de acesso via tokens JWT.
Comunicação síncrona com as demais APIs.
Tecnologias: Python, FastAPI, SQLAlchemy, PostgreSQL.

  - API de Ingestão de Dados

Responsável por receber, validar e gerenciar documentos enviados para indexação.
Opera de forma assíncrona, garantindo maior escalabilidade.
Principais operações: upload, atualização, exclusão e listagem.
Tecnologias: Python, FastAPI, SQLAlchemy, Kafka, Docling, SentenceTransformer, LibreOffice.

  - Workers da API de Ingestão
    
Executam a extração, limpeza e indexação dos documentos.
Fragmentam arquivos em chunks utilizando PLN e regras de segmentação.
Geram embeddings e armazenam no ChromaDB.
Tecnologias: Python, Docling, LangChain, SQLAlchemy.

  - API do Chatbot
    
Interage com o usuário e realiza consultas RAG.
Combina contexto recuperado com respostas de LLMs.
Tecnologias: Python, FastAPI, LangChain, LangGraph, Cohere, SQLAlchemy.

  - Banco de Dados Relacional (PostgreSQL)
    
Armazena dados estruturados e metadados do sistema.

  - Banco Vetorial (ChromaDB)
    
Armazena embeddings para buscas semânticas de alta performance.

  - Frontend
    
Interface web para interação com o chatbot e gestão de documentos.
Tecnologias: React, React Hooks, Axios, Context, HTML, CSS

## Considerações sobre Infraestrutura
Embora a PoC tenha sido implantada localmente, a arquitetura foi desenhada com repositórios independentes e microsserviços conteinerizados, permitindo fácil migração para qualquer provedor de nuvem (AWS, GCP, Azure). Como requisito da PoC, priorizou-se o uso de ferramentas gratuitas para reduzir custos sem comprometer a validade dos experimentos.

## Contatos e Contribuições

Contribuições são bem-vindas! Para sugestões, melhorias ou relatórios de bug, abra uma issue ou envie um pull request.

Leandro Rodrigues Carneiro  

[GitHub](https://github.com/LeandroRodriguesCarneiro) | Contato: leandrorodrigues131531@gmail.com

