---
title: Gerir partilhas de matriz Virtual StorSimple | Microsoft Docs
description: "Descreve o Gestor de dispositivos do StorSimple e explica como utilizá-la para gerir partilhas na sua matriz de Virtual StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Utilizar o serviço StorSimple Manager de dispositivos para gerir partilhas na matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir partilhas na sua matriz de Virtual StorSimple.

O serviço do Gestor de dispositivos do StorSimple é uma extensão no portal do Azure permite-lhe gerir solução StorSimple a partir de uma interface web único. Além da gestão de partilhas e os volumes, pode utilizar o serviço StorSimple Manager de dispositivos para ver e gerir dispositivos, ver alertas, gerir políticas de cópia de segurança e gerir o catálogo de cópias de segurança.

## <a name="share-types"></a>Tipos de partilha

StorSimple partilhas podem ser:

* **Afixado localmente**: dados nestas partilhas permanece na matriz permanente e não transbordam para a nuvem.
* **Em camadas**: dados nestas partilhas podem transbordam para a nuvem. Quando cria uma partilha em camadas, aproximadamente, 10% do espaço é aprovisionado no escalão local e é aprovisionado 90% do espaço na nuvem. Por exemplo, se aprovisionado uma partilha de 1 TB, 100 GB deverá residir no espaço local e 900 GB seria utilizada na nuvem quando as camadas de dados. Por sua vez Isto implica que se executar a partir do espaço local no dispositivo, não é possível aprovisionar uma partilha em camadas (porque a 10% necessário no escalão local não estará disponível).

### <a name="provisioned-capacity"></a>Capacidade de aprovisionamento

Consulte a tabela seguinte para a capacidade máxima de aprovisionado para cada tipo de partilha.

| **Identificador de limite** | **Limite** |
| --- | --- |
| Tamanho mínimo de uma partilha em camadas |500 GB |
| Tamanho máximo de uma partilha em camadas |20 TB |
| Tamanho mínimo de uma partilha afixado localmente |50 GB |
| Tamanho máximo de uma partilha afixado localmente |2 TB |

## <a name="the-shares-blade"></a>O painel de partilhas

O **partilhas** menu no painel de resumo de serviço do StorSimple mostra uma lista de partilhas de armazenamento numa matriz StorSimple fornecida e permite-lhe geri-los.

![Painel de partilhas](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Uma partilha é constituída por uma série de atributos:

* **Nome da partilha** – um nome descritivo que têm de ser exclusivos e ajuda a identificar a partilha.
* **Estado** – pode ser online ou offline. Se uma partilha estiver offline, os utilizadores da partilha não será possível aceder à mesma.
* **Tipo** – indica se a partilha está **em camadas** (predefinição) ou **afixado localmente**.
* **Capacidade** – Especifica a quantidade de dados utilizadas em comparação com a quantidade total de dados que podem ser armazenados na partilha.
* **Descrição** – uma definição opcional que o ajuda descrevem a partilha.
* **Permissões** -permissões do NTFS para a partilha que podem ser geridos através do Explorador do Windows.
* **Cópia de segurança** – no caso da matriz Virtual StorSimple, todas as partilhas são ativadas automaticamente para cópia de segurança.

![Detalhes de partilhas](./media/storsimple-virtual-array-manage-shares/share-details.png)

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicione uma partilha
* Modificar uma partilha
* Colocar offline uma partilha
* Eliminar uma partilha

## <a name="add-a-share"></a>Adicione uma partilha

1. No painel Resumo de serviço do StorSimple, clique em **+ Adicionar partilha** na barra de comando. Isto abre-se a **Adicionar partilha** painel.

    ![Adicionar a partilha](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. No **Adicionar partilha** painel, efetue o seguinte procedimento:
   
    1. No **nome da partilha** campo, introduza um nome exclusivo para a partilha. O nome tem de ser uma cadeia que contém 3 e 127 carateres.

    2. Opcional **Descrição** para a partilha. A descrição irão ajudá-lo a identificar os proprietários de partilha.

    3. No **tipo** pendente lista, especifique se pretende criar um **em camadas** ou **afixado localmente** partilhar. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione **afixado localmente partilha**. Para todos os outros dados, selecione **em camadas** partilhar.

    4. No **capacidade** campo, especifique o tamanho da partilha. Uma partilha em camadas tem de estar entre 500 GB e 20 TB e uma partilha localmente afixada tem de estar entre 50 GB e 2 TB.

    5. No **predefinir permissões totais** campo, atribua as permissões para o utilizador ou grupo que está a aceder a esta partilha. Especifique o nome de utilizador ou o grupo de utilizadores no  _john@contoso.com_  formato. Recomendamos que utilize um grupo de utilizadores (em vez de um utilizador único) para permitir que os privilégios de administrador para aceder a estas partilhas. Depois de ter de atribuir as permissões aqui, em seguida, pode utilizar o Explorador de ficheiros para modificar estas permissões.
3. Quando tiver terminado de configurar a partilha, clique em **criar**. Será criada uma partilha com as definições especificadas e verá uma notificação. Por predefinição, será ativada para a partilha de cópia de segurança.
4. Para confirmar que a partilha foi criada com êxito, vá para o **partilhas** painel. Deverá ver a partilha na lista.
   
    ![Partilha de criar com êxito](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificar uma partilha

Modificar uma partilha quando precisar de alterar a descrição da partilha. Outras propriedades de partilha não podem ser modificadas assim que a partilha for criada.

#### <a name="to-modify-a-share"></a>Para modificar uma partilha

1. Do **partilhas** definição no painel de resumo de serviço do StorSimple, selecione a matriz virtual no qual reside a partilha que pretende modificar.
2. **Selecione** a partilha para ver a descrição do atual e modificá-lo.
3. Guarde as alterações ao clicar no **guardar** barra de comando. As definições especificadas serão aplicadas e verá uma notificação.
   
    ![ Editar partilha](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Colocar offline uma partilha

Poderá ter de colocar offline uma partilha quando estiver a planear modificá-lo ou eliminá-lo. Quando uma partilha estiver offline, não está disponível para acesso de leitura e escrita. Terá de colocar offline a partilha no anfitrião, bem como no dispositivo.

#### <a name="to-take-a-share-offline"></a>Para colocar offline uma partilha

1. Certifique-se de que a partilha em questão não está a ser utilizada antes de colocar-offline.
2. Efetuar a partilha na matriz offline, efetuando os seguintes passos:
   
    1. Do **partilhas** definição no painel de resumo de serviço do StorSimple, selecione a matriz virtual no qual reside a partilha desejar colocar offline.

    2. **Selecione** de partilha e clique em **...**  (em alternativa com o botão direito nesta linha) e no menu de contexto, selecione **colocar offline**.
     
        ![Partilha offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Reveja as informações de **colocar offline** painel e confirme a aceitação da operação. Clique em **colocar offline** para colocar offline a partilha. Verá uma notificação da operação em curso.

    4. Para confirmar que a partilha com êxito foi colocada offline, vá para o **partilhas** painel. Deverá ver o estado da partilha de como offline.

## <a name="delete-a-share"></a>Eliminar uma partilha

> [!IMPORTANT]
> Pode eliminar uma partilha apenas se estiver offline.


Conclua os seguintes passos para eliminar uma partilha.

#### <a name="to-delete-a-share"></a>Para eliminar uma partilha

1. Do **partilhas** definição no painel de resumo de serviço do StorSimple, selecione a matriz virtual no qual reside a partilha de que pretende eliminar.
2. **Selecione** de partilha e clique em **...**  (em alternativa com o botão direito nesta linha) e no menu de contexto, selecione **eliminar**.
   
    ![Eliminar a partilha](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Verifique o estado da partilha de que pretende eliminar. Se a partilha de que pretende eliminar não estiver offline, coloque-offline pela primeira vez. Siga os passos no [colocar offline uma partilha](#take-a-share-offline).
4. Quando lhe for pedido para confirmação no **eliminar** painel, aceite a confirmação e clique em **eliminar**. A partilha agora será eliminada e o **partilhas** painel mostra a lista atualizada de partilhas na matriz virtual.

## <a name="next-steps"></a>Passos seguintes
Saiba como [clonar uma partilha do StorSimple](storsimple-virtual-array-clone.md).

