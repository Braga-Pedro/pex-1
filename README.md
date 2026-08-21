# Blindagem — assistente de resposta à exposição de dados pessoais

**Linha de projeto do PEX** · Curso de Tecnologia em Análise e Desenvolvimento de Sistemas
**Stack-alvo:** Java 21 + Spring Boot 3 + Spring AI + PostgreSQL/pgvector
**Data de referência:** 20/08/2026

> Esta branch contém uma linha de projeto. O contexto institucional do PEX (regras, prazos, formato de entrega) está na `main`. Veja [`PROJETOS.md`](https://github.com/Braga-Pedro/pex-1/blob/main/PROJETOS.md) para o índice das linhas.

---

## 1. Em uma frase

Um assistente que, a partir do **perfil de exposição** de uma pessoa — sem nunca pedir ou armazenar o CPF dela — gera um **plano de ação priorizado, verificável e com prazo**, fundamentado em fontes oficiais citadas, e a acompanha até que cada passo esteja concluído.

Não é mais um verificador de vazamento. A razão está na seção 4, e é a decisão mais importante deste documento.

---

## 2. O problema, com números

| Dado | Fonte |
|---|---|
| **53,9 milhões** de beneficiários do Auxílio Emergencial tiveram nome completo, CPF, NIS, município e valor publicados no Portal da Transparência em 2020 | CGU / Portal da Transparência |
| **~7 milhões** de tentativas de fraude no 1º semestre de 2025 — uma a cada **2,3 segundos** | Panorama de golpes digitais |
| **24 milhões** de brasileiros vitimados por golpes de Pix e boleto entre jul/2024 e jun/2025, com **R$ 29 bilhões** em perdas | Levantamento setorial |
| **R$ 6 bilhões** desviados em descontos indevidos em aposentadorias e pensões entre 2019 e 2024 | Apuração sobre consignado |
| Maior alta percentual de tentativas de fraude: pessoas com **mais de 60 anos** (+11,9%); **72 mil** denúncias de golpe financeiro contra idosos no Disque 100 em 2024 | Serasa Experian / Disque 100 |

O elo entre as linhas: golpes de consignado e abertura de conta usam **dados obtidos de vazamentos e de fontes públicas** para passar pela verificação de identidade. A exposição não é o dano — é o insumo do dano.

---

## 3. O que foi confirmado da ideia original, e o que precisou ser corrigido

A intuição de partida era: *"dados de brasileiros estão públicos, isso alimenta golpes, e falta uma ferramenta que diga o que fazer."* Pesquisando, duas coisas se confirmaram e uma precisou de ajuste.

**Confirmado — a exposição de 2020 foi real e massiva.** O Portal da Transparência publicou, para 53,9 milhões de pessoas, a combinação nome completo + CPF + NIS + município + valor recebido.

**Confirmado — falta a ferramenta.** Não existe serviço oficial do governo que informe se um CPF apareceu em vazamento. O Have I Been Pwned cobre e-mail e senha, não CPF. A Serasa monitora *consultas* ao CPF, não exposição. O Registrato do Banco Central mostra vínculos financeiros, não exposição.

**Corrigido — hoje o Portal da Transparência mascara o CPF.** A publicação atual de programas sociais traz nome, valor, estado e município, com o CPF parcialmente oculto, em atendimento à LGPD. Ou seja: a premissa "hoje qualquer um baixa a lista com CPF" **não vale mais para as publicações atuais**.

Isso não enfraquece o projeto — desloca o alvo. O que foi baixado em 2020 já circula, e mascarar depois não desfaz exposição pretérita. E nome completo somado a município e programa social continua sendo material suficiente para engenharia social convincente. O projeto passa a tratar **exposição acumulada e risco presente**, não "vazou ou não vazou".

---

## 4. O que este projeto **não** é — e por que

> **Não construir um banco de dados de CPFs vazados, sob nenhuma arquitetura.**

Parece o caminho óbvio: obter as bases que circulam, indexar, permitir consulta por CPF. É o caminho errado, por cinco razões independentes — bastaria uma:

1. **LGPD.** Tratar dados pessoais de milhões de terceiros exige base legal. Não há base legal disponível para um projeto acadêmico pessoal.
2. **É a ferramenta do criminoso.** Consulta por CPF que devolve dados é exatamente o produto vendido em mercado clandestino. A intenção de quem construiu não muda o que a ferramenta faz.
3. **Vira alvo.** Concentrar essa base cria um ativo que atrai ataque, sob responsabilidade de um estudante sozinho.
4. **Mata o portfólio.** O repositório não poderia ser público, nem demonstrado em entrevista — o que anula o objetivo do projeto.
5. **Obtenção ilícita.** Baixar base vazada para alimentar o sistema é aquisição de dado de origem criminosa.

**Sobre o modelo do Have I Been Pwned.** Ele resolve a parte da privacidade da *consulta* com k-anonymity: o cliente envia apenas um prefixo do hash, o servidor devolve todos os hashes com aquele prefixo, e a comparação final acontece no cliente — o servidor nunca sabe o que foi consultado. É uma técnica elegante e vale estudar. Mas ela protege quem consulta, **não resolve a origem da base**. O impedimento continua de pé.

**Conclusão de projeto:** a verificação de vazamento é a parte menos original e mais arriscada da ideia. Descartá-la libera o esforço para onde não há concorrência.

---

## 5. O gap real

As ferramentas existentes entregam, cada uma, **um pedaço** — e nenhuma diz o que fazer com o pedaço.

| Ferramenta | Entrega | Não entrega |
|---|---|---|
| Have I Been Pwned | E-mail/senha em vazamentos | Nada sobre CPF, nada sobre o que fazer |
| Serasa | Alerta de consulta ao CPF | Age depois do fato, não previne |
| Registrato (BCB) | Contas, chaves Pix e dívidas em seu nome | Não interpreta nem orienta; a maioria nem sabe que existe |
| Cartilhas CERT.br / ANPD | Orientação correta e oficial | Genérica, extensa, não priorizada, não verificável |

O que falta, e é onde o projeto vive:

- **Priorização** — a pessoa não sabe o que fazer *primeiro*. Nem tudo tem o mesmo impacto.
- **Verificabilidade** — nenhuma cartilha diz "faça X, e você saberá que funcionou quando vir Y".
- **Personalização** — o conselho para um aposentado do INSS não é o mesmo para um universitário.
- **Acompanhamento** — orientação lida e não executada tem valor zero.

---

## 6. Os cinco componentes

### 6.1 Entrevista guiada — sem CPF, sem nome

O sistema **nunca pede dado identificável**. Ele pergunta sobre situação, não sobre identidade:

- Recebeu Auxílio Emergencial ou outro benefício social?
- É aposentado ou pensionista do INSS?
- Tem chave Pix cadastrada como o próprio CPF?
- Já teve celular roubado ou perdido?
- Usa a mesma senha em mais de um serviço?
- Tem conta em mais de um banco ou fintech?

Cada resposta ativa um conjunto de vetores de risco. Nenhuma resposta identifica a pessoa.

### 6.2 Motor de risco

Mapeia respostas para **vetores de ataque concretos** documentados no Brasil: consignado fraudulento, SIM swap, abertura de conta em nome de terceiro, golpe do falso funcionário de banco, Pix por engenharia social. Cada vetor carrega impacto estimado e probabilidade conforme o perfil.

### 6.3 Plano de ação priorizado e verificável

O núcleo do produto. Cada passo traz: **o que fazer**, **por onde** (link ou canal oficial), **quanto tempo leva**, **como confirmar que funcionou**. Exemplos reais levantados na pesquisa:

| Ação | Canal oficial | Verificação |
|---|---|---|
| Bloquear consignado do INSS | Meu INSS → "Novo Pedido" → "Bloquear/Desbloquear Benefício para Empréstimo Consignado"; ou Central 135 | Efetiva em até 24h úteis; confira o status no próprio Meu INSS. **O INSS recomenda manter sempre bloqueado**, desbloqueando só quando for contratar |
| Cadastrar senha de portabilidade do chip | Claro `*1052` ou app Minha Claro → Segurança → Senha de Portabilidade; Vivo `*8486` ou Meu Vivo → Senha de Segurança do Chip; TIM e Oi têm equivalente | Tente uma solicitação de segunda via e confirme que a senha é exigida |
| Levantar contas e chaves Pix em seu nome | Registrato do Banco Central → Minha Vida Financeira, com login gov.br | Compare a lista com o que você reconhece; conta desconhecida vira contestação imediata |

> **Regra de ouro do produto:** o Banco Central afirma que **não autoriza intermediários** — só o próprio cidadão acessa o Registrato. O sistema, portanto, **nunca pede credencial de nada**. Ele orienta a pessoa a acessar por conta própria. Isso é requisito de segurança, não limitação.

### 6.4 Camada de perguntas com RAG sobre corpus oficial

Indexa material **público e oficial**: fascículos "Vazamento de Dados" e "Proteção de Dados" da Cartilha CERT.br (com contribuição da ANPD), orientações do gov.br, resoluções do Banco Central sobre Pix, textos da LGPD, material da Anatel sobre portabilidade.

Responde dúvidas com **citação obrigatória da fonte** e **abstém-se** quando o corpus não sustenta a resposta — encaminhando ao canal oficial competente. Em orientação de segurança, resposta inventada causa dano direto.

### 6.5 Modo assistido

O público mais vitimado — idosos — não usa a ferramenta sozinho. O modo assistido gera um **roteiro de acompanhamento** para quem está ajudando e um **resumo imprimível** para deixar com a pessoa. Este componente é o que transforma a ferramenta em ação de extensão de verdade.

---

## 7. Os três diferenciais

**1. O passo verificável.** Toda orientação de segurança termina em "troque suas senhas" e ninguém sabe se ficou mais seguro. Aqui, cada passo tem um critério objetivo de conclusão. É o que separa conselho de resultado.

**2. Arquitetura sem dado pessoal, por decisão de projeto.** O sistema não armazena CPF, nome, telefone ou e-mail. O progresso do plano fica no navegador do usuário ou sob um identificador anônimo sem vínculo com pessoa. A justificativa não é estética: **armazenar isso transformaria a ferramenta em alvo**, e uma ferramenta de segurança que vira alvo falhou no próprio propósito. Essa é a decisão de arquitetura mais defensável do projeto em uma entrevista técnica.

**3. Reconhecimento de padrão de golpe.** A partir do perfil, o sistema mostra **como uma abordagem fraudulenta contra aquele perfil se pareceria** — quais dados o golpista provavelmente já tem, qual pretexto usaria, qual pedido faria. Objetivo: treinar reconhecimento, do mesmo modo que simulações de phishing são prática padrão em segurança corporativa.

> **Salvaguardas obrigatórias deste componente:** opera exclusivamente sobre o perfil informado pelo próprio usuário; **não gera mensagem pronta para envio a terceiros**; a saída é descritiva ("reconheça este padrão"), nunca operacional; e é sempre acompanhada da ação defensiva correspondente. Estas restrições vão codificadas em teste, não só documentadas.

---

## 8. Arquitetura

| Componente | Papel |
|---|---|
| **Java 21 + Spring Boot 3** | Base. Records para o modelo de risco, tipagem forte no domínio |
| **Spring AI** | `ChatClient` e `VectorStore` para a camada de perguntas, com citação de fonte obrigatória |
| **PostgreSQL + pgvector** | Corpus e embeddings. **Nenhuma tabela de dados pessoais** |
| **Apache Tika** | Extração de texto dos PDFs oficiais (cartilhas, resoluções, normas) |
| **Motor de regras** | Perfil → vetores de risco → plano priorizado. Determinístico e testável, **sem LLM no caminho crítico** |
| **Spring Security** | Sessão anônima; sem cadastro, sem credencial de terceiros |
| **Testcontainers + JUnit** | Integração com Postgres real em contêiner |
| **Docker + GitHub Actions** | Pipeline com a suíte de avaliação como etapa que reprova o build |

**Decisão de arquitetura central:** o plano de ação é gerado por **motor de regras determinístico**, não por LLM. O modelo atua só na camada de perguntas abertas. Motivo: orientação de segurança precisa ser auditável e reproduzível — a mesma entrada tem de gerar a mesma saída, sempre, e alguém precisa poder apontar qual regra produziu cada recomendação. Saber **onde não usar IA** é um sinal de maturidade mais forte do que usá-la em todo lugar.

---

## 9. Como isso vira PEX

### ⚠️ O ponto a resolver primeiro

O PEX exige uma **organização** — empresa, ONG, escola, igreja, associação. **Ajudar um amigo, individualmente, provavelmente não é aceito** como projeto de extensão, por mais real que seja o benefício. Extensão pressupõe ação junto a uma coletividade.

**Confirme isso no roteiro da sua disciplina antes de planejar em cima.**

### A solução que preserva a vantagem da ideia

Mantenha a ferramenta como produto individual e faça a **ação de extensão em formato de oficina**, num grupo que já se reúne:

- Centro de convivência de idosos, grupo de terceira idade de igreja, associação de bairro, escola (turma de EJA ou pais), sindicato

Isso conserva exatamente o que motivou a ideia — **um único contato pontual**, sem parceria de dois anos, sem reunião mensal — e ainda ataca o público mais vitimado do país. Uma oficina rende lista de presença, fotos e depoimentos: evidência farta para o relatório.

O amigo continua no plano, como **piloto**: é com ele que você testa a ferramenta e grava o vídeo de uso antes de levar a um grupo.

### Cronograma

| PEX | Entrega |
|---|---|
| **2** | Pesquisa e desenho: mapear vetores de risco reais no Brasil, montar o corpus oficial, desenhar o motor de regras. Sem código de produção — é o semestre de estudar Java e Spring |
| **3** | MVP: entrevista guiada, motor de regras e plano de ação gerado. Piloto com o amigo, com registro em vídeo |
| **4** | Camada RAG sobre o corpus oficial, com citação e abstenção. **Oficina no grupo escolhido** — formato liberado a partir deste PEX |
| **5** | Modo assistido, acompanhamento de progresso e material imprimível. Segunda oficina, com ajustes do que a primeira revelou |
| **6** | Observabilidade, medição de impacto (quantos planos gerados, quantos passos concluídos) e publicação aberta da ferramenta |

---

## 10. Por que esta linha atende aos três objetivos ao mesmo tempo

| Objetivo | Como é atendido |
|---|---|
| **Cumprir os PEX** | Benefício social direto e mensurável, com formato de oficina que exige contato pontual |
| **Portfólio Java** | Spring Boot, Spring AI, pgvector, motor de regras, testes de integração, CI — e um tema de segurança que rende conversa técnica |
| **Produto potencial** | Não existe equivalente brasileiro; serve a pessoa física, empresa (treinamento de equipe) e terceiro setor |

Some-se o que a ideia original buscava: **dependência mínima de terceiros**. O desenvolvimento não depende de ninguém. Só a ação de extensão precisa de um grupo — uma vez, por algumas horas.

---

## 11. Riscos e como tratá-los

| Risco | Tratamento |
|---|---|
| Pessoa física não ser aceita como beneficiária do PEX | Formato de oficina em grupo; confirmar no roteiro antes de investir |
| Escopo maior do que 2h/dia comporta | Motor de regras e RAG são fases separadas (PEX 3 e 4), nunca simultâneas |
| Dar orientação errada e causar dano | Corpus exclusivamente oficial, citação obrigatória, abstenção conservadora, motor determinístico e auditável |
| O componente de reconhecimento de golpe ser mal utilizado | Salvaguardas da seção 7 codificadas em teste automatizado |
| Fontes oficiais mudarem procedimento | Data de verificação registrada por item do plano; revisão a cada semestre do PEX |

---

## 12. Fontes

- [Portal da Transparência — Auxílio Emergencial](https://portaldatransparencia.gov.br/download-de-dados/auxilio-emergencial)
- [Portal da Transparência — Benefícios ao cidadão (perguntas frequentes)](https://portaldatransparencia.gov.br/perguntas-frequentes/beneficios-ao-cidadao)
- [Cartilha de Segurança para Internet — fascículo Vazamento de Dados (CERT.br)](https://cartilha.cert.br/fasciculos/vazamento-de-dados/fasciculo-vazamento-de-dados.pdf)
- [ANPD e CERT.br lançam publicações sobre proteção de dados](https://www.gov.br/anpd/pt-br/assuntos/noticias/em-comemoracao-ao-dia-internacional-da-protecao-de-dados-cert-br-lanca-novas-publicacoes-sobre-o-tema)
- [INSS — como bloquear ou desbloquear o benefício para empréstimo consignado](https://www.gov.br/inss/pt-br/noticias/veja-como-bloquear-ou-desbloquear-o-beneficio-para-emprestimo-consignado)
- [Registrato — Banco Central do Brasil](https://www.bcb.gov.br/cidadaniafinanceira/registrato)
- [Have I Been Pwned](https://haveibeenpwned.com/)

**Ressalva:** números de fraude vêm de levantamentos setoriais e de imprensa, com metodologias distintas — leia como ordem de grandeza. Procedimentos oficiais (INSS, operadoras, Banco Central) foram verificados em agosto de 2026 e devem ser reconferidos antes de entrarem no plano gerado pela ferramenta.
