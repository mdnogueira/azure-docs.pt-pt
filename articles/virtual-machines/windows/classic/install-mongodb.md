---
title: Instalar MongoDB no Windows VM no Azure | Microsoft Docs
description: "Saiba como instalar MongoDB numa VM do Azure criada com o modelo de implementação clássica com o Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.openlocfilehash: d70194f30b7866cab38cfbbe1ea06664b0ddc1f5
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>Instalar MongoDB no Windows VM no Azure
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md).  Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para instalar e configurar o MongoDB utilizando o modelo de implementação Resource Manager, consulte o artigo [neste artigo](../install-mongodb.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[MongoDB] [ MongoDB] é um popular open source e de elevado desempenho base de dados NoSQL. Este artigo orienta-o a criar uma máquina virtual (VM) do Windows Server utilizando o [portal do Azure][AzurePortal]. Em seguida, criar e anexar um disco de dados para a VM antes de instalar e configurar o MongoDB. Se tiver uma VM existente no Azure que pretende utilizar, pode ir diretamente para [instalar e configurar o MongoDB](#install-and-run-mongodb-on-the-virtual-machine).

## <a name="create-a-virtual-machine-running-windows-server"></a>Criar uma máquina virtual que executa o Windows Server
Siga estas instruções para criar uma máquina virtual.

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> Pode adicionar um ponto final para o MongoDB ao criar a máquina virtual e configurá-lo da seguinte forma: nome como **Mongo**, utilize **TCP** como o protocolo e definir portas públicas e privadas para **27017**.
>
>

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Para fornecer armazenamento para a máquina virtual, anexar um disco de dados e, em seguida, inicializá-lo para que o Windows pode utilizá-lo. Se já tiver um disco de dados, pode anexar esse disco existente, ou pode anexar um disco vazio.

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre a inicializar o disco, consulte "como: iniciar um novo disco de dados no Windows Server" em [como anexar um disco de dados para uma máquina virtual do Windows](attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Instalar e executar MongoDB na máquina virtual
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Resumo
Neste tutorial, aprendeu a criar uma máquina virtual com o Windows Server, remotamente ligar ao mesmo e anexar um disco de dados.  Também aprendeu como instalar e configurar o MongoDB na máquina virtual baseado no Windows. Agora pode aceder MongoDB na máquina virtual baseado no Windows, seguindo os tópicos avançados a [documentação do MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

