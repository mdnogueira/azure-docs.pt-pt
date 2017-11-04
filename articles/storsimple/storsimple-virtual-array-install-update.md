---
title: "Instalar atualizações numa matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs"
description: "Descreve como utilizar a IU da web de matriz Virtual StorSimple para aplicar atualizações utilizando o método de portal e a correção"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2d081604c0ca01f47c3ff2aab7477859d280963
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Instalar atualizações na sua matriz Virtual StorSimple - portal do Azure

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar atualizações na sua matriz Virtual StorSimple através da IU da web local e através do portal do Azure. Tem de aplicar as atualizações de software ou correções a par da matriz de Virtual StorSimple. 

Tenha em atenção que a instalar uma atualização ou correção reinicia o seu dispositivo. Dado que a matriz de Virtual StorSimple é um dispositivo de nó único, qualquer e/s em curso é interrompido e período de indisponibilidade ocorre com o seu dispositivo. 

Antes de aplicar uma atualização, recomendamos que colocar os volumes ou partilhas offline no anfitrião primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos nos dados.

> [!IMPORTANT]
> Se estiver a executar a atualização 0.1 ou versões de software DG, tem de utilizar o método de correção através da IU da web local para instalar a atualização 0.3. Se estiver a executar a atualização 0,2, recomendamos que instale as atualizações através do portal clássico do Azure.
 

## <a name="use-the-local-web-ui"></a>Utilize a IU da web do local

Existem dois passos, ao utilizar a IU da web local:

* Transferir a atualização ou a correção
* Instalar a atualização ou a correção

### <a name="download-the-update-or-the-hotfix"></a>Transferir a atualização ou a correção

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correção

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (KB) de correção que pretende transferir. Introduza **3182061** para atualização 0.3 e, em seguida, clique em **pesquisa**.
   
    A listagem de correção é apresentado, por exemplo, **StorSimple Virtual matriz atualização 0.3**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao cesto.

5. Clique em **Ver Cesto**.

6. Clique em **Transferir**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. As atualizações são transferidas para a localização especificada e colocadas numa sub-pasta com o mesmo nome que a atualização. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

7. Abra a pasta copiada, deverá ver um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para instalar a atualização ou correção.

### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correção

Antes da instalação de atualização ou correção, certifique-se de que tem a atualização ou a correção transferido localmente no seu anfitrião ou acessível através de uma partilha de rede. 

Utilize este método para instalar as atualizações num dispositivo com GA ou atualização 0.1 versões de software. Este procedimento demora menos de 2 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correção

1. No local IU da web, aceda a **manutenção** > **atualização de Software**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update1m.png)

2. No **caminho do ficheiro de atualização**, introduza o nome de ficheiro para a atualização ou a correção. Também pode navegar para o ficheiro de instalação de atualização ou correção se colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update2m.png)

3. É apresentado um aviso. Este é um dispositivo de nó único, após a atualização ser aplicada, o reinício do dispositivo e existe um período de indisponibilidade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update3m.png)

4. Inicia a atualização. Depois do dispositivo está atualizado com êxito, esta reiniciar. A IU do local não está acessível desta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update5m.png)

5. Após o reinício estiver concluído, é direcionado para o **sessão** página. Para verificar que o software do dispositivo atualizou, na web local IU, aceda ao **manutenção** > **atualização de Software**. A versão do software apresentados deve ser **10.0.0.0.0.10288.0** para atualização 0.3.
   
   > [!NOTE]
   > As versões do software elaboramos relatórios de forma ligeiramente diferente na IU da web local e o portal do Azure. Por exemplo, a IU da web local relatórios **10.0.0.0.0.10288** e os relatórios do portais do Azure **10.0.10288.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a atualização 0,2, recomendamos que instale as atualizações através do portal do Azure. O procedimento portal exige que o utilizador procurar, transferir e, em seguida, instale as atualizações. Este procedimento demora cerca de 7 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Após a instalação estiver concluída (como indicado pelo Estado de tarefa a 100%), aceda ao seu serviço do Gestor de dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que pretende atualizar a lista de dispositivos ligados a este serviço. No **definições** painel, aceda a **gerir** secção e selecione **atualizações ao dispositivo**. A versão do software apresentados deve ser **10.0.10288.0**.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

