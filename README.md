# webhook-inspector — node-react

Projeto full-stack pequeno para inspecionar e armazenar webhooks. Monorepo com duas aplicações:

- `api` — servidor Node.js (Fastify + Drizzle) para receber, armazenar e listar webhooks
- `web` — frontend React (Vite + TypeScript) para visualizar webhooks e gerar handlers

## Tecnologias

API (api/)

- Node.js + TypeScript
- Fastify
- Drizzle ORM (Postgres) + drizzle-zod
- Zod para validação
- uuidv7 (ids)
- Docker / docker-compose (opcional, para DB)

Web (web/)

- React + TypeScript
- Vite
- @tanstack/react-query (data fetching)
- Radix UI (dialog, etc.)
- Lucide icons
- Zod (client schemas)

Ferramentas de workspace

- pnpm workspace (gerenciamento de pacotes)

## Estrutura do projeto

Árvore resumida (mostra os arquivos e pastas principais):

```
node-react/
├─ api/
│  ├─ package.json                # scripts e dependências do backend
│  ├─ drizzle.config.ts
│  ├─ src/
│  │  ├─ server.ts                # entrada do Fastify
│  │  ├─ env.ts                   # variáveis de ambiente
│  │  ├─ db/
│  │  │  ├─ index.ts              # inicialização do Drizzle (db client)
│  │  │  ├─ seed.ts               # script de seed (ex.: popular tabela)
│  │  │  ├─ migrations/
│  │  │  └─ schema/
│  │  │     └─ webhooks.ts        # definição da tabela webhooks
│  │  └─ routes/
│  │     ├─ list-webhooks.ts      # rota GET /api/webhooks
│  │     ├─ capture-webhook.ts    # rota para capturar webhooks
│  │     └─ ...                   # outras rotas (generate, delete, get)
│  └─ docker-compose.yml          # opcional: postgres para dev

├─ web/
│  ├─ package.json                # scripts e dependências do frontend
│  ├─ src/
│  │  ├─ main.tsx                 # entrada do React
│  │  ├─ routes/
│  │  │  ├─ index.tsx
│  │  │  └─ webhooks.$id.tsx
│  │  ├─ components/
│  │  │  ├─ webhooks-list.tsx     # lista paginada de webhooks
│  │  │  ├─ webhooks-list-item.tsx# item da lista
│  │  │  └─ ui/                   # componentes pequenos (badge, checkbox..)
│  │  └─ http/schemas/            # Zod schemas usados no cliente
│  └─ public/

├─ package.json                    # workspace package.json (root)
└─ pnpm-workspace.yaml
```

## Quick start (desenvolvimento)

Pré-requisitos: Node.js (>=18), pnpm, Docker (opcional para Postgres).

1. Instalar dependências na raiz:

```powershell
pnpm install
```

2. Rodar banco de dados (opcional):

```powershell
# Se quiser usar docker-compose no diretório `api`
cd api; docker compose up -d
```

3. Rodar as aplicações (em terminais separados):

```powershell
# API (na pasta api)
cd api; pnpm run dev

# Web (na pasta web)
cd web; pnpm run dev
```

Observação: os scripts `dev` podem variar conforme o `package.json` de cada pacote. Se não existir um script `dev` use `pnpm -w run` ou abra o `package.json` correspondente para ver os scripts disponíveis.

## Cursor / paginação

Este projeto usa paginação por cursor e atualmente o cursor é baseado em `createdAt` (ISO string). Ao listar webhooks, o servidor retorna `nextCursor` como ISO string e o frontend valida esse valor com Zod.

## Troubleshooting comum

- Erros `invalid_type` / `is_NaN` do Zod: geralmente indicam que o shape ou tipo da resposta da API não bate com o schema do cliente (ex.: datas como strings vs Date). Verifique o `console` do navegador para ver `raw /api/webhooks response:` (há logs de debug no frontend).
- Erro 500 no backend ao listar webhooks: checar migrations/schema e formatos de campos (ex.: `created_at` é `timestamp`).

## Contribuição

1. Abra uma issue descrevendo o problema/feature
2. Crie um branch com nome claro
3. Faça um PR descrevendo as mudanças

## Licença

ISC (ver package.json).

---
