# Dados do Produto (OMR Studio)

## Campos Principais

- `empresa_nome` (obrigatório).
- `empresa_tipo` (texto curto).
- `horario_funcionamento` (texto livre).
- `contatos_extras` (texto livre).
- `endereco` (texto livre).
- `observacoes` (textarea).
- `persona` (`josi` acolhedora ou `clara` objetiva).
- Produtos (lista):
  - `nome` (obrigatório).
  - `descricao` (opcional, curta).
  - `preco` (numérico ou texto formatado).
- FAQs (lista):
  - `pergunta` (obrigatória).
  - `resposta` (obrigatória).

## Fluxos de Dados no Painel

### Informações

- `GET /rest/v1/empresas?owner_user=eq.${uid}` recupera dados da empresa.
- `PATCH /rest/v1/empresas?id=eq.${empresa_id}` atualiza dados. Em erro, exibir toast e permitir retry.

### Produtos

- `GET /rest/v1/produtos?empresa_id=eq.${empresa_id}` lista produtos.
- `POST /rest/v1/produtos` adiciona novo produto.
- `PATCH /rest/v1/produtos?id=eq.${produto_id}` edita produto.
- `DELETE /rest/v1/produtos?id=eq.${produto_id}` remove (hard delete).

### FAQs

- Mesmas rotas de Produtos (`/rest/v1/faqs`).

## Headers Padrão (Supabase REST)

```
apikey: ${SUPABASE_ANON_KEY}
Authorization: Bearer ${ACCESS_TOKEN}
Content-Type: application/json
```

## Segurança e RLS

- RLS ativo em todas as tabelas.
- Policies garantem que o usuário autenticado só acesse dados ligados à própria empresa (`empresa_id`).
- Nunca utilizar `service_role` no frontend; usar apenas `anon key`.

## Variáveis de Ambiente Importantes

- `SUPABASE_URL`, `SUPABASE_ANON_KEY`.
- `N8N_WEBHOOK_URL`.
- `EVOLUTION_BASE_URL`, `EVOLUTION_TOKEN`.
- `OPENAI_API_KEY` (ou provider LLM usado no n8n).

## Referências

- Script SQL completo: `db/reset.sql`.
- Test-Drive: `docs/test-drive.md`.
- Conexões (Evolution): `docs/conexoes-evolution.md`.
- Ajuda (Suporte): `docs/ajuda.md`.
