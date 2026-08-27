# PEX 1 — Relatório

**Fonte:** roteiro real do PEX I, [`pex-context/Roteiro_PEX_I_TEORICO.docx_3.pdf`](pex-context/Roteiro_PEX_I_TEORICO.docx_3.pdf) — "PROJETO DE EXTENSÃO I – CONHECENDO A EXTENSÃO" (Todos os cursos).
**Data de referência:** 27/08/2026

> Este documento substitui um rascunho anterior (commits `1329df9` a `c118e06` no histórico do git), baseado numa estrutura genérica — resumo em palavras próprias + 3 perguntas abertas, com um artigo da SciELO sobre LGPD. Essa estrutura foi assumida sem o roteiro real em mãos e **não corresponde ao que o roteiro pede de fato**. O rascunho antigo continua no histórico do repositório, mas não deve ser usado para a entrega.

---

## O que a atividade pede, de fato

O roteiro é um documento geral, válido para todos os cursos, que explica o que é o PEX, por que ele existe (Resolução n° 7/2018 do MEC: mínimo 10% dos créditos curriculares em programas de extensão), os 17 ODS da ONU, e que cada curso tem um roteiro próprio a cada semestre.

A atividade concreta do PEX I ("Atividade Proposta para PEX I") é:

1. Acessar os **Anais de Projeto Integrador de Extensão da Descomplica/Uniamérica**, apontados pelo roteiro em `uniamerica.br/doc-anais/anais-da-xi-mostra.pdf`.
2. Escolher, dentro dessa coletânea, um artigo **na área de conhecimento do seu curso** (ADS).
3. Ler com atenção.
4. Preencher, nas caixas de entrega da plataforma, quatro itens: **ODS trabalhado**, **a atividade que foi realizada** (a do artigo escolhido), **suas conclusões**, e **anexar o artigo escolhido**.

**Ponto importante:** a fonte do artigo é essa coletânea de Anais especificamente — não SciELO, Periódicos CAPES, Google Acadêmico, ACM ou IEEE. Nenhuma dessas fontes genéricas atende ao que o roteiro pede aqui.

O roteiro não menciona resumo redigido livremente, nem perguntas abertas do tipo "por que escolheu / o que aprendeu / como aplicaria" — isso pertencia à estrutura genérica assumida antes. Também não menciona PDF único obrigatório para esta atividade especificamente; parece ser preenchimento direto de campos na plataforma, com o artigo como anexo. **Confirme isso ao abrir a caixa de entrega real**, porque pode haver detalhe de formatação não capturado no texto do roteiro.

---

## Questões de fixação

O roteiro trata isso como um quiz de múltipla escolha, separado da atividade acima — três perguntas, respondíveis diretamente pelo próprio texto do roteiro:

**1. Com base na leitura sugerida do Manual de Projeto de Extensão, qual é o principal objetivo dos projetos de extensão universitária?**

**Resposta: (a)** Aplicar o conhecimento acadêmico em situações reais, promovendo uma troca entre a instituição de ensino e a comunidade.

*Justificativa: o roteiro afirma que "os projetos de extensão têm como foco principal a aplicação prática do conhecimento em prol da sociedade... Esses projetos funcionam como uma ponte entre a teoria estudada em sala de aula e os desafios do mundo real."*

**2. Qual das opções abaixo melhor define o que é um projeto de extensão universitária?**

**Resposta: (a)** É uma iniciativa que leva o conhecimento acadêmico para a comunidade, promovendo uma troca entre saberes científicos e populares.

*Justificativa: o roteiro define PEX como "iniciativas que visam conectar o conhecimento acadêmico produzido nas universidades com a comunidade externa."*

**3. O que são os Objetivos de Desenvolvimento Sustentável (ODS), estabelecidos pela ONU?**

**Resposta: (a)** São metas globais que visam promover o desenvolvimento sustentável em áreas como educação, saúde, meio ambiente, igualdade e erradicação da pobreza até 2030.

*Justificativa: o roteiro cita a ONU diretamente: "apelo global à ação para acabar com a pobreza, proteger o meio ambiente e o clima e garantir que as pessoas, em todos os lugares, possam desfrutar de paz e de prosperidade... a fim de que possamos atingir a Agenda 2030."*

---

## Artigo escolhido

| | |
|---|---|
| **Título** | CyberLeak: Análise de Padrões de Segurança Digital |
| **Autores** | Anthony Gabriel Kuhnen Rodrigues, Bouchra Assad Akl Abou-Itaif, Guilherme Narde da Lapa, Gustavo Piegat Glizt da Silva, Jean Felipe Moschen Buss, Vinícius Gabriel Aquino Ferreira (acadêmicos de Engenharia de Software); Larissa Daiana de Almeida Barbado (mentora); Ruminiki Schmoeller e Isabel Fernandes de Souza (docentes) |
| **Fonte** | Anais da XI Mostra de Projetos Integradores de Extensão, Descomplica/Uniamérica |
| **Páginas** | 65 a 70 (numeração impressa no rodapé do PDF; o intervalo 68–73 inicialmente estimado estava perto, mas não exato) |
| **Anexo neste repositório** | [`pex-context/CyberLeak - artigo escolhido (Anais XI Mostra, p.65-70).pdf`](pex-context/CyberLeak%20-%20artigo%20escolhido%20%28Anais%20XI%20Mostra%2C%20p.65-70%29.pdf) |

**Do que trata:** projeto que atende a uma demanda de um Centro Especializado em Segurança Cibernética. Consolida e analisa dados de vazamentos de credenciais (e-mails e senhas) usando princípios de Business Intelligence, culminando numa dashboard interativa em Power BI. A base de dados vem da API do **Have I Been Pwned (HIBP)** — 773 milhões de registros vazados — a mesma referência já usada na pesquisa deste repositório para a linha `projeto/blindagem`.

---

## Campos a preencher — respostas com base no artigo real

**ODS trabalhado:**

**ODS 9 — Indústria, Inovação e Infraestrutura**, como leitura principal: o projeto constrói uma ferramenta tecnológica (pipeline de dados + banco PostgreSQL + dashboard Power BI) para fortalecer a infraestrutura de segurança digital, o que se encaixa diretamente na meta de fomentar inovação e infraestrutura tecnológica resiliente.

Leitura secundária defensável: **ODS 16 — Paz, Justiça e Instituições Eficazes**, cuja meta 16.10 trata de proteger o acesso à informação e liberdades fundamentais — aplicável porque o projeto atende diretamente a uma instituição de segurança e mira reduzir dano causado por vazamento de dado pessoal. Se o seu curso ou a monitoria tiver preferência por um dos dois, use esse; do contrário, ODS 9 é a escolha mais direta.

**Atividade que foi realizada** *(descrição do que o projeto do artigo fez — não é atividade sua)*:

O projeto atendeu a uma demanda de um Centro Especializado em Segurança Cibernética para consolidar e analisar dados de vazamentos de credenciais. A equipe coletou arquivos de texto com credenciais vazadas e desenvolveu um script em Python que percorre cada linha, identifica URLs (armazenadas como domínio) e separa e-mail de nome de usuário. As senhas foram criptografadas antes de entrarem no banco, e cada uma foi classificada por força (muito fraca a muito forte) usando a biblioteca `zxcvbn`, que também gera alertas do tipo "data facilmente adivinhável". Os dados — e-mail, usuário, domínio, senha, classificação — foram armazenados num banco PostgreSQL modelado como data warehouse em esquema estrela, com três tabelas fato separadas (e-mails, senhas, fontes de vazamento), especificamente para que nenhuma consulta consiga associar diretamente um e-mail à sua senha, mesmo que uma das tabelas seja comprometida. Esse banco alimenta uma dashboard interativa no Power BI, que permite buscar credenciais específicas, visualizar a distribuição de vazamentos por ano, por domínio afetado (destaque para verificações.io e facebook.com) e por categoria de dado exposto, usando como base 773 milhões de registros da API do Have I Been Pwned.

**Suas conclusões** *(aqui cabe voz própria)*:

O CyberLeak me chamou atenção porque dialoga direto com um tema que já venho estudando por conta própria: como transformar exposição de dados vazados em algo acionável, sem comprometer ainda mais a privacidade de quem já foi exposto. O que mais me impressionou na solução foi a decisão de separar e-mails e senhas em tabelas fato distintas dentro de um modelo estrela — uma escolha de modelagem que, por design, torna impossível reconstruir a associação entre credencial e senha mesmo que uma tabela seja comprometida. É o tipo de decisão que prioriza segurança desde a concepção do sistema, não como algo adicionado depois.

Também achei relevante o uso da API do Have I Been Pwned como fonte de dados: é a mesma referência que venho usando nas minhas próprias pesquisas sobre exposição de credenciais no Brasil, o que reforça que o problema que o CyberLeak ataca é real e amplamente reconhecido no mercado de segurança da informação. 773 milhões de registros vazados só nessa base mostram a escala do problema.

O projeto me deixou refletindo sobre até onde uma ferramenta de consulta de vazamento deveria ir. O CyberLeak resolve bem o problema de mapear e visualizar padrões — quais domínios vazam mais, quais senhas são mais fracas — mas isso é diferente de orientar a pessoa comum sobre o que fazer diante da própria exposição. É justamente essa lacuna, entre saber que os dados vazaram e saber como agir, que venho explorando num projeto pessoal de extensão em paralelo aos estudos.

**Anexo:** o PDF com as páginas 65–70 já está salvo em `pex-context/` (link na tabela acima) — anexe esse arquivo na entrega da plataforma.

---

## Antes de enviar

- [ ] Reler as conclusões e ajustar ao seu estilo de escrita (mesma observação de sempre: releia antes de assumir como pronto)
- [ ] Decidir entre ODS 9 e ODS 16 (ou os dois, se a caixa de entrega permitir mais de um) e preencher o campo real na plataforma
- [ ] Confirmar o mecanismo exato de envio na plataforma (campos separados vs. PDF único) — o roteiro sugere campos separados, mas confirme ao abrir a entrega real
- [ ] Responder o quiz de fixação (respostas já mapeadas na seção acima, mas confira se as opções batem exatamente com o que aparece na plataforma)
- [ ] Anexar o PDF do artigo (`pex-context/CyberLeak - artigo escolhido (Anais XI Mostra, p.65-70).pdf`)
- [ ] Confirmar prazo exato e número de tentativas para esta atividade específica
- [ ] Não deixar para o último dia do prazo
