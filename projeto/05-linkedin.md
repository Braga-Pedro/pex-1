# Pauta de conteúdo — LinkedIn

O objetivo não é "postar sobre o projeto", é **publicar achados**. Post que ensina algo circula; post que anuncia progresso pessoal, não.

Regra geral: cada post precisa ter um número, um erro ou uma decisão contra-intuitiva. Se não tiver nenhum dos três, não é post — é update.

---

## Por fase

| # | Quando | Ângulo | Por que funciona |
|---|---|---|---|
| 1 | Fim da fase 0 | **"Construí um MCP server em PHP — sim, tem SDK oficial"** | A maioria dos devs PHP acha que IA não é território deles. Mostrar o SDK oficial mantido com a PHP Foundation e o time do Symfony é notícia para muita gente. Vídeo de 30s da ferramenta respondendo no Claude Desktop. |
| 2 | Fim da fase 1 | **"Read-only por design"** | Decisão de arquitetura contra-intuitiva (menos funcionalidade de propósito) com justificativa forte: uma IA que pode cancelar contratos vai cancelar o errado um dia. Rende discussão. |
| 3 | Fim da fase 3 | **"Escrevi evals para meu MCP server e descobri que X% das perguntas chamavam a ferramenta errada"** | O post mais forte da série. Número real, achado desconfortável, e ataca a dor nº1 declarada do mercado. Publique o número mesmo se for feio — principalmente se for feio. |
| 4 | Fase 3 | **"Como escrever a descrição de uma ferramenta MCP"** | Conteúdo de referência, com antes/depois e o efeito medido em accuracy. Alto potencial de salvamento. |
| 5 | Fase 3 | **"Rodando evals de graça com Ollama"** | Resolve uma dor concreta (custo de token em CI) e conecta com quem se preocupa com custo de IA — que é a maioria. |
| 6 | Fase 4 | **"O NextFit tem 70 relatórios. Ainda assim ninguém acha o que precisa."** | Insight de produto: o problema não é falta de dado, é interface. Fala com fundadores e PMs, não só devs. |
| 7 | Fase 5 | **Lançamento** | Repositório aberto, instruções de instalação, resultados dos evals. Curto e direto. |

---

## Ângulos guardados para quando fizer sentido

- **"Por que 88% dos pilotos de agentes não chegam a produção"** — análise de mercado ancorada nos dados de 2026, com o projeto como exemplo do que fazer diferente
- **"O nicho vazio: nenhum ERP brasileiro tem MCP server"** — post de tese, bom para atrair conversas comerciais
- **"O que aprendi lendo os termos de uso de 5 APIs de gestão"** — se o processo render material
- **"Privacidade como feature"** — sem telemetria, credenciais locais, dado não trafega por terceiro

---

## O que não postar

- Progresso sem achado ("hoje implementei a terceira ferramenta")
- Screenshot de código sem explicação do problema que resolve
- Anúncio de intenção antes de existir artefato
- Post que só funciona se a pessoa já souber o que é MCP — sempre explique em uma frase

---

## Formato que funciona para este tipo de conteúdo

1. **Primeira linha carrega o achado.** Não construa suspense — o feed corta.
2. **Um número concreto** no primeiro parágrafo.
3. **O erro ou a surpresa** no meio.
4. **Artefato verificável** no fim: link do repo, vídeo, resultado de eval.
5. Sem emoji de foguete. Sem "🧵 thread". Sem "deixa eu te contar".

---

## Sobre o vídeo demo

Um vídeo de 30 a 60 segundos que mostra: pergunta digitada em português → ferramenta sendo chamada → resposta com dado real. Sem narração, sem introdução, sem logo animado.

Esse único artefato vale mais que os sete posts, e é reutilizável em todos eles.
