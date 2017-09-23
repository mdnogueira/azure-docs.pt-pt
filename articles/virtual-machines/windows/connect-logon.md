---
title: Ligar a uma VM do Windows Server | Microsoft Docs
description: "Saiba como ligar e iniciar sessão numa VM do Windows utilizando o Portal do Azure e o modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 88431377a36d5bc36220c630f0c8d4a46ab4a434
ms.contentlocale: pt-pt
ms.lasthandoff: 03/31/2017

---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Como ligar e iniciar sessão numa máquina virtual do Azure a executar o Windows
Irá utilizar o botão **Ligar** no Portal do Azure para iniciar uma sessão de Ambiente de Trabalho Remoto (RDP) a partir de um ambiente de trabalho do Windows. Ligue primeiro à máquina virtual e, em seguida, inicie sessão.

Se estiver a tentar ligar a uma VM do Windows a partir de um Mac, terá de instalar um cliente RDP para Mac, como [o protocolo RDP (Remote Desktop Protocol) da Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual
1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Virtual Machines**.
3. Selecione a máquina virtual na lista.
4. No painel da máquina virtual, clique em **Ligar**.
   
    ![Captura de ecrã do Portal do Azure que mostra como ligar à VM.](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Se o botão **Ligar** no portal estiver a cinzento e não estiver ligado ao Azure através de uma ligação [Express Route](../../expressroute/expressroute-introduction.md) ou [Rede de VPNs](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), terá de criar e atribuir um endereço IP público à VM antes de poder utilizar o RDP. Pode ler mais sobre [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passos seguintes
Caso se depare com problemas ao tentar ligar, veja [Troubleshoot Remote Desktop connections (Resolução de Problemas de Ligações ao Ambiente de Trabalho Remoto)](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este artigo orienta-o ao longo do diagnóstico e da resolução de problemas comuns.


