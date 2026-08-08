# Arquitetura — kb-mcp

## Visão geral

```
┌──────────────────┐
│  Claude Code     │  modelo do usuário — paga a inferência
│  (do usuário)    │
└────────┬─────────┘
         │ protocolo MCP (stdio ou HTTP)
┌────────▼─────────┐
│     kb-mcp       │  PHP 8.3 + mcp/sdk oficial
│  (servidor MCP)  │  zero token consumido
└────────┬─────────┘
         │ SQL + similaridade vetorial
┌────────▼─────────┐
│ PostgreSQL       │
│ + pgvector       │
└────────▲─────────┘
         │ escrita offline
┌────────┴─────────┐
│ Pipeline de      │  Python + Ollama (embeddings locais)
│ ingestão         │
└────────▲─────────┘
         │
   ┌─────┴──────┬──────────────┐
   │            │              │
Mattermost   Docs         Repositório
(histórico)  (Confluence/  (markdown +
             Notion)        código)
```

Duas metades independentes: **ingestão** (Python, offline, roda periodicamente) e **consulta** (PHP, online, responde ao MCP). Elas se comunicam só pelo banco — nada de acoplamento em tempo de execução.

---

## Stack

| Camada | Tecnologia | Motivo |
|---|---|---|
| Servidor MCP | PHP 8.3 + [`mcp/sdk`](https://github.com/modelcontextprotocol/php-sdk) | SDK oficial, mantido com a PHP Foundation e o time do Symfony; linguagem principal do autor |
| Índice | PostgreSQL + pgvector | Vetores e metadados no mesmo banco; sem serviço extra |
| Embeddings | Ollama local | Custo zero, dado não sai da infraestrutura |
| Ingestão | Python | Ecossistema de parsing e o repertório que o autor já tem |
| Testes | Pest (PHP) + pytest (Python) | |
| Distribuição | Docker Compose | Um comando para subir tudo |

⚠️ O SDK PHP é **experimental até o 1.0** — fixar versão no `composer.json` e acompanhar o changelog.

---

## As ferramentas MCP

| Ferramenta | Tipo | O que faz |
|---|---|---|
| `buscar_conhecimento` | leitura | Busca semântica nas três fontes; retorna trechos com fonte, URL, data e score |
| `buscar_threads_resolvidas` | leitura | Busca só no histórico do Mattermost, por perguntas similares já respondidas |
| `listar_fontes` | leitura | O que está indexado, volume por fonte e data da última atualização |
| `escalar_para_humano` | **escrita** | Posta a dúvida no canal de suporte, com o que já foi tentado |

`escalar_para_humano` é a única escrita, e é segura por natureza: publica uma mensagem, não muta estado de negócio. O pior caso é uma mensagem desnecessária num canal.

### Por que `buscar_threads_resolvidas` é separada

Poderia ser um filtro de `buscar_conhecimento`, mas separar tem duas vantagens. Primeiro, ferramentas separadas dão ao modelo um sinal mais claro de intenção — "já perguntaram isso antes?" é uma pergunta diferente de "o que a documentação diz?". Segundo, o retorno tem forma diferente: uma thread traz pergunta, resposta, quem respondeu e quando, e isso é contexto que a documentação não tem.

### Formato de retorno

Toda ferramenta de leitura devolve a mesma estrutura, e ela é o que torna a abstenção possível:

```json
{
  "confianca": "alta | media | baixa",
  "trechos": [
    {
      "conteudo": "...",
      "fonte": "mattermost | docs | repositorio",
      "referencia": "canal #suporte, 12/03/2026",
      "url": "https://...",
      "atualizado_em": "2026-03-12",
      "score": 0.87,
      "possivelmente_desatualizado": false
    }
  ],
  "observacao": "..."
}
```

Nunca retorna texto solto. Sem fonte, não há resposta — e sem resposta, o modelo não tem o que alucinar em cima.

---

## Conectores de ingestão

Cada conector normaliza sua fonte para o mesmo documento interno: `{conteudo, fonte, referencia, url, criado_em, atualizado_em, autor_pseudonimo}`.

### Mattermost — o conector que importa

O de maior valor e o mais trabalhoso. Considerações:

- **Acesso:** API do Mattermost com token de bot, ou export administrativo. Requer autorização formal.
- **Unidade de indexação é a thread, não a mensagem.** Uma mensagem isolada raramente é útil; a thread inteira tem pergunta e resposta.
- **Nem toda thread é conhecimento.** Filtrar: threads sem resposta, conversas sociais, discussões que não convergiram. Um heurístico inicial — thread com ao menos uma resposta e sinal de resolução (reação, "valeu", "resolvido") — já separa bem.
- **Pseudonimizar autores** na indexação. Quem respondeu importa como sinal de confiabilidade, mas não precisa ser identificável no índice.
- **Excluir canais privados e DMs.** Somente canais públicos de suporte, e somente os aprovados explicitamente.

### Documentação estruturada

API do Confluence/Notion, ou export. Chunking por seção, preservando a hierarquia de títulos — o caminho até o trecho é contexto valioso.

### Repositório

Markdown e código. Para markdown, chunking por seção. Para código, priorizar READMEs, docstrings e comentários de módulo em vez de indexar corpo de função — o objetivo é responder "como funciona", não "onde está a linha".

---

## Privacidade e LGPD

Threads internas contêm dados pessoais e possivelmente informação confidencial da empresa. Decisões:

- **Índice roda na infraestrutura da empresa.** Nada em nuvem de terceiro.
- **Embeddings gerados localmente** com Ollama — o conteúdo não é enviado a nenhuma API.
- **Pseudonimização de autores** na indexação.
- **Allowlist de canais**, nunca blocklist. Só entra o que foi aprovado.
- **Sem telemetria.** O servidor não envia nada para lugar nenhum, e isso precisa estar no README.
- **Log sem conteúdo** — registrar qual ferramenta foi chamada e o score obtido, nunca o trecho retornado.
- **Autorização formal antes da primeira indexação.** Não é etapa burocrática: é o que permite usar isso como PEX e dormir tranquilo.

---

## Decisões registradas

| Decisão | Alternativa descartada | Motivo |
|---|---|---|
| MCP consumido pelo Claude Code do usuário | Bot que responde no Mattermost | Custo de inferência zero; sem infra de bot; usuários já têm o cliente |
| Abstenção no retorno da ferramenta | Instrução em prompt de sistema | Não há prompt sob controle — o modelo é do usuário |
| Thread como unidade de indexação | Mensagem individual | Mensagem isolada não carrega pergunta + resposta |
| Ingestão em Python, servidor em PHP | Tudo numa linguagem | Melhor ferramenta em cada metade; acopladas só pelo banco |
| Embeddings locais (Ollama) | API de embeddings | Custo zero e dado não sai da empresa |
| Uma única escrita (`escalar_para_humano`) | Read-only estrito | O escalonamento é parte do fluxo pedido, e postar mensagem é seguro |
| Allowlist de canais | Indexar tudo e filtrar depois | Erro de allowlist não vaza dado; erro de blocklist vaza |
