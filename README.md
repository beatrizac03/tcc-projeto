# Protótipo de Usabilidade Intergeracional

Protótipo web desenvolvido para apoiar uma pesquisa acadêmica sobre como
pessoas de diferentes faixas etárias e níveis de familiaridade com
tecnologia interagem com interfaces digitais.

A experiência simula o uso de um celular e de aplicativos cotidianos,
permitindo observar como participantes navegam, encontram
funcionalidades, tomam decisões e identificam possíveis riscos.

## Objetivo

Investigar aspectos de usabilidade e acessibilidade em diferentes
gerações por meio de tarefas práticas em um ambiente simulado. O
protótipo não busca avaliar o conhecimento tecnológico do participante,
mas observar a experiência de interação com as interfaces.

## Funcionalidades

-   **Consentimento inicial:** apresenta a proposta da pesquisa e
    solicita concordância voluntária antes do início.
-   **Perfil do participante:** registra faixa etária e nível de
    familiaridade com tecnologia, sem solicitar nome ou e-mail real.
-   **Simulação de celular:** oferece uma tela inicial com aplicativos
    fictícios, como Vitrine, Banco e E-mail.
-   **Cinco desafios interativos:** tarefas envolvendo recuperação de
    acesso, configurações de conta, compra em uma loja virtual,
    pagamento via Pix e identificação de mensagem suspeita.
-   **Avaliação final:** coleta respostas sobre a experiência após a
    realização dos desafios.
-   **Métricas da sessão:** apresenta o tempo total, o tempo médio por
    desafio concluído e a quantidade de desafios pulados.
-   **Eventos de interação:** envia eventos para `window.dataLayer`,
    permitindo integração com o Google Tag Manager quando ele estiver
    configurado no ambiente.
-   **Modo de depuração:** permite abrir etapas específicas por
    parâmetros na URL para facilitar testes.

## Desafios

1.  **Recuperação de acesso:** entrar na Vitrine recuperando a senha por
    e-mail.
2.  **Configurações da conta:** atualizar informações, localizar o
    ajuste de tamanho do texto e sair da conta.
3.  **Compra na Vitrine:** encontrar uma cadeira entre R\$ 400 e R\$ 700
    que esteja em estoque e gerar o código Pix.
4.  **Pagamento no banco:** usar o Pix no aplicativo Banco para pagar o
    pedido simulado.
5.  **Segurança no e-mail:** analisar as mensagens recebidas e marcar
    uma mensagem suspeita.

Cada desafio pode ser concluído ou pulado. O protótipo registra
informações de duração e algumas ações relevantes para a análise da
interação.

## Tecnologias

-   HTML5
-   CSS
-   JavaScript
-   `window.dataLayer` para disponibilizar eventos de interação a
    ferramentas de análise, como o Google Tag Manager

O protótipo é executado no navegador e não depende de um processo de
compilação JavaScript.

## Como executar

1.  Baixe ou clone os arquivos do projeto.
2.  Mantenha os arquivos de interface na mesma pasta, incluindo
    `index.html` e o arquivo `style.css` referenciado pelo HTML, caso
    ele faça parte da versão completa do projeto.
3.  Abra `index.html` em um navegador atualizado.
4.  Na tela inicial, marque o consentimento e clique em **Começar**.
5.  Selecione a faixa etária e a familiaridade com tecnologia para
    iniciar a experiência.

> **Observação:** o HTML referencia `style.css`. Caso o arquivo não
> esteja presente, será necessário incluí-lo para carregar os estilos
> externos esperados pela página.

## Modo de depuração

É possível abrir uma etapa específica adicionando parâmetros à URL. O
parâmetro `debug=verdade` habilita esse comportamento.

Exemplos:

  URL                                        Tela aberta
  ------------------------------------------ ------------------------
  `index.html?debug=verdade&desafio=1`       Desafio 1
  `index.html?debug=verdade&desafio=2`       Desafio 2
  `index.html?debug=verdade&desafio=3`       Desafio 3
  `index.html?debug=verdade&desafio=4`       Desafio 4
  `index.html?debug=verdade&desafio=5`       Desafio 5
  `index.html?debug=verdade&etapa=profile`   Perfil do participante
  `index.html?debug=verdade&etapa=sus`       Avaliação final
  `index.html?debug=verdade&etapa=result`    Resultado e métricas

Os parâmetros de depuração servem para testes e não substituem a
execução normal da pesquisa.

## Eventos e análise

O protótipo inicializa `window.dataLayer` e adiciona eventos por meio de
uma função JavaScript. Entre os eventos utilizados estão:

-   `step_view`: visualização de uma etapa;
-   `task_skipped`: desafio pulado;
-   `wrong_product`: seleção de produto que não atende aos critérios do
    desafio;
-   `decoy_click`: interação com um elemento distrator.

Os eventos podem incluir parâmetros sobre a etapa, o desafio, a duração
ou a ação realizada. Para visualizar os eventos, abra as ferramentas de
desenvolvedor do navegador e confira o console. Para enviar os dados ao
Google Tag Manager, configure o contêiner e as tags correspondentes no
ambiente de publicação.

## Privacidade e cuidados na aplicação

-   O protótipo orienta o participante a não inserir dados pessoais
    reais.
-   Use apenas informações fictícias durante os testes.
-   Antes de aplicar a pesquisa, confira o texto de consentimento e os
    procedimentos de armazenamento e tratamento dos dados.
-   A presença de `window.dataLayer` não significa, por si só, que os
    eventos estejam sendo enviados para uma plataforma externa; isso
    depende da configuração de análise do ambiente.

## Status

Protótipo acadêmico para testes de usabilidade. Recomenda-se testar os
fluxos completos em navegadores e dispositivos diferentes antes de
utilizá-lo em sessões de pesquisa.

------------------------------------------------------------------------

Desenvolvido como apoio a uma pesquisa acadêmica sobre usabilidade e
acessibilidade em diferentes faixas etárias.
