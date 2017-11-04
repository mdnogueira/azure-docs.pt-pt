---
title: "Extensão de VM de diagnóstico de desempenho do Azure para Windows | Microsoft Docs"
description: "Apresenta a extensão de VM de diagnóstico de desempenho do Azure para Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 85d4764534c77ea0e4d999e249abe456d0234d75
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensão de VM de diagnóstico de desempenho do Azure para Windows

## <a name="summary"></a>Resumo
Extensão de VM de diagnóstico de desempenho do Azure ajuda a recolher dados de diagnóstico de desempenho de VMs do Windows, efetua a análise e fornece um relatório do findings & recomendações para identificar e resolver problemas de desempenho na máquina virtual. Esta extensão instala uma ferramenta de resolução de problemas chamada [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-systems"></a>Sistemas Operativos
Esta extensão pode ser instalada no Windows Server 2008 R2, 2012, 2012 R2, 2016; Sistemas operativos Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Esquema de extensão
O JSON seguinte mostra o esquema para a extensão de diagnóstico de desempenho do Azure. Esta extensão requer o nome e a chave para uma conta de armazenamento para o armazenamento a saída de diagnóstico e o relatório. Estes valores são sensíveis e devem ser armazenados no interior da configuração da definição protegido. Dados da definição de extensão protegido de VM do Azure é encriptados e desencriptados apenas na máquina virtual de destino. Tenha em atenção que storageAccountName e storageAccountKey diferenciam maiúsculas de minúsculas. Outros parâmetros necessários estão listados na secção abaixo.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Valores de propriedade

|   **Nome**   |**Valor / exemplo**|       **Descrição**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versão da API
|Fabricante|Microsoft.Azure.Performance.Diagnostics|Espaço de nomes do publicador para a extensão
|tipo|AzurePerformanceDiagnostics|Tipo de extensão de VM
|typeHandlerVersion|1.0|Versão de processador de extensão
|performanceScenario|Básico|Cenário de desempenho para capturar os dados. Os valores válidos são: **básico**, **vmslow**, **azurefiles**, e **personalizado**.
|traceDurationInSeconds|300|Duração de rastreios se qualquer uma das opções de rastreio estão selecionadas.
|DiagnosticsTrace|D|Opção para ativar o rastreio de diagnóstico. Os valores válidos são **d** ou valor de vazio. Se não pretender capturar este trace, basta deixe o valor como vazio.
|perfCounterTrace|P|Opção para ativar o rastreio de contador de desempenho. Os valores válidos são **p** ou valor de vazio. Se não pretender capturar este trace, basta deixe o valor como vazio.
|networkTrace|n|Opção para ativar o rastreio Netmon. Os valores válidos são  **n**  ou valor de vazio. Se não pretender capturar este trace, basta deixe o valor como vazio.
|xperfTrace|x|Opção para ativar o rastreio XPerf. Os valores válidos são **x** ou valor de vazio. Se não pretender capturar este trace, basta deixe o valor como vazio.
|storPortTrace|s|Opção para ativar o rastreio StorPort. Os valores válidos são s ou o valor vazio. Se não pretender capturar este trace, basta deixe o valor como vazio.
|srNumber|123452016365929|Suporta o número da permissão se disponível. Deixe como vazio se não o tiver.
|requestTimeUtc|9/2/2017 11:06:00 PM|Hora de data atuais em Utc. Não é necessário fornecer este valor se estiver a utilizar o portal para instalar esta extensão.
|storageAccountName|mystorageaccount|Nome da conta do Storage para armazenar os registos de diagnóstico e os resultados.
|storageAccountKey|lDuVvxuZB28NNP … hAiRF3voADxLBTcc = =|Chave da conta de armazenamento.

## <a name="install-the-extension"></a>Instalar a extensão

Siga estes passos para instalar a extensão de VM em máquinas virtuais Windows:

1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Selecione a máquina virtual onde pretende instalar esta extensão.

    ![Selecione a máquina virtual](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecione **extensões** painel e clique em de **adicionar** botão.

    ![Selecionar extensões](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecione a extensão de diagnóstico de desempenho do Azure, reveja os termos e condições e clique em de **criar** botão.

    ![Criar a extensão de diagnóstico de desempenho do Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Forneça os valores de parâmetro para a instalação e clique em **OK** instalar a extensão. Pode encontrar mais informações sobre os cenários de resolução de problemas suportados [aqui](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Instalar a extensão](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Após a instalação com êxito, verá uma mensagem a indicar o aprovisionamento com êxito.

    ![Aprovisionamento com êxito a mensagem](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A execução da extensão será iniciado depois do aprovisionamento é concluída com êxito e irá demorar alguns minutos ou menos para concluir a execução para o cenário básico. Para obter outros cenários, irá percorrer a duração especificada durante a instalação.

## <a name="remove-the-extension"></a>Remova a extensão
Para remover a extensão de uma máquina virtual, siga estes passos:

1. Inicie sessão no [portal do Azure](http://portal.azure.com), selecione a máquina virtual em que pretende remover esta extensão e, em seguida, selecione o painel de extensões. 
2. Clique em de (**...** ) para a entrada de extensão de diagnóstico de desempenho na lista e selecione desinstalar.

    ![Desinstalar a extensão](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Também pode selecionar a entrada de extensão e selecione a opção desinstalar.

## <a name="template-deployment"></a>Implementação do modelo
Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo Azure Resource Manager para executar a extensão de diagnóstico de desempenho do Azure durante uma implementação de modelo Azure Resource Manager. Segue-se um modelo de exemplo que pode ser utilizado com a implementação do modelo:

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Implementação de PowerShell
O `Set-AzureRmVMExtension` comando pode ser utilizado para implementar a extensão de máquina virtual de diagnósticos de desempenho do Azure para uma máquina virtual existente. Antes de executar o comando, as configurações de públicas e privadas tem de ser armazenados numa tabela hash do PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario" = "basic"; "traceDurationInSeconds" = 300; "diagnosticsTrace" = "d"; "perfCounterTrace" = "p"; "networkTrace" = ""; "xperfTrace" = ""; "storPortTrace" = ""; "srNumber" = ""; "requestTimeUtc" = "2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName" = "mystorageaccount" ; "storageAccountKey" = "mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Informações sobre os dados capturados
Ferramenta de PerfInsights recolhe vários registos, configuração, dados de diagnóstico etc., consoante o cenário selecionado. Para obter mais informações sobre os dados recolhidos por cenário volte visite [PerfInsights documentação](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Ver e partilhar os resultados

Resultado da extensão está armazenado para uma pasta denominada log_collection na unidade temporária (normalmente D:\log_collection) por predefinição. Nesta pasta, pode ver o ficheiro zip que contém os registos de diagnóstico e de um relatório com findings e recomendações.

O ficheiro zip criado também é carregado para a conta de armazenamento que indicou durante a instalação e é partilhado durante 30 dias a utilizar [assinaturas de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Um ficheiro de texto com o nome *zipfilename*_saslink.txt também é criado na pasta log_collection. Este ficheiro contém a ligação SAS criada para transferir o ficheiro zip. Qualquer pessoa que tenha esta ligação será capaz de transferir o ficheiro zip.

Microsoft pode utilizar esta ligação SAS para transferir os dados de diagnóstico para uma investigação mais aprofundada, o engenheiro de suporte a trabalhar num pedido de suporte.

Para ver o relatório, apenas extrair o ficheiro zip e abra **PerfInsights Report.html** ficheiro.

Também poderá transferir o ficheiro zip diretamente a partir do portal, selecionando a extensão.

![Ver o estado detalhado](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> A ligação SAS apresentada no portal do poderão não funcionar, por vezes, devido a Url incorreto (provocado por carateres especiais) durante a codificação e descodificação a operação. A solução é para obter a ligação diretamente a partir do ficheiro *_saslink.txt da VM.

## <a name="troubleshoot-and-support"></a>Resolver problemas e suporte
### <a name="troubleshoot"></a>Resolução de problemas

- Estado de implementação da extensão (na área de notificação) pode mostrar "Em curso a implementação", apesar da extensão é aprovisionada com êxito.

    Este problema pode ser ignorado com segurança, desde que o estado da extensão indica que a extensão é aprovisionada com êxito.
- Alguns problemas durante a instalação pode resolver problemas com os registos de extensão. Resultado da execução de extensão é registado para ficheiros encontrados no diretório seguinte:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Suporte

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
