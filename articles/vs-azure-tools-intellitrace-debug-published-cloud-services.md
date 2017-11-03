---
title: "Depurar uma publicada um Azure serviço em nuvem com o Visual Studio e o IntelliTrace | Microsoft Docs"
description: "Saiba como depurar um serviço em nuvem com o Visual Studio e de IntelliTrace"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: d33a8338b37f7479196449238388c7dbf391bbb6
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>A depuração de um serviço em nuvem do Azure publicadas com o Visual Studio e o IntelliTrace
Com IntelliTrace, que pode iniciar sessão extensas informações de depuração para uma instância de função quando for executada no Azure. Se precisar de descobrir a causa de um problema, pode utilizar os registos do IntelliTrace para seguir o código do Visual Studio como se estivesse em execução no Azure. Em vigor, registos do IntelliTrace de chave de execução de código e os dados de ambiente quando a aplicação do Azure está em execução como um serviço em nuvem no Azure e permite-lhe os dados do Visual Studio registados de reprodução. 

Pode utilizar o IntelliTrace, se tiver o Visual Studio Enterprise instalado e os .NET Framework 4 de destinos de aplicação do Azure ou uma versão posterior. IntelliTrace recolhe informações para as funções do Azure. Máquinas virtuais para estas funções sempre executar sistemas operativos de 64 bits.

Como alternativa, pode utilizar [depuração remota](http://go.microsoft.com/fwlink/p/?LinkId=623041) ligar diretamente a um serviço em nuvem que está em execução no Azure.

> [!IMPORTANT]
> IntelliTrace destina-se apenas a cenários de depuração e não deve ser utilizado para uma implementação de produção.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Configurar uma aplicação do Azure para o IntelliTrace
Para ativar o IntelliTrace para uma aplicação do Azure, tem de criar e publicar a aplicação de um projeto do Visual Studio do Azure. Tem de configurar o IntelliTrace para a sua aplicação do Azure antes de o publicar no Azure. Se publicar a aplicação sem configurar o IntelliTrace, terá de voltar a publicar o projeto. Para obter mais informações, consulte [uma nuvem do Azure a publicação de serviços de projetos com o Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando estiver pronto para implementar a aplicação do Azure, certifique-se de que os destinos de compilação do projeto estão definidos para **depurar**.

1. No **Explorador de soluções**, clique no projeto e, no menu de contexto, selecione **publicar**.
   
1. No **publicar aplicação Azure** caixa de diálogo, selecione a subscrição do Azure e selecione **seguinte**.

1. No **definições** página, selecione o **definições avançadas** separador.

1. Ative o **ativar IntelliTrace** opção para recolher registos do IntelliTrace para a sua aplicação quando é publicado na nuvem.
   
1. Para personalizar a configuração básica do IntelliTrace, selecione **definições** junto a **ativar IntelliTrace**.

    ![Ligação de definições de IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. No **IntelliTrace definições** caixa de diálogo, pode especificar os eventos de registo, se pretende recolher informações sobre a chamada, os módulos e processos para recolher os registos para e quanto espaço a atribuir à gravação. Para obter mais informações sobre IntelliTrace, consulte [depuração com IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Definições de IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

O registo do IntelliTrace é um ficheiro de registo circular o tamanho máximo especificado nas definições do IntelliTrace (o tamanho predefinido é 250 MB). Os registos do IntelliTrace são recolhidos para um ficheiro no sistema de ficheiros da máquina virtual. Quando pedir os registos, um instantâneo é tirado neste ponto no tempo e transferido para o seu computador local.

Depois do serviço em nuvem do Azure foi publicado para o Azure, pode determinar se IntelliTrace tenha sido ativado a partir do nó do Azure no **Explorador de servidores**, conforme mostrado na imagem seguinte:

![Explorador de servidores - IntelliTrace ativada](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Transferir os registos do IntelliTrace para uma instância de função
Com o Visual Studio, pode transferir os registos do IntelliTrace para uma instância de função, seguindo estes passos:

1. No **Explorador de servidores**, expanda o **serviços em nuvem** nó e localize a instância de função cujos registos que pretende transferir. 

1. A instância de função com o botão direito e, no menu de contexto s, selecione **ver registos do IntelliTrace**. 

    ![Ver a opção de menu de registos do IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Os registos do IntelliTrace são transferidos para um ficheiro num diretório no seu computador local. Os registos de cada vez que solicite o IntelliTrace, é criado um instantâneo de novo. Enquanto os registos estão a ser transferidos, o Visual Studio mostra o progresso da operação no **registo de atividade do Azure** janela. Como é mostrado na figura seguinte, pode expandir o item de linha para a operação ver mais detalhes.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Pode continuar a trabalhar no Visual Studio, enquanto estiver a transferir os registos do IntelliTrace. Quando o registo foi concluída a transferência, abre-se no Visual Studio.

> [!NOTE]
> Os registos do IntelliTrace poderão conter exceções que o framework gera e processa posteriormente. Código do framework interno gera estas exceções como parte normal de iniciar uma função, pelo que pode ignorar com segurança.
> 
> 

## <a name="next-steps"></a>Passos seguintes
- [Opções para depuração cloud services do Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Publicação de um serviço em nuvem do Azure com o Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)