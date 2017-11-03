---
title: "Gerir a política de Media Services do Azure a colocação em cache a CDN do Azure | Microsoft Docs"
description: "Saiba como gerir a política de Media Services do Azure a colocação em cache a CDN do Azure."
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 0c479a58f4158bb1a72dc43432507160f65d2791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gerir a política de Media Services do Azure a colocação em cache a CDN do Azure
Media Services do Azure fornece com base HTTP adaptável de transmissão em fluxo e transferência progressiva. HTTP com base em fluxo é altamente dimensionável com benefícios da colocação em cache no proxy e as camadas da CDN, bem como caching do lado do cliente. Pontos finais de transmissão em fluxo fornece capacidades de transmissão em fluxo gerais e também a configuração para os cabeçalhos de cache HTTP. Pontos finais de transmissão em fluxo define HTTP Cache-Control: idade máxima e cabeçalhos de expira. Pode obter mais informações para os cabeçalhos de cache HTTP de [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de colocação em cache predefinida
Por predefinição pontos finais de transmissão em fluxo, aplicam-se os cabeçalhos de cache de 3 dias para dados de transmissão em fluxo a pedido (suporte de dados real fragmentos/segmentos) e manifest(playlist). Para transmissão em fluxo em direto, pontos finais de transmissão em fluxo aplicam-se os cabeçalhos de cache de 3 dias de dados (suporte de dados real fragmentos/segmentos) e 2 segundos em cache cabeçalho manifest(playlist) pedidos. Quando ativa a programa em direto para a pedido (arquivo em direto), em seguida, cabeçalhos de cache de transmissão em fluxo a pedido são aplicadas.

## <a name="azure-cdn-integration"></a>Integração da CDN do Azure
Media Services do Azure fornece [integrado CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos finais de transmissão em fluxo. Cabeçalhos cache-control aplica-se da mesma forma como pontos finais de transmissão em fluxo para CDN ativada pontos finais de transmissão em fluxo. Azure CDN utiliza ponto final de transmissão em fluxo configurado os valores de cache para definir o tempo de vida dos objetos internamente em cache e também utiliza este valor para definir a entrega cabeçalhos de cache. Quando utilizar a CDN ativada pontos finais de transmissão em fluxo não é recomendado para definir os valores de cache pequenas. Definir valores pequenos diminuir o desempenho e reduzir a vantagem da CDN. Não é permitido definir cabeçalhos de cache menor 600 segundos para o CDN ativada pontos finais de transmissão em fluxo.

> [!IMPORTANT]
>Media Services do Azure tem uma integração completa com CDN do Azure. Com um único clique pode integrar todas as os CDN do Azure fornecedores disponíveis (Akamai e da Verizon) para o ponto final de transmissão em fluxo, incluindo produtos CDN Standard e Premium. Para obter mais informações, consulte este [anúncio](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Custos de dados do ponto final de CDN de transmissão em fluxo apenas obtém desativada se estiver ativada a CDN através de ponto final de APIs de transmissão em fluxo ou utilizando a secção de ponto final de transmissão em fluxo do portal de gestão do Azure. Integração manual ou criar diretamente um ponto final da CDN através de APIs de CDN ou secção portal não irá desativar os encargos de dados.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurar cabeçalhos de cache com Media Services do Azure
Pode utilizar o portal de gestão do Azure ou APIs de serviços de suporte de dados do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar cabeçalhos de cache utilizando o portal de gestão Consulte [como gerir pontos finais de transmissão em fluxo](../media-services/media-services-portal-manage-streaming-endpoints.md) secção Configurar o ponto final de transmissão em fluxo.
2. API de REST de serviços de multimédia do Azure [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. .NET SDK, de serviços de multimédia do Azure [StreamingEndpointCacheControl propriedades](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração de cache
1. Valor de cache do Azure Media Services configurado substitui o valor predefinido.
2. Se não houver nenhuma configuração manual, aplica-se os valores predefinidos.
3. Pela cache de 2 segundos predefinidos cabeçalhos aplica TTL manifest(playlist) transmissão em fluxo, independentemente da configuração do suporte de dados do Azure ou o armazenamento do Azure e substituir deste valor não está disponível.

