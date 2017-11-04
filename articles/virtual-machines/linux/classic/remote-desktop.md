---
title: Ambiente de trabalho remoto para uma VM com Linux | Microsoft Docs
description: "Saiba como instalar e configurar o ambiente de trabalho remoto para ligar a uma VM de Linux do Microsoft Azure para o modelo de implementação clássica"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: mingzhan
ms.openlocfilehash: 2ad497a0244f9c7cdad34faf807cc9ed10ea704d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Utilizar um Ambiente de Trabalho Remoto para ligar a uma VM do Linux no Microsoft Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para a versão do Resource Manager atualizada deste artigo, consulte [aqui](../use-remote-desktop.md).

## <a name="overview"></a>Descrição geral
RDP (protocolo de ambiente de trabalho remoto) é um protocolo proprietário utilizado para o Windows. Como podemos utilizar o RDP para ligar a uma VM com Linux (máquina virtual) remotamente?

Esta orientação irá dar-lhe a resposta! Irá ajudá-lo a instalação e configuração xrdp no Microsoft Azure Linux VM, que permite estabelecer ligação ao mesmo com o ambiente de trabalho remoto a partir de uma máquina Windows. Utilizaremos VM com Linux com Ubuntu ou OpenSUSE como o exemplo nesta orientação.

A ferramenta de xrdp é um servidor RDP de open source para que lhe permite ligar o servidor Linux com o ambiente de trabalho remoto a partir de um computador Windows. RDP tem um melhor desempenho ao VNC (informática por rede Virtual). VNC composições utilizar gráficos de qualidade de JPEG e pode ser lenta, enquanto que o RDP é rápido e crystal encriptado.

> [!NOTE]
> Já tem de ter uma VM do Azure da Microsoft com o Linux. Para criar e configurar uma VM com Linux, consulte o [tutorial da VM do Linux do Azure](createportal.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Criar um ponto final para o ambiente de trabalho remoto
Utilizamos o ponto final predefinido 3389 para ambiente de trabalho remoto neste documento. Configurar o ponto final 3389 como `Remote Desktop` na VM com Linux como abaixo:

![Imagem](./media/remote-desktop/endpoint-for-linux-server.png)

Se não souber como configurar um ponto final para a VM, consulte [esta orientação](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Instalar o ambiente de trabalho Gnome
Ligar à VM com Linux através do `putty`e instalar `Gnome Desktop`.

Ubuntu, utilize:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Para OpenSUSE, utilize:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Instalar xrdp
Ubuntu, utilize:

    #sudo apt-get install xrdp

Para OpenSUSE, utilize:

> [!NOTE]
> Atualize a versão de OpenSUSE com a versão que está a utilizar o seguinte comando. O exemplo abaixo é para `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Iniciar xrdp e definir o serviço de xdrp em cima de arranque
Para OpenSUSE, utilize:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Para Ubuntu, xrdp será iniciado e ativado no arranque-se automaticamente após a instalação.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Utilizar xfce se estiver a utilizar uma versão de Ubuntu posterior Ubuntu 12.04LTS
Porque a versão atual do xrdp não suporta Gnome ambiente de trabalho para as versões do Ubuntu posterior Ubuntu 12.04LTS, utilizaremos `xfce` ambiente de trabalho em vez disso.

Para instalar `xfce`, utilize este comando:

    #sudo apt-get install xubuntu-desktop

Em seguida, ative `xfce` utilizando este comando:

    #echo xfce4-session >~/.xsession

Edite o ficheiro de configuração `/etc/xrdp/startwm.sh`:

    #sudo vi /etc/xrdp/startwm.sh   

Adicione a linha `xfce4-session` antes da linha `/etc/X11/Xsession`.

Para reiniciar o serviço de xrdp, utilizá-lo:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Ligar a VM com Linux a partir de uma máquina Windows
Numa máquina Windows, inicie o cliente de ambiente de trabalho remoto e introduza o nome de DNS de VM do Linux. Ou aceda ao Dashboard da sua VM no portal do Azure e clique em `Connect` para ligar a VM com Linux. Nesse caso, consulte a janela de início de sessão:

![Imagem](./media/remote-desktop/no2.png)

Inicie sessão com o nome de utilizador e palavra-passe da sua VM do Linux.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como utilizar xrdp, consulte [http://www.xrdp.org/](http://www.xrdp.org/).
