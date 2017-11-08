---
title: "Modificar os dados 0 definições num dispositivo StorSimple | Microsoft Docs"
description: Saiba como utilizar o Windows PowerShell para StorSimple para reconfigurar a interface rede DATA 0 no dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 58e3d509-f425-4a7f-b650-d496a7c85193
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 089988354c5e1ca2e8d5d1554084062a655e43da
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>Modificar as definições de interface de rede 0 de dados no dispositivo StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [modificar as definições de interface de rede 0 de dados no dispositivo StorSimple](storsimple-8000-modify-data-0.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
O dispositivo Microsoft Azure StorSimple possui seis interfaces de rede, a partir dos dados 0 a 5 de dados. Os dados 0 interface é sempre configurada através da interface do Windows PowerShell ou a consola de série e é automaticamente ativado para a nuvem. Tenha em atenção que não é possível configurar interface rede DATA 0 através do portal clássico do Azure. 

Inicial dos dados 0 a interface de rede pela primeira vez é configurada através do Assistente de configuração durante a implementação do dispositivo StorSimple. Quando o dispositivo está num modo operacional, poderá ter de reconfigurar dados 0 definições. Este tutorial fornece dois métodos para modificar dados 0 rede definições, tanto através do Windows PowerShell para StorSimple.

Depois de ler este tutorial, será capaz de:

* Modificar dados 0 definição através do Assistente de configuração de rede
* Modificar definições de rede 0 de dados através de `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificar as definições de rede 0 de dados através do Assistente de configuração
Pode reconfigurar as definições de rede 0 de dados ao ligar à interface do Windows PowerShell do dispositivo StorSimple e iniciar uma sessão de Assistente de configuração. Execute os seguintes passos para modificar dados 0 definições:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Para modificar as definições de rede 0 de dados através do Assistente de configuração
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando lhe for pedido fornecer o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Invoke-HcsSetupWizard`
3. Será apresentado um Assistente de configuração para o ajudar a configurar os dados 0 interface do seu dispositivo. Forneça novos valores para o endereço IP, o gateway e a máscara de rede.

> [!NOTE]
> Os controladores fixos IPs terá de ser reconfigurado através de **configurar** página do dispositivo StorSimple no portal clássico do Azure. Para obter mais informações, aceda a [modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces).
> 
> 

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificar as definições de rede 0 de dados através do cmdlet Set-HcsNetInterface
Uma maneira alternativa reconfigurar DATA 0 é de interface de rede através da utilização do `Set-HcsNetInterface` cmdlet. O cmdlet é executado a partir da interface do Windows PowerShell do dispositivo StorSimple. Quando utilizar este procedimento, o controlador de IPs fixo também pode ser configurado aqui. Execute os seguintes passos para modificar os dados 0 definições: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Para modificar as definições de rede 0 de dados através do cmdlet Set-HcsNetInterface
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando lhe for pedido forneça a palavra-passe de administrador do dispositivo. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Entre parênteses Retos angled, escreva os seguintes valores para os dados 0:
   
   * Endereço IPv4
   * Gateway de IPv4
   * Máscara de sub-rede IPv4
   * Endereço IPv4 fixo para o controlador 0
   * Endereço IPv4 fixo para o controlador 1
     
     Para obter mais informações sobre a utilização deste cmdlet, aceda a [do Windows PowerShell para StorSimple referência de cmdlet](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Passos seguintes
* Para configurar as interfaces de rede que não sejam dados 0, pode utilizar o [Configurar página no portal clássico do Azure](storsimple-modify-device-config.md). 
* Se ocorrer algum problema quando configurar as interfaces de rede, consulte [resolver problemas de implementação](storsimple-troubleshoot-deployment.md).

