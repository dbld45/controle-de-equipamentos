# Publicar o Controle AV no Vercel — passo a passo

## 1. Coloque o projeto no GitHub

Crie um repositório novo no GitHub e envie **o conteúdo desta pasta** para ele. O arquivo `vercel.json` precisa ficar na raiz do repositório, ao lado de `package.json`.

Você pode fazer isso pelo VS Code/GitHub Desktop ou pelo site do GitHub.

## 2. Importe no Vercel

1. Entre em `vercel.com`.
2. Clique em **Add New > Project**.
3. Importe o repositório criado no GitHub.
4. Deixe o **Root Directory** na raiz do repositório.
5. O projeto já inclui o comando de build e o diretório de saída no `vercel.json`.

Você pode fazer o primeiro deploy mesmo antes de configurar o banco; a interface poderá ser publicada, mas a API só responderá depois que `DATABASE_URL` e as variáveis de segurança estiverem configuradas.

## 3. Crie o banco PostgreSQL

No projeto do Vercel:

1. Abra **Storage / Marketplace**.
2. Adicione **Neon Postgres**.
3. Crie um banco novo ou conecte um banco Neon existente.
4. Confira em **Project Settings > Environment Variables** se existe uma variável `DATABASE_URL` com a conexão PostgreSQL.

Não precisa executar SQL manualmente. Na primeira chamada da API, o sistema cria as tabelas e índices automaticamente.

## 4. Configure as variáveis

Em **Project Settings > Environment Variables**, configure para Production, Preview e Development conforme desejar:

```text
DATABASE_URL        fornecida pelo Neon/Postgres
JWT_SECRET          pelo menos 32 caracteres aleatórios
ADMIN_EMAIL         seu e-mail de administrador
ADMIN_PASSWORD      sua senha inicial, mínimo 8 caracteres
SEED_DEMO           true para testar / false para uso real
CORS_ORIGIN         *
PHOTO_MAX_MB        2
```

Exemplo de `JWT_SECRET`:

```text
controle-av-2026-mude-esta-chave-por-uma-secreta-987654
```

Não use esse exemplo literalmente em produção; crie uma chave só sua.

## 5. Faça o Redeploy

Depois de salvar as variáveis:

1. Abra **Deployments**.
2. No último deploy, escolha **Redeploy**.
3. Quando terminar, abra o domínio `https://seu-projeto.vercel.app`.

O frontend usa `/api`, portanto frontend e backend funcionam no mesmo domínio.

## 6. Primeiro acesso

Use o `ADMIN_EMAIL` e `ADMIN_PASSWORD` que você colocou nas variáveis do Vercel.

Se `SEED_DEMO=true`, também haverá:

```text
operador@controleav.local
Operador@123
```

Depois de validar tudo, é recomendável trocar `SEED_DEMO` para `false` antes de usar um banco novo de produção.

## 7. Teste da API

Abra:

```text
https://SEU-DOMINIO.vercel.app/api/health
```

Resposta esperada:

```json
{
  "ok": true,
  "service": "controle-equipamentos-av-api",
  "database": "postgres"
}
```

## Scanner no celular

O domínio do Vercel já usa HTTPS, que é o requisito do navegador para acesso à câmera no celular. Ao abrir a tela Scanner, permita o uso da câmera quando o navegador solicitar.

## Observações de produção

- Esta edição usa PostgreSQL em vez de SQLite.
- Backups são snapshots JSON armazenados no próprio PostgreSQL e também podem ser baixados.
- Fotos são armazenadas no banco para manter o deploy simples. Se o acervo crescer muito, o próximo passo recomendado é mover fotos para Vercel Blob.
- Para alterar a senha inicial do administrador depois do deploy, use a tela de Usuários ou defina uma senha segura desde o início.
