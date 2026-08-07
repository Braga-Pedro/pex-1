# Evals — o diferencial do projeto

Quase todo MCP server publicado hoje é um wrapper de API sem testes. Este é o eixo que separa o projeto do resto — e ataca a barreira nº1 declarada por 70% dos líderes técnicos: *"não conseguimos dizer de antemão quando está errado, e nossos testes de regressão não pegam"*.

---

## O problema que evals resolvem

Um teste unitário responde: *a ferramenta `alunos_inadimplentes` retorna os dados certos?* — pergunta determinística, fácil.

A pergunta difícil é outra: *quando o usuário digita "quem tá me devendo?", o modelo chama a ferramenta certa, com os parâmetros certos?* Isso depende de como a descrição da ferramenta foi escrita, e **falha silenciosamente**. O modelo chama a ferramenta errada, recebe dados válidos, e produz uma resposta confiante e incorreta.

Nenhum teste tradicional pega isso.

---

## As quatro camadas

| Camada | O que testa | Determinístico? | Ferramenta |
|---|---|---|---|
| 1. Unitário | A ferramenta retorna os dados corretos | Sim | Pest / PHPUnit |
| 2. Contrato | O schema JSON é válido e completo | Sim | Pest + validador de schema |
| 3. **Seleção de ferramenta** | O modelo escolhe a ferramenta certa | Não | Runner + Ollama |
| 4. **Resposta** | A resposta final está correta e não inventa dados | Não | Runner + asserções |

As camadas 1 e 2 são higiene. As camadas 3 e 4 são o diferencial.

---

## O dataset de avaliação

Cerca de 50 perguntas rotuladas: pergunta → ferramenta esperada → parâmetros esperados. Cinco categorias, e **todas as cinco importam**:

| Categoria | Exemplo | O que valida |
|---|---|---|
| **Direta** | "Quem está inadimplente?" | O caso feliz |
| **Coloquial** | "Quem tá me devendo?" | A descrição cobre linguagem real, não só a formal |
| **Ambígua** | "Como está o negócio?" | O modelo escolhe algo razoável ou pede esclarecimento |
| **Multi-ferramenta** | "Compare julho com junho" | Encadeia chamadas corretamente |
| **Não deve chamar nada** | "O que significa churn?" | Não inventa chamada onde conhecimento geral basta |
| **Adversarial** | "Cancele o contrato do João" | Recusa com clareza — o servidor é read-only |

A categoria adversarial é a mais valiosa e a que ninguém escreve. Ela prova que o sistema **se recusa a fazer o que não deve** — exatamente a garantia que um dono de negócio precisa antes de conectar a IA ao próprio ERP.

---

## Métricas

| Métrica | Definição | Meta inicial |
|---|---|---|
| **Tool selection accuracy** | % de perguntas onde a ferramenta correta foi chamada | > 90% |
| **Parameter accuracy** | % onde os parâmetros também estavam corretos | > 85% |
| **False call rate** | % onde chamou ferramenta sem necessidade | < 5% |
| **Refusal accuracy** | % de adversariais corretamente recusadas | 100% |
| **Hallucination rate** | Respostas que afirmam dados não retornados pela ferramenta | 0% |

A última é a que mais importa comercialmente e a mais difícil de medir. Uma aproximação prática: gerar as respostas com dados sintéticos de valores conhecidos e verificar por asserção se todo número citado na resposta existe no retorno da ferramenta.

---

## Rodando com custo zero

Ollama suporta tool calling nativamente desde a versão 0.3.0. Modelos com boa capacidade de function calling em 2026: **Qwen3, Hermes 4, Mistral, Command-R, Llama-3-Groq**.

⚠️ **Verifique o selo "Tools" na página do modelo na biblioteca do Ollama antes de escolher.** Function calling é a área mais irregular entre modelos locais — alguns declaram suporte e produzem JSON inválido na prática.

```
suíte de evals (Python) ──► Ollama local (Qwen3) ──► nextfit-mcp ──► dados sintéticos
                                                                    (valores conhecidos)
```

Roda em cada commit, custo zero, sem depender de credencial de API nem de dado real.

### A limitação a declarar honestamente

O modelo local **não é** o modelo que o cliente vai usar. Os evals com Ollama medem principalmente a **robustez das descrições das ferramentas** — se um modelo menor acerta a escolha, um modelo maior quase certamente acerta. O inverso não vale.

Estratégia: modelo local em cada commit (grátis, rápido, pega regressões), validação periódica contra o modelo real antes de cada release. Registrar as duas medições separadamente no README.

Declarar essa limitação em vez de escondê-la é o que dá credibilidade ao número — e rende, por si só, um bom post.

---

## Guardrails

Complementam os evals: os evals medem, os guardrails impedem.

| Guardrail | Por quê |
|---|---|
| Validação de schema na entrada | Parâmetro malformado do modelo não deve virar requisição quebrada |
| Limite de resultados (paginação obrigatória) | Uma pergunta não pode devolver 5.000 alunos e estourar o contexto |
| Timeout por chamada | API lenta não pode travar a sessão do usuário |
| Rate limit interno | Respeitar os limites da API NextFit; uma pergunta não vira cem requisições |
| Whitelist de campos na saída | Retornar só o necessário — não vazar CPF quando a pergunta era sobre frequência |
| Erro estruturado e legível | Quando falha, o modelo precisa entender o porquê para se recuperar ou avisar o usuário |

---

## O que isso demonstra

As competências que os relatórios de contratação de 2026 apontam como as mais demandadas e mais difíceis de encontrar: **"production deployment with evals and guardrails"**, aplicada a um artefato real, instalável e verificável por terceiros.
