---
title: "Introdução ao Azure DNS com o portal do Azure | Microsoft Docs"
description: "Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com o portal do Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: 93b24e3d9fbb3fbb3ea995271fd63d1e82eb9c9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Introdução ao Azure DNS com o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI do Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-cli.md)

Este artigo explica-lhe os passos para criar a sua primeira zona DNS e o registo com o portal do Azure. Também pode executar estes passos com o Azure PowerShell ou a CLI do Azure de várias plataformas.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito nos passos seguintes.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No menu Hub, clique em **Novo > Redes >** e, em seguida, clique em **Zona DNS**, para abrir o painel Criar zona DNS.

    ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localização**|EUA Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="create-a-dns-record"></a>Criar um registo DNS

O exemplo seguinte explica-lhe o processo de criação do novo registo "A". Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md). 

1. Com a Zona DNS criada, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique na zona DNS **contoso.com**, no painel Todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir **contoso.com** na caixa **Filtrar por nome...** para aceder facilmente à Zona DNS.

1. No topo do painel **Zona DNS**, selecione **+ Conjunto de registos**, para abrir o painel **Adicionar conjunto de registos**.

1. No painel **Adicionar conjunto de registos**, introduza os valores seguintes e clique em **OK**. Neste exemplo, está a criar um registo A.

   |**Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|www|Nome do registo|
   |**Tipo**|A| Os tipos de registo DNS para criar valores aceitáveis são A, AAAA, CNAME, MX, NS, SRV, TXT e PTR.  Para mais informações sobre os tipos de registo, visite [Descrição geral das zonas DNS e registos](dns-zones-records.md)|
   |**TTL**|1|Tempo de vida do pedido DNS.|
   |**Unidade de TTL**|Horas|Medida de tempo do valor TTL.|
   |**Endereço IP**|ipAddressValue| Este valor é o endereço IP que o registo DNS resolve.|

## <a name="view-records"></a>Ver registos

Na parte inferior do painel da zona DNS, pode ver os registos para a zona DNS. Deverá ver os registos DNS e SOA predefinidos, que são criados em cada zona, assim como quaisquer novos registos que tenha criado.

![zona](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Atualizar servidores de nomes

Depois de ter a certeza que a sua zona DNS e os registos foram configurados corretamente, tem de configurar o seu nome de domínio para utilizar os servidores de nomes do DNS do Azure. Isto permite que outros utilizadores na Internet encontrem os seus registos DNS.

Os servidores de nomes para a sua zona são indicados no portal do Azure:

![zona](./media/dns-getstarted-portal/viewzonens500.png)

Estes servidores de nomes devem ser configurados com a entidade de registo de nomes de domínio (onde comprou o nome de domínio). A entidade de registo oferece a opção para configurar os servidores de nomes do domínio. Para obter mais informações, consulte [Delegate your domain to Azure DNS (Delegar o seu domínio ao DNS do Azure)](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, conclua os seguintes passos:

1. No painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no grupo de recursos **MyResourceGroup**, no painel Todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir **MyResourceGroup** na caixa **Filtrar por nome...** para aceder facilmente ao grupo de recursos.
1. No painel **MyResourceGroup**, clique no botão **Eliminar**.
1. O portal requer que escreva o nome do grupo de recursos para confirmar que pretende eliminá-lo. Clique em **Eliminar**, escreva *MyResourceGroup* no nome do grupo de recursos e clique em **Eliminar**. A eliminação de um grupo de recursos elimina todos os recursos dentro do grupo de recursos. Por conseguinte, confirme os conteúdos de um grupo de recursos antes de o eliminar. O portal elimina todos os recursos contidos dentro do grupo de recursos e, em seguida, elimina o próprio grupo de recursos. Este processo demora vários minutos.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre DNS do Azure, consulte [Azure DNS overview (Descrição geral do DNS do Azure)](dns-overview.md).

Para saber mais sobre a gestão de registos DNS no DNS do Azure, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

