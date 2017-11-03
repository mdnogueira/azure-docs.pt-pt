---
title: "Descrição geral do Azure gerida aplicações | Microsoft Docs"
description: "Descreve os conceitos do Azure gerida aplicações"
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 682b7577135e327457976dc77ce4b4364bd12e48
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-managed-applications-overview"></a>Descrição geral de aplicações geridas do Azure

Aplicações geridas do Azure permitem-lhe a oferta de soluções de nuvem que são mais fácil para os consumidores implementar e operar. Implementar a infraestrutura e fornecer suporte em curso. Para disponibilizar uma aplicação gerida para todos os clientes, publicá-lo no Azure marketplace. Para o disponibilizar apenas aos utilizadores na sua organização, publicá-lo para um catálogo interno. 

Uma aplicação gerida é semelhante a um modelo de solução no mercado, com uma diferença de chave. Numa aplicação gerida, os recursos são aprovisionados para um grupo de recursos que é gerido pelo fabricante da aplicação. O grupo de recursos está presente na subscrição do cliente, mas uma identidade no inquilino do publicador tem acesso ao grupo de recursos. Como publicador, deve especificar o custo de suporte em curso da solução.

## <a name="advantages-of-managed-applications"></a>Vantagens de aplicações geridas

Aplicações geridas reduzem barreiras as eficazes para os consumidores que utilizam as suas soluções. Não precisam de conhecimentos na infraestrutura de nuvem para utilizar a solução. Os consumidores limitada acesso a recursos críticos. Não é necessário preocupar efetuar um erro quando geri-lo. 

Aplicações geridas permitem-lhe estabelecer uma relação contínua com os consumidores. Definir termos para gerir a aplicação e todos os custos são processados através de faturação do Azure.

Embora os clientes implementar estas aplicações geridas nas suas subscrições, não têm de manter, atualizar ou service-los. Pode certificar-se de que todos os clientes estão a utilizar versões aprovadas. Os clientes não têm conhecimento de domínio específico da aplicação para gerir estas aplicações de desenvolver. Os clientes automaticamente adquirir atualizações da aplicação sem a necessidade de se preocupar com a resolução de problemas e diagnosticar problemas com as aplicações. 

Para as equipas de TI, aplicações geridas permitem-lhe a oferta de soluções previamente aprovadas aos utilizadores na organização. Certifique-se que nestas soluções de estão em conformidade com as normas organizacionais.

## <a name="types-of-managed-applications"></a>Tipos de aplicações geridas

Pode publicar a sua aplicação gerida externamente ou internamente.

![Publicar internamente ou externamente](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>catálogo de serviço

O catálogo de serviço é um catálogo interno de aprovados soluções para os utilizadores numa organização. Utilizar o catálogo para garantir a conformidade com determinadas normas organizacionais ao possam fornecer soluções para as organizações. Os empregados utilizar o catálogo para detetar facilmente ao conjunto avançado de aplicações que são recomendados e aprovada pelo seus departamentos de TI. Verão as aplicações geridas que outras pessoas na sua organização partilham com os mesmos.

Para obter informações sobre como publicar uma aplicação do catálogo de serviço geridas, consulte [Criar aplicação do catálogo de serviço](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Os fornecedores que bem ao faturar para os seus serviços podem disponibilizar uma aplicação gerida através do Azure marketplace. Depois do fornecedor publica uma aplicação, estão disponível para os utilizadores fora da organização. Com esta abordagem, fornecedores de serviços geridos (MSPs), os fabricantes independentes de software (ISV) e do sistema integradores (SIs) podem oferecer as soluções para todos os clientes do Azure.

Para obter informações sobre como publicar uma aplicação gerida no Marketplace, consulte [Criar aplicação marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Grupos de recursos para aplicações geridas

Normalmente, os recursos para uma aplicação gerida residirem em dois grupos de recursos. O consumidor gere um grupo de recursos e o publicador a outro grupo de recursos. Ao definir a aplicação gerida, o publicador Especifica os níveis de acesso. A imagem seguinte mostra um cenário em que o publicador solicita a função de proprietário do grupo de recursos geridos. O publicador colocar um bloqueio de só de leitura neste grupo de recursos para o consumidor.

![Acesso de grupo de recursos](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupo de recursos de aplicação

Este grupo de recursos contém a instância de aplicações geridas. Este grupo de recursos só pode conter um recurso. O tipo de recurso da aplicação gerida está **Microsoft.Solutions/applications**.

O consumidor tem acesso total ao grupo de recursos e utiliza-o para gerir o ciclo de vida da aplicação gerida.

### <a name="managed-resource-group"></a>Grupo de recursos geridos

Este grupo de recursos contém todos os recursos que são necessários para a aplicação gerida. Por exemplo, este grupo de recursos contém as máquinas virtuais, contas de armazenamento e redes virtuais para a solução. O consumidor limitou o acesso a este grupo de recursos porque o consumidor não gere os recursos individuais para a aplicação gerida. Corresponde ao acesso do publicador a este grupo de recursos para a função especificada na definição de aplicações geridas. Por exemplo, o fabricante pode pedir a função de proprietário ou contribuinte para este grupo de recursos.

Quando o consumidor elimina a aplicação gerida, também é eliminar o grupo de recursos geridos.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para definir e implementar uma aplicação gerida, consulte [criar e implementar do Azure gerida aplicação com a CLI do Azure](managed-apps-quickstart-cli.md)
* Para obter informações sobre a publicação de uma aplicação interna, consulte [Criar aplicação do catálogo de serviço](publish-service-catalog-app.md).
* Para obter informações sobre a publicação de aplicações geridas no Marketplace, consulte [Criar aplicação marketplace](publish-marketplace-app.md).

