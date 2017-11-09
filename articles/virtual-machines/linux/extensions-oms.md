---
title: "Extensão da máquina virtual do Azure do OMS para Linux | Microsoft Docs"
description: "Implemente o agente do OMS na máquina de virtual com Linux utilizando uma extensão da máquina virtual."
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 6eb07b5ad4b3ebdde75575fc5682ae2153a0c12f
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual OMS para Linux

## <a name="overview"></a>Descrição geral

Operations Management Suite (OMS) fornece capacidades de remediação de monitorização, alertas e alertas na nuvem e no local ativos. A extensão de máquina virtual do agente do OMS de Linux está publicada e suportada pela Microsoft. A extensão instala o agente do OMS em máquinas virtuais do Azure e inscreve máquinas virtuais para uma área de trabalho existente do OMS. Este documento fornece detalhes sobre as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual do OMS Linux.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente do OMS pode ser executada em relação a estas distribuições do Linux.

| Distribuição | Versão |
|---|---|
| CentOS Linux | 5, 6 e 7 |
| Oracle Linux | 5, 6 e 7 |
| Red Hat Enterprise Linux Server | 5, 6 e 7 |
| Debian GNU/Linux | 6, 7 e 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| Servidor Linux Empresarial SUSE | 11 e 12 |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de agente do OMS para Linux requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do OMS. A extensão requer o ID da área de trabalho e a chave de área de trabalho a partir da área de trabalho do OMS de destino; Estes valores podem ser encontrados no portal do OMS. Porque a chave da área de trabalho deve ser tratada como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados da definição de extensão protegido de VM do Azure é encriptados e desencriptados apenas na máquina virtual de destino. Tenha em atenção que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Fabricante | Microsoft.EnterpriseCloud.Monitoring |
| tipo | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId (por exemplo) | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando implementar um ou mais máquinas virtuais que necessitam de configuração de implementação de post, tais como a integração do OMS. Um modelo de Gestor de recursos de exemplo que inclui a extensão de VM de agente do OMS pode ser encontrado no [Galeria de início rápido de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração de JSON para uma extensão da máquina virtual pode ser aninhada o recurso de máquina virtual ou colocada no nível superior de um modelo do Resource Manager JSON de raiz ou. A colocação da configuração JSON afeta o valor do nome do recurso e do tipo. Para obter mais informações, consulte [definir nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-template-child-resource.md). 

O exemplo seguinte assume que a extensão do OMS é aninhada o recurso de máquina virtual. Quando o recurso de extensão de aninhamento, JSON é colocado no `"resources": []` objeto da máquina virtual.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Quando colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual principal e o tipo de reflete a configuração aninhada.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Implementação da CLI do Azure

A CLI do Azure pode ser utilizada para implementar a extensão de VM de agente do OMS para uma máquina virtual existente. Substitua a chave do OMS e o ID do OMS as da sua área de trabalho do OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Resolver problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

É possível obter dados sobre o estado das implementações de extensão do portal do Azure e utilizando a CLI do Azure. Para ver o estado de implementação das extensões para uma determinada VM, execute o seguinte comando utilizando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e os respetivos significados

| Código de erro | Significado | Ação possíveis |
| :---: | --- | --- |
| 10 | VM já está ligada a uma área de trabalho do OMS | Para ligar a VM para a área de trabalho especificada no esquema de extensão, defina stopOnMultipleConnections como false nas definições públicas ou remova esta propriedade. Esta VM obtém cobrada depois de cada área de trabalho está ligado a um. |
| 11 | Configuração inválida fornecida para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implementação. |
| 12 | O Gestor de pacotes dpkg está bloqueado | Certifique-se de que todos os dpkg operações de atualização na máquina tiver concluído e tente novamente. |
| 20 | Ativar chamado prematuramente | [Atualizar o agente Linux do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) para a versão mais recente disponível. |
| 51 | Esta extensão não é suportada no sistema de operação da VM | |
| 55 | Não é possível ligar ao serviço do Microsoft Operations Management Suite | Verifique se o sistema tem acesso à Internet ou que foi fornecido um proxy HTTP válido. Além disso, verifique a correção do ID de área de trabalho. |

Informações adicionais de resolução de problemas podem ser encontradas no [guia de resolução de problemas do OMS agente para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#).

### <a name="support"></a>Suporte

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/en-us/support/faq/).
