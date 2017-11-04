---
title: "Implementar o conjunto de dimensionamento de Máquina Virtual com o Visual Studio | Microsoft Docs"
description: "Implementar os conjuntos de dimensionamento de Máquina Virtual utilizando o Visual Studio e um modelo do Resource Manager"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78a4b0c8d305f57f495402cecb92d18425ff6bff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um conjunto de dimensionamento de Máquina Virtual com o Visual Studio
Este artigo mostra como implementar uma Azure Virtual Machine conjunto de dimensionamento de uma implementação de grupo de recursos do Studio Visual a utilizar.

[Conjuntos de dimensionamento de Máquina Virtual do Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) é um recurso de computação do Azure para implementar e gerir uma coleção de máquinas virtuais semelhantes com dimensionamento automático e balanceamento de carga. Pode aprovisionar e implementar os conjuntos de dimensionamento de Máquina Virtual utilizando [modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Modelos Azure Resource Manager pode ser implementados utilizando a CLI do Azure, PowerShell, REST e também diretamente a partir do Visual Studio. O Visual Studio fornece um conjunto de modelos de exemplo, o que pode ser implementada como parte de um projeto de implementação de grupo de recursos do Azure.

Implementações do grupo de recursos do Azure são uma forma de grupo e publicar um conjunto de recursos relacionados do Azure numa operação única implementação. Pode saber mais sobre os mesmos aqui: [criar e implementar grupos de recursos do Azure através do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Pré-requisitos
Para começar a implementar os conjuntos de dimensionamento de Máquina Virtual no Visual Studio, precisa do seguinte:

* Visual Studio 2013 ou posterior
* SDK do Azure 2.7, 2.8 ou 2.9

>[!NOTE]
>Estas instruções partem do princípio de que está a utilizar o Visual Studio com [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Criar um projeto
1. Criar um novo projeto no Visual Studio escolhendo **ficheiro | Novo | Projeto**.
   
    ![Ficheiro novo][file_new]

2. Em **Visual c# | Nuvem**, escolha **do Azure Resource Manager** para criar um projeto de implementação de um modelo do Azure Resource Manager.
   
    ![Criar projeto][create_project]

3. Na lista de modelos, selecione o Linux ou escala definir o modelo de Máquina Virtual Windows.
   
   ![Selecione o modelo][select_Template]

4. Depois do projeto ser criado Consulte scripts de implementação do PowerShell, um modelo do Azure Resource Manager e um ficheiro de parâmetros para o conjunto de dimensionamento de Máquina Virtual.
   
    ![Explorador de Soluções][solution_explorer]

## <a name="customize-your-project"></a>Personalizar o projeto
Agora pode editar o modelo de personalizá-lo para as necessidades da sua aplicação, por exemplo, adicionar as propriedades de extensão VM ou editar regras de balanceamento de carga. Por predefinição, os modelos de conjunto de dimensionamento de Máquina Virtual estão configurados para implementar a extensão de AzureDiagnostics, o que torna mais fácil adicionar regras de dimensionamento automático. Também implementa um balanceador de carga com um endereço IP público, configurado com regras NAT de entrada. 

O Balanceador de carga permite-lhe estabelecer ligação com as instâncias de VM com o SSH (Linux) ou o RDP (Windows). Inicia o intervalo de portas front-end em 50000. Para linux Isto significa que, se lhe SSH para a porta 50000, é encaminhados para a porta 22 da primeira VM num conjunto de dimensionamento. Ligar à porta 50001 é encaminhado para a porta 22 da VM segundo e assim sucessivamente.

 É uma boa forma de editar os modelos com o Visual Studio para utilizar o contorno de JSON para organizar os parâmetros, variáveis e recursos. Tendo conhecimento do esquema Visual Studio pode realçar erros no seu modelo, antes de implementá-lo.

![Explorador JSON][json_explorer]

## <a name="deploy-the-project"></a>Implementar o projeto
1. Implemente o modelo de Gestor de recursos do Azure para criar o recurso do conjunto de dimensionamento da Máquina Virtual. Faça duplo clique no nó do projeto e escolha **implementar | Nova implementação**.
   
    ![Implementar Modelo][5deploy_Template]
    
2. Selecione a sua subscrição na caixa de diálogo "Implementar ao grupo de recursos".
   
    ![Implementar Modelo][6deploy_Template]

3. Aqui, pode criar um grupo de recursos do Azure para implementar o modelo para.
   
    ![Novo grupo de recursos][new_resource]

4. Em seguida, clique em **Editar parâmetros** para introduzir parâmetros que são transmitidos ao seu modelo. Forneça o nome de utilizador e palavra-passe para o SO, o que é necessário para criar a implementação. Se não tiver as ferramentas do PowerShell para o Visual Studio instalado, é recomendado para verificar **guardar palavras-passe** para evitar uma linha de linha de comandos do PowerShell oculta ou utilize [keyvault suporte](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Editar parâmetros][edit_parameters]

5. Agora, clique em **implementar**. O **saída** janela mostra o progresso da implementação. Tenha em atenção que a ação está a executar o **implementar AzureResourceGroup.ps1** script.
   
   ![Janela de saída][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Explorar o conjunto de dimensionamento da Máquina Virtual
Uma vez concluída a implementação, pode ver o conjunto de dimensionamento de Máquina Virtual nova no Visual Studio **Cloud Explorer** (atualizar a lista). Cloud Explorer permite-lhe gerir recursos do Azure no Visual Studio ao desenvolver aplicações. Também pode ver o conjunto de dimensionamento de Máquina Virtual no [portal do Azure](https://portal.azure.com) e [Explorador de recursos do Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 O portal fornece a melhor forma de gerir visualmente a sua infraestrutura do Azure com um browser, enquanto o Explorador de recursos do Azure fornece uma forma fácil para explorar e a depuração de recursos do Azure, dar uma janela para a "vista de instância" e também que mostra os comandos do PowerShell para os recursos está a visualizar.

## <a name="next-steps"></a>Passos seguintes
Assim que tiver implementado com êxito os conjuntos de dimensionamento de Máquina Virtual através do Visual Studio, pode personalizar ainda mais o projeto de acordo com os requisitos da aplicação. Por exemplo, configurar o dimensionamento automático, adicionando um **Insights** recurso, infraestrutura a adicionar ao seu modelo (como VMs autónomo) ou a implementação de aplicações através da extensão de script personalizado. Modelos de bom exemplo podem ser encontrados no [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) repositório do GitHub (procure "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
