---
title: "Configurar uma cadeia de ligação para o Storage do Azure | Microsoft Docs"
description: "Configure uma cadeia de ligação para uma conta de armazenamento do Azure. Uma cadeia de ligação contém informações necessárias para autenticar o acesso a uma conta de armazenamento a partir da sua aplicação no tempo de execução."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: tamram
ms.openlocfilehash: 192799cb44dc9a56c65a6414c1267c506252fe29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-storage-connection-strings"></a>Configurar Cadeias de Ligação do Storage do Azure

Uma cadeia de ligação inclui as informações de autenticação necessárias para a sua aplicação para aceder a dados numa conta do Storage do Azure no tempo de execução. Pode configurar cadeias de ligação para:

* Ligar para o emulador de armazenamento do Azure.
* Aceder a uma conta de armazenamento no Azure.
* Aceder a recursos especificados no Azure através de uma assinatura de acesso partilhado (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Armazenar a cadeia de ligação
A aplicação tem de aceder a cadeia de ligação no tempo de execução para autenticar pedidos efetuados ao Storage do Azure. Tem várias opções para armazenar a cadeia de ligação:

* Uma aplicação em execução no ambiente de trabalho ou num dispositivo pode armazenar a cadeia de ligação de um **App. config** ou **Web. config** ficheiro. Adicione a cadeia de ligação para o **AppSettings** secção estes ficheiros.
* Uma aplicação em execução num serviço em nuvem do Azure pode armazenar a cadeia de ligação no [ficheiro de esquema (. cscfg) de configuração do serviço do Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicione a cadeia de ligação para o **ConfigurationSettings** secção do ficheiro de configuração do serviço.
* Pode utilizar a cadeia de ligação diretamente no seu código. No entanto, recomendamos que armazene a cadeia de ligação num ficheiro de configuração na maioria dos cenários.

Armazenar a cadeia de ligação num ficheiro de configuração facilita a atualizar a cadeia de ligação para alternar entre o emulador do storage e uma conta de armazenamento do Azure na nuvem. Só tem de editar a cadeia de ligação para apontar para o ambiente de destino.

Pode utilizar o [do Configuration Manager do Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para aceder a cadeia de ligação no runtime, onde a aplicação está em execução.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Criar uma cadeia de ligação para o emulador de armazenamento
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para mais informações sobre o emulador do storage, consulte [utilizar o emulador de armazenamento do Azure para desenvolvimento e testes](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Criar uma cadeia de ligação para uma conta de armazenamento do Azure
Para criar uma cadeia de ligação para a sua conta de armazenamento do Azure, utilize o seguinte formato. Indique se pretende ligar à conta de armazenamento através de HTTPS (recomendado) ou HTTP, substitua `myAccountName` com o nome da sua conta de armazenamento e substituir `myAccountKey` com a chave de acesso da conta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por exemplo, a cadeia de ligação poderá ter um aspeto semelhante a:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Embora o Storage do Azure suporta HTTP e HTTPS numa cadeia de ligação, *HTTPS é vivamente recomendável*.

> [!TIP]
> Pode encontrar cadeias de ligação da sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Navegue para **definições** > **chaves de acesso** no painel de menu da sua conta de armazenamento para ver cadeias de ligação para ambas as chaves de acesso primária e secundária.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de ligação utilizando uma assinatura de acesso partilhado
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Criar uma cadeia de ligação para um ponto final de armazenamento explícita
Pode especificar pontos finais de serviço explícita na sua cadeia de ligação em vez de utilizar os pontos finais predefinidos. Para criar uma cadeia de ligação que especifica um ponto de final explícito, especifique o ponto final de serviço completo para cada serviço, incluindo a especificação de protocolo (HTTPS (recomendado) ou HTTP), no seguinte formato:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Um cenário em que poderá pretender especificar um ponto de final explícito é quando tiver mapeado o ponto de final do Blob storage para um [domínio personalizado](../blobs/storage-custom-domain-name.md). Nesse caso, pode especificar o ponto final personalizado para o Blob storage na sua cadeia de ligação. Opcionalmente, pode especificar os pontos finais predefinidos para os outros serviços, se a sua aplicação utiliza-los.

Eis um exemplo de uma cadeia de ligação que especifica um ponto de final explícito para o serviço Blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Este exemplo Especifica pontos finais explícitos para todos os serviços, incluindo um domínio personalizado para o serviço Blob:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Os valores de ponto final de uma cadeia de ligação são utilizados para construir o pedido de URI para os serviços de armazenamento e, que estipulam a forma dos URIs são devolvidos para o seu código.

Se tiver mapeado um ponto final de armazenamento para um domínio personalizado e omitir esse ponto final de uma cadeia de ligação, em seguida, não poderá utilizar essa cadeia de ligação para aceder a dados em que o serviço a partir do código.

> [!IMPORTANT]
> Os valores de ponto final de serviço no seu cadeias de ligação tem de ser formados URIs, incluindo `https://` (recomendado) ou `http://`. Porque o armazenamento do Azure ainda não suporta HTTPS para domínios personalizados, *tem* especificar `http://` para qualquer ponto final URI que aponta para um domínio personalizado.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de ligação com um sufixo de ponto final
Para criar uma cadeia de ligação para um serviço de armazenamento em regiões ou instâncias com sufixos de outro ponto final, tal como para o Azure China ou Azure Government, utilize o seguinte formato de cadeia de ligação. Indique se pretende ligar à conta de armazenamento através de HTTPS (recomendado) ou HTTP, substitua `myAccountName` com o nome da conta de armazenamento, substitua `myAccountKey` com a sua chave de acesso da conta e substitua `mySuffix` com o sufixo do URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Eis uma cadeia de ligação de exemplo para serviços de armazenamento no Azure China:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisar uma cadeia de ligação
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passos seguintes
* [Utilizar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
* [Exploradores de armazenamento do Azure](storage-explorers.md)
* [Utilizar assinaturas de acesso partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)

