---
title: "Criar e publicar uma aplicação gerida do catálogo de serviço do Azure | Microsoft Docs"
description: "Mostra como criar um Azure geridos aplicação destina-se a membros da sua organização."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: fd2c60cbc237f6d302616723c745563a3e1afecb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicar uma aplicação gerida para consumo interno

Pode criar e publicar Azure [geridos aplicações](overview.md) que se destinam a ser membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que garantir a conformidade com as normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, não no Azure marketplace.

Para publicar uma aplicação gerida para o catálogo de serviço, tem de:

* Crie um modelo que define os recursos a implementar a aplicação gerida.
* Defina os elementos de interface de utilizador para o portal quando implementar a aplicação gerida.
* Crie um pacote. zip que contém os ficheiros de modelo necessária.
* Decidir que utilizador, grupo ou aplicação precisa de aceder ao grupo de recursos na subscrição do utilizador.
* Crie a definição de aplicação gerida que aponta para o pacote. zip e pedidos de acesso para a identidade.

Para este artigo, a sua aplicação gerida contém apenas uma conta de armazenamento. Destina-se para ilustrar os passos para publicar uma aplicação gerida. Para obter exemplos completos, consulte [projetos de exemplo para o Azure geridos aplicações](sample-projects.md).

## <a name="create-the-resource-template"></a>Criar o modelo de recursos

Cada definição de aplicações geridas contém um ficheiro denominado **mainTemplate.json**. Aqui, defina os recursos do Azure para aprovisionar. O modelo é não diferente de um modelo do Resource Manager regular.

Crie um ficheiro denominado **mainTemplate.json**. O nome é maiúsculas e minúsculas.

Adicione o seguinte JSON para o ficheiro. Define os parâmetros para criar uma conta de armazenamento e especifica as propriedades da conta do storage.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString('storage'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Guarde o ficheiro mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Criar a definição de interface de utilizador

O portal do Azure utiliza o **createUiDefinition.json** ficheiro para gerar a interface de utilizador para os utilizadores que criar a aplicação gerida. Definir a forma como os utilizadores forneçam entrada para cada parâmetro. Pode utilizar as opções, como uma lista pendente, caixa de texto, a caixa de palavra-passe e outras ferramentas de entrada. Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, consulte [começar com CreateUiDefinition](create-uidefinition-overview.md).

Crie um ficheiro denominado **createUiDefinition.json**. O nome é maiúsculas e minúsculas.

Adicione o seguinte JSON para o ficheiro.

```json
{
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Guarde o ficheiro createUIDefinition.json.

## <a name="package-the-files"></a>Os ficheiros do pacote

Adicione os dois ficheiros para um ficheiro. zip com o nome app.zip. Os dois ficheiros tem de ser ao nível da raiz do ficheiro. zip. Se o put-los numa pasta, receberá um erro ao criar a definição de aplicação gerida que indica que os ficheiros necessários não estão presentes. 

Carregar o pacote para uma localização acessível a partir de onde pode ser utilizada. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Criar a definição de aplicações geridas

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Criar um grupo de utilizadores do Azure Active Directory ou a aplicação

O passo seguinte consiste em selecionar um grupo de utilizadores ou aplicações para gerir os recursos em nome do cliente. Este grupo de utilizadores ou a aplicação tem permissões sobre o grupo de recursos geridos, de acordo com a função que está atribuído. A função pode ser qualquer função de controlo de acesso baseado em funções (RBAC) incorporada, como o proprietário ou contribuinte. Também pode dar uma permissão de utilizador individuais para gerir os recursos, mas normalmente atribuir esta permissão a um grupo de utilizador. Para criar um novo grupo de utilizadores do Active Directory, consulte [criar um grupo e adicionar membros no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

É necessário o ID de objeto do grupo de utilizadores a utilizar para gerir os recursos. 

![Obter ID de grupo](./media/publish-service-catalog-app/get-group-id.png)

### <a name="get-the-role-definition-id"></a>Obter o ID de definição de função

Em seguida, terá do ID de definição de função da função incorporada do RBAC que pretende conceder acesso para a aplicação, utilizador ou grupo de utilizadores. Normalmente, utiliza a função de proprietário ou contribuinte ou leitor. O comando seguinte mostra como obter o ID de definição de função para a função de proprietário:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Criar a definição de aplicações geridas

Se ainda não tiver um grupo de recursos para armazenar a definição da aplicação gerida, crie uma agora:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Agora, crie o recurso de definição de aplicações geridas.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "<group-id>:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application-by-using-the-portal"></a>Criar a aplicação gerida utilizando o portal

Agora, vamos utilizar o portal para implementar a aplicação gerida. Consulte a interface de utilizador que criou no pacote.

1. Aceda ao portal do Azure. Selecione **+ novo** e procure **catálogo de serviço**.

   ![Catálogo de serviço de pesquisa](./media/publish-service-catalog-app/select-new.png)

1. Selecione **aplicações geridas do catálogo de serviço**.

   ![Selecione o catálogo de serviço](./media/publish-service-catalog-app/select-service-catalog.png)

1. Selecione **Criar**.

   ![Selecione criar](./media/publish-service-catalog-app/select-create.png)

1. Localizar a aplicação gerida que pretende criar na lista de soluções disponíveis e selecione-o. Selecione **Criar**.

   ![Localizar a aplicação gerida](./media/publish-service-catalog-app/find-application.png)

1. Fornece informações básicas que é necessárias para a aplicação gerida. Especifique a subscrição e um novo grupo de recursos que contém a aplicação gerida. Selecione **Central EUA oeste** para a localização. Quando terminar, selecione **OK**.

   ![Fornecer parâmetros de aplicações geridas](./media/publish-service-catalog-app/provide-basics.png)

1. Indique os valores que são específicos para os recursos da aplicação gerida. Quando terminar, selecione **OK**.

   ![Fornecer os parâmetros do recurso](./media/publish-service-catalog-app/provide-resource-values.png)

1. O modelo valida os valores que forneceu. Se a validação for bem sucedida, selecione **OK** para iniciar a implementação.

   ![Validar a aplicações geridas](./media/publish-service-catalog-app/validate.png)

Após a conclusão da implementação, a aplicação gerida existe num grupo de recursos denominado applicationGroup. A conta de armazenamento existe num grupo de recursos com o nome applicationGroup plus um valor de cadeia de hash.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral de aplicações gerido](overview.md).
* Por exemplo projetos, consulte [projetos de exemplo para o Azure geridos aplicações](sample-projects.md).
* Para obter informações sobre a publicação de aplicações geridas no Azure Marketplace, consulte [Azure geridos aplicações no mercado](publish-marketplace-app.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, consulte [começar com CreateUiDefinition](create-uidefinition-overview.md).