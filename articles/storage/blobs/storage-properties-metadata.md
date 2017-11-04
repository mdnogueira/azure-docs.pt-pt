---
title: Definir e obter as propriedades do objeto e os metadados no armazenamento do Azure | Microsoft Docs
description: Armazenar metadados personalizados sobre objetos no Storage do Azure e definir e obter as propriedades do sistema.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e obter propriedades e metadados

Objetos no propriedades do sistema de suporte de armazenamento do Azure e os metadados definidos pelo utilizador, para além dos dados que contêm. Este artigo aborda as propriedades do sistema de gestão e os metadados definidos pelo utilizador com o [biblioteca de clientes do Storage do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriedades do sistema**: existem propriedades do sistema em cada recurso de armazenamento. Alguns deles possam ler ou definir, enquanto outras são só de leitura. Nos bastidores, algumas propriedades do sistema correspondem a determinados cabeçalhos de HTTP padrão. A biblioteca de clientes do storage do Azure mantém estas por si.

* **Os metadados definidos pelo utilizador**: os metadados definidos pelo utilizador são metadados que especifica um determinado recurso sob a forma de um par nome / valor. Pode utilizar os metadados para armazenar os valores adicionais com um recurso de armazenamento. Estes valores de metadados adicionais são os seus próprios apenas para fins de e não afetam a forma como se comporta ao recurso.

Ao obter valores de propriedade e metadados para um recurso de armazenamento é um processo de dois passos. Antes de ler estes valores, tem explicitamente obtê-los ao chamar o **FetchAttributesAsync** método.

> [!IMPORTANT]
> Os valores de propriedade e metadados para um recurso de armazenamento não são preenchidos a menos que tem de chamar um do **FetchAttributesAsync** métodos.
>
> Receberá um `400 Bad Request` se qualquer pares nome/valor contém carateres não ASCII. Metadados pares de nome/valor são os cabeçalhos de HTTP válidos e, por isso, tem de cumprir todas as restrições que rege cabeçalhos de HTTP. Por conseguinte, é recomendado que utilize a codificação do URL ou codificação Base64 para os nomes e valores que contêm carateres não ASCII.
>

## <a name="setting-and-retrieving-properties"></a>Definir e obter propriedades
Para obter os valores de propriedade, chame o **FetchAttributesAsync** método no blob ou contentor para preencher as propriedades, em seguida, ler os valores.

Para definir propriedades de um objeto, especifique a propriedade valor, em seguida, invoque o **SetProperties** método.

Exemplo de código seguinte cria um contentor, em seguida, escreve algumas das respetivos valores de propriedade para uma janela da consola.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Definir e obter os metadados
Pode especificar metadados como um ou mais pares nome-valor num recurso blob ou contentor. Para definir os metadados, adicionar pares nome-valor para o **metadados** coleção no recurso, em seguida, chame o **SetMetadata** método para guardar os valores para o serviço.

> [!NOTE]
> O nome do seu metadados tem de estar em conformidade com as convenções de nomenclatura para os identificadores de c#.
>
>

Exemplo de código seguinte define metadados de um contentor. Um valor é definido através da coleção **adicionar** método. O outro valor é definido utilizando a sintaxe de chave/valor implícito. Ambos são válidas.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Para obter os metadados, chame o **FetchAttributes** método no blob ou contentor para preencher o **metadados** coleção, em seguida, ler os valores, conforme mostrado no exemplo abaixo.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Passos seguintes
* [Biblioteca de clientes do Storage do Azure para referência do .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Biblioteca de clientes do Storage do Azure para o pacote NuGet do .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
