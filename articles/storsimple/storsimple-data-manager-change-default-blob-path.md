---
title: "Caminho de blob de alteração da predefinição | Microsoft Docs"
description: "Saiba como configurar uma função do Azure para mudar o nome de um caminho de ficheiro do blob (pré-visualização privada)"
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
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Alterar um caminho de blob do caminho predefinido (pré-visualização privada)

Este artigo descreve como configurar uma função do Azure para mudar o nome de um caminho de ficheiro de blob predefinido. 

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem uma definição de tarefa que foi configurada corretamente num recurso de dados híbrida dentro de um grupo de recursos.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função do Azure, efetue o seguinte:

1. Aceda ao [Portal do Azure](http://portal.azure.com/).

2. Na parte superior do painel esquerdo, clique em **novo**. 

3. No **pesquisa** caixa, escreva **aplicação de função**, e, em seguida, prima Enter.

    ![Escreva "Aplicação de função" na caixa de pesquisa](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. No **resultados** lista, clique em **aplicação de função**.

    ![Selecione o recurso de aplicação de função na lista de resultados](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    O **aplicação de função** é aberta a janela.

5. Clique em **Criar**.

    ![O botão de "Criar" da janela de aplicação de função](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. No **aplicação de função** painel de configuração, efetue o seguinte procedimento:

    a. No **nome da aplicação** caixa, escreva o nome da aplicação.
    
    b. No **subscrição** caixa, escreva o nome da subscrição.

    c. Em **grupo de recursos**, clique em **criar nova**e, em seguida, escreva o nome do grupo de recursos.

    d. No **alojamento planear** caixa, escreva **consumo planear**.

    e. No **localização** caixa, escreva a localização.

    f. Em **conta de armazenamento**, selecione uma conta de armazenamento existente ou crie uma nova conta de armazenamento. Uma conta de armazenamento é utilizada internamente para a função.

    ![Introduza novos dados de configuração de aplicação de função](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Clique em **Criar**.  
    A aplicação de função é criada.

8. No painel esquerdo, clique em **mais serviços**, e, em seguida, efetue o seguinte:
    
    a. No **filtro** caixa, escreva **serviços aplicacionais**.
    
    b. Clique em **serviços aplicacionais**. 

    ![Hiperligação "Mais serviços" no painel esquerdo](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. Na lista de serviços aplicacionais, clique no nome da aplicação de função.  
    Abre a página da aplicação de função.

10. No painel esquerdo, clique em **nova função**, e, em seguida, efetue o seguinte: 

    a. No **idioma** lista, selecione **c#**.
    
    b. Na matriz de mosaicos do modelo, selecione **QueueTrigger CSharp**.

    c. No **nome da função** caixa, escreva um nome para a sua função.

    d. No **nome da fila** caixa, escreva o nome de definição de tarefa de transformação de dados.

    e. Em **ligação de conta de armazenamento**, clique em **novo**e, em seguida, selecione a conta que corresponda à tarefa de transformação de dados.  
        Anote o nome da ligação. É necessário o nome mais tarde na função do Azure.

       ![Criar uma nova função de c#](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Clique em **Criar**.  
    O **função** é aberta a janela.

11. No **função** janela, execute _.csx_ de ficheiros e, em seguida, efetue o seguinte:

    a. Cole o seguinte código:

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. Substitua **STORAGE_CONNECTIONNAME** na linha 11 com a ligação de conta de armazenamento (consulte o ponto de 8 c).

    c. Na parte superior esquerda, clique em **guardar**.

    ![Guardar a função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para concluir a função, adicione um ficheiro mais efetuando o seguinte procedimento:

    a. Clique em **ver ficheiros**.

       ![A hiperligação "Ver ficheiros"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Clique em **Adicionar**.
    
    c. Tipo **project.json**, e, em seguida, prima Enter.
    
    d. No **project.json** ficheiro, cole o seguinte código:

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. Clique em **Guardar**.

Foi criada com uma função do Azure. Esta função é acionada sempre que é gerado um novo blob pela tarefa de transformação de dados.

## <a name="next-steps"></a>Passos seguintes

[Utilize o Gestor de dados StorSimple IU para transformar os seus dados](storsimple-data-manager-ui.md)
