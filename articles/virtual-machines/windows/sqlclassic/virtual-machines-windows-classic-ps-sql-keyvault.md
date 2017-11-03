---
title: "Integrar o Cofre de chaves com o SQL Server em VMs do Windows no Azure (clássica) | Microsoft Docs"
description: "Saiba como automatizar a configuração de encriptação do SQL Server para utilização com o Cofre de chaves do Azure. Este tópico explica como utilizar a integração do Cofre de chaves do Azure com o SQL Server a criar máquinas virtuais no modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a9ac5763bb934bd0646e47c3936f7bdd0d603b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurar a integração do Cofre de chaves do Azure para o SQL Server em Virtual Machines do Azure (clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Descrição geral
Existem várias funcionalidades de encriptação do SQL Server, tais como [encriptação de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), e [encriptação de cópias de segurança](https://msdn.microsoft.com/library/dn449489.aspx). Estes formulários da encriptação tem de gerir e armazenar as chaves criptográficas que utiliza para a encriptação. O serviço Cofre de chaves do Azure (AKV) foi concebido para melhorar a segurança e gestão destas chaves numa localização segura e altamente disponível. O [conector do SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server para utilizar estas chaves a partir do Cofre de chaves do Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Se estiver a executar o SQL Server com máquinas no local, existem [passos que pode seguir para aceder ao Cofre de chaves do Azure a partir do seu computador do SQL Server no local](https://msdn.microsoft.com/library/dn198405.aspx). Mas para o SQL Server em VMs do Azure, pode poupar tempo ao utilizar o *integração do Cofre de chaves do Azure* funcionalidade. Com alguns cmdlets Azure PowerShell para ativar esta funcionalidade, pode automatizar a configuração necessária para uma VM do SQL Server para o seu Cofre de chaves de acesso.

Quando esta funcionalidade está ativada, instala o conector do SQL Server, configura automaticamente o fornecedor EKM para aceder ao Cofre de chaves do Azure e cria a credencial para que possa aceder ao seu cofre. Se tem em conta os passos na documentação do mencionadas anteriormente no local, pode ver que esta funcionalidade automatiza os passos 2 e 3. É a única coisa que ainda terá de efetuar manualmente a criação do Cofre de chaves e as chaves. A partir daí, a configuração completa da sua VM do SQL Server é um processo automatizada. Depois desta funcionalidade concluída esta configuração, pode executar as instruções T-SQL para começar a encriptar as cópias de segurança ou bases de dados, como faria normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar a integração AKV
Utilize o PowerShell para configurar a integração do Cofre de chaves do Azure. As secções seguintes fornecem uma descrição geral dos parâmetros necessários e, em seguida, um script do PowerShell de exemplo.

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão de IaaS do SQL Server
Primeiro, [instalar a extensão do SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreender os parâmetros de entrada
A tabela seguinte lista os parâmetros necessários para executar o script do PowerShell na secção seguinte.

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| **$akvURL** |**O URL do Cofre de chaves** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome Principal de serviço** |"fde2b411 - 33d 5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segredo Principal de serviço** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =" |
| **$credName** |**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial. |"mycred1" |
| **$vmName** |**Nome da máquina virtual**: O nome de uma VM do SQL Server criado anteriormente. |"myvmname" |
| **$serviceName** |**Nome do serviço**: nome do serviço em nuvem que está associado a VM do SQL Server. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Ativar a integração AKV com o PowerShell
O **New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet cria um objeto de configuração para a funcionalidade de integração do Cofre de chaves do Azure. O **conjunto AzureVMSqlServerExtension** configura esta integração com o **KeyVaultCredentialSettings** parâmetro. Os passos seguintes mostram como utilizar estes comandos.

1. No Azure PowerShell, primeiro de configurar os parâmetros de entrada com os seus valores específicos conforme descrito nas secções anteriores deste tópico. O script seguinte é um exemplo.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Em seguida, utilize o seguinte script para configurar e ativar a integração AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão de agente do SQL Server IaaS irá atualizar a VM do SQL Server com esta configuração de novo.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

