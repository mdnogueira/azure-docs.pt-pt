---
title: "Criar um balanceador de carga com acesso à Internet – Modelo do Azure | Microsoft Docs"
description: "Saiba como criar um balanceador de carga com acesso à Internet no Resource Manager com um modelo"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: f30a450c7d3aa73bb2fb196ca7de94436916bed0
ms.lasthandoff: 04/27/2017

---

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Criar um balanceador de carga com acesso à Internet com um modelo

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Também pode [saber como criar um balanceador de carga com acesso à Internet com um modelo de implementação clássica](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar o modelo com a função clique para implementar

O modelo de exemplo disponível no repositório público utiliza um ficheiro de parâmetros com os valores predefinidos utilizados para gerar o cenário descrito acima. Para implementar este modelo com a função clique para implementar, siga [esta ligação](http://go.microsoft.com/fwlink/?LinkId=544801), clique em **Implementar no Azure**, substitua os valores de parâmetro predefinidos, se necessário, e siga as instruções apresentadas no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo com o PowerShell

Para implementar o modelo que transferiu com o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.
2. Execute o cmdlet **New-AzureRmResourceGroupDeployment** para criar um grupo de recursos com o modelo.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo com a CLI do Azure

Para implementar o modelo com a CLI do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo Resource Manager, como mostrado abaixo.

    ```azurecli
    azure config mode arm
    ```

    O resultado esperado para o comando acima é o seguinte:

        info:    New mode is arm

3. A partir do browser, navegue para o [Modelo de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copie o conteúdo do ficheiro json e cole num novo ficheiro no seu computador. Para este cenário, teria de copiar os valores abaixo para um ficheiro denominado **c:\lb\azuredeploy.parameters.json**.
4. Execute o cmdlet **azure group deployment create** para implementar o novo balanceador de carga com o modelo e os ficheiros de parâmetros que transferiu e alterou acima. A lista apresentada depois do resultado explica os parâmetros utilizados.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Passos seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

