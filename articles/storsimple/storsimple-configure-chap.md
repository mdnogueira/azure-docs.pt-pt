---
title: "Configurar o CHAP para o dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve como configurar o protocolo de autenticação de Handshake de desafio (CHAP) num dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 467044d7-7885-4382-90bd-3148dbbd341f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 36b4e73d0336deb9560d44163fc5330d1c9d775c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-chap-for-your-storsimple-device"></a>Configurar o CHAP para o dispositivo StorSimple
Este tutorial explica como configurar o CHAP para o dispositivo StorSimple. O procedimento detalhado neste artigo aplica-se a série 8000 do StorSimple, bem como dispositivos StorSimple 1200.

CHAP representa Challenge Handshake Authentication Protocol. É um esquema de autenticação utilizado pelos servidores para validar a identidade de clientes remotos. A verificação é baseada numa palavra-passe partilhada ou o segredo. CHAP pode ser unidirecional (unidirecionais) ou mútua (bidirecional). CHAP unidirecional é quando o destino autentica um iniciador. CHAP inverso ou mútua, por outro lado, requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. Autenticação de iniciador pode ser implementada sem a autenticação de destino. No entanto, a autenticação de destino pode ser implementada apenas se a autenticação de iniciador também é implementada. 

Como melhor prática, recomendamos que utilize CHAP para melhorar a segurança de iSCSI.

> [!NOTE]
> Tenha em atenção que IPSEC não é atualmente suportado em dispositivos StorSimple.
> 
> 

As definições CHAP no dispositivo StorSimple podem ser configuradas das seguintes formas:

* Autenticação unidirecional ou unidirecional
* Bidirecional ou a autenticação mútua ou inversa

Em cada um nestes casos, o portal para o dispositivo e o software de iniciador iSCSI do servidor tem de ser configurado. Os passos detalhados para esta configuração estão descritos no tutorial seguinte.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidirecional ou unidirecional
Na autenticação unidirecional, o destino autentica o iniciador. Esta autenticação necessita que configure as definições de iniciador do CHAP no dispositivo StorSimple e o iSCSI software iniciador do anfitrião. Os procedimentos detalhados para o seu dispositivo StorSimple e o anfitrião do Windows são descritos a seguir.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar o seu dispositivo para autenticação unidirecional
1. No portal clássico do Azure, no **dispositivos** página, clique em de **configurar** separador.
   
    ![Iniciador do CHAP](./media/storsimple-configure-chap/IC740943.png)
2. Desloque para baixo nesta página e, além de **Iniciador do CHAP** secção:
   
   1. Forneça um nome de utilizador para o Iniciador do CHAP.
   2. Forneça uma palavra-passe para o Iniciador do CHAP.
      
    > [!IMPORTANT]
    > O nome de utilizador tem de conter menos de 233 carateres. A palavra-passe do CHAP tem de ter entre 12 e 16 carateres. Um nome de utilizador ou palavra-passe mais ocorrerá uma falha de autenticação no anfitrião do Windows.
   
   3. Confirme a palavra-passe.
3. Clique em **Guardar**. Será apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação unidirecional no servidor de anfitrião do Windows
1. No servidor de anfitrião do Windows, inicie o Iniciador do iSCSI.
2. No **propriedades do iniciador iSCSI** janela, execute os seguintes passos:
   
   1. Clique em de **deteção** separador.
      
       ![Propriedades do iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Clique em **detetar Portal**.
3. No **detetar Portal de destino** caixa de diálogo:
   
   1. Especifique o endereço IP do seu dispositivo.
   2. Clique em **avançadas**.
      
       ![Detetar portal de destino](./media/storsimple-configure-chap/IC740945.png)
4. No **definições avançadas** caixa de diálogo:
   
   1. Selecione o **ativar CHAP iniciar sessão** caixa de verificação.
   2. No **nome** campo, forneça o nome de utilizador que especificou para o Iniciador do CHAP no portal clássico.
   3. No **segredo de destino** campo, forneça a palavra-passe que especificou para o Iniciador do CHAP no portal clássico.
   4. Clique em **OK**.
      
       ![Definições avançadas gerais](./media/storsimple-configure-chap/IC740946.png)
5. No **destinos** separador do **propriedades do iniciador iSCSI** janela, o estado do dispositivo deve aparecer como **ligado**. Se estiver a utilizar um dispositivo StorSimple 1200, em seguida, cada volume será montado como um destino iSCSI conforme mostrado abaixo. Por conseguinte, os passos 3 a 4 terá de ser repetido para cada volume.
   
    ![Volumes montados como destinos de separada](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Se alterar o nome de iSCSI, o novo nome será utilizado para novas sessões iSCSI. Novas definições não são utilizadas para existente sessões até terminar a sessão e iniciar sessão novamente.
   > 
   > 

Para obter mais informações sobre como configurar o CHAP no servidor de anfitrião do Windows, aceda a [considerações adicionais](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Autenticação bidirecional ou mútua
Na autenticação bidirecional, o destino autentica o iniciador e, em seguida, o iniciador autentica o destino. Isto requer que o utilizador configure as definições de iniciador do CHAP, bem como as definições CHAP inversas sobre o software de iniciador do dispositivo e do iSCSI no anfitrião. Os procedimentos seguintes descrevem os passos para configurar a autenticação mútua no dispositivo e o anfitrião do Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar o seu dispositivo para autenticação mútua
1. No portal clássico do Azure, no **dispositivos** página, clique em de **configurar** separador.
   
    ![Destino do CHAP](./media/storsimple-configure-chap/IC740948.png)
2. Desloque para baixo nesta página e, além de **CHAP destino** secção:
   
   1. Forneça um **CHAP inverso nome de utilizador** para o seu dispositivo.
   2. Forneça um **CHAP inverso palavra-passe** para o seu dispositivo.
   3. Confirme a palavra-passe.
3. No **Iniciador do CHAP** secção:
   
   1. Forneça um **nome de utilizador** para o seu dispositivo.
   2. Forneça um **palavra-passe** para o seu dispositivo.
   3. Confirme a palavra-passe.
4. Clique em **Guardar**. Será apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação de bidirecional no servidor de anfitrião do Windows
1. No servidor de anfitrião do Windows, inicie o Iniciador do iSCSI.
2. No **propriedades do iniciador iSCSI** janela, clique em de **configuração** separador.
3. Clique em **CHAP**.
4. No **iSCSI segredo Iniciador do CHAP mútua** caixa de diálogo:
   
   1. Tipo de **inverter a palavra-passe de CHAP** que configurou no portal clássico do Azure.
   2. Clique em **OK**.
      
       ![segredo do CHAP mútua iniciador iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Clique em de **destinos** separador.
6. Clique em de **Connect** botão. 
7. No **ligar para o destino** caixa de diálogo, clique em **avançadas**.
8. No **propriedades avançadas** caixa de diálogo:
   
   1. Selecione o **ativar CHAP iniciar sessão** caixa de verificação.
   2. No **nome** campo, forneça o nome de utilizador que especificou para o Iniciador do CHAP no portal clássico.
   3. No **segredo de destino** campo, forneça a palavra-passe que especificou para o Iniciador do CHAP no portal clássico.
   4. Selecione o **efetue a autenticação mútua** caixa de verificação.
      
       ![Autenticação mútua definições avançadas](./media/storsimple-configure-chap/IC740950.png)
   5. Clique em **OK** para concluir a configuração do CHAP

Para obter mais informações sobre como configurar o CHAP no servidor de anfitrião do Windows, aceda a [considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais
O **ligar rápida** funcionalidade não suporta ligações que tenham CHAP ativada. CHAP estiver ativada, certifique-se de que utiliza o **Connect** botão está disponível na **destinos** separador para ligar a um destino.

![Ligar ao destino](./media/storsimple-configure-chap/IC740947.png)

No **ligar ao destino** caixa de diálogo que é apresentada, selecione o **adicionar esta ligação para a lista de destinos favorito** caixa de verificação. Isto garante que sempre que o computador é reiniciado, efetuada uma tentativa está a restaurar a ligação destinos iSCSI favorito.

## <a name="errors-during-configuration"></a>Erros durante a configuração
Se a configuração CHAP está incorreta, em seguida, é provável que ver um **falha de autenticação** mensagem de erro.

## <a name="verification-of-chap-configuration"></a>Verificação da configuração do CHAP
Pode verificar que o CHAP está a ser utilizado, efetuando os seguintes passos.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar a configuração do CHAP
1. Clique em **destinos favoritos**.
2. Selecione o destino para que ativou a autenticação.
3. Clique em **detalhes**.
   
    ![destinos iSCSI do iniciador propriedades favorito](./media/storsimple-configure-chap/IC740951.png)
4. No **favorito detalhes de destino** diálogo caixa, tenha em atenção a entrada no **autenticação** campo. Se a configuração tiver sido bem-sucedido, deverá indicar **CHAP**.
   
    ![Detalhes de destino favorito](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [segurança do StorSimple](storsimple-security.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

