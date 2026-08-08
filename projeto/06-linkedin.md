# Pauta de conteúdo — LinkedIn

O objetivo não é postar sobre o projeto, é **publicar achados**. Post que ensina circula; post que anuncia progresso pessoal, não.

Regra: cada post precisa de um número, um erro ou uma decisão contra-intuitiva. Sem nenhum dos três, é update, não post.

**Vantagem deste tema sobre o anterior:** todo time de produto tem essa dor. Não precisa explicar por que o problema importa — o leitor já viveu isso hoje de manhã.

---

## Por fase

| # | Quando | Ângulo | Por que funciona |
|---|---|---|---|
| 1 | Fim da fase 0 | **"Construí um MCP server em PHP — sim, tem SDK oficial"** | Boa parte dos devs PHP acha que IA não é território deles. O SDK mantido com a PHP Foundation e o Symfony é notícia. Vídeo de 30s. |
| 2 | Fim da fase 1 | **"Indexei X meses de Mattermost e descobri que Y% das perguntas eram repetidas"** | Número concreto sobre uma dor universal. O dado sozinho já é o post. |
| 3 | Fase 3 | **"Controle o dado, não o prompt"** | A decisão de arquitetura mais interessante do projeto: sem prompt de sistema sob controle, a abstenção vive no retorno da ferramenta. Contra-intuitivo e transferível. |
| 4 | Fase 4 | **"O eval que quase falsifiquei sem querer"** | O split temporal. Fácil de errar, invisível quando erra, e a métrica fica linda. Alto valor para quem constrói RAG. |
| 5 | Fase 4 | **"False confident rate: a métrica que importa em suporte com IA"** | Argumenta que deflexão é a métrica errada. Ângulo de opinião sustentado por dados. |
| 6 | Fase 5 | **"O subproduto que valeu mais que o produto"** | A lista priorizada de lacunas de documentação, gerada pelos escalonamentos. Insight de produto que fala com PMs, não só devs. |
| 7 | Fase 5 | **Lançamento com números reais** | Antes/depois de perguntas repetidas, taxa de escalonamento, reincidência. Curto e verificável. |

---

## Ângulos guardados

- **"Por que não fiz um bot"** — a análise de custo por requisição vs. usar o cliente que o usuário já tem. Fala com quem está orçando projeto de IA.
- **"88% dos pilotos de agentes não chegam a produção"** — análise de mercado com o projeto como contraexemplo do que fazer diferente.
- **"Rodando evals de graça com Ollama"** — resolve dor concreta de custo em CI.
- **"Privacidade como feature"** — embeddings locais, sem telemetria, índice na infra do cliente.

---

## O que não postar

- Progresso sem achado ("hoje terminei o conector")
- **Qualquer conteúdo real do Mattermost da empresa** — nem anonimizado, nem "só um exemplo". Use dados sintéticos em toda demonstração pública.
- Nome da empresa ou detalhe que permita identificá-la, sem autorização explícita
- Número de deflexão sem o número de reincidência ao lado
- Post que só funciona se o leitor já souber o que é MCP — explique em uma frase, sempre

---

## Formato

1. **Primeira linha carrega o achado.** O feed corta; não construa suspense.
2. **Número concreto** no primeiro parágrafo.
3. **O erro ou a surpresa** no meio.
4. **Artefato verificável** no fim.
5. Sem emoji de foguete, sem "🧵 thread", sem "deixa eu te contar".

---

## Sobre a demonstração

Um vídeo de 30 a 60 segundos: pergunta digitada no Claude Code → ferramenta sendo chamada → resposta com citação da fonte. E uma segunda tomada mostrando o caso de **abstenção** — pergunta fora da base, sistema recusando e oferecendo escalonamento.

A segunda tomada é mais rara e mais impressionante que a primeira. Praticamente ninguém mostra o sistema dizendo "não sei".

⚠️ Gravar com **base sintética**, nunca com dados reais da empresa.
