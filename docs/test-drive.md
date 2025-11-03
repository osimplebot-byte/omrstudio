# Test-Drive (Simulador)

## Objetivo

Reproduzir dentro do painel a experiência de atendimento do WhatsApp sem persistir histórico. Serve para validar contexto da empresa, produtos, FAQs e persona antes da conexão real.

## UX e Regras

- Chat estilo GPT.
- Histórico somente em memória (limpo ao recarregar a página).
- Sugestões fixas no front (ex.: "Quais produtos?", "Qual horário?", "Existe suporte humano?").
- Persona alternável no topo (`josi` acolhedora, `clara` objetiva) sem salvar no banco.
- Tempo-alvo de resposta: ~3 segundos (timeout 8 segundos).
- Mensagens de erro amigáveis e possibilidade de retry em caso de timeout.

## Contrato HTTP (Front → n8n)

- **Endpoint**: `POST ${N8N_WEBHOOK_URL}/test-drive`
- **Headers**:
  ```
  Content-Type: application/json
  Authorization: Bearer ${ACCESS_TOKEN}   // opcional; habilitar se quiser validar token no n8n
  x-supabase-project: ${SUPABASE_URL}     // opcional; somente se necessário
  ```
- **Payload**:
  ```json
  {
    "id": "UUID_DA_EMPRESA",
    "persona": "josi",
    "message": "Quais serviços vocês oferecem?"
  }
  ```
  - `id` é o `empresa_id` retornado no cadastro e salvo localmente.
- **Resposta 200**:
  ```json
  {
    "output": "Oferecemos corte, pintura e escova! Quer agendar?",
    "trace": {
      "tokens": 512,
      "elapsed_ms": 930,
      "model": "gpt-4.2-mini"
    }
  }
  ```
- **Erros**:
  - `400` payload inválido (falta `id` ou `message`).
  - `401/403` sem autorização (caso valide token no n8n).
  - `408` timeout do modelo.
  - `429` limite de uso.
  - `500` falha inesperada.
  - Corpo padrão:
    ```json
    { "error": "timeout", "message": "IA indisponível, tente novamente em alguns segundos." }
    ```

## Fluxo no n8n

1. Webhook (`POST /test-drive`).
2. Validação (`id`, `persona` ∈ `['josi','clara']`, `message` 1–400 caracteres).
3. Consulta Supabase (`empresas`, `produtos`, `faqs` por `empresa_id`).
4. Montagem do prompt:
   ```json
   {
     "persona": "josi",
     "empresa": { "nome": "...", "tom": "josi", "horario": "..." },
     "produtos": [{ "nome": "Corte", "preco": 120.00 }],
     "faqs": [{ "pergunta": "Atendem domingo?", "resposta": "Não" }],
     "usuario_msg": "Quais serviços vocês oferecem?"
   }
   ```
5. Chamada de LLM (temperature 0.3, max_tokens 400).
6. Mapeamento para `{ output, trace: { tokens, elapsed_ms, model } }`.
7. (Opcional) Log em `public.mensagens` com `tipo = 'test_drive'` e `detalhe = { q, a }`.
8. Resposta JSON.

## Template de Prompt

```
SISTEMA:
Você é {persona} (tom: Josi = acolhedora; Clara = objetiva).
Responda apenas com base nos dados da empresa. Se faltar dado, encaminhe para atendimento humano.

CONTEXTO:
Empresa: {empresa.nome}
Horário: {empresa.horario}
Produtos/Serviços: {lista produto:preço}
FAQs: {pergunta:resposta}

USUÁRIO:
{usuario_msg}

REGRAS:
- Seja simples e direto.
- Não invente preço se não existir.
- Ofereça encaminhamento humano apenas se necessário.
```

## Snippet Front (Vanilla JS)

```js
const WEBHOOK = `${N8N_WEBHOOK_URL}/test-drive`;

async function sendTestDrive({ empresa_id, persona, message }) {
  const res = await fetch(WEBHOOK, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ id: empresa_id, persona, message })
  });
  if (!res.ok) {
    let msg = 'IA indisponível, tente novamente.';
    try {
      const e = await res.json();
      msg = e.message || msg;
    } catch {}
    throw new Error(msg);
  }
  return res.json();
}

const history = [];
async function onSend(msg) {
  try {
    const { output, trace } = await sendTestDrive({
      empresa_id: localStorage.getItem('empresa_id'),
      persona: document.querySelector('#persona').value,
      message: msg
    });
    history.push({ role: 'user', content: msg });
    history.push({ role: 'agent', content: output });
    renderChat(history);
  } catch (e) {
    toastError(e.message);
  }
}
```

## Critérios de Aceitação

- Enviar mensagem retorna `output` em ~3 segundos (monitorado por `trace.elapsed_ms`).
- Trocar persona ajusta o tom da resposta sem recarregar a página.
- Timeout do n8n exibe mensagem amigável e permite tentar novamente.
- Nenhum histórico é salvo; recarregar limpa a conversa.

