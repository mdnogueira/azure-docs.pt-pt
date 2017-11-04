---
title: Microsoft Dynamics CRM e o Azure Application Insights | Microsoft Docs
description: "Obter telemetria de utilizar o Application Insights do Microsoft Dynamics CRM Online. Instruções do programa de configuração, obter dados, visualização e exportação."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: 759dac681592d0e5951e09638533b93c6348d899
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Instruções: Ativar a telemetria para utilizar o Application Insights do Microsoft Dynamics CRM Online
Este artigo mostra como obter dados de telemetria [Microsoft Dynamics CRM Online](https://www.dynamics.com/) utilizando [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Vamos explicar o processo de adição de script do Application Insights à sua aplicação completado captura de dados e visualização de dados.

> [!NOTE]
> [Procurar a solução de exemplo](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar o Application Insights à instância de CRM Online de nova ou existente
Para monitorizar a sua aplicação, adicionar um Application Insights SDK à sua aplicação. O SDK envia a telemetria para o [portal do Application Insights](https://portal.azure.com), onde pode utiliza as ferramentas de diagnóstico e análise poderosa, ou exportar os dados para armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso do Application Insights no Azure
1. Obter [uma conta no Microsoft Azure](http://azure.com/pricing). 
2. Inicie sessão no [portal do Azure](https://portal.azure.com) e adicionar um novo recurso do Application Insights. Este é onde os dados serão processados e apresentados.
   
    ![Clique em +, serviços de programação, Application Insights.](./media/app-insights-sample-mscrm/01.png)
   
    Escolha ASP.NET como o tipo de aplicação.
3. Abra a página de introdução e abrir o "Monitor e diagnosticar do lado do cliente".
   
    ![Fragmento de código para inserção na sua página web](./media/app-insights-sample-mscrm/03.png)

**Manter a página de código aberto** enquanto efetua o passo seguinte na outra janela do browser. Terá do código em breve. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso de web de JavaScript no Microsoft Dynamics CRM
1. Abra a sua instância CRM Online e o início de sessão com privilégios de administrador.
2. Abra Microsoft Dynamics CRM definições, personalizações, personalizar o sistema
   
    ![Definições do Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/04.png)
   
    ![Definições > personalizações](./media/app-insights-sample-mscrm/05.png)

    ![Personalizar a opção de sistema](./media/app-insights-sample-mscrm/06.png)

1. Crie um recurso de JavaScript.
   
    ![Caixa de diálogo Nova recurso Web](./media/app-insights-sample-mscrm/07.png)
   
    Atribua um nome, selecione **Script (JScript)** e abra o editor de texto.
   
    ![Abra o editor de texto](./media/app-insights-sample-mscrm/08.png)
2. Copie o código do Application Insights. Ao copiar Certifique-se ignorar tags de script. Consulte abaixo a captura de ecrã:
   
    ![Definir a sua chave de instrumentação](./media/app-insights-sample-mscrm/09.png)
   
    O código inclui a chave de instrumentação que identifica o recurso do Application insights.
3. Guarde e publicar.
   
    ![Guarde e publicar](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Instrumento formulários
1. Na Microsoft CRM Online, para abrir o formulário de conta
   
    ![Formulário de conta](./media/app-insights-sample-mscrm/11.png)
2. Abrir o formulário de propriedades
   
    ![Propriedades do formulário](./media/app-insights-sample-mscrm/12.png)
3. Adicione o recurso de web de JavaScript que criou
   
    ![Menu Adicionar](./media/app-insights-sample-mscrm/13.png)
   
    ![Adicione o recurso da web](./media/app-insights-sample-mscrm/14.png)
4. Guarde e publicar as personalizações do formulário.

## <a name="metrics-captured"></a>Métricas capturadas
Configurou a captura de telemetria para o formulário. Sempre que é utilizada, os dados serão enviados para o recurso do Application Insights.

Seguem-se exemplos dos dados que irá ver.

#### <a name="application-health"></a>Estado de funcionamento da aplicação
![Tempo de carregamento de página de exemplo](./media/app-insights-sample-mscrm/15.png)

![Gráfico de vistas de página de exemplo](./media/app-insights-sample-mscrm/16.png)

Exceções de browser:

![Gráfico de exceções de browser](./media/app-insights-sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![Lista de exceções](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Utilização
![Os utilizadores, sessões e vistas de página](./media/app-insights-sample-mscrm/19.png)

![Gráficos de sesion](./media/app-insights-sample-mscrm/20.png)

![Versões do browser](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Browsers
![Divisão de tempo de carregamento de página](./media/app-insights-sample-mscrm/22.png)

![Contagem de sessões por versão do browser](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalização
![Número de sessões por país](./media/app-insights-sample-mscrm/24.png)

![Sessões e os utilizadores por país](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Pedido de vista de página no
![Resumo de vista de página](./media/app-insights-sample-mscrm/26.png)

![Pesquisar nos eventos de vista de página](./media/app-insights-sample-mscrm/27.png)

![Vistas de página semelhantes](./media/app-insights-sample-mscrm/28.png)

![Propriedades de visualização de página](./media/app-insights-sample-mscrm/29.png)

![Páginas por sessão](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Código de exemplo
[Procurar o código de exemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Pode fazê-lo analysis mais profundo do mesmo se tiver [exportar os dados para o Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solução do exemplo Microsoft Dynamics CRM
[Eis a solução de exemplo implementada no Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saiba mais
* [O que é o Application Insights?](app-insights-overview.md)
* [Application Insights para páginas web](app-insights-javascript.md)
* [Mais exemplos e instruções](app-insights-code-samples.md)
