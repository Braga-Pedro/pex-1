# Abstenção — saber quando não sabe

Este é o documento central do projeto. Tudo o mais é encanamento.

---

## O problema

Um sistema de suporte que responde errado com convicção é **pior que nenhum sistema**. Não porque erra — humanos erram — mas porque destrói a confiança do time. Basta uma resposta confiante e errada para que ninguém volte a usar, e o projeto morre com métricas de deflexão bonitas.

O dado de mercado é direto: mesmo com RAG, modelos frontier alucinam **4–9%** em fidelidade ao contexto recuperado. RAG reduz alucinação; não elimina.

A boa notícia: *"confidence-calibrated abstention with models trained to say 'I don't know' reduce false-confident hallucinations 2-5x"*.

---

## A restrição que define o desenho

**O modelo é o Claude Code do usuário. Não há prompt de sistema sob nosso controle.**

Parece limitação, mas força a solução correta. Se a abstenção dependesse de uma instrução ("responda apenas com base nos trechos fornecidos"), ela seria:

- ignorável pelo modelo
- invisível se o usuário tiver outras instruções conflitantes
- impossível de testar de forma determinística

Colocando a abstenção **no formato do dado retornado**, o modelo não pode ignorar o que não recebeu. Se a ferramenta não devolve conteúdo, não há o que alucinar. Se devolve um sinal explícito de baixa confiança junto de trechos fracos, o modelo tem base concreta para recusar.

> Controle o dado, não o prompt.

---

## As quatro camadas

### 1. Score explícito no retorno

Todo trecho carrega sua pontuação de similaridade, e o retorno carrega um veredito agregado:

| `confianca` | Quando | Efeito pretendido |
|---|---|---|
| `alta` | Melhor score ≥ limiar alto, ≥ 2 trechos concordantes | Responde normalmente |
| `media` | Score intermediário, ou fonte única | Responde com ressalva explícita |
| `baixa` | Melhor score < limiar baixo | Não há base — sugere escalar |

Os limiares saem da calibração com dados reais (ver [`03-evals.md`](03-evals.md)), nunca de chute. Valores iniciais para calibrar em cima, não para usar direto.

### 2. Citação obrigatória por construção

A ferramenta **nunca devolve prosa**. Devolve trechos com `{conteudo, fonte, url, atualizado_em}`. Isso significa que qualquer afirmação que o modelo faça pode ser rastreada até um documento — e que uma afirmação sem trecho de suporte é visivelmente inventada.

É a diferença entre "o sistema disse X" e "o sistema disse X, segundo esta thread de 12/03 no canal #suporte". A segunda forma é verificável pelo usuário em dois cliques.

### 3. Sinal de obsolescência

Todo trecho carrega `atualizado_em` e, quando passa do limite configurado, `possivelmente_desatualizado: true`.

Isso responde ao alerta de mercado: *"deflection quality decays if content is not actively maintained"*. Sem esse sinal, a base envelhece silenciosamente e o sistema passa a responder com confiança sobre um comportamento que mudou há seis meses — o pior modo de falha possível, porque parece funcionar.

### 4. Instrução embutida no retorno

Quando a confiança é baixa, o campo `observacao` diz explicitamente o que fazer:

```json
{
  "confianca": "baixa",
  "trechos": [],
  "observacao": "Não há base suficiente na documentação indexada para responder com segurança. Informe isso ao usuário e ofereça a ferramenta escalar_para_humano."
}
```

Não é prompt de sistema — é conteúdo de tool result, que o modelo sempre lê. É o canal de instrução que resta quando não se controla o prompt.

---

## O tradeoff, explicitamente

Limiar alto reduz alucinação e **aumenta abstenção inútil** — o sistema recusa perguntas que sabia responder. Limiar baixo faz o oposto.

Não existe configuração ótima universal; existe a calibração certa para este time. E ela muda conforme a base cresce.

**Ponto de partida sugerido:** errar para o lado da abstenção. Um sistema que às vezes diz "não sei" e manda falar com humano é confiável e continua sendo usado. Um que às vezes mente é abandonado. Com a confiança estabelecida, dá para afrouxar.

Medir os **dois lados** do tradeoff, sempre. Relatório que mostra só a taxa de alucinação está escondendo metade do problema.

---

## O que não é abstenção

Três armadilhas comuns:

- **Hedging não é abstenção.** "Pode ser que o webhook use POST, mas não tenho certeza" é o pior dos mundos: passa informação possivelmente errada com uma ressalva que o leitor ignora. Ou há base, ou não há.
- **Abstenção não é desculpa para retrieval ruim.** Se o sistema abstém em 60% das perguntas, o problema é a indexação, não o limiar. Abstenção é rede de segurança, não substituto de qualidade.
- **Escalar não é abstenção completa.** O escalonamento deve levar contexto: a pergunta, o que foi buscado, os melhores trechos encontrados e por que foram insuficientes. Escalonamento sem contexto só transfere o trabalho.

---

## Como isso é verificado

Detalhes em [`03-evals.md`](03-evals.md). A métrica que governa o projeto:

> **False confident rate** — proporção de respostas dadas com confiança `alta` que estavam erradas. Meta: **< 2%**.

Todas as outras métricas são secundárias a esta.
