---
title: "Com o Visual Studio publicar Assistente da aplicação do Azure | Microsoft Docs"
description: "Saiba como configurar várias definições no Visual Studio publicar Assistente da aplicação Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 25b3ca9af2639860d9cfcb1492aef745fb47beb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Com o Visual Studio publicar Assistente da aplicação do Azure
Depois de desenvolver uma aplicação web no Visual Studio, pode publicar essa aplicação para um serviço em nuvem do Azure utilizando o **publicar aplicação Azure** assistente. 

> [!NOTE]
> Este tópico é sobre a implementação de serviços em nuvem, não para web sites. Para obter informações sobre a implementação em web sites, consulte [como implementar um Web Site do Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).
> 
> 

## <a name="accessing-the-publish-azure-application-wizard"></a>Aceder ao Assistente para publicar aplicações do Azure

Pode aceder ao Assistente de publicar aplicação Azure de duas formas dependendo do tipo de projeto do Visual Studio que tem.

**Se tiver um projeto de serviço em nuvem do Azure:**

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, clique com o botão direito no projeto e, no menu de contexto, selecione **publicar**.

**Se tiver um projeto de aplicação web não está ativado para o Azure:**

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, clique com o botão direito no projeto e, no menu de contexto, selecione **converter** > **converter para o projeto de serviço em nuvem do Azure**. 

1. No **Explorador de soluções**, clique com o botão direito no projeto do Azure criado recentemente e, no menu de contexto, selecione **publicar**.

## <a name="sign-in-page"></a>Página de início de sessão

![Página de início de sessão](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Conta** - Selecione uma conta ou **adicionar uma conta** na lista pendente conta.

**Escolha a sua subscrição** -escolher a subscrição a utilizar para a sua implementação.
   
## <a name="settings-page---common-settings-tab"></a>Página de definições - separador de definições comuns   

![Definições comuns](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**O serviço em nuvem** -através da lista pendente, selecione uma nuvem existente, serviço ou selecione  **&lt;criar novo >**e criar um serviço em nuvem. O Centro de dados apresenta parênteses para cada serviço em nuvem. Recomenda-se que o Centro de dados localização para o serviço em nuvem sejam os mesmos que a localização do Centro de dados para a conta de armazenamento (definições avançadas).  

**Ambiente** -selecione **produção** ou **transição**. Escolha o ambiente de teste se pretender implementar a aplicação num ambiente de teste. 

**Criar configuração** -selecione **depurar** ou **versão**.

**A configuração do serviço** -selecione **nuvem** ou **Local**.
   
**Ativar o ambiente de trabalho remoto para todas as funções** -confirme esta opção se pretender ser capaz de ligar remotamente ao serviço. Esta opção é utilizada principalmente para resolução de problemas. Ao selecionar esta caixa de verificação, o **configuração do ambiente de trabalho remoto** é apresentada a caixa de diálogo. Escolha o **definições** ligação para alterar a configuração.
   
**Ativar o Web Deploy para todos os web funções** -confirme esta opção, para ativar a implementação do serviço web. Tem de selecionar o **ativar o ambiente de trabalho remoto para todas as funções** opção para utilizar esta funcionalidade. Para obter mais informações, consulte [ [publicação de um serviço em nuvem do Azure com o Visual Studio](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). 

## <a name="settings-page---advanced-settings-tab"></a>Página de definições - avançada do separador Definições

![Definições avançadas](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Etiqueta de implementação** -aceite o nome predefinido ou introduza um nome à sua escolha. Acrescentar a data para a etiqueta de implementação, deixe a caixa de verificação selecionada. 
   
**Conta de armazenamento** -selecionar a conta de armazenamento a utilizar para esta implementação, * *&lt;criar novo > para criar uma conta de armazenamento. O Centro de dados apresenta parênteses para cada conta de armazenamento. Recomenda-se que o Centro de dados localização para a conta de armazenamento sejam os mesmos que a localização do Centro de dados para o serviço de nuvem (definições comuns).  
   
A conta de armazenamento do Azure armazena o pacote para a implementação da aplicação. Depois da aplicação é implementada, o pacote é removido da conta do storage.

**Eliminar a implementação em caso de falha** -Selecione esta opção para que a implementação eliminada se forem encontrados erros durante a publicação. Deverá ser desmarcada se pretender manter um endereço IP virtual constante do serviço em nuvem.

**Atualização da implementação** -Selecione esta opção se pretender implementar apenas componentes atualizados. Este tipo de implementação pode ser mais rápido do que uma implementação completa. Isto deve ser verificado se pretender manter um endereço IP virtual constante do serviço em nuvem. 

**Atualização da implementação - definições** -esta caixa de diálogo é utilizada para especificar ainda mais a forma como pretende que as funções a serem atualizados. Se optar por **atualização Incremental**, cada instância da sua aplicação é atualizada após a si, para que a aplicação está sempre disponível. Se optar por **atualização em simultâneo**, todas as instâncias da aplicação são atualizadas ao mesmo tempo. A atualização em simultâneo é mais rápida, mas o serviço poderá não estar disponível durante o processo de atualização. 

![Definições de implementação](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**Ativar o IntelliTrace** -especificar se pretende ativar o IntelliTrace. Com IntelliTrace, que pode iniciar sessão extensas informações de depuração para uma instância de função quando for executada no Azure. Se precisar de descobrir a causa de um problema, pode utilizar os registos do IntelliTrace para seguir o código do Visual Studio como se estivesse em execução no Azure. Para obter mais informações sobre como utilizar o IntelliTrace, consulte [depuração um serviço em nuvem do Azure publicadas com o Visual Studio e o IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md). 

**Ativar a criação de perfis** -especificar se pretende ativar a criação de perfis de desempenho. O gerador de perfis do Visual Studio permite-lhe obter uma análise aprofundada dos aspetos computacionais da forma como o serviço em nuvem é executado. Para obter mais informações sobre como utilizar o gerador de perfis do Visual Studio, consulte [testar o desempenho de um serviço em nuvem do Azure](./vs-azure-tools-performance-profiling-cloud-services.md).

**Ativar o depurador remoto para todas as funções** -especificar se pretende ativar a depuração remota. Para obter mais informações sobre a depuração serviços em nuvem com o Visual Studio, consulte [depuração um serviço em nuvem do Azure ou a máquina virtual no Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Página de definições de diagnóstico

![Definições de diagnóstico](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnóstico permite-lhe resolver problemas de um serviço em nuvem do Azure (ou a máquina virtual do Azure). Para obter informações sobre diagnósticos, consulte [configurar diagnósticos para máquinas virtuais e serviços Cloud do Azure](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Para obter informações sobre o Application Insights, consulte [o que é Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Página de resumo

![Resumo](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Perfil de destino** -pode optar por criar um perfil de publicação das definições que escolheu. Por exemplo, poderá criar um perfil para um ambiente de teste e outra para a produção. Para guardar este perfil, escolha o **guardar** ícone. O assistente cria o perfil e guarda-o no projeto do Visual Studio. Para modificar o nome do perfil, abra o **destino perfil** lista e, em seguida, escolha **< gerir … >**.
   
   > [!NOTE]
   > O perfil de publicação é apresentada no Explorador de soluções no Visual Studio e as definições de perfil que são escritas para um ficheiro com uma extensão de .azurePubxml. As definições são guardadas como atributos das etiquetas XML.
   > 
   > 

## <a name="publishing-your-application"></a>Publicação da sua aplicação

Depois de configurar todas as definições para a implementação do seu projeto, selecione **publicar** na parte inferior da caixa de diálogo. Pode monitorizar o estado do processo no **saída** janela no Visual Studio.

## <a name="next-steps"></a>Passos seguintes
- [Migrar e publicar uma aplicação Web num serviço em nuvem do Azure a partir do Visual Studio](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)
- [Saiba como utilizar o Visual Studio para publicar um serviço em nuvem do Azure](./vs-azure-tools-publishing-a-cloud-service.md)
- [A depuração de um serviço em nuvem do Azure publicadas com o Visual Studio e o IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)
- [Testar o desempenho de um serviço em nuvem do Azure](./vs-azure-tools-performance-profiling-cloud-services.md)
- [Configurar diagnósticos para máquinas virtuais e serviços em nuvem do Azure](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). 
- [O que é o Application Insights?](./application-insights/app-insights-overview.md)