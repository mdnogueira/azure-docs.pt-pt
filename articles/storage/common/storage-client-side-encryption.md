---
title: "Encriptação do lado do cliente com .NET para o armazenamento do Microsoft Azure | Microsoft Docs"
description: "A biblioteca de clientes de armazenamento do Azure para .NET suporta a encriptação do lado do cliente e a integração com o Cofre de chaves do Azure para segurança máxima para as suas aplicações de armazenamento do Azure."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: becfccca-510a-479e-a798-2044becd9a64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.openlocfilehash: fe8023729bd1294dedd2a4e4723a8be0976731d6
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Encriptação do lado do cliente e o Azure Cofre de chaves para o armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
O [biblioteca de clientes do Storage do Azure para o pacote Nuget do .NET](https://www.nuget.org/packages/WindowsAzure.Storage) suporta a encriptação de dados dentro de aplicações de cliente antes de carregar para o Storage do Azure e a desencriptação de dados durante a transferência para o cliente. A biblioteca também suporta a integração com [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

Para um tutorial passo a passo que orienta-o ao longo do processo de encriptação a blobs com a encriptação do lado do cliente e o Cofre de chaves do Azure, consulte [encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Cofre de chaves do Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Para a encriptação do lado do cliente com o Java, consulte [encriptação do lado do cliente com o Java para armazenamento do Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através de técnica envelope
Os processos de encriptação e desencriptação siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através de técnica envelope
Encriptação através de técnica envelope funciona da seguinte forma:

1. A biblioteca de clientes do storage do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica one time utilização.
2. Dados de utilizador são encriptados utilizando este CEK.
3. O CEK, em seguida, é moldado (encriptado) utilizando a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerida localmente ou armazenada em cofres de chaves do Azure.
   
    A biblioteca de clientes de armazenamento próprio nunca tem acesso a KEK. A biblioteca invoca o algoritmo de chave de encapsulamento de aplicações fornecido pelo Cofre de chaves. Os utilizadores podem optar por utilizar fornecedores personalizados de encapsulamento de aplicações chave/abertura, se assim o desejar.

4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure. A chave encapsulada juntamente com alguns metadados de encriptação adicionais é armazenada como metadados (num blob) ou interpolated com os dados encriptados (fila de mensagens e as entidades da tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através de técnica envelope
Desencriptação através de técnica envelope funciona da seguinte forma:

1. A biblioteca de clientes parte do princípio de que o utilizador está a gerir a chave de encriptação de chaves (KEK) localmente ou cofres de chaves do Azure. O utilizador não precisa de saber a chave específica que foi utilizado para encriptação. Em vez disso, uma resolução de chave que é resolvido identificadores de chaves diferentes para as chaves pode ser configurada e utilizada.
2. A biblioteca de cliente transfere os dados encriptados, juntamente com quaisquer material de encriptação que é armazenado no serviço.
3. A chave de encriptação de conteúdo encapsulada (CEK) é, em seguida, e não encapsulada (desencriptados) utilizando a encriptação de chaves de chaves (KEK). Aqui, a biblioteca de cliente não tem acesso a KEK. Basta invoca personalizado ou o algoritmo de abertura do fornecedor do Cofre de chaves.
4. A chave de encriptação de conteúdo (CEK), em seguida, é utilizada para desencriptar os dados encriptados do utilizador.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação
A biblioteca de clientes de armazenamento utiliza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados de utilizador. Especificamente, [encadeamento de bloco de cifras (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modo com AES. Cada serviço funciona de forma ligeiramente diferente, pelo que, vamos abordar cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca de clientes atualmente suporta encriptação de todo blobs apenas. Especificamente, a encriptação é suportada quando os utilizadores utilizam o **UploadFrom*** métodos ou **OpenWrite** método. Para as transferências, ambos completos e transferências de intervalo são suportadas.

Durante a encriptação, a biblioteca de clientes irá gerar um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes e efetuar a encriptação de envelope dos dados blob utilizando estas informações. O CEK encapsulada e alguns metadados de encriptação adicionais são armazenadas como metadados juntamente com o blob encriptado no serviço do blob.

> [!WARNING]
> Se estiver a editar ou carregar os seus metadados do blob, tem de garantir que estes metadados são preservados. Se carregar novos metadados sem estes metadados, o CEK encapsulada, IV e outros metadados serão perdidos e o conteúdo de blob nunca será recuperável novamente.
> 
> 

Transferência de um blob encriptado envolve a obter o conteúdo da utilização de BLOBs de todo o **DownloadTo***/**BlobReadStream** métodos conveniência. O CEK encapsulada e não encapsulada e utilizado juntamente com o IV (armazenada como metadados do blob neste caso) para devolver os dados desencriptados para os utilizadores.

Transferência de um intervalo de arbitrário (**DownloadRange*** métodos) no blob encriptado envolve ajustar o intervalo fornecido por utilizadores para obter uma pequena quantidade de dados adicionais que podem ser utilizados para desencriptá com êxito o intervalo pedido.

Todos os tipos de BLOBs (blobs de blocos, blobs de páginas e blobs de acréscimo) podem ser encriptados/desencriptados utilizando este esquema.

### <a name="queues"></a>Filas
Uma vez que a fila de mensagens pode ser de qualquer formato, a biblioteca de clientes define um formato personalizado que inclua o Vetor de inicialização (IV) e a chave de encriptação de conteúdo encriptado (CEK) no texto da mensagem.

Durante a encriptação, a biblioteca de cliente gera um IV aleatória de 16 bytes juntamente com um CEK aleatória de bytes de 32 e efetua a encriptação de envelope do texto de mensagem de fila utilizando estas informações. O CEK encapsulada e alguns metadados de encriptação adicionais, em seguida, são adicionados à mensagem de fila encriptados. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante a desencriptação, a chave encapsulada é extraída da mensagem de fila e não encapsulada. O IV também é extraído da mensagem de fila e utilizada juntamente com a chave e não encapsulada para desencriptar os dados da mensagem de fila. Tenha em atenção que os metadados de encriptação são pequeno (em 500 bytes), pelo que enquanto-contam para o limite de 64KB de uma mensagem de fila, o impacto deve ser fácil de gerir.

### <a name="tables"></a>Tabelas
A biblioteca de cliente suporta a encriptação das propriedades de entidade para inserção e substitua operações.

> [!NOTE]
> Intercalação não é atualmente suportada. Uma vez que um subconjunto de propriedades poderá ter sido encriptado anteriormente com uma chave diferente, basta intercalar as propriedades de novo e atualizar os metadados resultará na perda de dados. Intercalar o requer efetuar chamadas de serviço adicional para a entidade pré-existente de leitura do serviço ou utilizar uma nova chave por propriedade, que não são adequadas por motivos de desempenho.
> 
> 

Encriptação de dados de tabela funciona da seguinte forma:  

1. Os utilizadores especificar as propriedades sejam encriptados.
2. A biblioteca de cliente gera um Vetor de inicialização aleatório (IV) de 16 bytes juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de bytes de 32 para cada entidade e efetua a encriptação de envelope nas propriedades individuais sejam encriptados ao efetuar a derivação de uma nova IV por propriedade. A propriedade de encriptação é armazenada como dados binários.
3. O CEK encapsulada e alguns metadados de encriptação adicionais, em seguida, são armazenadas como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia que contém as informações sobre IV, a versão e a chave encapsulada. A propriedade segundo reservada (_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações sobre as propriedades que são encriptados. As informações nesta segunda propriedade (_ClientEncryptionMetadata2) são o próprio encriptados.
4. Devido a estas propriedades reservadas adicionais necessárias para a encriptação, os utilizadores agora podem ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade tem de ser inferior a 1 MB.

Tenha em atenção que as propriedades de cadeia só podem ser encriptadas. Se outros tipos de propriedades estão a ser encriptada, tem de ser convertidas para cadeias. As cadeias de encriptados são armazenadas no serviço como propriedades binárias e estes são convertidos para cadeias depois de desencriptação.

Para as tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Pode fazê-lo especificando a um atributo [EncryptProperty] (para entidades POCO que derivem de TableEntity) ou um resolvedor de encriptação nas opções de pedido. Um resolvedor de encriptação é um delegado que utiliza uma chave de partição, chave de linha e nome de propriedade e devolve um valor boleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada durante a escrita para a transmissão. Também fornece o delegado para a possibilidade de lógica em torno da forma como as propriedades são encriptadas. (Por exemplo, se X, então encriptar propriedade um; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não seja necessário fornecer estas informações ao ler ou consultar entidades.

### <a name="batch-operations"></a>Operações de lote
Em operações de lote, a mesma KEK irá ser utilizada em todas as linhas que operação em lote porque a biblioteca de clientes só permite um objeto de opções (e, por conseguinte, uma política/KEK) por operação em lote. No entanto, a biblioteca de clientes internamente gerará uma nova IV aleatória e CEK aleatório por linha no lote. Os utilizadores também podem optar por encriptar propriedades diferentes para cada operação no lote se definir este comportamento o Resolvedor de encriptação.

### <a name="queries"></a>Consultas
Para executar operações de consulta, tem de especificar uma resolução de chave que é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um fornecedor, a biblioteca de clientes irá gerar um erro. Para qualquer consulta que efetua projecções do lado do servidor, a biblioteca de clientes irá adicionar as propriedades de metadados de encriptação do especial (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por predefinição para as colunas selecionadas.

## <a name="azure-key-vault"></a>Azure Key Vault
O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Ao utilizar o Cofre de chaves do Azure, os utilizadores podem encriptar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSMs). Para obter mais informações, consulte [que é o Cofre de chaves do Azure?](../../key-vault/key-vault-whatis.md).

A biblioteca de clientes de armazenamento utiliza a biblioteca principal do Cofre de chaves para fornecer uma estrutura comum através do Azure para a gestão de chaves. Os utilizadores obtêm também o benefício adicional de utilizar a biblioteca de extensões do Cofre de chaves. A biblioteca de extensões fornece uma funcionalidade útil em torno simple e totalmente integrada Symmetric/RSA local e os fornecedores de chave de nuvem, bem como com agregação e a colocação em cache.

### <a name="interface-and-dependencies"></a>Interface e dependências
Existem três pacotes do Cofre de chaves:

* Microsoft.Azure.KeyVault.Core contém a IKey e IKeyResolver. É um pacote pequeno sem dependências. A biblioteca de clientes de armazenamento para .NET define-o como uma dependência.
* Microsoft.Azure.KeyVault contém o cliente de REST do Cofre de chave.
* Microsoft.Azure.KeyVault.Extensions contém um código de extensão que inclui as implementações de algoritmos criptográficos e um RSAKey e um SymmetricKey. É também depende dos espaços de nomes de núcleo e de KeyVault e fornece uma funcionalidade para definir um resolvedor de agregação (quando os utilizadores querem utilizar vários fornecedores de chaves) e uma resolução de chave de colocação em cache. Apesar da biblioteca de clientes de armazenamento não diretamente dependem deste pacote, se os utilizadores que pretende utilizar o Cofre de chaves do Azure para armazenar as respetivas chaves ou utilizar as extensões do Cofre de chaves para consumir local e na nuvem fornecedores de criptografia, terá este pacote.

O Cofre de chaves foi concebido para chaves mestras de alto valor e limitação limites por Cofre de chaves são concebidos com isto em mente. Quando efetuar a encriptação do lado do cliente com o Cofre de chaves, o modelo preferencial é utilizar chaves simétricas mestre armazenadas como segredos no Cofre de chaves e colocadas em cache localmente. Os utilizadores tem de fazer o seguinte:

1. Criar um segredo offline e carregue-o no Cofre de chaves.
2. Utilize o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para a encriptação e em cache localmente estas informações. Utilizar CachingKeyResolver para colocar em cache; os utilizadores não são esperados para implementar os seus próprios de lógica de colocação em cache.
3. Utilize a resolução de colocação em cache como uma entrada ao criar a política de encriptação.

Podem encontrar mais informações sobre a utilização do Cofre de chaves no [exemplos de código de encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Melhores práticas
Suporte de encriptação está disponível apenas na biblioteca de cliente do armazenamento para .NET. Windows Phone e Windows Runtime não suporta atualmente encriptação.

> [!IMPORTANT]
> Tenha em atenção estes pontos importantes quando utilizar a encriptação do lado do cliente:
> 
> * Ao ler a partir de ou escrever um blob encriptado, utilize os comandos de carregamento de blob todo e comandos de transferência de blob de intervalo/inteiro. Evitar a escrita para um blob encriptado utilizando o protocolo operações como colocar bloco, colocar a lista de bloqueios, escrever páginas, desmarque páginas ou acrescentar bloco; caso contrário, pode danificar o blob encriptado e torná-lo ilegível.
> * Para as tabelas, existe uma restrição semelhante. Tenha o cuidado de não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.
> * Se definir os metadados no blob encriptado, pode substituir os metadados relacionados com a encriptação necessário para a desencriptação, uma vez que a definição de metadados não é semiaditivas. Isto também se aplica aos instantâneos; Evite especificar metadados durante a criação de um instantâneo de um blob encriptado. Se os metadados tem de ser definido, lembre-se de que chamar a **FetchAttributes** método primeiro para obter os metadados de encriptação atual e evitar escritas em simultâneo, enquanto estiver a ser definido metadados.
> * Ativar o **RequireEncryption** propriedade nas opções de pedido predefinido para os utilizadores que deverão funcionar apenas com dados encriptados. Consulte abaixo para obter mais informações.
> 
> 

## <a name="client-api--interface"></a>Cliente API / da Interface
Ao criar um objeto de EncryptionPolicy, os utilizadores podem fornecer apenas uma chave (implementar IKey), apenas uma resolução (implementar IKeyResolver) ou ambos. IKey é o tipo de chave básico que é identificado com um identificador de chave e que fornece a lógica de encapsulamento de aplicações/abertura. IKeyResolver é utilizado para resolver uma chave durante o processo de desencriptação. Define um método de ResolveKey que devolva uma IKey fornecido um identificador de chave. Isto proporciona aos utilizadores a capacidade de escolher entre várias chaves que são geridas em várias localizações.

* Para a encriptação, a chave é utilizada sempre e a ausência de uma chave resultará num erro.
* Para desencriptação:
  * A resolução de chave é invocada se especificada para obter a chave. Se o resolvedor for especificado, mas não tem um mapeamento para o identificador da chave, é emitido um erro.
  * Se o resolvedor não for especificado, mas foi especificada uma chave, a chave é utilizada se o respetivo identificador corresponde ao identificador de chave necessário. Se o identificador não corresponde, será emitido um erro.

Os exemplos de código neste artigo demonstram definir uma política de encriptação e trabalhar com dados encriptados, mas não demonstrar como trabalhar com o Cofre de chaves do Azure. O [amostras de encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) no GitHub demonstrar um cenário de ponto a ponto mais detalhado para blobs, filas e tabelas, along com a integração do Cofre de chaves.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Os utilizadores, opcionalmente, podem ativar um modo de funcionamento em que todos os carregamentos e transferências tem de estar encriptadas. Neste modo, as tentativas para carregar dados sem uma política de encriptação ou transferir os dados que não estão encriptados no serviço irão falhar no cliente. O **RequireEncryption** propriedade do objeto de opções de pedido controla este comportamento. Se a aplicação será encriptar todos os objetos armazenados no Storage do Azure, em seguida, pode definir o **RequireEncryption** propriedade nas opções predefinidas de pedido para o objeto de cliente do serviço. Por exemplo, definir **CloudBlobClient.DefaultRequestOptions.RequireEncryption** para **verdadeiro** para exigir a encriptação para todas as operações executadas através desse objeto de cliente do blob.


### <a name="blob-service-encryption"></a>Encriptação do serviço blob
Criar um **BlobEncryptionPolicy** objeto e defina-o nas opções do pedido (por API ou a um nível de cliente utilizando **DefaultRequestOptions**). Tudo o resto será processado pela biblioteca de clientes internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Encriptação do serviço fila
Criar um **QueueEncryptionPolicy** objeto e defina-o nas opções do pedido (por API ou a um nível de cliente utilizando **DefaultRequestOptions**). Tudo o resto será processado pela biblioteca de clientes internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Encriptação do serviço tabela
Além de criar uma política de encriptação e defini-la nas opções de pedido, tem de especificar se um **EncryptionResolver** no **TableRequestOptions**, ou defina o atributo [EncryptProperty] na entidade.

#### <a name="using-the-resolver"></a>Utilizar o resolvedor

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Utilizar atributos
Tal como mencionado acima, se a entidade implementa TableEntity, em seguida, as propriedades podem ser decorado com o atributo [EncryptProperty] em vez de especificar o **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Encriptação e de desempenho
Tenha em atenção que encriptar os resultados de dados de armazenamento em overhead de desempenho adicionais. A chave de conteúdo e IV tem de ser gerado, o próprio conteúdo tem de estar encriptado e adicional meta-data tem de ser formatado e carregado. Esta sobrecarga irá variar consoante a quantidade de dados que está a ser encriptados. Recomendamos que os clientes sempre testar as aplicações de desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos seguintes
* [Tutorial: Encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Cofre de chaves do Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Transferir o [biblioteca de clientes do Storage do Azure para o pacote NuGet do .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
* Transferir o NuGet de Cofre de chaves do Azure [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [cliente](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/), e [extensões](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) pacotes  
* Visite o [documentação do Cofre de chaves do Azure](../../key-vault/key-vault-whatis.md)