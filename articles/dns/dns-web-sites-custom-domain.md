---
title: "Criar registos DNS personalizados para uma aplicação web | Microsoft Docs"
description: "Como criar o domínio personalizado registos DNS para a aplicação web utilizando o DNS do Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d4b0aa817c3fd7f3304b5122ac584166d8079d3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Criar registos DNS para uma aplicação web num domínio personalizado

Pode utilizar o DNS do Azure para alojar um domínio personalizado para as suas aplicações web. Por exemplo, estiver a criar uma aplicação web do Azure e pretende que os utilizadores para aceder ao mesmo ao utilizar contoso.com ou www.contoso.com como um FQDN.

Para tal, tem de criar dois registos:

* Um registo de raiz "A", apontando para contoso.com
* Um registo "CNAME" para o nome de www que aponta para o registo

Tenha em atenção que se cria um registo a para uma aplicação web no Azure, o registo tem de ser manualmente atualizados se o subjacente de endereços IP para as alterações de aplicação web.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, tem primeiro de criar uma zona DNS no DNS do Azure e delegar a zona na sua entidade de registo ao DNS do Azure.

1. Para criar uma zona DNS, siga os passos em [criar uma zona DNS](dns-getstarted-create-dnszone.md).
2. Para delegar o seu DNS ao DNS do Azure, siga os passos no [delegação de domínio DNS](dns-domain-delegation.md).

Depois de criar uma zona e delegá-lo ao DNS do Azure, em seguida, pode criar registos para o seu domínio personalizado.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Criar um registo a para o domínio personalizado

Um registo é utilizado para mapear um nome para o endereço IP. No exemplo a seguir vamos atribuirá como um registo para um endereço IPv4:

### <a name="step-1"></a>Passo 1

Criar um registo a e atribuir a uma variável $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Passo 2

Adicione o valor de IPv4 ao conjunto de registos criado anteriormente "@" utilizando a variável de $rs atribuída. O valor de IPv4 atribuído será o endereço IP para a sua aplicação web.

Para localizar o endereço IP de uma aplicação web, siga os passos no [configurar um nome de domínio personalizado no App Service do Azure](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Passo 3

Consolide as alterações para o conjunto de registos. Utilize `Set-AzureRMDnsRecordSet` para carregar as alterações ao conjunto ao DNS do Azure de registos:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Criar um registo CNAME para o domínio personalizado

Se o seu domínio já está a ser gerido pelo DNS do Azure (consulte [delegação de domínio DNS](dns-domain-delegation.md), pode utilizar o seguinte o exemplo para criar um registo CNAME para contoso.azurewebsites.net.

### <a name="step-1"></a>Passo 1

Abra o PowerShell e criar um novo conjunto de registos CNAME e atribuir a uma variável $rs. Neste exemplo irá criar um tipo de conjunto de registos CNAME com um"TTL" de 600 segundos na zona DNS com o nome "contoso.com".

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

O exemplo seguinte é a resposta.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Passo 2

Assim que for criado o conjunto de registos CNAME, terá de criar um valor de alias que irá apontar para a aplicação web.

Utilizar a variável anteriormente atribuída "$rs" pode utilizar o comando do PowerShell abaixo para criar o alias para contoso.azurewebsites.net de aplicação web.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

O exemplo seguinte é a resposta.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Passo 3

Confirmar as alterações utilizando o `Set-AzureRMDnsRecordSet` cmdlet:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Pode validar o registo foi criado corretamente consultando com nslookup, "www.contoso.com", conforme mostrado abaixo:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Crie um registo de "awverify" para as aplicações web

Se optar por utilizar um registo para a sua aplicação web, tem de seguir um processo de verificação para se certificar de que possui o domínio personalizado. Este passo de verificação é feito através da criação de um registo CNAME especial com o nome "awverify". Esta secção aplica-se apenas um registos.

### <a name="step-1"></a>Passo 1

Crie o registo de "awverify". No exemplo abaixo, iremos criar o registo de "aweverify" para contoso.com verificar a propriedade para o domínio personalizado.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

O exemplo seguinte é a resposta.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Passo 2

Quando o conjunto de registos "awverify" for criado, atribua o registo CNAME, definido o alias. No exemplo abaixo, iremos atribuirá o registo CNAMe como alias awverify.contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

O exemplo seguinte é a resposta.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Passo 3

Confirmar as alterações utilizando o `Set-AzureRMDnsRecordSet cmdlet`, conforme mostrado no comando abaixo.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Passos seguintes

Siga os passos no [configurar um nome de domínio personalizado para o App Service](../app-service/app-service-web-tutorial-custom-domain.md) para configurar a sua aplicação web para utilizar um domínio personalizado.
