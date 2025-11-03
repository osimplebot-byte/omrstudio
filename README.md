# OMR Studio — Guia Principal

Bem-vindo à documentação do OMR Studio. Este README resume as funções do produto e aponta para as seções especializadas. Use-o como índice para navegar entre as etapas do painel e as integrações.

## Visão Geral do Produto

- Painel web mobile-first para configurar empresa, persona, catálogo e FAQs.
- Integração com WhatsApp via Evolution API.
- Backend fornecido pelo Supabase (Auth, Postgres, REST com RLS).
- Orquestração e automações via n8n.
- Frontend atual em Vanilla JS + Tailwind CSS + Toastify.
- Deploy oficial: https://omelhorrobo.site/.

## Fluxos Essenciais

- **Login / Esqueci Senha** — Autenticação Supabase; mensagens de erro padronizadas.
- **Cadastro com Auto-Login** — Cria empresa, vincula usuário, gera instância de conexão automaticamente.
- **Painel** — Subabas de Informações, Produtos, FAQ, Test-Drive, Conexões e Ajuda.
- **Deploy** — Build publicado no cPanel; conferir MIME types.

## Documentação por Etapa

- **Dados e Contratos Supabase** → `docs/dados.md`
- **Guia de Estilo Visual** → `docs/estilo-visual.md`
- **Simulador Test-Drive** (chat IA) → `docs/test-drive.md`
- **Integração com Evolution API** → `docs/conexoes-evolution.md`
- **Chat de Suporte (Ajuda)** → `docs/ajuda.md`
- **Reset do Banco / Schema** → `db/reset.sql`
- **Protótipo de Layout Web** → `ui/layout.html`

## Segurança e Riscos

- RLS ativo por `empresa_id`; testar casos positivo/negativo.
- Usar somente `anon key` no frontend; nunca expor `service_role`.
- Não trafegar senha via GET; tratar erros com mensagens amigáveis e retry.
- Hard delete em Produtos/FAQs exige confirmação (sem histórico).

## Checkpoints (DoD)

- Login com erros padronizados.
- Cadastro → auto-login → empresa criada → instância `pending_qr`.
- Painel Informações salva corretamente.
- Produtos/FAQs CRUD completo com RLS.
- Test-Drive responde em ~3s (`trace.elapsed_ms`).
- Conexões atualizam QR/estado, desconectam e persistem configs.
- Deploy no cPanel sem falha de MIME.

## Recursos Visuais

- **Mockups da Interface** → `assets/images/mockup-1.png`, `assets/images/mockup-2.png`, `assets/images/mockup-3.png`

## Proximos Passos

- Evoluções futuras devem atualizar as seções específicas.
- Cada doc adicional deve ser referenciada aqui para manter o índice atualizado.
