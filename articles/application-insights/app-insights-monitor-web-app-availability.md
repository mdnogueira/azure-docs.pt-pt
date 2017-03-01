---
title: Monitorizar a disponibilidade e a capacidade de resposta de qualquer site | Microsoft Docs
description: "Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: c800f6e7b6bd1e17165146f981e32a8cbb251e3c
ms.openlocfilehash: af4343dbe23f314a85c98d7337f42c4b60b03c6a


---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Monitorizar a disponibilidade e a capacidade de resposta de qualquer site
Depois de implementar a aplicação Web ou o site em qualquer servidor, pode configurar testes Web para monitorizar a respetiva disponibilidade e capacidade de resposta. O [Azure Application Insights](app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Este ferramenta alerta-o se a aplicação não responder ou responder lentamente.

![Exemplo de teste Web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Pode configurar testes Web para qualquer ponto final HTTP ou HTTPS acessível a partir da Internet pública. Não tem de adicionar nada ao Web site que está a testar. Nem sequer tem de estar no seu site. Pode testar um serviço de API REST do qual dependa.

Existem dois tipos de testes Web:

* [Teste de ping do URL](#create): um teste simples que pode criar no Portal do Azure.
* [Teste Web de vários passos](#multi-step-web-tests): pode criá-lo no Visual Studio Enterprise e carregá-lo no Portal.

Pode criar até dez testes por recurso de aplicação.

## <a name="a-namecreatea1-create-a-resource-for-your-test-reports"></a><a name="create"></a>1. Criar um recurso para os relatórios de teste
Ignore este passo se já tiver [configurado um recurso do Application Insights][start] para esta aplicação e quiser ver os relatórios de disponibilidade no mesmo local.

Inicie sessão no [Microsoft Azure](http://azure.com), aceda ao [portal do Azure](https://portal.azure.com) e crie um recurso do Application Insights.

![Novo > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Clique em **Todos os recursos** para abrir o painel Descrição geral do novo recurso.

## <a name="a-namesetupa2-create-a-url-ping-test"></a><a name="setup"></a>2. Criar um teste de ping do URL
No recurso do Application Insights, procure o mosaico Disponibilidade. Clique no mosaico para abrir o painel Testes Web da aplicação e adicione um teste Web.

![Indique, pelo menos, o URL do seu site](./media/app-insights-monitor-web-app-availability/13-availability.png)

* O **URL** tem de estar visível na Internet pública. Pode incluir uma cadeia de consulta (assim, pode, por exemplo, testar um pouco a base de dados). Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.
* **Analisar pedidos dependentes**: imagens, scripts, ficheiros de estilo e outros recursos da página são pedidos como parte do teste e o tempo de resposta registado inclui todos esses períodos. O teste falha se todos estes recursos não puderem ser transferidos com êxito no tempo limite para a realização do teste completo.
* **Permitir repetição de tentativas**: quando o teste falhar, será feita uma nova tentativa após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. (Recomendamos esta definição. Em média, cerca de 80% das falhas desaparecem aquando da repetição.)
* **Frequência de teste**: define a frequência de execução do teste em cada localização de teste. Com uma frequência de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.
* As **Localizações de teste** são os locais a partir de onde os nossos servidores enviam pedidos Web ao seu URL. Escolha mais do que uma localização para poder diferenciar os problemas no site dos problemas de rede. Pode selecionar até 16 localizações.
* **Critérios de êxito**:

    **Tempo limite de teste**: reduza este valor para ser alertado de repostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.

    **Resposta HTTP**: o código de estado devolvido é contabilizado como um êxito. 200 é o código que indica que foi devolvida uma página Web normal.

    **Correspondência do conteúdo**: uma cadeia, como “Bem-vindo!” Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar.
* Por predefinição, são-lhe enviados **Alertas** em caso de falhas nas três localizações durante mais de cinco minutos. É provável que uma falha numa localização esteja relacionada com um problema de rede e não com um problema do seu site. Contudo, pode alterar o limiar para ser mais ou menos sensível, sendo que também pode alterar os destinatários dos e-mails.

    Pode configurar um [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) que será chamado sempre que for gerado um alerta. (Mas tenha em atenção que, atualmente, os parâmetros de consulta não são submetidos como Propriedades.)

### <a name="test-more-urls"></a>Testar mais URLs
Adicionar mais testes. Para o exemplo, tal como para o teste da sua home page, pode verificar se a sua base de dados está em execução testando o URL de uma pesquisa.

## <a name="a-namemonitora3-see-your-web-test-results"></a><a name="monitor"></a>3. Ver resultados do teste Web
Passados 1 a 2 minutos, os resultados são apresentados no painel Teste Web.

![Resultados do resumo no painel principal](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Clique em qualquer barra no gráfico de resumo para obter uma vista mais detalhada desse período de tempo.

Estes gráficos reúnem os resultados de todos os testes Web desta aplicação.

## <a name="a-namefailuresaif-you-see-failures"></a><a name="failures"></a>Se vir falhas
Clique num ponto vermelho.

![Clicar num ponto vermelho](./media/app-insights-monitor-web-app-availability/open-instance.png)


A partir de um resultado do teste Web, pode:

* Inspecionar a resposta recebida do seu servidor.
* Abra a telemetria enviada pela aplicação do servidor ao processar a instância do pedido falhado.
* Registe um problema ou item de trabalho no Git ou no VSTS para controlar o problema. O erro irá conter uma ligação para este evento.
* Abra o resultado do teste da Web no Visual Studio.


*Os resultados parecem estar OK mas foi reportada uma falha?* Verifique todas as imagens, scripts, folhas de estilo e outros ficheiros carregados pela página. Se qualquer um deles falhar, o teste é reportado como falhado, mesmo se a página principal HTML carregar corretamente.

*Não existem itens relacionados?* Poderá dever-se à [amostragem](app-insights-sampling.md) estar em funcionamento.

## <a name="multi-step-web-tests"></a>Testes Web com vários passos
Pode monitorizar um cenário que envolva uma sequência de URLs. Por exemplo, se estiver a monitorizar um site de vendas, pode testar se a adição de artigos no carrinho de compras funciona corretamente.

> [!NOTE] 
> Os testes Web de vários passos estão sujeitos a um custo. [Esquema do escalão de preço](http://azure.microsoft.com/pricing/details/application-insights/).
> 

Para criar um teste com vários passos, registe o cenário com o Visual Studio e, em seguida, carregue o registo no Application Insights. O Application Insights reproduz o cenário de tempos a tempos e verifica as respostas.

Tenha em atenção que não pode utilizar funções codificadas nos testes: os passos do cenário devem ser contidos como um script no ficheiro .webtest.

#### <a name="1-record-a-scenario"></a>1. Registar um cenário
Utilize o Visual Studio Enterprise para guardar uma sessão Web.

1. Crie um projeto de teste de desempenho da Web.

    ![No Visual Studio, crie um projeto a partir do modelo Desempenho da Web e Carregar Teste.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Abra o ficheiro .webtest e comece a gravar.

    ![Abrir o ficheiro .webtest e clicar em Gravar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Execute as ações de utilizador que pretende simular no teste: abrir o site, adicionar um produto ao carrinho e assim sucessivamente. Em seguida, pare o teste.

    ![O gravador de teste Web é executado no Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Não crie um cenário longo. Existe um limite de 100 passos e de 2 minutos.
4. Edite o teste para:

   * Adicionar validações para verificar o texto recebido e os códigos de reposta.
   * Remova todas as interações supérfluas. Também pode remover pedidos dependentes de imagens ou adicionar ou controlar sites.

     Lembre-se de que apenas pode editar o script de teste, não pode adicionar código personalizado ou chamar outros testes Web. Não insira ciclos no teste. Pode utilizar os plug-ins do teste Web padrão.
5. Execute o teste no Visual Studio para verificar se funciona.

    A execução de testes Web abre um browser e repete as ações que gravou. Verifique se funciona como esperado.

    ![No Visual Studio, abra o ficheiro .webtest e clique em Executar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Carregar o teste Web no Application Insights
1. No portal do Application Insights, crie um novo teste Web.

    ![No painel de testes Web, selecione Adicionar.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Selecione o teste com vários passos e carregue o ficheiro .webtest.

    ![Selecionar o teste Web com vários passos.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Defina as localizações do teste, a frequência e os parâmetros de alerta da mesma forma como para os testes de ping.

Veja os resultados do teste e as eventuais falhas da mesma forma como para os testes com URL único.

Um teste demasiado demorado é um motivo comum de falha. Não pode levar mais do dois minutos.

Não se esqueça de que todos os recursos de uma página devem ser carregados corretamente para que o teste seja bem sucedido, incluindo scripts, folhas de estilo, imagens e assim sucessivamente.

Tenha em atenção que o teste Web deve estar completamente contido no ficheiro .webtest: não pode utilizar funções codificadas no teste.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Ligar a hora e números aleatórios ao teste com vários passos
Suponhamos que está a testar uma ferramenta que obtém dados dependentes da hora, tais como stocks de um feed externo. Ao gravar o teste Web, tem de utilizar horas específicas, embora sejam definidas como parâmetros do teste, StartTime e EndTime.

![Um teste Web com parâmetros.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando executa o teste, gostaria que a EndTime fosse sempre a hora atual e a StartTime a hora de há 15 minutos.

Os Plug-ins de Teste Web permitem-lhe parametrizar tempos.

1. Adicione um plug-in de teste Web para cada valor variável de parâmetro que pretende. Na barra de ferramentas do teste Web, selecione **Adicionar Plug-in de Teste Web**.

    ![Escolher Adicionar Plug-in de Teste Web e selecionar um tipo.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Neste exemplo, utilizamos duas instâncias do Plug-in Data/Hora. Uma instância para “há&15; minutos” e outra para “agora”.
2. Abra as propriedades de cada plug-in. Atribua um nome e defina-o para utilizar a hora atual. Para uma das propriedades, defina Adicionar Minutos = -15.

    ![Definir um nome, Utilizar Hora Atual e Adicionar Minutos.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Nos parâmetros do teste Web, utilize {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![No parâmetro de teste, utilize {{nome do plug-in}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Agora, carregue o teste no portal. Os valores dinâmicos são utilizados sempre que o teste for executado.

## <a name="dealing-with-sign-in"></a>Lidar com início de sessão
Se os seus utilizadores iniciarem sessão na sua aplicação, terá várias opções para simular o início de sessão, de modo a poder testar páginas depois do início de sessão. A abordagem que utiliza depende do tipo de segurança fornecida pela aplicação.

Em todos os casos, deve criar uma conta na sua aplicação apenas para efeitos de teste. Se possível, restrinja as permissões da conta neste teste, para que não haja nenhuma possibilidade de os testes Web afetarem os utilizadores reais.

### <a name="simple-username-and-password"></a>Nome de utilizador e palavra-passe simples
Grave um teste Web como habitualmente. Elimine primeiro os cookies.

### <a name="saml-authentication"></a>Autenticação SAML
Utilize o plug-in SAML disponível para testes Web.

### <a name="client-secret"></a>Segredo do cliente
Se a sua aplicação tiver uma rota de início de sessão que envolva um segredo do cliente, utilize-a. O Azure Active Directory (AAD) é um exemplo de um serviço que fornece um início de sessão com segredo do cliente. No AAD, o segredo do cliente é a Chave da Aplicação.

Eis um exemplo de teste Web de uma aplicação Web do Azure com uma chave de aplicação:

![Exemplo de segredo do cliente](./media/app-insights-monitor-web-app-availability/110.png)

1. Obtenha o token do AAD utilizando o segredo do cliente (AppKey).
2. Extraia o token de portador a partir da resposta.
3. Chame a API com o token de portador no cabeçalho de autorização.

Certifique-se de que o teste Web é um cliente real: ou seja, tem a sua própria aplicação no AAD - e utilize o clientId + appkey. O serviço em teste tem também a sua própria aplicação no AAD: o URI appID desta aplicação é refletido no teste Web no campo “recurso”.

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de Autenticação Aberta é iniciar sessão com a conta Microsoft ou Google. Muitas aplicações que utilizam a OAuth fornecem uma alternativa ao segredo do cliente. Por isso, a primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de iniciar sessão com a OAuth, a abordagem geral será:

* Utilizar uma ferramenta como o Fiddler para examinar o tráfego entre o browser, o site de autenticação e a aplicação.
* Iniciar duas sessões ou mais com computadores ou browsers diferentes ou com longos intervalos (para permitir a expiração dos tokens).
* Ao comparar as diversas sessões, identificar o token devolvido pelo site de autenticação, que é de seguida transmitido ao servidor da aplicação depois do início de sessão.
* Gravar um teste Web com o Visual Studio.
* Parametrizar os tokens definindo o parâmetro quando o token é devolvido do autenticador e utilizando-o na consulta do site.
  (O Visual Studio tenta parametrizar o teste, mas não parametriza corretamente os tokens.)

## <a name="a-nameedita-edit-or-disable-a-test"></a><a name="edit"></a> Editar ou desativar um teste
Abra um teste individual para editá-lo ou desativá-lo.

![Editar ou desativar um teste Web](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Pode querer desativar os testes Web enquanto estiver a efetuar a manutenção do seu serviço.

## <a name="performance-tests"></a>Testes de desempenho
Pode executar um teste de carga no seu Website. Como o teste de disponibilidade, pode enviar pedidos simples ou pedidos com vários passos a partir dos nossos pontos a nível mundial. Ao contrário de um teste de disponibilidade, são enviados muitos pedidos, simulando vários utilizadores em simultâneo.

No painel Descrição geral, abra **Definições**, **Testes de desempenho**. Quando cria um teste, recebe um convite para ligar ou criar uma conta dos Serviços da Equipa do Visual Studio.

Quando o teste estiver concluído, são-lhe apresentados tempos de resposta e taxas de êxito.

## <a name="automation"></a>Automatização
* [Utilizar scripts do PowerShell para configurar um teste Web](app-insights-powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) que será chamado sempre que for gerado um alerta.

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
* *Posso chamar um código a partir do meu teste Web?*

    Não. Os passos do teste devem estar no ficheiro .webtest. E não pode chamar outros testes Web nem utilizar ciclos. No entanto, existem vários plug-ins que poderão ser úteis.
* *Suporta HTTPS?*

    Suportamos TLS 1.1 e TLS 1.2.
* *Existe alguma diferença entre “testes Web” e “testes de disponibilidade”?*

    Utilizamos os dois termos alternadamente.
* *Gostaria de utilizar testes de disponibilidade no nosso servidor interno, que é executado protegido por uma firewall.*

    Configure a firewall para permitir pedidos a partir dos [endereços IP dos agentes de teste web](app-insights-ip-addresses.md).
* *O carregamento de um teste Web de vários passos falha*

    Existe um limite de tamanho de 300 K.

    Os ciclos não são suportados.

    As referências a outros testes Web não são suportadas.

    As origens de dados não são suportadas.
* *O meu teste com vários passos não é concluído*

    Existe um limite de 100 pedidos por teste.

    O teste é interrompido se for executado durante mais de dois minutos.
* *Como executar um teste com certificados de cliente?*

    Essa função não é suportada.

## <a name="a-namevideoavideo"></a><a name="video"></a>Vídeo
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Monitoring-Availability-with-Application-Insights/player]
>
>

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passos seguintes
[Pesquisar registos de diagnóstico][diagnostic]

[Resolução de problemas][qna]

[IP addresses of web test agents (Endereços IP dos agentes de testes Web)](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md



<!--HONumber=Feb17_HO3-->


