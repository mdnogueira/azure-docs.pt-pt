---
title: "Instalar a atualização 0,5 na matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve como utilizar a IU da web de matriz Virtual StorSimple para aplicar atualizações utilizando o método de portal e a correção do Azure"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: c47da5b90c16e2d5b5709e2a6affc026238b9468
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalar a atualização 0,5 na sua matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar a atualização 0,5 na sua matriz de Virtual StorSimple através da IU da web local e através do portal do Azure. Tem de aplicar as atualizações de software ou correções a par da matriz de Virtual StorSimple.

Antes de aplicar uma atualização, recomendamos que colocar os volumes ou partilhas offline no anfitrião primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos nos dados. Depois dos volumes ou partilhas estiverem offline, deve também ter uma manual cópia de segurança do dispositivo.

> [!IMPORTANT]
> - Atualização 0,5 corresponde à **10.0.10290.0** versão do software no seu dispositivo. Para obter informações sobre quais são as Novidades nesta atualização, visite [notas de versão para a atualização 0,5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Se estiver a executar a atualização 0,2 ou posterior, recomendamos que instale as atualizações através do portal do Azure. Se estiver a executar a atualização 0.1 ou versões de software DG, tem de utilizar o método de correção através da IU da web local para instalar a atualização 0,5.
>
> - Tenha em atenção que a instalar uma atualização ou correção reinicia o seu dispositivo. Dado que a matriz de Virtual StorSimple é um dispositivo de nó único, qualquer e/s em curso é interrompido e período de indisponibilidade ocorre com o seu dispositivo.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a atualização 0.2 e posterior, recomendamos que instale as atualizações através do portal do Azure. O procedimento portal exige que o utilizador procurar, transferir e, em seguida, instale as atualizações. Este procedimento demora cerca de 7 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Após a instalação estiver concluída, aceda ao seu serviço do Gestor de dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que acabou de ser atualizado. Aceda a **definições > Gerir > atualizações ao dispositivo**. A versão do software apresentados deve ser **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Utilize a IU da web do local

Existem dois passos, ao utilizar a IU da web local:

* Transferir a atualização ou a correção
* Instalar a atualização ou a correção

### <a name="download-the-update-or-the-hotfix"></a>Transferir a atualização ou a correção

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correção

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (KB) de correção que pretende transferir. Introduza **4021576** para atualizar 0,5 e, em seguida, clique em **pesquisa**.
   
    A listagem de correção é apresentado, por exemplo, **StorSimple Virtual matriz atualização 0,5**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Clique em **Transferir**. 

5. Deverá ver dois ficheiros para transferir, um *. msu* e um *. cab* ficheiro. Transferir cada um desses ficheiros para uma pasta. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.
    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Consulte:
    -  Um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para atualizar o software de dispositivos.
    - Um ficheiro de pacote de agente de monitorização de Geneva `GenevaMonitoringAgentPackageInstaller`. Este ficheiro é utilizado para atualizar o agente de monitorização e diagnóstico do serviço (MDS). Faça duplo clique o ficheiro cab. É apresentado um. msi. Selecione o ficheiro, rato e, em seguida, **extrair** o ficheiro. Irá utilizar o _. msi_ ficheiro para atualizar o agente.

        ![Extrair o ficheiro de atualização de agente do MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correção

Antes da instalação de atualização ou correção, certifique-se de que tem a atualização ou a correção transferido localmente no seu anfitrião ou acessível através de uma partilha de rede.

Utilize este método para instalar as atualizações num dispositivo com GA ou atualização 0.1 versões de software. Este procedimento demora menos de 2 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correção

1. No local IU da web, aceda a **manutenção** > **atualização de Software**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. No **caminho do ficheiro de atualização**, introduza o nome de ficheiro para a atualização ou a correção. Também pode navegar para o ficheiro de instalação de atualização ou correção se colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. É apresentado um aviso. Este é um dispositivo de nó único, após a atualização ser aplicada, o reinício do dispositivo e existe um período de indisponibilidade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Inicia a atualização. Depois do dispositivo está atualizado com êxito, esta reiniciar. A IU do local não está acessível desta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Após o reinício estiver concluído, é direcionado para o **sessão** página. Para verificar que o software do dispositivo atualizou, na web local IU, aceda ao **manutenção** > **atualização de Software**. A versão do software apresentados deve ser **10.0.0.0.0.10290.0** para atualização 0,5.
   
   > [!NOTE]
   > As versões do software elaboramos relatórios de forma ligeiramente diferente na IU da web local e o portal do Azure. Por exemplo, a IU da web local relatórios **10.0.0.0.0.10290** e os relatórios do portais do Azure **10.0.10290.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. O passo seguinte é atualizar o agente do MDS. No **atualização de Software** página, vá para o **caminho do ficheiro de atualização** e navegue para o `GenevaMonitoringAgentPackageInstaller.msi` ficheiro. Repita os passos 2 a 4. Depois da matriz virtual reinicia, inicie sessão na IU da web do local.

A atualização está agora concluída.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

