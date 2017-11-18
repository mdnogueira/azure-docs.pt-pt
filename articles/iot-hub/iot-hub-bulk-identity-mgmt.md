---
title: "Importar a exportação de identidades de dispositivo do IoT Hub do Azure | Microsoft Docs"
description: "Como utilizar o SDK do serviço IoT do Azure para efetuar operações em massa contra o registo de identidade para importar e exportar as identidades de dispositivo. Operações de importação permitem-lhe criar, atualizar e eliminar as identidades de dispositivo em massa."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: f3fbccdbd00fb64348e2132ca6adc3a06ad18535
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Gerir as identidades de dispositivo do IoT Hub em massa

Cada IoT hub tem um registo de identidade, que pode utilizar para criar recursos por dispositivo no serviço. O registo de identidade também lhe permite controlar o acesso aos pontos finais do orientado para o dispositivo. Este artigo descreve como importar e exportar as identidades de dispositivo em massa e de um registo de identidade.

As operações de importação e exportação ocorrer no contexto de *tarefas* que lhe permite executar as operações do serviço em massa em relação a um IoT hub.

O **RegistryManager** classe inclui o **ExportDevicesAsync** e **ImportDevicesAsync** métodos que utilizam o **tarefa** framework. Estes métodos permitem-lhe exportar, importar e sincronizar a totalidade de um registo de identidade do IoT hub.

## <a name="what-are-jobs"></a>Quais são as tarefas?

Utilizam operações de registo de identidade a **tarefa** sistema quando a operação:

* Tem um tempo de execução longo potencialmente em comparação com as operações de tempo de execução padrão.
* Devolve uma grande quantidade de dados para o utilizador.

Em vez de uma única chamada API a aguardar ou bloquear no resultado da operação, a operação cria de forma assíncrona uma **tarefa** para que o IoT hub. A operação, em seguida, imediatamente devolve um **JobProperties** objeto.

O fragmento de código do c# seguinte mostra como criar uma tarefa de exportação:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Para utilizar o **RegistryManager** classe no seu código c#, adicione o **Microsoft.Azure.Devices** pacote NuGet ao seu projeto. O **RegistryManager** classe está a ser o **Microsoft.Azure.Devices** espaço de nomes.

Pode utilizar o **RegistryManager** classe para consultar o estado do **tarefa** utilizando o devolvido **JobProperties** metadados.

O fragmento de código do c# seguinte mostra como consultar a cada cinco segundos para ver se a tarefa terminou a execução:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Dispositivos de exportação

Utilize o **ExportDevicesAsync** método para exportar a totalidade de um registo de identidade do IoT hub para um [Storage do Azure](../storage/index.yml) através do contentor de blob um [assinatura de acesso partilhado](../storage/common/storage-security-guide.md#data-plane-security).

Este método permite-lhe criar cópias de segurança fiáveis das suas informações de dispositivo num contentor de blob controlado por si.

O **ExportDevicesAsync** método requer dois parâmetros:

* A *cadeia* que contém um URI de um contentor de blob. Este URI tem de conter um token SAS, que concede acesso de escrita ao contentor. A tarefa cria um blob de bloco neste contentor para armazenar os dados de dispositivo de exportação serializada. O token SAS tem de incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* A *booleano* que indica se pretende excluir chaves de autenticação dos seus dados de exportação. Se **falso**, chaves de autenticação estão incluídas na saída de exportação. Caso contrário, as chaves são exportadas como **nulo**.

O seguinte fragmento de código do c# mostra como iniciar uma tarefa de exportação que inclui as chaves de autenticação de dispositivo dos dados de exportação e, em seguida, consultar conclusão:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

A tarefa armazena o resultado no contentor do blob fornecido como um blob de blocos com o nome **devices.txt**. Os dados de saída é composta por dados de dispositivo JSON serializado com um dispositivo por linha.

O exemplo seguinte mostra os dados de saída:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se um dispositivo tiver dados duplo, os dados duplo também são exportados, juntamente com os dados do dispositivo. O exemplo seguinte mostra este formato. Todos os dados na linha "twinETag" até ao fim de são dados duplo.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Se precisar de acesso a estes dados no código, pode facilmente anular a serialização deste dados utilizando o **ExportImportDevice** classe. O fragmento de código do c# seguinte mostra como ler as informações de dispositivo que tenha sido anteriormente exportadas para um blob de bloco:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
> Também pode utilizar o **GetDevicesAsync** método o **RegistryManager** classe para obter uma lista dos seus dispositivos. No entanto, esta abordagem tem um limite rígido de 1000 no número de objetos de dispositivo que são devolvidos. O caso de utilização esperada para o **GetDevicesAsync** método é para cenários de desenvolvimento ajudar a depuração e não é recomendado para cargas de trabalho de produção.

## <a name="import-devices"></a>Importar dispositivos

O **ImportDevicesAsync** método o **RegistryManager** classe permite-lhe efetuar operações de importação e sincronização de em massa de um registo de identidade do IoT hub. Como o **ExportDevicesAsync** método, o **ImportDevicesAsync** método utiliza o **tarefa** framework.

Asseguramos utilizando o **ImportDevicesAsync** método porque, além de aprovisionamento de novos dispositivos no seu registo de identidade, pode também atualizar e eliminar dispositivos existentes.

> [!WARNING]
> Uma operação de importação não pode ser anulada. Sempre efetuar cópias de segurança de dados existentes utilizando o **ExportDevicesAsync** método para outro contentor do blob antes de efetuar em massa alterações ao seu registo de identidade.

O **ImportDevicesAsync** método aceita dois parâmetros:

* A *cadeia* que contém um URI de um [Storage do Azure](../storage/index.yml) contentor do blob a utilizar como *entrada* à tarefa. Este URI tem de conter um token SAS, que concede acesso de leitura para o contentor. Este contentor tem de conter um blob com o nome **devices.txt** que contém os dados do dispositivo serializada para importar para o seu registo de identidade. Importar dados tem de conter informações do dispositivo no mesmo formato de JSON que o **ExportImportDevice** tarefa utiliza quando cria um **devices.txt** blob. O token SAS tem de incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* A *cadeia* que contém um URI de um [Storage do Azure](https://azure.microsoft.com/documentation/services/storage/) contentor do blob a utilizar como *saída* da tarefa. A tarefa cria um blob de bloco neste contentor para armazenar qualquer informação sobre o erro de importação concluída **tarefa**. O token SAS tem de incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Os dois parâmetros podem apontar para o mesmo contentor de blob. Os parâmetros separados simplesmente ativar mais controlo sobre os seus dados como o contentor de saída requer permissões adicionais.

O fragmento de código do c# seguinte mostra como iniciar uma tarefa de importação:

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Este método também pode ser utilizado para importar os dados para o dispositivo duplo. O formato dos dados de entrada é o mesmo que o formato mostrado no **ExportDevicesAsync** secção. Desta forma, pode reimportar os dados exportados. O **$metadata** é opcional.

## <a name="import-behavior"></a>Comportamento de importação

Pode utilizar o **ImportDevicesAsync** método para efetuar as seguintes operações em massa no seu registo de identidade:

* Registo em massa de novos dispositivos
* Eliminações de em massa de dispositivos existentes
* Em massa alterações de estado (ativar ou desativar dispositivos)
* Atribuição de em massa de chaves de autenticação do novo dispositivo
* Em massa auto-regeneração de chaves de autenticação do dispositivo
* Atualização em massa de dados duplo

Pode efetuar qualquer combinação das operações anteriores num único **ImportDevicesAsync** chamada. Por exemplo, pode registar novos dispositivos e eliminar ou atualizar dispositivos existentes ao mesmo tempo. Quando utilizado juntamente com o **ExportDevicesAsync** método, pode migrar completamente todos os seus dispositivos de um hub IoT para outro.

Se o ficheiro de importação incluir metadados duplo, estes metadados substitui os metadados de duplo existente. Se o ficheiro de importação não inclui os metadados de duplo, em seguida, apenas o `lastUpdateTime` metadados é atualizado com a hora atual.

Utilize a opção **importMode** propriedade dos dados de serialização de importação para cada dispositivo para controlar o importação processo por dispositivo. O **importMode** propriedade tem as seguintes opções:

| importMode | Descrição |
| --- | --- |
| **createOrUpdate** |Se não existir um dispositivo especificado com **id**, recentemente está registado. <br/>Se o dispositivo já existir, as informações existentes são substituídas com os dados de entrada fornecidos sem para o **ETag** valor. <br> O utilizador pode especificar opcionalmente dados duplo juntamente com os dados do dispositivo. Etag o duplo, se for especificado, é processada independentemente do etag do dispositivo. Se existir um erro de correspondência com etag o duplo existente, um erro é escrito no ficheiro de registo. |
| **criar** |Se não existir um dispositivo especificado com **id**, recentemente está registado. <br/>Se o dispositivo já existir, é escrito um erro no ficheiro de registo. <br> O utilizador pode especificar opcionalmente dados duplo juntamente com os dados do dispositivo. Etag o duplo, se for especificado, é processada independentemente do etag do dispositivo. Se existir um erro de correspondência com etag o duplo existente, um erro é escrito no ficheiro de registo. |
| **Atualizar** |Se já existir um dispositivo especificado com **id**, informações existentes são substituídas com os dados de entrada fornecidos sem para o **ETag** valor. <br/>Se o dispositivo não existir, é escrito um erro no ficheiro de registo. |
| **updateIfMatchETag** |Se já existir um dispositivo especificado com **id**, informações existentes são substituídas com os dados de entrada fornecidos apenas se existir um **ETag** corresponder. <br/>Se o dispositivo não existir, é escrito um erro no ficheiro de registo. <br/>Se existir um **ETag** erro de correspondência, o erro é escrito para o ficheiro de registo. |
| **createOrUpdateIfMatchETag** |Se não existir um dispositivo especificado com **id**, recentemente está registado. <br/>Se o dispositivo já existir, são substituídas informações existentes com os dados de entrada fornecidos, apenas se existir um **ETag** corresponder. <br/>Se existir um **ETag** erro de correspondência, o erro é escrito para o ficheiro de registo. <br> O utilizador pode especificar opcionalmente dados duplo juntamente com os dados do dispositivo. Etag o duplo, se for especificado, é processada independentemente do etag do dispositivo. Se existir um erro de correspondência com etag o duplo existente, um erro é escrito no ficheiro de registo. |
| **eliminar** |Se já existir um dispositivo especificado com **id**, este é eliminado sem para o **ETag** valor. <br/>Se o dispositivo não existir, é escrito um erro no ficheiro de registo. |
| **deleteIfMatchETag** |Se já existir um dispositivo especificado com **id**, é eliminado apenas se existir um **ETag** corresponder. Se o dispositivo não existir, é escrito um erro no ficheiro de registo. <br/>Se existir um erro de correspondência de ETag, é escrito um erro no ficheiro de registo. |

> [!NOTE]
> Se os dados de serialização não definir explicitamente um **importMode** sinalizador para um dispositivo, assume como **createOrUpdate** durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importar o exemplo de dispositivos – em massa de aprovisionamento de dispositivos

Exemplo de código c# seguinte ilustra como gerar várias identidades de dispositivo que:

* Inclua chaves de autenticação.
* Escreva essas informações do dispositivo para um blob de bloco.
* Importe os dispositivos para o registo de identidade.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemplo de dispositivos de importação – eliminação em massa

O exemplo de código seguinte mostra como eliminar os dispositivos que adicionou a utilizar o exemplo de código anterior:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Obter o URI de SAS do contentor

O exemplo de código seguinte mostra como gerar um [URI de SAS](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) com leitura, escrita e eliminação permissões para um contentor do blob:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a efetuar operações de em massa contra o registo de identidade de um hub IoT. Siga estas ligações para saber mais sobre a gestão do Azure IoT Hub:

* [Métricas de IoT Hub][lnk-metrics]
* [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
