---
title: "Web matriz Virtual StorSimple administração de IU | Microsoft Docs"
description: "Descreve como efetuar tarefas de administração do dispositivo básico através da IU da web de matriz Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Utilizar a IU da Web para administrar a matriz de Virtual StorSimple
![fluxo de processo de configuração](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Descrição geral
Os tutoriais neste artigo aplicam-se para o Microsoft Azure StorSimple Virtual matriz (também conhecido como o local no dispositivo virtual StorSimple) com o lançamento de disponibilidade geral (DG) de Março de 2016. Este artigo descreve algumas das tarefas de gestão que podem ser efetuadas na matriz Virtual StorSimple e fluxos de trabalho complexos. Pode gerir a matriz de Virtual StorSimple, utilizando o Gestor do StorSimple IU (referida como a IU do portal) e local IU da web do dispositivo de serviço. Este artigo incida nas tarefas que podem executar utilizando a IU da web.

Este artigo inclui os seguintes tutoriais:

* Obter a chave de encriptação de dados do serviço
* Resolver erros de configuração do web da IU
* Gerar um pacote de registo
* Encerrar ou reiniciar o seu dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obter a chave de encriptação de dados do serviço
Uma chave de encriptação de dados do serviço é gerada quando registar o seu primeiro dispositivo com o serviço StorSimple Manager. Esta chave, em seguida, é necessário com a chave de registo do serviço para registar dispositivos adicionais com o serviço StorSimple Manager.

Se tiver no local incorreto a chave de encriptação de dados do serviço e a necessidade para obtê-lo, execute os seguintes passos no web local da IU do dispositivo registado com o serviço.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obter a chave de encriptação de dados do serviço
1. Estabelecer ligação com a IU da web do local. Aceda a **configuração** > **definições da Cloud**.
2. Na parte inferior da página, clique em **Get chave de encriptação de dados do serviço**. Será apresentada uma chave. Copie e guarde esta chave.
   
    ![obter a chave de encriptação de dados do serviço 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Resolver erros de configuração do web da IU
Em alguns casos quando configurou o dispositivo através da web local IU, poderá executar erros. Para diagnosticar e resolver esses erros, pode executar os testes de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Para executar os testes de diagnóstico
1. No local IU da web, aceda a **resolução de problemas** > **os testes de diagnóstico**.
   
    ![executar diagnósticos 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Na parte inferior da página, clique em **executar testes de diagnóstico**. Isto iniciará a testes para diagnosticar possíveis problemas na sua rede, o dispositivo, o web proxy, hora, ou as definições da nuvem. Será notificado de que o dispositivo está a executar testes.
3. Depois de concluir os testes, os resultados serão apresentados. O exemplo seguinte mostra os resultados dos testes de diagnóstico. Tenha em atenção que as definições de proxy web não foram configuradas neste dispositivo e, por conseguinte, o teste do web proxy não foi executado. Todos os outros testes para as definições de rede, servidor DNS e as definições de hora foram concluída com êxito.
   
    ![executar diagnósticos 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Gerar um pacote de registo
Um pacote de registo é composta por todos os registos relevantes que podem ajudar a Microsoft Support resolver quaisquer problemas de dispositivos. Nesta versão, um pacote de registo pode ser gerado através da IU da web local.

#### <a name="to-generate-the-log-package"></a>Para gerar o pacote de registo
1. No local IU da web, aceda a **resolução de problemas** > **os registos do sistema**.
   
    ![gerar o pacote de registo 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Na parte inferior da página, clique em **criar pacote de registo**. Será criado um pacote dos registos do sistema. Esta ação irá demorar alguns minutos.
   
    ![gerar o pacote de registo 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Será notificado depois do pacote é criado com êxito e a página será atualizada para indicar a hora e data quando o pacote foi criado.
   
    ![gerar o pacote do registo 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Clique em **pacote de transferência do registo**. Um pacote zipped será transferido no seu sistema.
   
    ![gerar o pacote de registo 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Pode deszipe o pacote transferido do registo e ver os ficheiros de registo do sistema.

## <a name="shut-down-and-restart-your-device"></a>Encerrar e reiniciar o dispositivo
Pode encerrar ou reiniciar o dispositivo virtual utilizando a IU da web do local. Iremos Recomendamos antes de reiniciar, colocar os volumes ou partilhas offline no anfitrião e, em seguida, o dispositivo. Desta forma, serão qualquer possibilidade de danos nos dados. 

#### <a name="to-shut-down-your-virtual-device"></a>Para encerrar o dispositivo virtual
1. No local IU da web, aceda a **manutenção** > **as definições de energia**.
2. Na parte inferior da página, clique em **encerramento**.
   
    ![encerramento do dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Será apresentado um aviso a indicar que um encerramento do dispositivo interromperão qualquer e/s que estavam em curso, resultando num tempo de inatividade. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Aviso de encerramento do dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Será notificado de que foi iniciado o encerramento.
   
    ![encerramento do dispositivo foi iniciado](./media/storsimple-ova-web-ui-admin/image38.png)
   
    O dispositivo irá agora encerrar. Se pretender iniciar o seu dispositivo, terá de fazê-lo através do Gestor de Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar o dispositivo virtual
1. No local IU da web, aceda a **manutenção** > **as definições de energia**.
2. Na parte inferior da página, clique em **reiniciar**.
   
    ![reinício de dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)
3. Será apresentado um aviso a indicar que a reiniciar o dispositivo irá interromper qualquer IOs que estavam em curso, resultando num tempo de inatividade. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Reinicie o aviso](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Será notificado de que o reinício foi iniciado.
   
    ![reinício iniciado](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Enquanto o reinício está em curso, irá perder a ligação para a IU. Pode monitorizar o reinício, atualizando a IU periodicamente. Em alternativa, pode monitorizar o estado de reinício do dispositivo através do Gestor de Hyper-V.

## <a name="next-steps"></a>Passos seguintes
Saiba como [utilizar o serviço StorSimple Manager para gerir o seu dispositivo](storsimple-virtual-array-manager-service-administration.md).

