---
title: "Instalar a atualização 1.0 na matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve como utilizar a IU da web de matriz Virtual StorSimple para aplicar atualizações utilizando o método de portal e a correção do Azure"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalar a atualização 1.0 na sua matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar a atualização 1.0 na sua matriz Virtual StorSimple através da IU da web local e através do portal do Azure.

Aplicar as atualizações de software ou correções a par da matriz de Virtual StorSimple. Antes de aplicar uma atualização, recomendamos que colocar os volumes ou partilhas offline no anfitrião primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos nos dados. Depois dos volumes ou partilhas estiverem offline, deve também ter uma manual cópia de segurança do dispositivo.

> [!IMPORTANT]
> - Atualização 1.0 corresponde à **10.0.10296.0** versão do software no seu dispositivo. Para obter informações sobre quais são as Novidades nesta atualização, visite [notas de versão para atualização 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tenha em atenção que a instalar uma atualização ou correção reinicia o seu dispositivo. Dado que a matriz de Virtual StorSimple é um dispositivo de nó único, qualquer e/s em curso é interrompido e período de indisponibilidade ocorre com o seu dispositivo.
>
> - A atualização 1 está disponível no portal do Azure apenas se a matriz virtual está em execução de atualização 0,6. Para matrizes de virtuais com versões de 0,6 anterior à atualização, tem de instalar a atualização 0,6 primeiro e, em seguida, instalar a atualização 1.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Se executar a atualização 0.2 e posterior, recomendamos que instale as atualizações através do portal do Azure. O procedimento portal exige que o utilizador procurar, transferir e, em seguida, instale as atualizações. Consoante a versão de software que a matriz de virtual está em execução, aplicar a atualização através do portal do Azure é diferente.

 - Se a matriz de virtual estiver em execução de atualização 0,6, o portal do Azure instala diretamente atualização 1 (10.0.10296.0) no seu dispositivo. Este procedimento demora cerca de 7 minutos a concluir.
 - Se a matriz de virtual está a executar uma versão antes da atualização 0,6, a atualização for efetuada em duas fases. O portal do Azure instala pela primeira vez atualização 0,6 (10.0.10293.0) no seu dispositivo. A matriz virtual reinicia e o portal, em seguida, instala atualização 1 (10.0.10296.0) no seu dispositivo. Este procedimento demora cerca de 15 minutos a concluir.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Após a instalação estiver concluída, aceda ao seu serviço do Gestor de dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que acabou de ser atualizado. Aceda a **definições > Gerir > atualizações ao dispositivo**. A versão do software apresentados deve ser **10.0.10296.0**.

![Versão do software após a atualização](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Utilize a IU da web do local

Existem dois passos, ao utilizar a IU da web local:

* Transferir a atualização ou a correção
* Instalar a atualização ou a correção

> [!IMPORTANT] 
> **Continuar com esta atualização apenas se estiver a executar a atualização 0,6 (10.0.10293.0). Se estiver a executar uma versão anterior, [instalação atualização 0,6](storsimple-virtual-array-install-update-06.md) no seu dispositivo primeiro e, em seguida, aplicar Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Transferir a atualização ou a correção

Se a matriz de virtual estiver em execução de atualização 0,6, execute os seguintes passos para transferir a atualização 1 do catálogo Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correção

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se estiver a utilizar o catálogo Microsoft Update pela primeira vez neste computador, clique em **instalar** quando lhe for pedido para instalar o suplemento do catálogo Microsoft Update.

3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (KB) de correção que pretende transferir. Introduza **4047203** para atualização 1.0 e, em seguida, clique em **pesquisa**.
   
    A listagem de correção é apresentado, por exemplo, **StorSimple Virtual matriz atualização 1.0**.
   
    ![Catálogo de pesquisa](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Clique em **Transferir**.

5. Transfira os dois ficheiros para uma pasta. Também pode copiar a pasta de partilha de rede que seja acessível a partir do dispositivo.

6. Abra a pasta onde os ficheiros estão localizados.

    ![Ficheiros no pacote](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Consulte os dois ficheiros:
    -  Um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para atualizar o software de dispositivos.
    - Um ficheiro que contenha as atualizações cumulativas para Agosto `windows8.1-kb4034681-x64`. Para obter mais informações sobre o que está incluído neste rollup, aceda a [agregação de segurança mensal de Agosto](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correção

Antes da instalação de atualização ou correção, certifique-se de que:

 - Tem a atualização ou correção transferido localmente no anfitrião ou acessível através de uma partilha de rede.
 - A matriz de virtual está em execução de atualização 0,6 (10.0.10293.0). Se estiver a executar uma versão antes da atualização 0,6, [instalação atualização 0,6](storsimple-virtual-array-install-update-06.md) primeiro e, em seguida, instale a atualização 1.

Este procedimento demora cerca de 4 minutos a concluir. Execute os seguintes passos para instalar a atualização ou correção.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correção

1. No local IU da web, aceda a **manutenção** > **atualização de Software**. Tome nota da versão do software que estão em execução. **Continuar com esta atualização apenas se estiver a executar a atualização 0,6 (10.0.10293.0). Se estiver a executar uma versão anterior, [instalação atualização 0,6](storsimple-virtual-array-install-update-06.md) no seu dispositivo primeiro e, em seguida, aplicar Update 1.**
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. No **caminho do ficheiro de atualização**, introduza o nome de ficheiro para a atualização ou a correção. Também pode navegar para o ficheiro de instalação de atualização ou correção se colocado numa partilha de rede. Clique em **Aplicar**.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. É apresentado um aviso. A matriz virtual fornecida é um dispositivo de nó único, após a atualização ser aplicada, o reinício do dispositivo e existe um período de indisponibilidade. Clique no ícone de verificação.
   
   ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Inicia a atualização. Depois do dispositivo está atualizado com êxito, esta reiniciar. A IU do local não está acessível desta duração.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Após o reinício estiver concluído, é direcionado para o **sessão** página. Para verificar que o software do dispositivo atualizou, na web local IU, aceda ao **manutenção** > **atualização de Software**. A versão do software apresentados deve ser **10.0.0.0.0.10296** para atualização 1.0.
   
   > [!NOTE]
   > As versões do software elaboramos relatórios de forma ligeiramente diferente na IU da web local e o portal do Azure. Por exemplo, a IU da web local relatórios **10.0.0.0.0.10296** e os relatórios do portais do Azure **10.0.10296.0** para a mesma versão.
   
    ![atualizar o dispositivo](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Repita os passos 2 a 4 para instalar a correção de segurança do Windows utilizando o ficheiro `windows8.1-kb4012213-x64`. A matriz virtual é reiniciado após a instalação e precisar de iniciar sessão para a IU da web do local.

> [!NOTE]
> Se diretamente aplicado Update 1 num dispositivo com uma versão antes da atualização 0,6, estão em falta algumas atualizações. Contacte Support da Microsoft para os passos seguintes.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
