---
title: "Encriptação do lado do cliente com o Python de armazenamento do Microsoft Azure | Microsoft Docs"
description: "A biblioteca de clientes de armazenamento do Azure para Python suporta a encriptação do lado do cliente para segurança máxima para as suas aplicações de armazenamento do Azure."
services: storage
documentationcenter: python
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: f9bf7981-9948-4f83-8931-b15679a09b8a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.openlocfilehash: bf6696cfdfe9fc18dd2f000162a4e787a7ca6e21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Encriptação do lado do cliente com o Python de armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
O [biblioteca de clientes do Storage do Azure para Python](https://pypi.python.org/pypi/azure-storage) suporta a encriptação de dados dentro de aplicações de cliente antes de carregar para o Storage do Azure e a desencriptação de dados durante a transferência para o cliente.

> [!NOTE]
> Biblioteca Python de armazenamento do Azure está em pré-visualização.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através de técnica envelope
Os processos de encriptação e desencriptação siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através de técnica envelope
Encriptação através de técnica envelope funciona da seguinte forma:

1. A biblioteca de clientes do storage do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica one time utilização.
2. Dados de utilizador são encriptados utilizando este CEK.
3. O CEK, em seguida, é moldado (encriptado) utilizando a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, que é gerida localmente.
   A biblioteca de clientes de armazenamento próprio nunca tem acesso a KEK. A biblioteca invoca o algoritmo de chave de encapsulamento de aplicações fornecido pela KEK. Os utilizadores podem optar por utilizar fornecedores personalizados de encapsulamento de aplicações chave/abertura, se assim o desejar.
4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure. A chave encapsulada juntamente com alguns metadados de encriptação adicionais é armazenada como metadados (num blob) ou interpolated com os dados encriptados (fila de mensagens e as entidades da tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através de técnica envelope
Desencriptação através de técnica envelope funciona da seguinte forma:

1. A biblioteca de clientes parte do princípio de que o utilizador está a gerir a chave de encriptação de chaves (KEK) localmente. O utilizador não precisa de saber a chave específica que foi utilizado para encriptação. Em vez disso, uma resolução de chave, o que é resolvido diferentes identificadores de chaves para chaves, pode configurar e utilizar.
2. A biblioteca de cliente transfere os dados encriptados, juntamente com quaisquer material de encriptação que é armazenado no serviço.
3. A chave de encriptação de conteúdo encapsulada (CEK) é, em seguida, e não encapsulada (desencriptados) utilizando a encriptação de chaves de chaves (KEK). Aqui, a biblioteca de cliente não tem acesso a KEK. Basta ele invoca o algoritmo de abertura o fornecedor personalizado.
4. A chave de encriptação de conteúdo (CEK), em seguida, é utilizada para desencriptar os dados encriptados do utilizador.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação
A biblioteca de clientes de armazenamento utiliza [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados de utilizador. Especificamente, [encadeamento de bloco de cifras (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modo com AES. Cada serviço funciona de forma ligeiramente diferente, pelo que, vamos abordar cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca de clientes atualmente suporta encriptação de todo blobs apenas. Especificamente, a encriptação é suportada quando os utilizadores utilizam o **criar*** métodos. Para as transferências, ambos completos e transferências de intervalo são suportadas e parallelization de carregamento e transferência está disponível.

Durante a encriptação, a biblioteca de clientes irá gerar um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes e efetuar a encriptação de envelope dos dados blob utilizando estas informações. O CEK encapsulada e alguns metadados de encriptação adicionais são armazenadas como metadados juntamente com o blob encriptado no serviço do blob.

> [!WARNING]
> Se estiver a editar ou carregar os seus metadados do blob, tem de garantir que estes metadados são preservados. Se carregar novos metadados sem estes metadados, o CEK encapsulada, IV e outros metadados serão perdidos e o conteúdo de blob nunca será recuperável novamente.
> 
> 

Transferência de um blob encriptado envolve a obter o conteúdo da utilização de BLOBs de todo o **obter*** métodos conveniência. O CEK encapsulada e não encapsulada e utilizado juntamente com o IV (armazenada como metadados do blob neste caso) para devolver os dados desencriptados para os utilizadores.

Transferir um intervalo de arbitrário (**obter*** transmitido métodos com parâmetros de intervalo) no blob encriptado envolve ajustar o intervalo fornecido por utilizadores para obter uma pequena quantidade de dados adicionais que podem ser utilizadas para com êxito desencriptar o intervalo pedido.

Os blobs de blocos e blobs de páginas só podem ser encriptados/desencriptar a utilização deste esquema. Não são atualmente suportadas para encriptação de blobs de acréscimo.

### <a name="queues"></a>Filas
Uma vez que a fila de mensagens pode ser de qualquer formato, a biblioteca de clientes define um formato personalizado que inclua o Vetor de inicialização (IV) e a chave de encriptação de conteúdo encriptado (CEK) no texto da mensagem.

Durante a encriptação, a biblioteca de cliente gera um IV aleatória de 16 bytes juntamente com um CEK aleatória de bytes de 32 e efetua a encriptação de envelope do texto de mensagem de fila utilizando estas informações. O CEK encapsulada e alguns metadados de encriptação adicionais, em seguida, são adicionados à mensagem de fila encriptados. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
3. O CEK encapsulada e alguns metadados de encriptação adicionais, em seguida, são armazenadas como duas propriedades reservadas adicionais. O primeiro reservado propriedade (\_ClientEncryptionMetadata1) é uma propriedade de cadeia que contém as informações sobre IV, a versão e a chave encapsulada. O segundo reservado propriedade (\_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações sobre as propriedades que são encriptados. As informações nesta segunda propriedade (\_ClientEncryptionMetadata2) é o próprio encriptados.
4. Devido a estas propriedades reservadas adicionais necessárias para a encriptação, os utilizadores agora podem ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade tem de ser inferior a 1MB.
   
   Tenha em atenção que as propriedades de cadeia só podem ser encriptadas. Se outros tipos de propriedades estão a ser encriptada, tem de ser convertidas para cadeias. As cadeias de encriptados são armazenadas no serviço como propriedades binárias e estes são convertidos para cadeias (cadeias não processadas, não EntityProperties com tipo EdmType.STRING) depois de desencriptação.
   
   Para as tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isto pode ser feito armazenando o estas propriedades em objetos de TableEntity com o conjunto de tipo para EdmType.STRING e encriptar definido como VERDADEIRO ou definir o encryption_resolver_function no objeto tableservice. Um resolvedor de encriptação é uma função que utiliza uma chave de partição, chave de linha e nome de propriedade e devolve um valor boleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada durante a escrita para a transmissão. Também fornece o delegado para a possibilidade de lógica em torno da forma como as propriedades são encriptadas. (Por exemplo, se X, então encriptar propriedade um; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não seja necessário fornecer estas informações ao ler ou consultar entidades.

### <a name="batch-operations"></a>Operações de lote
Uma política de encriptação aplica-se a todas as linhas no batch. A biblioteca de clientes internamente irá gerar um novo IV aleatória e CEK aleatório por linha no batch. Os utilizadores também podem optar por encriptar propriedades diferentes para cada operação no lote se definir este comportamento o Resolvedor de encriptação.
Se um lote é criado como um Gestor de contexto através do método de batch() tableservice, política de encriptação do tableservice será aplicada automaticamente para o batch. Se um lote for criado explicitamente ao chamar o construtor, a política de encriptação tem de ser transmitida como um parâmetro e unmodified à esquerda para a duração do lote.
Tenha em atenção que as entidades são encriptadas conforme estes forem inseridos em batch através da política de encriptação do batch (entidades não são encriptadas no momento da consolidar o lote através da política de encriptação do tableservice).

### <a name="queries"></a>Consultas
Para executar operações de consulta, tem de especificar uma resolução de chave que é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um fornecedor, a biblioteca de clientes irá gerar um erro. Para qualquer consulta que efetua projeções de lado do servidor, a biblioteca de clientes irá adicionar as propriedades de metadados de encriptação do especial (\_ClientEncryptionMetadata1 e \_ClientEncryptionMetadata2) por predefinição para as colunas selecionadas.

> [!IMPORTANT]
> Tenha em atenção estes pontos importantes quando utilizar a encriptação do lado do cliente:
> 
> * Ao ler a partir de ou escrever um blob encriptado, utilize os comandos de carregamento de blob todo e comandos de transferência de blob de intervalo/inteiro. Evitar a escrita para um blob encriptado com operações de protocolo, tais como colocar bloco, colocar a lista de bloqueios, escrever páginas ou desmarque páginas; caso contrário, pode danificar o blob encriptado e torná-lo ilegível.
> * Para as tabelas, existe uma restrição semelhante. Tenha o cuidado de não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.
> * Se definir os metadados no blob encriptado, pode substituir os metadados relacionados com a encriptação necessário para a desencriptação, uma vez que a definição de metadados não é semiaditivas. Isto também se aplica aos instantâneos; Evite especificar metadados durante a criação de um instantâneo de um blob encriptado. Se os metadados tem de ser definido, lembre-se de que chamar a **get_blob_metadata** método primeiro para obter os metadados de encriptação atual e evitar escritas em simultâneo, enquanto estiver a ser definido metadados.
> * Ativar o **require_encryption** sinalizador no objeto de serviço para os utilizadores que deverão funcionar apenas com os dados encriptados. Consulte abaixo para obter mais informações.
> 
> 

A biblioteca de clientes de armazenamento espera que a KEK fornecida e resolução de chave para implementar a interface seguinte. [O Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) suporte para a gestão de Python KEK está pendente e irá ser integrada nesta biblioteca quando tiver terminado.

## <a name="client-api--interface"></a>Cliente API / da Interface
Depois de ter sido criado um objeto de serviço de armazenamento (ou seja, blockblobservice), o utilizador poderá atribuir valores para os campos que compõem uma política de encriptação: key_encryption_key, key_resolver_function e require_encryption. Os utilizadores podem fornecer apenas KEK, apenas uma resolução, ou ambos. key_encryption_key é o tipo de chave básico que é identificado com um identificador de chave e que fornece a lógica de encapsulamento de aplicações/abertura. key_resolver_function é utilizado para resolver uma chave durante o processo de desencriptação. Devolve um KEK válido fornecido um identificador de chave. Isto proporciona aos utilizadores a capacidade de escolher entre várias chaves que são geridas em várias localizações.

A KEK tem de implementar os seguintes métodos para encriptar os dados com êxito:

* wrap_key(cek): encapsula num wrapper CEK especificado utilizando um algoritmo da preferência do utilizador (bytes). Devolve a chave encapsulada.
* get_key_wrap_algorithm(): devolve o algoritmo utilizado para moldar as chaves.
* get_kid(): devolve o id de chave de cadeia para este KEK.
  A KEK tem de implementar os seguintes métodos para desencriptar com êxito os dados:
* unwrap_key (cek, algoritmo): devolve o formulário e não encapsulado do CEK especificado utilizando o algoritmo especificado de cadeia.
* get_kid(): devolve um id de chave de cadeia para este KEK.

A resolução de chave tem de implementar, pelo menos, um método que, recebe um id de chave, devolve a KEK correspondente implementar a interface acima. Apenas este método é atribuído à propriedade key_resolver_function no objeto de serviço.

* Para a encriptação, a chave é utilizada sempre e a ausência de uma chave resultará num erro.
* Para desencriptação:
  
  * A resolução de chave é invocada se especificada para obter a chave. Se o resolvedor for especificado, mas não tem um mapeamento para o identificador da chave, é emitido um erro.
  * Se o resolvedor não for especificado, mas foi especificada uma chave, a chave é utilizada se o respetivo identificador corresponde ao identificador de chave necessário. Se o identificador não corresponde, será emitido um erro.
    
    Os exemplos de encriptação no azure.storage.samples <fix URL>demonstrar um cenário de ponto a ponto mais detalhado para tabelas, filas e blobs.
      As implementações de exemplo da KEK e resolução de chave são fornecidas nos ficheiros de exemplo como KeyWrapper e KeyResolver respetivamente.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Os utilizadores, opcionalmente, podem ativar um modo de funcionamento em que todos os carregamentos e transferências tem de estar encriptadas. Neste modo, as tentativas para carregar dados sem uma política de encriptação ou transferir os dados que não estão encriptados no serviço irão falhar no cliente. O **require_encryption** sinalizador no objeto de serviço controla este comportamento.

### <a name="blob-service-encryption"></a>Encriptação do serviço blob
Defina a encriptação de campos da política no objeto blockblobservice. Tudo o resto será processado pela biblioteca de clientes internamente.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Encriptação do serviço fila
Defina a encriptação de campos da política no objeto queueservice. Tudo o resto será processado pela biblioteca de clientes internamente.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Encriptação do serviço tabela
Além de criar uma política de encriptação e defini-la nas opções de pedido, tem de especificar se um **encryption_resolver_function** no **tableservice**, ou defina o atributo de encriptar no EntityProperty.

### <a name="using-the-resolver"></a>Utilizar o resolvedor

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.
def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False

# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Utilizar atributos
Tal como mencionado acima, uma propriedade pode estar marcada para a encriptação ao armazenar num objeto de EntityProperty e definir o campo de encriptar.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Encriptação e de desempenho
Tenha em atenção que encriptar os resultados de dados de armazenamento em overhead de desempenho adicionais. A chave de conteúdo e IV tem de ser gerado, o próprio conteúdo tem de estar encriptado e metadados adicionais têm de ser formatado e carregados. Esta sobrecarga irá variar consoante a quantidade de dados que está a ser encriptados. Recomendamos que os clientes sempre testar as aplicações de desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos seguintes
* Transferir o [biblioteca de clientes do Storage do Azure para Java PyPi pacote](https://pypi.python.org/pypi/azure-storage)
* Transferir o [código a partir do GitHub de origem da biblioteca de clientes do Storage do Azure para Python](https://github.com/Azure/azure-storage-python)
