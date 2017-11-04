---
title: "Vistas em soluções de gestão do Operations Management Suite (OMS) | Microsoft Docs"
description: "Soluções de gestão no Operations Management Suite (OMS), normalmente, irão incluir um ou mais vistas para visualizar dados.  Este artigo descreve como exportar uma vista criada pelo estruturador de vistas e incluí-la numa solução de gestão. "
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 533b5564a805e0b41f2b1a4ad92e12b133220952
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Vistas em soluções de gestão do Operations Management Suite (OMS) (pré-visualização)
> [!NOTE]
> Esta documentação é de preliminar para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Nenhum esquema descrita abaixo está sujeita a alterações.    
>
>

[Soluções de gestão no Operations Management Suite (OMS)](operations-management-suite-solutions.md) normalmente incluirá a uma ou mais vistas para visualizar dados.  Este artigo descreve como exportar uma vista criada pelo [estruturador de vistas](../log-analytics/log-analytics-view-designer.md) e incluí-la numa solução de gestão.  

> [!NOTE]
> Os exemplos neste artigo utilizam parâmetros e variáveis que são necessários ou comuns para soluções de gestão e descrito em [criar soluções de gestão no Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)
>
>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já está familiarizado com a [criar uma solução de gestão](operations-management-suite-solutions-creating.md) e a estrutura de um ficheiro de solução.

## <a name="overview"></a>Descrição geral
Para incluir uma vista numa solução de gestão, crie um **recursos** -lo no [ficheiro de solução](operations-management-suite-solutions-creating.md).  O JSON que descreve a configuração detalhada a vista é normalmente complexo apesar e não algo que seria capaz de criar manualmente um autor de solução típica.  O método mais comum é criar a vista utilizando a [estruturador de vistas](../log-analytics/log-analytics-view-designer.md), exportá-lo e, em seguida, adicione a respetiva configuração detalhada à solução.

Os passos básicos para adicionar uma vista para uma solução são os seguintes.  Cada passo é descrito mais detalhadamente nas secções abaixo.

1. Exporte a vista para um ficheiro.
2. Crie o recurso de vista na solução.
3. Adicione os detalhes da vista.

## <a name="export-the-view-to-a-file"></a>A vista para um ficheiro de exportação
Siga as instruções apresentadas em [estruturador de vistas de análise do registo](../log-analytics/log-analytics-view-designer.md) para exportar uma vista para um ficheiro.  O ficheiro exportado vai estar no formato JSON com o mesmo [elementos que o ficheiro de solução](operations-management-suite-solutions-solution-file.md).  

O **recursos** tem um recurso com um tipo de elemento do ficheiro de vista **Microsoft.OperationalInsights/workspaces** que representa a área de trabalho do OMS.  Este elemento terá um subelemento com um tipo de **vistas** que representa a vista e contém a configuração detalhada.  Irá copiar os detalhes deste elemento e, em seguida, copie-o para a sua solução.

## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de vista na solução
Adicione o seguinte recurso de vista para o **recursos** elemento do seu ficheiro de solução.  Esta opção utiliza as variáveis que são descritas abaixo que tem de adicionar também.  Tenha em atenção que o **Dashboard** e **OverviewTile** propriedades são marcadores de posição que irá substituir com as propriedades correspondentes do ficheiro exportado vista.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Adicione as seguintes variáveis para o elemento de variáveis do ficheiro de solução e substitua os valores para as para a sua solução.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Tenha em atenção que pode copiar o recurso de vista completa do ficheiro exportado vista, mas terá de efetuar as seguintes alterações para que esta funcione na sua solução.  

* O **tipo** para a vista de recursos tem de ser alterado de **vistas** para **Microsoft.OperationalInsights/workspaces**.
* O **nome** propriedade para o recurso de vista tem de ser alterada para incluir o nome da área de trabalho.
* A dependência de área de trabalho tem de ser removido, uma vez que o recurso de área de trabalho não está definido na solução.
* **DisplayName** propriedade tem de ser adicionado à vista.  O **Id**, **nome**, e **DisplayName** todos devem corresponder.
* Os nomes de parâmetros tem de ser alterados para corresponder ao conjunto de parâmetros necessário.
* Variáveis devem ser definidas na solução e utilizadas nas propriedades adequadas.

## <a name="add-the-view-details"></a>Adicione os detalhes de vista
O recurso de vista no ficheiro de vista exportado irá conter dois elementos no **propriedades** elemento com o nome **Dashboard** e **OverviewTile** que contenha a secção detalhada configuração da vista.  Copiar estes dois elementos e os respetivos conteúdos para o **propriedades** elemento do recurso ver no seu ficheiro de solução.

## <a name="example"></a>Exemplo
Por exemplo, o exemplo seguinte mostra um ficheiro de solução simples com uma vista.  Nas reticências (…) são apresentados para o **Dashboard** e **OverviewTile** conteúdo por razões de espaço.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Passos seguintes
* Saiba os detalhes completos da criação de [soluções de gestão](operations-management-suite-solutions-creating.md).
* Incluir [runbooks de automatização na sua solução de gestão](operations-management-suite-solutions-resources-automation.md).
