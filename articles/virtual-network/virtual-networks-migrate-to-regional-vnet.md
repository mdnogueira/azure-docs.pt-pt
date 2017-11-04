---
title: "Migrar uma rede virtual do Azure (clássica) a partir de um grupo de afinidade para uma região | Microsoft Docs"
description: "Saiba como migrar uma rede virtual (clássica) a partir de um grupo de afinidade para uma região."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: b9b3bd0f2184ac85261166d5fe2ab67e1bf319d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrar uma rede virtual (clássica) a partir de um grupo de afinidade para uma região

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que implementações mais novas utilizem o modelo de implementação Resource Manager.

Grupos de afinidades Certifique-se de que os recursos criados no mesmo grupo de afinidade fisicamente alojados por servidores que estão fechar em conjunto, ativar a estes recursos para comunicar mais rápida. No passado, grupos de afinidade eram um requisito para criar redes virtuais (clássica). Nessa altura, o serviço do Gestor de rede que geridos redes virtuais (clássicas) só pode funcionar dentro de um conjunto de servidores físicos ou unidade de escala. Melhoramentos da arquitetura tem aumentado o âmbito da gestão de rede a uma região.

Como resultado estas melhorias da arquitetura, grupos de afinidades já não são recomendados, ou necessários para as redes virtuais (clássicas). A utilização de grupos de afinidade para redes virtuais (clássica) é substituída pela regiões. Redes virtuais (clássica) que estão associados a regiões são denominadas redes virtuais regionais.

Recomendamos que não utilize grupos de afinidade em geral. Além dos requisitos de rede virtual, os grupos de afinidades também foram importantes utilizar para se certificar de recursos, tais como a computação (clássica) e armazenamento (clássica), foram colocadas perto de si. No entanto, com a arquitetura de rede do Azure atual, estes requisitos de posicionamento são já não são necessários.

> [!IMPORTANT]
> Embora seja tecnicamente possível criar uma rede virtual que está associada um grupo de afinidade, não há nenhuma razão apelativa para fazê-lo. Muitas funcionalidades de rede virtual, tais como grupos de segurança de rede, só estão disponíveis ao utilizar uma rede virtual regional e não estão disponíveis para as redes virtuais que estão associados a grupos de afinidades.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Edite o ficheiro de configuração de rede

1. Exporte o ficheiro de configuração de rede. Para saber como exportar um ficheiro de configuração de rede utilizando o PowerShell ou a interface de linha de comandos do Azure (CLI) 1.0, consulte [configurar uma rede virtual com um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md#export).
2. Edite o ficheiro de configuração de rede, substituindo **AffinityGroup** com **localização**. Especifique um Azure [região](https://azure.microsoft.com/regions) para **localização**.
   
   > [!NOTE]
   > O **localização** é a região que especificou para o grupo de afinidade que está associado a sua rede virtual (clássica). Por exemplo, se a rede virtual (clássica) está associada um grupo de afinidade que está localizado em EUA oeste, ao migrar, o **localização** tem de apontar para EUA oeste. 
   > 
   > 
   
    Edite as seguintes linhas no ficheiro de configuração de rede, substituindo os valores com os seus próprios: 
   
    **Valor antigo:** \<VirtualNetworkSitename = AffinityGroup "VNetUSWest" = "VNetDemoAG"\> 
   
    **Valor novo:** \<VirtualNetworkSitename = "VNetUSWest" localização "EUA Oeste" de =\>
3. Guardar as alterações e [importar](virtual-networks-using-network-configuration-file.md#import) a configuração de rede para o Azure.

> [!NOTE]
> Esta migração não irá causar qualquer período de indisponibilidade para os serviços.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>O que fazer se tiver uma VM (clássica) num grupo de afinidade
VMs (clássica) que estão atualmente a ser um grupo de afinidade não precisam de ser removido do grupo de afinidade. Depois de uma VM for implementada, é implementada para uma unidade de escala único. Grupos de afinidades pode restringir o conjunto de tamanhos VM disponíveis para uma nova implementação de VM, mas qualquer VM existente que é implementada já está restringida ao conjunto de tamanhos de VM disponíveis na unidade de escala em que a implementação da VM. Porque a VM já está a ser implementada para uma unidade de escala, remover uma VM a partir de um grupo de afinidade não tem efeito na VM.
