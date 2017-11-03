---
title: "Gerir listas de controlo de acesso de ponto final do Azure | PowerShell | Clássico | Microsoft Docs"
description: Saiba como gerir ACLs com o PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Gerir listas de controlo de acesso de ponto final com o PowerShell no modelo de implementação clássica
Pode criar e gerir o controlo de acesso de rede lista (ACLs) para pontos finais utilizando o Azure PowerShell ou no Portal de gestão. Neste tópico, encontrará procedimentos para tarefas comuns de ACL que pode realizar com o PowerShell. Para obter a lista do Azure PowerShell cmdlets consulte [Cmdlets de gestão do Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Para obter mais informações sobre as ACLs, consulte [o que é uma lista de controlo de acesso de rede (ACL)?](virtual-networks-acl.md). Se pretender gerir as ACLs utilizando o Portal de gestão, consulte [como conjunto de cópias de segurança de pontos finais para uma Máquina Virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gerir ACLs de rede utilizando o Azure PowerShell
Pode utilizar os cmdlets do PowerShell do Azure para criar, remover e configurar (conjunto) rede acesso as listas de controlo (ACLs). Iremos tiver incluído alguns exemplos de algumas das formas como pode configurar uma ACL com o PowerShell.

Para obter uma lista completa dos cmdlets do PowerShell de ACL, pode utilizar qualquer um dos seguintes procedimentos:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Criar uma ACL de rede com regras que permitem o acesso a partir de uma sub-rede remota
O exemplo abaixo ilustra uma forma de criar uma novo ACL que contém as regras. Esta ACL, em seguida, é aplicada a um ponto final da máquina virtual. As regras ACL no exemplo abaixo permitirá o acesso a partir de uma sub-rede remota. Para criar uma nova ACL de rede com regras de autorização para uma sub-rede remota, abra uma ISE do PowerShell do Azure. Copie e cole o script abaixo, configurar o script com os seus próprios valores e, em seguida, execute o script.

1. Crie o novo objeto ACL de rede.
   
        $acl1 = New-AzureAclConfig
2. Defina uma regra que permite acesso de sub-rede remota. No exemplo abaixo, definir regra *100* (que tem prioridade sobre regra 200 e superior) para permitir que a sub-rede remota *10.0.0.0/8* acesso ao ponto final de máquina virtual. Substitua os valores pelos seus próprios requisitos de configuração. O nome "Configuração de ACL de SharePoint" deve ser substituído com o nome amigável que pretende ligar esta regra.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Para as regras adicionais, repita o cmdlet, substituindo os valores com os seus próprios requisitos de configuração. Não se esqueça de alterar a regra de número de ordem para refletir a ordem em que pretenda que as regras sejam aplicadas. O menor número de regras tem precedência sobre o número mais alto.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Em seguida, pode criar um novo ponto final (Add) ou definir a ACL para um ponto final existente (conjunto). Neste exemplo, iremos adicionar um novo ponto final máquina virtual denominado "web" e atualizar o ponto final da máquina virtual com as definições de ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Em seguida, combine os cmdlets e execute o script. Neste exemplo, os cmdlets combinados seria este aspeto:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Remover uma regra de ACL de rede que permite acesso de sub-rede remota
O exemplo abaixo ilustra uma forma para remover uma regra ACL de rede.  Para remover uma regra de ACL de rede com regras de autorização para uma sub-rede remota, abra uma ISE do PowerShell do Azure. Copie e cole o script abaixo, configurar o script com os seus próprios valores e, em seguida, execute o script.

1. Primeiro passo é obter o objeto de ACL de rede para o ponto final de máquina virtual. Em seguida, irá remover a regra de ACL. Neste caso, estamos a removê-lo por ID de regra. Isto removerá o ID de regra 0 apenas da ACL. Não remove o objeto ACL do ponto final da máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Em seguida, tem de aplicar o objeto de ACL de rede para o ponto final de máquina virtual e atualize a máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Remover uma ACL de rede de um ponto final da máquina virtual
Em certos cenários, pode querer remover um objeto de ACL de rede de um ponto final da máquina virtual. Para tal, abra uma ISE do PowerShell do Azure. Copie e cole o script abaixo, configurar o script com os seus próprios valores e, em seguida, execute o script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
[O que é uma lista de controlo de acesso de rede (ACL)?](virtual-networks-acl.md)

