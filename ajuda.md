# Ajuda (Chat de Suporte)

## Regras do MVP

- Chat estilo mensageiro para contato rápido com suporte.
- Histórico somente em memória da sessão (zerado ao atualizar a página).
- Polling de novas mensagens a cada 3 segundos.
- Uma thread por sessão (encerrada no refresh).
- Mensagens limitadas a 500 caracteres; throttle de 1 mensagem a cada 2 segundos.

## Endpoint Unificado (n8n)

Webhook: `POST ${N8N_WEBHOOK_URL}/api-backend`

Ações suportadas:

1. `support.start`
   ```json
   {
     "action": "support.start",
     "payload": { "empresa_id": "UUID", "user_email": "joao@ex.com" }
   }
   ```
   Resposta:
   ```json
   {
     "thread_id": "SUP-7f2a1c",
     "welcome": "Oi! Sou do Suporte OMR. Como posso ajudar?"
   }
   ```

2. `support.send`
   ```json
   {
     "action": "support.send",
     "payload": { "thread_id": "SUP-7f2a1c", "message": "Minha instância não conecta." }
   }
   ```
   Resposta: `{ "ok": true, "ts": 1730567890 }`

3. `support.poll`
   ```json
   {
     "action": "support.poll",
     "payload": { "thread_id": "SUP-7f2a1c", "since_ts": 1730567880 }
   }
   ```
   Resposta:
   ```json
   {
     "messages": [
       { "role": "support", "text": "Pode tocar em Atualizar na aba Conexões.", "ts": 1730567891 },
       { "role": "support", "text": "Se aparecer QR, escaneie e me avise.", "ts": 1730567893 }
     ]
   }
   ```

4. `support.end`
   ```json
   {
     "action": "support.end",
     "payload": { "thread_id": "SUP-7f2a1c" }
   }
   ```
   Resposta: `{ "ok": true }`

Erros padrão: `400 invalid_payload`, `401/403 unauthorized`, `429 throttled`, `500 provider_error`.
Corpo sugerido: `{ "error": "...", "message": "..." }`.

## Fluxo no n8n

- Webhook → Switch `action`.
- `support.start`: gera `thread_id` (ex.: `SUP-${randomBase36}`), guarda em store temporário com TTL ~15 min, envia mensagem de boas-vindas e pode acionar canal humano (Evolution ou e-mail).
- `support.send`: valida tamanho, armazena no store e fan-out para suporte humano (WhatsApp Evolution `/message/sendText` ou e-mail).
- `support.poll`: retorna mensagens novas desde `since_ts`.
- `support.end`: remove dados da thread.
- Variáveis úteis: `SUPPORT_WHATSAPP_NUMBER`, `SUPPORT_EMAIL`, `EVOLUTION_BASE_URL`, `EVOLUTION_TOKEN`.

## Snippet Front (Vanilla JS)

```js
const API = `${N8N_WEBHOOK_URL}/api-backend`;
let threadId = null;
let lastTs = 0;
let sendingLock = false;
const chat = [];

async function api(action, payload) {
  const res = await fetch(API, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ action, payload })
  });
  if (!res.ok) {
    let m = 'Falha na comunicação.';
    try {
      const e = await res.json();
      m = e.message || m;
    } catch {}
    throw new Error(m);
  }
  return res.json();
}

async function startSupport() {
  const empresa_id = localStorage.getItem('empresa_id');
  const user_email = localStorage.getItem('user_email');
  const data = await api('support.start', { empresa_id, user_email });
  threadId = data.thread_id;
  if (data.welcome) {
    chat.push({ role: 'support', text: data.welcome, ts: Date.now() / 1000 | 0 });
    renderChat(chat);
  }
  pollLoop();
}

async function sendMsg(text) {
  if (sendingLock) return;
  if (!text || text.trim().length < 1) return;
  if (text.length > 500) return toast('Mensagem muito longa (máx. 500).');

  sendingLock = true;
  chat.push({ role: 'user', text, ts: Date.now() / 1000 | 0 });
  renderChat(chat);

  try {
    await api('support.send', { thread_id: threadId, message: text.trim() });
    setTimeout(() => (sendingLock = false), 2000);
  } catch (e) {
    sendingLock = false;
    toast(e.message || 'Não foi possível enviar.');
  }
}

async function pollOnce() {
  if (!threadId) return;
  try {
    const data = await api('support.poll', { thread_id: threadId, since_ts: lastTs });
    (data.messages || []).forEach((m) => {
      chat.push(m);
      if (m.ts && m.ts > lastTs) lastTs = m.ts;
    });
    if (data.messages?.length) renderChat(chat);
  } catch (e) {
    // silencioso; próxima rodada tenta novamente
  }
}

function pollLoop() {
  setInterval(pollOnce, 3000);
}

// chamar startSupport() ao abrir a aba Ajuda
```

## Contrato Simplificado (Opcional)

Para um fluxo mínimo, sempre responder `{ "output": "..." }`.

Request:
```
POST /webhook/api-backend
Content-Type: application/json
{
  "ACTION": "help",
  "ID": "UUID_DA_EMPRESA",
  "MENSAGEM": "texto da dúvida/problema"
}
```

Respostas:
- Sucesso 200: `{ "output": "Mensagem do suporte ou instrução objetiva." }`
- Erro: `{ "output": "Falha ao processar. Tente novamente em instantes." }`

## Critérios de Aceitação

- Iniciar chat retorna `thread_id` e mensagem de boas-vindas.
- Enviar mensagem assume feedback imediato no histórico e polling traz respostas em até 3 segundos.
- Atualizar a página limpa o histórico (memória local).
- Mensagens >500 caracteres ou dentro do intervalo de throttle emitem erro claro.

