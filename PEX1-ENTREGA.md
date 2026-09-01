# PROJETO DE EXTENSÃO I — CONHECENDO A EXTENSÃO

**Aluno:** Pedro Henrique Braga de Lima
**Curso:** Tecnologia em Análise e Desenvolvimento de Sistemas
**Instituição:** Centro Universitário União das Américas Descomplica
**RA / Matrícula:** 2443545
**Data:** 01/09/2026

---

## 1. Artigo selecionado

**Título:** CyberLeak: Análise de Padrões de Segurança Digital

**Autores:** Anthony Gabriel Kuhnen Rodrigues, Bouchra Assad Akl Abou-Itaif, Guilherme Narde da Lapa, Gustavo Piegat Glizt da Silva, Jean Felipe Moschen Buss e Vinícius Gabriel Aquino Ferreira (acadêmicos); Larissa Daiana de Almeida Barbado (mentora); Ruminiki Schmoeller e Isabel Fernandes de Souza (docentes).

**Fonte:** Anais da XI Mostra de Projetos Integradores de Extensão, Centro Universitário União das Américas Descomplica, páginas 65 a 70.

**Por que este artigo:** é um projeto da área de conhecimento do meu curso, Análise e Desenvolvimento de Sistemas, envolvendo engenharia de dados, banco de dados e Business Intelligence aplicados à segurança da informação.

---

## 2. ODS trabalhado

**ODS 9 — Indústria, Inovação e Infraestrutura**

O objetivo 9 da ONU propõe "construir infraestruturas resilientes, promover a industrialização inclusiva e sustentável e fomentar a inovação". Além da infraestrutura física, ele abrange a infraestrutura tecnológica e digital, incluindo acesso à tecnologia da informação, pesquisa, desenvolvimento e inovação.

O CyberLeak se encaixa nesse objetivo porque constrói uma peça concreta de infraestrutura tecnológica, formada por um pipeline de tratamento de dados, um banco de dados modelado como data warehouse e uma dashboard analítica, com a finalidade de fortalecer a capacidade de resposta de um Centro Especializado em Segurança Cibernética diante de ameaças digitais. É inovação aplicada diretamente à infraestrutura de segurança, tornando resiliente um serviço do qual a comunidade depende.

---

## 3. Atividade que foi realizada

O projeto atendeu a uma demanda real de um Centro Especializado em Segurança Cibernética, que precisava consolidar e analisar dados de vazamentos de credenciais para entender padrões de exposição.

A equipe partiu de arquivos de texto contendo credenciais vazadas e desenvolveu um script em Python responsável por percorrer cada linha do conjunto, identificar as URLs de origem (armazenadas apenas como domínio) e separar o endereço de e-mail do nome de usuário. As senhas foram criptografadas antes de entrar no banco de dados e cada uma recebeu uma classificação de força, indo de muito fraca a muito forte, gerada pela biblioteca `zxcvbn`, que também emite alertas do tipo "data facilmente adivinhável" quando identifica padrões previsíveis.

Os dados tratados, ou seja, e-mail, nome de usuário, domínio, senha e classificação de força, foram armazenados em um banco PostgreSQL modelado como data warehouse em esquema estrela. A modelagem adotou uma decisão de segurança relevante: três tabelas fato separadas, uma para e-mails, uma para senhas e uma para fontes de vazamento, de forma que nenhuma consulta consiga associar diretamente um e-mail à sua respectiva senha, mesmo que uma das tabelas venha a ser comprometida.

Esse banco alimenta uma dashboard interativa construída em Power BI, que permite consultar credenciais específicas e visualizar a distribuição dos vazamentos por ano, por domínio afetado, com destaque para verificações.io e facebook.com, e por categoria de dado exposto. A base de referência do trabalho foi a API do Have I Been Pwned, que reúne cerca de 773 milhões de registros de credenciais vazadas.

---

## 4. Conclusões

O CyberLeak me chamou atenção porque dialoga direto com um tema que já venho estudando por conta própria: como transformar a exposição de dados vazados em algo acionável, sem comprometer ainda mais a privacidade de quem já foi exposto.

O que mais me impressionou na solução foi a decisão de separar e-mails e senhas em tabelas fato distintas dentro do modelo estrela. É uma escolha de modelagem que, por si só, torna impossível reconstruir a associação entre credencial e senha ainda que uma das tabelas seja comprometida. Esse tipo de decisão prioriza segurança desde a concepção do sistema, e não como algo acrescentado depois que o problema aparece. Como estudante de Análise e Desenvolvimento de Sistemas, foi o ponto do artigo que mais me fez repensar a forma como costumo modelar dados sensíveis.

Também achei relevante o uso da API do Have I Been Pwned como fonte de dados. É a mesma referência que venho consultando em pesquisas próprias sobre exposição de credenciais no Brasil, o que reforça que o problema atacado pelo CyberLeak é real e amplamente reconhecido no mercado de segurança da informação. Os 773 milhões de registros presentes só nessa base dão a dimensão da escala do problema.

Por fim, o projeto me deixou refletindo sobre até onde uma ferramenta de consulta de vazamento deveria ir. O CyberLeak resolve bem o problema de mapear e visualizar padrões, mostrando quais domínios vazam mais e quais senhas são mais frágeis, mas isso ainda é diferente de orientar a pessoa comum sobre o que fazer diante da própria exposição. É justamente essa lacuna, entre saber que os dados vazaram e saber como agir, que pretendo explorar nos próximos projetos de extensão do curso.

Vejo aqui exatamente a ponte entre universidade e sociedade que o roteiro descreve: um conhecimento técnico que existia dentro da academia, aplicado a uma demanda concreta de uma organização externa, gerando um resultado que essa organização não teria produzido sozinha.

---

## 5. Anexo

Artigo selecionado, CyberLeak: Análise de Padrões de Segurança Digital, Anais da XI Mostra de Projetos Integradores de Extensão, páginas 65 a 70, anexado a este documento.
