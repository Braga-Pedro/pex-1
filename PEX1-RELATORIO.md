# PEX 1 — Rascunho do relatório

## Artigo escolhido

| | |
|---|---|
| **Título** | Lei Geral de Proteção de Dados (LGPD): a relação entre as políticas e os regimes de informação |
| **Autores** | Nadine Passos Conceição D'Oliveira; Francisco José Aragão Pedroza Cunha |
| **Periódico** | RDBCI: Revista Digital de Biblioteconomia e Ciência da Informação (indexado na SciELO) |
| **Ano** | 2024 |
| **DOI** | [10.20396/rdbci.v22i00.8675749](https://doi.org/10.20396/rdbci.v22i00.8675749) |
| **Link** | https://www.scielo.br/j/rdbci/a/DWntpkXMB9GgCPKycFcxtts/?lang=pt |

**Por que esse artigo.** Une o tema que você já pesquisa a fundo — segurança da informação e proteção de dados, o mesmo pano de fundo da linha `projeto/blindagem` deste repositório — com respaldo acadêmico avaliado (SciELO, DOI). O artigo é de Ciência da Informação, não de Computação estritamente; a ponte com ADS precisa ficar explícita na resposta (já está feita na pergunta 1 abaixo): a LGPD hoje é requisito de projeto de qualquer sistema que trate dado pessoal, que é exatamente o que um desenvolvedor de ADS constrói.

---

## Resumo

*Rascunho já em palavras próprias — releia e ajuste ao seu estilo antes de enviar; não é para colar direto.*

O artigo analisa a Lei Geral de Proteção de Dados (Lei 13.709/2018) não apenas como texto jurídico, mas através do conceito de "regime de informação", da Ciência da Informação. Uma forma de olhar como política, informação e poder se relacionam dentro de uma sociedade. Nessa perspectiva, um regime de informação é formado por atores sociais (quem produz e usa a informação), por dispositivos (as regras que definem o que pode e o que não pode), por artefatos (a tecnologia envolvida) e por ações de informação (como os dados efetivamente circulam entre as partes).

Os autores mostram que a LGPD funciona como um desses dispositivos, ela define papéis específicos para cada parte envolvida no tratamento de um dado pessoal. O titular (a pessoa a quem o dado pertence), o controlador (quem decide o que fazer com o dado), o operador (quem executa esse tratamento por conta do controlador), o encarregado (o intermediário entre a empresa, o titular e a Autoridade Nacional de Proteção de Dados) e a própria ANPD, como fiscalizadora. O conceito central que sustenta tudo isso é o da **autodeterminação informacional**: a ideia de que é a pessoa quem decide o que acontece com os próprios dados, não a empresa que os coletou.

O artigo também descreve o **ciclo de vida de um dado pessoal em cinco fases**: coleta, retenção, processamento, compartilhamento e eliminação. Mostra que a LGPD amplia bastante o que já existia na Lei de Acesso à Informação de 2011, que protegia só dados ligados a intimidade e honra. Hoje qualquer dado pessoal está sob esse guarda-chuva, com atenção redobrada para os chamados dados sensíveis (origem étnica, religião, orientação política, dado biométrico).

Por fim, os autores discutem dois instrumentos que colocam a lei em prática: o **Inventário de Dados Pessoais**, que documenta todo tratamento de dado feito por um sistema ou processo, e o **Relatório de Impacto à Proteção de Dados**, que avalia riscos e as medidas para reduzi-los. A conclusão principal é que a LGPD não deve ser lida como uma lei isolada, mas como parte de uma mudança maior, de um Estado burocrático para o que os autores chamam de Estado informacional, em que o controle sobre dado pessoal virou questão central de política pública.

---

## Perguntas do roteiro

*Estrutura genérica — confirme contra o roteiro real do seu perfil antes de enviar.*

### 1. Por que você escolheu esse artigo e como ele se conecta à sua área de formação?

Escolhi esse artigo porque segurança da informação e proteção de dados é um tema que já estudo com profundidade fora da sala de aula — venho pesquisando o assunto para um projeto pessoal de tecnologia voltado a proteger pessoas contra exposição de dados e os golpes que exploram essa exposição. O artigo aprofunda exatamente o arcabouço legal que qualquer sistema de tecnologia que lida com dado pessoal — de um e-commerce a um aplicativo de banco — precisa respeitar hoje. Em ADS, não existe mais projetar um sistema sem levar a LGPD em conta: ela define quem pode acessar o quê, por quanto tempo um dado pode ficar armazenado, e que direitos o usuário tem sobre a própria informação. Entender a lei pela lente dos "regimes de informação" ajudou a enxergar que não é uma exigência burocrática isolada, mas parte de como a própria relação entre empresa, usuário e Estado está sendo redesenhada.

### 2. Quais os principais aprendizados/conceitos que você extraiu da leitura?

- **Autodeterminação informacional** — o titular do dado, a pessoa, é quem deveria estar no centro da decisão sobre o próprio dado, não a empresa que o coletou.
- **Ciclo de vida do dado pessoal** (coleta → retenção → processamento → compartilhamento → eliminação) — um jeito prático de pensar onde, em cada etapa de um sistema, existe risco e responsabilidade.
- **A diferença entre controlador, operador e encarregado** — até ler o artigo, tratava esses termos como sinônimos; são papéis com responsabilidade jurídica diferente, e um sistema bem desenhado precisa deixar claro qual papel cada parte exerce.
- **Inventário de Dados Pessoais e Relatório de Impacto à Proteção de Dados** — instrumentos concretos, não só princípios abstratos: documentos que uma equipe de desenvolvimento efetivamente produz e mantém.

### 3. Como você aplicaria (ou já aplica) esse conhecimento na prática profissional?

Já aplico boa parte desse conhecimento num projeto pessoal de tecnologia que venho desenvolvendo: um assistente que orienta pessoas expostas por vazamento de dados sobre o que fazer para se proteger. Uma das decisões centrais do projeto — antes mesmo de ler este artigo — foi não armazenar nenhum dado pessoal identificável do usuário: nem CPF, nem nome, nem telefone. Lendo o artigo, percebi que essa escolha é, na prática, uma aplicação direta da autodeterminação informacional que os autores descrevem: em vez de pedir para o usuário confiar seus dados a mim, o sistema devolve a decisão para ele — orienta, mas não retém. O conceito de ciclo de vida do dado também mudou como penso a arquitetura: cada informação que um sistema manipula deveria ter uma resposta clara para "por que ela existe aqui, e quando ela deixa de existir aqui" — não só para dado sensível regulado por lei, mas como princípio geral de projeto.

---

## Evidência a anexar

- [ ] Print da página do artigo na SciELO, ou o PDF do artigo, conforme o roteiro pedir
- [ ] Conferir no roteiro real se ele pede link, print ou PDF — varia por perfil

## Antes de enviar

- [ ] Reescrever o resumo com suas próprias palavras — já está em rascunho autoral, mas releia e ajuste ao seu estilo; não copiar trechos do artigo (há declaração anti-plágio no envio)
- [ ] Conferir se as 3 perguntas acima batem com o roteiro real do seu perfil; ajustar quantidade e redação se for diferente
- [ ] Montar PDF único (resumo + respostas + evidência anexada)
- [ ] Marcar a declaração de autoria antes de enviar
- [ ] Não deixar para o último dia do prazo — sobra fôlego pra reenviar se não aprovar de primeira (3 tentativas dentro do prazo, correção em até 24h)
