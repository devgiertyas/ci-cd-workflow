# CI/CD com Netlify e Prisma

Este repositório configura um sistema de deploy automatizado para duas branches principais: **develop** (desenvolvimento) e **main** (produção). Ele realiza o deploy do frontend no Netlify e gerencia migrações do banco de dados utilizando o Prisma.

---

## **Requisitos Mínimos**

- **Node.js**: Versão 22.4.0 ou superior  
- **Git**: Para gerenciar o repositório e branches  
- **Banco de Dados**: PostgreSQL (usando o Neon Tech para hospedar os bancos de desenvolvimento e produção)

---

## **URLs**

### **Frontend**

- **Desenvolvimento (Branch `develop`)**: [https://cicdworkflow-dev.netlify.app](https://cicdworkflow-dev.netlify.app)  
- **Produção (Branch `main`)**: [https://cicdworkflow-prod.netlify.app/](https://cicdworkflow-prod.netlify.app/)

### **Banco de Dados**

- **Desenvolvimento**: `postgresql://<usuario>:<senha>@<host>:<porta>/develop_db`  
- **Produção**: `postgresql://<usuario>:<senha>@<host>:<porta>/main_db`

> As URLs de conexão estão configuradas como variáveis de ambiente no `.env` e nos secrets do GitHub.

---

## **Como Criar uma Migração**

1. Configure o arquivo `.env` com a variável `DATABASE_URL` apontando para o banco correto.
2. Edite o arquivo `prisma/schema.prisma` para definir os modelos do banco de dados.
3. Rode o comando para gerar uma nova migração:

   ```
   npx prisma migrate dev --name <nome_da_migracao>
   ```

   - Exemplo:

     ```
     npx prisma migrate dev --name add_users_table
     ```

4. O Prisma criará os arquivos SQL da migração em `prisma/migrations/`.

5. Para aplicar as migrações em outro ambiente (como produção):

   ```
   npx prisma migrate deploy
   ```

---

## **Comandos Importantes**

```bash
# Gerar Cliente Prisma
npx prisma generate
```

```bash
# Abrir Prisma Studio
npx prisma studio
```

```bash
# Resetar o Banco de Dados (Apenas para Desenvolvimento)
npx prisma migrate reset
```

---

## **Workflows Automatizados**

### **Branch `develop`**

1. Sempre que houver um push para o branch `develop`, o workflow:
   - Realiza o deploy do frontend no Netlify ([URL de desenvolvimento](https://cicdworkflow-dev.netlify.app)).
   - Aplica migrações no banco de desenvolvimento.

### **Branch `main`**

1. Sempre que tiver um pull request fechado, o deploy é agendado diariamente às **21:00 (Horário de Brasília)**. 
2. O workflow:
   - Realiza o deploy do frontend no Netlify ([URL de produção](https://cicdworkflow-prod.netlify.app/)).
   - Aplica migrações no banco de produção.

---

## **Estrutura do Projeto**

```
|-- prisma/
|   |-- schema.prisma       # Configuração do banco de dados
|   |-- migrations/         # Diretório das migrações geradas pelo Prisma
|-- .github/
|   |-- workflows/
|       |-- develop.yml     # Workflow de CI/CD para o branch develop
|       |-- main.yml        # Workflow de CI/CD para o branch main
|-- index.html              # Página frontend estática
|-- package.json            # Configuração do Node.js
|-- README.md               # Este arquivo
|-- .env                    # Variáveis de ambiente (não commitado)
```

---

## **Configuração de Variáveis de Ambiente**

### **Arquivo `.env`**

Crie o arquivo `.env` com as seguintes variáveis:

```
# URL do banco de desenvolvimento
DATABASE_URL="postgresql://<usuario>:<senha>@<host>:<porta>/develop_db"
```

### **GitHub Secrets**

Adicione os seguintes secrets no GitHub:

1. **Netlify Secrets**:
   - `NETLIFY_AUTH_TOKEN`: Token de acesso à API do Netlify.
   - `NETLIFY_SITE_ID_DEVELOP`: API ID do site Netlify para o branch `develop`.
   - `NETLIFY_SITE_ID_MAIN`: API ID do site Netlify para o branch `main`.

2. **Banco de Dados Secrets**:
   - `DATABASE_URL_DEVELOP`: URL do banco de desenvolvimento.
   - `DATABASE_URL_MAIN`: URL do banco de produção.

---

## **Como Contribuir**

1. Crie uma branch para suas alterações:

   ```
   git checkout -b feature/nova-funcionalidade
   ```

2. Faça commits com mensagens claras e descritivas:

   ```
   git commit -m "Adiciona nova funcionalidade"
   ```

3. Abra um **Pull Request** para o branch `develop`.

---

Se precisar de ajuda ou quiser sugerir melhorias, entre em contato! 🚀
