# Guia de configuração — Estudo de Comportamento Digital

Este pacote contém o protótipo (`index.html`) do site-laboratório para o seu TCC. É um arquivo único, sem dependências de build — basta hospedar e enviar o link.

## 1. Antes de publicar

No arquivo `index.html`, troque **`GTM-XXXXXXX`** (aparece 2 vezes: no `<script>` do `<head>` e no `<noscript>` logo após `<body>`) pelo ID do seu contêiner GTM.

### Criar o contêiner GTM
1. Acesse [tagmanager.google.com](https://tagmanager.google.com) e crie uma conta → tipo **Web**.
2. Copie o ID gerado (formato `GTM-XXXXXXX`) e cole no HTML.
3. Crie uma propriedade GA4 em [analytics.google.com](https://analytics.google.com) e copie o **ID de medição** (formato `G-XXXXXXX`).
4. No GTM, crie uma tag do tipo **Configuração do Google Analytics: GA4**, cole o ID de medição, com acionador "All Pages" (Todas as páginas).

## 2. Eventos que o site já dispara (via `dataLayer`)

| Evento | Quando dispara | Parâmetros úteis |
|---|---|---|
| `step_view` | toda vez que uma etapa é exibida | `step_name`, `faixa_etaria`, `dispositivo` |
| `consent_given` | usuário aceita o termo e clica em "Começar" | — |
| `profile_submitted` | usuário preenche faixa etária + afinidade | `faixa_etaria`, `afinidade_tecnologica` |
| `task_complete` | conclusão de cada uma das 3 tarefas | `task`, `tempo_segundos`, `erros`, `usou_busca`, `cliques` |
| `form_error` | e-mail inválido no formulário da tarefa 2 | `campo` |
| `search_used` | usuário usa a busca na tarefa 3 | `task` |
| `wrong_click` | clica em produto errado na tarefa 3 | `alvo_clicado` |
| `accessibility_button_used` | usa o botão flutuante A+/A− | `step_atual` |
| `rage_click` | 2+ cliques no mesmo elemento em menos de 700ms (indício de frustração) | `elemento`, `step_atual` |
| `sus_submitted` | envio do questionário de 3 perguntas | `q1`, `q2`, `q3` |
| `flow_completed` | chegada à tela de resultado (fluxo 100% concluído) | `tempo_total_segundos`, `erros_formulario`, `usou_busca`, `perfil_resultado` |
| `share_link_copied` | usuário copia o link para compartilhar | — |

Todos os eventos acima também já vêm com `faixa_etaria` embutida (exceto os anteriores ao preenchimento do perfil), então qualquer tag GTM disparada por eles pode enviar essa dimensão junto ao GA4.

## 3. Configurar as dimensões personalizadas no GA4

1. No GTM, edite a tag de configuração do GA4 → em **Campos a serem definidos**, adicione um campo `faixa_etaria` com valor `{{DLV - faixa_etaria}}` (crie essa variável de camada de dados em Variáveis → Nova → Variável de camada de dados, nome da variável `faixa_etaria`). Repita para `afinidade_tecnologica` e `dispositivo`.
2. No GA4, vá em **Administrador → Definições personalizadas → Dimensões personalizadas** e registre `faixa_etaria`, `afinidade_tecnologica` e `dispositivo` como dimensões de escopo de evento.
3. Isso permite segmentar qualquer relatório do GA4 (tempo na página, funil, eventos) por faixa etária — que é o comparativo central do seu TCC.

## 4. Mapa de calor — Microsoft Clarity (gratuito)

1. Crie um projeto em [clarity.microsoft.com](https://clarity.microsoft.com).
2. Cole o snippet de instalação do Clarity direto no `<head>` do `index.html` (ou publique-o como uma tag "HTML personalizado" dentro do próprio GTM, com acionador "All Pages").
3. O Clarity já segmenta gravações de sessão automaticamente — mas para comparar por faixa etária, use a API `clarity('set', 'faixa_etaria', valor)` logo após o usuário escolher a faixa (no trecho `profile_submitted` do JS), assim cada sessão gravada fica rotulada.

## 5. Onde hospedar (grátis)

- **Netlify Drop**: [app.netlify.com/drop](https://app.netlify.com/drop) — arraste a pasta e recebe um link em segundos.
- **GitHub Pages**: suba os arquivos num repositório e ative Pages nas configurações.
- **Vercel**: `vercel deploy` também funciona para HTML estático.

Qualquer uma gera um link único e estável — ideal para circular por 1 mês.

## 6. Sugestão de análise para o TCC

Com os dados de ~1 mês, os comparativos mais fortes para apresentar são:
- Tempo médio de conclusão por tarefa, segmentado por `faixa_etaria`
- Taxa de erro no formulário (`form_error`) por faixa
- % que usou busca vs. navegação direta (`search_used`) por faixa
- Taxa de conclusão do fluxo (`flow_completed` ÷ `consent_given`) por faixa — mede abandono
- Uso do botão de acessibilidade (`accessibility_button_used`) por faixa — direto ao tema de acessibilidade
- Frequência de `rage_click` por faixa — indício de frustração/fricção
- Nota média do SUS reduzido (`sus_submitted`) por faixa
- Mapas de calor lado a lado (Clarity) comparando uma faixa etária jovem vs. uma mais velha na mesma tarefa
