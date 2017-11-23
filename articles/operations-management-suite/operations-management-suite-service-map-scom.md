---
title: "Integração de mapa de serviço com o System Center Operations Manager | Microsoft Docs"
description: "Mapa de serviço é uma solução de Operations Management Suite que Deteta os componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços automaticamente. Este artigo aborda utilizando o mapa de serviço para criar automaticamente os diagramas de aplicação distribuída no Operations Manager."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: af1f683f08ff6b70b23ff265f39b9a76f92f4be2
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Integração de mapa de serviço com o System Center Operations Manager
  > [!NOTE]
  > Esta funcionalidade está em pré-visualização pública.
  > 
  
Mapa de serviço do Operations Management Suite automaticamente Deteta os componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Mapa de serviço permite-lhe ver os servidores a forma errada dos mesmos, como sistemas interligados que fornecem serviços críticos. Mapa de serviço mostra as ligações entre servidores, processos e portas em qualquer arquitetura ligados de TCP, sem qualquer configuração necessária para além da instalação de um agente. Para obter mais informações, consulte o [documentação de mapa de serviço](operations-management-suite-service-map.md).

Esta integração entre o mapa de serviço e o System Center Operations Manager, pode criar automaticamente diagramas de aplicação distribuída no Operations Manager que se baseiam os maps de dependência dinâmica no mapa de serviço.

## <a name="prerequisites"></a>Pré-requisitos
* Um grupo de gestão do Operations Manager (2012 R2 ou posterior) que está a gerir um conjunto de servidores.
* Uma área de trabalho de Operations Management Suite com a solução de mapa de serviço ativada.
* Um conjunto de servidores (pelo menos um) que estão a ser gerido pelo Operations Manager e o envio de dados para o mapa de serviço. São suportados servidores Windows e Linux.
* Um principal de serviço com acesso à subscrição do Azure que está associado a área de trabalho do Operations Management Suite. Para obter mais informações, aceda a [criar um principal de serviço](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gestão de mapa de serviço
Ativar a integração entre o Operations Manager e o mapa de serviço ao importar o grupo de pacotes de gestão Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Pode transferir o grupo do pacote de gestão do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gestão:
* Vistas de aplicação de mapa de serviço da Microsoft
* Mapa de serviço do Microsoft System Center interno
* Substituições de mapa de serviço do Microsoft System Center
* Mapa de serviço do Microsoft System Center

## <a name="configure-the-service-map-integration"></a>Configurar a integração de mapa de serviço
Depois de instalar o pacote de gestão de mapa de serviço, um novo nó **mapa de serviço**, é apresentado em **Operations Management Suite** no **administração** painel. 

Para configurar a integração de mapa de serviço, efetue o seguinte:

1. Para abrir o Assistente de configuração, no **descrição geral do mapa de serviço** painel, clique em **adicionar a área de trabalho**.  

    ![Painel de descrição geral do mapa de serviço](media/oms-service-map/scom-configuration.png)

2. No **configuração da ligação** janela, introduza o nome de inquilino ou o ID, o ID da aplicação (também conhecido como o nome de utilizador ou o clientID) e a palavra-passe do principal de serviço e, em seguida, clique em **seguinte**. Para obter mais informações, aceda a [criar um principal de serviço](#creating-a-service-principal).

    ![A janela de configuração da ligação](media/oms-service-map/scom-config-spn.png)

3. No **seleção de subscrição** janela, selecione a subscrição do Azure, o grupo de recursos do Azure (aquele que contém a área de trabalho do Operations Management Suite) e a área de trabalho do Operations Management Suite e, em seguida, clique em **seguinte**.

    ![A área de trabalho de configuração do Operations Manager](media/oms-service-map/scom-config-workspace.png)

4. No **seleção de grupo máquina** janela, escolher os grupos de máquina de mapa de serviço que pretende sincronizar para o Operations Manager. Clique em **Adicionar/remover máquina grupos**, escolher grupos na lista de **grupos máquina disponíveis**e clique em **adicionar**.  Quando tiver terminado de selecionar grupos, clique em **Ok** para concluir.
    
    ![Os grupos de máquina de configuração do Gestor de operações](media/oms-service-map/scom-config-machine-groups.png)
    
5. No **seleção de servidor** janela, configurar o grupo de servidores de mapa de serviço com os servidores que pretende que a sincronização entre o Operations Manager e o mapa de serviço. Clique em **Adicionar/remover servidores**.   
    
    Para a integração criar um diagrama de aplicação distribuída para um servidor, tem de ser o servidor:

    * Gerido pelo Operations Manager
    * Gerido pelo mapa de serviço
    * Listados no grupo de servidores de mapa de serviço

    ![O grupo de configuração do Operations Manager](media/oms-service-map/scom-config-group.png)

6. Opcional: Selecione o agrupamento de recursos do servidor de gestão para comunicar com o Operations Management Suite e, em seguida, clique em **adicionar a área de trabalho**.

    ![O agrupamento de recursos de configuração do Gestor de operações](media/oms-service-map/scom-config-pool.png)

    Poderá demorar um minuto para configurar e registar a área de trabalho do Operations Management Suite. Depois de estar configurada, o Operations Manager inicia a primeira sincronização de mapa de serviço do Operations Management Suite.

    ![O agrupamento de recursos de configuração do Gestor de operações](media/oms-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Mapa de serviço do monitor
Após a ligação, a área de trabalho do Operations Management Suite é uma nova pasta, o mapa de serviço, é apresentada no **monitorização** painel da consola do Operations Manager.

![Painel de monitorização do Operations Manager](media/oms-service-map/scom-monitoring.png)

A pasta de mapa de serviço tem quatro nós:
* **Alertas ativos**: apresenta uma lista de todos os alertas ativos sobre a comunicação entre o Operations Manager e o mapa de serviço.  Tenha em atenção que estes alertas não estão a ser sincronizada com êxito para o Operations Manager de alertas do Operations Management Suite. 

* **Servidores**: apresenta uma lista de servidores monitorizados que estão configurados para sincronização do mapa de serviço.

    ![O painel de servidores de monitorização do Operations Manager](media/oms-service-map/scom-monitoring-servers.png)

* **Vistas de dependência do grupo de computador**: apresenta uma lista de todos os grupos de computador que são sincronizados a partir de mapa de serviço. Pode clicar em qualquer grupo para ver o diagrama de aplicação distribuída.

    ![O diagrama de aplicação distribuída do Operations Manager](media/oms-service-map/scom-group-dad.png)

* **Vistas de dependência do**: apresenta uma lista de todos os servidores que são sincronizados a partir de mapa de serviço. Pode clicar em qualquer servidor para ver o diagrama de aplicação distribuída.

    ![O diagrama de aplicação distribuída do Operations Manager](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou eliminar a área de trabalho
Pode editar ou eliminar a área de trabalho configurada através de **descrição geral do mapa de serviço** painel (**administração** painel > **Operations Management Suite** > **mapa de serviço**). Pode configurar apenas uma área de trabalho do Operations Management Suite por agora.

![O painel da área de trabalho do Operations Manager editar](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar as regras e substituições
Uma regra, _Microsoft.SystemCenter.ServiceMapImport.Rule_, é criada para obter periodicamente informações do mapa de serviço. Para alterar as temporizações de sincronização, pode configurar substituições da regra (**criação** painel > **regras** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![A janela de propriedades substituições do Operations Manager](media/oms-service-map/scom-overrides.png)

* **Ativado**: Ativar ou desativar as atualizações automáticas. 
* **IntervalMinutes**: repor a hora entre as atualizações. O intervalo predefinido é uma hora. Se pretender sincronizar com mais frequência mapas de servidor, pode alterar o valor.
* **TimeoutSeconds**: repor o período de tempo antes do pedido exceder o tempo limite. 
* **TimeWindowMinutes**: repor a janela de tempo para consultar os dados. Predefinição é uma janela de 60 minutos. O valor máximo permitido pelo mapa de serviço é de 60 minutos.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design apresenta os seguintes problemas e limitações:
* Apenas poderá ligar a uma única área de trabalho do Operations Management Suite.
* Embora possa adicionar servidores para o grupo de servidores de mapa de serviço manualmente através do **criação** painel, os maps para esses servidores não são sincronizados imediatamente.  Estes serão sincronizadas de mapa de serviço durante o próximo ciclo de sincronização.
* Se efetuar alterações para os diagramas de aplicação distribuída criada pelo pacote de gestão, essas alterações, provavelmente, serão substituídas na próxima sincronização com o mapa de serviço.

## <a name="create-a-service-principal"></a>Criar um principal de serviço
Para obter documentação oficial do Azure sobre a criação de um serviço principal, consulte:
* [Criar um principal de serviço com o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar um principal de serviço, utilizando a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar um principal de serviço utilizando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Tem quaisquer comentários para-nos sobre o mapa de serviço ou esta documentação? Visite a nossa [página de voz do utilizador](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde pode sugerir funcionalidades ou votar em sugestões existentes.
