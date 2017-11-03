---
title: "Estatísticas em tempo real na CDN do Azure | Microsoft Docs"
description: "Estatísticas em tempo real fornecem dados em tempo real sobre o desempenho da CDN do Azure quando distribui conteúdo aos seus clientes."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estatísticas em tempo real na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Este documento explica estatísticas em tempo real na CDN do Microsoft Azure.  Esta funcionalidade fornece dados em tempo real, tais como largura de banda, os Estados da cache e ligações simultâneas para o perfil de CDN quando distribui conteúdo aos seus clientes. Isto permite a monitorização contínua do Estado de funcionamento do seu serviço em qualquer altura, incluindo eventos em direto ir.

Os gráficos seguintes estão disponíveis:

* [Largura de banda](#bandwidth)
* [Códigos de estado](#status-codes)
* [Estados da cache](#cache-statuses)
* [Ligações](#connections)

## <a name="accessing-real-time-stats"></a>Aceder ao estatísticas em tempo real
1. No [Portal do Azure](https://portal.azure.com), navegue para o perfil de CDN.
   
    ![Painel do perfil da CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
3. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **estatísticas em tempo real** flyout.  Clique em **objeto grande HTTP**.
   
    ![Portal de gestão de CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    São apresentados os gráficos de estatísticas em tempo real.

Cada um dos gráficos apresenta estatísticas em tempo real para o intervalo de tempo selecionado, quando a página for carregada.  Os gráficos atualizar automaticamente a cada alguns segundos.  O **Atualizar gráfico** botão, se estiver presente, limpará o gráfico, após o qual apresentará apenas os dados selecionados.

## <a name="bandwidth"></a>Largura de Banda
![Gráfico de largura de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

O **largura de banda** gráfico apresenta a quantidade de largura de banda utilizada para a plataforma atual ao longo do intervalo de tempo selecionado. A parte do gráfico sombreada indica a utilização de largura de banda. É apresentada a quantidade exata de largura de banda atualmente a ser utilizada diretamente abaixo o gráfico de linha.

## <a name="status-codes"></a>Códigos de estado
![Gráfico de código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

O **códigos de estado** gráfico indica com que frequência certos códigos de resposta HTTP estão a ocorrer durante o intervalo de tempo selecionado.

> [!TIP]
> Para obter uma descrição de cada opção de código de estado HTTP, consulte [códigos de estado de HTTP do Azure CDN](https://msdn.microsoft.com/library/mt759238.aspx).
> 
> 

É apresentada uma lista de códigos de estado HTTP diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número de ocorrências por segundo para esse código de estado atuais. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar por monitorizar apenas os códigos de estado que tenham significância especial para a sua configuração da CDN. Para tal, verifique os códigos de estado pretendido e desmarque todas as outras opções e clique em **Atualizar gráfico**. 

Pode ocultar temporariamente dados registados para um código de estado específico.  Na legenda diretamente abaixo do gráfico, clique o código de estado que pretende ocultar. O código de estado estará imediatamente oculta do gráfico. Clicar novamente esse código de estado irá fazer com que esta opção ser apresentada.

## <a name="cache-statuses"></a>Estados da cache
![Gráfico de Estados de cache](./media/cdn-real-time-stats/cdn-cache-status.png)

O **Estados de Cache** gráfico indica com que frequência determinados tipos de Estados de cache estão a ocorrer durante o intervalo de tempo selecionado. 

> [!TIP]
> Para obter uma descrição de cada opção de código de estado da cache, consulte [códigos de estado de Cache do Azure CDN](https://msdn.microsoft.com/library/mt759237.aspx).
> 
> 

É apresentada uma lista de códigos de estado de cache diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número de ocorrências por segundo para esse código de estado atuais. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar por monitorizar apenas os códigos de estado que tenham significância especial para a sua configuração da CDN. Para tal, verifique os códigos de estado pretendido e desmarque todas as outras opções e clique em **Atualizar gráfico**. 

Pode ocultar temporariamente dados registados para um código de estado específico.  Na legenda diretamente abaixo do gráfico, clique o código de estado que pretende ocultar. O código de estado estará imediatamente oculta do gráfico. Clicar novamente esse código de estado irá fazer com que esta opção ser apresentada.

## <a name="connections"></a>Ligações
![Gráfico de ligações](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica quantos ligações foram estabelecidas para os seus servidores edge. Cada pedido para um recurso que passa através do nosso resultados CDN numa ligação.

## <a name="next-steps"></a>Passos Seguintes
* Seja notificado com [alertas em tempo real na CDN do Azure](cdn-real-time-alerts.md)
* Dig mais profundo com [avançadas de relatórios HTTP](cdn-advanced-http-reports.md)
* Analisar [padrões de utilização](cdn-analyze-usage-patterns.md)

