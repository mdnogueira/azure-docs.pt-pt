---
title: "Uma aplicação de Proxy da aplicação demora demasiado tempo a carregar | Microsoft Docs"
description: "Resolver problemas de desempenho de carregamento de página com o Proxy de aplicações do Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ce462c90746e6af0dc201686557121665b82b93d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Uma aplicação de Proxy da aplicação demora demasiado tempo a carregar

Este artigo ajuda-o a compreender por que motivo uma aplicação de Proxy de aplicações do Azure AD pode demorar muito tempo a carregar e o que pode fazer para resolver este problema.

## <a name="overview"></a>Descrição geral
Se as aplicações estão a funcionar, mas pode ver uma latência muito, poderão existir alguns tweaks menores na sua topologia de rede que pode considerar para melhorar a velocidade. Para uma edição de avaliação das topologias diferentes, consulte o [documento de considerações de rede](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations).

Se não ajudam a essas considerações, infelizmente não tem atualmente temos recomendações adicionais para otimização de desempenho. Como o serviço de Proxy de aplicações expande a mais centros de dados que podem ser quanto mais próximo para si, pode começar a ver melhorada latência diretamente. Para ver a lista completa dos centros de dados do Azure, pode ver o [página de teste de latência](http://www.azurespeed.com/Azure/Latency). 

Os centros de dados com o serviço de Proxy de aplicações podem ser encontrados com o [ferramenta de teste de portas de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Comentários sobre localizações de centro de dados de Proxy de aplicações 
É possível que existam centros de dados do Azure que como ainda não incluem o Proxy de aplicações, mas seriam levar a uma melhoria de latência excelente para si. Os dados do Centro de localização em < aadapfeedback@microsoft.com > pelo que pode utilizar os seus comentários para planear como podemos expanda.

Estamos a trabalhar para algumas funcionalidades adicionais que ajudam a melhorar a latência para inquilinos atualmente Consulte latências longas e certifique-se a partilha de documentação quando disponível.

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores de proxy no local existentes](application-proxy-working-with-proxy-servers.md)
