# Conceito — MCP server para sistemas de gestão brasileiros

**Primeiro alvo:** `nextfit-mcp` — servidor MCP para o NextFit (gestão de academias, estúdios e assessorias esportivas).
**Data de referência:** 07/08/2026

---

## A tese em uma frase

> Um MCP server transforma qualquer sistema de gestão em "IA-ready" sem tocar no sistema.

---

## Por que este problema

Os dados de 2026 são consistentes: o gargalo da adoção de IA não é capacidade de modelo, é **integração e confiabilidade**.

| Evidência | Fonte |
|---|---|
| 80–95% dos projetos de IA falham em ROI — por dado ruim e integração fraca, **não por qualidade de modelo** | Terminal X |
| 88% dos pilotos de agentes nunca chegam a produção | Forrester / Joget |
| **46%** citam integração com sistemas existentes como principal desafio de deploy | IBM |
| 70% apontam saídas não-determinísticas como barreira nº1 | Arcade.dev |

E no Brasil o gap é ainda mais gritante: **65,7% dos micro e pequenos empresários não usam nenhuma ferramenta de IA, mas 52,7% consideram a tecnologia essencial para competir em 2026** (InfinitePay). O bloqueio não é vontade — é que ninguém conectou a IA aos sistemas que eles já usam.

---

## Por que agora

2026 é o ano da adoção enterprise de MCP:

- **97 milhões** de downloads do SDK por mês
- **10.000+** servidores MCP públicos ativos
- **41%** das organizações já com MCP em produção limitada ou ampla (Stacklok)
- Gartner projeta **75%** dos fornecedores de API gateway com recursos MCP até o fim de 2026
- Protocolo doado por Anthropic à **Linux Foundation** (Agentic AI Foundation) em dez/2025, com OpenAI, AWS, Google, Microsoft e Cloudflare entre os membros

Ou seja: o padrão está estabelecido e o ecossistema está explodindo — mas **nenhum sistema de gestão brasileiro tem MCP server público**. Omie, Bling, Tiny, Conta Azul, NextFit: nicho aberto.

---

## O que é entregue

Um servidor que o cliente instala e conecta ao cliente de IA que ele já usa (Claude Desktop, Claude Code, Cursor). A partir daí ele pergunta em português sobre o próprio negócio:

> *"Quais alunos estão inadimplentes há mais de 30 dias?"*
> *"Quantas matrículas novas tivemos em julho comparado a junho?"*
> *"Quem não aparece para treinar há duas semanas?"*

O servidor recebe a chamada de ferramenta, consulta a API do NextFit, devolve dados estruturados. O modelo redige a resposta.

---

## Custo de inferência: zero

Este é o ponto que sustenta o modelo de negócio.

**Um MCP server não chama LLM nenhum.** Ele é um processo que expõe ferramentas. Quem consome token é o cliente de IA do outro lado — e quem paga é o cliente final, através da assinatura que ele já mantém.

| | Interface | Paga a inferência | Custo para o autor |
|---|---|---|---|
| **MCP server** | Claude Desktop / Cursor (já em uso) | O cliente, via assinatura | **R$ 0** |
| Chat próprio | Construída por você | Você, por requisição | Variável |

Além de eliminar o custo, isso vira argumento de venda: *"não cobro nada de IA — você usa a assinatura que já tem"*. E nenhum dado sensível do cliente trafega pela sua infraestrutura.

---

## O diferencial: evals

Praticamente todo MCP server publicado hoje é um wrapper sem testes. Publicar um **com suíte de avaliação e guardrails** ataca simultaneamente as duas maiores dores declaradas do mercado — integração e não-determinismo — e demonstra exatamente a competência que os relatórios de contratação dizem não encontrar.

Detalhes em [`02-evals.md`](02-evals.md).

---

## Contexto acadêmico

Este projeto é a espinha dorsal dos **PEX 2 a 6** (ver [`04-roadmap-pex.md`](04-roadmap-pex.md)). A organização parceira é uma assessoria esportiva em região metropolitana que usa o NextFit — o autor é aluno/atleta de lá, vínculo seguro para fins de extensão.

O roteiro oficial de ADS cita nominalmente "consultoria tecnológica para ajudar organizações a melhorar suas infraestruturas tecnológicas" e lista Segurança da Informação, Bancos de Dados e UX/UI entre as competências esperadas.

**ODS principal:** 9 — Indústria, Inovação e Infraestrutura.
**ODS secundário:** 8 — Trabalho Decente e Crescimento Econômico.

---

## Objetivos que este projeto atende ao mesmo tempo

1. **Acadêmico** — cumprir PEX 2 a 6 com organização real e ação tecnológica documentada.
2. **Portfólio** — repositório open source instalável, demonstrável em vídeo de 30 segundos, útil para terceiros. O formato que mais circula no LinkedIn.
3. **Comercial** — base para uma linha de MCP servers para o ecossistema de gestão brasileiro, num nicho hoje vazio e com timing de mercado raro.
