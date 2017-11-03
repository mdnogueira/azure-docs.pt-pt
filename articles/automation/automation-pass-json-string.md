---
title: "Transferir um objeto JSON para um runbook de automatização do Azure | Microsoft Docs"
description: "Como passar os parâmetros para um runbook como um objeto JSON"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "json do runbook do PowerShell, automatização do azure"
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: e9352ac1a346537d6214590be6dbc9db7ca0f461
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Transferir um objeto JSON para um runbook de automatização do Azure

Pode ser útil armazenar dados que pretende passar para um runbook num ficheiro JSON.
Por exemplo, poderá criar um ficheiro JSON que contém todos os parâmetros que pretende passar para um runbook.
Para tal, tem de converter o JSON para uma cadeia e, em seguida, converter a cadeia de um objeto do PowerShell antes de passar o respetivo conteúdo para o runbook.

Neste exemplo, vamos criar um script do PowerShell que chama [início AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar um runbook de PowerShell, transmitir o conteúdo de JSON para o runbook.
O runbook de PowerShell inicia uma VM do Azure, ao obter os parâmetros para a VM de JSON que foi passado.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[inscrever-se numa conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automatização](automation-sec-configure-azure-runas-account.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos esta máquina, pelo que não deve ser uma VM de produção.
* O Azure Powershell instalado num computador local. Consulte [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-json-file"></a>Criar o ficheiro JSON

Escreva o seguinte teste num ficheiro de texto e guarde-o como `test.json` algures no seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook do PowerShell com o nome de "Teste-Json" na automatização do Azure.
Para saber como criar um novo runbook do PowerShell, consulte [o meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook tem de colocar um objeto como um parâmetro de entrada.

O runbook, em seguida, pode utilizar as propriedades definidas no JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Guarde e publicar este runbook na sua conta de automatização.

## <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora pode chamar o runbook do seu computador local utilizando o Azure PowerShell.
Execute os seguintes comandos do PowerShell:

1. Inicie sessão no Azure:
   ```powershell
   Login-AzureRmAccount
   ```
    Lhe for pedido para introduzir as suas credenciais do Azure.
1. Obter o conteúdo do ficheiro JSON e convertê-lo para uma cadeia:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath`é o caminho onde guardou o ficheiro JSON.
1. Converter os conteúdos de cadeia de `$json` para um objeto do PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Criar uma hashtable para os parâmetros para `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Tenha em atenção que está a definir o valor de `Parameters` para o objeto do PowerShell que contém os valores do ficheiro JSON. 
1. Iniciar o runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

O runbook utiliza os valores do ficheiro JSON para iniciar uma VM.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a edição de runbooks do PowerShell e o fluxo de trabalho do PowerShell com um editor de texto, consulte [editar textual runbooks na automatização do Azure](automation-edit-textual-runbook.md) 
* Para saber mais sobre criar e importar runbooks, consulte [criar ou importar um runbook na automatização do Azure](automation-creating-importing-runbook.md)


