---
title: Gerir registos DNS no DNS do Azure utilizando a CLI do Azure 1.0 | Microsoft Docs
description: "Gerir conjuntos de registos de DNS e registos DNS do Azure ao alojamento do seu domínio no DNS do Azure. Todos os comandos de CLI 1.0 para operações de conjuntos de registos e registos."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
ms.openlocfilehash: 307b327e4c04a0461e39930114eb193791cbda9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Gerir registos DNS no DNS do Azure utilizando a CLI do Azure 1.0

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerir registos DNS para a sua zona DNS ao utilizar a interface várias plataformas do Azure da linha de comandos (CLI), que está disponível para o Windows, Mac e Linux. Também pode gerir os registos DNS utilizando [Azure PowerShell](dns-operations-recordsets.md) ou [portal do Azure](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

* [CLI 1.0 do Azure](dns-operations-recordsets-cli-nodejs.md) - CLI para os modelos de implementação de gestão clássica e de recursos.
* [CLI 2.0 do Azure](dns-operations-recordsets-cli.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos.

Os exemplos deste artigo partem do princípio de que já tem [instalado Azure CLI 1.0, tem sessão iniciada e criar uma zona DNS](dns-operations-dnszones-cli-nodejs.md).

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o comando `azure network dns record-set add-record`. Para obter ajuda, consulte `azure network dns record-set add-record -h`.

Ao criar um registo, tem de especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de recursos, o tipo de registo e os detalhes do registo que está a ser criado. O nome do conjunto de registos especificado tem de ser um *relativo* nome, o que significa que tem de excluir o nome da zona.

Se o conjunto de registos ainda não existir, este comando cria-o por si. Se já existir um conjunto de registos, este comando adda o que especificar ao registo existente do conjunto de registos.

Se for criado um novo conjunto de registos, é utilizado um TTL (time-to-live) predefinido de 3600. Para obter instruções sobre como utilizar TTLs diferentes, consulte [criar um conjunto de registos DNS](#create-a-dns-record-set).

O exemplo seguinte cria um registo A denominado *www* na zona *contoso.com* do grupo de recursos *MyResourceGroup*. O endereço IP do registo A é *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para criar um registo no vértice da zona (neste caso, "contoso.com"), utilize o nome do registo "@", incluindo as aspas:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Criar um conjunto de registos de DNS

Nos exemplos acima, o registo DNS ou foi adicionado a um conjunto de registo existente ou o conjunto de registos foi criado *implicitamente*. Também pode criar o conjunto de registos *explicitamente* antes de adicionar registos ao mesmo. O DNS do Azure suporta conjuntos de registos 'empty', que podem atuar como um marcador de posição para reservar um nome DNS antes de criar registos DNS. Os conjuntos de registos vazios são visíveis no plane de controlo de DNS do Azure, mas não aparecem nos servidores de nome DNS do Azure.

Os conjuntos de registos são criados utilizando o `azure network dns record-set create` comando. Para obter ajuda, consulte `azure network dns record-set create -h`.

Criar o registo de definir explicitamente permite-lhe especificar o conjunto de registos propriedades, tais como o [Time-To-Live (TTL)](dns-zones-records.md#time-to-live) e metadados. [Os metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor.

O exemplo seguinte cria um conjunto com um valor de TTL 60 segundo, através de registos vazio o `--ttl` parâmetro (formato curto `-l`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

O exemplo seguinte cria um conjunto de registos com duas entradas de metadados, "Depart = financeiro" e "ambiente = produção", utilizando o `--metadata` parâmetro (formato curto `-m`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

Ter criado um conjunto de registos vazio, registos podem ser adicionados utilizando `azure network dns record-set add-record` conforme descrito em [criar um registo DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos de

Ter visto em detalhe como '' registos de criar, os exemplos seguintes mostram como criar registo de outros tipos de registos suportado pelo DNS do Azure.

Os parâmetros utilizados para especificar os dados de registo variam consoante o tipo de registo. Por exemplo, para um registo do tipo "A", especifica o endereço IPv4 com o parâmetro `-a <IPv4 address>`. Os parâmetros para cada tipo de registo podem ser apresentados utilizando `azure network dns record-set add-record -h`.

Em cada caso, vamos mostrar como criar um único registo. O registo é adicionado ao conjunto de registos existente ou um conjunto de registos criados implicitamente. Para obter mais informações sobre como criar conjuntos de registos e definir o registo de parâmetro explicitamente definido, consulte [criar um conjunto de registos DNS](#create-a-dns-record-set).

Não iremos dar um exemplo para criar um conjunto de registos SOA, uma vez que são criados SOAs e eliminada com cada zona DNS e não pode ser criada ou eliminada separadamente. No entanto, [o SOA pode ser modificada, conforme mostrado no exemplo posterior](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Criar um registo AAAA

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-cname-record"></a>Crie um registo CNAME

> [!NOTE]
> As normas DNS não permite registos CNAME no vértice da zona de uma (`-Name "@"`), nem permitem a conjuntos de registos que contêm mais de um registo.
> 
> Para obter mais informações, consulte [registos CNAME](dns-zones-records.md#cname-records).

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Criar um registo MX

Neste exemplo, utilizamos o nome do conjunto de registos “@” para criar o registo MX no vértice da zona (neste caso, “contoso.com”).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Criar um registo NS

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Criar um registo PTR

Neste caso, "os meus-arpa-zone.com' representa a zona ARPA que representa o intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs.  O nome do registo '10' é o último octeto do endereço IP neste intervalo de IP representado por este registo.

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>Criar um registo SRV

Ao criar um [conjunto de registos SRV](dns-zones-records.md#srv-records), especifique o  *\_serviço* e  *\_protocolo* no nome do conjunto de registos. Não é necessário incluir "@" no nome do conjunto de registos quando criar um registo SRV definido no vértice da zona.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>Criar um registo TXT

O exemplo seguinte mostra como criar um registo TXT. Para obter mais informações sobre o comprimento da cadeia máxima suportada nos registos TXT, consulte [registos TXT](dns-zones-records.md#txt-records).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>Obter um conjunto de registos

Para obter um conjunto de registos existente, utilize `azure network dns record-set show`. Para obter ajuda, consulte `azure network dns record-set show -h`.

Como ao criar um registo ou um conjunto de registos, o nome do conjunto de registos especificado tem de ser um *relativo* nome, o que significa que tem de excluir o nome da zona. Também tem de especificar o tipo de registo, a zona que contém o conjunto de registos e o grupo de recursos que contém a zona.

O exemplo seguinte obtém o registo *www* de um tipo de zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>Lista os conjuntos de registos

Pode listar todos os registos numa zona DNS ao utilizar o `azure network dns record-set list` comando. Para obter ajuda, consulte `azure network dns record-set list -h`.

Neste exemplo devolve conjuntos de todos os registos na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, independentemente do nome ou o tipo de registo:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

Neste exemplo devolve todos os conjuntos de registos que corresponde ao tipo de registo fornecido (neste caso, '' registos):

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicione um registo para um conjunto de registos existente

Pode utilizar `azure network dns record-set add-record` ambos para criar um registo num novo conjunto de registos ou para adicionar um registo a um existente conjunto de registos.

Para obter mais informações, consulte [criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos de](#create-records-of-other-types) acima.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remova um registo de um conjunto de registos existente.

Para remover um registo DNS de um conjunto de registos existente, utilize `azure network dns record-set delete-record`. Para obter ajuda, consulte `azure network dns record-set delete-record -h`.

Este comando elimina um registo DNS de um conjunto de registos. Se for eliminado o último registo num conjunto de registos, o próprio conjunto de registos é **não** eliminado. Em vez disso, um conjunto de registos vazio é à esquerda. Para eliminar o conjunto em vez disso, de registos, consulte [eliminar um conjunto de registos](#delete-a-record-set).

Tem de especificar o registo ser eliminado e a zona deve ser eliminada, os mesmos parâmetros a utilizar quando criar um registo utilizando `azure network dns record-set add-record`. Estes parâmetros são descritos nas [criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos de](#create-records-of-other-types) acima.

Este comando solicita a confirmação. Esta linha de comandos pode ser suprimida utilizando o `--quiet` mudar (formato curto `-q`).

O exemplo seguinte elimina o registo com o valor '1.2.3.4' do registo com o conjunto nomeado *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*. O pedido de confirmação é suprimido.

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registos existente

Cada conjunto de registos contém uma [time-to-live (TTL)](dns-zones-records.md#time-to-live), [metadados](dns-zones-records.md#tags-and-metadata)e registos DNS. As secções seguintes explicam como modificar cada uma destas propriedades.

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar um registo A, AAAA, MX, NS, PTR, SRV ou TXT

Para modificar um registo existente do tipo A, AAAA, MX, NS, PTR, SRV ou TXT, deve primeiro, adicione um novo registo e, em seguida, elimine o registo existente. Para obter instruções detalhadas sobre como eliminar e adicionar registos, consulte as secções anteriores deste artigo.

O exemplo seguinte mostra como modificar um "A" registo do endereço IP 1.2.3.4 para o endereço IP 5.6.7.8:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>Para modificar um registo CNAME

Para modificar um registo CNAME, utilize `azure network dns record-set add-record` para adicionar o novo valor de registo. Ao contrário de outros tipos de registos, um conjunto de registos CNAME só pode conter um único registo. Por conseguinte, o registo existente é *substituído* quando é adicionado o novo registo e não precisa de ser eliminado em separado.  Será solicitado para aceitar esta substituição.

O exemplo modifica o conjunto de registos CNAME *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, para que apontem para www.fabrikam.net em vez do valor existente:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Utilize `azure network dns record-set set-soa-record` para modificar o SOA para uma determinada zona DNS. Para obter ajuda, consulte `azure network dns record-set set-soa-record -h`.

O exemplo seguinte mostra como definir a propriedade 'correio eletrónico' do registo SOA da zona de *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>A modificar registos NS no vértice da zona

O conjunto no vértice da zona de registos NS é criado automaticamente com cada zona DNS. Contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Pode adicionar servidores para este registo NS definidas, para suportar os domínios de alojamento conjunta com mais do que um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes de DNS do Azure pré-preenchidos.

Tenha em atenção que isto aplica-se apenas ao registo NS definido no vértice da zona. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser modificados sem restrição.

O exemplo seguinte mostra como adicionar um servidor de nome adicionais para o conjunto no vértice da zona de registos NS:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Para modificar o valor de TTL de um conjunto de registos existente

Para modificar o valor de TTL de um conjunto de registos existente, utilize `azure network dns record-set set`. Para obter ajuda, consulte `azure network dns record-set set -h`.

O exemplo seguinte mostra como modificar um conjunto de registos TTL, neste caso, a 60 segundos:

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar os metadados de um conjunto de registos existente

[Os metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor. Para modificar os metadados de um conjunto de registos existente, utilize `azure network dns record-set set`. Para obter ajuda, consulte `azure network dns record-set set -h`.

O exemplo seguinte mostra como modificar um conjunto de registos com duas entradas de metadados, "Depart = financeiro" e "ambiente = produção", utilizando o `--metadata` parâmetro (formato curto `-m`). Tenha em atenção que quaisquer metadados existente *substituído* pelos valores fornecidos.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>Eliminar um conjunto de registos

Os conjuntos de registos podem ser eliminados utilizando o `azure network dns record-set delete` comando. Para obter ajuda, consulte `azure network dns record-set delete -h`. Também um conjunto de registos a eliminar elimina todos os registos no conjunto de registos.

> [!NOTE]
> Não é possível eliminar o SOA e conjuntos de registos do NS no vértice da zona (`-Name "@"`).  Estes são criados automaticamente quando a zona foi criada e são eliminados automaticamente quando a zona é eliminada.

O exemplo seguinte elimina o conjunto nomeado de registos *www* de tipo A partir da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

Lhe for pedido para confirmar a operação de eliminação. Para suprimir esta linha de comandos, utilize o `--quiet` mudar (formato curto `-q`).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [registos no DNS do Azure e zonas](dns-zones-records.md).
<br>
Saiba como [proteger os seus zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o DNS do Azure.
