# Projetos — índice

Este repositório organiza a conclusão do **PEX (Projeto de Extensão)** do curso de ADS. A `main` guarda o contexto institucional comum; **cada linha de projeto vive na própria branch**.

**Data de referência:** 20/08/2026

---

## Linhas de projeto

### `projeto/blindagem` — nova

**Assistente de resposta à exposição de dados pessoais.** A partir do perfil de exposição de uma pessoa — sem pedir nem armazenar CPF — gera um plano de ação priorizado e **verificável**, com fontes oficiais citadas, e acompanha a execução. Cobre duas superfícies: exposição de **dados** (o que se sabe sobre você) e exposição de **voz** (o que se consegue imitar de você, com o Protocolo Familiar de Verificação contra golpe de voz clonada). Não é verificador de vazamento nem detector de deepfake — as duas partes foram descartadas por decisão de projeto.

| | |
|---|---|
| **Dor** | Ferramentas existentes dizem que você foi exposto, nenhuma diz o que fazer, em que ordem, e como verificar. Todo alerta sobre voz clonada recomenda palavra-código, e nenhum ensina a implantar uma |
| **Organização do PEX** | Oficina em grupo já constituído (centro de convivência, igreja, associação) — contato pontual, sem parceria longa |
| **Diferencial** | Passo verificável, arquitetura sem dado pessoal, motor de regras determinístico fora do LLM, e a tese de tirar a voz do papel de fator de autenticação em vez de tentar detectar deepfake |
| **Stack** | Java 21 + Spring Boot + Spring AI + pgvector |

```
git checkout projeto/blindagem
```

### `projeto/support-mcp` — linha ativa

**MCP server de suporte técnico interno.** Consolida documentação estruturada, código do repositório e o histórico do Mattermost numa base pesquisável, exposta ao Claude Code que os próprios usuários já usam. Quando a base não sustenta uma resposta, sinaliza a incerteza e escala para um canal humano.

| | |
|---|---|
| **Dor** | Usuários perguntam repetidamente coisas já respondidas no Mattermost |
| **Organização do PEX** | A empresa onde o autor trabalha, setor de suporte |
| **Diferencial** | Abstenção calibrada — o sistema sabe quando não sabe |
| **Custo de inferência** | Zero — o modelo é o do próprio usuário |

```
git checkout projeto/support-mcp
```

### `projeto/nextfit-mcp` — arquivada

**MCP server para o NextFit**, sistema de gestão de academias e assessorias esportivas. Mesma arquitetura (MCP + evals), aplicada a consultas de negócio sobre alunos, contratos e financeiro.

| | |
|---|---|
| **Dor** | O NextFit tem 70+ relatórios, mas o gestor não acha o que precisa |
| **Organização do PEX** | Assessoria esportiva onde o autor treina |
| **Por que arquivada** | A dor de suporte interno é vivida diariamente, tem acesso garantido ao dado, mercado maior e validação imediata por usuários reais |

Continua tecnicamente válida e pode ser retomada — inclusive como segundo conector, já que o núcleo é o mesmo.

```
git checkout projeto/nextfit-mcp
```

---

## O que as duas têm em comum

Não foi uma troca de projeto, foi uma troca de **caso de uso**. O núcleo permanece:

- Servidor **MCP** em PHP com o SDK oficial (`mcp/sdk`, mantido com a PHP Foundation e o time do Symfony)
- **Custo de inferência zero** — um MCP server não chama LLM; quem paga é o cliente, pela assinatura que já tem
- **Suíte de evals** como diferencial — quase todo MCP server publicado é um wrapper sem testes
- Escopo conservador em operações de escrita
- Serve simultaneamente aos PEX 2–6, ao portfólio e a um produto potencial

---

## O que fica na `main`

| Arquivo | Conteúdo |
|---|---|
| `README.md` | Panorama das regras institucionais do PEX e roadmap de entrega |
| `PROJETOS.md` | Este índice |
| `CLAUDE.md` | Instruções para o Claude Code neste repositório |
| `pex-context/` | PDFs oficiais, transcrição de monitoria e índice das fontes |

Nada específico de projeto — isso vive nas branches.
