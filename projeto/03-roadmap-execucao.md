# Roadmap de execução — nextfit-mcp

**Disponibilidade:** 2h/dia (~14h/semana).
**Horizonte:** ~13 semanas até a v1 publicada.

Cada fase entrega algo que funciona sozinho. Se o cronograma apertar, para-se numa fronteira limpa em vez de ficar com meio produto.

---

## Fase 0 — Fundação (semanas 1–2)

- Repositório `nextfit-mcp`, licença MIT, README inicial
- `composer require mcp/sdk` — confirmar o estado atual do SDK (experimental até 1.0) e fixar a versão
- Servidor MCP mínimo com **uma** ferramenta de teste (`ping`)
- Conectar no Claude Desktop e ver a ferramenta aparecer
- Docker Compose para rodar o servidor

**Pronto quando:** você pergunta algo no Claude Desktop e sua ferramenta em PHP responde.

> Esse é o momento de maior valor por esforço do projeto inteiro — é quando fica concreto. Grave o vídeo aqui.

---

## Fase 1 — Primeiras ferramentas com dados sintéticos (semanas 3–4)

- Gerador de dados sintéticos realistas (alunos, contratos, faturas, check-ins)
- Três ferramentas read-only: `listar_alunos`, `alunos_inadimplentes`, `resumo_financeiro`
- Testes unitários (Pest) e testes de contrato de schema

**Por que sintético primeiro:** destrava todo o desenvolvimento sem depender de credencial nem expor dado real na prototipação. Os dados sintéticos também viram o harness de teste — valores conhecidos por construção permitem verificar se a resposta inventou número.

**Pronto quando:** as três ferramentas funcionam no Claude Desktop com dados de mentira.

---

## Fase 2 — Integração real com o NextFit (semanas 5–6)

- Cliente HTTP (Guzzle) para a API NextFit
- Autenticação por variável de ambiente
- Camada de cache e rate limit
- Adaptador que troca fonte sintética por real via configuração
- Tratamento de erro estruturado

**Dependência externa:** exige que o plano contratado pela assessoria habilite a API e que o dono gere credenciais. **Negocie isso na semana 1**, em paralelo — é o maior risco de cronograma do projeto.

**Pronto quando:** as mesmas três ferramentas respondem com dados reais.

---

## Fase 3 — Suíte de evals (semanas 7–9)

- Dataset de ~50 perguntas rotuladas nas seis categorias
- Runner em Python com Ollama local
- Métricas: tool selection accuracy, parameter accuracy, false call rate, refusal accuracy
- Relatório em Markdown gerado a cada execução
- Integração no CI (GitHub Actions)

**Pronto quando:** `make evals` roda e imprime um placar.

> Espere números ruins na primeira execução. Isso não é fracasso — é o achado. As descrições das ferramentas vão precisar de várias rodadas de ajuste, e esse ciclo *é* o conteúdo mais interessante do projeto.

---

## Fase 4 — Ferramentas derivadas (semanas 10–11)

- `alunos_sem_checkin` — quem sumiu há N dias
- `contratos_vencendo` — renovações próximas
- `metricas_periodo` — matrículas, cancelamentos, churn
- `frequencia_aluno`, `buscar_aluno`

Estas são as que agregam valor além de repassar a API — o NextFit tem o dado bruto, não a leitura acionável.

**Pronto quando:** o conjunto completo passa nos evals.

---

## Fase 5 — Empacotamento e publicação (semanas 12–13)

- README completo: instalação, configuração, exemplos de perguntas, resultados dos evals
- Declaração explícita de privacidade (sem telemetria, credenciais locais, read-only)
- Imagem Docker publicada
- Vídeo demo de 30–60 segundos
- Publicação em diretórios de MCP servers
- Post de lançamento

**Pronto quando:** um terceiro consegue instalar seguindo só o README.

---

## Fase 6 — Segundo conector (futuro)

Replicar a arquitetura para um sistema de maior alcance (Omie, Bling, Tiny, Conta Azul). O núcleo — camada de ferramentas, evals, guardrails — é reaproveitável; muda o adaptador de API.

É aqui que o projeto deixa de ser portfólio e começa a ser produto.

---

## Riscos

| Risco | Mitigação |
|---|---|
| API NextFit não habilitada no plano da assessoria | Fase 1 inteira roda em dados sintéticos; negociar acesso desde a semana 1 |
| SDK PHP experimental muda antes do 1.0 | Fixar versão; acompanhar changelog; a superfície MCP em si é estável |
| Modelos locais irregulares em tool calling | Escolher pelo selo "Tools" do Ollama; testar 2–3 modelos antes de fixar o do CI |
| Descrições ruins → evals ruins | É esperado na primeira rodada; o ciclo de ajuste é parte do trabalho, não um contratempo |
| Escopo crescer para escrita | Read-only é decisão registrada na v1 — resistir |
| Cronograma acadêmico x cronograma técnico | As fases foram desenhadas para casar com as entregas do PEX (ver `04-roadmap-pex.md`) |
