# Guia de configuração — Estudo de Comportamento Digital (simulador de celular)

Este pacote contém o protótipo (`index.html`) do celular simulado para o seu TCC. É um arquivo único, sem dependências de build — basta hospedar e enviar o link. Funciona bem tanto em desktop (aparece como um mockup de celular na tela) quanto no celular real da pessoa (a "moldura" quase desaparece e o app ocupa a tela toda).

## 1. Antes de publicar

No arquivo `index.html`, troque **`GTM-XXXXXXX`** (aparece 2 vezes: no `<script>` do `<head>` e no `<noscript>` logo após `<body>`) pelo ID do seu contêiner GTM.

### Criar o contêiner GTM
1. Acesse [tagmanager.google.com](https://tagmanager.google.com) e crie uma conta → tipo **Web**.
2. Copie o ID gerado (formato `GTM-XXXXXXX`) e cole no HTML.
3. Crie uma propriedade GA4 em [analytics.google.com](https://analytics.google.com) e copie o **ID de medição** (formato `G-XXXXXXX`).
4. No GTM, crie uma tag do tipo **Configuração do Google Analytics: GA4**, cole o ID de medição, com acionador "All Pages".

## 2. Como funciona a simulação

A pessoa vê uma tela inicial de celular com ícones de apps (Órbita, Banco, E-mail, e alguns decorativos como Câmera, Ajustes, Calculadora — que servem de isca/distração). Um aviso fora do celular (como se fosse a instrução de um moderador de teste) diz o que fazer a cada desafio; o celular em si não dá dicas — só o ícone "💡 Dica" que aparece depois de 18s parado, e o link para pular o desafio depois de 50s.

**Ordem lógica:** ajustei a ordem dos desafios 2 e 3 em relação ao que fechamos antes — primeiro a pessoa compra (usando a aba Loja, ainda logada), e só depois mexe na conta e sai (aba Conta). Se a ordem fosse a original, ela sairia da conta antes de poder comprar, e teria que logar de novo — não fazia sentido de fluxo.

**Desafio 1 — Login (app Órbita):** mesmo fluxo de login + recuperação de senha de antes, agora dentro da tela do app depois de tocar no ícone "Órbita" na tela inicial.

**Desafio 2 — Comprar um produto (aba Loja, dentro do Órbita):** o mesmo componente de filtro (categoria, preço, estoque, chips) e o checkout com Cartão pré-selecionado mesmo pedindo Pix — agora acessado pela aba inferior "Loja" do app, não mais por um menu de topo.

**Desafio 3 — Gerenciar conta e sair (aba Conta, dentro do Órbita):** editar telefone → aumentar a fonte por um ícone "Aa" discreto (estilo Skoob) → sair da conta numa lista de 7 itens com "Sair" por último (estilo Amazon). Um detalhe novo: se a pessoa tentar usar o ícone "Ajustes" do sistema (tela inicial do celular) durante a etapa de aumentar a fonte, ela recebe uma mensagem explicando que esse app tem controle de texto próprio — isso mede a confusão real entre "configuração do celular" e "configuração dentro do app", que é uma fonte de erro genuína e comum.

**Desafio 4 — Fazer um Pix (app Banco):** tela de banco com saldo e ações rápidas (Pix, Pagar, Extrato, Cartões — só o Pix funciona, os outros são isca). Mesmo fluxo de Pix de antes (busca de contato, chave mascarada, revisão, dois botões de confirmar parecidos).

**Desafio 5 — Identificar mensagem suspeita (app E-mail):** a pessoa abre o app de e-mail, vê uma caixa de entrada com 4 mensagens e precisa abrir e "Marcar como suspeita" a correta. Cada mensagem aberta também tem um botão "Arquivar" ao lado (isca de CTA).

## 3. Eventos que o site já dispara (via `dataLayer`)

| Evento | Quando dispara | Parâmetros úteis |
|---|---|---|
| `step_view` | toda vez que uma etapa é exibida | `step_name`, `faixa_etaria`, `dispositivo` |
| `consent_given` | aceite do termo inicial | — |
| `profile_submitted` | faixa etária + afinidade preenchidas | `faixa_etaria`, `afinidade_tecnologica` |
| `app_opened` | toque em qualquer ícone da tela inicial do celular | `app`, `step_atual` |
| `cookie_consent` | escolha no pop-up de cookies do app Órbita | `escolha` |
| `form_error` | erro de validação (login, recuperação, perfil, Pix) | `task` |
| `ambiguous_cta_click` | clique num botão-distrator (Criar conta, Google, Ver detalhes, Confirmar e favoritar, Arquivar e-mail) | `task`, `cta` |
| `nav_item_click` | uso de "Esqueci minha senha", troca de aba no Órbita, item da lista de conta | `item`, `step_atual` |
| `decoy_click` | ícone de app errado, aba fora de ordem, item de isca na lista de conta, ação bancária decorativa | `item`, `step_atual` |
| `wrong_click` | produto errado no catálogo | `task`, `alvo_clicado` |
| `wrong_payment_method` | tenta finalizar sem trocar pra Pix | `escolhido` |
| `filter_applied` / `filter_cleared` | uso do filtro de produtos | `categorias`, `preco_min`, `preco_max` |
| `task_complete` | conclusão de cada um dos 5 desafios | `task`, `tempo_segundos`, `tentativas_isca`, `erros`, `usou_busca`, `usou_recuperacao`, `fonte_encontrada_via` |
| `accessibility_button_used` | uso do ícone "Aa" dentro do app | `step_atual` |
| `hint_shown` | dica exibida após 18s parado | `step_name` |
| `challenge_skipped` | toque em "pular este desafio" | `challenge` |
| `search_used` | busca de destinatário no Pix | `task` |
| `phishing_identified` | reporta corretamente o e-mail de golpe | `tempo_segundos`, `falsos_positivos` |
| `false_positive_report` | reporta por engano uma mensagem legítima | `item` |
| `rage_click` | 2+ cliques no mesmo elemento em menos de 700ms | `elemento`, `step_atual` |
| `sus_submitted` | envio do questionário de 4 perguntas | `q1`–`q4` |
| `flow_completed` | chegada à tela de resultado | `tempo_total_segundos`, `pontos_atrito`, `desafios_pulados`, `perfil_resultado` |
| `share_link_copied` | cópia do link do estudo | — |

Todos os eventos também vêm com `faixa_etaria` embutida (exceto os anteriores ao perfil).

## 4. Configurar as dimensões personalizadas no GA4

1. No GTM, edite a tag de configuração do GA4 → **Campos a serem definidos** → adicione `faixa_etaria` com valor `{{DLV - faixa_etaria}}` (crie a variável de camada de dados correspondente). Repita para `afinidade_tecnologica` e `dispositivo`.
2. No GA4, em **Administrador → Definições personalizadas**, registre as três como dimensões de escopo de evento.

## 5. Mapa de calor — Microsoft Clarity (gratuito)

1. Crie um projeto em [clarity.microsoft.com](https://clarity.microsoft.com) e cole o snippet no `<head>`.
2. Use `clarity('set', 'faixa_etaria', valor)` logo após `profile_submitted` no JS para rotular as sessões gravadas por faixa.

## 6. Onde hospedar (grátis)

- **Netlify Drop**: [app.netlify.com/drop](https://app.netlify.com/drop)
- **GitHub Pages** ou **Vercel** também funcionam para HTML estático.

## 7. Sugestão de análise para o TCC

- Tempo médio de conclusão por desafio, segmentado por `faixa_etaria`
- **Qual ícone a pessoa toca primeiro em cada desafio** (`app_opened` comparado ao app correto esperado) — mede reconhecimento de "qual app faz o quê", um comparativo geracional muito direto
- Confusão entre "Ajustes" do sistema e o controle de fonte do app (`decoy_click` com `item:'ajustes-sistema'`) por faixa — acho que esse pode ser um dos achados mais interessantes do estudo
- Tempo até achar "Sair" na lista de 7 itens (padrão Amazon) por faixa
- Uso correto do filtro antes de comprar, e troca do método de pagamento pré-selecionado, por faixa
- % que caiu no phishing do e-mail (`false_positive_report` ou não completar `phishing_identified`) por faixa
- Quantos desafios foram pulados (`challenge_skipped`) e em qual — provavelmente concentrado no Desafio 3 (achar "Sair"), independente de idade, o que seria um achado de design e não geracional
- Nota do SUS reduzido comparada ao desempenho objetivo, por faixa
- Mapas de calor (Clarity) comparando faixas etárias no mesmo desafio
