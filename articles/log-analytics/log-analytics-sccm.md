---
title: "Ligar o Gestor de configuração ao Log Analytics | Microsoft Docs"
description: "Este artigo mostra os passos para ligar o Configuration Manager para análise de registos e iniciar a análise de dados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 62d31ed486458245156f7fc832294d662c62991e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Ligar o Gestor de configuração ao Log Analytics
Pode ligar o System Center Configuration Manager para análise de registos na OMS para dados de coleção de dispositivos de sincronização. Isto disponibiliza dados da sua hierarquia do Configuration Manager na OMS.

## <a name="prerequisites"></a>Pré-requisitos

Análise de registos suporta ramo atual do System Center Configuration Manager, versão 1606 e planos superior.  

## <a name="configuration-overview"></a>Descrição geral de configuração
Os seguintes passos resume o processo para ligar o Configuration Manager para análise de registos.  

1. No Portal de gestão do Azure, registe o Configuration Manager como uma aplicação de aplicação Web e/ou Web API e certifique-se de que tem o ID de cliente e a chave secreta do cliente do registo do Azure Active Directory. Consulte [portal de utilização para criar do Active Directory principal de serviço e aplicação que pode aceder aos recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obter informações detalhadas sobre como efetuar este passo.
2. No Portal de gestão do Azure, [fornecer o Configuration Manager (a aplicação web registado) com permissão para aceder à OMS](#provide-configuration-manager-with-permissions-to-oms).
3. No Configuration Manager, [adicionar uma ligação utilizando o Assistente para adicionar ligação OMS](#add-an-oms-connection-to-configuration-manager).
4. No Configuration Manager, [atualizar as propriedades de ligação](#update-oms-connection-properties) se a chave secreta do cliente ou a palavra-passe nunca expira ou se tenha perdida.
5. Com as informações a partir do portal do OMS [transfira e instale o Microsoft Monitoring Agent](#download-and-install-the-agent) no computador que executa a função de sistema de sites de ponto do Configuration Manager service ligação. O agente envia dados do Configuration Manager para OMS.
6. Na análise de registos, [importar coleções do Configuration Manager](#import-collections) como grupos de computadores.
7. Na análise de registos, ver dados do Configuration Manager como [grupos de computadores](log-analytics-computer-groups.md).

Pode ler mais sobre a ligação do Configuration Manager à OMS no [sincronizar os dados do Configuration Manager para o Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Forneça o Configuration Manager com permissões para OMS
O procedimento seguinte fornece o Portal de gestão do Azure com permissões para aceder à OMS. Especificamente, tem de conceder a *função de contribuinte* aos utilizadores no grupo de recursos para permitir que o Portal de gestão do Azure ligar o Configuration Manager ao OMS.

> [!NOTE]
> Tem de especificar permissões no OMS para o Configuration Manager. Caso contrário, receberá uma mensagem de erro quando utiliza o Assistente de configuração no Configuration Manager.
>
>

1. Abra o [portal do Azure](https://portal.azure.com/) e clique em **procurar** > **análise de registos (OMS)** para abrir o painel de análise de registos (OMS).  
2. No **análise de registos (OMS)** painel, clique em **adicionar** para abrir o **área de trabalho OMS** painel.  
   ![Painel do OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. No **área de trabalho OMS** painel, forneça as seguintes informações e, em seguida, clique em **OK**.

   * **Área de trabalho do OMS**
   * **Subscrição**
   * **Grupo de recursos**
   * **Localização**
   * **Escalão de preço**  
     ![Painel do OMS](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > O exemplo anterior cria um novo grupo de recursos. O grupo de recursos só é utilizado para fornecer o Configuration Manager com permissões para a área de trabalho do OMS neste exemplo.
     >
     >
4. Clique em **procurar** > **grupos de recursos** para abrir o **grupos de recursos** painel.
5. No **grupos de recursos** painel, clique o grupo de recursos que criou acima para abrir o &lt;nome do grupo de recursos&gt; painel Definições.  
   ![Painel de definições do grupo de recursos](./media/log-analytics-sccm/sccm-azure03.png)
6. No &lt;nome do grupo de recursos&gt; painel Definições, clique em controlo de acesso (IAM) para abrir o &lt;nome do grupo de recursos&gt; painel de utilizadores.  
   ![Painel de utilizadores do grupo de recursos](./media/log-analytics-sccm/sccm-azure04.png)  
7. No &lt;nome do grupo de recursos&gt; painel de utilizadores, clique em **adicionar** para abrir o **adicionar acesso** painel.
8. No **adicionar acesso** painel, clique em **selecionar uma função**e, em seguida, selecione o **contribuinte** função.  
   ![Selecione uma função](./media/log-analytics-sccm/sccm-azure05.png)  
9. Clique em **adicionar utilizadores**, selecione o utilizador do Gestor de configuração, clique em **selecione**e, em seguida, clique em **OK**.  
   ![Adicionar utilizadores](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Adicionar uma ligação de OMS ao Configuration Manager
Para adicionar uma ligação do OMS, ambiente do Configuration Manager tem de ter um [ponto de ligação de serviço](https://technet.microsoft.com/library/mt627781.aspx) configurados para o modo online.

1. No **administração** área de trabalho do Configuration Manager, selecione **OMS conector**. Esta ação abre o **Adicionar Assistente de ligação do OMS**. Selecione **seguinte**.
2. No **geral** ecrã, confirme que efetuou as seguintes ações e que que tenha os detalhes para cada item, em seguida, selecione **seguinte**.

   1. No Portal de gestão do Azure, tiver registado do Configuration Manager como uma aplicação de aplicação Web e/ou Web API e de que tem o [ID de cliente a partir do registo](../active-directory/active-directory-integrating-applications.md).
   2. No Portal de gestão do Azure, criou uma chave de segredo de aplicação para a aplicação registada no Azure Active Directory.  
   3. No Portal de gestão do Azure, que forneceu a aplicação web registado com permissão para aceder à OMS.  
      ![Ligação à página geral de assistente do OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. No **do Azure Active Directory** ecrã, configure as definições de ligação para o OMS, fornecendo o **inquilino** , **ID de cliente** , e **chave de segredo do cliente**  , em seguida, selecione **seguinte**.  
   ![Ligação à página OMS Assistente do Azure Active Directory](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Se lhe conseguido todos os outros procedimentos com êxito, em seguida, as informações no **configuração da ligação OMS** ecrã serão apresentadas automaticamente nesta página. As informações para as definições de ligação devem aparecer para sua **subscrição do Azure** , **grupo de recursos do Azure** , e **área de trabalho do Operations Management Suite**.  
   ![Ligação à página de ligação do OMS assistente OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. O assistente liga ao serviço do OMS utilizando as informações que tenha de entrada. Selecione as coleções de dispositivos que pretende sincronizar com o OMS e, em seguida, clique em **adicionar**.  
   ![Selecione as coleções](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Verifique as definições de ligação no **resumo** ecrã, em seguida, selecione **seguinte**. O **progresso** ecrã mostra o estado da ligação, em seguida, deve **concluída**.

> [!NOTE]
> OMS tem de ligar para o site de nível superior na sua hierarquia. Se ligar OMS para um site primário autónomo e, em seguida, adicionar um site de administração central ao seu ambiente, terá de eliminar e recriar a ligação do OMS dentro da nova hierarquia.
>
>

Depois de ligação do Configuration Manager para OMS, pode adicionar ou remover coleções e ver as propriedades da ligação OMS.

## <a name="update-oms-connection-properties"></a>Atualizar propriedades de ligação do OMS
Se uma chave secreta de cliente ou a palavra-passe nunca expira ou se tenha perdida, terá de atualizar manualmente as propriedades de ligação do OMS.

1. No Configuration Manager, navegue para **serviços em nuvem** , em seguida, selecione **OMS conector** para abrir o **propriedades de ligação do OMS** página.
2. Nesta página, clique em de **do Azure Active Directory** separador para ver o **inquilino**, **ID de cliente**, **expiração chave secreta de cliente**. **Certifique-se** sua **chave secreta do cliente** se tiver expirado.

## <a name="download-and-install-the-agent"></a>Transfira e instale o agente
1. No portal do OMS, [transferir o ficheiro de configuração do agente do OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Utilize um dos seguintes métodos para instalar e configurar o agente no computador que executa a função de sistema de sites de ponto do Configuration Manager service ligação:
   * [Instalar o agente utilizando a configuração](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [Instalar o agente utilizando a linha de comandos](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [Instalar o agente utilizando DSC na automatização do Azure](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>Importar coleções
Depois de ter uma ligação de OMS foi adicionada para o Configuration Manager e o agente instalado no computador com a ligação de serviço do Configuration Manager função do sistema de sites do ponto, o passo seguinte é para importar coleções do Configuration Manager na OMS como computador grupos.

Após a importação estiver ativada, as informações de associação da coleção são obtidas todos os 3 horas para manter as associações de coleção atual. Pode optar por desativar a importação em qualquer altura.

1. No portal do OMS, clique em **definições**.
2. Clique em de **grupos de computadores** separador e, em seguida, clique o **SCCM** separador.
3. Selecione **associações de coleção do Gestor de configuração da importação** e, em seguida, clique em **guardar**.  
   ![Grupos de computadores - separador SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Ver dados do Configuration Manager
Depois de ter uma ligação de OMS foi adicionada para o Configuration Manager e o agente foi instalado no computador com a função de sistema de sites de ponto do Configuration Manager service ligação, são enviados dados do agente do OMS. Na OMS, as coleções do Configuration Manager aparecem como [grupos de computadores](log-analytics-computer-groups.md). Pode ver os grupos do **do Configuration Manager** página em **grupos de computadores** no **definições**.

Depois das coleções são importadas, pode ver que foram detetados como quantos computadores com associações de coleção. Também pode ver o número de coleções que tenham sido importados.

![Grupos de computadores - separador SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Ao clicar em qualquer um, abre-se procurar, apresentar o todas os grupos importados ou todos os computadores que pertencem a cada grupo. Utilizar [pesquisa registo](log-analytics-log-searches.md), pode iniciar uma análise aprofundada de dados do Configuration Manager.

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisa registo](log-analytics-log-searches.md) para ver informações detalhadas sobre os dados do Configuration Manager.
