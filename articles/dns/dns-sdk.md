---
title: Criar zonas DNS e conjuntos de registos no DNS do Azure utilizando o SDK .NET | Microsoft Docs
description: Como criar zonas DNS e os conjuntos de registos no DNS do Azure utilizando o SDK .NET.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: jonatul
ms.openlocfilehash: c0fb0be8da1c0ca48a4d43ea027d30a0bc17fe30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas DNS e conjuntos de registos utilizando o SDK .NET

É possível automatizar as operações criar, eliminar ou atualizar zonas DNS, os conjuntos de registos e registos ao utilizar o SDK de DNS com a biblioteca de gestão de DNS do .NET. Um projeto do Visual Studio completo está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Criar uma conta do principal de serviço

Normalmente, é concedido acesso programático a recursos do Azure através de uma conta dedicada em vez das suas próprias credenciais de utilizador. Estas contas dedicadas são denominadas contas 'principal de serviço'. Para utilizar o projeto de exemplo do SDK de DNS do Azure, terá primeiro de criar uma conta do principal de serviço e atribua-lhe as permissões corretas.

1. Siga [estas instruções](../azure-resource-manager/resource-group-authenticate-service-principal.md) para criar uma conta do principal de serviço (o projeto de exemplo do SDK de DNS do Azure assume que a autenticação baseada em palavra-passe.)
2. Criar um grupo de recursos ([Eis como](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Utilizar o RBAC do Azure para a conta do principal de serviço de conceder permissões de 'Contribuinte da zona de DNS' para o grupo de recursos ([Eis como](../active-directory/role-based-access-control-configure.md).)
4. Se utilizar o projeto de exemplo do SDK de DNS do Azure, edite o ficheiro de 'program.cs' da seguinte forma:

   * Introduza os valores corretos para o tenantId, clientId (também conhecido como ID de conta), o segredo (service principal conta palavra-passe) e subscriptionId como utilizada no passo 1.
   * Introduza o nome do grupo de recursos selecionado no passo 2.
   * Introduza um nome de zona DNS da sua preferência.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes de NuGet e declarações de espaço de nomes

Para utilizar o SDK .NET da Azure DNS, tem de instalar o **biblioteca de gestão do DNS do Azure** pacote NuGet e outros necessitem pacotes do Azure.

1. No **Visual Studio**, abra um projeto ou um novo projeto.
2. Aceda a **ferramentas**  **>**  **Gestor de pacotes NuGet**  **>**  **gerir pacotes NuGet para solução...** .
3. Clique em **procurar**, ativar o **incluir pré-lançamento** caixa de verificação e escreva **Microsoft.Azure.Management.Dns** na caixa de pesquisa.
4. Selecione o pacote e clique em **instalar** adicioná-lo ao seu projeto de Visual Studio.
5. Repetir o processo acima também instala os seguintes pacotes: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicione as seguintes declarações de espaço de nomes

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializar o cliente de gestão do DNS

O *DnsManagementClient* contém os métodos e propriedades necessárias para gerir zonas DNS e recordsets.  O seguinte código regista conta do principal de serviço e cria um objeto de DnsManagementClient.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Criar ou atualizar uma zona DNS

Para criar uma zona DNS, primeiro "Zona" é criado um objeto que contém os parâmetros de zona DNS. Porque zonas DNS não estão ligadas a uma região específica, a localização é definida como 'global'. Neste exemplo, um [do Azure Resource Manager 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) também é adicionado à zona.

Para, efetivamente, criar ou atualizar a zona no DNS do Azure, o objecto de zona que contém os parâmetros de zona é passado para o *DnsManagementClient.Zones.CreateOrUpdateAsyc* método.

> [!NOTE]
> DnsManagementClient suporta três modos de funcionamento: síncrona ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrona com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Pode escolher qualquer um dos modos destas, dependendo das necessidades da sua aplicação.

O DNS do Azure suporta simultaneidade otimista, denominada [Etags](dns-getstarted-create-dnszone.md). Neste exemplo, especificar "*" para o 'If-None-Match' cabeçalho indica ao DNS do Azure para criar uma zona DNS, se ainda não existir.  A chamada falha se uma zona com o nome especificado já existe no grupo de recursos específico.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Criar conjuntos de registos de DNS e registos

Registos DNS são geridos como um conjunto de registos. Um conjunto de registos é um conjunto de registos com o mesmo nome e tipo de registo dentro de uma zona.  O nome do conjunto de registos é relativo para o nome da zona, não o nome DNS completamente qualificado.

Para criar ou atualizar um conjunto de registos, é criado e transmitido para um objeto de parâmetros de "RecordSet" *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Como com zonas DNS, existem três modos de funcionamento: síncrona ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrona com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Tal como acontece com zonas DNS, operações em conjuntos de registos incluem suporte para simultaneidade otimista.  Neste exemplo, uma vez que não 'If-Match' nem 'If-None-Match' é especificadas, o conjunto de registos é sempre criado.  Esta chamada substitui quaisquer existente conjunto de registos com o mesmo nome e tipo de registo nesta zona DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Obter conjuntos de registos e zonas

O *DnsManagementClient.Zones.Get* e *DnsManagementClient.RecordSets.Get* métodos obter zonas individuais e conjuntos de registos, respetivamente. RecordSets são identificados pelo respetivo tipo, o nome e o grupo de recursos e horário existam na. Zonas são identificadas pelo respetivo nome e o grupo de recursos que existam na.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Atualize um conjunto de registos existente

Para atualizar o conjunto de registos de DNS existente, primeiro obter o conjunto de registos, em seguida, atualizar o conteúdo do conjunto de registos, em seguida, submeta a alteração.  Neste exemplo, especificamos 'Etag' do conjunto de registos obtido no parâmetro 'If-Match'. A chamada falha se uma operação simultânea modificou o registo definir entretanto.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Os conjuntos de registos e zonas de lista

Para listar zonas, utilize o *DnsManagementClient.Zones.List...*  métodos, que suportam a listar o todas as zonas de um grupo de recursos específico ou todas as zonas numa determinada subscrição do Azure (através de grupos de recursos.) Para listar os conjuntos de registos, utilize *DnsManagementClient.RecordSets.List...*  métodos, que suportam a listagem de todos os conjuntos de registos numa determinada zona ou apenas os conjuntos de registos de um tipo específico.

Tenha em atenção quando listar zonas e poderão ser paginated conjuntos de registos resultante.  O exemplo seguinte mostra como itere através das páginas de resultados. (Um tamanho de página artificialmente pequeno de '2' é utilizado para forçar a paginação; na prática, este parâmetro deve ser omitido e o tamanho da página predefinido utilizado).

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Passos seguintes

Transferir o [projeto de exemplo do SDK .NET da Azure DNS](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que inclui mais exemplos de como utilizar o SDK de .NET de DNS do Azure, incluindo exemplos para outros tipos de registos de DNS.
