<properties
    pageTitle="Exportar um modelo do Azure Resource Manager | Microsoft Azure"
    description="Utilize o Azure Resource Manager para exportar um modelo a partir de um grupo de recursos existente."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="tomfitz"/>

# Exportar um modelo do Azure Resource Manager a partir de recursos existentes

Saber como construir modelos do Azure Resource Manager pode ser uma tarefa intimidante. Felizmente, o Resource Manager pode ajudá-lo, uma vez que lhe permite exportar um modelo a partir de recursos existentes na sua subscrição. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário.

Neste tutorial, irá iniciar sessão no Portal do Azure, criar uma conta do Storage e exportar o modelo para essa conta do Storage. Irá adicionar uma rede virtual para modificar o grupo de recursos. Por fim, irá exportar um novo modelo que representa o estado atual. Embora este artigo se concentre numa infraestrutura simplificada, pode utilizar estes mesmos passos para exportar um modelo para uma solução mais complexa.

## Criar uma conta do Storage

1. No [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Dados + Armazenamento** > **Conta do Storage**.

      ![criar armazenamento](./media/resource-manager-export-template/create-storage.png)

2. Crie uma conta do Storage com o nome **storage**, as suas iniciais e a data. O nome da conta do Storage tem de ser exclusivo em todo o Azure. Se tentar inicialmente um nome que já está a ser utilizado, experimente utilizar uma variação. Para o grupo de recursos, utilize **ExportGroup**. Pode utilizar os valores predefinidos para as outras propriedades. Selecione **Criar**.

      ![fornecer valores para o armazenamento](./media/resource-manager-export-template/provide-storage-values.png)

Após a conclusão da implementação, a sua subscrição contém a conta do Storage.

## Exportar o modelo para implementação

1. Aceda ao painel do grupo de recursos para o novo grupo de recursos. Irá reparar que o resultado da última implementação está listado. Selecione essa ligação.

      ![painel do grupo de recursos](./media/resource-manager-export-template/resource-group-blade.png)

2. Será apresentado um histórico das implementações do grupo. No seu caso, é provável que esteja listada apenas uma implementação. Selecione essa implementação.

     ![última implementação](./media/resource-manager-export-template/last-deployment.png)

3. É apresentado um resumo da implementação. O resumo inclui o estado da implementação e as respetivas operações, bem como os valores que forneceu para os parâmetros. Para ver o modelo que foi utilizado para a implementação, selecione **Ver modelo**.

     ![ver resumo da implementação](./media/resource-manager-export-template/deployment-summary.png)

4. O Resource Manager obtém automaticamente os cinco ficheiros seguintes:

   - O modelo que define a infraestrutura para a sua solução. Quando criou a conta do Storage através do portal, o Resource Manager utilizou um modelo para a implementar e guardou esse modelo para consulta futura.

   - Um ficheiro de parâmetros que pode utilizar para passar valores durante a implementação. Contém os valores que indicou durante a primeira implementação, mas pode alterar qualquer um destes valores quando implementar novamente o modelo.

   - Um ficheiro de script do Azure PowerShell que pode utilizar para implementar o modelo.

   - Um ficheiro de script da interface de linha de comandos do Azure (CLI) que pode utilizar para implementar o modelo.

   - Uma classe .NET que pode utilizar para implementar o modelo.

     Os ficheiros estão disponíveis através de ligações no painel. Por predefinição, está selecionado o modelo.

       ![ver modelo](./media/resource-manager-export-template/view-template.png)

     Vamos observar o modelo com atenção. O modelo deverá ter um aspeto semelhante ao seguinte:

        {      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",      "contentVersion": "1.0.0.0",      "parameters": {        "name": {          "type": "String"        },        "accountType": {          "type": "String"        },        "location": {          "type": "String"        },        "encryptionEnabled": {          "defaultValue": false,          "type": "Bool"        }      },      "resources": [        {          "type": "Microsoft.Storage/storageAccounts",          "sku": {            "name": "[parameters('accountType')]"          },          "kind": "Storage",          "name": "[parameters('name')]",          "apiVersion": "2016-01-01",          "location": "[parameters('location')]",          "properties": {            "encryption": {              "services": {                "blob": {                  "enabled": "[parameters('encryptionEnabled')]"                }              },              "keySource": "Microsoft.Storage"            }          }        }      ]    }

     Repare que o modelo define os parâmetros para o nome da conta do Storage, o tipo e a localização. Um parâmetro também indica se a encriptação está ativada e o valor predefinido é **false**. Na secção **recursos**, é apresentada a definição da conta do Storage a implementar.

Os parênteses retos contêm uma expressão que é avaliada durante a implementação. As expressões entre parênteses no modelo são utilizadas para obter valores de parâmetros durante a implementação. É possível utilizar muitas mais expressões e serão apresentados exemplos de outras expressões neste artigo. Para obter a lista completa, consulte [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).

Para saber mais sobre a estrutura de um modelo, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).

## Adicionar uma rede virtual

O modelo que transferiu na secção anterior representava a infraestrutura da implementação original, mas não terá em conta quaisquer alterações que efetuar após a implementação.
Para ilustrar este problema, vamos modificar o grupo de recursos ao adicionar uma rede virtual através do portal.

1. No painel do grupo de recursos, selecione **Adicionar** e, em seguida, escolha **rede virtual** nos recursos disponíveis.

2. Atribua à rede virtual o nome **VNET** e utilize os valores predefinidos para as outras propriedades. Selecione **Criar**.

      ![alerta de definição](./media/resource-manager-export-template/create-vnet.png)

3. Depois de a rede virtual ser implementada com êxito para o grupo de recursos, observe novamente o histórico de implementações. Agora verá duas implementações. Selecione a implementação mais recente.

      ![histórico de implementações](./media/resource-manager-export-template/deployment-history.png)

4. Observe o modelo dessa implementação. Repare que este define apenas as alterações que efetuou para adicionar a rede virtual.

Normalmente, é melhor trabalhar com um modelo que implementa toda a infraestrutura para a sua solução numa única operação em vez de recordar vários modelos diferentes a implementar.


## Exportar o modelo para o grupo de recursos

Embora cada implementação mostre apenas as alterações que efetuou ao seu grupo de recursos, pode exportar um modelo em qualquer altura para mostrar os atributos de todo o grupo de recursos.  

1. Para ver o modelo para um grupo de recursos, selecione **Exportar modelo**.

      ![exportar grupo de recursos](./media/resource-manager-export-template/export-resource-group.png)

2. Verá novamente os cinco ficheiros que pode utilizar para voltar a implementar a solução, mas, desta vez, o modelo é ligeiramente diferente. Este modelo tem apenas dois parâmetros: um para o nome da conta do Storage e outro para o nome da rede virtual.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     O Resource Manager não obteve os modelos que foram utilizados durante a implementação. Em vez disso, gerou um novo modelo baseado na configuração atual dos recursos. O Resource Manager não sabe quais são os valores que pretende passar como parâmetros, pelo que impõe a maioria dos valores com base nos valores do grupo de recursos. Por exemplo, a localização da conta do Storage e o valor da replicação são definidos para:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Transfira o modelo para que possa trabalhar no mesmo localmente.

      ![transferir modelo](./media/resource-manager-export-template/download-template.png)

4. Localize o ficheiro .zip que transferiu e extraia os conteúdos. Pode utilizar este modelo transferido para voltar a implementar a infraestrutura.

## Passos seguintes

Parabéns! Aprendeu a exportar um modelo a partir dos recursos que criou no portal.

- Na segunda parte deste tutorial, irá personalizar o modelo que acabou de transferir ao adicionar mais parâmetros e voltar a implementá-lo através de um script. Consulte [Personalizar e voltar a implementar um modelo exportado](resource-manager-customize-template.md).
- Para ver como exportar um modelo através do PowerShell, consulte [Utilizar o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo através da CLI do Azure, consulte [Utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).



<!--HONumber=Jun16_HO2-->


