---
title: Criar um balanceador de carga interno com um modelo no Resource Manager | Microsoft Docs
description: Saiba como criar um balanceador de carga interno com um modelo no Resource Manager
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 64150862-6ced-42de-85dc-89d323257d7c
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 482c9cd46902d9e3f4e1e0f001182fdb43ce9367


---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Criar um balanceador de carga interno com um modelo
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR>
[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[modelo de implementação clássica](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar o modelo com a função clique para implementar
O modelo de exemplo disponível no repositório público utiliza um ficheiro de parâmetros com os valores predefinidos utilizados para gerar o cenário descrito acima. Para implementar este modelo com a função clique para implementar, siga [esta ligação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), clique em **Implementar no Azure**, substitua os valores de parâmetro predefinidos, se necessário, e siga as instruções apresentadas no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo com o PowerShell
Para implementar o modelo que transferiu com o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.
2. Transfira o ficheiro de parâmetros para o disco local.
3. Edite o ficheiro e guarde-o.
4. Execute o cmdlet **New-AzureRmResourceGroupDeployment** para criar um grupo de recursos com o modelo.
   
        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo com a CLI do Azure
Para implementar o modelo com a CLI do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../xplat-cli-install.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo Resource Manager, como mostrado abaixo.
   
        azure config mode arm
   
    O resultado esperado para o comando acima é o seguinte:
   
        info:    New mode is arm
3. Abra o ficheiro de parâmetros, selecione o conteúdo e guarde-o num ficheiro no seu computador. Para este exemplo, guardámos o ficheiro de parâmetros em *parameters.json*.
4. Execute o comando **azure group deployment create** para implementar o novo balanceador de carga interno com o modelo e os ficheiros de parâmetros que transferiu e alterou acima. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Passos seguintes
[Configurar um modo de distribuição de balanceador de carga com a afinidade do IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


