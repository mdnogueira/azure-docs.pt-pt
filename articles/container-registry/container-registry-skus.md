---
title: SKUs de registo de contentor do Azure
description: "Comparações entre as diferentes escalões de serviço disponíveis no registo de contentor do Azure"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: dae97084bdaab77efd38169cdf7e70c827b0b5ab
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="azure-container-registry-skus"></a>SKUs de registo de contentor do Azure

Registo de contentor do Azure (ACR) está disponível em vários escalões de serviço, conhecidos como SKUs. Fornecem estas SKUs preços previsíveis e várias opções para a forma como pretende utilizar o registo de Docker privado no Azure. Escolher um SKU de nível mais elevado fornece mais o desempenho e dimensionamento. No entanto, todos os SKUs fornecem as mesmas capacidades programáticas, permitindo um programador obter uma introdução básica e converter para Standard e Premium, à medida que aumenta de utilização do registo.

## <a name="basic"></a>Básica
Um ponto de entrada com otimização de custos para programadores de aprendizagem sobre o registo de contentor do Azure. Os registos do básicos tenham as mesmas capacidades programáticas como Standard e Premium (integração de autenticação do Azure Active Directory, a eliminação de imagem e web hooks), no entanto, existem restrições de utilização e de tamanho.

## <a name="standard"></a>Standard
Os registos do padrão oferecem as mesmas capacidades que básico, com limites de armazenamento maiores e débito de imagem. Os registos do padrão devem satisfazer as necessidades da maioria dos cenários de produção.

## <a name="premium"></a>Premium
Os registos do Premium fornecem limites superiores sobre as restrições, tais como operações simultâneas, ativar cenários de volume elevado e de armazenamento. Para além de maior capacidade de débito de imagem, Premium adiciona funcionalidades como [georreplicação](container-registry-geo-replication.md) para gerir um único registo em várias regiões, manter um registo de fecho de rede para cada implementação.

## <a name="classic"></a>Clássica
O registo de clássico SKU ativada a edição inicial do serviço de registo de contentor do Azure no Azure. Os registos do clássicos são apoiados por uma conta de armazenamento que o Azure cria na sua subscrição, o que limita a capacidade para ACR fornecer capacidades de nível mais elevadas, tais como o aumento do débito e a georreplicação. Devido ao respetivas capacidades limitadas, planeamos de despromover o SKU clássico no futuro.

> [!NOTE]
> Devido a descontinuação planeada do registo clássico SKU, recomendamos que utilize básica, Standard ou Premium para todos os registos de novo. Para obter informações sobre como converter o registo clássico existente, consulte [alterar SKUs](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Matriz de funcionalidade SKU de registo

A tabela seguinte fornece detalhes sobre as funcionalidades e limites de escalões de serviço básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>Gerir o tamanho do registo
As restrições de armazenamento de cada SKU destinam-se para alinhar com um cenário típico: básica para começar a trabalhar, Standard para a maioria das aplicações de produção e Premium para desempenho de hiper escala e [georreplicação](container-registry-geo-replication.md). Ao longo da vida do seu registo, deve gerir o seu tamanho eliminando periodicamente o conteúdo não utilizado.

Pode encontrar a utilização atual de um registo no registo do contentor **descrição geral** no portal do Azure:

![Informações de utilização do registo no portal do Azure](media/container-registry-skus/registry-overview-quotas.png)

Pode gerir o tamanho do seu registo eliminando repositórios no portal do Azure.

Em **serviços**, selecione **repositórios**, em seguida, clique com botão direito do repositório de que pretende eliminar, em seguida, selecione **eliminar**.

![Eliminar um repositório no portal do Azure](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>SKUs de alteração

Pode alterar o SKU de um registo no portal do Azure.

No registo **descrição geral** no portal do Azure, selecione **atualização**, em seguida, selecione um novo **SKU** da lista pendente SKU.

![Atualizar o registo de contentor SKU no portal do Azure](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>A alteração do clássico
Quando altera um registo do clássico para básico, Standard ou Premium, Azure cópias existentes imagens de contentor da conta do storage associada na sua subscrição para uma conta de armazenamento gerido pelo Azure. Este processo pode demorar algum tempo.

Durante a conversão, `docker pull` continua a funcionar, no entanto, `docker push` está bloqueada até a conclusão da conversão.

Depois de concluída, a conta de armazenamento de subscrição já não é utilizada pelo ACR.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Por que razão a alteração do clássico para básico, Standard ou Premium?

Devido às capacidades limitadas de registos de clássico, recomendamos que altere os registos de clássico aos escalões básico, Standard ou Premium. Estes SKUs de nível mais elevados mais aprofundadamente integram o registo para as capacidades do Azure. Algumas dessas funcionalidades incluem:

* Integração do Active Directory do Azure para a autenticação individuais (consulte [início de sessão do az acr](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* Suporte de eliminação de imagem e etiqueta
* [Georreplicação](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Mais de tudo, um registo de clássico depende a conta de armazenamento que Azure automaticamente aprovisionado na sua subscrição do Azure quando criou o registo. Por outro lado, a básicas, Standard e Premium SKUs tirar partido das *armazenamento geridas*. Ou seja, o Azure gere transparente o armazenamento das suas imagens para si – uma conta de armazenamento separada não é criada na sua própria subscrição.

Algumas das vantagens de armazenamento gerido fornecida pela básicas, Standard e Premium registos:

* As imagens de contentor são [encriptados em descanso ao](../storage/common/storage-service-encryption.md).
* As imagens estão armazenadas utilizando [armazenamento georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage), assuring cópia de segurança das suas imagens com a replicação de multirregião.
* Capacidade de [mover entre os SKUs](#changing-skus), ativar o débito mais elevado quando seleciona um SKU de nível mais elevado. Com cada SKU ACR pode satisfazer as necessidades de débito como aumentam às suas necessidades. A implementação da forma como o ACR distribui o débito pretendido subjacente é expresso como *intenção* (por selecionar SKUs superiores), sem a necessidade de gerir os detalhes da implementação.

## <a name="pricing"></a>Preços

Para obter informações sobre cada uma das SKUs de registo de contentor do Azure de preços, consulte [preços do registo de contentor](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Passos seguintes

**Plano de registo de contentor do Azure**

Visite o [ACR plano](https://aka.ms/acr/roadmap) no GitHub para encontrar informações sobre funcionalidades futuras no serviço.

**UserVoice de registo de contentor do Azure**

Submeter e votar em novas sugestões de funcionalidades no [ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry).