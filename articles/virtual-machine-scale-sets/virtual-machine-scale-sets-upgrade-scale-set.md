---
title: "Atualizar um conjunto de dimensionamento da máquina virtual do Azure | Microsoft Docs"
description: "Atualizar um conjunto de dimensionamento da máquina virtual do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: guybo
ms.openlocfilehash: c7093e221ff8fe69ded1cfbce4f3ddeb1a195666
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>Atualizar um conjunto de dimensionamento de máquina virtual
Este artigo descreve como pode implementar uma atualização de SO para um conjunto sem qualquer período de indisponibilidade de dimensionamento de máquina virtual do Azure. Neste contexto, uma atualização de SO envolve a alterar a versão ou SKU do SO ou alterar o URI de uma imagem personalizada. A atualização sem meios de período de indisponibilidade atualizar máquinas virtuais uma a uma hora ou em grupos (por exemplo, um domínio de falhas numa altura) em vez de ao mesmo tempo. Ao fazê-lo, pode manter executar quaisquer máquinas virtuais que não estão a ser atualizadas.

Para evitar ambiguidade, vamos distinguir quatro tipos de atualização de SO que poderá querer efetuar:

* Alterar a versão ou a SKU de uma imagem de plataforma. Por exemplo, a alteração Ubuntu versão 14.04.2-LTS de 14.04.201506100 para 14.04.201507060, ou alterar a Ubuntu 15.10/mais recente SKU para 16.04.0-LTS/latest. Este cenário é descrito neste artigo.
* Alterar o URI que aponta para uma nova versão de uma imagem personalizada incorporadas (**propriedades** > **virtualMachineProfile** > **storageProfile**  >  **osDisk** > **imagem** > **uri**). Este cenário é descrito neste artigo.
* Alterar a referência da imagem de um conjunto de dimensionamento que foi criada utilizando discos de gerida do Azure.
* Aplicação de patches de SO de uma máquina virtual (exemplos deste incluem a instalação de um patch de segurança e executar o Windows Update). Este cenário é suportado, mas não abrangido neste artigo.

Conjuntos de dimensionamento de máquina virtual que são implementados como parte de um [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) cluster não são abrangidos aqui. Consulte [Patch de SO de Windows no seu cluster do Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-patch-orchestration-application) para obter mais informações sobre a aplicação de patches de Service Fabric.

A sequência básica para alterar a versão do SO/SKU de uma imagem de plataforma ou o URI de uma imagem personalizada procura da seguinte forma:

1. Obter o modelo de conjunto de dimensionamento de máquina virtual.
2. Altere a versão, SKU, a referência da imagem ou o valor URI no modelo.
3. Atualize o modelo.
4. Fazer uma *manualUpgrade* chamar nas máquinas virtuais no conjunto de dimensionamento. Este passo só é relevante se *upgradePolicy* está definido como **Manual** no seu dimensionamento definido. Se estiver definido como **automática**, todas as máquinas virtuais sejam atualizadas em simultâneo, fazendo com que um período de indisponibilidade.

Com estas informações em mente, vamos ver como pode atualizar a versão de um conjunto no PowerShell e utilizando a API de REST de dimensionamento. Estes exemplos incluem o caso de uma imagem de plataforma, mas este artigo fornece informações suficientes adaptar este processo para uma imagem personalizada.

## <a name="powershell"></a>PowerShell
Neste exemplo atualiza uma escala de máquina virtual do Windows definido (criar para a nova versão 4.0.20160229. Depois de atualizar o modelo, faz uma instância de máquina virtual de um de atualização de cada vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Se estão a atualizar o URI para uma imagem personalizada em vez de alterar uma versão de imagem de plataforma, substitua a linha "definir a nova versão" com um comando que irá atualizar a imagem de origem URI. Por exemplo, se o conjunto de dimensionamento foi criado sem utilizar discos gerida do Azure, a atualização seria este aspeto:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Se uma imagem personalizada baseada em conjunto de dimensionamento foi criada utilizando discos gerida do Azure, em seguida, seria possível atualizar a referência da imagem. Por exemplo:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>A API REST
Seguem-se alguns exemplos de Python que utilizam a API de REST do Azure para implementar uma atualização de versão do SO. Utilizam ambos o lightweight [azurerm](https://pypi.python.org/pypi/azurerm) biblioteca de funções de wrapper de API REST do Azure para efetuar uma ação obter na escala definir modelo, seguido de um PUT com um modelo atualizado. Podem também ver vistas de instâncias de máquina virtual para identificar as máquinas virtuais por domínio de atualização.

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) é um script de Python, que é utilizado para implementar uma atualização do SO para um dimensionamento da máquina virtual em execução definir um domínio de atualização de cada vez.

![Script de Vmssupgrade para escolher um domínio de atualização ou máquinas virtuais](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Este script permite-lhe escolher máquinas virtuais específicas para atualizar ou especifique um domínio de atualização. Suporta a alteração de uma versão de imagem de plataforma ou alterar o URI de uma imagem personalizada.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) é um editor para fins gerais para conjuntos de dimensionamento de máquina virtual que mostra a virtual máquina de estado como um heatmap em que uma linha representa um domínio de atualização. Entre outras coisas, pode atualizar o modelo para um conjunto de dimensionamento com uma nova versão, o SKU ou o URI de imagem personalizada e, em seguida, escolha os domínios de falhas para atualizar. Se o fizer, todas as máquinas virtuais nesse domínio de atualização são atualizadas para o novo modelo. Em alternativa, pode efetuar uma atualização sem interrupção, com base no tamanho de lote da sua preferência.  

A seguinte captura de ecrã mostra um modelo de um conjunto para Ubuntu 14.04-2LTS versão 14.04.201507060 de dimensionamento. Muitas mais opções foram adicionadas a esta ferramenta, desde que foi executada nesta captura de ecrã.

![Modelo de Vmsseditor de um conjunto para Ubuntu 14.04-2LTS de dimensionamento](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Depois de clicar em **atualizar** e, em seguida, **obter detalhes**, máquinas virtuais no UD 0 começar a atualizar.

![Vmsseditor que mostra atualização em curso](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

