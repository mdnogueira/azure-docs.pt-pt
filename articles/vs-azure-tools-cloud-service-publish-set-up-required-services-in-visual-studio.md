---
title: "Preparar para publicar ou implementar um serviço em nuvem do Visual Studio | Microsoft Docs"
description: "Saiba os procedimentos para configurar os serviços de conta de nuvem e de armazenamento e configurar a sua aplicação do Azure."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: kraigb
ms.openlocfilehash: 5b2043ada193e5a08957656e153b58a61e7eba9e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Preparar para publicar ou implementar um serviço em nuvem do Visual Studio

Para publicar um projeto de serviço em nuvem, tem de configurar os seguintes serviços, tal como descrito neste artigo:

* A **serviço em nuvem** para executar as funções no ambiente do Azure, e 
* A **conta de armazenamento** que fornece acesso aos serviços tabela, fila e Blob.

## <a name="create-a-cloud-service"></a>Criar um serviço cloud

Um serviço em nuvem executa as funções no ambiente do Azure. Pode criar um serviço em nuvem no Visual Studio ou através de [portal do Azure](https://portal.azure.com/) conforme descrito nas secções que se seguem.

### <a name="create-a-cloud-service-from-visual-studio"></a>Criar um serviço em nuvem a partir do Visual Studio

1. Com um projeto de serviço em nuvem criado anteriormente, clique com botão direito a projeto, selecione **publicar**.
1. Se necessário, inicie sessão com a Microsoft ou uma conta organizacional associada à sua subscrição do Azure, em seguida, selecione **seguinte** para avançar para o **definições** página.
1. A **criar serviço de nuvem e de conta de armazenamento** é apresentada a caixa de diálogo (se não, selecione **criar novo** do **serviço em nuvem** lista).
1. Introduza um nome sensível do serviço em nuvem, o que faz parte do seu URL e tem de ser exclusivo. Também escolher uma região ou grupo de afinidade e selecione uma opção de replicação.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Criar um serviço em nuvem através do portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione **serviços em nuvem (clássica)** no lado esquerdo da página.
1. Selecione **+ adicionar**, em seguida, forneça as informações necessárias (DNS nome, subscrição, grupo de recursos e localização). Não é necessário carregar um pacote neste momento, porque, fazê-lo mais tarde no Visual Studio.
1. Selecione **criar** para concluir o processo.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta do storage fornece acesso aos serviços tabela, fila e Blob. Pode criar uma conta do storage através do Visual Studio ou o [portal do Azure](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Criar uma conta do storage a partir do Visual Studio

1. No **Explorador de soluções** com um projeto de serviço em nuvem criado anteriormente, localize o **serviços ligados** nós dentro de um projeto de função, rato e selecione **Adicionar serviço ligado**. (No Visual Studio 2015, clique com botão direito do **armazenamento** nó e selecione **criar conta de armazenamento**.)
1. No **serviços ligados** lista que é apresentada, selecione **armazenamento na nuvem com o Storage do Azure**.
1. Na caixa de diálogo Storage do Azure que aparece, selecione **+ criar nova conta do Storage**, uma caixa de diálogo que pode especificar a sua subscrição, um nome que aparece fo a conta, um preço camada, grupo de recursos e localização.
1. Selecione **criar** quando tiver terminado. A nova conta de armazenamento é apresentado na lista de contas de armazenamento disponível na sua subscrição.
1. Selecione de que a conta e selecione **adicionar**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Criar uma conta de armazenamento através do portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione **+ novo** na parte superior esquerda.
1. Selecione **armazenamento** em "Azure Marketplace,", em seguida, **conta de armazenamento - BLOBs, ficheiro, tabela, fila** do lado direito.
1. Forneça as informações necessárias (nome, modelo de implementação e assim sucessivamente.
1. Selecione **criar** para concluir o processo.

## <a name="configure-your-app-to-use-the-storage-account"></a>Configure a sua aplicação utilizar a conta de armazenamento

Depois de criar uma conta de armazenamento, ligar a partir do Visual Studio automaticamente atualiza as configurações de serviço para o projeto, incluindo URLs e as chaves de acesso.

Se criou um serviço em nuvem do Visual Studio com o **adicionar o serviço ligado**, pode verificar as ligações abrindo `ServiceConfiguration.Cloud.cscfg` e `ServiceConfiguration.Local.cscfg`.

Se tiver criado um serviço em nuvem através do portal do Azure, siga os mesmos passos no [criar uma conta do storage a partir do Visual Studio](#create-a-storage-account-from-visual-studio) mas selecione a conta existente em vez de criar um novo. Visual Studio, em seguida, atualiza a configuração por si.

Para configurar as definições de utilizam manualmente, as páginas de propriedades no Visual Studio para a função aplicável no seu projeto de serviço em nuvem (a função com o botão direito e selecione **propriedades**). Para obter mais informações, consulte [configurar uma cadeia de ligação para uma conta de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Sobre chaves de acesso

O portal do Azure mostra os URLs que pode utilizar para aceder a recursos em cada um dos serviços de armazenamento do Azure bem como as chaves de acesso primária e secundária para a sua conta. Utilize estas chaves para autenticar pedidos efetuados contra os serviços de armazenamento.

A chave de acesso secundária fornece o mesmo acesso à sua conta de armazenamento como a chave de acesso primária e é gerada uma cópia de segurança a chave de acesso primária comprometida. Além disso, é recomendado que voltar a gerar as chaves de acesso regularmente. Pode modificar uma definição de cadeia de ligação para utilizar a chave secundária ao regenerar a chave primária, em seguida, pode modificar-a para utilizar a chave primária regenerada ao regenerar a chave secundária.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a publicação de aplicações para o Azure a partir do Visual Studio, consulte [publicação de um serviço em nuvem com as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).
