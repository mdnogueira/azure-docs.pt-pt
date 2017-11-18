---
title: "Utilizar o PowerShell para criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure | Microsoft Docs"
description: "Utilize o PowerShell para criar uma atribuição de política do Azure para identificar recursos incompatíveis."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 5c00d50817e40de0a43d05eb85662b494247d8fa
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure com o PowerShell

O primeiro passo na conformidade de compreender no Azure é saber onde tem de estar posicionados com os seus próprios recursos atuais. Este guia de introdução os passos do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, irá com êxito identificou que máquinas virtuais não estiver a utilizar discos geridos e estão, por conseguinte, *incompatíveis*.


O PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este detalhes guia através do PowerShell para criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Este guia requer o Azure PowerShell versão 4.0 ou posterior do módulo. Executar ```Get-Module -ListAvailable AzureRM``` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

Antes de começar, certifique-se de que a versão mais recente do PowerShell está instalada. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="opt-in-to-azure-policy"></a>Escolher a política do Azure

Política do Azure está agora disponível em pré-visualização pública e tem de registar para pedir acesso.

1. Aceda a política do Azure em https://aka.ms/getpolicy e selecione **inscrever-se** no painel esquerdo.

   ![Pesquisa para a política](media/assign-policy-definition/sign-up.png)

2. Optar ativamente por participar na política do Azure ao selecionar as subscrições no **subscrição** lista que gostaria de trabalhar com. Em seguida, selecione **registar**.

   ![Optar por utilizar a política do Azure](media/assign-policy-definition/preview-opt-in.png)

   O pedido é aprovado automaticamente para a pré-visualização. Aguarde até 30 minutos para o sistema processe o registo.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Este guia de introdução, iremos criar uma atribuição de política e atribuir a *auditoria máquinas de virtuais sem discos geridos* definição. Esta definição de política irá identificar recursos que não está em conformidade com as condições definidas na definição de política.

Siga estes passos para criar uma nova atribuição de política.

Execute o seguinte comando para ver todas as definições de política e a descobrir o que pretende atribuir:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Política do Azure vem com definições de política já incorporadas que pode utilizar. Poderá ver definições de política incorporados, tais como:

- Impor a etiqueta e o respetivo valor
- Aplicar a etiqueta e o respetivo valor
- Requer a versão do SQL Server 12.0

Em seguida, atribua a definição de política para o âmbito pretendido utilizando a `New-AzureRmPolicyAssignment` cmdlet.

Para este tutorial, estamos a proporcionar as seguintes informações para o comando:
- Apresentar **nome** para a atribuição de política. Neste caso, vamos utilizar máquinas virtuais de auditoria sem discos geridos.
- **Política** – esta é a definição de política, com base desativado que estiver a utilizar para criar a atribuição. Neste caso, é a definição de política – *auditoria máquinas de virtuais sem discos geridos*
- A **âmbito** - um âmbito determina que recursos ou agrupamento de recursos de atribuição de política obtém imposta. Podem ir de uma subscrição para grupos de recursos. Neste exemplo, vamos está a atribuir a definição de política para o **FabrikamOMS** grupo de recursos.
- **$definition** – é necessário fornecer o ID de recurso da definição de política – neste caso, estamos a utilizar o ID de definição de política - *auditoria máquinas de virtuais sem discos geridos*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Agora, está pronto para identificar recursos incompatíveis para compreender o estado de compatibilidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos incompatíveis

1. Navegue de volta para a página de destino de política do Azure.
2. Selecione **conformidade** no painel esquerdo e procure o **atribuição de política** que criou.

   ![Conformidade com a política](media/assign-policy-definition/policy-compliance.png)

   Se existirem quaisquer recursos existentes que não são compatíveis com esta atribuição de nova, irão aparecer no **recursos não compatível** separador, conforme mostrado acima.

## <a name="clean-up-resources"></a>Limpar recursos

Noutros guias de nesta coleção baseiam-se este guia de introdução. Se pretender continuar a trabalhar nos tutoriais subsequentes, não limpeza até os recursos criados neste guia de introdução. Se não pretender continuar, elimine a atribuição de que criou ao executar o comando:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, é atribuído a uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas, para garantir que **futuras** recursos que obterem criados estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./create-manage-policy.md)
