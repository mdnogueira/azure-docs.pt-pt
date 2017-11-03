---
title: Como utilizar o Blob storage do Azure do iOS | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: ios
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: df188021-86fc-4d31-a810-1b0e7bcd814b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: objective-c
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: f238804e6031fcf3f194695a06bf5b88733a27b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-ios"></a>Como utilizar o Blob storage do iOS
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo irá mostrar como efetuar cenários comuns utilizando o armazenamento de Blobs do Microsoft Azure. Os exemplos são escritos em Objective-C e utilize o [biblioteca de clientes do Storage do Azure para iOS](https://github.com/Azure/azure-storage-ios). Os cenários abrangidos incluem **carregar**, **listagem**, **transferir**, e **eliminar** blobs. Para obter mais informações sobre os blobs, consulte o [passos](#next-steps) secção. Também pode transferir o [aplicação de exemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente a utilização do Storage do Azure numa aplicação iOS.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar a biblioteca de iOS do Storage do Azure na sua aplicação
É possível importar a biblioteca de iOS do Storage do Azure na sua aplicação utilizando o [CocoaPod de armazenamento do Azure](https://cocoapods.org/pods/AZSClient) ou importando o **Framework** ficheiro. CocoaPod é a forma recomendada como torna a integração da biblioteca mais fácil, no entanto importar do ficheiro framework é menos intrusivo para o seu projeto existente.

Para utilizar esta biblioteca, precisa do seguinte:
- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Se ainda não o fez, por isso, já, [instalar o CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) no seu computador, abra uma janela de terminal e executando o seguinte comando
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Em seguida, no diretório do projeto (o diretório que contém o ficheiro xcodeproj), crie um novo ficheiro chamado _Podfile_(extensão de ficheiro). Adicione o seguinte ao _Podfile_ e guardar.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Na janela de terminal, navegue para o diretório de projeto e execute o seguinte comando

    ```shell    
    pod install
    ```

4. Se a sua xcodeproj estiver aberta no Xcode, feche-o. No seu diretório do projeto abra o ficheiro de projeto recém-criado que terá a extensão de .xcworkspace. Este é o ficheiro que irá trabalhar a partir de agora em.

## <a name="framework"></a>Framework
É a forma como utilizar a biblioteca criar manualmente a estrutura:

1. Em primeiro lugar, transferir ou clonar a [repositório azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Vá para *azure-storage-ios* -> *Lib* -> *biblioteca de clientes do Storage do Azure*e abra `AZSClient.xcodeproj` no Xcode.
3. Na parte superior esquerda do Xcode, altere o esquema de Active Directory de "Biblioteca de clientes do Storage do Azure" para "Framework".
4. Compilar o projeto (⌘ + B). Esta ação irá criar um `AZSClient.framework` ficheiro no seu ambiente de trabalho.

Em seguida, pode importar o ficheiro de framework na sua aplicação da seguinte forma:

1. Criar um novo projeto ou abrir projeto existente no Xcode.
2. Arrastar e largar o `AZSClient.framework` para o seu navegador de projeto Xcode.
3. Selecione *copiar itens, se for necessário*e clique em *concluir*.
4. Clique no seu projeto no painel de navegação da esquerda e clique em de *geral* separador no topo do editor de projeto.
5. Sob o *estruturas e bibliotecas ligadas* secção, clique no botão Adicionar (+).
6. Na lista de bibliotecas que já são fornecidos, procure `libxml2.2.tbd` e adicioná-la ao seu projeto.

## <a name="import-the-library"></a>Importar a biblioteca 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Se estiver a utilizar o Swift, terá de criar um cabeçalho de bridging e importar < AZSClient/AZSClient.h > não existe:

1. Criar um ficheiro de cabeçalho `Bridging-Header.h`e adicione a instrução de importação acima.
2. Vá para o *definições de criação* separador e procure *cabeçalho de Bridging Objective-C*.
3. Faça duplo clique no campo de *cabeçalho de Bridging Objective-C* e adicione o caminho para o ficheiro de cabeçalho:`ProjectName/Bridging-Header.h`
4. Compilar o projeto (⌘ + B) para verificar que o cabeçalho de bridging foi captado pelo Xcode.
5. Começar a utilizar a biblioteca diretamente no ficheiro qualquer Swift, não é necessário para instruções de importação.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações assíncronas
> [!NOTE]
> Todos os métodos que executam um pedido de serviço são operações assíncronas. Os exemplos de código, irá encontrar que estes métodos tem um processador de conclusão. Código dentro do processador de conclusão será executado **depois** o pedido é concluído. Code depois do processador de conclusão será executado **enquanto** o pedido está a ser efetuado.
> 
> 

## <a name="create-a-container"></a>Criar um contentor
Todos os BLOBs no armazenamento do Azure têm de residir num contentor. O exemplo seguinte mostra como criar um contentor, denominado *newcontainer*, na sua conta de armazenamento se ainda não existir. Ao escolher um nome para o contentor, ser mindful das regras de nomenclatura mencionadas acima.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Pode confirmar que este processo funciona ao observar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) e verificar que *newcontainer* está na lista de contentores para a sua conta de armazenamento.

## <a name="set-container-permissions"></a>Definir permissões do contentor
Permissões de um contentor estão configuradas para **privada** acesso por predefinição. No entanto, os contentores fornecem algumas opções diferentes para acesso do contentor:

* **Privada**: dados blob e contentor podem ser lida por apenas o proprietário da conta.
* **Blob**: dados BLOBs neste contentor podem ser lidos através do pedido anónimo, mas não estão disponíveis dados de contentor. Os clientes não é possível enumerar os blobs no contentor através do pedido anónimo.
* **Contentor**: dados blob e contentor podem ser lidos através do pedido anónimo. Os clientes podem enumerar os blobs no contentor através do pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.

O exemplo seguinte mostra como criar um contentor com **contentor** permissões, o que lhe permitirá o acesso público só de leitura para todos os utilizadores na Internet de acesso:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Tal como mencionado no [conceitos do serviço do Blob](#blob-service-concepts) secção, Blob Storage oferece três tipos diferentes de blobs: blobs de blocos, blobs de acréscimo e blobs de páginas. A biblioteca de iOS do Storage do Azure suporta todos os três tipos de blobs. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

O exemplo seguinte mostra como carregar um blob de blocos de um NSString. Se já existir um blob com o mesmo nome neste contentor, os conteúdos deste blob serão substituídos.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Pode confirmar que este processo funciona ao observar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) e verificar que o contentor, *containerpublic*, contém o blob, *sampleblob*. Neste exemplo, é utilizado um contentor público para também pode verificar se esta aplicação trabalhado acedendo para o URI de blobs:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Para além de carregar um blob de blocos de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um ficheiro local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
O exemplo seguinte mostra como listar todos os blobs num contentor. Quando efetuar esta operação, não mindful dos seguintes parâmetros:     

* **continuationToken** -o representa de token de continuação onde deve começar a operação de listagem. Se não for fornecido nenhum token, este irá listar blobs a partir do início. Pode ser apresentado qualquer número de blobs, do zero até um máximo de conjunto. Mesmo que este método devolve zero resultados, se `results.continuationToken` não é nil, poderão existir mais blobs no serviço que não foi indicados.
* **prefixo** -pode especificar o prefixo a utilizar para a lista de blob. Apenas os blobs que começam com este prefixo serão apresentados.
* **useFlatBlobListing** - tal como mencionado no [nomenclatura e referência de contentores e blobs](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) secção, embora o serviço Blob é um esquema de armazenamento simples, é possível criar uma hierarquia virtual ao nomear blobs com informações de caminho. No entanto, não plana listagem não é atualmente suportada. Esta funcionalidade estará disponível brevemente. Por agora, este valor deve ser **Sim**.
* **blobListingDetails** -pode especificar quais os itens a incluir quando listar os blobs
  * _AZSBlobListingDetailsNone_: listar apenas consolidados blobs e devolve os metadados de blob.
  * _AZSBlobListingDetailsSnapshots_: listar blobs consolidados e instantâneos do blob.
  * _AZSBlobListingDetailsMetadata_: obter metadados do blob para cada blob devolvido na listagem.
  * _AZSBlobListingDetailsUncommittedBlobs_: listar blobs consolidados e não consolidados.
  * _AZSBlobListingDetailsCopy_: incluir copiar propriedades na listagem.
  * _AZSBlobListingDetailsAll_: lista de todos os blobs consolidados disponíveis, não consolidados blobs e instantâneos e devolver todos os Estados de metadados e copie para os blobs.
* **maxResults** -o número máximo de resultados para devolver para esta operação. Utilize -1 para não definir um limite.
* **completionHandler** - o bloco de código para executar com os resultados da operação de listagem.

Neste exemplo, um método de programa auxiliar é utilizado para a chamada de recursivamente a lista de blobs método sempre que é devolvido um token de continuação.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Transferir um blob
O exemplo seguinte mostra como transferir um blob para um objeto de NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Eliminar um blob
O exemplo seguinte mostra como eliminar um blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Eliminar um contentor do blob
O exemplo seguinte mostra como eliminar um contentor.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como utilizar o Blob Storage a partir do iOS, siga estas ligações para saber mais sobre a biblioteca de iOS e o serviço de armazenamento.

* [Biblioteca de clientes do Storage do Azure para iOS](https://github.com/azure/azure-storage-ios)
* [IOS de armazenamento do Azure documentação de referência](http://azure.github.io/azure-storage-ios/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage)

Se tiver dúvidas sobre esta biblioteca, não hesite em publicar a nossa [fórum do MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se tiver sugestões de funcionalidades para o Storage do Azure, publique para [comentários de armazenamento do Azure](https://feedback.azure.com/forums/217298-storage/).

