---
title: Mover um AWS VMs do Windows para o Azure | Microsoft Docs
description: "Mova uma instância do Amazon Web Services (AWS) EC2 Windows para máquinas virtuais do Azure com o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 7d2b498d3f84c4fd6cccf97c6d7781f293f5b395
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Mover uma VM do Windows do Amazon Web Services (AWS) para o Azure com o PowerShell

Se estiver a avaliar virtual machines do Azure para alojar cargas de trabalho, pode exportar uma instância de VM do Windows do Amazon Web Services (AWS) EC2 existente e carregar o disco rígido virtual (VHD) para o Azure. Assim que o VHD é carregado, pode criar uma nova VM no Azure do VHD. 

Este tópico aborda a mover uma única VM do AWS para o Azure. Se pretender mover as VMs do AWS para o Azure à escala, consulte [migrar máquinas virtuais nos Amazon Web Services (AWS) para o Azure com o Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Preparar a VM 
 
Pode carregar os VHDs generalizados e especializados para o Azure. Cada tipo requer a preparação da VM antes de exportar a partir do AWS. 

- **Generalizado VHD** -um VHD generalizado apresentou todas as informações da sua conta pessoal removidas utilizando Sysprep. Se tenciona utilizar o VHD como uma imagem para criar novas VMs do, deve: 
 
    * [Preparar uma VM do Windows](prepare-for-upload-vhd-image.md).  
    * Generalize a máquina virtual utilizando o Sysprep.  

 
- **Especializada VHD** -um VHD especializado mantém as contas de utilizador, aplicações e outros dados de estado de original VM. Se pretender utilizar o VHD como-para criar uma nova VM, certifique-se de que os seguintes passos são concluídos.  
    * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md). **Não** generalize a VM com o Sysprep. 
    * Remova quaisquer agentes que estão instalados na VM (ou seja, as ferramentas do VMware) e ferramentas de Virtualização do convidado. 
    * Certifique-se de que a VM está configurada para solicitar o respetivo endereço IP e as definições de DNS através do DHCP. Isto garante que o servidor obtém um endereço IP dentro da VNet durante o arranque.  


## <a name="export-and-download-the-vhd"></a>Exportar e transferir o VHD 

Exporte a instância de EC2 a um VHD num registo de Amazon S3. Siga os passos descritos no tópico de documentação do Amazon [exportar uma instância como uma VM utilizando o VM para importar/exportar](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) e execute o [criar instância-exportação-tarefas](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) comando para exportar a instância de EC2 para um ficheiro VHD. 

O ficheiro exportado de VHD é guardado no registo do Amazon S3 que especificar. A sintaxe básica para exportar o VHD é abaixo, substitua apenas o texto do marcador de posição no <brackets> com as suas informações.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Assim que o VHD foi exportado, siga as instruções em [como transferir um objeto de um registo de S3?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) transferir o ficheiro VHD a partir do registo de S3. 

> [!IMPORTANT]
> Transferência de dados de encargos AWS taxas para transferir o VHD. Consulte [preços do Amazon S3](https://aws.amazon.com/s3/pricing/) para obter mais informações.


## <a name="next-steps"></a>Passos seguintes

Agora pode carregar o VHD para o Azure e criar uma nova VM. 

- Se executou o Sysprep na sua origem para **Generalizar** -lo antes de exportar, consulte [carregar um VHD generalizado e utilizá-la para criar um novo VMs no Azure](upload-generalized-managed.md)
- Se não foi possível executar o Sysprep antes de exportar, o VHD é considerado **especializadas**, consulte [carregar um VHD especializado para o Azure e criar uma nova VM](create-vm-specialized.md)

 
