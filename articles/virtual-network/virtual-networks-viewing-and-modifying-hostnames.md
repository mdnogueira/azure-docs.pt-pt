---
title: "Ver e modificar os nomes de anfitrião | Microsoft Docs"
description: "Como ver e alterar os nomes de anfitrião para máquinas virtuais do Azure, web e funções de trabalho para resolução de nomes"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.openlocfilehash: 9a3a1e1b58dcb828e2d2d09c18f1aab6d46051aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="viewing-and-modifying-hostnames"></a>Ver e modificar os nomes de anfitrião
Para permitir que as instâncias da função ser referenciado por nome de anfitrião, tem de definir o valor para o nome de anfitrião no ficheiro de configuração do serviço para cada função. Fazê-lo ao adicionar o nome de anfitrião pretendido para o **vmName** atributo o **função** elemento. O valor da **vmName** atributo é utilizado como base para o nome de anfitrião de cada instância de função. Por exemplo, se **vmName** é *webrole* e existem três instâncias de função, os nomes de anfitrião das instâncias será *webrole0*, *webrole1*, e *webrole2*. Não tem de especificar um nome de anfitrião para máquinas virtuais no ficheiro de configuração, porque o nome de anfitrião para uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre como configurar um serviço do Microsoft Azure, consulte [esquema de configuração de serviço do Azure (. cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Ver os nomes de anfitrião
Pode ver os nomes de anfitrião de máquinas virtuais e instâncias de função no serviço em nuvem, utilizando qualquer uma das ferramentas abaixo.

### <a name="azure-portal"></a>Portal do Azure
Pode utilizar o [portal do Azure](http://portal.azure.com) para ver os nomes de anfitrião para máquinas virtuais no painel de descrição geral de uma máquina virtual. Tenha em atenção que o painel mostra um valor para **nome** e **nome de anfitrião**. Apesar de estarem inicialmente o mesmo, a alteração do nome de anfitrião não irá alterar o nome da máquina virtual ou instância de função.

Instâncias de função também podem ser visualizadas no portal do Azure, mas quando listar as instâncias de um serviço em nuvem, o nome do anfitrião não é apresentado. Verá um nome para cada instância, mas esse nome não representa o nome do anfitrião.

### <a name="service-configuration-file"></a>Ficheiro de configuração de serviço
Pode transferir o ficheiro de configuração de serviço para um serviço implementado o **configurar** painel do serviço no portal do Azure. Em seguida, pode procurar o **vmName** atributo para o **nome da função** elemento para ver o nome de anfitrião. Tenha em atenção que este nome de anfitrião é utilizado como base para o nome de anfitrião de cada instância de função. Por exemplo, se **vmName** é *webrole* e existem três instâncias de função, os nomes de anfitrião das instâncias será *webrole0*, *webrole1*, e *webrole2*.

### <a name="remote-desktop"></a>Ambiente de trabalho remoto
Depois de ativar o ambiente de trabalho remoto (Windows), sistema de interação remota do Windows PowerShell (Windows) ou ligações SSH (Linux e Windows) a máquinas virtuais ou instâncias de função, pode ver o nome de anfitrião a partir de uma ligação de ambiente de trabalho remoto Active Directory de várias formas:

* Escreva o nome do anfitrião na linha de comandos ou do terminal SSH.
* Escreva o ipconfig/todas as no comando (apenas Windows) de linha de comandos.
* Ver o nome do computador em que as definições do sistema (apenas Windows).

### <a name="azure-service-management-rest-api"></a>REST API de gestão de serviço do Azure
De um cliente REST, siga estas instruções:

1. Certifique-se de que tem um certificado de cliente para ligar ao portal do Azure. Para obter um certificado de cliente, siga os passos apresentados na [como: transferir e importar definições de publicação e informações de subscrição](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Defina uma entrada de cabeçalho x-ms-version com um valor de 2013-11-01 com o nome.
3. Enviar um pedido no seguinte formato: https://management.core.windows.net/\<subscrition id\>/services/hostedservices/\<nome do serviço\>? detalhes incorporar = true
4. Procure o **HostName** elemento para cada **RoleInstance** elemento.

> [!WARNING]
> Também pode ver o sufixo de domínio interno do serviço em nuvem da resposta de chamada REST, verificando o **InternalDnsSuffix** elemento, ou executando o ipconfig/todas as numa linha de comandos numa sessão de ambiente de trabalho remoto (Windows) ou executando cat /etc/resolv.conf de um terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modificar um nome de anfitrião
Pode modificar o nome de anfitrião para qualquer máquina virtual ou instância de função através do carregamento de um ficheiro de configuração do serviço modificado ou mudar o nome do computador a partir de uma sessão de ambiente de trabalho remoto.

## <a name="next-steps"></a>Passos seguintes
[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração do serviço do Azure (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Especifique as definições de DNS a utilizar ficheiros de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

