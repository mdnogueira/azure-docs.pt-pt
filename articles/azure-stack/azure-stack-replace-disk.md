---
title: "Substituir um disco físico na pilha do Azure | Microsoft Docs"
description: "Descreve o processo para como substituir um disco físico na pilha do Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 26e5e9f8882cc764922a2cbf0f39e7a3d1b6995b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Substituir um disco físico na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Este artigo descreve o processo geral para substituir um disco físico na pilha do Azure. Se falhar um disco físico, deve substitui-lo logo que possível.

Pode utilizar este procedimento para sistemas integrados e para implementações de kit de desenvolvimento que tenham acesso frequente-swappable discos.

Substituição do disco real passos variam com base no seu fornecedor de hardware de fabricante de equipamento original (OEM). Consulte a documentação do fornecedor campo unidade substituível em (FRU) para obter passos detalhados que são específicas para o seu sistema. 

## <a name="review-disk-alert-information"></a>Reveja as informações de alerta de disco
Quando um disco falha, receberá um alerta que indica que conectividade foi perdida a um disco físico. 

 ![Perdeu a conectividade de alerta que mostra a disco físico](media/azure-stack-replace-disk/DiskAlert.png)

Se abrir o alerta, a descrição do alerta contém o nó de unidade de escala e a localização de bloco físico exato para o disco que tem de substituir. Ainda mais a pilha do Azure ajuda-o a identificar o disco com falhas utilizando o LED indicador capacidades.

 ## <a name="replace-the-disk"></a>Substituir o disco

Siga FRU instruções sobre o fornecedor de hardware de OEM a substituição de disco real.

Para impedir a utilização de um disco não suportado num sistema integrado, o sistema bloqueia discos que não são suportados pelo seu fornecedor. Se tentar utilizar um disco não suportado, um novo alerta indica que um disco tem foi colocado em quarentena devido a um modelo não suportado ou o firmware.

Depois de as substituir o disco, a pilha de Azure Deteta o disco novo automaticamente e inicia o processo de reparação do disco virtual.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Verificar o estado de reparação de disco virtual
 
 Depois de substituir o disco, pode monitorizar o estado de funcionamento de disco virtual e repare o progresso da tarefa, utilizando o ponto final com privilégios. Siga estes passos a partir de qualquer computador que tenha conectividade de rede para o ponto final com privilégios.

1. Abra uma sessão do Windows PowerShell e ligue para o ponto final com privilégios.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Execute o seguinte comando para ver o estado de funcionamento do disco virtual:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Saída do PowerShell do comando Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Execute o seguinte comando para ver o estado atual da tarefa de armazenamento:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Saída do PowerShell do comando Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Resolver problemas de reparação de disco virtual

Se o disco virtual reparar tarefa é apresentada bloqueada, execute o seguinte comando para reiniciar a tarefa:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
