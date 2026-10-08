# Guia de configuração — Estudo de Comportamento Digital

Este pacote contém o protótipo (`index.html`) do site-laboratório para o seu TCC. É um arquivo único, sem dependências de build — basta hospedar e enviar o link.

## 1. Antes de publicar

No arquivo `index.html`, troque **`GTM-XXXXXXX`** (aparece 2 vezes: no `<script>` do `<head>` e no `<noscript>` logo após `<body>`) pelo ID do seu contêiner GTM.

### Criar o contêiner GTM
1. Acesse [tagmanager.google.com](https://tagmanager.google.com) e crie uma conta → tipo **Web**.
2. Copie o ID gerado (formato `GTM-XXXXXXX`) e cole no HTML.
3. Crie uma propriedade GA4 em [analytics.google.com](https://analytics.google.com) e copie o **ID de medição** (formato `G-XXXXXXX`).
4. No GTM, crie uma tag do tipo **Configuração do Google Analytics: GA4**, cole o ID de medição, com acionador "All Pages" (Todas as páginas).

## 2. Os 5 desafios do fluxo atual

O protótipo simula um app fictício ("Órbita") com cabeçalho de navegação persistente (menus "Serviços" e "Minha Conta", mais um sino de notificações) a partir do Desafio 2. Cada desafio, depois de 18s parado, mostra uma dica discreta; depois de 50s, mostra um link "Não estou conseguindo — pular este desafio", que avança pro próximo preservando os dados já coletados.

**Desafio 1 — Entrar na conta:** a pessoa recebe um e-mail e uma senha propositalmente errada (simula "esqueci minha senha"); a tela de login rejeita a senha e ela precisa achar o link "Esqueci minha senha", passar por confirmação de e-mail → código (mostrado na tela, simulado) → nova senha → voltar e logar com a senha nova. Botões decoy: "Criar conta" e "Entrar com Google" (inertes, só geram evento).

**Desafio 2 — Gerenciar sua conta:** três etapas em sequência — (1) editar telefone no formulário "Meus dados"; (2) aumentar o tamanho do texto usando um ícone "Aa" discreto ao lado de um bloco de texto (inspirado no Skoob — afordance por ícone, não por menu); (3) sair da conta usando o menu "Minha Conta", que tem 8 itens e o "Sair" fica por último (inspirado no padrão da Amazon, que esconde o logout no fim de uma lista longa).

**Desafio 3 — Comprar um produto:** usa "Serviços → Comprar produtos" para abrir um componente de filtro de verdade (categoria em checkbox, faixa de preço, disponibilidade, chips de filtro ativo removíveis) — a pessoa precisa filtrar corretamente e ainda diferenciar "Cadeira Ipê" de duas variações de nome parecido ("Slim" e "Premium") que passam pelo mesmo filtro. No checkout, o método de pagamento vem pré-selecionado em "Cartão de crédito" mesmo o desafio pedindo Pix — testa se a pessoa nota e troca antes de finalizar.

**Desafio 4 — Fazer um Pix:** busca de destinatário favorito, prévia da chave Pix mascarada (CPF/e-mail/telefone), valor com máscara de moeda, revisão + checkbox obrigatório, e dois botões de confirmação parecidos lado a lado ("Confirmar Pix" vs. "Confirmar e salvar como favorito").

**Desafio 5 — Identificar uma mensagem suspeita:** o sino de notificações mostra 3 mensagens legítimas (uma delas com tom de urgência realista, tipo alerta de segurança "quase-suspeito") e 1 phishing de prêmio. Testa se a pessoa reporta a certa sem cair na ambiguidade da mensagem de segurança.

## 3. Eventos que o site já dispara (via `dataLayer`)

| Evento | Quando dispara | Parâmetros úteis |
|---|---|---|
| `step_view` | toda vez que uma etapa é exibida | `step_name`, `faixa_etaria`, `dispositivo` |
| `consent_given` | usuário aceita o termo e clica em "Começar" | — |
| `profile_submitted` | usuário preenche faixa etária + afinidade | `faixa_etaria`, `afinidade_tecnologica` |
| `cookie_consent` | escolha no pop-up de cookies (aparece no Desafio 1) | `escolha` (aceitar_tudo / gerenciar_preferencias) |
| `form_error` | erro de validação (login, recuperação de senha, perfil, Pix) | `task` |
| `ambiguous_cta_click` | clique num botão-distrator (Criar conta, Entrar com Google, Ver detalhes, Confirmar e salvar como favorito) | `task`, `cta` |
| `nav_item_click` | qualquer clique em item do menu Serviços/Minha Conta, incluindo "Esqueci minha senha" | `item`, `step_atual` |
| `decoy_click` | clique num item de menu que é isca, ou em "Sair" fora do contexto do Desafio 2 | `item`, `step_atual` |
| `wrong_click` | clique no produto errado no catálogo | `task`, `alvo_clicado` |
| `wrong_payment_method` | tenta finalizar a compra sem trocar para Pix | `escolhido` |
| `filter_applied` / `filter_cleared` | uso do componente de filtro de produtos | `categorias`, `preco_min`, `preco_max` |
| `task_complete` | conclusão de cada um dos 5 desafios | `task`, `tempo_segundos`, `tentativas_isca`, `erros`, `usou_busca`, `usou_recuperacao`, `fonte_encontrada_via` |
| `accessibility_button_used` | usa o botão flutuante A+/A− OU o ícone "Aa" inline | `step_atual`, `origem` (floating / inline-icon) |
| `hint_shown` | dica exibida após 18s parado num desafio | `step_name` |
| `challenge_skipped` | usuário clica em "pular este desafio" | `challenge` |
| `search_used` | busca usada no Pix (destinatário) | `task` |
| `phishing_identified` | reporta corretamente a notificação de golpe | `tempo_segundos`, `falsos_positivos` |
| `false_positive_report` | reporta por engano uma notificação legítima | `item` |
| `rage_click` | 2+ cliques no mesmo elemento em menos de 700ms | `elemento`, `step_atual` |
| `sus_submitted` | envio do questionário de 4 perguntas | `q1`–`q4` |
| `flow_completed` | chegada à tela de resultado | `tempo_total_segundos`, `pontos_atrito`, `desafios_pulados`, `perfil_resultado` |
| `share_link_copied` | usuário copia o link para compartilhar | — |

Todos os eventos acima também já vêm com `faixa_etaria` embutida (exceto os anteriores ao preenchimento do perfil), então qualquer tag GTM disparada por eles pode enviar essa dimensão junto ao GA4.

## 4. Configurar as dimensões personalizadas no GA4

1. No GTM, edite a tag de configuração do GA4 → em **Campos a serem definidos**, adicione um campo `faixa_etaria` com valor `{{DLV - faixa_etaria}}` (crie essa variável de camada de dados em Variáveis → Nova → Variável de camada de dados, nome da variável `faixa_etaria`). Repita para `afinidade_tecnologica` e `dispositivo`.
2. No GA4, vá em **Administrador → Definições personalizadas → Dimensões personalizadas** e registre `faixa_etaria`, `afinidade_tecnologica` e `dispositivo` como dimensões de escopo de evento.
3. Isso permite segmentar qualquer relatório do GA4 (tempo na página, funil, eventos) por faixa etária — que é o comparativo central do seu TCC.

## 5. Mapa de calor — Microsoft Clarity (gratuito)

1. Crie um projeto em [clarity.microsoft.com](https://clarity.microsoft.com).
2. Cole o snippet de instalação do Clarity direto no `<head>` do `index.html` (ou publique-o como uma tag "HTML personalizado" dentro do próprio GTM, com acionador "All Pages").
3. Para comparar por faixa etária, use a API `clarity('set', 'faixa_etaria', valor)` logo após o usuário escolher a faixa (no trecho `profile_submitted` do JS), assim cada sessão gravada fica rotulada.

## 6. Onde hospedar (grátis)

- **Netlify Drop**: [app.netlify.com/drop](https://app.netlify.com/drop) — arraste a pasta e recebe um link em segundos.
- **GitHub Pages**: suba os arquivos num repositório e ative Pages nas configurações.
- **Vercel**: `vercel deploy` também funciona para HTML estático.

Qualquer uma gera um link único e estável — ideal para circular por 1 mês.

## 7. Sugestão de análise para o TCC

Com os dados de ~1 mês, os comparativos mais fortes para apresentar são:
- Tempo médio de conclusão por desafio, segmentado por `faixa_etaria`
- Taxa de uso da recuperação de senha (`usou_recuperacao` dentro do `task_complete` do login) por faixa — mede o quanto o erro proposital de senha gera dificuldade real
- Por qual caminho a pessoa achou o aumento de fonte — ícone inline (estilo Skoob) vs. nenhum dos dois — via `origem` em `accessibility_button_used`, comparado por faixa
- Tempo até achar "Sair" no Desafio 2 (padrão Amazon) por faixa — measure de quanto a posição no fim de uma lista longa penaliza usuários menos familiarizados
- Uso correto do componente de filtro (`filter_applied` antes do `task_complete` da compra) vs. quem tenta sem filtrar, por faixa
- Quantos trocaram de "Cartão de crédito" para "Pix" sem errar (`wrong_payment_method`) por faixa — teste de atenção a default pré-selecionado
- Cliques em isca de menu (`decoy_click`) e em produto errado (`wrong_click`) por faixa
- Cliques em CTA ambígua (`ambiguous_cta_click`) por faixa — mede confusão entre botão certo e distrator
- Escolha no pop-up de cookies (`cookie_consent`) por faixa — mede fadiga de consentimento
- % que caiu no phishing simulado — reportou a notificação errada (`false_positive_report`) ou não completou `phishing_identified` — provavelmente o dado mais forte do estudo para o tema de vulnerabilidade digital por geração
- Quantos desafios foram pulados (`challenge_skipped`) por faixa, e em qual desafio a maioria desiste
- Taxa de conclusão do fluxo (`flow_completed` ÷ `consent_given`) por faixa — mede abandono silencioso vs. desistência consciente
- Nota média do SUS reduzido (`sus_submitted`) por faixa, comparada ao desempenho objetivo (tempo/erros) — vale checar se há dissociação entre percepção e desempenho real
- Mapas de calor lado a lado (Clarity) comparando uma faixa etária jovem vs. uma mais velha no mesmo desafio
