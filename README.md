# Conta da Casa

A conta que a casa não mostra: um assistente e um simulador para entender quanto do dinheiro apostado fica com a casa.

Projeto acadêmico da disciplina **Projetos III** (graduação em Economia, 2º semestre de 2026).
Site educativo que reúne:

- **Crupiê**, o assistente de IA (feito no Typebot), que responde dúvidas sobre apostas esportivas com base na base de conhecimento do grupo e indica as fontes;
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
- **Crupiê (assistente):** o `index.html` carrega o widget oficial do Typebot (`@typebot.io/js`). A conversa acontece nos servidores do Typebot e do provedor do modelo de IA, não no GitHub.

## Como o assistente está conectado

O `index.html` carrega o bot publicado em `https://typebot.co/my-typebot-grcdism`. Para trocar de bot:

1. No Typebot, abra o bot e vá em **Share → HTML & Javascript**.
2. No código mostrado, copie o valor que aparece depois de `typebot:`.
3. No `index.html`, procure `const TYPEBOT_ID =` e substitua o valor entre aspas pelo novo.

Mudanças feitas dentro do Typebot (textos, base de conhecimento, instruções) aparecem no site sem mexer neste repositório, desde que você clique em **Publish** no Typebot.

## Identidade visual

- **Nome:** Conta da Casa. **Assistente:** Crupiê.
- **Logo:** uma perna chutando uma ficha de pôquer como se fosse uma bola de futebol (desenhada em SVG dentro do `index.html`).
- **Cores:** branco e tinta escura para leitura; verde de feltro (`#0F5132`) como fundo do simulador, a "mesa"; nele, o painel verde-claro "Seus dados" reúne os campos que a pessoa pode alterar e as folhas brancas mostram os resultados; vermelho (`#B3261E`) só para a margem da casa e para perdas. O site também tem tema escuro, que segue a configuração do aparelho; os painéis do simulador continuam claros, como o chat do Crupiê.
- **Fontes (Google Fonts, gratuitas):** Big Shoulders Display nos títulos e números grandes; Figtree nos textos.
- **Movimento:** discreto (entrada do topo, recibo "impresso" e revelação das seções ao rolar) e desligado para quem ativa "reduzir movimento" no aparelho.
- Não há fotos de apostas, cassinos ou estádios, para não deixar o tema atraente.

## Cálculos do simulador

O simulador usa os três resultados de um jogo de futebol (vitória do time da casa, empate e vitória do time visitante) ou, no botão **Sem empate**, só os dois resultados sem o empate (como no vôlei). Ao trocar de opção, se as odds de casa e visitante ainda forem as do exemplo, elas mudam para o exemplo da outra opção (sem empate: 1,80 e 2,00); odds digitadas pela pessoa ficam como estão. Para odds decimais $O_i$ dos resultados que entram na conta:

- Probabilidade implícita: $q_i = 1/O_i$
- Soma: $S = \sum_i q_i$; margem da casa: $S - 1$
- Probabilidade sem margem (a usada pelo simulador): $p_i = q_i / S$
- **Premissa:** a casa de apostas tem mais dados e especialistas que o apostador, então a probabilidade das odds sem a margem é tratada como a melhor aproximação da chance real do evento. O simulador não aceita uma probabilidade digitada pela pessoa.
- Para uma aposta de valor $V$ no resultado $i$, com ganho líquido $G = V(O_i - 1)$:
  - Valor esperado: $E = pG - (1-p)V$
  - Desvio-padrão: $\sigma = \sqrt{p(G-E)^2 + (1-p)(-V-E)^2}$
- Critério de Kelly: $f^* = \max\left(0,\; p - \frac{1-p}{O_i - 1}\right)$
- Monte Carlo: 300 sequências de apostas independentes; em cada aposta, ganha com probabilidade $p$. Só se aposta quando a banca cobre o valor da aposta: se a banca fica menor que o valor da aposta, ela para ali. Mostra média, melhor e pior trajetória e a proporção de trajetórias com lucro, com prejuízo e que ficaram sem dinheiro para apostar (no valor fixo, banca menor que o valor da aposta; no % da banca, menos de R$ 1). Proporções acima de 0% e abaixo de 0,5% aparecem como "menos de 1%" (e o contrário como "mais de 99%"), para 1 trajetória em 300 não virar "0%". O eixo do gráfico usa valores redondos e não mostra valores negativos.
- O critério de Kelly aparece apenas como comparação, não como recomendação. Com a probabilidade sem margem, ele sempre dá zero (não apostar), porque o valor esperado é negativo em qualquer resultado: $E = V(1/S - 1)$.

## Entradas e validação

- Números podem ser digitados com vírgula ou ponto (`2,10` ou `2.10`). Em valores em reais, `1.500` é lido como mil e quinhentos.
- Banca maior que zero; aposta maior que zero e não maior que a banca; percentual entre 0 e 100; odds maiores que 1; número de apostas inteiro entre 5 e 150. Textos como "1500 reais" são aceitos; letras no lugar de números geram "Digite só o número, sem letras".
- Os erros aparecem quando a pessoa para de digitar (depois de 0,7 s) ou sai do campo, para não piscar erro no meio da digitação; enquanto isso, o último resultado continua na tela.
- Um número de apostas inválido esconde só a Simulação de Monte Carlo; a conta da aposta continua aparecendo.
- Se a margem da casa passar de 20%, aparece um aviso (sem bloquear a conta), porque isso costuma ser odd digitada errado ou a opção com/sem empate trocada.
- Se as probabilidades implícitas somam menos de 100%, o simulador não calcula e avisa que alguma odd provavelmente foi digitada errado, porque numa mesma casa essa soma sempre passa de 100%. No modo sem empate, o aviso lembra de conferir se as odds são de um jogo sem empate.
- No modo sem empate, a odd do empate fica escondida e não é validada nem usada na conta.

## Limitações conhecidas

- Supor que a probabilidade sem margem se aproxima da chance real é uma simplificação: a chance verdadeira do evento não é conhecida.
- As apostas simuladas são independentes e têm sempre as mesmas odds.
- A simulação usa 300 trajetórias: as porcentagens mudam um pouco a cada execução (o botão "Rodar novamente" mostra essa variação).
- O plano gratuito do Typebot tem limite mensal de conversas, e o modelo de IA gratuito tem limite diário de requisições.

## Verificação

- As fórmulas exibidas foram conferidas contra um cálculo independente em 13 cenários: 9 com empate (jogos equilibrados e com favorito forte, apostas na casa, no empate e no visitante, valor fixo, % da banca e mercado sem margem) e 4 sem empate (odds iguais, favorito forte, % da banca e mercado sem margem).
- A troca entre com e sem empate foi testada em computador e celular: esconde a linha e a opção do empate, passa quem tinha marcado o empate para a vitória da casa, não apaga odds digitadas e funciona pelo teclado.
- Antes da divulgação, o site, o simulador e o Crupiê passaram por uma rodada de testes com 40 a 50 simulações de usuário (personas de apostador médio em celular, tablet e computador), avaliadas com as perguntas do formulário de satisfação do grupo. As falhas encontradas (balões "?" cortados no celular, erros piscando durante a digitação, porcentagens arredondadas para 0%, entre outras) foram corrigidas e têm testes automáticos.
- A simulação foi comparada com o cálculo exato (distribuição binomial e programação dinâmica com a regra de parar sem dinheiro) em centenas de milhares de trajetórias.
- Acessibilidade verificada com axe-core (critérios WCAG 2.2 AA), nos temas claro e escuro, e layout testado em telas de 320 px a 1280 px.
