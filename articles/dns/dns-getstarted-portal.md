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
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 79f0c9297c4be70f705f325274f3d9241ea4bc3f
ms.lasthandoff: 03/29/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Introdução ao Azure DNS com o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI do Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-cli.md)

Este artigo explica-lhe os passos para criar a sua primeira zona DNS e o registo com o portal do Azure. Também pode executar estes passos com o Azure PowerShell ou a CLI do Azure de várias plataformas.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No menu Hub, clique em **Novo > Redes >** e, em seguida, clique em **Zona DNS**, para abrir o painel Criar zona DNS.

    ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

4. No painel **Criar zona DNS**, nomeie a sua zona DNS. Por exemplo, *contoso.com*.
 
    ![Criar zona](./media/dns-getstarted-portal/newzone250.png)

5. Em seguida, especifique o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já exista. Se optar por criar um novo grupo de recursos, utilize a lista pendente **Localização** para especificar a localização do grupo de recursos. Tenha em atenção que esta definição se refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

6. Pode deixar a caixa de verificação **Afixar ao dashboard** selecionada se pretender localizar facilmente a sua nova zona no dashboard. Em seguida, clique em **Criar**.

    ![Afixar ao dashboard](./media/dns-getstarted-portal/pindashboard150.png)

7. Depois de clicar em Criar, verá que a nova zona está a ser configurada no dashboard.

    ![Criação](./media/dns-getstarted-portal/creating150.png)

8. Quando tiver sido criada a nova zona, o painel para a nova zona abre-se no dashboard.


## <a name="create-a-dns-record"></a>Criar um registo DNS

O exemplo seguinte explica-lhe o processo de criação do novo registo "A". Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md). 


1. No topo do painel **Zona DNS**, selecione **+ Conjunto de registos**, para abrir o painel **Adicionar conjunto de registos**.

    ![Novo conjunto de registos](./media/dns-getstarted-portal/newrecordset500.png)

4. No painel **Adicionar conjunto de registos**, nomeie o conjunto de registos. Por exemplo, pode nomear o conjunto de registos "**www**".

    ![Adicionar conjunto de registos](./media/dns-getstarted-portal/addrecordset500.png)

5. Selecione o tipo de registo que pretende criar. Para este exemplo, selecione **A**.
6. Defina o **TTL**. O tempo de duração predefinido é de uma hora.
7. Adicione o endereço IP do registo.
8. Selecione **OK** na parte inferior do painel para criar o registo DNS.


## <a name="view-records"></a>Ver registos

Na parte inferior do painel da zona DNS, pode ver os registos para a zona DNS. Deverá ver os registos DNS e SOA predefinidos, que são criados em cada zona, assim como quaisquer novos registos que tenha criado.

![zona](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Atualizar servidores de nomes

Depois de ter a certeza que a sua zona DNS e os registos foram configurados corretamente, tem de configurar o seu nome de domínio para utilizar os servidores de nomes do DNS do Azure. Isto permite que outros utilizadores na Internet encontrem os seus registos DNS.

Os servidores de nomes para a sua zona são indicados no portal do Azure:

![zona](./media/dns-getstarted-portal/viewzonens500.png)

Estes servidores de nomes devem ser configurados com a entidade de registo de nomes de domínio (onde comprou o nome de domínio). A entidade de registo irá oferecer a opção para configurar os servidores de nomes do domínio. Para obter mais informações, consulte [Delegate your domain to Azure DNS (Delegar o seu domínio ao DNS do Azure)](dns-domain-delegation.md).


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre DNS do Azure, consulte [Azure DNS overview (Descrição geral do DNS do Azure)](dns-overview.md).

Para saber mais sobre a gestão de registos DNS no DNS do Azure, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).


