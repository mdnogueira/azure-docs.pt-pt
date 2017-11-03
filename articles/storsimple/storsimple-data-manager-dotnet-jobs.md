---
title: Utilizar o SDK do .NET para tarefas do Gestor de dados do Microsoft Azure StorSimple | Microsoft Docs
description: "Saiba como utilizar o .NET SDK para iniciar o Gestor de dados do StorSimple tarefas (pré-visualização privada)"
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
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Utilizar o SDK do .net para iniciar a transformação de dados (pré-visualização privada)

## <a name="overview"></a>Descrição geral

Este artigo explica como pode utilizar a funcionalidade de transformação de dados do serviço StorSimple Manager de dados para transformar dados de dispositivo StorSimple. Os dados transformados, em seguida, estão a ser consumidos por outros serviços do Azure na nuvem. O artigo tem também uma explicação passo a passo para o ajudar a criar uma aplicação de consola .NET de exemplo para iniciar uma tarefa de transformação de dados e, em seguida, monitorizá-lo para conclusão.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:
*   Um sistema com o Visual Studio 2012 2013, 2015 ou 2017 instalado.
*   O Azure Powershell instalado. [Transfira o Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Definições de configuração ao inicializar a tarefa de transformação de dados (instruções para obter estas definições são incluídas aqui).
*   Uma definição de tarefa que foi configurada corretamente num recurso de dados híbrida dentro de um grupo de recursos.
*   Todas as dlls necessárias. Transfira estes dlls do [repositório do GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   `Get-ConfigurationParams.ps1`[script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) do repositório github.

## <a name="step-by-step"></a>Passo-a-passo

Execute os seguintes passos para utilizar o .NET para iniciar uma tarefa de transformação de dados.

1. Para obter os parâmetros de configuração, efetue os seguintes passos:
    1. Transferir o `Get-ConfigurationParams.ps1` do script de repositório github em `C:\DataTransformation` localização.
    1. Execute o `Get-ConfigurationParams.ps1` script no repositório github. Escreva o seguinte comando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Pode passar quaisquer valores para o ActiveDirectoryKey e AppName.


2. Este script produz os seguintes valores:
    * ID de cliente
    * ID do inquilino
    * Chaves de diretório Active Directory (igual de um introduzido acima)
    * ID da subscrição

3. Utilizar o Visual Studio 2012, 2013 ou 2015, crie uma aplicação de consola c# .NET.

    1. Iniciar **Visual Studio 2012/2013/2015**.
    1. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**.
    2. Expanda **Modelos** e selecione **Visual C#**.
    3. Selecione **Aplicação de Consola** na lista de tipos de projeto à direita.
    4. Introduza **DataTransformationApp** para o **nome**.
    5. Selecione **C:\DataTransformation** para o **localização**.
    6. Clique em **OK** para criar o projeto.

4.  Agora, adicione todas as DLLs presentes no [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) pasta como **referências** no projeto que criou. Para transferir os ficheiros dll, efetue o seguinte:

    1. No Visual Studio, aceda a **vista > Explorador de soluções**.
    1. Clique na seta à esquerda do projeto de aplicação de transformação de dados. Clique em **referências** e, em seguida, faça duplo clique para **Adicionar referência**.
    2. Navegue para a localização da pasta de pacotes, selecione todas as DLLs e clique em **adicionar**e, em seguida, clique em **OK**.

5. Adicione o seguinte ao **utilizar** instruções para o ficheiro de origem (Program.cs) no projeto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. O código seguinte inicializa a instância de tarefa de transformação de dados. Adicionar este no **método Main**. Substitua os valores dos parâmetros de configuração que obteve anteriormente. Plug-in os valores de **nome do grupo de recursos** e **nome de recurso de dados de híbrida**. O **nome do grupo de recursos** é aquele que aloja o recurso de dados híbrida, na qual foi configurada a definição de tarefa.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);

    ```

7. Especifique os parâmetros com o qual a definição de tarefa tem de ser executado

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);

    ```

    (OU)

    Se pretender alterar os parâmetros de definição de tarefa durante o tempo de execução, em seguida, adicione o seguinte código:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    
    ```

8. Após a inicialização, adicione o seguinte código para acionar uma tarefa de transformação de dados na definição da tarefa. Plug-in apropriados **nome da definição de tarefa**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);

    ```

9. Esta tarefa carrega os ficheiros correspondentes presentes no diretório de raiz no volume do StorSimple para o contentor especificado. Quando um ficheiro é carregado, uma mensagem foi removida da fila (na mesma conta de armazenamento como o contentor) com o mesmo nome que a definição de tarefa. Esta mensagem pode ser utilizada como um acionador para iniciar a qualquer processamento adicional do ficheiro.

10. Depois da tarefa foi acionada, adicione o seguinte código para controlar a tarefa de conclusão.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```


## <a name="next-steps"></a>Passos seguintes

[Utilize o Gestor de dados StorSimple IU para transformar os seus dados](storsimple-data-manager-ui.md).
