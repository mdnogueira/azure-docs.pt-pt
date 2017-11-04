---
title: "Criar um nó principal do HPC Pack numa VM do Azure | Microsoft Docs"
description: "Saiba como utilizar o portal do Azure e o modelo de implementação Resource Manager para criar um nó principal do Microsoft HPC Pack 2012 R2 numa VM do Azure."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: b2bb9caf82a580dc5f67ea0b0b1c2e9a46363e9c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Criar o nó principal de um cluster HPC Pack numa VM do Azure com uma imagem do Marketplace
Utilize um [imagem de máquina virtual do Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do Azure Marketplace e o portal do Azure para criar o nó principal de um cluster HPC. Esta imagem de VM de pacote HPC é baseada no Windows Server 2012 R2 Datacenter com o HPC Pack 2012 R2 Update 3 pré-instaladas. Utilize este nó principal para uma prova de implementação de conceito de pacote HPC no Azure. Em seguida, pode adicionar nós de computação para o cluster para executar cargas de trabalho HPC.

> [!TIP]
> Para implementar um cluster HPC Pack 2012 R2 completado no Azure que inclui o nó principal e os nós de computação, recomendamos que utilize um método automatizado. As opções incluem o [script de implementação de HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) e modelos do Resource Manager, tais como o [cluster HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Modelos do Resource Manager também estão disponíveis para [clusters do Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Considerações de planeamento
Como é mostrado na figura seguinte, pode implementar nó principal do HPC Pack num domínio do Active Directory numa rede virtual do Azure.

![Nó principal do HPC Pack][headnode]

* **Domínio do Active Directory**: O HPC Pack 2012 R2 nó principal tem de estar associado a um domínio do Active Directory no Azure antes de começar com o HPC serviços na VM. Conforme mostrado neste artigo, para uma prova de implementação de conceito, pode promover a VM que criar para o nó principal como um controlador de domínio antes de iniciar os serviços HPC. Outra opção consiste em implementar um controlador de domínio separado e a floresta no Azure ao qual associar o VM de nó principal.

* **Modelo de implementação**: para a maioria das implementações novas, a Microsoft recomenda que utilize o modelo de implementação Resource Manager. Este artigo pressupõe que utiliza este modelo de implementação.

* **Rede virtual do Azure**: ao utilizar o modelo de implementação Resource Manager para implementar o nó principal, que especificar ou criar uma rede virtual do Azure. Utilizar a rede virtual se precisar de aderir ao nó principal para um domínio do Active Directory existente. Também precisa de mais tarde para adicionar o nó de computação VMs para o cluster.

## <a name="steps-to-create-the-head-node"></a>Passos para criar o nó principal
Seguem-se passos de alto nível para utilizar o portal do Azure para criar uma VM do Azure para o nó principal do HPC Pack utilizando o modelo de implementação Resource Manager. 

1. Se pretender criar uma nova floresta do Active Directory no Azure com VMs do controlador de domínio separado, uma das alternativas consiste em utilizar um [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Para uma prova simple de implementação de conceito, é adequado para omita este passo e configurar o nó principal do VM-se automaticamente como um controlador de domínio. Esta opção é descrita mais à frente neste artigo.
2. No [HPC Pack 2012 R2, Windows Server 2012 R2 página](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) no Azure Marketplace, clique em **criar Máquina Virtual**. 
3. No portal, no **HPC Pack 2012 R2 no Windows Server 2012 R2** página, selecione o **Resource Manager** modelo de implementação e, em seguida, clique em **criar**.
   
    ![Imagem de HPC Pack][marketplace]
4. Utilize o portal para configurar as definições e criar a VM. Se estiver familiarizado com o Azure, siga o tutorial [criar máquina virtual do Windows no portal do Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para uma prova de implementação de conceito, normalmente, pode aceitar a predefinição ou definições recomendadas.
   
   > [!NOTE]
   > Se pretender associar o nó principal para um domínio do Active Directory existente no Azure, certifique-se de que especificar a rede virtual para esse domínio, ao criar a VM.
   > 
   > 
5. Depois de criar a VM e a VM está em execução, [ligar à VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pelo ambiente de trabalho remoto. 
6. Associe a VM a uma floresta de domínio do Active Directory escolhendo uma das seguintes opções:
   
   * Se criou a VM numa rede virtual do Azure com uma floresta de domínio existente, associe a VM para a floresta utilizando ferramentas padrão do Gestor de servidor ou o Windows PowerShell. Em seguida, reinicie.
   * Se criou a VM numa rede virtual novo (sem uma floresta de domínio existente), em seguida, promova a VM como um controlador de domínio. Utilize padrão passos para instalar e configurar a função de serviços de domínio do Active Directory no nó principal. Para obter passos detalhados, consulte [instalar uma nova floresta Windows Server 2012 Active Directory](https://technet.microsoft.com/library/jj574166.aspx).
7. Depois da VM está em execução e está associada a uma floresta do Active Directory, inicie os serviços de pacote HPC da seguinte forma:
   
    a. Ligar ao nó principal do VM utilizando uma conta de domínio que seja membro do grupo Administradores local. Por exemplo, utilize a conta de administrador que configurou quando criou o VM de nó principal.
   
    b. Para uma configuração predefinida do nó principal, inicie o Windows PowerShell como administrador e escreva o seguinte:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Pode demorar alguns minutos para os serviços HPC Pack iniciar.
   
    Nó principal adicionais para opções de configuração, escreva `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Passos seguintes
* Agora, pode trabalhar com o nó principal do cluster HPC Pack. Por exemplo, inicie o Gestor de clusters HPC e concluir o [lista de tarefas de implementação](https://technet.microsoft.com/library/jj884141.aspx).
* Se pretender aumentar a cluster computação capacidade a pedido, adicione [Azure burst nós](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) num serviço em nuvem. 
* Tente executar uma carga de trabalho de teste no cluster. Por exemplo, consulte o HPC Pack [guia de introdução](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
