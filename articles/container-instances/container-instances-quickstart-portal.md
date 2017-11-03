---
title: "Guia de introdução - criar o primeiro contentor de instâncias de contentor do Azure com o portal do Azure"
description: "Implementar e começar a utilizar o Azure Container Instances"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0179107ece1e150246ab40836783d810425be3ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o seu primeiro contentor no Azure Container Instances

O Azure Container Instances permite criar e gerir facilmente contentores no Azure. Este guia de introdução, criar um contentor no Azure e expô-la à internet com um endereço IP público. Esta operação foi concluída através do portal do Azure. Com apenas alguns cliques, verá isto no seu browser:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-container-instance"></a>Criar uma instância de contentor

Selecione o **novo** > **contentores** > **instâncias de contentor do Azure (pré-visualização)**.

![Começar a criar uma nova instância do contentor no portal do Azure][aci-portal-01]

Introduza os seguintes valores no **nome do contentor**, **imagem contentor**, e **grupo de recursos** caixas de texto. Deixe os outros valores as predefinições, em seguida, clique em **OK**.

* Nome do contentor:`mycontainer`
* Imagem do contentor:`microsoft/aci-helloworld`
* Grupo de recursos:`myResourceGroup`

![Configurar definições básicas para uma nova instância do contentor no portal do Azure][aci-portal-03]

Pode criar contentores de Windows e Linux em instâncias de contentor do Azure. Neste guia de introdução, iremos irá deixe a predefinição de **Linux** , uma vez que especificamos um contentor baseado em Linux (`microsoft/aci-helloworld`) no passo anterior.

Deixe as outras definições no **configuração** nas predefinições, em seguida, clique em **OK** para validar a configuração.

![Configurar uma nova instância do contentor no portal do Azure][aci-portal-04]

Quando a validação é concluída, são apresentadas um resumo das definições de contentor. Selecione **OK** para submeter o pedido de implementação do contentor.

![Resumo das definições para uma nova instância de contentor no portal do Azure][aci-portal-05]

Quando inicia a implementação, é colocado um mosaico no dashboard do portal do que indica o progresso da implementação. Uma vez concluída a implementação, o mosaico é atualizado para apresentar o novo **mycontainer myc1** grupo contentor.

![Progresso da criação de uma nova instância de contentor no portal do Azure][aci-portal-08]

Selecione o **mycontainer myc1** grupo contentor para apresentar as propriedades de grupo do contentor. Tome nota do **endereço Ip** do grupo contentor, bem como o **estado** do seu contentor.

![Descrição geral de grupo do contentor no portal do Azure][aci-portal-06]

Depois do contentor é movido para o **executar** de estado, navegue para o endereço IP que anotou no passo anterior para apresentar a aplicação alojada no contentor de novo.

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, criou uma instância de contentor do Azure a partir de uma imagem num repositório Docker Hub público. Se gostaria de tentar criar um contentor de si e implementá-la para instâncias de contentor do Azure com o registo de contentor do Azure, continue para o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorials](./container-instances-tutorial-prepare-app.md) (Tutoriais do Azure Container Instances)