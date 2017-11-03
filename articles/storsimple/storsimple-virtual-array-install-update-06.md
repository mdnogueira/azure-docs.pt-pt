---
title: "Instalar a atualização 0,6 na matriz Virtual StorSimple | Microsoft Docs"
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalar a atualização 0,6 na sua matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar a atualização 0,6 na sua matriz de Virtual StorSimple através da IU da web local e através do portal do Azure. Aplicar as atualizações de software ou correções a par da matriz de Virtual StorSimple.

Antes de aplicar uma atualização, recomendamos que colocar os volumes ou partilhas offline no anfitrião primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos nos dados. Depois dos volumes ou partilhas estiverem offline, deve também ter uma manual cópia de segurança do dispositivo.

> [!IMPORTANT]
> - Atualização 0,6 corresponde à **10.0.10293.0** versão do software no seu dispositivo. Para obter informações sobre quais são as Novidades nesta atualização, visite [notas de versão para a atualização 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Se estiver a executar a atualização 0,2 ou posterior, recomendamos que instale as atualizações através do portal do Azure. Se estiver a executar a atualização 0.1 ou versões de software DG, tem de utilizar o método de correção através da IU da web local para instalar a atualização 0,6.
>
> - Tenha em atenção que a instalar uma atualização ou correção reinicia o seu dispositivo. Dado que a matriz de Virtual StorSimple é um dispositivo de nó único, qualquer e/s em curso é interrompido e período de indisponibilidade ocorre com o seu dispositivo.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a atualização 0.2 e posterior, recomendamos que instale as atualizações através do portal do Azure. O procedimento portal exige que o utilizador procurar, transferir e, em seguida, instale as atualizações. Este procedimento demora cerca de 7 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Após a instalação estiver concluída, aceda ao seu serviço do Gestor de dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que acabou de ser atualizado. Aceda a **definições > Gerir > atualizações ao dispositivo**. A versão do software apresentados deve ser **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Utilize a IU da web do local

Existem dois passos, ao utilizar a IU da web local:

* Transferir a atualização ou a correção
* Instalar a atualização ou a correção

### <a name="download-the-update-or-the-hotfix"></a>Transferir a atualização ou a correção

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correção

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se estiver a utilizar o catálogo Microsoft Update pela primeira vez neste computador, clique em **instalar** quando lhe for pedido para instalar o suplemento do catálogo Microsoft Update.

3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (KB) de correção que pretende transferir. Introduza **4023268** para atualizar 0,6 e, em seguida, clique em **pesquisa**.
   
    A listagem de correção é apresentado, por exemplo, **StorSimple Virtual matriz atualização 0,6**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Clique em **Transferir**.

5. Deverá ver cinco ficheiros para transferir. Transferir cada um desses ficheiros para uma pasta. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.
    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Consulte:
    -  Um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para atualizar o software de dispositivos.
    - Um ficheiro de pacote de agente de monitorização de Geneva `GenevaMonitoringAgentPackageInstaller`. Este ficheiro é utilizado para atualizar o agente de monitorização e diagnóstico do serviço (MDS). Faça duplo clique o ficheiro cab. A _. msi_ é apresentado. Selecione o ficheiro, rato e, em seguida, **extrair** o ficheiro. Utilizar o _. msi_ ficheiro para atualizar o agente.

        ![Extrair o ficheiro de atualização de agente do MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Não é necessário atualizar o agente MDS se estiver a executar a atualização de StorSimple 0,5 (0.0.10293.0).

    - Três ficheiros que contêm as atualizações de segurança críticas do Windows, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, e `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correção

Antes da instalação de atualização ou correção, certifique-se de que tem a atualização ou a correção transferido localmente no seu anfitrião ou acessível através de uma partilha de rede.

Utilize este método para instalar as atualizações num dispositivo com GA ou atualização 0.1 versões de software. Este procedimento demora cerca de 12 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correção

1. No local IU da web, aceda a **manutenção** > **atualização de Software**. Tome nota da versão do software que estão em execução. Se estiver a executar **10.0.10290.0**, não terá de atualizar o agente do MDS no passo 6.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. No **caminho do ficheiro de atualização**, introduza o nome de ficheiro para a atualização ou a correção. Também pode navegar para o ficheiro de instalação de atualização ou correção se colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. É apresentado um aviso. A matriz virtual fornecida é um dispositivo de nó único, após a atualização ser aplicada, o reinício do dispositivo e existe um período de indisponibilidade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Inicia a atualização. Depois do dispositivo está atualizado com êxito, esta reiniciar. A IU do local não está acessível desta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Após o reinício estiver concluído, é direcionado para o **sessão** página. Para verificar que o software do dispositivo atualizou, na web local IU, aceda ao **manutenção** > **atualização de Software**. A versão do software apresentados deve ser **10.0.0.0.0.10293** para atualização 0,6.
   
   > [!NOTE]
   > As versões do software elaboramos relatórios de forma ligeiramente diferente na IU da web local e o portal do Azure. Por exemplo, a IU da web local relatórios **10.0.0.0.0.10293** e os relatórios do portais do Azure **10.0.10293.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Ignore este passo se estivesse a executar StorSimple Virtual matriz atualização 0,5 (**10.0.10290.0**) antes de aplicar esta atualização. Apontou da versão de software no passo 1 antes de iniciar a atualização. Se estivesse a executar a atualização 0,5, o agente MDS já está atualizado.

    Se estiver a executar uma versão de software antes da atualização 0,5, o passo seguinte para está a atualizar o agente do MDS. No **atualização de Software** página, vá para o **caminho do ficheiro de atualização** e navegue para o `GenevaMonitoringAgentPackageInstaller.msi` ficheiro. Repita os passos 2 a 4. Depois da matriz virtual reinicia, inicie sessão na IU da web do local.

7. Repita os passos 2 a 4 para instalar a segurança do Windows corrige utilizando ficheiros `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, e `windows8.1-kb4019213-x64`. A matriz virtual reiniciado depois de cada instalação e precisar de iniciar sessão para a IU da web do local.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

