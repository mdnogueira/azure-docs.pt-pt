---
title: Gerir a Simultaneidade no Armazenamento do Microsoft Azure
description: "Como gerir simultaneidade para os serviços Blob, fila, tabela e ficheiro"
services: storage
documentationcenter: 
author: jasontang501
manager: tadb
editor: tysonn
ms.assetid: cc6429c4-23ee-46e3-b22d-50dd68bd4680
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.openlocfilehash: 937cca66a0af0674b868e6a87681adbea330e91c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerir a Simultaneidade no Armazenamento do Microsoft Azure
## <a name="overview"></a>Descrição geral
Aplicações modernas de Internet com base normalmente têm vários utilizadores ver e atualizar os dados em simultâneo. Isto requer que os programadores de aplicações cuidadosamente pensar sobre como fornecer uma experiência previsível para os respetivos utilizadores finais, especialmente para cenários em que vários utilizadores podem atualizar os mesmos dados. Existem três estratégias de simultaneidade de dados principal que os programadores normalmente considerar:  

1. Simultaneidade otimista – uma aplicação, efetuar uma atualização como parte da respetiva atualização verificará se os dados foi alterada desde a aplicação de leitura pela última vez que os dados. Por exemplo, se dois utilizadores visualizar uma página wiki efetuar uma atualização para a mesma página, em seguida, a plataforma de wiki tem de garantir que a segunda atualização não substituir a primeira atualização – e que ambos os utilizadores compreender se os seus atualização foi concluída com êxito ou não. Esta estratégia é frequentemente utilizada em aplicações web.
2. Simultaneidade pessimistic – uma procura de aplicação para efetuar uma atualização demorará um bloqueio de um objeto a impedir que outros utilizadores a atualizar os dados até que o bloqueio é libertado. Por exemplo, um cenário de replicação de dados principal/subordinado onde apenas o mestre de irá efetuar atualizações o mestre de normalmente armazena um bloqueio exclusivo durante um longo período de tempo nos dados para garantir que ninguém mais pode atualizá-lo.
3. Última wins de escritor – uma abordagem que permite que as operações de atualização continuar sem verificar se qualquer outra aplicação atualizou os dados desde a aplicação primeiro ler os dados. Esta estratégia (ou à falta de uma estratégia de formal) é normalmente utilizada em que os dados estão particionados de forma a que não há nenhum probabilidade que vários utilizadores acedem aos mesmos dados. Pode também ser útil em que estão a ser processados fluxos de dados de curta duração.  

Este artigo fornece uma descrição geral de como a plataforma de armazenamento do Azure simplifica a programação ao fornecer suporte de primeira classe para todas as três estratégias estes concorrência.  

## <a name="azure-storage--simplifies-cloud-development"></a>Armazenamento do Azure – simplifica a programação de nuvem
O serviço de armazenamento do Azure suporta todas as estratégias de três, embora seja distinctive na respetiva capacidade de oferecem suporte completo de simultaneidade otimista e pessimistic porque foi concebido para aderir um modelo a consistência forte garante que, à Serviço de armazenamento consolida uma inserção de dados ou operação de atualização de todos os acede mais para que os dados irão ver a atualização mais recente. Plataformas de armazenamento que utilizam um modelo de consistência eventual têm um atraso entre quando uma operação de escrita é efetuada por um utilizador e quando os dados atualizados podem ser vistos por outros utilizadores, por conseguinte, complicating desenvolvimento de aplicações de cliente para evitar inconsistências de afetar os utilizadores finais.  

Além de selecionar uma estratégia de concorrência adequado aos programadores devem também estar cientes de como uma plataforma de armazenamento isola as alterações – particularmente alterações ao mesmo objeto em transações. O serviço de armazenamento do Azure utiliza o isolamento do instantâneo para permitir operações de leitura acontecer simultaneamente com operações de escrita dentro de uma única partição. Ao contrário de outros níveis de isolamento, o isolamento do instantâneo garante que todas as leituras consulte um instantâneo consistente dos dados, mesmo quando as atualizações estão a ocorrer – essencialmente devolvendo os últimos valores consolidados durante uma atualização transação está a ser processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gerir a simultaneidade no Blob storage
Pode optar por utilizar qualquer um dos modelos de simultaneidade otimista ou pessimistic para gerir o acesso para os blobs e contentores no serviço blob. Se não especificar explicitamente wins de escritas último uma estratégia é a predefinição.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidade otimista para os blobs e contentores
O serviço de armazenamento atribui um identificador a cada objeto armazenado. Este identificador é atualizado sempre que é executada uma operação de atualização de um objeto. O identificador é devolvido ao cliente como parte de uma resposta de HTTP GET utilizando o cabeçalho ETag (tag de entidade) que está definido no âmbito do protocolo HTTP. Um utilizador efetuar uma atualização desse objecto pode enviar por ETag original juntamente com um cabeçalho condicional garantir que uma atualização situação ocorrerá apenas se tiver sido cumprida uma condição de determinados – neste caso, a condição é um cabeçalho de "If-Match", que requer o t de serviço de armazenamento Nã Certifique-se o valor ETag especificado no pedido de atualização é igual à que é armazenado no serviço de armazenamento.  

A descrição deste processo é o seguinte:  

1. Obter um blob do serviço de armazenamento, a resposta inclui um valor de cabeçalho ETag de HTTP que identifica a versão atual do objeto no serviço de armazenamento.
2. Quando atualizar o blob, inclua o valor ETag recebido no passo 1 de **If-Match** condicional cabeçalho do pedido enviar para o serviço.
3. O serviço compara o valor ETag no pedido com o valor ETag atual do blob.
4. Se o valor ETag atual do blob é uma versão diferente do ETag no **If-Match** cabeçalho condicional no pedido, o serviço devolve um erro de 412 ao cliente. Esta definição indica ao cliente que outro processo tiver atualizado o blob, uma vez que o cliente obtido.
5. Se o valor ETag atual do blob é a mesma versão que o ETag no **If-Match** cabeçalho condicional no pedido, o serviço efetua a operação pedida e atualiza o valor ETag atual do blob para mostrar que foi criado uma nova versão.  

O c# fragmento seguinte (utilizando a biblioteca de cliente do Storage 4.2.0) mostra um exemplo simples de como construir um **If-Match AccessCondition** baseado no valor ETag que é acedido a partir das propriedades de um blob que foram anteriormente um obter ou inserir. Em seguida, utiliza o **AccessCondition** objeto quando atualiza o blob: o **AccessCondition** objeto adiciona o **If-Match** cabeçalho para o pedido. Se outro processo tiver atualizado o blob, o serviço blob devolve uma mensagem de estado de 412 HTTP (falha de pré-condição). Pode transferir o exemplo completo aqui: [gerir concorrência com o Storage do Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the etag in response.
string orignalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No etag, provided so orignal blob is overwritten (thus generating a new etag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the orignal ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(orignalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

O serviço de armazenamento também inclui suporte para os cabeçalhos condicionais adicionais, tais como **If-modificado-Since**, **If-Unmodified-Since** e **If-None-Match** , bem como combinações de ambos. Para obter mais informações, consulte [especificando condicional cabeçalhos para operações de serviço Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) no MSDN.  

A tabela seguinte resume as operações de contentor que aceitam, tais como cabeçalhos condicionais **If-Match** no pedido de e para que devolva um valor ETag na resposta.  

| Operação | Devolve o valor ETag de contentor | Aceita condicionais cabeçalhos |
|:--- |:--- |:--- |
| Criar contentor |Sim |Não |
| Obter as propriedades do contentor |Sim |Não |
| Obter metadados do contentor |Sim |Não |
| Definir os metadados do contentor |Sim |Sim |
| Obter o contentor ACL |Sim |Não |
| Definir o contentor ACL |Sim |Sim (*) |
| Eliminar o contentor |Não |Sim |
| Contentor de concessão |Sim |Sim |
| Lista de Blobs |Não |Não |

(*) As permissões definidas pelo SetContainerACL são colocadas em cache e atualizações para estas permissões que demore 30 segundos para propagar durante o período de atualizações não são garantidas que seja consistente.  

A tabela seguinte resume as operações de BLOBs que aceitam, tais como cabeçalhos condicionais **If-Match** no pedido de e para que devolva um valor ETag na resposta.

| Operação | Devolve o valor ETag | Aceita condicionais cabeçalhos |
|:--- |:--- |:--- |
| Colocar Blob |Sim |Sim |
| Obter o Blob |Sim |Sim |
| Obter propriedades de Blob |Sim |Sim |
| Defina as propriedades de Blob |Sim |Sim |
| Obter metadados do Blob |Sim |Sim |
| Definir os metadados do Blob |Sim |Sim |
| Blob de concessão (*) |Sim |Sim |
| Blob de instantâneo |Sim |Sim |
| Copiar Blob |Sim |Sim (para o blob de origem e de destino) |
| Abortar copiar Blob |Não |Não |
| Eliminar o Blob |Não |Sim |
| Colocar bloco |Não |Não |
| Colocar a lista de bloqueios |Sim |Sim |
| Obter a lista de bloqueios |Sim |Não |
| Colocar a página |Sim |Sim |
| Obter intervalos de página |Sim |Sim |

(*) Blob de concessão não altera o ETag no blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimistic blobs
Para bloquear um blob para utilização exclusiva, pode adquirir um [concessão](http://msdn.microsoft.com/library/azure/ee691972.aspx) no mesmo. Quando adquirir uma concessão, especifique durante quanto precisa da concessão: Isto pode ser de entre 15 a 60 segundos ou infinito, o que perfaz um bloqueio exclusivo. Podem renovar uma concessão finita para o expandir e pode de versão qualquer concessão quando tiver terminado com o mesmo. O serviço blob versões automaticamente concessões finito quando expirarem.  

Concessões ativar estratégias de sincronização diferentes para ser suportado, incluindo escrita exclusiva / partilhado a escrita de leitura, exclusiva / exclusiva de ler e partilhado de escrita / leitura exclusivos. Onde existe uma concessão o serviço de armazenamento impõe exclusivas escritas (put, defina e eliminar operações) no entanto garantir exclusivity para operações de leitura requer o programador garantir que todas as aplicações de cliente utilizam um ID de concessão e que o cliente apenas um numa altura tem um ID de concessão válido. Operações de leitura que inclua um resultado de ID de concessão no leituras partilhadas.  

O seguinte fragmento de c# mostra um exemplo de adquirir uma concessão exclusiva para 30 segundos num blob, ao atualizar o conteúdo do blob e, em seguida, libertação da concessão. Se já existir uma concessão válida no blob ao tentar adquirir uma concessão novo, o serviço blob devolve um resultado de estado de "Conflito HTTP (409)". O fragmento seguinte utiliza uma **AccessCondition** objeto encapsular as informações de concessão quando faz um pedido para atualizar o blob no serviço de armazenamento.  Pode transferir o exemplo completo aqui: [gerir concorrência com o Storage do Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Se tentar uma operação de escrita no blob em leasing sem passar o ID de concessão, o pedido falha com um erro de 412. Tenha em atenção que se expira a concessão antes de chamar o **UploadText** método mas ainda transmita o ID de concessão, também falha o pedido com um **412** erro. Para obter mais informações sobre a gestão de tempos de expiração de concessão e IDs de concessão, consulte o [concessão Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) documentação REST.  

As seguintes operações de blob podem utilizar concessões para gerir a simultaneidade pessimistic:  

* Colocar Blob
* Obter o Blob
* Obter propriedades de Blob
* Defina as propriedades de Blob
* Obter metadados do Blob
* Definir os metadados do Blob
* Eliminar o Blob
* Colocar bloco
* Colocar a lista de bloqueios
* Obter a lista de bloqueios
* Colocar a página
* Obter intervalos de página
* Blob de instantâneo - ID de concessão opcional se existir uma concessão
* Copiar Blob - concessão ID necessárias se uma concessão existe no blob de destino
* Necessário de ID de concessão de BLOBs de cópia de abortar - se uma concessão infinita existe no blob de destino
* Blob de concessão  

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimistic para contentores
Concessões em contentores de ativar as mesmas estratégias de sincronização ser suportado como blobs (exclusiva de escrita / partilhado a escrita de leitura, exclusiva / exclusiva de ler e partilhado de escrita / leitura exclusivo) no entanto, ao contrário blobs o serviço de armazenamento apenas impõe exclusivity no operações de eliminação. Para eliminar um contentor com uma concessão ativa, um cliente tem de incluir o ID de concessão de Active Directory com o pedido de eliminação. Todas as outras operações de contentor ter êxito se for um contentor em leasing sem incluir o ID de concessão caso em que são partilhados operações. Se for necessária a exclusivity de atualização (put ou definido) ou operações de leitura, em seguida, os programadores devem certificar-se que todos os clientes utilizam um ID de concessão e que apenas um cliente a uma hora tem um ID de concessão válido.  

As seguintes operações de contentor podem utilizar concessões para gerir a simultaneidade pessimistic:  

* Eliminar o contentor
* Obter as propriedades do contentor
* Obter metadados do contentor
* Definir os metadados do contentor
* Obter o contentor ACL
* Definir o contentor ACL
* Contentor de concessão  

Para obter mais informações, consulte:  

* [Especificar condicionais cabeçalhos para operações de serviço Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Contentor de concessão](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blob de concessão](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gerir a simultaneidade no serviço tabela
O serviço de tabela utiliza otimista simultaneidade verifica como comportamento predefinido quando estiver a trabalhar com as entidades, ao contrário do serviço blob onde explicitamente tem optar por executar verificações de simultaneidade otimista. A outra diferença entre os serviços tabela e blob é que só pode gerir o comportamento de simultaneidade de entidades enquanto com o serviço blob pode gerir a simultaneidade de contentores e blobs.  

Para utilizar simultaneidade otimista e certifique-se outro processo modificada uma entidade, uma vez que obtidos a partir do serviço de armazenamento de tabela, pode utilizar o valor ETag que recebe quando o serviço tabela devolve uma entidade. A descrição deste processo é o seguinte:  

1. Obter uma entidade a partir do serviço de armazenamento de tabela, a resposta inclui um valor ETag que identifica o identificador atual associado a essa entidade no serviço de armazenamento.
2. Quando atualizar a entidade, inclua o valor ETag recebido no passo 1 obrigatórias **If-Match** cabeçalho do pedido enviar para o serviço.
3. O serviço compara o valor ETag no pedido com o valor ETag atual da entidade.
4. Se o valor ETag atual da entidade é diferente de ETag no obrigatórias **If-Match** cabeçalho do pedido, o serviço devolve um erro de 412 ao cliente. Esta definição indica ao cliente que outro processo atualizou a entidade, uma vez que o cliente obtido.
5. Se o valor ETag atual da entidade é o mesmo que o ETag no obrigatórias **If-Match** cabeçalho no pedido ou o **If-Match** cabeçalho contém o caráter universal (*), o serviço efetua a a operação solicitada e atualiza o valor ETag atual da entidade para mostrar que foi atualizado.  

Tenha em atenção que, ao contrário do serviço blob, o serviço tabela requer o cliente incluir um **If-Match** cabeçalho em pedidos de atualização. No entanto, é possível forçar um incondicional atualizar (último escritor wins estratégia) e ignorar simultaneidade verifica se o cliente define o **If-Match** cabeçalho para o caráter universal (*) no pedido.  

O seguinte fragmento de c# mostra uma entidade de cliente que foi está criada anteriormente ou a obtenção de ter o respetivo endereço de e-mail atualizado. Iniciais inserir ou obter operação armazena o valor ETag no objeto de cliente e porque este exemplo utiliza a mesma instância de objeto ao ser executada a operação de substituição, envia automaticamente o valor ETag novamente para o serviço tabela, ativar o serviço Verifique a existência de violações de concorrência. Se outro processo tem de atualizar a entidade no table storage, o serviço devolve uma mensagem de estado 412 HTTP (falha de pré-condição).  Pode transferir o exemplo completo aqui: [gerir concorrência com o Storage do Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Para desativar explicitamente a verificação de concorrência, deverá definir o **ETag** propriedade o **empregado** object "*" antes de executar a operação de substituição.  

```csharp
customer.ETag = "*";  
```

A tabela seguinte resume como as operações de entidade de tabela utilizam ETag valores:

| Operação | Devolve o valor ETag | Necessita de cabeçalho de pedido de If-Match |
|:--- |:--- |:--- |
| Entidades de consulta |Sim |Não |
| Inserir a entidade |Sim |Não |
| Entidade de atualização |Sim |Sim |
| Intercalar entidade |Sim |Sim |
| Eliminar a entidade |Não |Sim |
| Inserir ou substituir entidade |Sim |Não |
| Inserir ou Merge entidade |Sim |Não |

Tenha em atenção que o **inserir ou substituir entidade** e **inserção ou entidade intercalar** efetue operações *não* efetuar as verificações de concorrência porque não enviam um valor ETag à tabela serviço.  

Em geral, os programadores com tabelas deverá confiar na simultaneidade otimista quando desenvolver aplicações dimensionáveis. Se necessitar de bloqueio pessimistic, os programadores de uma abordagem poderá demorar quando aceder a tabelas está a atribuir um blob designado para cada tabela e tentar tomar uma concessão no blob antes da tabela a utilizar. Esta abordagem requer a aplicação para garantir que todos os caminhos de acesso a dados obter a concessão antes da tabela a utilizar. Deve também ter em atenção de que o período de concessão mínimo é de 15 segundos que requer cuidado considerações sobre escalabilidade.  

Para obter mais informações, consulte:  

* [Operações de entidades](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gerir a simultaneidade no serviço fila
Um cenário que no qual simultaneidade for uma preocupação no serviço de colocação é onde vários clientes são obtenção de uma fila de mensagens. Quando uma mensagem é obtida a partir da fila, a resposta inclui a mensagem e um valor de receção pop, que é necessário para eliminar a mensagem. A mensagem não é automaticamente eliminada da fila, mas depois de obtido, não é visível para outros clientes para o intervalo de tempo especificado pelo parâmetro visibilitytimeout. O cliente que obtém a mensagem é esperado ao eliminar a mensagem depois de ser processada e antes da data especificada pelo TimeNextVisible elemento da resposta é calculado com base no valor do parâmetro visibilitytimeout. O valor de visibilitytimeout é adicionado à hora em que a mensagem é obtida para determinar o valor de TimeNextVisible.  

O serviço fila não tem suporte para simultaneidade otimista ou pessimistic e para este clientes razão, as mensagens obtidas a partir de uma fila de processamento devem certificar-se as mensagens são processadas de forma idempotent. Uma estratégia de wins último escritor é utilizada para operações de atualização como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

* [API de REST do serviço fila](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Receber mensagens](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gerir a simultaneidade no serviço de ficheiro
O serviço de ficheiros pode ser acedido através de dois protocolo diferentes pontos finais – SMB e REST. O serviço REST não tem suporte para o bloqueio otimista ou bloqueio pessimistic e todas as atualizações serão siga uma estratégia de wins último escritor. Clientes SMB que montagem partilhas de ficheiros podem tirar partido dos mecanismos de bloqueio de sistema de ficheiros para gerir o acesso aos ficheiros partilhados – incluindo a capacidade de efetuar pessimistic bloqueio. Quando um cliente do SMB abre um ficheiro, especifica o acesso a ficheiros e a partilha de modo. Definir uma opção de acesso a ficheiros de "Escrever" ou "Leitura/escrita" juntamente com um modo de partilha de ficheiros de "None" resultará no ficheiro a ser bloqueado por um cliente do SMB, até que o ficheiro está fechado. Se a operação REST for tentada num ficheiro em que um cliente do SMB tem o ficheiro bloqueado o serviço REST irá devolver o código de estado 409 (conflito) com o código de erro SharingViolation.  

Quando um cliente do SMB abre um ficheiro para eliminar, marca o ficheiro como pendente eliminação até que todos os outros clientes SMB são fechadas alças abertas nesse ficheiro. Enquanto um ficheiro é marcado como pendente delete, qualquer operação REST nesse ficheiro irá devolver o código de estado 409 (conflito) com o código de erro SMBDeletePending. Não é devolvido o código de estado 404 (não for encontrado), uma vez que é possível que o cliente SMB remover o sinalizador de eliminação pendente antes de fechar o ficheiro. Por outras palavras, código de estado 404 (não for encontrado) é esperado apenas quando o ficheiro foi removido. Tenha em atenção que enquanto um ficheiro está no estado de eliminação pendente SMB, não será incluído nos resultados da lista de ficheiros. Além disso, tenha em atenção que as operações REST da eliminação de ficheiros e REST eliminar diretório são consolidadas atomically e resulta num Estado de eliminação pendente.  

Para obter mais informações, consulte:  

* [Gerir ficheiros bloqueia](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Resumo e os passos seguintes
O serviço de armazenamento do Microsoft Azure tem foi concebido para satisfazer as necessidades das aplicações mais complexas online sem forçar a programadores para comprometer ou rethink pressupostos de conceção importantes, tais como a simultaneidade e consistência de dados que entram a executar para é concedido.  

Para a aplicação de exemplo completo referenciada neste blogue:  

* [Gestão de concorrência com o Storage do Azure - exemplo de aplicação](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações, consulte Storage do Azure:  

* [Página inicial de armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Storage do Azure](storage-introduction.md)
* Armazenamento de introdução para [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabela](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../storage-dotnet-how-to-use-queues.md), e [ficheiros](../storage-dotnet-how-to-use-files.md)
* Arquitetura de armazenamento – [Storage do Azure: um serviço de armazenamento de nuvem de elevada disponibilidade com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

