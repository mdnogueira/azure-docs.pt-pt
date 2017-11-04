---
title: "Criar soluções de gestão no Operations Management Suite (OMS) | Microsoft Docs"
description: "Soluções de gestão de expandam a funcionalidade do Operations Management Suite (OMS), fornecendo os cenários de gestão em pacote que os clientes podem adicionar a sua área de trabalho do OMS.  Este artigo fornece detalhes sobre como pode criar soluções de gestão a ser utilizado no seu próprio ambiente ou disponibilizada aos seus clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee3462c13101d18921dc488b08c79e1e4e02ff3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-a-management-solution-file-in-operations-management-suite-oms-preview"></a>Criar um ficheiro de solução de gestão no Operations Management Suite (OMS) (pré-visualização)
> [!NOTE]
> Esta documentação é de preliminar para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Nenhum esquema descrita abaixo está sujeita a alterações.  

Soluções de gestão no Operations Management Suite (OMS) são implementadas como [modelos do Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).  A tarefa principal em aprender a criar soluções de gestão é learning como [criar um modelo](../azure-resource-manager/resource-group-authoring-templates.md).  Este artigo fornece detalhes exclusivos de modelos utilizados para soluções e como configurar recursos de solução típica.


## <a name="tools"></a>Ferramentas

Pode utilizar qualquer editor de texto para trabalhar com ficheiros de solução, mas recomendamos tirar partido das funcionalidades fornecidas no Visual Studio ou Visual Studio Code, conforme descrito nos seguintes artigos.

- [Criar e implementar grupos de recursos do Azure através do Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Trabalhar com modelos Azure Resource Manager no Visual Studio Code](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>estrutura
A estrutura básica de um ficheiro de solução de gestão é o mesmo que um [modelo do Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format) que é o seguinte.  Cada uma das secções abaixo descreve os elementos de nível superior e e os respetivos conteúdos numa solução.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros
[Os parâmetros](../azure-resource-manager/resource-group-authoring-templates.md#parameters) são valores que precisa do utilizador quando instalam a solução de gestão.  Existem parâmetros padrão com todas as soluções e pode adicionar parâmetros adicionais conforme necessário para a sua solução específica.  Como os utilizadores irão fornecer valores de parâmetros quando instalam a sua solução irá depender o parâmetro específico e a forma como a solução está a ser instalada.

Quando um utilizador instala a solução de gestão através de [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) ou [modelos de início rápido do Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions) são-lhe pedidos para selecionar um [área de trabalho OMS e automatizaçãodecontas](operations-management-suite-solutions.md#oms-workspace-and-automation-account).  Estes são utilizados para preencher os valores de cada um dos parâmetros padrão.  Não é pedido ao utilizador diretamente fornecer valores para os parâmetros padrão, mas são lhe for pedidos para fornecer valores para os parâmetros adicionais.

Quando o utilizador instala a sua solução [outro método](operations-management-suite-solutions.md#finding-and-installing-management-solutions), têm de fornecer um valor para todos os parâmetros padrão e todos os parâmetros adicionais.

Um parâmetro de exemplo é mostrado abaixo.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela seguinte descreve os atributos de um parâmetro.

| Atributo | Descrição |
|:--- |:--- |
| tipo |Tipo de dados para o parâmetro. O controlo de entrada apresentado para o utilizador depende do tipo de dados.<br><br>bool - na caixa pendente<br>cadeia, a caixa de texto<br>Int - caixa de texto<br>SecureString '-' o campo de palavra-passe<br> |
| categoria |Categoria opcional para o parâmetro.  Os parâmetros da mesma categoria são agrupados em conjunto. |
| Controlo |Funcionalidades adicionais para os parâmetros de cadeia.<br><br>é apresentada a DateTime - controlo de Datetime.<br>GUID - o valor Guid é gerado automaticamente e o parâmetro não for apresentado. |
| descrição |Descrição opcional para o parâmetro.  Apresentado num balão de informações junto do parâmetro. |

### <a name="standard-parameters"></a>Parâmetros padrão
A tabela seguinte lista os parâmetros padrão para todas as soluções de gestão.  Estes valores são preenchidos para o utilizador em vez de pedir para os mesmos, quando é instalada a solução do Azure Marketplace ou modelos de início rápido.  O utilizador tem de fornecer valores para os mesmos se a solução é instalada com outro método.

> [!NOTE]
> A interface de utilizador no Azure Marketplace e modelos de início rápido está à espera os nomes de parâmetro na tabela.  Se utilizar diferentes nomes de parâmetros, em seguida, será solicitado ao utilizador para os mesmos e não automaticamente preenchidas.
>
>

| Parâmetro | Tipo | Descrição |
|:--- |:--- |:--- |
| accountName |Cadeia |Nome da conta de automatização do Azure. |
| pricingTier |Cadeia |Escalão de preço da área de trabalho de análise de registos e da conta de automatização do Azure. |
| regionId |Cadeia |Região da conta de automatização do Azure. |
| SolutionName |Cadeia |Nome da solução.  Se estiver a implementar a solução através de modelos de início rápido, em seguida, deve definir solutionName como um parâmetro para que pode definir uma cadeia em vez disso, exigindo que o utilizador especifique um. |
| workspaceName |Cadeia |Nome de área de trabalho de análise do registo. |
| workspaceRegionId |Cadeia |Região da área de trabalho de análise de registos. |


Segue-se a estrutura dos parâmetros padrão que pode copiar e colar no seu ficheiro de solução.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Consulte a valores de parâmetros em outros elementos da solução com a sintaxe **parâmetros ('nome de parâmetro')**.  Por exemplo, para aceder ao nome de área de trabalho, utilizaria **parameters('workspaceName')**

## <a name="variables"></a>Variáveis
[Variáveis](../azure-resource-manager/resource-group-authoring-templates.md#variables) são valores que irá utilizar o resto da solução de gestão.  Estes valores não são expostos ao utilizador instalar a solução.  Estes foram concebidas para fornecer o autor com uma única localização onde podem gerir os valores que podem ser utilizados várias vezes em toda a solução. Deve colocar quaisquer valores específicos à sua solução as variáveis por oposição a codificação hard-las no **recursos** elemento.  Isto torna o código mais legível e permite-lhe alterar facilmente estes valores em versões posteriores.

Segue-se um exemplo de um **variáveis** elemento com os parâmetros comuns utilizados em soluções.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Consulte para os valores das variáveis através da solução com a sintaxe **variáveis ('nome da variável')**.  Por exemplo, para aceder à variável SolutionName, teria de utilizar **variables('SolutionName')**.

Também pode definir variáveis de complexas define que múltiplo de valores.  Estes são particularmente úteis em soluções de gestão onde está a definir várias propriedades para diferentes tipos de recursos.  Por exemplo, foi reestruturar as variáveis de solução mostradas acima para o seguinte.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Neste caso, consulte os valores das variáveis através da solução com a sintaxe **variables('variable name').property**.  Por exemplo, para aceder à variável nome de solução, teria de utilizar **variables('Solution'). Nome**.

## <a name="resources"></a>Recursos
[Recursos](../azure-resource-manager/resource-group-authoring-templates.md#resources) definem os recursos diferentes que a sua solução de gestão irão instalar e configurar.  Esta será a parte maior e mais complexa do modelo.  Pode obter a estrutura e a descrição completa dos elementos de recurso no [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Recursos diferentes que, normalmente, irá definir estão descritos nos outros artigos nesta documentação. 


### <a name="dependencies"></a>Dependências
O **dependsOn** elementos Especifica um [dependência](../azure-resource-manager/resource-group-define-dependencies.md) no outro recurso.  Quando a solução é instalada, não foi criado um recurso até que todas as dependências dele foram criadas.  Por exemplo, poderá a sua solução [iniciar um runbook](operations-management-suite-solutions-resources-automation.md#runbooks) quando é instalado utilizando uma [recursos da tarefa](operations-management-suite-solutions-resources-automation.md#automation-jobs).  O recurso de tarefa seria dependente do recurso de runbook para se certificar de que o runbook for criado antes da tarefa é criada.

### <a name="oms-workspace-and-automation-account"></a>Área de trabalho OMS e conta de automatização
Soluções de gestão requerem um [área de trabalho OMS](../log-analytics/log-analytics-manage-access.md) para conter vistas e um [conta de automatização](../automation/automation-security-overview.md#automation-account-overview) para conter os runbooks e recursos relacionados.  Estes devem estar disponíveis antes dos recursos na solução são criados e não devem ser definidos na solução de si próprio.  O utilizador será [especificar uma conta e a área de trabalho](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando implementar a sua solução, mas como o autor deve considerar os seguintes pontos.

## <a name="solution-resource"></a>Recurso de solução
Cada solução requer uma entrada de recurso no **recursos** elemento que define a solução de si próprio.  Isto irá ter um tipo de **Microsoft.OperationsManagement/solutions** e ter a seguinte estrutura. Isto inclui [parâmetros padrão](#parameters) e [variáveis](#variables) que normalmente são utilizados para definir propriedades da solução.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspacename'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Dependências
O recurso de solução tem de ter um [dependência](../azure-resource-manager/resource-group-define-dependencies.md) em todos os outros recursos na solução, uma vez que têm de existir antes de pode criar a solução.  Fazê-lo ao adicionar uma entrada para cada recurso de **dependsOn** elemento.

### <a name="properties"></a>Propriedades
O recurso de solução tem as propriedades na tabela seguinte.  Isto inclui recursos referenciados e contidas pela solução que define a forma como o recurso é gerido depois da solução está instalada.  Cada recurso na solução deverá estar listado no **referencedResources** ou **containedResources** propriedade.

| Propriedade | Descrição |
|:--- |:--- |
| workspaceResourceId |ID da área de trabalho de análise de registos no formato  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nome da área de trabalho\>*. |
| referencedResources |Lista de recursos na solução que não devem ser removidos quando a solução é removida. |
| containedResources |Lista de recursos na solução que devem ser removidos quando a solução é removida. |

O exemplo acima é uma solução com um runbook, uma agenda e vista.  A agenda e um runbook são *referenciado* no **propriedades** elemento para que não são removidas quando a solução é removida.  A vista é *contidos* por isso, é removida quando a solução é removida.

### <a name="plan"></a>Planear
O **plano** entidade do recurso solução tem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| nome |Nome da solução. |
| Versão |Versão da solução, conforme determinado pelo autor. |
| Produto |Cadeia exclusiva para identificar a solução. |
| Fabricante |Publicador da solução. |



## <a name="sample"></a>Exemplo
Pode ver exemplos de ficheiros de solução com um recurso de solução nas seguintes localizações.

- [Recursos de automatização](operations-management-suite-solutions-resources-automation.md#sample)
- [Recursos de pesquisa e o alerta](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Passos seguintes
* [Adicionar alertas e pesquisas guardadas](operations-management-suite-solutions-resources-searches-alerts.md) à sua solução de gestão.
* [Adicionar vistas](operations-management-suite-solutions-resources-views.md) à sua solução de gestão.
* [Adicionar runbooks e outros recursos de automatização](operations-management-suite-solutions-resources-automation.md) à sua solução de gestão.
* Saiba os detalhes de [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquisa [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) exemplos de modelos de Gestor de recursos diferentes.
