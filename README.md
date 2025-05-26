# Projeto de automação integrado com n8n, Postgres Vector, Adminer, Redis, Evolution API, Ngrok e Ollama

Este projeto configura uma stack de serviços para automação de fluxos de trabalho e integração de APIs orquestrando vários serviços utilizando Docker Compose para criar um ambiente de desenvolvimento completo para automação, gerenciamento de dados, comunicação e IA.

## Serviços

Os seguintes serviços são definidos no `docker-compose.yml`:

- **n8n:** Plataforma de automação de workflows.
- **Postgres:** Banco de dados PostgreSQL com extensão pgvector para funcionalidades vetoriais.
- **Evolution API:** API para integração com plataformas de mensagens.
- **Ngrok:** Cria túneis seguros para expor o n8n para a internet (útil para webhooks).
- **Redis:** Banco de dados em memória para cache, utilizado pela Evolution API.
- **Adminer:** Interface web para administração do banco de dados Postgres.
- **Ollama:** Ferramenta para executar e gerenciar modelos de linguagem grandes (LLMs) localmente.

Abaixo estão as instruções para instalar e começar a usar todos os serviços.

## Pré-requisitos

Antes de começar, certifique-se de ter os seguintes itens instalados em sua máquina:

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- Uma conta no [Ngrok](https://ngrok.com/) (necessária para expor o n8n publicamente)

## Passo a Passo para configuração e instalação

### 1. Clone o Repositório

Clone este repositório para sua máquina local:

```bash
git clone https://github.com/seu-usuario/seu-repositorio.git
cd seu-repositorio
```

### 2. Crie uma Conta no Ngrok

- Acesse [ngrok.com](https://ngrok.com/) e crie uma conta gratuita.
- Após o login, vá até o painel e copie seu **Authtoken** (exemplo: `1a2b3c4d5e6f7g8h9i0j_xxxxxxxxxxxxxxxxxxxxxx`).

### 3. Crie um arquivo `.env`

Copie o conteúdo de `.env_exemplo` para um novo arquivo chamado `.env`.

```bash
cp .env_exemplo .env
nano .env
```

Preencha as variáveis de ambiente com os valores apropriados.

**Variáveis de Ambiente:**

As seguintes variáveis precisam ser configuradas no arquivo `.env`:

- **Postgres:**
  - `POSTGRES_USER`: Nome de usuário do Postgres.
  - `POSTGRES_PASSWORD`: Senha do usuário Postgres.
  - `POSTGRES_DB`: Nome do banco de dados.
- **n8n:**
  - `N8N_BASIC_AUTH_ACTIVE`: Ativar autenticação básica ( `true` ou `false`).
  - `N8N_BASIC_AUTH_USER`: Nome de usuário para autenticação no n8n.
  - `N8N_BASIC_AUTH_PASSWORD`: Senha para autenticação no n8n.
  - `DB_TYPE`: Tipo de banco de dados (deve ser `postgresdb`).
  - `DB_POSTGRESDB_HOST`: Host do banco de dados Postgres (deve ser `postgres`).
  - `DB_POSTGRESDB_PORT`: Porta do Postgres (geralmente 5432).
  - `DB_POSTGRESDB_USER`, `DB_POSTGRESDB_PASSWORD`, `DB_POSTGRESDB_DATABASE`: Valores correspondentes do Postgres.
  - `WEBHOOK_URL`: URL base para webhooks do n8n (geralmente o endereço do ngrok).
- **Evolution API:**
  - `AUTHENTICATION_API_KEY`: Chave de API para autenticação na Evolution API.
  - `DATABASE_ENABLED`: Habilita a conexão com o banco de dados ( `true` ou `false`).
  - `DATABASE_PROVIDER`: Provedor do banco de dados (deve ser `postgresql`).
  - `DATABASE_CONNECTION_URI`: String de conexão com o banco de dados Postgres.
  - `DATABASE_CONNECTION_CLIENT_NAME`: Nome do cliente de conexão do banco de dados.
  - `DATABASE_SAVE_DATA_INSTANCE`: Salvar dados da instância no banco de dados ( `true` ou `false`).
  - `DATABASE_SAVE_DATA_NEW_MESSAGE`: Salvar novas mensagens no banco de dados ( `true` ou `false`).
  - `DATABASE_SAVE_MESSAGE_UPDATE`: Salvar atualizações de mensagens no banco de dados ( `true` ou `false`).
  - `DATABASE_SAVE_DATA_CONTACTS`: Salvar dados de contatos no banco de dados ( `true` ou `false`).
  - `DATABASE_SAVE_DATA_CHATS`: Salvar dados de chats no banco de dados ( `true` ou `false`).
  - `DATABASE_SAVE_DATA_LABELS`: Salvar dados de labels no banco de dados ( `true` ou `false`).
  - `DATABASE_SAVE_DATA_HISTORIC`: Salvar dados históricos no banco de dados ( `true` ou `false`).
- **Redis (Evolution API):**
  - `CACHE_REDIS_ENABLED`: Habilita o cache Redis ( `true` ou `false`).
  - `CACHE_REDIS_URI`: URI de conexão com o Redis.
  - `CACHE_REDIS_PREFIX_KEY`: Prefixo para as chaves do cache Redis.
  - `CACHE_REDIS_SAVE_INSTANCES`: Salvar instâncias no cache Redis ( `true` ou `false`).
- **Ngrok:**
  - `NGROK_PROTOCOL`: Protocolo a ser utilizado (geralmente `http`).
  - `NGROK_PORT`: Porta do serviço a ser exposto (deve ser `n8n:5678`).
  - `NGROK_AUTHTOKEN`: Seu token de autenticação do Ngrok (necessário para usar domínios personalizados).

- Substitua `SUA_KEY` em `AUTHENTICATION_API_KEY` por uma chave de API segura para o Evolution API.
- Substitua `SUA_KEY` em `NGROK_AUTHTOKEN` pelo token obtido no passo 2.
- Ajuste `POSTGRES_PASSWORD`, `N8N_BASIC_AUTH_USER` e `N8N_BASIC_AUTH_PASSWORD` para valores seguros de sua escolha.
- Substitua sua SUA_KEY do ngrok no arquivo ngrok.yml

**Importante:** Defina senhas seguras para o Postgres e o n8n.
Se preferir, utilize o gerador de senhas do [LastPass](https://www.lastpass.com/pt/features/password-generator) 

### 4. Inicie os Serviços

Na raiz do projeto, execute o comando abaixo para subir todos os serviços:

```bash
docker-compose up -d
```

Isso irá baixar as imagens necessárias (caso ainda não tenham sido baixadas), criar os contêineres e iniciar todos os serviços em modo "detached" (em segundo plano, sem bloquear o terminal).

- **n8n**: Ferramenta de automação de fluxos de trabalho (porta 5678).
- **redis**: Cache para o Evolution API (porta 6380).
- **postgres**: Banco de dados PostgreSQL com suporte a vetores (porta 5432).
- **adminer**: Interface web para gerenciar o PostgreSQL (porta 8081).
- **evolution-api**: API para integração de mensagens (porta 8080).
- **ngrok**: Túnel para expor o n8n publicamente (porta 4040).

### 5. Verifique o acesso aos Serviços

- **n8n:** Acesse a interface web em `http://localhost:5678`. Use as credenciais definidas em `.env` ( `N8N_BASIC_AUTH_USER` e `N8N_BASIC_AUTH_PASSWORD`).
- **Redis:** O redis estará disponível na porta 6380.
- **Postgres:** O banco de dados estará disponível na porta 5432. Você pode usar o `Adminer` para gerenciá-lo.
- **Adminer:** Acesse em `http://localhost:8081`. Conecte-se ao Postgres usando as credenciais definidas no `.env`.
- **Evolution API:** Acesse a API em `http://localhost:8080`.
- **Ngrok:** A interface web do ngrok estará disponível em `http://localhost:4040`. A URL pública gerada pelo ngrok (ex: `https://viable-proud-tomcat.ngrok-free.app`) deve ser definida na variável `WEBHOOK_URL` no arquivo `.env`.
- **Ollama:** Acesse a API na porta 11434. Consulte a documentação do Ollama para instruções sobre como usar a API.

### 6. Configure os Webhooks

Como este projeto utiliza o Ngrok, a URL pública gerada para o n8n muda a cada vez que os serviços são reiniciados. Você precisará:

- Acessar `http://localhost:4040` para obter a nova URL do Ngrok (exemplo: `https://abcd-1234.ngrok.io`).
- Atualizar a URL do webhook no **Evolution API** (geralmente em suas configurações ou endpoints de integração).
- Se você configurou webhooks em outros serviços ou fluxos do n8n que dependem dessa URL, atualize-os também.

**Lembrete**: Sempre redefina as URLs dos webhooks após reiniciar os serviços para garantir que tudo funcione corretamente.

### 7. Pare os Serviços (Opcional)

Para parar os serviços, use:

```bash
docker-compose down
```

Para parar e remover os volumes (limpar dados), use:

```bash
docker-compose down -v
```

## Estrutura dos Serviços

- **n8n**: Ferramenta de automação conectada ao PostgreSQL.
- **Evolution API**: API de mensagens com suporte a Redis e PostgreSQL.
- **PostgreSQL**: Banco de dados principal com suporte a vetores (via `pgvector`).
- **Redis**: Cache para o Evolution API.
- **Ngrok**: Expõe o n8n para acesso externo.
- **Adminer**: Interface para gerenciamento do banco de dados.

## Notas

- Certifique-se de que as portas (5678, 5432, 8080, 4040, 6380, 8081) estejam livres em sua máquina.
- **Ngrok:** Fornece uma URL temporária. Para uma URL fixa (domínio personalizado), você precisa de uma conta paga e configurar o `NGROK_AUTHTOKEN` no arquivo `.env` ou considere uma outra solução de túnel.
- **Volumes:** Os dados do n8n e do Postgres são armazenados em volumes Docker (`n8n_data` e `postgres_data`), para que não sejam perdidos ao reiniciar os contêineres.
- **Rede:** Todos os contêineres estão na mesma rede Docker (`minha_rede`), o que permite que eles se comuniquem entre si usando seus nomes de serviço (ex: `postgres`, `n8n`).
- Mantenha o arquivo `.env` seguro e não o compartilhe publicamente.
 
## Próximos Passos

- Explore a documentação de cada serviço para aprender mais sobre como usá-los.
- Comece a criar workflows no n8n para automatizar suas tarefas.
- Integre a Evolution API com seus fluxos de trabalho para enviar e receber mensagens.
- Utilize o Ollama para adicionar funcionalidades de IA aos seus projetos.
