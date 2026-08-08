# Conceito — MCP server de suporte técnico interno

**Nome de trabalho:** `kb-mcp`
**Data de referência:** 07/08/2026

---

## A dor

No Mattermost da empresa, usuários perguntam repetidamente coisas que já foram respondidas. Não é falta de conhecimento — é falta de recuperação. A resposta existe em três lugares:

1. **Documentação estruturada** (Confluence/Notion/similar) — organizada, mas pouco consultada
2. **Código e markdown do repositório** — a fonte mais precisa e a menos legível para quem não é do time
3. **Histórico do próprio Mattermost** — onde a pergunta já foi respondida, corretamente, por um humano

A terceira fonte é a mais valiosa e a única **completamente inacessível** hoje. Ninguém rola seis meses de thread procurando uma resposta; é mais rápido perguntar de novo. E assim a pergunta se repete, e alguém do time gasta tempo respondendo pela quinta vez.

---

## A solução

Um **MCP server** que consolida as três fontes numa base pesquisável e a expõe ao Claude Code que os próprios usuários já usam.

```
usuário no Claude Code
        │  "como configuro o webhook de faturamento?"
        ▼
   [kb-mcp] ──► busca semântica ──► docs + código + histórico Mattermost
        │
        ├─ achou com boa confiança → trechos + fonte + data
        └─ não achou / confiança baixa → sinaliza e oferece escalar
                                                │
                                                ▼
                                    canal de suporte (humano)
```

Quem pergunta resolve sozinho na maior parte dos casos. Quando a base não sustenta a resposta, o sistema **diz que não sabe** e encaminha — em vez de inventar.

---

## Por que isto funciona sem custo de inferência

O modelo é o **Claude Code do próprio usuário**, pago pela assinatura que ele já tem. O MCP server não chama LLM nenhum: recebe chamadas de ferramenta, consulta o índice, devolve trechos com metadados.

| | Quem paga a inferência | Custo do projeto |
|---|---|---|
| **Este desenho** | O usuário, via assinatura existente | **R$ 0** |
| Bot no Mattermost | Você, por mensagem respondida | Recorrente |

Os embeddings são gerados **localmente com Ollama**, offline, durante a indexação. Nenhum dado da empresa trafega para terceiros e não há fatura de API em lugar nenhum.

---

## O diferencial: saber quando não sabe

Este é o núcleo do projeto, não um detalhe de implementação. Ver [`02-abstencao.md`](02-abstencao.md).

Os números de mercado explicam por quê:

| Evidência | Fonte |
|---|---|
| Modelos frontier alucinam **4–9%** em fidelidade ao contexto, mesmo com RAG | Presenc AI |
| Abstenção calibrada reduz alucinações confiantes em **2–5×** | idem |
| *"deflection quality decays if content is not actively maintained"* | Digital Applied |
| Para suporte interno, deflexão só conta se o chamado **não reaparece** como escalonamento depois | idem |

Um sistema de suporte que responde errado com convicção é **pior que nenhum sistema** — porque destrói a confiança do time e ninguém volta a usar. A métrica que define o projeto não é quantas perguntas ele responde, é **quantas ele erra com confiança**.

---

## Expectativa honesta de resultado

RAG bem configurado resolve 55–72% de tickets tier-1 nos casos mais favoráveis, mas a faixa realista com base curada é **15–30% de deflexão**. É esse o número a prometer internamente.

O ganho secundário costuma ser subestimado: ao medir quais perguntas se repetem, o sistema revela **onde a documentação está falhando**. Isso vira backlog de documentação com prioridade baseada em evidência, não em opinião.

---

## Contexto acadêmico

Este projeto é a espinha dorsal dos **PEX 2 a 6** (ver [`05-roadmap-pex.md`](05-roadmap-pex.md)).

**Organização parceira:** a empresa onde o autor trabalha, **setor de suporte**. A vedação institucional é CNPJ próprio e *mesmo setor* onde se atua — suporte é outro setor, então o arranjo é permitido. Exige CNPJ, termo assinado, e deixar explícito no relatório que o trabalho **não é remunerado**.

**ODS principal:** 9 — Indústria, Inovação e Infraestrutura.

---

## Os três objetivos, atendidos ao mesmo tempo

1. **Acadêmico** — PEX 2 a 6 com organização real, dado real e impacto mensurável. O PEX 4 ("Coleta de Informações") encaixa literalmente: analisar o histórico do Mattermost *é* coleta e organização de informações.
2. **Portfólio** — o problema é universal (todo time de produto tem essa dor) e a solução é demonstrável em 30 segundos. Muito mais compartilhável que um conector de nicho.
3. **Produto** — a arquitetura é agnóstica de fonte. Trocar o conector do Mattermost pelo do Slack abre um mercado ordens de grandeza maior.
