# Conexões (Evolution API)

## Estados e Polling

- Estados exibidos na UI: **Desconectado → Conectando (QR) → Conectado → Erro**.
- Polling de status a cada 5 segundos (ajustar conforme SLA).

## Proxy Recomendado (n8n)

- Endpoint: `POST ${N8N_WEBHOOK_URL}/evolution`.
- Payload padrão: `{ "action": "<acao>", "instance": "<instancia>", ... }`.
- Ações esperadas e respostas:
  - `status` → `{ "status": "connected" | "qrcode" | "offline" | "error" }`.
  - `connect` → `{ "qr": { "code": "...", "pairingCode": "..." } }`.
  - `logout` → `{ "ok": true }`.
  - `get_settings` → `{ "settings": { ...flags } }`.
  - `set_settings` → `{ "ok": true, "settings": { ... } }`.
- O proxy comunica com a Evolution API utilizando `EVOLUTION_BASE_URL` e `EVOLUTION_TOKEN` nas rotas:
  - `/instance/connectionState`
  - `/instance/connect`
  - `/instance/logout`
  - `/settings/get`
  - `/settings/set`

## UI da Aba Conexões

- **Atualizar conexão**:
  - Chama `connect`.
  - Exibe QR (imagem ou texto/base64) quando `status === 'qrcode'`.
  - Inicia polling até retornar `connected`.
- **Desconectar**:
  - Chama `logout` e atualiza a tela para estado desconectado.
- **Salvar configurações**:
  - `set_settings` com flags como `reject_call`, `groups_ignore`, `always_online`, `read_messages`, `sync_full_history`, `msg_call`.
- **Mini-log opcional**:
  - Usar `public.mensagens` com `tipo = 'evo_status'` ou `'evo_settings_set'` para registrar eventos relevantes (sem excesso).

## Critérios de Aceitação

- Atualizar conexão mostra QR quando necessário e evolui para **Conectado** após leitura bem-sucedida.
- Deslogar altera o estado para **Desconectado** e invalida polling.
- Salvar configurações retorna confirmação e reflete na Evolution.
- Polling a cada 5 segundos mantém status consistente; falhas mostram mensagem amigável e orientam retry.

