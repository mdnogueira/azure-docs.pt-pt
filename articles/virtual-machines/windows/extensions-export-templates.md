---
title: "Exportar os grupos de recursos do Azure que contêm as extensões de VM | Microsoft Docs"
description: "Exporte os modelos do Resource Manager, que incluem as extensões de máquina virtual."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: danis
ms.openlocfilehash: 739ae7995fca41fe8abb7cd54ccb72ff3bc43854
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportar os grupos de recursos que contêm as extensões de VM

Grupos de recursos do Azure podem ser exportados para um novo modelo do Resource Manager que pode, em seguida, voltar a implementar. O processo de exportação interpreta recursos existentes e cria um modelo do Resource Manager que quando implementado resulta num grupo de recursos semelhantes. Ao utilizar a opção de exportação do grupo de recursos em relação a um grupo de recursos que contém as extensões de Máquina Virtual, vários itens tem de ser considerados como compatibilidade de extensão e protegida de definições.

Suportado de detalhes deste documento como funciona o o processo de exportação do grupo de recursos sobre extensões de máquina virtual, incluindo uma lista de extensões e protegida de detalhes sobre o processamento de dados.

## <a name="supported-virtual-machine-extensions"></a>Extensões de Máquina Virtual suportadas

Estão disponíveis várias extensões de Máquina Virtual. Nem todas as extensões podem ser exportadas para um modelo do Resource Manager utilizando a funcionalidade de "Scripts de automatização". Se uma extensão da máquina virtual não é suportada, tem de ser colocado manualmente no modelo exportado.

As seguintes extensões podem ser exportadas com a funcionalidade de script de automatização.

| Extensão ||||
|---|---|---|---|
| Cópia de segurança Acronis | Agente do Datadog Windows | SO de aplicação de patches para Linux | Linux de instantâneos VM
| Cópia de segurança Acronis Linux | Extensão de docker | Agente de puppet |
| Informações de BG | Extensão DSC | Informações do site 24x7 Apm |
| BMC CTM agente Linux | Dynatrace Linux | Servidor do site 24x7 Linux |
| BMC CTM agente Windows | Dynatrace Windows | Servidor do site 24x7 Windows |
| Cliente chef | HPE segurança aplicação Defender | DSA Micro tendência |
| Script personalizado | Antimalware de IaaS | Tendência Micro DSA Linux |
| Extensão de Script Personalizado | Diagnóstico do IaaS | Acesso VM para Linux |
| Script personalizado para Linux | Cliente de Chef do Linux | Acesso VM para Linux |
| Agente do Linux Datadog | Diagnóstico do Linux | Instantâneo VM |

## <a name="export-the-resource-group"></a>Exportar grupo de recursos

Para exportar um grupo de recursos a um modelo de reutilizável, conclua os seguintes passos:

1. Iniciar sessão no portal do Azure
2. No Hub Menu, clique em grupos de recursos
3. Selecione o grupo de recursos de destino na lista
4. No painel do grupo de recursos, clique em scripts de automatização

![Exportação de modelo](./media/extensions-export-templates/template-export.png)

O script do Azure Resource Manager automatizações produz um modelo do Resource Manager, um ficheiro de parâmetros e vários scripts de implementação de exemplo, tais como o PowerShell e da CLI do Azure. Neste momento, o modelo exportado pode ser transferido com o botão de transferência, adicionado como um novo modelo para a biblioteca de modelos ou implementada novamente com o botão implementar.

## <a name="configure-protected-settings"></a>Configurar as definições de protegido

Várias extensões de máquina virtual do Azure incluem uma configuração de definições protegido, encripta os dados confidenciais, tais como as credenciais e cadeias de configuração. Definições protegidas não são exportadas com o script de automatização. Se precisam de definições necessárias, protegidas possível voltar para o exportado transformada em modelo.

### <a name="step-1---remove-template-parameter"></a>Passo 1 - Remove parâmetro de modelo

Quando o grupo de recursos é exportado, é criado um parâmetro de modelo único para fornecer um valor para as definições de protegido exportadas. Este parâmetro pode ser removido. Remova o parâmetro, examine a lista de parâmetros e eliminar o parâmetro semelhante para este exemplo JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Passo 2 - Get protegidos propriedades das definições

Uma vez cada definição protegidos tem um conjunto de propriedades necessárias, tem de ser reunidas a uma lista destas propriedades. Cada um dos parâmetros da configuração de definições protegidos pode ser encontrado no [esquema de Gestor de recursos do Azure no GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Este esquema inclui apenas os conjuntos de parâmetros para as extensões listadas na secção Descrição geral deste documento. 

De dentro do repositório de esquema, procure a extensão pretendida, para este exemplo `IaaSDiagnostics`. Uma vez as extensões `protectedSettings` objeto foi localizado, tome nota de cada um dos parâmetros. No exemplo do `IaasDiagnostic` extensão, a necessitar de parâmetros são `storageAccountName`, `storageAccountKey`, e `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Passo 3 – voltar a criar a configuração protegida

No modelo exportado, procure `protectedSettings` e substitua o objeto de definição protegido exportado com uma nova, que inclui os parâmetros de extensão necessária e um valor para cada um deles.

No exemplo do `IaasDiagnostic` extensão, a nova configuração de definição protegido deverá ter um aspeto semelhante ao seguinte exemplo:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

O recurso de extensão final semelhante ao seguinte exemplo de JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Se utilizar os parâmetros do modelo para fornecer valores de propriedade, estes têm de ser criado. Quando criar os parâmetros do modelo para definir valores de protegidas, certifique-se utilizar o `SecureString` parâmetro de tipo para que os valores confidenciais são protegidos. Para obter mais informações sobre como utilizar parâmetros, consulte [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

No exemplo do `IaasDiagnostic` extensão, seriam possível criar os parâmetros seguintes na secção de parâmetros do modelo do Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Neste momento, o modelo pode ser implementado com qualquer método de implementação do modelo.
