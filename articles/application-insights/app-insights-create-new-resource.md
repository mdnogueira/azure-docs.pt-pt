---
title: Criar um novo recurso do Azure Application Insights | Microsoft Docs
description: "Configure manualmente monitorização do Application Insights para uma nova aplicação em direto."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 9023f3d9ae3ddd4d75b5853a08177cba7718cec1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
Azure Application Insights apresenta dados sobre a sua aplicação num Microsoft Azure *recursos*. Criar um novo recurso, por conseguinte, faz parte de [ao configurar o Application Insights para monitorizar uma nova aplicação][start]. Em muitos casos, criação de um recurso pode ser efetuado automaticamente o IDE. Mas em alguns casos, criar um recurso manualmente - por exemplo, a ter recursos separados para programação e produção baseia-se da sua aplicação.

Depois de criar o recurso, obter a chave de instrumentação e utilize-a para configurar o SDK da aplicação. A chave do recurso contém ligações a telemetria para o recurso.

## <a name="sign-up-to-microsoft-azure"></a>Inscrever-se ao Microsoft Azure
Se ainda não recebeu um [Microsoft conta, crie uma agora](http://live.com). (Se utilizar serviços como o Outlook.com, OneDrive, Windows Phone ou XBox Live, já tiver uma conta Microsoft.)

Também precisa de uma subscrição [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-, utilizando o ID de Windows Live. Apenas a cobrado o que utiliza. Permite que o plano básico predefinido para uma determinada quantidade de utilização experimental gratuitamente.

Quando já tem acesso a uma subscrição, inicie sessão no Application Insights no [http://portal.azure.com](https://portal.azure.com)e utilizar o Live ID para iniciar sessão.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
No [portal.azure.com](https://portal.azure.com), adicione um recurso do Application Insights:

![Clicar em Novo, Application Insights](./media/app-insights-create-new-resource/01-new.png)

* **Tipo de aplicação** afeta o que vê no painel de descrição geral e as propriedades disponíveis no [explorer métrica][metrics]. Se não vir o seu tipo de aplicação, escolha geral.
* **Subscrição** é a sua conta de pagamento no Azure.
* **Grupo de recursos** está para efeitos práticos para a gestão de propriedades, como o controlo de acesso. Se já tiver criado outros recursos do Azure, pode optar por colocar este novo recurso no mesmo grupo.
* **Localização** é onde iremos manter os seus dados.
* **Afixar ao dashboard** coloca um mosaico de acesso de leitura rápida para o seu recurso na sua página de home page do Azure. Recomendado.

Quando a aplicação tiver sido criada, um novo painel abre. Este painel é onde ver os dados de utilização de desempenho e sobre a sua aplicação. 

Para voltar ao mesmo próxima vez que iniciar sessão no Azure, procure o mosaico de início rápido da sua aplicação no painel de início (ecrã principal). Ou clique em Procurar para localizar.

## <a name="copy-the-instrumentation-key"></a>Copie a chave de instrumentação
A chave de instrumentação identifica o recurso que criou. É necessário para dar ao SDK.

![Clique em Essentials, clique na chave de instrumentação, CTRL + C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Instalar o SDK na sua aplicação
Instale o Application Insights SDK na sua aplicação. Este passo descontos elevados depende do tipo da sua aplicação. 

Utilize a chave de instrumentação para configurar [SDK que instalou na sua aplicação][start].

O SDK inclui módulos padrão que enviam telemetria sem ter de escrever qualquer código. Para controlar as ações do utilizador ou diagnosticar problemas em mais detalhe, [utilizam a API] [ api] para enviar a sua própria telemetria.

## <a name="monitor"></a>Ver dados de telemetria
Feche o painel de início rápido para regressar ao painel da aplicação no portal do Azure.

Clique no mosaico de pesquisa para ver [pesquisa de diagnóstico][diagnostic], onde são apresentados os eventos primeiro. 

Se está à espera de mais dados, clique em **atualizar** após alguns segundos.

## <a name="creating-a-resource-automatically"></a>Criação de um recurso automaticamente
Pode escrever um [script do PowerShell](app-insights-powershell.md) para criar automaticamente um recurso.

## <a name="next-steps"></a>Passos seguintes
* [Criar um dashboard](app-insights-dashboards.md)
* [Pesquisa de Diagnóstico](app-insights-diagnostic-search.md)
* [Explorar métricas](app-insights-metrics-explorer.md)
* [Escrever consultas da Análise](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

