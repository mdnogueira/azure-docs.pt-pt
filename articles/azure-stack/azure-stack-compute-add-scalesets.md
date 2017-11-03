---
title: "Disponibilizar os conjuntos de dimensionamento de máquina virtual na pilha do Azure"
description: "Saiba como um administrador da nuvem adicionar dimensionamento da máquina virtual para a pilha do Azure Marketplace"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
keywords: 
ms.openlocfilehash: 31aeb963bdf4fd32712bc6f29f64060ec1c77cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Disponibilizar os conjuntos de dimensionamento de máquina virtual na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Conjuntos de dimensionamento de máquina virtual são um recurso de computação de pilha do Azure. Pode utilizá-los para implementar e gerir um conjunto de máquinas virtuais idênticas. Com todas as máquinas virtuais configurados da mesma, conjuntos de dimensionamento não necessitam de pré-aprovisionamento de máquinas virtuais. É mais fácil criar serviços em grande escala que macrocomputação, macrodados e de cargas de trabalho de destino.

Este tópico orienta-o processo para disponibilizar conjuntos de dimensionamento no mercado de pilha do Azure. Depois de concluir este procedimento, os utilizadores podem adicionar conjuntos de dimensionamento de máquina virtual para as suas subscrições.

Conjuntos de dimensionamento de máquina virtual na pilha do Azure são como conjuntos de dimensionamento de máquina virtual no Azure. Para obter mais informações, consulte os vídeos seguintes:
* [Mark Russinovich talks Azure Scale Sets (Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure)](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets with Guy Bowerman (Conjuntos de Dimensionamento de Máquinas Virtuais, com Guy Bowerman)](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Na pilha do Azure, conjuntos de dimensionamento de Máquina Virtual não suporta o dimensionamento automático. Pode adicionar mais instâncias a uma escala definida utilizando o portal de pilha do Azure, modelos do Resource Manager ou PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
* **PowerShell e ferramentas**

   Instalar e PowerShell configurado para a pilha do Azure e as ferramentas de pilha do Azure. Consulte [começar a trabalhar com o PowerShell na pilha de Azure](azure-stack-powershell-configure-quickstart.md).

   Depois de instalar as ferramentas de pilha do Azure, certifique-se de importar o módulo do PowerShell seguinte (caminho relativa a. \ComputeAdmin pasta na pasta master de ferramentas de AzureStack):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Imagem do sistema operativo**

   Se ainda não adicionou uma imagem do sistema operativo para a pilha do Azure Marketplace, consulte o artigo [adicionar a imagem de VM do Windows Server 2016 para o mercado de pilha do Azure](azure-stack-add-default-image.md).

   Para obter suporte do Linux, transfira Ubuntu Server 16.04 e adicioná-lo utilizando ```Add-AzsVMImage``` com os seguintes parâmetros: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Adicionar o conjunto de dimensionamento de máquina virtual

Editar o seguinte script do PowerShell para o seu ambiente e, em seguida, execute-o para adicionar um dimensionamento de máquina virtual definido para a pilha do Azure Marketplace. 

``$User``é a conta que utiliza para ligar o portal de administrador. Por exemplo, serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Remover um conjunto de dimensionamento de máquina virtual

Para remover uma máquina virtual item da Galeria de conjunto de dimensionamento, execute o seguinte comando do PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> O item da Galeria não pode ser removido imediatamente. Poderá ter de atualizar o portal várias vezes antes de serem removidos do Marketplace.


## <a name="next-steps"></a>Passos seguintes
[Perguntas mais frequentes sobre pilha do Azure](azure-stack-faq.md)

