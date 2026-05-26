# EnvLicense — Plataforma de Gestão de Licenças Ambientais

> Plataforma corporativa de conformidade regulatória ambiental com IA, OCR, dashboards executivos e georreferenciamento.
> Stack: **FastAPI + Supabase + OpenAI + Vanilla JS + Tailwind + Chart.js + Leaflet**

-----

## ✅ Status atual do seu Supabase

**Projeto Supabase: `envlicense` (`znduhfivfkykxbgdrgix`)**

A migração de dados já foi feita. O que tem dentro hoje:

|Tabela            |Dados                                                                                 |
|------------------|--------------------------------------------------------------------------------------|
|`agencies`        |11 órgãos (IBAMA, ANP, INEMA, IDEMA, ADEMA, SEMACE, INEA, IEMA, FEAM, SEMA-MT, CETESB)|
|`fields`          |49 campos operacionais                                                                |
|`assets`          |**2.283 ativos migrados** com situação, tipo, lat/long, profundidade, bacia           |
|`licenses`        |1 licença migrada                                                                     |
|`conditionalities`|9 condicionantes migradas                                                             |
|`license_assets`  |3 vínculos preservados                                                                |
|`cost_references` |11 referências de custo migradas                                                      |
|`legacy_*`        |Suas tabelas antigas preservadas (somente admin pode ler)                             |

Segurança: **RLS ativa em todas as tabelas operacionais**. Os logins agora usam o sistema de Auth do Supabase (não mais a tabela `legacy_users` com matrícula/senha).

-----

## 🚀 Próximos passos — você está aqui

### ⏭️ ETAPA A — Criar buckets de Storage (2 min)

Os PDFs das licenças precisam de um lugar para morar.

1. Vá em <https://supabase.com> → projeto `envlicense`
1. Menu da esquerda → ícone de **balde** (Storage)
1. Botão **New bucket** → nome `licenses` → **Private bucket ✅** → Create
1. Botão **New bucket** → nome `evidences` → **Private bucket ✅** → Create

### ⏭️ ETAPA B — Criar seu usuário admin (3 min)

1. Menu da esquerda → ícone de **pessoinha** (Authentication)
1. Aba **Users** → **Add user → Create new user**
1. Preencha email + senha forte → marque **Auto Confirm User ✅** → Create user
1. Volte ao **SQL Editor** (ícone `>_`) → cole isto (trocando seu email):

```sql
update public.profiles
set role = 'admin', full_name = 'Seu Nome'
where email = 'seuemail@empresa.com';
```

Clique **Run**.

### ⏭️ ETAPA C — Pegar as chaves do Supabase (3 min)

Menu da esquerda → engrenagem ⚙️ (Settings) → **API**. Copie e cole num bloco de notas:

|Chave           |Onde está                                      |
|----------------|-----------------------------------------------|
|**Project URL** |`https://znduhfivfkykxbgdrgix.supabase.co`     |
|**anon public** |Quadro “Project API keys” (começa com `eyJ...`)|
|**service_role**|Logo abaixo. **NUNCA exponha** essa chave      |
|**JWT Secret**  |Em “JWT Settings”, na mesma página             |

### ⏭️ ETAPA D — Subir o código no GitHub (5 min)

1. <https://github.com> → **+ → New repository** → nome `envlicense` → **Private** → Create
1. Na próxima tela, clique em **uploading an existing file**
1. Arraste **TODOS** os arquivos que te entreguei (`main.py`, `index.html`, `requirements.txt`, `config.js`, `vercel.json`, `render.yaml`, `README.md`, `.gitignore`)

> O arquivo `supabase_schema.sql` que estava na entrega anterior NÃO precisa subir — já aplicamos tudo via migração.

1. Commit changes (lá embaixo).

### ⏭️ ETAPA E — Backend no Render (10 min)

1. <https://render.com> → entre com GitHub → **New + → Web Service**
1. Conecte o repositório `envlicense`
1. Preencha:
- **Name**: `envlicense-api`
- **Runtime**: Python 3
- **Build Command**:
  
  ```
  apt-get update && apt-get install -y tesseract-ocr tesseract-ocr-por libgl1 || true && pip install -r requirements.txt
  ```
- **Start Command**:
  
  ```
  uvicorn main:app --host 0.0.0.0 --port $PORT --workers 2
  ```
- **Instance Type**: Free (testes) ou Starter $7/mês (produção)
1. Role até **Environment Variables** e cadastre:

|Key                   |Value                                              |
|----------------------|---------------------------------------------------|
|`SUPABASE_URL`        |`https://znduhfivfkykxbgdrgix.supabase.co`         |
|`SUPABASE_ANON_KEY`   |sua anon public                                    |
|`SUPABASE_SERVICE_KEY`|sua service_role (a secreta)                       |
|`SUPABASE_JWT_SECRET` |seu JWT Secret                                     |
|`OPENAI_API_KEY`      |sua chave da OpenAI (`sk-...`) — opcional          |
|`OPENAI_MODEL`        |`gpt-4o-mini`                                      |
|`ALLOWED_ORIGINS`     |`*` (provisório)                                   |
|`CRON_SECRET`         |uma senha aleatória forte (ex: `xyz9k2m8p4q7r3...`)|
|`PYTHON_VERSION`      |`3.11.9`                                           |

1. **Create Web Service**. Aguarde 5–10 min. Quando aparecer **Live** verde, copie a URL (ex: `https://envlicense-api.onrender.com`).
1. **Teste**: cole `https://envlicense-api.onrender.com/health` no navegador. Deve retornar `{"status":"ok","db":true,...}`.

### ⏭️ ETAPA F — Editar `config.js` com suas chaves (2 min)

1. GitHub → repo `envlicense` → arquivo `config.js` → ✏️ (Edit)
1. Substitua:

```js
window.ENVLICENSE_CFG = {
  SUPABASE_URL: "https://znduhfivfkykxbgdrgix.supabase.co",
  SUPABASE_ANON: "COLE_AQUI_SUA_ANON_PUBLIC",
  API_URL: "https://envlicense-api.onrender.com"
};
```

1. **Commit changes** lá embaixo.

### ⏭️ ETAPA G — Frontend no Vercel (3 min)

1. <https://vercel.com> → entre com GitHub → **Add New → Project**
1. Importe `envlicense`
1. Configure:
- **Framework Preset**: Other
- **Build Command**: deixe vazio
- **Output Directory**: digite `.` (um ponto)
1. **Deploy**. Em 30 segundos você terá `https://envlicense.vercel.app`.

### ⏭️ ETAPA H — Travar o CORS (1 min)

1. Volte ao Render → seu serviço `envlicense-api` → **Environment**
1. Edite `ALLOWED_ORIGINS` → cole sua URL do Vercel:
   
   ```
   https://envlicense.vercel.app
   ```
1. **Save Changes**. Reinicia automaticamente.

-----

## ✅ Teste final

1. Abra a URL do Vercel → faça login com o email/senha da Etapa B
1. **Painel Executivo**: deve mostrar os números reais — 827 ativos, 3 licenciados, etc.
1. **Mapa**: deve mostrar 2.283 pontos espalhados pela BA, RN, SE, CE
1. **Ativos**: lista paginada de todos os 2.283 poços (busca por código funciona)
1. **Licenças**: a licença `29.470` (Complexo Eólico Manacá)
1. **Condicionantes**: 9 condicionantes (7 pendentes + 2 atendidas)
1. **Assistente IA** (botão no canto): pergunte “Quantos ativos estão sem licença?”

-----

## 🔐 Os 5 papéis de usuário

|Papel         |Pode                                                     |
|--------------|---------------------------------------------------------|
|`admin`       |Tudo + gestão de usuários + auditoria + ler dados legados|
|`gestor`      |Criar/editar/excluir tudo, exceto users e audit logs     |
|`analista`    |Criar/editar entidades operacionais                      |
|`operacao`    |Leitura + marcar condicionantes                          |
|`visualizador`|Apenas leitura (modo campo)                              |

Para mudar o papel de qualquer usuário:

```sql
update public.profiles set role='gestor' where email='fulano@empresa.com';
```

-----

## 📊 Dados de comparação (antes / depois)

Os dados antigos **não foram apagados**. Estão nas tabelas `legacy_*` (acessíveis só por admin). Para conferir integridade:

```sql
select 'AGÊNCIAS' as item, count(*)::text as qtd from public.agencies
union all select 'CAMPOS', count(*)::text from public.fields
union all select 'ATIVOS (novos)', count(*)::text from public.assets
union all select 'ATIVOS (legacy)', count(*)::text from public.legacy_assets
union all select 'LICENÇAS (novas)', count(*)::text from public.licenses
union all select 'CONDICIONANTES (novas)', count(*)::text from public.conditionalities
union all select 'VÍNCULOS LIC-ATIVO', count(*)::text from public.license_assets;
```

-----

## 🗑️ Quando se sentir confortável: limpar legacy

Depois de algumas semanas usando o sistema novo, você pode apagar as tabelas `legacy_*`:

```sql
-- ⚠️ IRREVERSÍVEL. Só rode depois de validar que o novo sistema cobre tudo.
drop table if exists public.legacy_logs cascade;
drop table if exists public.legacy_simulations cascade;
drop table if exists public.legacy_documents cascade;
drop table if exists public.legacy_condition_completions cascade;
drop table if exists public.legacy_condition_asset_status cascade;
drop table if exists public.legacy_license_assets cascade;
drop table if exists public.legacy_conditions cascade;
drop table if exists public.legacy_licenses cascade;
drop table if exists public.legacy_assets cascade;
drop table if exists public.legacy_users cascade;
drop table if exists public.legacy_license_cost_reference cascade;
drop table if exists public.legacy_condition_cost_reference cascade;
drop table if exists public.legacy_cost_references cascade;
```

-----

## 🐛 Troubleshooting

|Problema                     |Solução                                                                          |
|-----------------------------|---------------------------------------------------------------------------------|
|Login fica girando           |Confira `SUPABASE_JWT_SECRET` no Render — é o “JWT Secret” da página Settings/API|
|Erro de CORS no console (F12)|Adicione a URL do Vercel em `ALLOWED_ORIGINS` no Render                          |
|Upload falha                 |Os buckets `licenses` e `evidences` foram criados? (Etapa A)                     |
|IA não extrai                |Adicione `OPENAI_API_KEY` no Render                                              |
|Dashboard zerado             |Confira no SQL Editor: `select * from public.v_compliance_kpis;`                 |
|Mapa em branco               |Confira: `select count(*) from assets where latitude is not null;`               |

-----

## 📞 Estrutura dos arquivos

```
envlicense/
├── main.py              # API FastAPI completa
├── requirements.txt     # Dependências Python
├── index.html           # SPA frontend
├── config.js            # Configuração de URLs
├── vercel.json          # Headers de segurança no Vercel
├── render.yaml          # Blueprint do Render (opcional)
├── .gitignore           # Ignora arquivos sensíveis
└── README.md            # Este arquivo
```

> O `supabase_schema.sql` não está mais aqui — a migração já foi feita diretamente no seu banco.

-----

**Versão:** 1.0.0
**Projeto Supabase:** `envlicense` (`znduhfivfkykxbgdrgix`)
