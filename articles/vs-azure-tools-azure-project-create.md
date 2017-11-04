---
title: "Criar um projeto de serviço em nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como criar um projeto de serviço em nuvem do Azure com o Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 1f6ded87b551f660853ea4eb0548f3d942e28fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Criar um projeto de serviço em nuvem do Azure com o Visual Studio
As ferramentas do Azure para Visual Studio fornece um modelo de projeto que lhe permite criar um serviço em nuvem do Azure. Depois do projeto foi criado, o Visual Studio permite-lhe configurar, depurar e implementar o serviço em nuvem no Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Passos para criar um projeto de serviço em nuvem do Azure no Visual Studio
Esta secção explica como criar um projeto de serviço em nuvem do Azure no Visual Studio com um ou mais funções da web.  

1. Inicie o Visual Studio como administrador.

1. No menu principal, selecione **ficheiro** > **novo** > **projeto**.

1. Selecione **nuvem** do Visual c# ou Visual Basic nós de modelo de projeto e selecione **o serviço em nuvem do Azure** da lista de modelos.

    ![Novo serviço em nuvem do Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Especifique qual a versão do .NET Framework que pretende utilizar para desenvolver o seu projeto.

1. Introduza um nome e localização do seu projeto e um nome para a solução. 

1. Selecione **OK**.

1. No **novo serviço de nuvem do Microsoft Azure** caixa de diálogo, selecione as funções que pretende adicionar e selecione o botão de seta para a direita para os adicionar à sua solução.

    ![Selecione as novas funções do serviço em nuvem do Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Para mudar o nome de uma função que adicionou, Paire o rato sobre a função no **novo serviço de nuvem do Microsoft Azure** caixa de diálogo e, no menu de contexto, selecione **mudar o nome**. Pode também mudar o nome de uma função dentro da sua solução (no **Explorador de soluções**) após ter sido adicionado.

    ![Mudar o nome de função do serviço em nuvem do Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

O projeto do Visual Studio do Azure tem associações para os projetos de função na solução. O projeto também inclui o *ficheiro de definição de serviço* e *ficheiro de configuração do serviço*:

- **Ficheiro de definição de serviço** -define as definições de tempo de execução para a sua aplicação, incluindo as funções às quais são necessárias, os pontos finais e tamanho da máquina virtual. 
- **Ficheiro de configuração do serviço** -configura quantas instâncias de uma função são executados e os valores das definições definidas para uma função. 

Para obter mais informações sobre estes ficheiros, consulte [configuram as funções para um serviço em nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Passos seguintes
- [Gerir funções de projetos do serviço em nuvem do Azure com o Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
