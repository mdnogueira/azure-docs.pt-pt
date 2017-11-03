---
title: "Modificar dados 0 definições do dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: Saiba como utilizar o Windows PowerShell para StorSimple para reconfigurar a interface rede DATA 0 no dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 90df43e22f17fd32fe642514df098b72700e77af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modificar as definições de interface de rede 0 de dados no seu dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral

O dispositivo Microsoft Azure StorSimple possui seis interfaces de rede, a partir dos dados 0 a 5 de dados. Os dados 0 interface é sempre configurada através da interface do Windows PowerShell ou a consola de série e é automaticamente ativado para a nuvem. Tenha em atenção que não é possível configurar interface rede DATA 0 através do portal do Azure.

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
3. É apresentado um Assistente de configuração para o ajudar a configurar os dados 0 interface do seu dispositivo. Forneça novos valores para o endereço IP, o gateway e a máscara de rede.

> [!NOTE]
> Os controladores fixos IPs terá de ser reconfigurado através de **as definições de rede** painel do dispositivo StorSimple no portal do Azure. Para obter mais informações, aceda a [modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces).

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
* Para configurar as interfaces de rede que não sejam dados 0, pode utilizar o [configurar definições de rede no portal do Azure](storsimple-8000-modify-device-config.md). 
* Se ocorrer algum problema quando configurar as interfaces de rede, consulte [resolver problemas de implementação](storsimple-troubleshoot-deployment.md).

