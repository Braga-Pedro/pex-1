# Evals — medindo o que importa

---

## A vantagem deste projeto

**O histórico do Mattermost é simultaneamente a base de conhecimento e o conjunto de avaliação.**

Cada thread resolvida traz uma pergunta real, feita por um usuário real, e uma resposta correta validada por um humano. Isso é um dataset de avaliação que normalmente custaria semanas para construir — e aqui já existe.

---

## ⚠️ Split temporal é obrigatório

Indexar threads **antigas**, avaliar com as **recentes**.

Um split aleatório coloca a thread de teste dentro do índice, e o sistema "acerta" recuperando literalmente a resposta que deveria descobrir. A métrica fica excelente e não significa nada.

```
│──────── indexado ────────│──── avaliação ────│
                           ▲
                    corte temporal
```

Além de evitar o vazamento, o split temporal simula a condição real de operação: responder perguntas novas com conhecimento acumulado. E revela um efeito que o split aleatório esconde — a degradação natural quando o assunto perguntado é mais recente que a base.

---

## Métricas

| Métrica | O que mede | Meta |
|---|---|---|
| **False confident rate** | Respondeu com confiança `alta` e errou | **< 2%** |
| Answer accuracy | Quando responde, acerta | > 80% |
| Abstention precision | Quando abstém, era mesmo desconhecido | > 85% |
| Abstention recall | Do que era desconhecido, quanto foi corretamente recusado | > 90% |
| Citation accuracy | A citação realmente sustenta a afirmação | > 90% |
| Retrieval recall@5 | O trecho certo apareceu entre os 5 primeiros | > 90% |

**A primeira governa o projeto.** As outras podem piorar um pouco em nome dela.

*Abstention precision* e *recall* medem lados opostos do mesmo tradeoff e precisam ser reportadas juntas — ver [`02-abstencao.md`](02-abstencao.md). Reportar só uma esconde metade do comportamento.

---

## Categorias do dataset

| Categoria | Exemplo | O que valida |
|---|---|---|
| **Direta** | "Qual o endpoint de criação de fatura?" | Caso feliz |
| **Coloquial** | "como q faz pra gerar cobrança msm" | Busca semântica funciona com linguagem real |
| **Ambígua** | "tá dando erro no login" | Pede esclarecimento em vez de chutar |
| **Multi-fonte** | "Como configuro X e onde vejo o log?" | Combina documentação e histórico |
| **Fora da base** | Pergunta sobre módulo não documentado | **Deve abster** |
| **Obsoleta** | Assunto cuja resposta mudou | Sinaliza `possivelmente_desatualizado` |

As duas últimas são as que ninguém escreve e as que mais importam. "Fora da base" mede se o sistema sabe reconhecer o próprio limite; "obsoleta" mede se ele percebe quando o conhecimento envelheceu.

Para construir a categoria "fora da base": perguntas sobre partes reais do sistema que comprovadamente não estão indexadas. É mais honesto que inventar perguntas absurdas — um sistema pode recusar "qual a capital da Mongólia?" e ainda assim inventar sobre um módulo interno.

---

## Como rodar

```
dataset (threads recentes) ──► Ollama local ──► kb-mcp ──► índice (threads antigas)
                                    │
                                    ▼
                            relatório de métricas
```

Ollama suporta tool calling nativamente. Modelos com boa capacidade em 2026: **Qwen3, Hermes 4, Mistral, Command-R**.

⚠️ Verifique o selo **"Tools"** na página do modelo na biblioteca do Ollama antes de escolher — function calling é a área mais irregular entre modelos locais; alguns declaram suporte e produzem JSON inválido.

Custo zero, roda em cada commit.

### A limitação a declarar

O modelo local **não é** o Claude Code que os usuários vão usar. Os evals locais medem principalmente a **robustez do desenho** — se um modelo menor consegue abster corretamente com os sinais que a ferramenta devolve, um modelo maior consegue também. O inverso não vale.

Estratégia: modelo local em cada commit (pega regressões, grátis), validação periódica contra o modelo real antes de cada release. Registrar as duas medições separadamente.

Declarar essa limitação em vez de escondê-la é o que dá credibilidade ao número.

---

## Medindo groundedness

A técnica que o estado da arte aponta é **claim-level entailment**: decompor a resposta em afirmações atômicas e verificar cada uma contra os trechos recuperados. O resultado é uma taxa por afirmação, não um score único e opaco — permite apontar *qual* frase não tem suporte.

Implementação pragmática para a v1: para cada afirmação verificável na resposta, checar se existe trecho recuperado que a sustente. Começar com verificação manual sobre uma amostra de 30 respostas, automatizar depois que o padrão de erro estiver claro.

Não vale automatizar isso antes de entender o que se está medindo.

---

## Métrica de campo — a que a empresa vai perguntar

Os evals medem qualidade técnica. A empresa vai querer saber se **diminuiu o trabalho**:

| Indicador | Como medir |
|---|---|
| Perguntas repetidas por semana | Contagem no canal, antes e depois |
| Escalonamentos via `escalar_para_humano` | Log do próprio servidor |
| Reincidência | O que foi escalado voltou depois? |

⚠️ Deflexão só conta se o chamado **não reaparece** como escalonamento depois. Contar deflexão sem medir reincidência produz um número bonito e falso.

Expectativa honesta a comunicar: **15–30%** de redução. Prometer 70% é criar decepção garantida.

---

## Efeito colateral valioso

Ao registrar quais perguntas foram escaladas, o sistema produz uma lista priorizada de **lacunas de documentação** — baseada em demanda real, não em opinião sobre o que "deveria" estar documentado.

Esse subproduto pode acabar valendo mais para a empresa que a deflexão em si, e é um resultado excelente para o relatório do PEX.
