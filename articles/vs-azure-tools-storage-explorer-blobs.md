---
title: "Gerir recursos de armazenamento de Blobs do Azure com o Explorador de armazenamento (pré-visualização) | Microsoft Docs"
description: "Gerir os contentores de Blobs do Azure e Blobs com o Explorador de armazenamento (pré-visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: c23b87cca66df0834a31494be7d8657ff9f2a865
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Gerir recursos de armazenamento de Blobs do Azure com o Explorador de armazenamento (pré-visualização)
## <a name="overview"></a>Descrição geral
[Armazenamento de Blobs do Azure](storage/blobs/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, tais como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS.
Pode utilizar o armazenamento de Blobs para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado. Neste artigo, irá aprender a utilizar o Explorador de armazenamento (pré-visualização) para trabalhar com contentores de BLOBs e blobs.

## <a name="prerequisites"></a>Pré-requisitos
Para executar os passos descritos neste artigo, é necessário o seguinte:

* [Transfira e instale o Explorador de Armazenamento (pré-visualização)](http://www.storageexplorer.com)
* [Ligar a um serviço ou a uma conta de armazenamento do Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contentor de blob
Todos os blobs tem de residir num contentor de blob, que é simplesmente um agrupamento lógico de blobs. Uma conta pode conter um número ilimitado de contentores, e cada contentor pode armazenar um número ilimitado de blobs.

Os passos seguintes mostram como criar um contentor do blob no Explorador de armazenamento (pré-visualização).

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento no qual pretende criar o contentor de blob.
3. Clique com botão direito **contentores de BLOBs**e, no menu de contexto - selecione **criar contentor de Blob**.

   ![Criar o menu de contexto de contentores de BLOBs][0]
4. Será apresentada uma caixa de texto abaixo o **contentores de BLOBs** pasta. Introduza o nome do contentor de blob. Consulte o [regras de nomenclatura de contentor](storage/blobs/storage-dotnet-how-to-use-blobs.md#create-a-container) secção para obter uma lista de restrições em contentores de BLOBs de atribuição de nomes e regras.

   ![Criar caixa de texto de contentores de BLOBs][1]
5. Prima **Enter** quando pronto para criar o contentor de blob, ou **Esc** para cancelar. Depois do contentor de blob foi criado com êxito, será apresentado sob o **contentores de BLOBs** pasta para a conta de armazenamento selecionada.

   ![Criada o contentor de BLOBs][2]

## <a name="view-a-blob-containers-contents"></a>Ver os conteúdos de um contentor de BLOBs
Os contentores de blob contém os blobs e de pastas (que também podem conter os blobs).

Os passos seguintes mostram como ver os conteúdos de um contentor do blob no Explorador de armazenamento (pré-visualização):

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor do blob que pretende ver.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Clique com o botão direito no contentor de blob que pretende visualizar e, no menu de contexto - selecione **abra Editor de contentor do Blob**.
   Também pode fazer duplo clique no contentor de blob que pretende ver.

   ![Menu de contexto de editor de contentor de blob aberta][19]
5. O painel principal apresentará o conteúdo do contentor do blob.

   ![Editor de contentor do blob][3]

## <a name="delete-a-blob-container"></a>Eliminar um contentor do blob
Contentores de blob podem ser facilmente criados e eliminadas, conforme necessário. (Para ver como eliminar os blobs individuais, consulte a secção [gerir os blobs num contentor de blob](#managing-blobs-in-a-blob-container).)

Os passos seguintes ilustram como eliminar um contentor do blob no Explorador de armazenamento (pré-visualização):

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor do blob que pretende ver.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Clique com o botão direito no contentor de blob que pretende eliminar e, no menu de contexto - selecione **eliminar**.
   Também pode premir **eliminar** ao eliminar o contentor de blob atualmente selecionada.

   ![Eliminar o menu de contexto de contentor do blob][4]
5. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

   ![Eliminar o blob confirmação do contentor][5]

## <a name="copy-a-blob-container"></a>Copie um contentor do blob
Explorador de armazenamento (pré-visualização) permite-lhe copiar um contentor do blob para a área de transferência e, em seguida, cole o contentor de BLOBs outra conta de armazenamento. (Para ver como copiar os blobs individuais, consulte a secção [gerir os blobs num contentor de blob](#managing-blobs-in-a-blob-container).)

Os passos seguintes mostram como copiar um contentor do blob de uma conta de armazenamento para outro.

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor do blob que pretende copiar.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Clique com o botão direito no contentor de blob que pretende copiar e, no menu de contexto - selecione **contentor de BLOBs de cópia**.

   ![Copie o menu de contexto de contentor do blob][6]
5. Clique com o botão direito a conta de armazenamento pretendido "destino" para o qual pretende colar o contentor de blob e, no menu de contexto - selecione **colar contentor de Blob**.

   ![Menu de contexto de contentor de blob de colar][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter a SAS para um contentor de blobs
As [assinaturas de acesso partilhado (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) disponibilizam acesso delegado a recursos na sua conta de armazenamento.
Isto significa que pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento durante um determinado período de tempo e com um conjunto de permissões especificadas, sem ter de partilhar as chaves de acesso da conta.

Os passos seguintes mostram como criar uma SAS para um contentor do blob:

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor do blob para o qual pretende obter uma SAS.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Clique com o botão direito no contentor de blob pretendido e, no menu de contexto - selecione **obter assinatura de acesso partilhado**.

   ![Obter o menu de contexto SAS][8]
5. Na caixa de diálogo **Shared Access Signature (Assinatura de Acesso Partilhado)**, especifique a política, as datas de início e de expiração, o fuso horário e os níveis de acesso que atribuir ao recurso.

   ![Obter as opções de SAS][9]
6. Quando tiver terminado de especificar as opções de SAS, selecione **Create (Criar)**.
7. Um segundo **assinatura de acesso partilhado** apresenta uma lista de contentor do blob, juntamente com o URL e QueryStrings pode utilizar para aceder ao recurso de armazenamento, em seguida, irá apresentar caixa de diálogo.
   Selecione **Copy (Copiar)**, junto ao URL que quer copiar para a área de transferências.

   ![Copie o URL SAS][10]
8. Quando terminar, selecione **Close (Fechar)**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerir políticas de acesso para um contentor do blob
Os passos seguintes mostram como gerir (adicionar e remover) políticas para um contentor de BLOBs de acesso:

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de blob cujas políticas de acesso que pretende gerir.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Selecione o contentor de blob pretendido e, no menu de contexto - selecione **gerir políticas de acesso**.

   ![Menu de Contexto Gerir políticas de acesso][11]
5. O **políticas de acesso** apresentará uma caixa de diálogo lista quaisquer políticas de acesso já criadas para o contentor de blob selecionado.

   ![Opções de política de acesso][12]        
6. Siga estes passos consoante a tarefa de gestão de política de acesso:

   * **Adicionar uma política de acesso nova** - selecione **Add (Adicionar)**. Depois de gerada, a caixa de diálogo **Access Policies** mostrará a política de acesso acabada de adicionar (com predefinições).
   * **Editar uma política de acesso** - efetue as edições pretendidas e selecione **guardar**.
   * **Remover uma política de acesso** - selecione **Remove (Remover)** junto à política de acesso que pretende remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o nível de acesso público para um contentor do blob
Por predefinição, cada contentor de blob está definido como "Sem acesso de público".

Os passos seguintes mostram como especificar um nível de acesso público para um contentor do blob.

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de blob cujas políticas de acesso que pretende gerir.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Selecione o contentor de blob pretendido e, no menu de contexto - selecione **definir o nível de acesso público**.

   ![Defina o menu de contexto de nível de acesso público][13]
5. No **definido ao nível do contentor acesso público** caixa de diálogo, especifique o nível de acesso pretendida.

   ![Definir opções de nível de acesso público][14]
6. Selecione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerir os blobs num contentor de blob
Depois de criar um contentor de blob, pode carregar um blob para esse contentor do blob, transferir um blob para o seu computador local, abra um blob no seu computador local e muito mais.

Os passos seguintes mostram como gerir os blobs (e as pastas) dentro de um contentor do blob.

1. Abrir o Explorador de Armazenamento (Pré-visualização).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor do blob que pretende gerir.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Faça duplo clique no contentor de blob que pretende ver.
5. O painel principal apresentará o conteúdo do contentor do blob.

   ![Contentor de BLOBs de vista][3]
6. O painel principal apresentará o conteúdo do contentor do blob.
7. Siga estes passos, consoante a tarefa que pretende realizar:

   * **Carregar ficheiros para um contentor do blob**

     1. Na barra de ferramentas do painel principal, selecione **Upload (Carregar)** e, em seguida, **Upload Files (Carregar Ficheiros)**, no menu pendente.

        ![Carregar o menu de ficheiros][15]
     2. Na caixa de diálogo **Upload files (Carregar ficheiros)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Files (Ficheiros)**, para selecionar o ficheiro ou ficheiros que quer carregar.

        ![Opções de ficheiros de carregamento][16]
     3. Especifique o tipo de **Blob tipo**. O artigo [introdução ao Blob storage do Azure através do .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de Blobs.
     4. Opcionalmente, especifique uma pasta de destino no qual os ficheiros selecionados serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Upload**.
   * **Carregar uma pasta para um contentor do blob**

     1. Na barra de ferramentas do painel principal, selecione **Upload** e, em seguida, **Upload Folder (Carregar Pasta)**, no menu pendente.

        ![Menu Carregar pasta][17]
     2. Na caixa de diálogo **Upload folder (Carregar pasta)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Folder (Pasta)**, para selecionar a pasta cujos conteúdos quer carregar.

        ![Carregue as opções de pastas][18]
     3. Especifique o tipo de **Blob tipo**. O artigo [introdução ao Blob storage do Azure através do .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de Blobs.
     4. Opcionalmente, especifique uma pasta de destino no qual os conteúdos da pasta selecionada serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Upload**.
   * **Transferir um blob para o seu computador local**

     1. Selecione o blob que pretende transferir.
     2. Na barra de ferramentas do painel principal, selecione **Download (Transferir)**.
     3. No **especificar onde pretende guardar o blob transferido** caixa de diálogo, especifique a localização onde pretende que o blob transferido e o nome que pretende atribuir-lhe.  
     4. Selecione **Guardar**.
   * **Abra um blob no seu computador local**

     1. Selecione o blob que pretende abrir.
     2. Na barra de ferramentas do painel principal, selecione **Open (Abrir)**.
     3. O blob será transferido e aberta utilizando a aplicação associada ao tipo de ficheiro subjacente do blob.
   * **Copiar um blob para a área de transferência**

     1. Selecione o blob que pretende copiar.
     2. Na barra de ferramentas do painel principal, selecione **Copy (Copiar)**.
     3. No painel esquerdo, navegue para outro contentor de blob e faça duplo clique para vê-lo no painel principal.
     4. Na barra de ferramentas do painel principal, selecione **colar** para criar uma cópia do blob.
   * **Eliminar um blob**

     1. Selecione o blob que pretende eliminar.
     2. Na barra de ferramentas do painel principal, selecione **Delete (Eliminar)**.
     3. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Passos seguintes
* Vejas os [mais recentes vídeos e notas de versão do Storage Explorer (Preview)](http://www.storageexplorer.com).
* Saiba como [utilizar blobs, tabelas, filas e ficheiros do Azure para criar aplicações](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
