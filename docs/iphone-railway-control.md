# Controle da Railway pelo iPhone

Este projeto possui dois workflows manuais:

- `railway-start.yml`: liga os nove serviços do projeto `sunny-charm` na ordem das dependências e valida o fazer.ai agents e o Chatwoot.
- `railway-stop.yml`: desliga os nove serviços na ordem inversa e confirma que os endpoints públicos ficaram indisponíveis.

Os workflows não recriam imagens, bancos ou volumes. `railway down` remove os deployments ativos, mas preserva os serviços, suas variáveis e seus volumes persistentes.

## 1. Configurar a Railway no GitHub

Crie um token de projeto para o ambiente `production` na Railway. No fork GitHub, abra **Settings > Secrets and variables > Actions > New repository secret** e cadastre:

- Nome: `RAILWAY_TOKEN`
- Valor: o token de projeto criado na Railway

Nunca coloque esse token no workflow, no Atalhos ou em commits.

## 2. Criar um token mínimo para o Atalhos

No GitHub da conta que possui o fork, crie um token de acesso pessoal de granularidade fina:

- Acesso somente ao repositório `ramonpmendesx1707/agents`.
- Permissão de repositório **Actions: Read and write**.
- Defina uma expiração curta e renove quando necessário.

Esse token fica somente nos dois atalhos do seu iPhone. Não use um token clássico `ghp_...` com acesso amplo.

## 3. Atalho Ligar servidores

Adicione a ação **Obter conteúdo de URL**:

- URL: `https://api.github.com/repos/ramonpmendesx1707/agents/actions/workflows/railway-start.yml/dispatches`
- Método: `POST`
- Cabeçalho `Authorization`: `Bearer SEU_TOKEN_GITHUB`
- Cabeçalho `Accept`: `application/vnd.github+json`
- Cabeçalho `X-GitHub-Api-Version`: `2026-03-10`
- Corpo JSON: `ref` = `main`

Uma resposta HTTP 200 na versão atual da API (ou 204 em versões anteriores) significa que o GitHub aceitou a execução.

## 4. Atalho Desligar servidores

Use a mesma configuração, alterando somente a URL:

`https://api.github.com/repos/ramonpmendesx1707/agents/actions/workflows/railway-stop.yml/dispatches`

## 5. Verificar a execução

No iPhone, abra o repositório no GitHub e acesse a aba **Actions**. Os workflows usam o grupo de concorrência `railway-power-control`, impedindo que ligar e desligar sejam executados ao mesmo tempo.

O endpoint do fazer.ai agents é:

`https://agents-production-01bc.up.railway.app/api/health`

O Chatwoot é:

`https://chatwoot-production-526a.up.railway.app`
