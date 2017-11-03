---
title: "Melhores práticas no registo de contentor do Azure"
description: "Saiba como utilizar o registo de contentor do Azure de forma eficiente, seguindo estas melhores práticas."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 9ec5573082dbf9de1288e1511f5041f8c20b416e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Melhores práticas para o registo de contentor do Azure

Ao seguir estas melhores práticas, pode ajudar a maximizar o desempenho e a utilização rentável do seu registo de Docker privada no Azure.

## <a name="network-close-deployment"></a>Implementação de fecho de rede

Crie o registo de contentor na mesma região do Azure no qual implementar contentores. Colocar o seu registo numa região que é o fecho de rede para o contentor de anfitriões podem ajudar a reduzir a latência e o custo.

Implementação de fecho de rede é uma das principais razões para utilizar um registo de contentor privada. Imagens de docker tem ótimo [dispor construção](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) que permite implementações incrementais. No entanto, os novos nós tem todas as camadas necessárias para uma determinada imagem de extração. Este inicial `docker pull` rapidamente pode adicionar até vários gigabytes. Ter um registo próximo da sua implementação privado minimiza a latência de rede.
Além disso, todas as nuvens públicas, Azure incluído, implementam as taxas de saída da rede. Extrair imagens a partir de um centro de dados para outro adiciona taxas de saída da rede, para além de latência.

## <a name="geo-replicate-multi-region-deployments"></a>Replicar Georreplicação multirregião implementações

Utilizar de registo contentor do Azure [georreplicação](container-registry-geo-replication.md) funcionalidade se estiver a implementar contentores várias regiões. Se estiver a servir clientes global de centros de dados local ou a sua equipa de desenvolvimento está em diferentes localizações, pode simplificar a gestão de registo e minimizar a latência através da replicação de georreplicação seu registo. Atualmente em pré-visualização, esta funcionalidade está disponível com [Premium](container-registry-skus.md#premium) registos.

Para saber como utilizar a georreplicação, consulte o tutorial de três partes [georreplicação no registo de contentor do Azure](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Espaços de nomes do repositório

Tirando partido dos espaços de nomes do repositório, pode permitir que um único registo de partilha em vários grupos na sua organização. Os registos podem ser partilhados em implementações e equipas. Registo de contentor do Azure suporta espaços de nomes aninhados, ativar o isolamento de grupo.

Por exemplo, considere o seguinte contentor etiquetas de imagem. As imagens que são utilizadas a nível empresarial, como `aspnetcore`, são colocadas no espaço de nomes de raiz, enquanto as imagens de contentor pertencentes a grupos de produção e Marketing cada utilização os seus próprios espaços de nomes.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Grupo de recursos dedicado

Porque os registos do contentor são recursos que são utilizados em vários anfitriões do contentor, um registo deve residir o seu próprio grupo de recursos.

Embora poderá experimentar um tipo de anfitriões específicos, tal como instâncias de contentor do Azure, irá provavelmente pretende eliminar a instância do contentor quando tiver terminado. No entanto, pode também querer manter a coleção de imagens enviado para o registo de contentor do Azure. Colocando o registo no seu próprio grupo de recursos, estará a minimizar o risco de acidentalmente a eliminar a coleção de imagens no registo ao eliminar o grupo de recursos de instância do contentor.

## <a name="authentication"></a>Autenticação

Quando a autenticação com um registo de contentor do Azure, existem dois cenários principais: autenticação individuais e a autenticação de serviço (ou "sem interface"). A tabela seguinte fornece uma breve descrição geral destes cenários e o método de autenticação para cada recomendado.

| Tipo | Cenário de exemplo | Método recomendado |
|---|---|---|
| Identidade individuais | Um programador extrair imagens ou enviar imagens a partir da respetiva máquina de desenvolvimento. | [o início de sessão do AZ acr](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Identidade de serviço/sem interface | Compilação e implementação pipelines onde o utilizador não está diretamente envolvido. | [Principal de serviço](container-registry-authentication.md#service-principal) |

Para obter informações aprofundadas sobre a autenticação de registo de contentor do Azure, consulte [autenticar com um registo de contentor do Azure](container-registry-authentication.md).

## <a name="next-steps"></a>Passos seguintes

Registo de contentor do Azure está disponível em vários escalões, denominados SKUs, que cada fornece funcionalidades diferentes. Para obter detalhes sobre os SKUs disponíveis, consulte [SKUs de registo de contentor do Azure](container-registry-skus.md).