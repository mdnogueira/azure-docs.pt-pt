---
title: "Criar um instantâneo só de leitura de um blob no Storage do Azure | Microsoft Docs"
description: "Saiba como criar um instantâneo de um blob para cópia de segurança de dados de BLOBs um determinado momento. Compreenda a forma como os instantâneos são faturados e como utilizá-los para minimizar os custos de capacidade."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 3710705d-e127-4b01-8d0f-29853fb06d0d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: tamram
ms.openlocfilehash: 7e891018ab110e7506601cd5b9b0460bf61711b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-blob-snapshot"></a>Criar um instantâneo de blob

Um instantâneo é uma versão só de leitura de um blob que é executada num ponto no tempo. Os instantâneos são úteis para criar cópias de segurança blobs. Depois de criar um instantâneo, pode ler, copiar ou eliminá-la, mas não é possível modificá-lo.

Um instantâneo de um blob é idêntico à respetiva base blob, exceto que tenha de URI de blob um **DateTime** valor acrescentado para o blob URI para indicar a hora em que o instantâneo foi tirado. Por exemplo, se uma página de URI do blob é `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o instantâneo URI é semelhante à `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Todos os instantâneos partilham o blob base URI. A única distinção entre o blob base e o instantâneo é o anexado **DateTime** valor.
>

Um blob pode ter qualquer número de instantâneos. Mantêm instantâneos até que estes sejam eliminados explicitamente. Um instantâneo não é possível outlive o blob base. Pode enumerar os instantâneos associados com o blob base para controlar os instantâneos atuais.

Quando cria um instantâneo de um blob, propriedades do sistema do blob são copiadas para o instantâneo com os mesmos valores. Metadados do blob base é também copiado para o instantâneo, a menos que especifique metadados separado para o instantâneo quando a criar.

Qualquer concessões associados com o blob base não afetam o instantâneo. Não é possível adquirir uma concessão num instantâneo.

Um ficheiro VHD é utilizado para armazenar as informações atuais e o estado de um disco VM. Pode desligar um disco de dentro da VM ou encerre a VM e, em seguida, tirar um instantâneo do respetivo ficheiro VHD. Pode utilizar esse ficheiro de instantâneo mais tarde para obter o ficheiro VHD neste ponto no tempo e recrie a VM.

Se a encriptação de serviço de armazenamento (SSE) está ativada para a conta do storage em que reside o blob, todos os instantâneos tomados desse blob serão encriptados inativos.

## <a name="create-a-snapshot"></a>Criar um instantâneo
Exemplo de código seguinte mostra como criar um instantâneo, utilizando o [biblioteca de clientes do Storage do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Este exemplo Especifica metadados adicionais para o instantâneo quando é criado.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>Instantâneos de cópia
Operações de cópia que envolvem os blobs e instantâneos, siga estas regras:

* Pode copiar um instantâneo ao longo do respetivo blob base. Ao promover um instantâneo para a posição do base blob, pode restaurar uma versão anterior de um blob. Os instantâneos permanecem, mas a base de blob é substituído por uma cópia do instantâneo gravável.
* Pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.
* Quando um blob de origem é copiado, todos os instantâneos do blob de origem não são copiados para o destino. Quando um blob de destino é substituído por uma cópia, todos os instantâneos associados com o blob de destino original permaneçam intactos.
* Quando cria um instantâneo de um blob de bloco, lista de bloqueios consolidada do blob é também copiada para o instantâneo. Quaisquer não consolidados blocos não são copiados.

## <a name="specify-an-access-condition"></a>Especifique uma condição de acesso
Quando chamar [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], pode especificar uma condição de acesso para que a criação do instantâneo apenas se a condição é cumprida. Para especificar uma condição de acesso, utilize o [AccessCondition] [ dotnet_AccessCondition] parâmetro. Se não for cumprida a condição especificada, a criação do instantâneo não e o serviço Blob devolve o código de estado [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Elimine os instantâneos
Não é possível eliminar um blob com instantâneos, exceto se os instantâneos também são eliminados. Pode eliminar um instantâneo individualmente ou especificar que todos os instantâneos ser eliminado quando o blob de origem é eliminado. Se tentar eliminar um blob que ainda tem instantâneos, resulta um erro.

O exemplo de código seguinte mostra como eliminar um blob e o respetivos instantâneos no .NET, onde `blockBlob` é um objeto do tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Instantâneos com armazenamento do Azure Premium
Ao utilizar instantâneos de armazenamento Premium, aplicam as seguintes regras:

* O número máximo de instantâneos por BLOBs de páginas numa conta de armazenamento premium é 100. Se esse limite for excedido, a operação de Blob de instantâneo devolve o código de erro 409 (`SnapshotCountExceeded`).
* Pode tirar um instantâneo de um blob de página numa conta de armazenamento premium uma vez a cada 10 minutos. Se a velocidade a que for excedida, a operação de Blob de instantâneo devolve o código de erro 409 (`SnapshotOperationRateExceeded`).
* Para ler um instantâneo, pode utilizar a operação de BLOBs de cópia para copiar um instantâneo para outra página blob na conta. O blob de destino para a operação de cópia não deve ter todos os instantâneos existentes. Se o blob de destino possui instantâneos, em seguida, a operação de cópia Blob devolve o código de erro 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Devolver o URI absoluto para um instantâneo
Este exemplo de código c# cria um instantâneo e escreve terminar o URI absoluto para a localização primária.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Compreender a forma como instantâneos acumular custos
Criar um instantâneo, o que é uma cópia só de leitura de um blob, pode resultar em encargos de armazenamento de dados adicionais à sua conta. Ao conceber a sua aplicação, é importante ter em consideração a forma como estes custos podem acumular, de modo a que pode minimizar os custos.

### <a name="important-billing-considerations"></a>Considerações importantes sobre faturação
A lista seguinte inclui pontos de chaves a considerar quando criar um instantâneo.

* A conta do storage incorreu custos de blocos exclusivos ou páginas, quer sejam no blob ou no instantâneo. A conta não implicar custos adicionais para instantâneos associados um blob até que Atualize o blob em que têm como base. Depois de atualizar o blob base, diverges dos respetivos instantâneos. Quando isto acontecer, são-lhe cobrados para as páginas em cada blob ou um instantâneo ou blocos exclusivos.
* Se substituir um bloco dentro de um blob de bloco, o bloco subsequentemente é cobrado como um bloqueio exclusivo. Isto acontece mesmo se o bloco tiver o mesmo ID de bloco e os mesmos dados porque tem no instantâneo. Depois do bloco está consolidado novamente,-diverges do respetivo homólogo em qualquer instantâneo e será cobrada para os respetivos dados. O mesmo se aplica para uma página num blob de página que é atualizado com dados idênticos.
* Substituir um blob de blocos ao chamar o [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray] [ dotnet_UploadFromByteArray] método substitui todos os blocos no blob. Se tiver um instantâneo associado a esse blob, todos os blocos no base blob e instantâneo agora diverge e será cobrada para todos os blocos de ambos os blobs. Isto acontece mesmo se os dados na base blob e o instantâneo permanecerem idênticos.
* Serviço Blob do Azure não tem um meio para determinar se dois blocos contiverem dados idênticos. Cada bloco que está carregado e consolidado é tratado como exclusivos, mesmo que tem os mesmos dados e o mesmo ID de bloco. Uma vez que acumular custos de blocos exclusivos, é importante considerar que a atualização de um blob que tenha um resultados de instantâneos em blocos exclusivos adicionais e encargos adicionais.

### <a name="minimize-cost-with-snapshot-management"></a>Minimizar os custos com gestão de instantâneos

É recomendado gerir os instantâneos cuidadosamente de forma a evitar custos adicionais. Pode seguir estes procedimentos recomendados para ajudar a minimizar os custos gasta no armazenamento do seu instantâneos:

* Eliminar e voltar a criar instantâneos associados a um blob sempre que atualizar o blob, mesmo que estão a atualizar com dados idênticos, a menos que a estrutura da aplicação requer que manter instantâneos. Ao eliminar e voltar a criar instantâneos do blob, pode certificar-se de que os BLOBs e instantâneos não diverge.
* Se estiver a manter instantâneos para um blob, evite chamar [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray] [ dotnet_UploadFromByteArray] para atualizar o blob. Estes métodos substituem todos os blocos no blob, fazendo com que o blob de base e respetivos instantâneos para diverge significativamente. Em vez disso, atualize o menor número possível de blocos utilizando o [PutBlock] [ dotnet_PutBlock] e [PutBlockList] [ dotnet_PutBlockList] métodos.

### <a name="snapshot-billing-scenarios"></a>Instantâneo de cenários de faturação
Os cenários seguintes demonstram como encargos acumular para um blob de bloco e respetivos instantâneos.

**Cenário 1**

Cenário 1, o blob base não foi atualizado após o instantâneo foi captado, pelo que são cobradas taxas apenas para blocos exclusivos 1, 2 e 3.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Cenário 2**

Cenário 2, o blob base foi atualizado, mas o instantâneo não tem. Bloco 3 foi atualizada e, mesmo que contém os mesmos dados e o mesmo ID, não é igual ao bloquear 3 no instantâneo. Como resultado, a conta é cobrada de quatro blocos.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Cenário 3**

No cenário 3, o blob base foi atualizado, mas o instantâneo não tem. Bloco 3 foi substituído por blocos 4 no base blob, mas o instantâneo ainda reflete bloco 3. Como resultado, a conta é cobrada de quatro blocos.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Cenário 4**

Cenário 4, o blob base foi atualizado completamente e contém nenhum dos seus blocos de originais. Como resultado, a conta é cobrada de todos os blocos exclusivos oito. Este cenário pode ocorrer se estiver a utilizar um método de atualização, tais como [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray][dotnet_UploadFromByteArray], porque estes métodos substituem todo o conteúdo de um blob.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passos seguintes

* Pode encontrar mais informações sobre como trabalhar com instantâneos de disco de máquina virtual (VM) no [cópia de segurança do Azure discos VM não geridos com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)

* Para obter exemplos de códigos adicionais utilizando o Blob storage, consulte [exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Pode transferir uma aplicação de exemplo e executá-la ou procurar o código no GitHub.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx