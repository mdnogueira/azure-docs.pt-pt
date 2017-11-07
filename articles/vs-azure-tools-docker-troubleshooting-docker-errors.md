---
title: "Resolução de problemas de erros de cliente de Docker no Windows utilizando o Visual Studio | Microsoft Docs"
description: "Resolva problemas que encontrar ao utilizar o Visual Studio para criar e implementar aplicações web para Docker no Windows utilizando o Visual Studio 2017."
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Resolver problemas de desenvolvimento de 2017 do Visual Studio com o Docker

Quando estiver a trabalhar com o Visual Studio Tools para Docker, que pode encontrar problemas ao criar ou depurar a aplicação. Abaixo são alguns comuns de resolução de problemas passos.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Partilha de volume não está ativada. Ativar volumes de partilha nas definições do Docker CE para Windows (apenas para contentores de Linux)

Para resolver este problema:

1. Clique com botão direito **Docker para Windows** na área de notificação e, em seguida, selecione **definições**.
1. Selecione **partilhado unidades** e partilhar a unidade de sistema juntamente com a unidade onde reside o projeto.

> [!NOTE]
> Se os ficheiros aparecerem partilhados, poderá ainda terá clique na ligação "Repor as credenciais de..." na parte inferior da caixa de diálogo para poder para voltar a ativar a partilha de volume.

![unidades partilhadas](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Não é possível iniciar a depuração

Uma razão pode estar relacionado com ter obsoletos componentes de depuração na sua pasta de perfil de utilizador. Execute os seguintes comandos para remover estas pastas para que os componentes de depuração mais recente são transferidos na próxima sessão de depuração.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Erros específicos de rede quando depurar a aplicação

Tente executar o script transferível de [rede do anfitrião de contentor da limpeza](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), que irá atualizar os componentes relacionados com a rede no seu computador anfitrião.


## <a name="microsoftdockertools-github-repo"></a>Repositório do GitHub Microsoft/DockerTools

Para quaisquer outros problemas que encontrar, consulte [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problemas.