---
title: "Gerir os nós de computação de cluster HPC Pack | Microsoft Docs"
description: "Saiba mais sobre as ferramentas de script do PowerShell para adicionar, remover, iniciar e parar nós de computação de cluster HPC Pack 2012 R2 no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: dc9f354191b9e80ff6a01bd401a874c6998bda79
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gerir o número e a disponibilidade de nós de computação num cluster HPC Pack no Azure
Se tiver criado um cluster HPC Pack 2012 R2 em VMs do Azure, é aconselhável formas de facilmente adicionar, remover, iniciar (aprovisionar) ou a parar (deprovision) algumas VMs de nó do cluster de computação. Para efetuar estas tarefas, execute scripts do PowerShell do Azure que estão instaladas no nó principal do VM. Estes scripts ajudam a controlar o número e a disponibilidade dos seus recursos de cluster HPC Pack, pelo que pode controlar os custos.

> [!IMPORTANT] 
> Este artigo aplica-se apenas a clusters HPC Pack 2012 R2 no Azure criadas com o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> Além disso, os scripts do PowerShell descritos neste artigo não estão disponíveis no HPC Pack 2016.

## <a name="prerequisites"></a>Pré-requisitos
* **Cluster HPC Pack 2012 R2 em VMs do Azure**: criar um cluster HPC Pack 2012 R2 no modelo de implementação clássica. Por exemplo, pode automatizar a implementação utilizando a imagem de VM do HPC Pack 2012 R2 no Azure Marketplace e um script do PowerShell do Azure. Para informações e os pré-requisitos, consulte [criar um Cluster HPC com o script de implementação do HPC Pack IaaS](hpcpack-cluster-powershell-script.md).
  
    Após a implementação, localizar o nó scripts de gestão no % CCP\_HOME % bin pasta no nó principal. Execute cada um dos scripts como administrador.
* **Certificado de gestão ou o ficheiro de definições de publicação do Azure**: precisa de executar um dos seguintes no nó principal:
  
  * **Ficheiro de definições de publicação de importação do Azure**. Para tal, execute os seguintes cmdlets PowerShell do Azure no nó principal:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Configurar o certificado de gestão do Azure no nó principal**. Se tiver o ficheiro. cer, importe-o no arquivo de certificados de CurrentUser\My e, em seguida, execute o seguinte cmdlet do PowerShell do Azure para o seu ambiente do Azure (AzureCloud ou AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Adicionar o nó de computação VMs
Adicionar nós de computação com a **adicionar HpcIaaSNode.ps1** script.

### <a name="syntax"></a>Sintaxe
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parâmetros
* **ServiceName**: nome do serviço em nuvem que novas VMs de nó de computação são adicionados ao.
* **ImageName**: nome de imagem de VM do Azure, que pode ser obtido através do portal clássico do Azure ou o cmdlet do Azure PowerShell **Get-AzureVMImage**. A imagem tem de cumprir os seguintes requisitos:
  
  1. Tem de ser instalado um sistema operativo Windows.
  2. Pacote HPC tem de estar instalado na função de nó de computação.
  3. A imagem tem de ser uma imagem privada na categoria de utilizador, não é uma imagem de VM do Azure pública.
* **Quantidade**: número de VMs a adição do nó de computação.
* **InstanceSize**: tamanho de nó de computação VMs.
* **DomainUserName**: nome de utilizador de domínio, que é utilizada para associar novas VMs ao domínio.
* **DomainUserPassword**: palavra-passe do utilizador de domínio.
* **NodeNameSeries** (opcional): padrão de nomenclatura para os nós de computação. O formato deve ser &lt; *raiz\_nome*&gt;&lt;*iniciar\_número*&gt;%. Por exemplo, MyCN % 10% significa uma série dos nomes de nó de computação a partir de MyCN11. Se não for especificado, o script utiliza o nó configurado nomenclatura série no HPC cluster.

### <a name="example"></a>Exemplo
O exemplo seguinte adiciona o nó de computação de grande tamanho 20 VMs no serviço em nuvem *hpcservice1*, com base na imagem de VM *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Remover o nó de computação VMs
Remover nós de computação com a **remover HpcIaaSNode.ps1** script.

### <a name="syntax"></a>Sintaxe
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parâmetros
* **Nome**: os nomes de nós de cluster a remover. São suportados carateres universais. O nome do conjunto de parâmetros é o nome. Não é possível especificar ambas as **nome** e **nó** parâmetros.
* **Nó**: HpcNode o objeto para os nós a serem removidos, que pode ser obtido através do cmdlet do HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetros é nó. Não é possível especificar ambas as **nome** e **nó** parâmetros.
* **DeleteVHD** (opcional): a definição para eliminar os discos associados para as VMs que são removidas.
* **Forçar** (opcional): a definição para forçar nós HPC offline antes de removê-los.
* **Confirmar** (opcional): pedir confirmação antes de executar o comando.
* **WhatIf**: definição para descrever o que aconteceria se executasse o comando sem executar realmente o comando.

### <a name="example"></a>Exemplo
O exemplo seguinte força offline nós com nomes a partir *HPCNode-CN -* e -los remove nós e os respetivos discos associados.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Iniciar as VMs do nó de computação
Início de computação nós com o **início HpcIaaSNode.ps1** script.

### <a name="syntax"></a>Sintaxe
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parâmetros
* **Nome**: os nomes de nós do cluster para ser iniciado. São suportados carateres universais. O nome do conjunto de parâmetros é o nome. Não é possível especificar ambas as **nome** e **nó** parâmetros.
* **Nó**-HpcNode o objeto para os nós ser iniciado, o que pode ser obtido através do cmdlet do HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetros é nó. Não é possível especificar ambas as **nome** e **nó** parâmetros.

### <a name="example"></a>Exemplo
O exemplo seguinte inicia nós com nomes a partir *HPCNode-CN -*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Parar as VMs do nó de computação
Parar de nós de computação com a **Stop-HpcIaaSNode.ps1** script.

### <a name="syntax"></a>Sintaxe
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parâmetros
* **Nome**-os nomes de nós do cluster seja interrompido. São suportados carateres universais. O nome do conjunto de parâmetros é o nome. Não é possível especificar ambas as **nome** e **nó** parâmetros.
* **Nó**: HpcNode o objeto para os nós seja interrompido, que pode ser obtido através do cmdlet do HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetros é nó. Não é possível especificar ambas as **nome** e **nó** parâmetros.
* **Forçar** (opcional): a definição para forçar nós HPC offline antes de pará-los.

### <a name="example"></a>Exemplo
O exemplo seguinte força nós offline com nomes a partir *HPCNode-CN -* e, em seguida, para os nós.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Passos seguintes
* Para automaticamente aumentar ou diminuir os nós de cluster, de acordo com a atual carga de trabalho de trabalhos e tarefas no cluster, consulte o artigo [automaticamente aumentar e diminuir os recursos de cluster HPC Pack no Azure, de acordo com a carga de trabalho de cluster](hpcpack-cluster-node-autogrowshrink.md).

