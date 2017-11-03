---
title: "Importar e exportar um ficheiro de zona de domínio ao DNS do Azure utilizando a CLI do Azure 1.0 | Microsoft Docs"
description: Saiba como importar e exportar um ficheiro de zona DNS ao DNS do Azure utilizando a CLI do Azure 1.0
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d6d3fa7aa0e8b2462b3a6b4b66d3d87ab5535314
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-10"></a>Importar e exportar um ficheiro de zona DNS utilizando a CLI do Azure 1.0 

Este artigo explica como importar e exportar ficheiros de zona DNS para o DNS do Azure utilizando a CLI do Azure 1.0.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração de zona DNS

Um ficheiro de zona DNS é um ficheiro de texto que contém os detalhes de cada registo de sistema de nomes de domínio (DNS) na zona. Segue um formato de padrão, tornando adequado para transferir os registos DNS entre sistemas DNS. Utilizando um ficheiro de zona é uma forma rápida, fiável e conveniente para transferir uma zona DNS ou a sair DNS do Azure.

O DNS do Azure suporta a importar e exportar ficheiros de zona, utilizando a interface de linha de comandos do Azure (CLI). Importar de ficheiro de zona é **não** atualmente suportados através do Azure PowerShell ou o portal do Azure.

A CLI do Azure 1.0 é uma ferramenta de linha de comandos de várias plataformas utilizada para gerir os serviços do Azure. Está disponível para as plataformas Windows, Mac e Linux do [página de transferências do Azure](https://azure.microsoft.com/downloads/). Suporte em várias plataformas é importante para importar e exportar ficheiros de zona, porque o software de servidor de nome mais comuns, [VINCULAR](https://www.isc.org/downloads/bind/), normalmente, é executado no Linux.

> [!NOTE]
> Não existem atualmente duas versões da CLI do Azure. CLI1.0 baseia-se no Node.js e tem de comandos a partir "azure".
> CLI2.0 baseia-se no Python e tem de começar com "az" de comandos. Ao importar de ficheiro de zona é suportado em ambas as versões, recomendamos que utilize os comandos CLI1.0, conforme descrito nesta página.

## <a name="obtain-your-existing-dns-zone-file"></a>Obter o ficheiro de zona DNS existente

Antes de importar um ficheiro de zona DNS no DNS do Azure, terá de obter uma cópia do ficheiro de zona. A origem deste ficheiro depende em que a zona DNS está alojada atualmente.

* Se a zona DNS for alojada por um serviço de parceiro (por exemplo, um de domínios, o fornecedor de alojamento DNS dedicado ou o fornecedor de nuvem alternativo), que o serviço deve fornecer a capacidade para transferir o ficheiro de zona DNS.
* Se a sua zona DNS estiver alojada no DNS do Windows, a pasta predefinida para os ficheiros de zona é **%systemroot%\system32\dns**. O caminho completo para cada ficheiro de zona também mostra no **geral** separador da consola do DNS.
* Se a sua zona DNS estiver alojada utilizando o ENLACE, a localização do ficheiro de zona para cada zona é especificada no ficheiro de configuração de ENLACE **named.conf**.

> [!NOTE]
> Ficheiros de zona transferidos da GoDaddy tem um formato ligeiramente não padrão. É necessário corrigir isto, antes de importar estes ficheiros de zona no DNS do Azure.
>
> Nomes DNS no RDATA de cada registo DNS estão especificados como nomes totalmente qualificados, mas não têm um terminar "." Isto significa que são interpretados por outros sistemas DNS como nomes relativos. Terá de editar o ficheiro de zona para acrescentar a terminação "." para os respetivos nomes antes de importá-los no DNS do Azure.
>
> Por exemplo, o registo CNAME "www 3600 em CNAME contoso.com" deve ser alterado para "" www"3600 em CNAME contoso.com.
> (com um terminar ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um ficheiro de zona DNS para o DNS do Azure

Importar um ficheiro de zona cria uma nova zona no DNS do Azure se ainda não existir. Se a zona já existir, os conjuntos de registos no ficheiro de zona têm de ser intercalados com os conjuntos de registos existentes.

### <a name="merge-behavior"></a>Comportamento de intercalação

* Por predefinição, os conjuntos de registos existentes e novos são intercalados. Os registos idênticos dentro de um conjunto de registo intercalado são anular duplicados.
* Em alternativa, especificando o `--force` opção substitui o processo de importação de conjuntos de registos existente com novos conjuntos de registos. Conjuntos de registos existentes que não têm um registo correspondente definido no ficheiro de zona importados não são removidos.
* Quando os conjuntos de registos são intercalados, será utilizada a hora para em direto (TTL) pré-existentes de conjuntos de registos. Quando `--force` é utilizado, é utilizado o valor de TTL do novo conjunto de registo.
* Início de parâmetros de autoridade (SOA) (exceto `host`) sempre são obtidas a partir do ficheiro de zona importados, independentemente `--force` é utilizado. Da mesma forma, para o nome servidor conjunto de registos no vértice da zona, o valor de TTL sempre é retirado do ficheiro de zona importado.
* Um registo CNAME importado não substituir um registo CNAME existente com o mesmo nome, a menos que o `--force` parâmetro for especificado.
* Quando for um conflito entre um registo CNAME e outro registo do mesmo nome mas tipo diferente (independentemente de qual é existente ou nova), o registo existente é mantido. Isto é independente da utilização de `--force`.

### <a name="additional-information-about-importing"></a>Informações adicionais sobre como importar

As seguintes notas fornecem detalhes técnicos adicionais sobre a zona importar processo.

* O `$TTL` diretiva é opcional e é suportada. Quando não existe nenhum `$TTL` diretiva é atribuída, registos sem um valor de TTL explícito são importado definido como default TTL de 3600 segundos. Quando dois registos no mesmo conjunto de registo especificar TTLs diferentes, é utilizado o valor mais baixo.
* O `$ORIGIN` diretiva é opcional e é suportada. Quando não existe nenhum `$ORIGIN` é definido, utilizado o valor predefinido é o nome de zona, conforme especificado na linha de comandos (juntamente com a terminação ".").
* O `$INCLUDE` e `$GENERATE` diretivas não são suportadas.
* Estes tipos de registos são suportados: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.
* O registo SOA é automaticamente criado pelo DNS do Azure quando é criada uma zona. Quando importar um ficheiro de zona, todos os parâmetros SOA são obtidos a partir do ficheiro de zona *exceto* o `host` parâmetro. Este parâmetro utiliza o valor fornecido pelo DNS do Azure. Isto acontece porque este parâmetro deve referir-se para o servidor primário de nome fornecido pelo DNS do Azure.
* O registo do servidor de nome definido no vértice da zona é também criado automaticamente pelo DNS do Azure quando a zona é criada. Apenas o valor de TTL deste conjunto de registos é importado. Estes registos incluem os nomes de servidor de nome fornecidos pelo DNS do Azure. Os dados de registo não são substituídos pelos valores contidos no ficheiro de zona importado.
* Durante a pré-visualização pública, o DNS do Azure suporta apenas registos TXT de cadeia única. Os registos TXT multistring são ser concatenado e truncado de 255 carateres.

### <a name="cli-format-and-values"></a>Formato CLI e valores

O formato do comando CLI do Azure para importar uma zona DNS é:

```azurecli
azure network dns zone import [options] <resource group> <zone name> <zone file name>
```

Valores:

* `<resource group>`é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>`é o nome da zona.
* `<zone file name>`é o caminho de nome de ficheiro de zona para ser importado.

Se não existir uma zona com este nome no grupo de recursos, é criado para si. Se a zona já existir, os conjuntos de registos importados são intercalados com os conjuntos de registos existentes. Para substituir os conjuntos de registos existentes, utilize o `--force` opção.

Para verificar o formato de um ficheiro de zona sem realmente importá-lo, utilize o `--parse-only` opção.

### <a name="step-1-import-a-zone-file"></a>Passo 1. Importar um ficheiro de zona

Para importar um ficheiro de zona para a zona **contoso.com**.

1. Inicie sessão sua subscrição do Azure utilizando a CLI do Azure 1.0.

    ```azurecli
    azure login
    ```

2. Selecione a subscrição em que pretende criar a sua nova zona DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. O DNS do Azure é um serviço de só de Gestor de recursos do Azure, para a CLI do Azure tem de ser mudada para o modo Resource Manager.

    ```azurecli
    azure config mode arm
    ```

4. Antes de utilizar o serviço DNS do Azure, tem de registar a subscrição para utilizar o fornecedor de recursos Network. (Esta é uma operação única para cada subscrição.)

    ```azurecli
    azure provider register Microsoft.Network
    ```

5. Se ainda não tiver uma, também terá de criar um grupo de recursos do Resource Manager.

    ```azurecli
    azure group create myresourcegroup westeurope
    ```

6. Para importar a zona **contoso.com** do ficheiro **contoso.com.txt** para uma nova zona DNS no grupo de recursos **myresourcegroup**, execute o comando `azure network dns zone import`.<BR>Este comando carrega o ficheiro de zona e analisá-lo. O comando executa uma série de comandos no serviço DNS do Azure para criar a zona e define todos os registos na zona. O comando comunica o progresso na janela da consola, juntamente com quaisquer erros ou avisos. Porque os conjuntos de registos são criados em série, poderá demorar alguns minutos para importar um ficheiro de zona grandes.

    ```azurecli
    azure network dns zone import myresourcegroup contoso.com contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Passo 2. Certifique-se a zona

Para verificar a zona DNS depois de importar o ficheiro, pode utilizar qualquer um dos seguintes métodos:

* Pode listar os registos ao utilizar o seguinte comando da CLI do Azure:

    ```azurecli
    azure network dns record-set list myresourcegroup contoso.com
    ```

* Pode listar os registos, utilizando o cmdlet do PowerShell `Get-AzureRmDnsRecordSet`.
* Pode utilizar `nslookup` para verificar a resolução de nomes para os registos. Porque a zona não delegada ainda, tem de especificar os servidores de nomes de DNS do Azure corretos explicitamente. O exemplo seguinte mostra como obter os nomes de servidor atribuídos à zona. IT também mostra como consultar os registos "www" utilizando `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/.../resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Passo 3. Atualizar delegação de DNS

Depois de ter verificado que a zona foi importada corretamente, terá de atualizar a delegação de DNS para que apontem para os servidores de nomes DNS do Azure. Para obter mais informações, consulte o artigo [atualizar a delegação de DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um ficheiro de zona DNS do DNS do Azure

O formato do comando CLI do Azure para importar uma zona DNS é:

```azurecli
azure network dns zone export [options] <resource group> <zone name> <zone file name>
```

Valores:

* `<resource group>`é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>`é o nome da zona.
* `<zone file name>`é o caminho de nome de ficheiro de zona ser exportada.

Como com a importação de zona, é primeiro tem de iniciar sessão, escolha a sua subscrição e configurar a CLI do Azure para utilizar o modo Resource Manager.

### <a name="to-export-a-zone-file"></a>Para exportar um ficheiro de zona

1. Inicie sessão sua subscrição do Azure utilizando a CLI do Azure.

    ```azurecli
    azure login
    ```

2. Selecione a subscrição em que pretende criar a sua zona DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. O DNS do Azure é um serviço de só de Gestor de recursos do Azure. A CLI do Azure tem de ser mudada para o modo Resource Manager.

    ```azurecli
    azure config mode arm
    ```

4. Para exportar a zona DNS do Azure existente **contoso.com** no grupo de recursos **myresourcegroup** para o ficheiro **contoso.com.txt** (na pasta atual), execute `azure network dns zone export`. Este comando activa o serviço de DNS do Azure para enumerar os conjuntos de registos na zona e exportar os resultados para um ficheiro de zona compatível com o ENLACE.

    ```azurecli
    azure network dns zone export myresourcegroup contoso.com contoso.com.txt
    ```
