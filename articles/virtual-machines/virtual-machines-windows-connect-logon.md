<properties
    pageTitle="Ligar a uma VM do Windows Server | Microsoft Azure"
    description="Saiba como ligar e iniciar sessão numa VM do Windows utilizando o Portal do Azure e o modelo de implementação Resource Manager."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# Como ligar e iniciar sessão numa máquina virtual do Azure a executar o Windows 


Irá utilizar o botão **Ligar** no Portal do Azure para iniciar uma sessão de Ambiente de Trabalho Remoto (RDP). Ligue primeiro à máquina virtual e, em seguida, inicie sessão.

## Ligar à máquina virtual

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).

2.  No menu Hub, clique em **Virtual Machines**.

3.  Selecione a máquina virtual na lista.

4. No painel da máquina virtual, clique em **Ligar**.

    ![Captura de ecrã do Portal do Azure que mostra como ligar à VM.](./media/virtual-machines-windows-connect-logon/connect.png)
    
 > [AZURE.TIP] Se o botão **Ligar** no portal estiver a cinzento e não estiver ligado ao Azure através de uma ligação [Express Route](../expressroute/expressroute-introduction.md) ou [Rede de VPNs](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), terá de criar e atribuir um endereço IP público à VM antes de poder utilizar o RDP. Pode ler mais sobre [endereços IP públicos no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## Iniciar sessão na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## Passos seguintes

Caso se depare com problemas ao tentar ligar, veja [Troubleshoot Remote Desktop connections (Resolução de Problemas de Ligações ao Ambiente de Trabalho Remoto)](virtual-machines-windows-troubleshoot-rdp-connection.md). Este artigo orienta-o ao longo do diagnóstico e da resolução de problemas comuns.



<!--HONumber=Aug16_HO1-->


