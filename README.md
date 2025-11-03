Beleza, aqui vai o **README consolidado (v0.3)** + **SQL reset total** já com **trigger** que cria a instância automática no cadastro da empresa. Direto ao ponto, só o que fecha.

---

# 📘 README — OMR Studio (v0.3)

**Promessa:** coloque seu robô inteligente pra trabalhar no WhatsApp em **menos de 5 minutos**.

## 1) O que é

O **OMR Studio** é um painel web mobile-first para donos de negócio configurarem a empresa (nome, tom de voz, horários, produtos/serviços e FAQs) e operarem a conexão WhatsApp. Backend no **Supabase** (Postgres + Auth + REST). Automação via **n8n**. Mensageria via **Evolution API**.

## 2) Telas & Fluxos

### Telas

* **Login** (Supabase Auth — password)
* **Cadastro** (modal: `empresa_nome`, `email`, `whatsapp`, `senha`, `confirmar_senha`, termos)
* **Painel**

  * **Informações** (dados da empresa + persona `josi|clara`)
  * **Produtos-Serviços** (CRUD HARD DELETE)
  * **FAQ** (CRUD HARD DELETE)
  * **Test-Drive** (chat simulado)
  * **Conexões** (QR/Status/Config)
  * **Ajuda** (FAQ estático — a definir)

### Fluxos cravados

**Login**

* `POST /auth/v1/token?grant_type=password` com `{ email, password }`
* Salvar sessão local. Logout limpa tudo.
* Erros curtos: *Credenciais inválidas*, *Conta inativa*, *Muitas tentativas*, *Servidor indisponível*.

**Esqueci a senha**

* `supabase.auth.resetPasswordForEmail(email)` → fluxo de redefinição.

**Cadastro (auto-login + criação automática)**

1. `signUp({ email, password })` (mín. 6, sem exigir número/símbolo).
2. Auto-login (`signInWithPassword`).
3. **Cria empresa**: `POST /rest/v1/empresas` → retorna `empresa_id`.
4. **Trigger no DB cria instância** (`instancias`) já ligada à empresa com:

   * `status='pending_qr'`
   * `evolution_instance='omr_' + uuidEmpresaSemHifen`
5. Cria/atualiza **perfil do usuário** (`usuarios`) com `empresa_id`.
6. Guarda `empresa_id` no `localStorage`.

**Informações (Salvar manual)**

* `GET /rest/v1/empresas?owner_user=eq.${uid}`
* `PATCH /rest/v1/empresas?id=eq.${empresa_id}`
* Em erro: toast “Falhou. Tente novamente”.

**Produtos-Serviços (HARD DELETE)**

* `GET /rest/v1/produtos?empresa_id=eq.${empresa_id}`
* `POST /rest/v1/produtos`
* `PATCH /rest/v1/produtos?id=eq.${id}`
* `DELETE /rest/v1/produtos?id=eq.${id}`

**FAQ (HARD DELETE)**

* Mesma estrutura de Produtos.

**Test-Drive (simulador real via n8n)**

* **UX:** chat estilo GPT, histórico só em memória, sugestões fixas, persona no topo (`josi|clara`) sem salvar.
* **Endpoint:** `POST ${N8N_WEBHOOK_URL}/test-drive`
* **Payload:** `{ "id": "<empresa_id>", "persona": "josi|clara", "message": "<texto>" }`
* **200 OK:** `{ "output": "<resposta>", "trace": { "tokens": <int>, "elapsed_ms": <int>, "model": "<nome>" } }`
* **Erros:** `400/401/403/408/429/500` com `{ "error": "...", "message": "..." }`
* **Alvos:** resposta ≤ **3s** (timeout 8s).
* **n8n (resumo):** Webhook → valida → busca `empresas/produtos/faqs` → monta prompt (temperatura 0.3, max 400) → responde.

**Conexões (Evolution)**

* **Estados UI:** *Desconectado* → *Conectando (QR)* → *Conectado* → *Erro*.
* **Polling:** 5s em `status`.
* **Proxy unificado (recomendado) via n8n:** `POST ${N8N_WEBHOOK_URL}/evolution`

  * Ações:

    * `status` → retorna `{ "status": "connected|qrcode|offline|error" }`
    * `connect` → retorna `{ "qr": { "code": "...", "pairingCode": "..." } }`
    * `logout` → `{ "ok": true }`
    * `get_settings` → flags atuais
    * `set_settings` → salva flags `{ reject_call, groups_ignore, always_online, read_messages, sync_full_history, msg_call }`
  * O proxy conversa com a Evolution (`/instance/connectionState`, `/instance/connect`, `/instance/logout`, `/settings/get`, `/settings/set`).
* **Aba Conexões — ações:**

  * **Atualizar conexão:** chama `connect` (mostra QR se necessário) e inicia polling 5s até `connected`.
  * **Desconectar:** `logout`.
  * **Salvar configurações:** `set_settings`.
  * **Mini-log:** opcional via `public.mensagens` (`tipo="evo_status" | "evo_settings_set"`).

## 3) Regras & Segurança

* **RLS ON** por `empresa_id` (herdado por tabelas filhas).
* **Front usa `anon key`**. Nunca usar `service_role` no front.
* **Nada de senha em GET**.
* **HARD DELETE** para produtos/FAQ (com confirmação).

## 4) Variáveis de ambiente

**Front:**

* `SUPABASE_URL`
* `SUPABASE_ANON_KEY`

**n8n:**

* `N8N_WEBHOOK_URL`
* `OPENAI_API_KEY` (ou provedor escolhido)

**Evolution:**

* `EVOLUTION_BASE_URL`
* `EVOLUTION_TOKEN`

## 5) Critérios de aceitação

* [ ] Cadastro → auto-login → **empresa criada** → **instância criada automaticamente** (`pending_qr`, `evolution_instance=omr_<uuidsemhifen>`).
* [ ] Painel Informações salva e reflete no GET.
* [ ] Produtos/FAQ: **criar/editar/deletar** (hard) com RLS bloqueando acesso cruzado.
* [ ] Test-Drive responde ≤ **3s** com `trace.elapsed_ms`.
* [ ] Conexões: **Atualizar** mostra QR quando necessário e vira **Conectado**; **Desconectar** funciona; **Salvar configurações** persiste.
* [ ] Deploy no cPanel sem erro de MIME.

## 6) Contratos REST (resumo)

**Headers (sempre):**

```
apikey: ${SUPABASE_ANON_KEY}
Authorization: Bearer ${ACCESS_TOKEN}
Content-Type: application/json
```

* Empresas

  * `GET /rest/v1/empresas?owner_user=eq.${uid}`
  * `PATCH /rest/v1/empresas?id=eq.${empresa_id}`
* Produtos

  * `GET /rest/v1/produtos?empresa_id=eq.${empresa_id}`
  * `POST /rest/v1/produtos`
  * `PATCH /rest/v1/produtos?id=eq.${id}`
  * `DELETE /rest/v1/produtos?id=eq.${id}`
* FAQs → igual Produtos
* Test-Drive (n8n)

  * `POST ${N8N_WEBHOOK_URL}/test-drive`
* Evolution (via proxy n8n)

  * `POST ${N8N_WEBHOOK_URL}/evolution` com `{ action, instance, ... }`

## 7) Termos & Privacidade

* Termos: `https://omelhorrobo.site/termos`
* Privacidade: `https://omelhorrobo.site/privacidade`

