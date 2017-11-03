---
title: Registar a pilha do Azure | Microsoft Docs
description: "Registe pilha do Azure com a sua subscrição do Azure."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registar a pilha do Azure com a sua subscrição do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pode registar [Azure pilha](azure-stack-poc.md) com o Azure para transferir os itens do marketplace a partir do Azure e configurar dados de comércio relatórios de volta à Microsoft. 

> [!NOTE]
>Registo é recomendado porque permite-lhe testar importante funcionalidade da pilha do Azure, como os relatórios de utilização e de sindicação do marketplace. Depois de registar pilha do Azure, a utilização é comunicada para comércio do Azure. Pode vê-lo sob a subscrição utilizada para o registo. Utilizadores do Azure do Kit de desenvolvimento de pilha não são-lhe cobrados de qualquer utilização que possam comunicam.
>


## <a name="get-azure-subscription"></a>Obter a subscrição do Azure

Antes de registar a pilha do Azure com o Azure, tem de ter:

- O ID de subscrição de uma subscrição do Azure. Para obter o ID, inicie sessão no Azure, clique em **mais serviços** > **subscrições**, clique na subscrição que pretende utilizar, e, em **Essentials** pode encontrar o **ID de subscrição**. China, Datacenters e subscrições de nuvem do Governo dos EUA não são atualmente suportados.
- O nome de utilizador e palavra-passe para uma conta que é proprietário da subscrição (são suportadas contas MSA/2FA).
- O Azure Active Directory para a subscrição do Azure. Pode encontrar este diretório no Azure ao passar por cima do seu avatar no canto superior direito do portal do Azure. 
- [Registar o fornecedor de recursos do Azure pilha](#register-azure-stack-resource-provider-in-azure).

Se não tiver uma subscrição do Azure que cumpra estes requisitos, pode [criar uma conta do Azure gratuita aqui](https://azure.microsoft.com/en-us/free/?b=17.06). Registar o Azure pilha incorreu sem qualquer custo na sua subscrição do Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registar o fornecedor de recursos do Azure pilha no Azure
> [!NOTE] 
> Este passo só tem de ser concluída, uma vez, num ambiente de pilha do Azure.
>

1. Inicie o ISE do Powershell como administrador.
2. Inicie sessão na conta do Azure que é proprietário da subscrição do Azure com o parâmetro - EnvironmentName definido como "AzureCloud".
3. Registe o fornecedor de recursos do Azure "Microsoft.AzureStack".

Exemplo: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Registar a pilha do Azure com o Azure

> [!NOTE]
>Todos estes passos têm de ser concluídos no computador anfitrião.
>

1. [Instale o PowerShell para a pilha do Azure](azure-stack-powershell-install.md). 
2. Copiar o [RegisterWithAzure.psm1 script](https://go.microsoft.com/fwlink/?linkid=842959) para uma pasta (como C:\Temp).
3. Inicie o ISE do PowerShell como administrador e importe o módulo de RegisterWithAzure.    
4. A partir do RegisterWithAzure.psm1 script, execute o módulo de AzsRegistration adicionar. Substitua os marcadores de posição seguintes: 
    - *YourCloudAdminCredential* é um objeto do PowerShell que contém as credenciais de domínio local para o domain\cloudadmin (para o kit de desenvolvimento, o que é azurestack\cloudadmin).
    - *YourAzureSubscriptionID* é o ID da subscrição do Azure que pretende utilizar para registar a pilha do Azure.
    - *YourAzureDirectoryTenantName* é o nome do diretório do inquilino do Azure associado à sua subscrição do Azure. O recurso de registo será criado no inquilino neste diretório. 
    - *YourPrivilegedEndpoint* é o nome do [privilegiado ponto final](azure-stack-privileged-endpoint.md).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. Na janela de pop-up início de sessão, introduza as credenciais da sua subscrição do Azure.

## <a name="verify-the-registration"></a>Verifique o registo

1. Inicie sessão no portal do administrador (https://adminportal.local.azurestack.external).
2. Clique em **mais serviços** > **Marketplace gestão** > **adicionar a partir do Azure**.
3. Se vir uma lista de itens disponíveis a partir do Azure (por exemplo, WordPress), a ativação foi efetuada com êxito.

> [!NOTE]
> Após a conclusão do registo, o aviso de Active Directory para registar não deixará de ser apresentada.

## <a name="next-steps"></a>Passos seguintes

[Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)

