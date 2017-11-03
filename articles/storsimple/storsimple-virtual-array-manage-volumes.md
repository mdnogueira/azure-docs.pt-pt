---
title: Gerir volumes na matriz Virtual StorSimple | Microsoft Docs
description: "Descreve o Gestor de dispositivos do StorSimple e explica como utilizá-la para gerir volumes na sua matriz de Virtual StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Serviço do Gestor de dispositivos do StorSimple de utilização para gerir volumes na matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir os volumes na sua matriz de Virtual StorSimple.

O serviço do Gestor de dispositivos do StorSimple é uma extensão no portal do Azure permite-lhe gerir solução StorSimple a partir de uma interface web único. Além da gestão de partilhas e os volumes, pode utilizar o serviço StorSimple Manager de dispositivos para ver e gerir dispositivos, ver alertas e ver e gerir políticas de cópia de segurança e o catálogo de cópias de segurança.

## <a name="volume-types"></a>Tipos de volume

Volumes do StorSimple podem ser:

* **Afixado localmente**: dados destes volumes permanece na matriz permanente e não transbordam para a nuvem.
* **Em camadas**: dados destes volumes podem transbordam para a nuvem. Quando cria um volume em camadas, aproximadamente, 10% do espaço de é aprovisionado no escalão local e 90% do espaço é aprovisionado na nuvem. Por exemplo, se aprovisionar um volume de 1 TB, 100 GB deverá residir no espaço local e 900 GB seria utilizada na nuvem quando as camadas de dados. Por sua vez Isto implica que se executar a partir do espaço local no dispositivo, não é possível aprovisionar um volume em camadas (porque a 10% necessário no escalão local não estará disponível).

### <a name="provisioned-capacity"></a>Capacidade de aprovisionamento
Consulte a tabela seguinte para a capacidade máxima de aprovisionado para cada tipo de volume.

| **Identificador de limite**                                       | **Limite**     |
|------------------------------------------------------------|---------------|
| Tamanho mínimo de um volume em camadas                            | 500 GB        |
| Tamanho máximo de um volume em camadas                            | 5 TB          |
| Tamanho mínimo de um volume localmente afixado                    | 50 GB         |
| Tamanho máximo de um volume localmente afixado                    | 500 GB        |

## <a name="the-volumes-blade"></a>O painel de Volumes
O **Volumes** menu no painel de resumo de serviço do StorSimple mostra uma lista de volumes de armazenamento numa matriz StorSimple fornecida e permite-lhe geri-los.

![Painel de volumes](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Um volume é constituída por uma série de atributos:

* **Nome do volume** – um nome descritivo que têm de ser exclusivos e ajuda a identificar o volume.
* **Estado** – pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que estão a permissão para aceder ao utilizar o volume.
* **Tipo** – indica se o volume é **em camadas** (predefinição) ou **afixado localmente**.
* **Capacidade** – Especifica a quantidade de dados utilizadas em comparação com a quantidade total de dados que podem ser armazenados pelo iniciador (servidor).
* **Cópia de segurança** – no caso da matriz Virtual StorSimple, todos os volumes são ativados automaticamente para cópia de segurança.
* **Ligado anfitriões** – Especifica os iniciadores (servidores) que têm permissão de acesso a este volume.

![Detalhes de volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicionar um volume
* Modificar um volume
* Colocar offline um volume
* Eliminar um volume

## <a name="add-a-volume"></a>Adicionar um volume

1. No painel Resumo de serviço do StorSimple, clique em **+ adicionar volume** na barra de comando. Isto abre-se a **adicionar volume** painel.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. No **adicionar volume** painel, efetue o seguinte procedimento:
   
   * No **nome do Volume** campo, introduza um nome exclusivo para o volume. O nome tem de ser uma cadeia que contém 3 e 127 carateres.
   * No **tipo** pendente lista, especifique se pretende criar um **em camadas** ou **afixado localmente** volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione **afixado localmente volume**. Para todos os outros dados, selecione **em camadas** volume.
   * No **capacidade** campo, especifique o tamanho do volume. Um volume em camadas tem de estar entre 500 GB e 5 TB e um volume localmente afixado tem de estar entre 50 GB e 500 GB.
   * * Clique em **ligados a anfitriões**, selecione um registo de controlo de acesso (ACR) correspondente para o iniciador iSCSI que pretende ligar a este volume e, em seguida, clique em **selecione**.
3. Para adicionar um novo anfitrião ligado, clique em **adicionar novo**, introduza um nome para o anfitrião e o iSCSI nome qualificado (IQN) e, em seguida, clique em **adicionar**.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Quando tiver concluído a configuração do volume, clique em **criar**. Será criado um volume com as definições especificadas e verá uma notificação sobre a criação com êxito do mesmo. Por predefinição será ativada para o volume de cópia de segurança.
5. Para confirmar que o volume foi criado com êxito, vá para o **Volumes** painel. Deverá ver o volume listado.
   
    ![Volume criar com êxito](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificar um volume

Modificar um volume quando precisar de alterar os anfitriões que o volume de acesso. Não não possível modificar os outros atributos de um volume quando o volume tiver sido criado.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Do **Volumes** definição no painel de resumo de serviço do StorSimple, selecione a matriz virtual onde reside o volume que pretende modificar.
2. **Selecione** o volume e clique em **ligados a anfitriões** para ver o anfitrião atualmente ligado e modificá-lo para um servidor diferente.
   
    ![Editar volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Guarde as alterações ao clicar no **guardar** barra de comando. As definições especificadas serão aplicadas e verá uma notificação.

## <a name="take-a-volume-offline"></a>Colocar offline um volume

Poderá ter de colocar offline um volume quando estiver a planear modificá-lo ou eliminá-lo. Quando um volume estiver offline, não está disponível para acesso de leitura e escrita. Terá de colocar offline o volume do anfitrião, bem como no dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar offline um volume

1. Certifique-se de que o volume em questão não está a ser utilizado antes de colocar-offline.
2. Colocar offline o volume anfitrião primeiro. Isto elimina a qualquer potencial risco de danos nos dados no volume. Para obter passos específicos, consulte as instruções para o seu sistema operativo do anfitrião.
3. Depois do volume do anfitrião estiver offline, efetuar o volume na matriz offline, efetuando os seguintes passos:
   
   * Do **Volumes** definição no painel de resumo de serviço do StorSimple, selecione a matriz virtual onde reside o volume que pretende colocar offline.
   * **Selecione** o volume e clique em **...**  (em alternativa com o botão direito nesta linha) e no menu de contexto, selecione **colocar offline**.
     
        ![Offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Reveja as informações de **colocar offline** painel e confirme a aceitação da operação. Clique em **colocar offline** para colocar offline o volume. Verá uma notificação da operação em curso.
   * Para confirmar que o volume com êxito foi colocado offline, vá para o **Volumes** painel. Deverá ver o estado do volume como offline.
     
       ![Confirmação de offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Eliminar um volume

> [!IMPORTANT]
> Pode eliminar um volume apenas se estiver offline.
> 
> 

Conclua os seguintes passos para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume

1. Do **Volumes** definição no painel de resumo de serviço do StorSimple, selecione a matriz virtual onde reside o volume que pretende eliminar.
2. **Selecione** o volume e clique em **...**  (em alternativa com o botão direito nesta linha) e no menu de contexto, selecione **eliminar**.
   
    ![Eliminar volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, coloque-offline em primeiro lugar, seguindo os passos no [colocar offline um volume](#take-a-volume-offline).
4. Quando lhe for pedido para confirmação no **eliminar** painel, aceite a confirmação e clique em **eliminar**. O volume agora será eliminado e o **Volumes** painel irá mostrar a lista atualizada de volumes dentro da matriz virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba como [clonar um volume StorSimple](storsimple-virtual-array-clone.md).

