# Entenda as apostas esportivas: assistente e simulador

Projeto acadêmico da disciplina **Projetos III** (graduação em Economia, 2º semestre de 2026).
Site educativo que reúne:

- **Assistente de IA** (feito no Typebot), que responde dúvidas sobre apostas esportivas com base na base de conhecimento do grupo e indica as fontes;
- **Simulador financeiro**, que calcula probabilidade implícita, margem da casa, valor esperado, risco (desvio-padrão), critério de Kelly e uma simulação de Monte Carlo com 300 trajetórias.

O site não incentiva apostas e não oferece aconselhamento financeiro, psicológico ou jurídico.

## Arquivos

| Arquivo | Para que serve |
|---|---|
| `index.html` | O site inteiro: textos, estilo, simulador e conexão com o Typebot |
| `README.md` | Este arquivo: explica o projeto e como mantê-lo |

## Como o site funciona

- **Hospedagem:** GitHub Pages (gratuito para repositórios públicos). O GitHub entrega o `index.html` para quem acessa o link.
- **Simulador:** roda inteiramente no navegador de quem acessa (JavaScript). Nenhum valor digitado é enviado para servidor algum.
- **Assistente:** o `index.html` carrega o widget oficial do Typebot (`@typebot.io/js`). A conversa acontece nos servidores do Typebot e do provedor do modelo de IA, não no GitHub.

## Como o assistente está conectado

O `index.html` carrega o bot publicado em `https://typebot.co/my-typebot-grcdism`. Para trocar de bot:

1. No Typebot, abra o bot e vá em **Share → HTML & Javascript**.
2. No código mostrado, copie o valor que aparece depois de `typebot:`.
3. No `index.html`, procure `const TYPEBOT_ID =` e substitua o valor entre aspas pelo novo.

Mudanças feitas dentro do Typebot (textos, base de conhecimento, instruções) aparecem no site sem mexer neste repositório, desde que você clique em **Publish** no Typebot.

## Cálculos do simulador

Para um mercado com odds decimais $O_1, \dots, O_k$:

- Probabilidade implícita: $q_i = 1/O_i$
- Soma: $S = \sum_i q_i$; margem da casa: $S - 1$
- Probabilidade sem margem (usada por padrão): $p_i = q_i / S$
- Para uma aposta de valor $V$ no resultado $i$, com ganho líquido $G = V(O_i - 1)$:
  - Valor esperado: $E = pG - (1-p)V$
  - Desvio-padrão: $\sigma = \sqrt{p(G-E)^2 + (1-p)(-V-E)^2}$
- Critério de Kelly: $f^* = \max\left(0,\; p - \frac{1-p}{O_i - 1}\right)$
- Monte Carlo: 300 sequências de apostas independentes; em cada aposta, ganha com probabilidade $p$. Mostra média, melhor e pior trajetória e a proporção de trajetórias com lucro e com prejuízo.

## Limitações conhecidas

- A probabilidade sem margem é uma estimativa, não a chance verdadeira do evento.
- As apostas simuladas são independentes e têm sempre as mesmas odds.
- No modo "valor fixo", a simulação continua apostando mesmo quando a banca fica menor que o valor da aposta, então a banca pode ficar negativa.
- O plano gratuito do Typebot tem limite mensal de conversas, e o modelo de IA gratuito tem limite diário de requisições.
