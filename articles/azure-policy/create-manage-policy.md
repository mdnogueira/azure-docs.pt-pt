---
title: "Utilizar a política do Azure para criar e gerir políticas para impor a compatibilidade organizacional | Microsoft Docs"
description: "Utilize a política de Azure para impor as normas, cumpre os requisitos regulamentares de conformidade e auditoria, controlar os custos, manter a consistência de segurança e o desempenho e impõe princípios de wide design enterprise."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/01/2017
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: adbf6e13efaad196c39e4fce0900fa40d7511122
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Criar e gerir políticas para impor a compatibilidade

Noções sobre como criar e gerir políticas no Azure é importante para permanecendo em conformidade com os padrões empresariais e contratos de nível de serviço. Neste tutorial, irá aprender a utilizar a política do Azure para fazer algumas das tarefas mais comuns relacionadas com a criar, atribuir e gerir políticas na sua organização, tais como:

> [!div class="checklist"]
> * Atribuir uma política para impor uma condição para recursos que cria no futuro
> * Criar e atribuir uma definição de iniciativa para controlar a conformidade para vários recursos
> * Resolver um recurso de não conformidade ou negado
> * Implementar uma nova política numa organização

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="opt-in-to-azure-policy"></a>Escolher a política do Azure

Política do Azure está agora disponível na pré-visualização limitada, por isso terá de registar para pedir acesso.

1. Aceda a política do Azure em https://aka.ms/getpolicy e selecione **inscrever-se** no painel esquerdo.

   ![Pesquisa para a política](media/assign-policy-definition/sign-up.png)

2. Optar ativamente por participar na política do Azure ao selecionar as subscrições no **subscrição** lista que gostaria de trabalhar com. Em seguida, selecione **registar**.

   A lista de subscrição inclui todas as subscrições do Azure.

   ![Optar ativamente por participar em utilizar a política do Azure](media/assign-policy-definition/preview-opt-in.png)

   Consoante a pedido, este poderá demorar até alguns dias para-nos aceitar o pedido de registo. Depois de aceite, obtém o pedido, será notificado através de e-mail que pode começar a utilizar o serviço.

## <a name="assign-a-policy"></a>Atribuir uma política

É o primeiro passo para impor a compatibilidade com a política do Azure para atribuir uma definição de política. Define uma definição de política em que condições de uma política é imposta e a ação a tomar. Neste exemplo, vamos atribuir uma definição de política incorporada chamada *requerem 12.0 da versão do SQL Server*, para impor a condição que todas as bases de dados do SQL Server tem de ser v12.0 para estar em conformidade.

1. Iniciar o serviço de política do Azure no portal do Azure, procurar e selecionar **política** no painel esquerdo.

   ![Pesquisa para a política](media/assign-policy-definition/search-policy.png)

2. Selecione **atribuições** no painel esquerdo da página política do Azure. Uma atribuição é uma política que tenha sido atribuída para ocorrer dentro de um âmbito específico.
3. Selecione **atribuir política** da parte superior do **atribuições** painel.

   ![Atribuir uma definição de política](media/create-manage-policy/select-assign-policy.png)

4. No **atribuir política** página, clique em ![botão de definição de política](media/assign-policy-definition/definitions-button.png) junto a **política** campo para abrir a lista de definições disponíveis.

   ![Definições de política disponíveis aberta](media/create-manage-policy/open-policy-definitions.png)

5. Selecione **requerem versão do SQL Server 12.0**.

   ![Localize uma política](media/create-manage-policy/select-available-definition.png)

6. Forneça uma apresentação **nome** para a atribuição de política. Neste caso, vamos utilizar *requerem o SQL Server versão 12.0*. Também pode adicionar opcional **Descrição**. A descrição fornece detalhes sobre a forma como esta atribuição de política assegura a todos os servidores SQL criados neste ambiente têm a versão 12.0.
7. Alterar o escalão de preço para **padrão** para se certificar de que a política é aplicada aos recursos existentes.

   Existem dois escalões de preços dentro de política do Azure – *livres* e *padrão*. Com o escalão gratuito, apenas pode impor políticas de recursos futuras, enquanto com padrão, pode também impor-los em recursos existentes para compreender melhor o seu estado de compatibilidade. Uma vez que estão na pré-visualização limitada, iremos não ainda libertadas um modelo de preços, pelo que não receberão uma fatura para selecionar *padrão*. Para mais informações sobre preços, observe: [preços de política do Azure](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

8. Selecione o **âmbito** -subscrição (ou grupo de recursos) registado anteriormente quando optado política do Azure. Um âmbito determina que recursos ou agrupamento de recursos de atribuição de política obtém imposta. Podem ir de uma subscrição para grupos de recursos.

   Para este exemplo, estamos a utilizar esta subscrição - **Dev de capacidade de análise do Azure**. A subscrição serão diferentes.

10. Selecione **atribuir**.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que Atribuímos a definição de política, vamos criar uma nova política para reduzir os custos, garantindo que as VMs criadas em todo o ambiente não podem ser na série de G. Desta forma, sempre que um utilizador na sua organização tenta criar a VM na série de G, o pedido irá obter negado.

1. Selecione **definição** em **criação** no painel esquerdo.

   ![Definição em criação](media/create-manage-policy/definition-under-authoring.png)

2. Selecione **+ definição de política**.
3. Introduza o seguinte:

   - O nome da definição de política - *necessitam de SKUs de VM mais pequeno do que a série de G*
   - A descrição do que a definição de política consiste em – esta definição de política impõe que todas as VMs criadas neste âmbito têm SKUs inferior a série de G para reduzir o custo.
   - A subscrição em que a definição de política irá em direto – neste caso, os nossa definição de política irá em direto **Dev de capacidade de análise do Advisor**. A lista de subscrições serão diferentes.
   - Escreva o código de json com:
      - Os parâmetros de política.
      - A política de regras/condições, neste caso – o tamanho do SKU de VM é igual a série de G
      - A política em vigor, neste caso – **negar**.

   Eis o que o json deve ter o seguinte aspeto

```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
```

<!-- Update the following link to the top level samples page
-->
   Para ver os exemplos de código json, observe este artigo - [descrição geral da política de recurso](../azure-resource-manager/resource-manager-policy.md)

4. Selecione **Guardar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Criar uma definição de política com a REST API

Pode criar uma política com a API REST para definições de política. A API REST permite-lhe criar e eliminar definições de política e obter informações sobre as definições existentes.
Para criar uma definição de política, utilize o seguinte exemplo:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Incluem um corpo do pedido semelhante ao seguinte exemplo:

```
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Criar uma definição de política com o PowerShell

Antes de continuar com o exemplo do PowerShell, certifique-se de que instalou a versão mais recente do Azure PowerShell. Parâmetros de política foram adicionados na versão 3.6.0. Se tiver uma versão anterior, os exemplos de devolverem um erro que indica que o parâmetro não é possível encontrar.

Pode criar uma definição de política utilizando o `New-AzureRmPolicyDefinition` cmdlet.

Para criar uma definição de política de um ficheiro, passe o caminho para o ficheiro. Para um ficheiro externo, utilize o seguinte exemplo:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para utilizar um ficheiro local, utilize o seguinte exemplo:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Para criar uma definição de política com uma regra de inline, utilize o seguinte exemplo:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

O resultado é armazenado num `$definition` objeto, que é utilizado durante a atribuição de política.
O exemplo seguinte cria uma definição de política que inclui os parâmetros:

```
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Definições de política do Vista

Para ver todas as definições de política na sua subscrição, utilize o seguinte comando:

```
Get-AzureRmPolicyDefinition
```

Devolve todas as definições de política disponíveis, incluindo políticas incorporadas. Cada política é devolvida o seguinte formato:

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Criar uma definição de política com a CLI do Azure

Pode criar uma definição de política utilizando a CLI do Azure com o comando de definição de política.
Para criar uma definição de política com uma regra de inline, utilize o seguinte exemplo:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Definições de política do Vista

Para ver todas as definições de política na sua subscrição, utilize o seguinte comando:

```
az policy definition list
```

Devolve todas as definições de política disponíveis, incluindo políticas incorporadas. Cada política é devolvida o seguinte formato:

```
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Criar e atribuir uma definição de iniciativa

Com uma definição de iniciativa, pode agrupar várias definições de política para atingir um objetivo abrangente. Criar uma definição de iniciativa para se certificar de que permanecem em conformidade com as definições de política que compõem a definição da iniciativa recursos no âmbito da definição.  Consulte o [descrição geral da política de Azure](./azure-policy-introduction.md) para obter mais informações sobre definições de iniciativa.

### <a name="create-an-initiative-definition"></a>Criar uma definição de iniciativa

1. Selecione **definições** em **criação** no painel esquerdo.

   ![Selecione as definições](media/create-manage-policy/select-definitions.png)

2. Selecione **iniciativa definição** na parte superior da página, esta seleção leva-o para o **iniciativa definição** formulário.
3. Introduza o nome e descrição da iniciativa.

   Neste exemplo, queremos garantir que os recursos estão em conformidade com as definições de política sobre como obter seguro, o nome da iniciativa seria **obter segura**, e a descrição seria: **este iniciativa foi criado para processar todas as definições de política associadas proteger recursos**.

   ![Definição de iniciativa](media/create-manage-policy/initiative-definition.png)

4. Procure a lista de **definições disponíveis** e selecione o definition(s) de política que pretende adicionar a esse iniciativa. Para o nosso **obter segura** iniciativa, adicione o seguinte incorporado nas definições de política:
   - Necessita do SQL Server versão 12.0
   - Monitorize aplicações web não protegido no Centro de segurança.
   - Monitorizar a rede permissiva no Centro de segurança.
   - Monitorizar a listas brancas no Centro de segurança de aplicação possíveis.
   - Monitorize sem encriptação discos de VM no Centro de segurança.

   ![Definições da iniciativa](media/create-manage-policy/initiative-definition-2.png)

   Depois de selecionar as definições de política na lista irá vê-lo em **políticas e os parâmetros**, conforme mostrado acima.

5. Selecione **Criar**.

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Vá para o **definições** separador em **criação**.
2. Procure o **obter segura** definição iniciativa que criou.
3. Selecione a definição da iniciativa e, em seguida, selecione **atribuir**.

   ![Atribuir uma definição](media/create-manage-policy/assign-definition.png)

4. Preencha o **atribuição** formulário, introduzindo:
   - nome: obter a atribuição de segura
   - Descrição: esta atribuição iniciativa é adaptada para impor este grupo de definições de política no **Azure Advisor capacidade Dev** subscrição
   - escalão de preço: Standard
   - âmbito gostaria esta atribuição aplicada a: **Dev de capacidade do Azure do Advisor**

5. Selecione **atribuir**.

## <a name="resolve-a-non-compliant-or-denied-resource"></a>Resolver um recurso de não conformidade ou negado

Seguindo o exemplo acima, depois de atribuir a definição de política para exigir 12.0 a versão do servidor SQL, seria obter negado um criado com uma versão diferente do SQL server. Nesta secção, iremos estiver a walking através de resolver uma tentativa de negado para criar um SQL server com uma versão diferente.

1. Selecione **atribuições** no painel esquerdo.
2. Percorrer todas as atribuições de política e iniciar o *requerem o SQL Server versão 12.0* atribuição.
3. Pedir uma exclusão para os grupos de recursos no qual está a tentar criar o SQL server. Neste caso, iremos são excluindo Microsoft.Sql/servers/databases: *baconandbeer/Cheetos* e *baconandbeer/Chorizo*.

   ![Exclusão de pedido](media/create-manage-policy/request-exclusion.png)

   Outras formas foi possível resolver a um recurso negado incluem: atingir para o contacto associado à política se tiver uma justificação forte para que necessitam do SQL server criado e editar diretamente a política se tem acesso a.

4. Selecione **Guardar**.

Nesta secção, resolver a negação da tentativa de criar um SQL server com a versão 12.0, ao solicitar uma exclusão para os recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender continuar a trabalhar nos tutoriais subsequentes, não limpeza até os recursos criados neste guia. Se não pretender continuar, utilize os seguintes passos para eliminar qualquer um dos atribuições ou as definições que criou acima:

1. Selecione **definições** (ou **atribuições** se estiver a tentar eliminar uma atribuição) no painel esquerdo.
2. Procure o novo iniciativa ou política definição (ou atribuição) que acabou criado.
3. Selecione as reticências no final da definição ou atribuição e selecione **eliminar definição** (ou **eliminar atribuição**).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, é com êxito feito o seguinte:

> [!div class="checklist"]
> * Atribuído uma política de impor uma condição para recursos que cria no futuro
> * Criar e atribuir uma definição de iniciativa para controlar a conformidade para vários recursos
> * Resolver um recurso de não conformidade ou negado
> * Implementada uma nova política numa organização

Para saber mais sobre as estruturas de definições de política, consulte este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição de política do Azure](policy-definition.md)
