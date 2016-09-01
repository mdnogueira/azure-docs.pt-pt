<properties
    pageTitle="Gerir a sua primeira API na API Management do Azure | Microsoft Azure"
    description="Saiba como criar APIs, adicionar operações e começar a utilizar a API Management."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/09/2016"
    ms.author="sdanie"/>

# Gerir a sua primeira API na API Management do Azure

## <a name="overview"> </a>Descrição geral

Este guia mostra como começar a utilizar rapidamente a API Management do Azure e efetuar a primeira chamada à API.

## <a name="concepts"> </a>O que é a API Management do Azure?

Pode utilizar a API Management do Azure para iniciar um programa de API totalmente funcional baseado em qualquer back-end.

Os cenários comuns incluem:

* **Proteção da infraestrutura móvel** mediante o controlo de acesso com chaves de API, prevenção de ataques DoS através da utilização de limitação ou utilização de políticas de segurança avançadas como a validação de token JWT.
* **Ativação de ecossistemas de parceiros ISV** ao proporcionar uma integração mais rápida dos parceiros através do portal do programador e construir uma fachada de API para a desassociação de implementações internas que não estão prontas para consumo por parte dos parceiros.
* **Execução de um programa de API interno** ao oferecer uma localização centralizada para que a organização possa comunicar sobre a disponibilidade e as alterações mais recentes às APIs ou controlar o acesso com base em contas organizacionais, tudo isto baseado num canal seguro entre o gateway da API e o back-end.


O sistema é constituído pelos seguintes componentes:

* O **gateway da API** é o ponto final que:
  * Aceita as chamadas à API e as encaminha para os seus back-ends.
  * Verifica as chaves de API, os tokens JWT, os certificados e outras credenciais.
  * Impõe quotas de utilização e limites de velocidade.
  * Transforma imediatamente a sua API sem modificações do código.
  * Coloca em cache as respostas de back-end, quando estão configuradas.
  * Regista metadados de chamadas para fins de análise.

* O **portal do publicador** é a interface administrativa onde configura o seu programa de API. Utilize-o para:
    * Definir ou importar o esquema de API.
    * Integrar APIs em produtos.
    * Configurar políticas como quotas ou transformações nas APIs.
    * Obter conhecimentos aprofundados a partir da análise.
    * Gerir utilizadores.

* O **portal do programador** serve como a principal presença na Web para os programadores, onde podem:
    * Ler a documentação da API.
    * Experimentar uma API através da consola interativa.
    * Criar uma conta e subscrever para obter chaves de API.
    * Aceder a análises sobre a sua própria utilização.


## <a name="create-service-instance"> </a>Criar uma instância da API Management

>[AZURE.NOTE] Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode criar uma conta gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][].

O primeiro passo para trabalhar com a API Management consiste em criar uma instância de serviço. Inicie sessão no [Portal Clássico do Azure][] e clique em **Novo**, **Serviços Aplicacionais**, **API Management**, **Criar**.

![Nova instância da API Management][api-management-create-instance-menu]

Para **URL**, especifique um nome de subdomínio exclusivo a utilizar para o URL do serviço.

Selecione a **Subscrição** e a **Região** pretendidas para a sua instância de serviço. Depois de efetuar as seleções, clique no botão **Seguinte**.

![Novo serviço da API Management][api-management-create-instance-step1]

Introduza **Contoso Lda.** para o **Nome da Organização** e introduza o seu endereço de e-mail no campo **E-mail do Administrador**.

>[AZURE.NOTE] Este endereço de e-mail é utilizado para notificações do sistema da API Management. Para obter mais informações, consulte [Como configurar notificações e modelos de e-mail na API Management do Azure][].

![Novo serviço da API Management][api-management-create-instance-step2]

As instâncias do serviço de API Management estão disponíveis em três camadas: Programador, Standard e Premium. Por predefinição, as novas instâncias de serviço de API Management são criadas na camada de Programador. Para selecionar a camada Standard ou Premium, marque a caixa de verificação **Definições avançadas** e selecione a camada pretendida no ecrã seguinte.

>[AZURE.NOTE] A camada de Programador destina-se a programas de API de desenvolvimento, teste e piloto em que a elevada disponibilidade não é uma preocupação. Nas camadas Standard e Premium, pode dimensionar a contagem de unidades reservadas para processar mais tráfego. As camadas Standard e Premium fornecem ao serviço de API Management a maior potência de processamento e o melhor desempenho. Pode concluir este tutorial utilizando qualquer camada. Para obter mais informações sobre as camadas de API Management, consulte [Preços da API Management][].

Clique na caixa de verificação para criar a instância de serviço.

![Novo serviço da API Management][api-management-instance-created]

Quando a instância de serviço for criada, o passo seguinte é criar ou importar uma API.

## <a name="create-api"> </a>Importar uma API

Uma API consiste num conjunto de operações que podem ser invocadas a partir de uma aplicação cliente. As operações de API são suportadas com um proxy para serviços Web existentes.

Pode criar APIs (e adicionar operações) manualmente ou pode importá-las. Neste tutorial, iremos importar a API para um serviço Web de calculadora de exemplo fornecido pela Microsoft e alojado no Azure.

>[AZURE.NOTE] Para obter orientações sobre a criação de uma API e a adição manual de operações, consulte [Como criar APIs](api-management-howto-create-apis.md) e [Como adicionar operações a uma API](api-management-howto-add-operations.md).

As APIs são configuradas a partir do portal do publicador, que é acedido através do Portal Clássico do Azure. Para aceder ao portal do publicador, clique em **Gerir** no Portal Clássico do Azure para o seu serviço de API Management.

![Portal do publicador][api-management-management-console]

Para importar a API de calculadora, clique em **APIs** no menu **API Management** à esquerda e, em seguida, clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute os seguintes passos para configurar a API de calculadora:

1. Clique em **De URL**, introduza **http://calcapi.cloudapp.net/calcapi.json** na caixa de texto **URL do documento de especificação** e clique no botão de opção **Swagger**.
2. Escreva **calc** na caixa de texto **Sufixo do URL da API Web**.
3. Clique na caixa **Produtos (opcionais)** e selecione **Inicial**.
4. Clique em **Guardar** para importar a API.

![Adicionar nova API][api-management-import-new-api]

>[AZURE.NOTE] A **API Management** suporta atualmente a versão 1.2 e 2.0 do documento Swagger para importação. Embora a [Especificação Swagger 2.0](http://swagger.io/specification) declare que as propriedades `host`, `basePath` e `schemes` são opcionais, o documento Swagger 2.0 **DEVE** conter essas propriedades; caso contrário, não será importado. 

Quando a API for importada, a página de resumo da API é apresentada no portal do publicador.

![Resumo da API][api-management-imported-api-summary]

A secção API tem vários separadores. O separador **Resumo** apresenta métricas e informações básicas sobre a API. O separador [Definições](api-management-howto-create-apis.md#configure-api-settings) é utilizado para ver e editar a configuração de uma API. O separador [Operações](api-management-howto-add-operations.md) é utilizado para gerir as operações da API. O separador **Segurança** pode ser utilizado para configurar a autenticação de gateway para o servidor de back-end utilizando a autenticação Básica ou a [autenticação de certificados mútuos](api-management-howto-mutual-certificates.md) e para configurar a [autorização do utilizador através da utilização de OAuth 2.0](api-management-howto-oauth2.md).  O separador **Problemas** é utilizado para ver os problemas comunicados pelos programadores que utilizam as suas APIs. O separador **Produtos** é utilizado para configurar os produtos que contêm esta API.

Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo:

-   **Inicial**
-   **Ilimitado**

Neste tutorial, a API de Calculadora Básica foi adicionada ao produto Inicial quando a API foi importada.

Para efetuar chamadas a uma API, os programadores têm primeiro de subscrever um produto que lhes dá acesso à mesma. Os programadores podem subscrever produtos no portal do programador ou os administradores podem subscrever produtos para os programadores no portal do publicador. É um administrador, uma vez que criou a instância da API Management nos passos anteriores do tutorial, pelo que já subscreve todos os produtos por predefinição.

## <a name="call-operation"> </a>Chamar uma operação a partir do portal do programador

As operações podem ser chamadas diretamente a partir do portal do programador, que fornece um meio cómodo para ver e testar as operações de uma API. Neste passo do tutorial, irá chamar a operação **Adicionar dois números inteiros** da API de Calculadora Básica. Clique em **Portal do programador** no menu na parte superior direita do portal do publicador.

![Portal do programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e, em seguida, clique em **Calculadora Básica** para ver as operações disponíveis.

![Portal do programador][api-management-developer-portal-calc-api]

Tenha em atenção as descrições e os parâmetros de exemplo que foram importados juntamente com a API e as operações, que proporcionam documentação para os programadores que irão utilizar esta operação. Estas descrições também podem ser adicionadas quando as operações são adicionadas manualmente.

Para chamar a operação **Adicionar dois números inteiros**, clique em **Experimente**.

![Experimente][api-management-developer-portal-calc-api-console]

Pode introduzir alguns valores para os parâmetros ou manter as predefinições e, em seguida, clique em **Enviar**.

![HTTP Get][api-management-invoke-get]

Depois de uma operação ser invocada, o portal do programador apresenta o **Estado da resposta**, os **Cabeçalhos da resposta** e qualquer **Conteúdo da resposta**.

![Resposta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Ver análise

Para ver análises da Calculadora Básica, regresse ao portal do publicador ao selecionar **Gerir** no menu na parte superior direita do portal do programador.

![Gerir][api-management-manage-menu]

A vista predefinida para o portal do publicador é o **Dashboard**, que fornece uma descrição geral da sua instância da API Management.

![Dashboard][api-management-dashboard]

Paire o rato sobre o gráfico referente a **Calculadora Básica** para ver as métricas específicas de utilização da API relativas a um determinado período de tempo.

>[AZURE.NOTE] Se não vir quaisquer linhas no gráfico, regresse ao portal do programador e efetue algumas chamadas à API, aguarde alguns momentos e, em seguida, volte ao dashboard.

Clique em **Ver Detalhes** para ver a página de resumo da API, incluindo uma versão maior das métricas apresentadas.

![Análise][api-management-mouse-over]

![Resumo][api-management-api-summary-metrics]

Para obter métricas e relatórios detalhados, clique em **Análise** no menu **API Management** à esquerda.

![Descrição geral][api-management-analytics-overview]

A secção **Análise** tem os seguintes quatro separadores:

-   **De relance** fornece métricas de utilização e estado de funcionamento globais, bem como os principais programadores, os principais produtos, as principais APIs e as principais operações.
-   **Utilização** fornece uma visão detalhada das chamadas à API e da largura de banda, incluindo uma representação geográfica.
-   **Estado de funcionamento** concentra-se em códigos de estado, taxas de êxito de colocação em cache, tempos de resposta e tempos de resposta da API e do serviço.
-   **Atividade** fornece relatórios detalhados sobre uma atividade específica por programador, produto, API e operação.

## <a name="next-steps"> </a>Passos seguintes

-   Veja os outros tópicos do tutorial [Introdução à configuração avançada de API][].

[Avaliação Gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Criar uma instância da API Management]: #create-service-instance
[Criar uma API]: #create-api
[Adicionar uma operação]: #add-operation
[Adicionar a nova API a um produto]: #add-api-to-product
[Subscrever o produto que contém a API]: #subscribe
[Chamar uma operação a partir do Portal do Programador]: #call-operation
[Ver análise]: #view-analytics
[Passos seguintes]: #next-steps


[Como gerir contas de programador na API Management do Azure]: api-management-howto-create-or-invite-developers.md
[Configurar definições de API]: api-management-howto-create-apis.md#configure-api-settings
[Como configurar notificações e modelos de e-mail na API Management do Azure]: api-management-howto-configure-notifications.md
[Respostas]: api-management-howto-add-operations.md#responses
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Introdução à configuração avançada de API]: api-management-get-started-advanced.md
[Preços da API Management]: http://azure.microsoft.com/pricing/details/api-management/

[Portal Clássico do Azure]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png



<!--HONumber=ago16_HO4-->


