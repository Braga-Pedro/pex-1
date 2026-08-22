# Blindagem — assistente de resposta à exposição de dados pessoais

**Linha de projeto do PEX** · Curso de Tecnologia em Análise e Desenvolvimento de Sistemas
**Stack-alvo:** Java 21 + Spring Boot 3 + Spring AI + PostgreSQL/pgvector
**Data de referência:** 20/08/2026

> Esta branch contém uma linha de projeto. O contexto institucional do PEX (regras, prazos, formato de entrega) está na `main`. Veja [`PROJETOS.md`](https://github.com/Braga-Pedro/pex-1/blob/main/PROJETOS.md) para o índice das linhas.

---

## 1. Em uma frase

Um assistente que, a partir do **perfil de exposição** de uma pessoa — sem nunca pedir ou armazenar o CPF dela — gera um **plano de ação priorizado, verificável e com prazo**, fundamentado em fontes oficiais citadas, e a acompanha até que cada passo esteja concluído.

Cobre duas superfícies: a **exposição de dados** (o que se sabe sobre você) e a **exposição de voz e identidade** (o que se consegue imitar de você). A segunda é a fronteira nova, e é onde o projeto tem menos concorrência.

Não é um verificador de vazamento nem um detector de deepfake. As razões estão na seção 4 — são as duas decisões mais importantes deste documento.

---

## 2. O problema, com números

### 2.1 Exposição de dados

| Dado | Fonte |
|---|---|
| **53,9 milhões** de beneficiários do Auxílio Emergencial tiveram nome completo, CPF, NIS, município e valor publicados no Portal da Transparência em 2020 | CGU / Portal da Transparência |
| **~6,94 milhões** de tentativas de fraude no 1º semestre de 2025 — uma a cada **2,3 segundos** | Serasa Experian |
| **24 milhões** de brasileiros vitimados por golpes de Pix e boleto entre jul/2024 e jun/2025, com **R$ 29 bilhões** em perdas | Levantamento setorial |
| **R$ 6 bilhões** desviados em descontos indevidos em aposentadorias e pensões entre 2019 e 2024 | Apuração sobre consignado |
| Maior alta percentual de tentativas de fraude: pessoas com **mais de 60 anos** (+11,9%); **72 mil** denúncias de golpe financeiro contra idosos no Disque 100 em 2024 | Serasa Experian / Disque 100 |

### 2.2 Exposição de voz — a fronteira nova

| Dado | Fonte |
|---|---|
| **+400%** em tentativas de fraude com voz clonada entre 2022 e 2024 | Febraban |
| **+830%** no uso de deepfakes entre 2024 e 2025 no Brasil; golpes com IA cresceram **148%** em 2025 | Levantamentos de mercado |
| Ferramentas de IA presentes em **42,5%** das fraudes financeiras registradas no país | Levantamento setorial |
| **15 segundos** de áudio público bastam para clonar uma voz com sotaque, ritmo e entonação | Reportagem técnica |

**Como o golpe funciona.** O criminoso coleta áudio de fontes públicas — stories do Instagram, vídeos do TikTok e do YouTube, áudios encaminhados em grupos grandes de WhatsApp. Clona a voz, liga de **um número novo e desconhecido** para um familiar, e encena uma emergência. Os três roteiros mais comuns: sequestro ("me pegaram, transfere R$ 5 mil agora"), acidente ("estou no hospital, preciso pagar para ser atendido") e prisão ("fui preso, preciso da fiança"). O pedido é sempre por Pix, sempre urgente, e quase sempre acompanhado de um pedido de sigilo.

Há uma variante corporativa em circulação: voz clonada de diretor ou executivo ligando para o setor financeiro para autorizar transferência. A Febraban também registrou deepfake de voz sendo usado no golpe da falsa central telefônica.

**O elo entre 2.1 e 2.2.** A exposição de dados fornece o roteiro — nome dos parentes, cidade, rotina, vínculo com o INSS. A exposição de voz fornece a interpretação. Juntas, produzem uma ligação que a vítima não tem como distinguir de uma real **ouvindo**. É por isso que as duas superfícies pertencem ao mesmo projeto.

---

## 3. O que foi confirmado da ideia original, e o que precisou ser corrigido

A intuição de partida era: *"dados de brasileiros estão públicos, isso alimenta golpes, e falta uma ferramenta que diga o que fazer."* Duas coisas se confirmaram e uma precisou de ajuste.

**Confirmado — a exposição de 2020 foi real e massiva.** O Portal da Transparência publicou, para 53,9 milhões de pessoas, a combinação nome completo + CPF + NIS + município + valor recebido.

**Confirmado — falta a ferramenta.** Não existe serviço oficial do governo que informe se um CPF apareceu em vazamento. O Have I Been Pwned cobre e-mail e senha, não CPF. A Serasa monitora *consultas* ao CPF, não exposição. O Registrato do Banco Central mostra vínculos financeiros, não exposição.

**Corrigido — hoje o Portal da Transparência mascara o CPF.** A publicação atual de programas sociais traz nome, valor, estado e município, com o CPF parcialmente oculto, em atendimento à LGPD. A premissa "hoje qualquer um baixa a lista com CPF" **não vale mais para as publicações atuais**.

Isso não enfraquece o projeto — desloca o alvo. O que foi baixado em 2020 já circula, e mascarar depois não desfaz exposição pretérita. E nome completo somado a município e programa social continua sendo material suficiente para engenharia social convincente. O projeto trata **exposição acumulada e risco presente**, não "vazou ou não vazou".

---

## 4. O que este projeto **não** é — e por que

### 4.1 Não construir um banco de dados de CPFs vazados

Parece o caminho óbvio: obter as bases que circulam, indexar, permitir consulta por CPF. É o caminho errado, por cinco razões independentes — bastaria uma:

1. **LGPD.** Tratar dados pessoais de milhões de terceiros exige base legal. Não há base legal disponível para um projeto acadêmico pessoal.
2. **É a ferramenta do criminoso.** Consulta por CPF que devolve dados é exatamente o produto vendido em mercado clandestino. A intenção de quem construiu não muda o que a ferramenta faz.
3. **Vira alvo.** Concentrar essa base cria um ativo que atrai ataque, sob responsabilidade de um estudante sozinho.
4. **Mata o portfólio.** O repositório não poderia ser público, nem demonstrado em entrevista — o que anula o objetivo do projeto.
5. **Obtenção ilícita.** Baixar base vazada para alimentar o sistema é aquisição de dado de origem criminosa.

**Sobre o modelo do Have I Been Pwned.** Ele resolve a privacidade da *consulta* com k-anonymity: o cliente envia apenas um prefixo do hash, o servidor devolve todos os hashes com aquele prefixo, e a comparação final acontece no cliente — o servidor nunca sabe o que foi consultado. Técnica elegante, vale estudar. Mas protege quem consulta e **não resolve a origem da base**. O impedimento continua de pé.

### 4.2 Não tentar detectar voz clonada

Esta é a decisão de projeto mais contraintuitiva, e a mais importante do componente de voz.

O reflexo natural diante de "clonaram a voz do meu filho" é construir um detector de deepfake de áudio. **Não faça isso**, por três motivos:

1. **É inviável no cenário real.** O golpe chega por ligação telefônica, com compressão agressiva, ruído e latência. Detectores de áudio sintético operam sobre gravações limpas, não sobre uma chamada ao vivo num celular na rua.
2. **É uma corrida armamentista perdida.** Os geradores melhoram mais rápido que os detectores, e são financiados por quem lucra com o golpe.
3. **O falso negativo é catastrófico.** Um detector que diz "esta voz é autêntica" quando não é **entrega à vítima exatamente a confiança que o golpista precisa**. A ferramenta passaria a ajudar o golpe. Nenhum ganho de acurácia compensa esse modo de falha.

**A tese que substitui a detecção:** a voz deixou de ser um fator de autenticação — do mesmo modo que a assinatura manuscrita deixou. A resposta certa não é detectar melhor, é **tirar a voz do papel de prova de identidade** e colocar a verificação em um fator que a IA não alcança: um segredo combinado previamente e um canal que o golpista não controla.

Todo o componente de voz decorre disso.

### 4.3 Não sintetizar, clonar ou reproduzir voz — em hipótese alguma

O sistema **não integra nem implementa síntese de voz**, nem para demonstração, nem para treinamento, nem em ambiente de teste. O ensaio descrito na seção 6.6 é encenado por uma pessoa, a partir de roteiro em texto.

Esta é a fronteira que separa ferramenta defensiva de ferramenta dual-use, e ela vai **codificada em teste automatizado**, não apenas escrita aqui.

---

## 5. O gap real

As ferramentas existentes entregam, cada uma, **um pedaço** — e nenhuma diz o que fazer com o pedaço.

| Ferramenta | Entrega | Não entrega |
|---|---|---|
| Have I Been Pwned | E-mail/senha em vazamentos | Nada sobre CPF, nada sobre o que fazer |
| Serasa | Alerta de consulta ao CPF | Age depois do fato, não previne |
| Registrato (BCB) | Contas, chaves Pix e dívidas em seu nome | Não interpreta nem orienta; a maioria nem sabe que existe |
| Cartilhas CERT.br / ANPD | Orientação correta e oficial | Genérica, extensa, não priorizada, não verificável |
| Alertas de banco sobre voz clonada | "Combine uma palavra-código" | Não ajuda a combinar, distribuir, memorizar nem testar |

O que falta, e é onde o projeto vive:

- **Priorização** — a pessoa não sabe o que fazer *primeiro*. Nem tudo tem o mesmo impacto.
- **Verificabilidade** — nenhuma cartilha diz "faça X, e você saberá que funcionou quando vir Y".
- **Personalização** — o conselho para um aposentado do INSS não é o mesmo para um universitário.
- **Acompanhamento** — orientação lida e não executada tem valor zero.
- **Implantação** — "combine uma palavra-código com a família" é conselho unânime e quase nunca executado, porque ninguém diz *como*.

Esse último ponto merece ênfase: toda reportagem sobre golpe de voz termina recomendando palavra-código. Nenhuma explica como escolher uma que resista, como combinar sem deixar rastro, como fazer um parente de 78 anos lembrar dela sob pressão, ou como saber se a família toda de fato aderiu. **É trabalho de produto, não de conselho.**

---

## 6. Os componentes

### 6.1 Entrevista guiada — sem CPF, sem nome

O sistema **nunca pede dado identificável**. Pergunta sobre situação, não sobre identidade:

- Recebeu Auxílio Emergencial ou outro benefício social?
- É aposentado ou pensionista do INSS?
- Tem chave Pix cadastrada como o próprio CPF?
- Já teve celular roubado ou perdido?
- Usa a mesma senha em mais de um serviço?
- **Publica stories, vídeos ou áudios com sua voz em perfil aberto?**
- **Manda áudio em grupos grandes de WhatsApp?**
- **Tem parentes idosos que atenderiam uma ligação sua pedindo ajuda?**

As três últimas são o eixo de voz, e nenhuma resposta identifica a pessoa.

### 6.2 Motor de risco

Mapeia respostas para **vetores de ataque concretos** documentados no Brasil: consignado fraudulento, SIM swap, abertura de conta em nome de terceiro, golpe do falso funcionário de banco, Pix por engenharia social e **falso familiar com voz clonada**. Cada vetor carrega impacto estimado e probabilidade conforme o perfil.

### 6.3 Plano de ação priorizado e verificável

O núcleo do produto. Cada passo traz: **o que fazer**, **por onde** (link ou canal oficial), **quanto tempo leva**, **como confirmar que funcionou**.

| Ação | Canal oficial | Verificação |
|---|---|---|
| Bloquear consignado do INSS | Meu INSS → "Novo Pedido" → "Bloquear/Desbloquear Benefício para Empréstimo Consignado"; ou Central 135 | Efetiva em até 24h úteis; confira o status no próprio Meu INSS. **O INSS recomenda manter sempre bloqueado**, desbloqueando só quando for contratar |
| Cadastrar senha de portabilidade do chip | Claro `*1052` ou app Minha Claro → Segurança → Senha de Portabilidade; Vivo `*8486` ou Meu Vivo → Senha de Segurança do Chip; TIM e Oi têm equivalente | Tente uma solicitação de segunda via e confirme que a senha é exigida |
| Levantar contas e chaves Pix em seu nome | Registrato do Banco Central → Minha Vida Financeira, com login gov.br | Compare a lista com o que você reconhece; conta desconhecida vira contestação imediata |
| Ativar verificação em duas etapas no WhatsApp | WhatsApp → Configurações → Conta → Verificação em duas etapas | Saia e entre novamente; o PIN deve ser exigido |
| Silenciar chamadas de desconhecidos | WhatsApp → Configurações → Privacidade → Chamadas → Silenciar números desconhecidos | Peça a alguém fora dos seus contatos para ligar; não deve tocar |
| Reduzir a superfície de voz | Restringir quem vê stories e vídeos; rever perfis abertos no TikTok, Instagram e YouTube | Abra seu perfil de uma conta deslogada e veja quanto áudio seu está acessível |

> **Regra de ouro do produto:** o Banco Central afirma que **não autoriza intermediários** — só o próprio cidadão acessa o Registrato. O sistema **nunca pede credencial de nada**. Ele orienta a pessoa a acessar por conta própria. Isso é requisito de segurança, não limitação.

### 6.4 Camada de perguntas com RAG sobre corpus oficial

Indexa material **público e oficial**: fascículos "Vazamento de Dados" e "Proteção de Dados" da Cartilha CERT.br (com contribuição da ANPD), orientações do gov.br, resoluções do Banco Central sobre Pix, alertas da Febraban sobre golpes, textos da LGPD, material da Anatel sobre portabilidade.

Responde com **citação obrigatória da fonte** e **abstém-se** quando o corpus não sustenta a resposta, encaminhando ao canal oficial competente. Em orientação de segurança, resposta inventada causa dano direto.

### 6.5 Modo assistido

O público mais vitimado — idosos — não usa a ferramenta sozinho. O modo assistido gera um **roteiro de acompanhamento** para quem está ajudando e um **resumo imprimível** para deixar com a pessoa. É o componente que transforma a ferramenta em ação de extensão de verdade.

### 6.6 Protocolo Familiar de Verificação — o componente de voz

Responde ao cenário do falso familiar com voz clonada. Quatro peças:

**a) Assistente de palavra-código.** Conduz a família a estabelecer um segredo compartilhado, com critérios que ninguém explica:

- Não pode ser adivinhável a partir das redes sociais — nome de pet, time, rua, apelido de infância e nome de escola estão todos publicados em algum lugar
- Não pode ser algo que já circule em conversa gravável
- Precisa ser fácil de lembrar **sob pânico**, que é a condição em que será usada
- Precisa de um plano de troca, para o caso de vazar

**b) Distribuição sem rastro.** Como combinar a palavra sem deixá-la em nenhum canal digital que possa ser comprometido — presencialmente sempre que possível, e o que fazer quando não for. Uma palavra-código enviada por WhatsApp está no WhatsApp.

**c) Cartão de contra-verificação.** Material imprimível, para ficar ao lado do telefone ou na carteira. Contém o roteiro de três passos e as frases-gatilho:

> **Desligue. Respire. Ligue você** — pelo número que já está salvo na sua agenda, nunca pelo número que ligou.
>
> Sinais de golpe: urgência extrema · pedido de sigilo ("não conta pra ninguém") · valor específico por Pix · canal novo ou desconhecido · resistência a desligar e retornar.

O roteiro é curto de propósito. Sob pânico, ninguém executa uma lista de dez itens. E ataca o ponto exato de que o golpe depende: **você usar o canal que ele controla**.

**d) Cobertura familiar.** O protocolo só funciona se todos os elos aderirem — a pessoa mais vulnerável da família costuma ser a que menos usa tecnologia. O sistema acompanha quem já foi coberto e gera o material para alcançar quem falta. **Métrica: percentual de familiares cobertos**, não "material lido".

---

## 7. Os quatro diferenciais

**1. O passo verificável.** Toda orientação de segurança termina em "troque suas senhas" e ninguém sabe se ficou mais seguro. Aqui, cada passo tem critério objetivo de conclusão. É o que separa conselho de resultado.

**2. Arquitetura sem dado pessoal, por decisão de projeto.** O sistema não armazena CPF, nome, telefone, e-mail — nem a palavra-código, que **nunca sai do papel impresso e da cabeça da família**. A justificativa não é estética: armazenar isso transformaria a ferramenta em alvo, e uma ferramenta de segurança que vira alvo falhou no próprio propósito.

**3. Deslocamento do fator de autenticação.** Enquanto o mercado corre atrás de detectar deepfake, o projeto assume que a voz não é mais prova e resolve o problema por outro caminho. É uma posição de engenharia defensável, explicável em dois minutos, e que demonstra entendimento de modelo de ameaça — não só de framework.

**4. Ensaio guiado.** Ninguém aprende a reagir sob pânico lendo uma cartilha, do mesmo modo que ninguém aprende a evacuar um prédio lendo o manual — por isso existe simulado de incêndio. O sistema gera um **roteiro de encenação** que uma pessoa usa para simular a ligação com outra, em ambiente seguro e combinado. Ao fim, mede: reconheceu os sinais? pediu a palavra-código? desligou e ligou de volta?

> **Salvaguardas do ensaio, codificadas em teste:** roda apenas com consentimento explícito de quem vai ser testado; **não gera áudio**, apenas roteiro em texto para encenação humana; não produz mensagem pronta para envio a terceiros; a saída é descritiva ("reconheça este padrão"), nunca operacional; e vem sempre acompanhada da ação defensiva correspondente.

---

## 8. Arquitetura

| Componente | Papel |
|---|---|
| **Java 21 + Spring Boot 3** | Base. Records para o modelo de risco, tipagem forte no domínio |
| **Spring AI** | `ChatClient` e `VectorStore` para a camada de perguntas, com citação de fonte obrigatória |
| **PostgreSQL + pgvector** | Corpus e embeddings. **Nenhuma tabela de dados pessoais** |
| **Apache Tika** | Extração de texto dos PDFs oficiais (cartilhas, resoluções, normas) |
| **Motor de regras** | Perfil → vetores de risco → plano priorizado. Determinístico e testável, **sem LLM no caminho crítico** |
| **Gerador de material impresso** | Cartão de contra-verificação e resumo do plano em PDF, prontos para imprimir |
| **Spring Security** | Sessão anônima; sem cadastro, sem credencial de terceiros |
| **Testcontainers + JUnit** | Integração com Postgres real em contêiner |
| **Docker + GitHub Actions** | Pipeline com a suíte de avaliação e os testes de salvaguarda como etapas que reprovam o build |

**Decisão de arquitetura central:** o plano de ação é gerado por **motor de regras determinístico**, não por LLM. O modelo atua só na camada de perguntas abertas. Orientação de segurança precisa ser auditável e reproduzível — a mesma entrada tem de gerar a mesma saída, sempre, e alguém precisa poder apontar qual regra produziu cada recomendação. Saber **onde não usar IA** é sinal de maturidade mais forte do que usá-la em todo lugar.

---

## 9. Salvaguardas — resumo

Estas restrições são requisitos de produto, verificados por teste automatizado no CI:

- [ ] Nenhum dado pessoal identificável é armazenado em qualquer ponto do sistema
- [ ] A palavra-código nunca transita nem é persistida pelo sistema
- [ ] Nenhuma credencial de serviço de terceiro é solicitada
- [ ] O sistema não sintetiza, clona nem reproduz voz — em nenhum ambiente
- [ ] O ensaio exige consentimento explícito e não gera conteúdo pronto para envio a terceiros
- [ ] Toda resposta da camada RAG cita a fonte ou se abstém
- [ ] O motor de regras é determinístico e auditável passo a passo

---

## 10. Como isso vira PEX

### ⚠️ O ponto a resolver primeiro

O PEX exige uma **organização** — empresa, ONG, escola, igreja, associação. **Ajudar um amigo, individualmente, provavelmente não é aceito** como projeto de extensão, por mais real que seja o benefício. Extensão pressupõe ação junto a uma coletividade.

**Confirme isso no roteiro da sua disciplina antes de planejar em cima.**

### A solução que preserva a vantagem da ideia

Mantenha a ferramenta como produto individual e faça a **ação de extensão em formato de oficina**, num grupo que já se reúne: centro de convivência de idosos, grupo de terceira idade de igreja, associação de bairro, escola, sindicato.

Isso conserva o que motivou a ideia — **um único contato pontual**, sem parceria de dois anos — e ataca o público mais vitimado do país. Uma oficina rende lista de presença, fotos e depoimentos: evidência farta para o relatório.

O amigo continua no plano, como **piloto**: é com ele que você testa a ferramenta e grava o vídeo de uso antes de levar a um grupo.

**O componente de voz é o que torna a oficina memorável.** Uma palestra sobre proteção de dados é esquecida na semana seguinte. Um ensaio ao vivo, em que a pessoa percebe que teria caído, não é. E o resultado é contável: quantas famílias saíram com palavra-código combinada.

### Cronograma

| PEX | Entrega |
|---|---|
| **2** | Pesquisa e desenho: mapear vetores de risco reais no Brasil, montar o corpus oficial, desenhar o motor de regras e o protocolo familiar. Sem código de produção — é o semestre de estudar Java e Spring |
| **3** | MVP: entrevista guiada, motor de regras e plano de ação gerado. Piloto com o amigo, com registro em vídeo |
| **4** | Protocolo Familiar completo: cartão imprimível, assistente de palavra-código e ensaio guiado. **Primeira oficina** — formato liberado a partir deste PEX |
| **5** | Camada RAG sobre o corpus oficial, modo assistido e acompanhamento de progresso. Segunda oficina, com ajustes do que a primeira revelou |
| **6** | Observabilidade, medição de impacto (planos gerados, passos concluídos, famílias cobertas) e publicação aberta da ferramenta |

> Nota: o Protocolo Familiar subiu para o PEX 4 e o RAG desceu para o 5. Motivo: o protocolo é a peça de maior impacto social e a que faz a oficina funcionar — e a oficina só é permitida a partir do quarto PEX. O RAG é valioso, mas é conforto; o cartão impresso salva alguém antes.

---

## 11. Por que esta linha atende aos três objetivos ao mesmo tempo

| Objetivo | Como é atendido |
|---|---|
| **Cumprir os PEX** | Benefício social direto e mensurável, com formato de oficina que exige contato pontual |
| **Portfólio Java** | Spring Boot, Spring AI, pgvector, motor de regras, testes de integração e de salvaguarda, CI — mais uma tese de segurança que sustenta conversa técnica |
| **Produto potencial** | Não existe equivalente brasileiro; serve pessoa física, empresa (a variante corporativa do golpe de voz é dor real) e terceiro setor |

Some-se o que a ideia original buscava: **dependência mínima de terceiros**. O desenvolvimento não depende de ninguém. Só a ação de extensão precisa de um grupo — uma vez, por algumas horas.

---

## 12. Riscos e como tratá-los

| Risco | Tratamento |
|---|---|
| Pessoa física não ser aceita como beneficiária do PEX | Formato de oficina em grupo; confirmar no roteiro antes de investir |
| Escopo maior do que 2h/dia comporta | Motor de regras, protocolo familiar e RAG são fases separadas (PEX 3, 4 e 5), nunca simultâneas |
| Dar orientação errada e causar dano | Corpus exclusivamente oficial, citação obrigatória, abstenção conservadora, motor determinístico e auditável |
| O ensaio ou o reconhecimento de padrão serem mal utilizados | Salvaguardas da seção 9 codificadas em teste automatizado |
| Falsa sensação de segurança | O sistema nunca afirma que uma ligação é legítima; ele só ensina a verificar por outro canal |
| Fontes oficiais mudarem procedimento | Data de verificação registrada por item do plano; revisão a cada semestre do PEX |

---

## 13. Fontes

**Exposição de dados**
- [Portal da Transparência — Auxílio Emergencial](https://portaldatransparencia.gov.br/download-de-dados/auxilio-emergencial)
- [Portal da Transparência — Benefícios ao cidadão](https://portaldatransparencia.gov.br/perguntas-frequentes/beneficios-ao-cidadao)
- [Cartilha CERT.br — fascículo Vazamento de Dados](https://cartilha.cert.br/fasciculos/vazamento-de-dados/fasciculo-vazamento-de-dados.pdf)
- [ANPD e CERT.br — publicações sobre proteção de dados](https://www.gov.br/anpd/pt-br/assuntos/noticias/em-comemoracao-ao-dia-internacional-da-protecao-de-dados-cert-br-lanca-novas-publicacoes-sobre-o-tema)

**Ações defensivas**
- [INSS — bloquear ou desbloquear benefício para empréstimo consignado](https://www.gov.br/inss/pt-br/noticias/veja-como-bloquear-ou-desbloquear-o-beneficio-para-emprestimo-consignado)
- [Registrato — Banco Central do Brasil](https://www.bcb.gov.br/cidadaniafinanceira/registrato)

**Voz clonada e deepfake**
- [Golpes por voz clonada deixam bancos brasileiros em alerta — Tribuna do Sertão](https://www.tribunadosertao.com.br/geral/2026/02/25/862930-golpes-por-voz-clonada-deixam-bancos-brasileiros-em-alerta-uma-fraude-a-cada-23-segundos)
- [Clonagem de voz e imagem por IA: nova fronteira de golpes no Brasil — Band](https://www.band.com.br/noticias/jornal-da-band/ultimas/clonagem-de-voz-e-imagem-por-ia-nova-fronteira-de-golpes-no-brasil-202601262022)
- [Como identificar golpes com IA: deepfake de voz e clonagem — Exame](https://exame.com/tecnologia/examelab/como-identificar-golpes-com-ia-deepfake-de-voz-clonagem-e-mensagens-falsas/)
- [Febraban — alerta sobre golpe do falso gerente](https://portal.febraban.org.br/noticia/4431/pt-br/)
- [Como proteger sua família de golpes com vozes clonadas — Olhar Digital](https://olhardigital.com.br/2025/07/11/carros-e-tecnologia/como-proteger-sua-familia-de-golpes-com-vozes-clonadas-por-inteligencia-artificial/)

**Ressalva:** números de fraude vêm de levantamentos setoriais e de imprensa, com metodologias distintas — leia como ordem de grandeza. Procedimentos oficiais (INSS, operadoras, Banco Central, WhatsApp) foram verificados em agosto de 2026 e devem ser reconferidos antes de entrarem no plano gerado pela ferramenta.
