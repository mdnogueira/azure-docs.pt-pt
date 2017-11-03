---
title: "Utilize a automatização do Azure para acionar uma tarefa | Microsoft Docs"
description: "Saiba como utilizar a automatização do Azure para acionar as tarefas de Data do StorSimple Manager (pré-visualização privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Utilize a automatização do Azure para acionar uma tarefa (pré-visualização privada)

Este artigo descreve como utilizar a automatização do Azure para acionar uma tarefa de Gestor de dados do StorSimple.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   O Azure Powershell instalado. [Transfira o Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Definições de configuração ao inicializar a tarefa de transformação de dados (instruções para obter estas definições são incluídas aqui).
*   Uma definição de tarefa que foi configurada corretamente num recurso de dados híbrida dentro de um grupo de recursos.
*   Transferir `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) ficheiro a partir do repositório github.
*   Transferir `Get-ConfigurationParams.ps1` [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) do repositório github.
*   Transferir `Trigger-DataTransformation-Job.ps1` [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) do repositório github.

## <a name="step-by-step"></a>Passo-a-passo

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Obter as permissões do Azure Active Directory para a tarefa de automatização executar a definição de tarefa

1. Para obter os parâmetros de configuração para o Active Directory, efetue os seguintes passos:

    1. Abra o Windows PowerShell no seu computador local. Certifique-se de que [Azure PowerShell](https://azure.microsoft.com/downloads/) está instalado.
    1. Execute o `Get-ConfigurationParams.ps1` script (na pasta que transferiu acima). Escreva o seguinte comando na janela do PowerShell:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        O ActiveDirectoryKey é uma palavra-passe que utilizar mais tarde. Introduza uma palavra-passe à sua escolha. AppName pode ser qualquer cadeia.

2. Este script produz os seguintes valores que devem ser utilizados ao acionar o runbook de automatização. Tome nota destes valores.

    - ID de cliente
    - ID do inquilino
    - Chaves de diretório Active Directory (igual de um introduzido acima)
    - ID da subscrição

### <a name="set-up-the-automation-account"></a>Configurar a conta de automatização

1. Inicie sessão no Azure e abra a sua conta de automatização.
2. Clique em **ativos** mosaico para abrir a lista de recursos.
3. Clique em **módulos** mosaico para abrir a lista de módulos.
4. Clique em **+ adicionar um módulo** botão e o painel do módulo de adicionar é iniciada.

    ![Definições de conta de automatização](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Depois de selecionar o `DataTransformationApp.zip` ficheiro a partir do seu computador local, clique em **OK** para importar o módulo.

   Quando a automatização do Azure importa um módulo à sua conta, extrai metadados sobre o módulo. Esta operação poderá demorar alguns minutos.

   ![Definições de conta de automatização](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Receberá uma notificação que o módulo está a ser implementado e outra notificação quando o processo estiver concluído.  Também pode verificar o estado **módulos** mosaico.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Para importar o runbook que aciona a definição de tarefa

1. No portal do Azure, abra a sua conta da Automatização.
2. Clique em **Runbooks** mosaico para abrir a lista de runbooks.
3. Clique em **+ adicionar um runbook** e, em seguida, **importar um runbook existente**.

   ![Importar um runbook existente](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Clique em **Runbook ficheiro** e selecione o ficheiro a importar `Trigger-DataTransformation-Job.ps1`.
5. Clique em **criar** para importar o runbook. O novo runbook aparece na lista de runbooks da conta de automatização.
7. Clique em **acionador-DataTransformation-Job** runbook e, em seguida, clique em **editar**.
8. Clique em **publicar** e, em seguida, **Sim** quando um pedido de confirmação.


### <a name="to-run-the-runbook"></a>Para executar o runbook:
1. No portal do Azure, abra a sua conta da Automatização.
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Clique em **acionador-DataTransformation-Job**.
4. Clique em **Iniciar** para iniciar o runbook.

   ![Iniciar o runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. No **iniciar runbook** painel, introduza todos os parâmetros. Clique em **OK** para submeter a tarefa de transformação de dados.

   ![Iniciar o runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Passos seguintes

[Utilize o Gestor de dados StorSimple IU para transformar os seus dados](storsimple-data-manager-ui.md).