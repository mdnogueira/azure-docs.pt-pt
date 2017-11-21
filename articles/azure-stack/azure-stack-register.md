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
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
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

Se não tiver uma subscrição do Azure que cumpra estes requisitos, pode [criar uma conta do Azure gratuita aqui](https://azure.microsoft.com/en-us/free/?b=17.06). Registar o Azure pilha incorreu sem qualquer custo na sua subscrição do Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registar o fornecedor de recursos do Azure pilha no Azure
> [!NOTE] 
> Este passo deve ser concluído apenas uma vez num ambiente de pilha do Azure.
>

1. Inicie uma sessão do Powershell como administrador.
2. Inicie sessão na conta do Azure que é proprietário da subscrição do Azure (pode utilizar o cmdlet Login-AzureRmAccount para iniciar sessão e quando iniciar sessão, certifique-se definir o parâmetro - EnvironmentName para "AzureCloud").
3. Registar o fornecedor de recursos do Azure "Microsoft.AzureStack."

**Exemplo:** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Registar a pilha do Azure com o Azure

> [!NOTE]
> Todos estes passos tem de ser executados a partir de uma máquina que tenha acesso ao ponto final com privilégios. Para o Kit de desenvolvimento de pilha do Azure, seria o computador anfitrião. Se estiver a utilizar um sistema integrado, contacte o operador de pilha do Azure.
>

1. Abra uma consola do PowerShell como administrador e [instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).  

2. Adicione a conta do Azure que irá utilizar para registar a pilha do Azure. Para tal, execute o `Add-AzureRmAccount` cmdlet sem quaisquer parâmetros. É-lhe pedido que introduza as credenciais da conta do Azure e poderá ter de utilizar a autenticação de fator 2, com base na configuração da sua conta.  

3. Se tiver várias subscrições, execute o seguinte comando para selecionar aquela que pretende utilizar:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Eliminar quaisquer versões dos módulos do Powershell que correspondem ao registo existentes e [transferir a versão mais recente do mesmo a partir do GitHub](azure-stack-powershell-download.md).  

5. O diretório de "AzureStack-ferramentas-master" que é criado no passo anterior, navegue para a pasta "Registo" e importe o módulo ".\RegisterWithAzure.psm1":  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. Na mesma sessão do PowerShell, execute o seguinte script. Quando lhe forem pedidas as credenciais, especifique `azurestack\cloudadmin` como o utilizador e a palavra-passe é igual à que utilizou para o administrador local durante a implementação.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Parâmetro | Descrição |
   | -------- | ------------- |
   | CloudAdminCredential | As credenciais de domínio de nuvem que são utilizadas para [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de utilizador está no formato "\<domínio do Azure da pilha\>\cloudadmin". No kit de desenvolvimento, o nome de utilizador é definida para "azurestack\cloudadmin". Se estiver a utilizar um sistema integrado, contacte o operador de pilha do Azure para obter este valor.|
   | AzureSubscriptionId | A subscrição do Azure que utiliza para registar a pilha do Azure.|
   | AzureDirectoryTenantName | Nome do diretório de inquilino do Azure que está associado a sua subscrição do Azure. O recurso de registo será criado no inquilino neste diretório. |
   | PrivilegedEndpoint | Uma previamente configurada PowerShell consola remota que fornece capacidades, como a recolha de registos e outra post tarefas de implementação. Para o kit de desenvolvimento, o ponto final com privilégios está alojado na máquina virtual "AzS ERCS01". Se estiver a utilizar um sistema integrado, contacte o operador de pilha do Azure para obter este valor. Para obter mais informações, consulte o [utilizando o ponto final com privilégios](azure-stack-privileged-endpoint.md) tópico.|
   | BillingModel | O modelo de faturação que utiliza a sua subscrição. Valores para este parâmetro tem-"Capacidade", "PayAsYouUse" e "Desenvolvimento" de permitidos. Para o kit de desenvolvimento, este valor é definido como "Desenvolvimento". Se estiver a utilizar um sistema integrado, contacte o operador de pilha do Azure para obter este valor. |

7. O script estiver concluído, verá uma mensagem "em ativação Azure (este passo pode demorar até 10 minutos a concluir) de pilha." 

## <a name="verify-the-registration"></a>Verifique o registo

1. Inicie sessão no portal do administrador (https://adminportal.local.azurestack.external).
2. Clique em **mais serviços** > **Marketplace gestão** > **adicionar a partir do Azure**.
3. Se vir uma lista de itens disponíveis a partir do Azure (por exemplo, WordPress), a ativação foi efetuada com êxito.

> [!NOTE]
> Após a conclusão do registo, o aviso de Active Directory para registar não deixará de ser apresentada.

## <a name="next-steps"></a>Passos seguintes

[Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)

