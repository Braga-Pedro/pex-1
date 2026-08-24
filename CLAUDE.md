# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## O que é este repositório

Este **não é um projeto de software**. É um espaço de trabalho pessoal do usuário para organizar a conclusão do **PEX (Projeto de Extensão)** do curso de Tecnologia em Análise e Desenvolvimento de Sistemas (ADS) — Descomplica + Centro Universitário União das Américas. Não há código-fonte, build, lint ou testes.

O conteúdo é uma mistura de:
- **PDFs institucionais** de referência (manual do PEX, roteiros, amostras de trabalhos aprovados) — usados como fonte de consulta, não devem ser editados.
- **Notas em markdown** escritas/mantidas com apoio do Claude, que sintetizam esses PDFs em contexto acionável.

## Modelo de branches — leia antes de editar

A `main` **não contém projeto técnico**. Ela guarda apenas o contexto institucional comum a todos. Cada linha de projeto vive na própria branch, com sua própria pasta `projeto/`:

| Branch | Conteúdo | Estado |
|---|---|---|
| `main` | Contexto institucional + `PROJETOS.md` (índice) | — |
| `projeto/support-mcp` | MCP server de suporte técnico interno (Mattermost + docs + código) | **ativa** |
| `projeto/nextfit-mcp` | MCP server para o NextFit (gestão de academias) | arquivada |

Consequências práticas:

- **Antes de editar documentação de projeto, confirme em que branch você está.** Editar `projeto/` na `main` é erro — lá não existe.
- Mudança que vale para todas as linhas (regra institucional, contexto do PEX) vai na `main`.
- Ao criar uma nova linha de projeto, ramifique da `main` e registre a linha em `PROJETOS.md`.
- A branch arquivada não deve ser apagada — o planejamento nela segue válido e pode ser retomado.

## Estrutura dos arquivos

**Raiz do repositório (`main`):**
- `README.md` — porta de entrada e panorama das regras institucionais do PEX: diagnóstico, formato do PEX 1 (relatório teórico, confirmado para todo mundo independente do período de ingresso — ver seção correspondente) e do PEX 2 em diante (prático), roadmap de entrega, erros a evitar. Contém uma "Data de referência" que deve ser atualizada se o roteiro for revisado.
- `PEX1-RELATORIO.md` — rascunho do relatório do PEX 1: artigo escolhido, resumo e respostas às perguntas do roteiro. É rascunho, não versão final — precisa ser conferido contra o roteiro real do perfil do usuário antes do envio.
- `CLAUDE.md` — este arquivo.
- `PROJETOS.md` — índice das linhas de projeto, com o motivo de cada uma existir e o que têm em comum.

**Pasta `projeto/` (só nas branches de projeto):**
Documentação numerada na ordem de leitura, começando por `00-conceito.md`. O conjunto de arquivos varia por linha — veja o `PROJETOS.md` da `main` ou o `00-conceito.md` da própria branch.

**Pasta `pex-context/` — índice de recursos:**
- `pex-context.md` — índice enxuto apontando para as fontes primárias (PDFs, vídeo de monitoria e sua transcrição) e o motivo de cada uma existir.
- `Transcricao monitoria youtube.txt` — transcrição bruta de uma aula de monitoria sobre o PEX, usada como fonte para o roadmap.
- PDFs institucionais de referência:
  - `Manual Projeto de Extensão 2024.pdf`
  - `Anais amostras de PEX - Projeto de Extensão.pdf`
  - `Descomplica _ Ads - Projeto De Extensão Iv.pdf`
  - `Roteiro_Analise_e_Desenvolvimento_de_Sistemas_IV_-_PEX.docx.pdf`

## Como trabalhar neste repositório

- O usuário está no 5º semestre do curso, sem nenhum PEX entregue ainda (situação de atraso/DP) — os PEX são cumulativos, então velocidade de regularização importa.
- O usuário é dev pleno (PHP, Laravel, Filament, Vue, JS, Docker, PostgreSQL, Python) com repertório em embeddings/RAG. O projeto escolhido precisa atender três objetivos ao mesmo tempo: cumprir os PEX, render portfólio no LinkedIn e ter chance de virar produto. Disponibilidade real: 2h/dia.
- Ao atualizar `README.md`, `PROJETOS.md`, os arquivos de `projeto/` ou `pex-context/pex-context.md`, mantenha o estilo existente: seções curtas em pt-BR, checklists com `- [ ]`, tabelas comparativas quando útil, e uma linha "Data de referência" atualizada.
- Ao extrair informação dos PDFs para as notas, confirme sempre contra o roteiro atual na Plataforma de Aulas (desde 2026.1 é a única via de entrega, para veteranos e calouros) — os PDFs institucionais podem estar desatualizados em relação ao formato vigente. O `README.md` já registra divergências confirmadas: o PEX 1 é relatório teórico para todo mundo (o que muda é a etapa, não o período de ingresso — confirmado por atendimento oficial em 24/08/2026), e a plataforma Work Love saiu de cena.
- Não há necessidade de comandos de build/lint/test — qualquer tarefa aqui é edição de texto/markdown ou pesquisa de conteúdo nos PDFs anexados.
