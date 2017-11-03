---
title: Gerir registos DNS no DNS do Azure com o Azure PowerShell | Microsoft Docs
description: "Gerir conjuntos de registos de DNS e registos DNS do Azure ao alojamento do seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registos e registos."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: 2962e30e5d9c60b8e786e2ba79647cabfc5925cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Gerir registos DNS e recordsets no DNS do Azure com o Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerir registos DNS para a sua zona DNS ao utilizar o Azure PowerShell. Registos DNS também podem ser geridos através da utilização de várias plataformas [CLI do Azure](dns-operations-recordsets-cli.md) ou [portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos deste artigo partem do princípio de que já tem [instalado o Azure PowerShell, tem sessão iniciada e criar uma zona DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Criar um novo registo DNS

Se o novo registo tem o mesmo nome e tipo como um registo existente, terá de [adicioná-lo para o conjunto de registos existente](#add-a-record-to-an-existing-record-set). Se o novo registo tem um nome diferente e o tipo para todos os registos existentes, terá de criar um novo conjunto de registos. 

### <a name="create-a-records-in-a-new-record-set"></a>Criar '' registos num novo conjunto de registos

Pode criar conjuntos de registos com o cmdlet `New-AzureRmDnsRecordSet`. Ao criar um conjunto de registos, tem de especificar o conjunto de registos nome, a zona, o tempo para em direto (TTL), o tipo de registo e os registos a ser criado.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. Por exemplo, quando utilizar um conjunto de registos do tipo "A", tem de especificar o endereço IP utilizando o parâmetro `-IPv4Address`. Outros parâmetros são utilizados para outros tipos de registos. Consulte [exemplos de tipo de registo adicionais](#additional-record-type-examples) para obter mais detalhes.

O exemplo seguinte cria um conjunto de registos com o nome relativo "www" na zona DNS "contoso.com". O nome completamente qualificado do conjunto de registos é "www.contoso.com". O tipo de registo é 'A', e o TTL é de 3600 segundos. O conjunto de registos contém um único registo, com o endereço IP '1.2.3.4'.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para criar um registo definido em 'vértice' de uma zona (neste caso, "contoso.com"), utilize o nome do conjunto de registos ' @' (excluindo as aspas):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Se precisar de criar um conjunto que contém mais de um registo de registos, o primeiro criar uma matriz de local e adicionar os registos e transmitir a matriz para `New-AzureRmDnsRecordSet` da seguinte forma:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Os metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor. O exemplo seguinte mostra como criar um conjunto de registos com duas entradas de metadados, "Depart = financeiro ' e ' Ambiente = produção '.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

O DNS do Azure também suporta os conjuntos de registos 'empty', que podem atuar como um marcador de posição para reservar um nome DNS antes de criar registos DNS. Os conjuntos de registos vazios são visíveis no plane de controlo de DNS do Azure, mas aparece nos servidores de nome DNS do Azure. O exemplo seguinte cria um conjunto de registos vazio:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos de

Ter visto em detalhe como '' registos de criar, os exemplos seguintes mostram como criar registos de outros tipos de registos suportados pelo DNS do Azure.

Em cada caso, vamos mostrar como criar um conjunto que contém um único registo de registos. Os exemplos anteriores para '' registos podem ser adaptados para criar conjuntos de registos de outros tipos de vários registos com metadados, ou para criar conjuntos de registos vazios.

Não iremos dar um exemplo para criar um conjunto de registos SOA, uma vez que são criados SOAs e eliminada com cada zona DNS e não pode ser criada ou eliminada separadamente. No entanto, [o SOA pode ser modificada, conforme mostrado no exemplo posterior](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registos AAAA com um único registo

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registos CNAME com um único registo

> [!NOTE]
> As normas DNS não permite registos CNAME no vértice da zona de uma (`-Name '@'`), nem permitem a conjuntos de registos que contêm mais de um registo.
> 
> Para obter mais informações, consulte [registos CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registos MX com um único registo

Neste exemplo, utilizamos o nome do conjunto de registos ' @' para criar um registo MX no vértice da zona (neste caso, 'contoso.com').


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registos NS com um único registo

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registos PTR com um único registo

Neste caso, "os meus-arpa-zone.com' representa a zona de pesquisa inversa ARPA, que representa o intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs. O nome do registo '10' é o último octeto do endereço IP neste intervalo de IP representado por este registo.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registos SRV com um único registo

Ao criar um [conjunto de registos SRV](dns-zones-records.md#srv-records), especifique o  *\_serviço* e  *\_protocolo* no nome do conjunto de registos. Não é necessário incluir ' @' no nome do conjunto de registos quando criar um registo SRV definido no vértice da zona.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Criar um registo TXT definido com um único registo

O exemplo seguinte mostra como criar um registo TXT. Para obter mais informações sobre o comprimento da cadeia máxima suportada nos registos TXT, consulte [registos TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Obter um conjunto de registos

Para obter um conjunto de registos existente, utilize `Get-AzureRmDnsRecordSet`. Este cmdlet devolve um objeto local que representa o conjunto de registos no DNS do Azure.

Tal como com `New-AzureRmDnsRecordSet`, o nome do conjunto de registos especificado tem de ser um *relativo* nome, o que significa que tem de excluir o nome da zona. Também tem de especificar o tipo de registo e o conjunto de zona que contém o registo.

O exemplo seguinte mostra como obter um conjunto de registos. Neste exemplo, a zona for especificada utilizando o `-ZoneName` e `-ResourceGroupName` parâmetros.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, também pode especificar a zona com um objeto de zona, transmitido utilizando o `-Zone` parâmetro.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista os conjuntos de registos

Também pode utilizar `Get-AzureRmDnsZone` lista a conjuntos de registos numa zona, omitindo a `-Name` e/ou `-RecordType` parâmetros.

O exemplo seguinte devolve conjuntos de todos os registos na zona:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

O exemplo seguinte mostra como todos os conjuntos de registos de um determinado tipo pode ser obtido ao especificar o tipo de registo enquanto o nome do conjunto de omitindo o registo:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para obter todos os conjuntos de registos com um nome específico, em tipos de registos, terá de obter todos os conjuntos de registos e, em seguida, filtrar os resultados:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Nos exemplos acima, a zona pode ser especificada através da utilização de `-ZoneName` e `-ResourceGroupName`parâmetros (conforme ilustrado), ou especificando um objeto de zona:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicione um registo para um conjunto de registos existente

Para adicionar um registo para um conjunto de registos existente, siga os seguintes três passos:

1. Obtenha o conjunto de registo existente

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo registo para o conjunto de registos local. Esta é uma operação offline.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Consolide a alteração novamente para o serviço DNS do Azure. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Utilizar `Set-AzureRmDnsRecordSet` *substitui* existente conjunto de registos no DNS do Azure (e todos os registos contém) com o conjunto de registos especificado. [Verificações de ETag](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são substituídas. Pode utilizar o opcional `-Overwrite` comutador para suprimir estas verificações.

Esta sequência de operações também pode ser *direcionado*, que significa passar o objeto de conjunto de registos ao utilizar o pipe em vez de o transmitir como um parâmetro:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Os exemplos acima mostram como adicionar um registo "A" para um conjunto de registos existente do tipo 'A'. Uma sequência semelhante de operações é utilizada para adicionar registos a conjuntos de registos de outros tipos, substituindo o `-Ipv4Address` parâmetro `Add-AzureRmDnsRecordConfig` com outros parâmetros específicos para cada tipo de registo. Os parâmetros para cada tipo de registo são os mesmos que para o `New-AzureRmDnsRecordConfig` cmdlet, conforme mostrado no [exemplos de tipo de registo adicionais](#additional-record-type-examples) acima.

Os conjuntos de registos do tipo 'CNAME' ou 'SOA' não podem conter mais de um registo. Esta restrição for das normas DNS. Não é uma limitação do DNS do Azure.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registo de um conjunto de registos existente

O processo para remover um registo de um conjunto de registos é semelhante ao processo de adicionar um registo para um conjunto de registos existente:

1. Obtenha o conjunto de registo existente

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Remova o registo o objeto do conjunto de registos local. Esta é uma operação offline. O registo que está a ser removido tem de ser uma correspondência exata com um registo existente em todos os parâmetros.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Consolide a alteração novamente para o serviço DNS do Azure. Utilize a opção `-Overwrite` comutador para suprimir [Etag verifica](dns-zones-records.md#etags) alterações em simultâneo.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

Utilizar a sequência de acima para remover o último registo de um conjunto de registos não elimina o conjunto de registos, em vez disso, deixa um conjunto de registos vazio. Para remover um inteiramente conjunto de registos, consulte [eliminar um conjunto de registos](#delete-a-record-set).

Da mesma forma para adicionar registos a um conjunto de registos, a sequência de operações para remover um conjunto de registos pode também ser direcionada:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

São suportados tipos de registo diferentes transferindo os parâmetros específicos do tipo adequados para `Remove-AzureRmDnsRecordSet`. Os parâmetros para cada tipo de registo são os mesmos que para o `New-AzureRmDnsRecordConfig` cmdlet, conforme mostrado no [exemplos de tipo de registo adicionais](#additional-record-type-examples) acima.


## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registos existente

Os passos para modificar um conjunto de registos existente são semelhantes para os passos que efetuar quando adicionar ou remover registos de um conjunto de registos:

1. Obter conjunto através de registos existente `Get-AzureRmDnsRecordSet`.
2. Modificar o objecto do conjunto de registos local por:
    * Adição ou remoção de registos
    * Alteração dos parâmetros de registos existentes
    * Alterar o registo como metadados e a hora em direto (TTL)
3. Consolidar as alterações utilizando o `Set-AzureRmDnsRecordSet` cmdlet. Isto *substitui* existente conjunto de registos no DNS do Azure com o conjunto de registos especificado.

Quando utilizar `Set-AzureRmDnsRecordSet`, [Etag verifica](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são substituídas. Pode utilizar o opcional `-Overwrite` comutador para suprimir estas verificações.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um registo de um conjunto de registos existente

Neste exemplo, vamos alterar o endereço IP de "Um" registo existente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Não é possível adicionar ou remover os registos do registo SOA criado automaticamente definido no vértice da zona (`-Name "@"`, incluindo as marcas de aspas). No entanto, pode modificar qualquer dos parâmetros dentro o registo SOA (exceto "anfitrião") e o registo de definir o valor de TTL.

O exemplo seguinte mostra como alterar o *E-Mail* propriedade do registo SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>A modificar registos NS no vértice da zona

O conjunto no vértice da zona de registos NS é criado automaticamente com cada zona DNS. Contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Pode adicionar servidores para este registo NS definidas, para suportar os domínios de alojamento conjunta com mais do que um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes de DNS do Azure pré-preenchidos.

Tenha em atenção que isto aplica-se apenas ao registo NS definido no vértice da zona. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser modificados sem restrição.

O exemplo seguinte mostra como adicionar um servidor de nome adicionais para o conjunto no vértice da zona de registos NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar os metadados do conjunto de registos

[Os metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor.

O exemplo seguinte mostra como modificar os metadados de um conjunto de registos existente:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Eliminar um conjunto de registos

Os conjuntos de registos podem ser eliminados utilizando o `Remove-AzureRmDnsRecordSet` cmdlet. Também um conjunto de registos a eliminar elimina todos os registos no conjunto de registos.

> [!NOTE]
> Não é possível eliminar o SOA e conjuntos de registos do NS no vértice da zona (`-Name '@'`).  O DNS do Azure criado-estes automaticamente quando a zona foi criada e elimina automaticamente quando a zona é eliminada.

O exemplo seguinte mostra como eliminar um conjunto de registos. Neste exemplo, o nome do conjunto de registos, tipo de conjunto de registos, nome da zona e grupo de recursos são cada especificadas explicitamente.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, o conjunto de registos pode ser especificado pelo nome e tipo e o horário especificado utilizando um objeto:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como uma terceira opção, próprio conjunto de registos podem ser especificado um objeto do conjunto de registos:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Quando especificar o conjunto ser eliminado utilizando um objeto de conjunto de registos de registos [Etag verifica](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são eliminadas. Pode utilizar o opcional `-Overwrite` comutador para suprimir estas verificações.

Também pode ser direcionado o objeto do conjunto de registos em vez de ser transmitida como um parâmetro:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Pedidos de confirmação

O `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet`, e `Remove-AzureRmDnsRecordSet` todos os cmdlets suporta pedidos de confirmação.

Cada cmdlet solicita a confirmação se o `$ConfirmPreference` variável de preferência de PowerShell tem um valor de `Medium` ou inferior. Dado que o valor predefinido para `$ConfirmPreference` é `High`, estas instruções não são fornecidas ao utilizar as predefinições do PowerShell.

Pode substituir atual `$ConfirmPreference` definição a utilizar o `-Confirm` parâmetro. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe confirmação antes de é executado. Se especificar `-Confirm:$False` , o cmdlet não solicitar confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, consulte [sobre variáveis de preferência](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [registos no DNS do Azure e zonas](dns-zones-records.md).
<br>
Saiba como [proteger os seus zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o DNS do Azure.
<br>
Reveja o [documentação de referência do PowerShell de DNS do Azure](/powershell/module/azurerm.dns).
