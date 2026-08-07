# Arquitetura — nextfit-mcp

## Repositório

**Novo, do zero.** O [`embedding_python_generate`](https://github.com/Braga-Pedro/embedding_python_generate) é evidência de repertório prévio em embeddings/RAG, não base deste projeto.

**Nome do repositório:** `nextfit-mcp`. Nomes descritivos funcionam melhor para MCP servers — as pessoas buscam literalmente "nextfit mcp" no GitHub. Um nome de marca só faz sentido quando virar guarda-chuva de vários conectores.

---

## Stack

**SDK oficial PHP de MCP** — [`mcp/sdk`](https://github.com/modelcontextprotocol/php-sdk), mantido em colaboração entre a **PHP Foundation** e o time do **Symfony**. Isso é o que torna o projeto viável na sua linguagem principal: PHP é um dos 10 SDKs oficialmente suportados do ecossistema.

| Camada | Tecnologia |
|---|---|
| Servidor MCP | PHP 8.3 + `mcp/sdk` |
| Cliente HTTP | Guzzle (consumo da API NextFit) |
| Cache | Redis ou arquivo (respeitar rate limit da API) |
| Testes | Pest ou PHPUnit |
| Evals | Python + Ollama (ver [`02-evals.md`](02-evals.md)) |
| Distribuição | Docker + Composer |

⚠️ O SDK PHP é **experimental até o 1.0** — fixar versão no `composer.json` e acompanhar o changelog. Vale confirmar o estado atual antes do primeiro commit.

---

## Como funciona

```
┌─────────────────┐   stdio/HTTP    ┌──────────────┐   HTTPS   ┌─────────┐
│ Claude Desktop  │ ◄─────────────► │ nextfit-mcp  │ ◄───────► │ NextFit │
│ (do cliente)    │   protocolo MCP │  (PHP)       │    API    │   API   │
└─────────────────┘                 └──────────────┘           └─────────┘
   paga a inferência                 zero token                 dados do
   (assinatura dele)                 consumido                  cliente
```

O servidor roda na infraestrutura do cliente (ou local na máquina dele). As credenciais do NextFit ficam com o cliente, em variáveis de ambiente — **nunca passam pelo autor do projeto**.

---

## Escopo das ferramentas — v1 read-only

**Decisão de projeto: a versão 1 não expõe nenhuma operação de escrita.**

O motivo é de risco, não de esforço: uma IA que pode cancelar contratos é uma IA que um dia vai cancelar o contrato errado. Read-only elimina a classe inteira de falhas destrutivas, acelera a entrega, e é muito mais fácil de vender para um dono de negócio desconfiado. Escrita entra depois, com confirmação explícita.

### Ferramentas propostas

| Ferramenta | Retorna | Pergunta que responde |
|---|---|---|
| `listar_alunos` | Lista filtrada por status, plano, modalidade | "Quantos alunos ativos temos?" |
| `buscar_aluno` | Ficha de um aluno | "Como está a situação do João?" |
| `alunos_inadimplentes` | Alunos com faturas em atraso + dias | "Quem está devendo?" |
| `contratos_vencendo` | Contratos que expiram em N dias | "Quem preciso renovar este mês?" |
| `resumo_financeiro` | Receita, inadimplência e ticket por período | "Como foi julho?" |
| `frequencia_aluno` | Check-ins de um aluno num período | "O Pedro está aparecendo?" |
| `alunos_sem_checkin` | Quem não treina há N dias | "Quem está sumindo?" |
| `metricas_periodo` | Novas matrículas, cancelamentos, churn | "Estamos crescendo?" |

As duas últimas são derivadas — o NextFit tem o dado bruto, mas não a leitura acionável. É onde o servidor agrega valor além de repassar a API.

### Como escrever as descrições

A qualidade da descrição de cada ferramenta é o fator que mais afeta se o modelo a chama na hora certa. A regra: **seja prescritivo sobre *quando* chamar, não só sobre o que a ferramenta faz.**

```
❌ "Lista alunos inadimplentes."
✅ "Lista alunos com faturas em atraso, ordenados por dias de atraso.
    Use quando o usuário perguntar sobre inadimplência, cobrança,
    pendências financeiras ou quem está devendo. Retorna nome,
    valor em aberto e dias de atraso. Não retorna histórico de pagamento —
    para isso use buscar_aluno."
```

Dizer o que a ferramenta **não** retorna é tão importante quanto dizer o que ela retorna: evita que o modelo a chame esperando dados que não virão.

---

## Segurança e LGPD

O servidor lida com dados pessoais (nome, CPF, situação financeira, frequência). Decisões de projeto:

- **Read-only na v1** — sem risco de mutação acidental.
- **Credenciais só por variável de ambiente**, nunca em código ou arquivo versionado.
- **Escopo mínimo** — se a API do NextFit permitir token com permissão restrita, documentar isso como requisito de instalação.
- **Sem telemetria.** O servidor não envia nada para lugar nenhum. Isso precisa estar escrito no README, porque é a primeira pergunta que um dono de negócio faz.
- **Log sem PII** — registrar qual ferramenta foi chamada e quanto tempo levou, nunca o conteúdo retornado.
- **Rate limit e cache** — respeitar os limites da API NextFit e não transformar uma pergunta em cem requisições.

---

## Decisões registradas

| Decisão | Alternativa descartada | Motivo |
|---|---|---|
| PHP + SDK oficial | TypeScript ou Python | SDK oficial existe em PHP; é a linguagem de maior fluência do autor |
| Read-only na v1 | Expor escrita | Elimina falhas destrutivas; muito mais vendável |
| MCP server puro | Interface de chat própria | Custo de inferência zero; escopo menor; melhor artefato de portfólio |
| Servidor roda no cliente | SaaS hospedado | Dado sensível não trafega pelo autor; sem custo de infraestrutura |
| NextFit primeiro | ERP de maior alcance | É o caso que dá para validar de verdade agora, e fecha o ciclo com o PEX |
| Evals com Ollama local | API paga | Custo zero para rodar a suíte em cada commit |
