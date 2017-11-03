---
title: "Ações de menu do MMC do Snapshot Manager do StorSimple | Microsoft Docs"
description: "Descreve como utilizar as ações de menu de consola de gestão da Microsoft (MMC) padrão no Snapshot Manager do StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 48f439a566a8067e153aab4fb789937d2f91268d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilizar as ações do menu MMC no Snapshot Manager do StorSimple

## <a name="overview"></a>Descrição geral
No Snapshot Manager do StorSimple, verá que as seguintes ações listadas em todos os menus de ação e todas as variações do **ações** painel.

* Vista
* Nova janela a partir daqui 
* Atualizar 
* Lista de exportação 
* Ajuda 

Estas ações fazem parte da consola de gestão da Microsoft (MMC) e não são específicas para Snapshot Manager do StorSimple. Este tutorial descreve estas ações e explica como utilizar cada um no Snapshot Manager do StorSimple.

## <a name="view"></a>Vista
Pode utilizar o **vista** opção para alterar o **resultados** vista de painel e para alterar a vista de janela de consola. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar a vista de painel de resultados
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique de qualquer nó ou expanda o nó e faça duplo clique num item de **resultados** painel e, em seguida, clique o **vista** opção. 
3. Para adicionar ou remover as colunas que aparecem no **resultados** painel, clique em **Adicionar/remover colunas**. O **Adicionar/remover colunas** é apresentada a caixa de diálogo.
   
    ![Adicionar ou remover colunas do painel de resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Preencha o formulário da seguinte forma:
   
   * Seleccionar itens do **disponível** colunas lista e clique em **adicionar** adicioná-los para o **apresentado colunas** lista. 
   * Clique nos itens no **apresentado colunas** lista e clique em **remover** removê-las a partir da lista. 
   * Selecione um item no **apresentadas** colunas lista e clique em **mover para cima** ou **mover para baixo** para mover o item para cima ou para baixo na lista. 
   * Clique em **restaurar predefinições** para voltar à predefinição **resultados** configuração de painel. 
5. Quando tiver terminado com as suas seleções, clique em **OK**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar a vista de janela de consola
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique de qualquer nó, clique em **vista**e, em seguida, clique em **personalizar**. O **personalizar** é apresentada a caixa de diálogo.
   
    ![Personalizar a janela de consola](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Selecione ou desmarque as caixas de verificação para mostrar ou ocultar itens na janela da consola. Quando tiver terminado com as suas seleções, clique em **OK**.

## <a name="new-window-from-here"></a>Nova janela a partir daqui
Pode utilizar o **nova janela a partir daqui** opção para abrir uma nova janela de consola.

#### <a name="to-open-a-new-console-window"></a>Para abrir uma nova janela de consola
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique de qualquer nó e, em seguida, clique em **nova janela a partir daqui**. 
   
    É apresentada uma nova janela que mostra apenas o âmbito que selecionou. Por exemplo, se, faça duplo clique o **políticas de cópia de segurança** nó, a nova janela mostrará apenas a **políticas de cópia de segurança** no nó de **âmbito** painel e uma lista de cópia de segurança definida as políticas no **resultados** painel. Veja o seguinte exemplo.
   
    ![Nova janela a partir daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Atualizar
Pode utilizar o **atualizar** ação para atualizar a janela de consola.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela de consola
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique de qualquer nó ou expanda o nó e faça duplo clique num item de **resultados** painel e, em seguida, clique em **atualizar**. 

## <a name="export-list"></a>Lista de exportação
Pode utilizar o **exportar lista** ação guardar uma lista de um ficheiro de valores separados por vírgulas (CSV). Por exemplo, pode exportar a lista de políticas de cópia de segurança ou o catálogo de cópias de segurança. Em seguida, pode importar o ficheiro CSV para uma aplicação de folha de cálculo para análise.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Guardar uma lista de um ficheiro de valores separados por vírgulas (CSV)
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No **âmbito** painel, faça duplo clique de qualquer nó ou expanda o nó e faça duplo clique num item de **resultados** painel e, em seguida, clique em **exportar lista**. 
3. O **exportar lista** é apresentada a caixa de diálogo. Preencha o formulário da seguinte forma: 
   
   1. No **nome de ficheiro** caixa, escreva um nome para o ficheiro CSV ou clique na seta para selecionar a partir da lista pendente.
   2. No **guardar como tipo** caixa, clique na seta e selecione um tipo de ficheiro da lista pendente.
   3. Para guardar apenas itens selecionados, selecione as linhas e, em seguida, clique em de **guardar apenas os linhas selecionadas** caixa de verificação. Para guardar apresenta uma lista de todos os exportado, desmarque a **guardar apenas as linhas selecionadas** caixa de verificação.
   4. Clique em **Guardar**.
      
      ![Exportar a lista como um ficheiro de valores separados por vírgulas](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Ajuda
Pode utilizar o **ajudar** menu para ver a ajuda online disponível do Snapshot Manager do StorSimple e a MMC.

#### <a name="to-view-available-online-help"></a>Para ver a ajuda online disponíveis
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique de qualquer nó ou expanda o nó e faça duplo clique num item de **resultados** painel e, em seguida, clique em **ajudar**. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).
* Saiba mais sobre [utilizando Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).

