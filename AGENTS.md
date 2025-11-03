# Diretrizes Gerais do Repositório

## Escopo
- Este arquivo cobre **todo o repositório**. Caso um diretório contenha um `AGENTS.md` próprio, siga as regras mais específicas descritas nele.
- Sempre leia os `AGENTS.md` dos subdiretórios antes de alterar arquivos neles, pois instruções mais profundas prevalecem.

## Resumo de Arquitetura
- OMR Studio é composto por:
  - **Painel Web** mobile-first desenvolvido com Vanilla JS, Tailwind CSS e Toastify.
  - **Supabase** responsável por Auth, Postgres com RLS, REST e funções auxiliares.
  - **Evolution API** para integração WhatsApp.
  - **n8n** para orquestração de automações.
- Detalhes adicionais:
  - Documentação aprofundada em `docs/dados.md`, `docs/estilo-visual.md`, `docs/test-drive.md`, `docs/conexoes-evolution.md` e `docs/ajuda.md`.
  - Estruturas de banco e reset em `db/reset.sql`.
  - Layout base do painel em `ui/layout.html`.
  - Referências visuais em `assets/images/mockup-1.png`, `assets/images/mockup-2.png`, `assets/images/mockup-3.png`.

## Convenções de Código
- Frontend deve permanecer em **Vanilla JS + Tailwind CSS + Toastify**.
- Padronize mensagens de erro conforme orientações do fluxo Supabase (texto claro, com retry quando aplicável).
- Respeite as políticas de **Row Level Security (RLS)** em todo acesso ao Supabase.
- Utilize apenas a **`anon key`** no frontend; nunca exponha credenciais `service_role`.

## Fluxo de Trabalho
- Sempre execute uma revisão de segurança verificando regras RLS, permissões e ausência de `service_role` em clientes.
- Valide os critérios de DoD descritos no `README.md` sempre que sua alteração puder afetá-los.
- Atualize a documentação relevante (`README.md`, arquivos em `docs/`, diagramas, etc.) quando houver mudanças de comportamento, fluxos ou arquitetura.

## Testes Recomendados
- Simule cenários críticos envolvendo Supabase: autenticação, CRUD com RLS e reset do banco via `db/reset.sql`.
- Valide integrações WhatsApp/Evolution API e fluxos automatizados pelo n8n sempre que impactados.
- Para frontend, teste o painel web com os mockups e garanta compatibilidade mobile-first.

## Mensagens de Commit e PR
- Prefira commits concisos no formato: `tipo: resumo breve` (ex.: `fix: corrige erro de login`).
- PRs devem conter título objetivo e corpo descrevendo mudanças, riscos e checagens relevantes (especialmente itens de segurança e DoD).

## Checklist de Revisão Rápida
- [ ] RLS respeitado? Nenhuma exposição de `service_role`?
- [ ] Mensagens de erro seguindo padrão do Supabase?
- [ ] Critérios de DoD afetados foram validados/testados?
- [ ] Documentação atualizada quando necessário?
- [ ] Testes essenciais (Supabase, Evolution API/n8n, frontend) executados?
