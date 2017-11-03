---
title: Gerir conjuntos de registos de DNS e registos de DNS do Azure | Microsoft Docs
description: "O DNS do Azure fornece a capacidade para gerir registos e conjuntos de registos DNS que aloja o seu domínio."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: 001b80ccba43beab44f6a598f820df65a85a345f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gerir registos DNS e conjuntos de registos ao utilizar o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerir conjuntos de registos e registos para a sua zona DNS ao utilizar o portal do Azure.

É importante compreender a diferença entre os conjuntos de registos de DNS e registos DNS individuais. Um conjunto de registos é uma coleção de registos numa zona que têm o mesmo nome e são o mesmo tipo. Para obter mais informações, consulte [DNS criar conjuntos de registos e registos ao utilizar o portal do Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Criar um novo conjunto de registos e registos

Para criar um conjunto de registos no portal do Azure, consulte [registos de DNS de criar utilizando o portal do Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Ver um conjunto de registos

1. No portal do Azure, visite o **zona DNS** painel.
2. Procure o conjunto de registos e selecione-o. Esta ação abre as propriedades do conjunto de registos.

    ![Procure um conjunto de registos](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adicione um novo registo para um conjunto de registos

Pode adicionar até 20 registos para qualquer conjunto de registos. Um conjunto de registos não pode conter dois registos idênticos existentes. Os conjuntos de registos vazios (com zero registos) podem ser criados, mas não aparecem nos servidores de nome DNS do Azure. Os conjuntos de registos do tipo CNAME podem conter, no máximo, um registo.

1. No **propriedades do conjunto de registos** painel para a sua zona DNS, clique o conjunto de registos que pretende adicionar um registo a.

    ![Selecione um conjunto de registos](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique o registo de definir as propriedades preenchendo nos campos.

    ![Adicione um registo](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Clique em **guardar** na parte superior do painel para guardar as suas definições. Em seguida, feche o painel.
4. No canto, verá que o registo está a guardar.

    ![Guardar o conjunto de registos](./media/dns-operations-recordsets-portal/saving150.png)

Após o registo foi guardada, os valores no **zona DNS** painel irá refletir o novo registo.

## <a name="update-a-record"></a>Atualizar um registo

Quando atualizar um registo de um conjunto de registos existente, os campos que pode atualizar dependem do tipo de registo que está a trabalhar com.

1. No **propriedades do conjunto de registos** painel para o conjunto de registos, procure o registo.
2. Modifique o registo. Quando modificar um registo, pode alterar as definições disponíveis para o registo. No exemplo seguinte, o **endereço IP** campo está selecionado e o endereço IP está a ser modificado.

    ![Modificar um registo](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **guardar** na parte superior do painel para guardar as suas definições. No canto superior direito, verá a notificação que foi guardado o registo.

    ![Guardar o conjunto de registos](./media/dns-operations-recordsets-portal/saved150.png)

Após o registo foi guardado, os valores para o registo definidos no **zona DNS** painel irá refletir o registo atualizado.

## <a name="remove-a-record-from-a-record-set"></a>Remover um registo de um conjunto de registos

Pode utilizar o portal do Azure para remover registos de um conjunto de registos. Tenha em atenção que a remover o último registo a partir de um conjunto de registos não elimina o conjunto de registos.

1. No **propriedades do conjunto de registos** painel para o conjunto de registos, procure o registo.
2. Clique em de registo que pretende remover. Em seguida, selecione **remover**.

    ![Remover um registo](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **guardar** na parte superior do painel para guardar as suas definições.
4. Após o registo foi removido, os valores para o registo no **zona DNS** painel irá refletir a remoção.

## <a name="delete"></a>Eliminar um conjunto de registos

1. No **propriedades do conjunto de registos** painel para o conjunto de registos, clique em **eliminar**.

    ![Eliminar um conjunto de registos](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Aparece uma mensagem a perguntar se pretende eliminar o conjunto de registos.
3. Certifique-se de que o nome corresponde ao conjunto de registos que pretende eliminar e, em seguida, clique em **Sim**.
4. No **zona DNS** painel, certifique-se de que o conjunto de registos já não está visível.

## <a name="work-with-ns-and-soa-records"></a>Trabalhar com registos NS e SOA

Registos NS e SOA, que são criados automaticamente são geridos de forma diferente dos restantes tipos de registos.

### <a name="modify-soa-records"></a>Modificar registos SOA

Não é possível adicionar ou remover os registos do registo SOA criado automaticamente definido no vértice da zona (nome = "@"). No entanto, pode modificar qualquer dos parâmetros dentro o registo SOA (exceto "anfitrião") e o registo de definir o valor de TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar os registos NS no vértice da zona

O conjunto no vértice da zona de registos NS é criado automaticamente com cada zona DNS. Contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Pode adicionar servidores para este registo NS definidas, para suportar os domínios de alojamento conjunta com mais do que um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes de DNS do Azure pré-preenchidos.

Tenha em atenção que isto aplica-se apenas ao registo NS definido no vértice da zona. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser modificados sem restrição.

### <a name="delete-soa-or-ns-record-sets"></a>Eliminar conjuntos de registos SOA ou NS

Não é possível eliminar o SOA e conjuntos de registos do NS no vértice da zona (nome = "@") que são criados automaticamente quando a zona é criada. Estes são eliminados automaticamente ao eliminar a zona.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o DNS do Azure, consulte o [descrição geral do DNS do Azure](dns-overview.md).
* Para obter mais informações sobre a automatização de DNS, consulte [zonas DNS criar e conjuntos de registos utilizando o SDK .NET](dns-sdk.md).
* Para obter mais informações sobre os registos DNS inversas, consulte [descrição geral do DNS inversa e de suporte no Azure](dns-reverse-dns-overview.md).
