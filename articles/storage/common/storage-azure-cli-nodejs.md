---
title: Utilizar a CLI do Azure 1.0 com o Storage do Azure | Microsoft Docs
description: "Saiba como utilizar a Interface de linha de comandos do Azure (CLI do Azure) 1.0 com o Storage do Azure para criar e gerir contas de armazenamento e de trabalhar com blobs do Azure e os ficheiros. A CLI do Azure é uma ferramenta de plataforma"
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.openlocfilehash: 55b857bd5b3d0c32553a60ff0649aeeb721cd6da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Utilizar a CLI do Azure 1.0 com o Storage do Azure

## <a name="overview"></a>Descrição geral

A CLI do Azure fornece um conjunto de código aberto, comandos de várias plataformas para trabalhar com a plataforma do Azure. Fornece muito a mesma funcionalidade que foi encontrada no [portal do Azure](https://portal.azure.com) funcionalidade de acesso a dados, bem como avançados.

Neste guia, iremos irá explorar como utilizar [Interface de linha de comandos do Azure (CLI do Azure)](../../cli-install-nodejs.md) para efetuar uma variedade de tarefas de desenvolvimento e de administração com o Storage do Azure. Recomendamos que pode transfere e instalar ou atualizar para a CLI do Azure mais recente antes de utilizar este guia.

Este guia pressupõe que compreende os conceitos básicos do Storage do Azure. O guia de fornece um número de scripts para demonstrar a utilização da CLI do Azure com o Storage do Azure. Lembre-se de que as variáveis de script com base na sua configuração antes de executar cada script de atualização.

> [!NOTE]
> O guia fornece os exemplos de comandos e scripts da CLI do Azure para contas de armazenamento clássicas. Consulte [utilizando a CLI do Azure para Mac, Linux e Windows com a gestão de recursos do Azure](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para comandos da CLI do Azure para contas de armazenamento do Resource Manager.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Introdução ao Storage do Azure e a CLI do Azure em 5 minutos
Este guia utiliza Ubuntu para obter exemplos, mas outras plataformas de SO devem efetuar da mesma forma.

**Novo no Azure:** obter uma subscrição do Microsoft Azure e uma conta Microsoft associada essa subscrição. Para obter informações sobre as opções de compra do Azure, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [opções de compra](https://azure.microsoft.com/pricing/purchase-options/), e [ofertas de membros](https://azure.microsoft.com/pricing/member-offers/) (para os membros de MSDN, Microsoft Partner Network, BizSpark e outros programas Microsoft).

Consulte [atribuir funções de administrador no Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) para obter mais informações sobre as subscrições do Azure.

**Depois de criar uma conta e subscrição do Microsoft Azure:**

1. Transferir e instalar a CLI do Azure, siga as instruções descritas no [instalar a CLI do Azure](../../cli-install-nodejs.md).
2. Depois de instalada a CLI do Azure, poderá utilizar o comando do azure da sua interface de linha de comandos (Bash, Terminal, linha de comandos) para aceder aos comandos da CLI do Azure. Tipo de _azure_ comando e deverá ver o seguinte resultado.

    ![Saída de comandos do Azure](./media/storage-azure-cli/azure_command.png)   
3. Na interface de linha de comandos, escreva `azure storage` a lista de todos os comandos de armazenamento do azure e obter uma primeira impressão das funcionalidades da CLI do Azure fornece. Pode escrever o nome de comando com **-h** parâmetro (por exemplo, `azure storage share create -h`) para ver detalhes da sintaxe de comando.
4. Agora, iremos irá dar-lhe um script simple que mostra comandos básicos da CLI do Azure para aceder ao armazenamento do Azure. O script primeiro irá pedir-lhe definir duas variáveis para a sua conta de armazenamento e a chave. Em seguida, o script irá criar um novo contentor nesta nova conta de armazenamento e carregar um ficheiro de imagem existente (blob) para esse contentor. Depois do script apresenta uma lista de todos os blobs no contentor, transferirá o ficheiro de imagem para o diretório de destino que exista no computador local.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. No computador local, abra o editor de texto preferido (vim por exemplo). Escreva o script acima no seu editor de texto.
6. Agora, tem de atualizar as variáveis de script com base nas suas definições de configuração.

   * **< Storage_account_name >** utilizar o nome fornecido no script ou introduza um novo nome para a sua conta de armazenamento. **Importante:** o nome da conta de armazenamento tem de ser exclusivo no Azure. Tem de ser em minúsculas, demasiado!
   * **< Storage_account_key >** a chave de acesso da conta de armazenamento.
   * **< Container_name >** utilizar o nome fornecido no script ou introduza um novo nome para o contentor.
   * **< Image_to_upload >** introduza um caminho para uma imagem no seu computador local, como: "~ / images/HelloWorld.png".
   * **< Destination_folder >** introduza um caminho para um diretório local para armazenar os ficheiros transferidos do Storage do Azure, tal como: "~/downloadImages".
7. Depois de ter atualizado as variáveis necessárias na vim, prima combinações de teclas `ESC`, `:`, `wq!` ao guardar o script.
8. Para executar este script, escreva simplesmente o nome de ficheiro de script na consola do bash. Depois deste script é executado, é necessário ter uma pasta de destino local que inclua o ficheiro de imagem transferido. A seguinte captura de ecrã mostra um exemplo de saída:

Depois do script é executado, é necessário ter uma pasta de destino local que inclua o ficheiro de imagem transferido.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gerir contas de armazenamento com a CLI do Azure
### <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure
Embora a maioria dos comandos armazenamento irá funcionar sem uma subscrição do Azure, recomendamos que para ligar à sua subscrição a partir da CLI do Azure. Para configurar a CLI do Azure para trabalhar com a sua subscrição, siga os passos em [ligar a uma subscrição do Azure a partir da CLI do Azure](../../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Para utilizar o armazenamento do Azure, terá de uma conta de armazenamento. Pode criar uma nova conta de armazenamento do Azure após ter configurado o computador para ligar à sua subscrição.

```azurecli
azure storage account create <account_name>
```

O nome da sua conta de armazenamento tem de ter entre 3 e 24 carateres de comprimento e utilizar apenas letras minúsculas e números.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Defina uma conta de armazenamento do Azure predefinida em variáveis de ambiente
Pode ter várias contas de armazenamento na sua subscrição. Pode escolher um deles e defini-lo nas variáveis de ambiente para todos os comandos de armazenamento na mesma sessão. Isto permite-lhe executar os comandos de armazenamento da CLI do Azure sem especificar a conta do storage e chave explicitamente.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Outra forma de definir uma conta do storage predefinida está a utilizar cadeia de ligação. Firstly obter a cadeia de ligação pelo comando:

```azurecli
azure storage account connectionstring show <account_name>
```

Em seguida, copie a cadeia de ligação de saída e defina-o à variável de ambiente:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Criar e gerir os blobs
Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, tais como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Esta secção assume que já estiver familiarizado com os conceitos de armazenamento de Blobs do Azure. Para obter informações detalhadas, consulte [introdução ao Blob storage do Azure através do .NET](../blobs/storage-dotnet-how-to-use-blobs.md) e [conceitos do serviço Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Criar um contentor
Todos os BLOBs no armazenamento do Azure tem de ser um contentor. Pode criar um contentor privada utilizando o `azure storage container create` comando:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Existem três níveis de acesso de leitura anónimo: **desativar**, **Blob**, e **contentor**. Para impedir o acesso anónimo para blobs, defina o parâmetro de permissão para **desativar**. Por predefinição, o novo contentor é privado e pode ser acedido apenas por proprietário da conta. Para permitir anónimo o acesso de leitura público para recursos do blob, mas não para metadados do contentor ou à lista de blobs no contentor, defina o parâmetro de permissão para **Blob**. Para permitir acesso de leitura completa público para recursos, os metadados do contentor e a lista de blobs no contentor de BLOBs, defina o parâmetro de permissão **contentor**. Para obter mais informações, consulte [gerir o acesso de leitura anónimo a contentores e blobs](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O Blob Storage do Azure suporta blobs de blocos e blobs de páginas. Para obter mais informações, consulte [compreender os Blobs de blocos, os Blobs de acréscimo e Blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar os blobs no contentor, pode utilizar o `azure storage blob upload`. Por predefinição, este comando carregamentos de ficheiros locais para um blob de bloco. Para especificar o tipo para o blob, pode utilizar o `--blobtype` parâmetro.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Transferir blobs a partir de um contentor
O exemplo seguinte demonstra como transferir blobs a partir de um contentor.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Blobs de cópia
Pode copiar blobs dentro ou entre contas de armazenamento e regiões de forma assíncrona.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outra. Neste exemplo, vamos criar um contentor onde blobs publicamente, são anonimamente acessível.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Este exemplo efetua uma cópia assíncrona. Pode monitorizar o estado de cada operação de cópia, executando o `azure storage blob copy show` operação.

Tenha em atenção que o URL de origem fornecido para a operação de cópia tem de estar acessível publicamente ou incluir um token SAS (assinatura de acesso partilhado).

### <a name="delete-a-blob"></a>Eliminar um blob
Para eliminar um blob, utilize o comando abaixo:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Criar e gerir partilhas de ficheiros
Ficheiros do Azure oferece um armazenamento partilhado para aplicações utilizando o protocolo SMB padrão. Máquinas virtuais do Microsoft Azure e serviços em nuvem, bem como as aplicações no local, podem partilhar os dados de ficheiro através de partilhas montadas. Pode gerir partilhas de ficheiros e dados de ficheiros através da CLI do Azure. Para obter mais informações sobre ficheiros do Azure, consulte [introdução ao Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Uma partilha de ficheiros do Azure é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha de ficheiros. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até os limites de capacidade da conta de armazenamento. O exemplo seguinte cria uma partilha de ficheiros com o nome **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica opcional para uma partilha de ficheiros do Azure. O exemplo seguinte cria um diretório com o nome **myDir** na partilha de ficheiros.

```azurecli
azure storage directory create myshare myDir
```

Tenha em atenção que o caminho de diretório pode incluir vários níveis, *por exemplo,*, **um / b**. No entanto, tem de se certificar de que todos os diretórios de principal existem. Por exemplo, para o caminho **um / b**, tem de criar o diretório **um** em primeiro lugar, em seguida, criar o diretório **b**.

### <a name="upload-a-local-file-to-directory"></a>Carregar um ficheiro local para o diretório
O exemplo seguinte carrega um ficheiro de **~/temp/samplefile.txt** para o **myDir** diretório. Edite o caminho do ficheiro para que aponta para um ficheiro válido no seu computador local:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Tenha em atenção que um ficheiro na partilha pode ser até 1 TB de tamanho.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Lista os ficheiros na partilha de raiz ou diretório
Pode listar os ficheiros e subdiretórios numa raiz de partilha ou a um diretório com o seguinte comando:

```azurecli
azure storage file list myshare myDir
```

Tenha em atenção que o nome do diretório é opcional para a operação de listagem. Se for omitido, o comando lista os conteúdos do diretório de raiz da partilha.

### <a name="copy-files"></a>Copiar ficheiros
A partir da versão 0.9.8 da CLI do Azure, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Abaixo, demonstramos como realizar estas operações de cópia utilizando comandos da CLI. Copiar um ficheiro para o novo diretório:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Para copiar um blob para um diretório do ficheiro:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Passos Seguintes

Pode encontrar a referência de comandos da CLI do Azure 1.0 para trabalhar com recursos de armazenamento aqui:

* [Comandos da CLI do Azure no modo Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Comandos da CLI do Azure no modo de gestão de serviço do Azure](../../cli-install-nodejs.md)

Também poderá pretender experimentar o [Azure CLI 2.0](../storage-azure-cli.md), nosso CLI de próxima geração escritas no Python, para utilização com o modelo de implementação Resource Manager.
