# Roadmap de execução — kb-mcp

**Disponibilidade:** 2h/dia (~14h/semana)
**Horizonte:** ~13 semanas até a v1 em uso real

Cada fase entrega algo que funciona sozinho. Se o cronograma apertar, para-se numa fronteira limpa.

---

## Semana 1, em paralelo a tudo: destravar o acesso

Antes de qualquer código, iniciar a conversa sobre **autorização para indexar o histórico do Mattermost**. É o maior risco do projeto e o único que não depende de você.

O que precisa ser acordado:
- Quais canais podem ser indexados (allowlist)
- Que o índice fica na infraestrutura da empresa
- Que autores serão pseudonimizados
- Termo de autorização do PEX, assinado pelo responsável do setor de suporte

Sem isso o projeto perde sua melhor fonte. Com isso, tudo o mais é execução.

---

## Fase 0 — Fundação (semanas 1–2)

- Repositório `kb-mcp`, Docker Compose (PostgreSQL + pgvector + Ollama)
- `composer require mcp/sdk` — confirmar estado atual e fixar versão
- Servidor MCP mínimo com uma ferramenta `listar_fontes` devolvendo dados fixos
- Conectar no Claude Code e ver a ferramenta aparecer

**Pronto quando:** você pergunta algo no Claude Code e sua ferramenta em PHP responde.

> Momento de maior valor por esforço do projeto. É quando deixa de ser ideia. Grave o vídeo aqui.

---

## Fase 1 — Mattermost (semanas 3–5) ← o coração

- Conector: API do Mattermost ou export, com allowlist de canais
- Agrupamento por **thread**, não por mensagem
- Heurística de "thread útil": tem resposta + sinal de resolução
- Pseudonimização de autores
- Geração de embeddings com Ollama, gravação em pgvector
- Ferramenta `buscar_threads_resolvidas`

**Pronto quando:** você faz no Claude Code uma pergunta que já foi respondida meses atrás e o sistema recupera a thread certa.

> Esta fase sozinha entrega a maior parte do valor. Se o cronograma apertar, é aqui que vale concentrar. As outras fontes são complemento.

---

## Fase 2 — Documentação e repositório (semanas 6–7)

- Conector de documentação estruturada (API ou export), chunking por seção
- Conector de repositório: markdown por seção; para código, priorizar READMEs, docstrings e comentários de módulo
- Ferramenta `buscar_conhecimento` unificando as três fontes
- Deduplicação — o mesmo conteúdo costuma aparecer em mais de uma fonte

**Pronto quando:** uma pergunta recupera trechos das três fontes, sem repetir a mesma informação.

---

## Fase 3 — Abstenção (semanas 8–9)

- Scoring e limiares de confiança (`alta`/`media`/`baixa`)
- Campo `observacao` com instrução embutida em baixa confiança
- Sinal `possivelmente_desatualizado`
- Ferramenta `escalar_para_humano`, postando no canal com contexto do que foi tentado
- Log de escalonamentos (sem conteúdo sensível)

**Pronto quando:** uma pergunta fora da base resulta em recusa explícita e oferta de escalonamento — não em invenção.

---

## Fase 4 — Evals (semanas 10–11)

- Dataset a partir de threads recentes, com **split temporal**
- As seis categorias, incluindo "fora da base" e "obsoleta"
- Runner em Python com Ollama local
- Relatório de métricas com os dois lados do tradeoff de abstenção
- CI (GitHub Actions)

**Pronto quando:** `make evals` imprime um placar, e você sabe qual é o false confident rate.

> Espere números ruins na primeira rodada — é o achado, não o fracasso. O ciclo de calibração dos limiares *é* o trabalho desta fase.

---

## Fase 5 — Uso real e medição (semanas 12–13)

- README: instalação, configuração, declaração de privacidade, resultados dos evals
- Onboarding do time de suporte
- Medição de campo: perguntas repetidas antes/depois, escalonamentos, **reincidência**
- Relatório de lacunas de documentação (subproduto)

**Pronto quando:** o time usa sem você por perto, e existe número de antes e depois.

---

## Fase 6 — Slack (futuro)

Trocar o conector do Mattermost pelo do Slack abre um mercado ordens de grandeza maior. O núcleo — índice, abstenção, evals, ferramentas — é o mesmo.

É aqui que deixa de ser projeto interno e vira produto.

---

## Riscos

| Risco | Mitigação |
|---|---|
| **Autorização do Mattermost negada ou lenta** | Iniciar na semana 1. Sem ela, fases 0 e 2 seguem (docs + repositório) e o projeto continua de pé, mais fraco |
| Nem todos que perguntam usam Claude Code | Medir na fase de diagnóstico do PEX 2. Se a cobertura for baixa, bot volta como fase alternativa |
| Poucas threads úteis no histórico | Medir antes de construir o conector completo: amostrar 100 threads e contar quantas passam na heurística |
| Base desatualizada gera resposta errada | Sinal de obsolescência é requisito de v1 |
| Limiar mal calibrado | Calibrar com dados na fase 4; errar para o lado da abstenção até haver confiança |
| SDK PHP experimental muda | Fixar versão; a superfície MCP em si é estável |
| Confusão entre trabalho e extensão | Termo explícito de trabalho voluntário; nunca executar em horário pago |
| Escopo crescer para bot | Registrado como fase alternativa, não como v1 |

---

## Sequência de dependências

```
autorização Mattermost ──────┐
                             ▼
fase 0 ──► fase 1 ──► fase 3 ──► fase 4 ──► fase 5
              │          ▲
              └► fase 2 ─┘
```

Fase 2 é paralelizável com a 1. Fase 3 depende de haver conteúdo indexado. Fase 4 depende da 3 — não dá para medir abstenção antes de existir abstenção.
