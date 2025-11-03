# 🧠 agents.md — Constituição Raiz do OMR Studio

## Missão
Fazer o **painel do OMR Studio** funcionar de ponta a ponta — do cadastro ao robô ativo no WhatsApp — **sem depender de servidores intermediários complexos**, mantendo o fluxo direto:  
**Frontend → Supabase → N8N → Evolution API → WhatsApp.**

A IA e os agentes humanos devem cooperar para entregar um sistema simples, funcional e autossuficiente, acessível a qualquer usuário via celular.

---

## Não Fazer
- Não criar dependências desnecessárias (Node, frameworks pesados, SDKs extras).  
- Não guardar **chaves, tokens ou secrets** no front-end.  
- Não alterar dados críticos sem passar pelo fluxo de API documentado (Supabase REST ou N8N).  
- Não inventar features antes da validação com o time (João).  
- Não complicar o que pode ser resolvido com lógica simples de estado ou fetch.

---

## Padrões de Código
- **Arquitetura modular:** cada função faz uma coisa só.  
- **JS puro (ES6)** e **TailwindCSS** como base visual.  
- Funções assíncronas sempre com `try/catch` e `console.warn` para erros previsíveis.  
- Nomeação clara: `dados.*`, `instancia.*`, `chat.*`, `auth.*`.  
- Logs visíveis no console (modo dev) e limpos no deploy.  
- Requisições REST documentadas no formato:
  ```js
  fetch(`${SUPABASE_URL}/rest/v1/tabela`, {
    method: 'POST',
    headers: { apikey: SUPABASE_KEY, 'Content-Type': 'application/json' },
    body: JSON.stringify(payload)
  })
````

---

## Segurança

* **Chaves e secrets** ficam apenas no N8N e Supabase, nunca no front.
* RLS (Row Level Security) ativado em produção.
* Tokens de sessão apenas em `localStorage` temporário.
* Webhooks autenticados com header secreto entre N8N e Evolution.

---

## UX / UI

* Filosofia **mobile-first**.
* Cada tela deve ter **3 estados mínimos**:

  * `loading`: exibe spinner ou placeholder.
  * `empty`: exibe instrução amigável.
  * `error`: exibe mensagem curta e clara.
* Componentes:

  * Tabs inferiores fixas (`Dados`, `Test-Drive`, `Conexão`, `Help`).
  * Feedbacks com Toastify.
  * Temas: claro e escuro com contraste legível.

---

## Comunicação entre Agentes

* O **frontend** envia payloads claros (JSON curto).
* O **N8N** valida, interpreta e repassa mensagens.
* O **Supabase** é a fonte única de verdade (dados oficiais).
* O **Evolution API** envia e recebe mensagens do WhatsApp.
* Logs importantes devem ser registrados no console e no painel de controle do N8N.

---

## Saídas da IA

* Sempre responder em **Markdown completo**, incluindo blocos de código executáveis.
* Se houver dúvida, **assumir o caso mais simples** (não criar suposições desnecessárias).
* Explicar brevemente o raciocínio técnico antes do código, quando útil.
* Preferir soluções simples que rodem direto no navegador.

---

## Ciclo de Trabalho

1. **Brainstorm IA + Humano** → definir o que será feito.
2. **Documentar** (mini plano no formato do `agents.md`).
3. **Codar** módulos isolados → testar no console.
4. **Integrar** via N8N / Supabase.
5. **Deploy** → validar UX real no celular.

---

## Juramento do Agente

> “Antes de otimizar, primeiro faço funcionar.
> Antes de escalar, primeiro simplifico.
> Antes de codar, primeiro entendo.
> E antes de perguntar, primeiro testo.”

---

Versão: **1.0 – Constituição Base do OMR Studio**
Responsável: João (Head de Produto e Orquestrador)
IA Signatária: SeverinoIA
Data: 02/11/2025