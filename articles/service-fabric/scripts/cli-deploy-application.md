---
title: "Exemplo de implementação do Script de CLI de recursos de infraestrutura de serviço do Azure"
description: "Implementar uma aplicação para um cluster do Service Fabric do Azure utilizando a CLI do Azure Service Fabric"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 2bbf689820a92cfa01b26fdbacb4526ade8956ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementar uma aplicação para um cluster do Service Fabric

Este script de exemplo copia um pacote de aplicação para um arquivo de imagens do cluster, regista o tipo de aplicação no cluster e cria uma instância de aplicação do tipo de aplicação. Todos os serviços predefinida também são criados neste momento.

Se necessário, instale o [CLI de recursos de infraestrutura de serviço](../service-fabric-cli.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Quando terminar, o [remover](cli-remove-application.md) script pode ser utilizado para remover a aplicação. O script de remover elimina a instância da aplicação, anula o registo do tipo de aplicação e elimina o pacote de aplicações da loja de imagem.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte o [documentação do Service Fabric CLI](../service-fabric-cli.md).

Amostras de CLI de recursos de infraestrutura de serviço adicionais para o Azure Service Fabric podem ser encontradas no [exemplos de Service Fabric CLI](../samples-cli.md).
