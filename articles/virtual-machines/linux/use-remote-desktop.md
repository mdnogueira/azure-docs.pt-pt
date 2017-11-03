---
title: Utilizar o ambiente de trabalho remoto para uma VM com Linux no Azure | Microsoft Docs
description: "Saiba como instalar e configurar o ambiente de trabalho remoto (xrdp) para ligar a uma VM com Linux no Azure com as ferramentas gráficas"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: iainfou
ms.openlocfilehash: d8d6130a270285c84c1dd057a3512cdeb39287f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalar e configurar o ambiente de trabalho remoto para ligar a uma VM com Linux no Azure
Máquinas de virtuais (VMs) com Linux no Azure, normalmente, são geridas na linha de comandos utilizando uma ligação secure shell (SSH). Quando são novos para Linux ou para cenários de resolução de problemas rápidos, a utilização de ambiente de trabalho remoto pode ser mais fácil. Este artigo fornece detalhes sobre como instalar e configurar um ambiente de trabalho ([xfce](https://www.xfce.org)) e o ambiente de trabalho remoto ([xrdp](http://www.xrdp.org)) para a VM com Linux utilizando o modelo de implementação Resource Manager.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer uma VM com Linux existente no Azure. Se precisar de criar uma VM, utilize um dos seguintes métodos:

- O [CLI do Azure 2.0](quick-create-cli.md)
- O [portal do Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalar um ambiente de trabalho na sua VM do Linux
A maioria das VMs com Linux no Azure não dispõe de um ambiente de trabalho instalado por predefinição. VMs com Linux normalmente são geridas através de ligações SSH em vez de um ambiente de trabalho. Existem vários ambientes de trabalho no Linux que pode escolher. Dependendo da sua escolha de ambiente de trabalho, este pode consumir um a 2 GB de espaço em disco e demorar 5 a 10 minutos para instalar e configurar todos os pacotes necessários.

O exemplo seguinte instala o lightweight [xfce4](https://www.xfce.org/) ambiente de trabalho numa VM com Ubuntu. Os comandos para outras distribuições variam ligeiramente (utilizar `yum` para instalar no Red Hat Enterprise Linux e configurar adequado `selinux` regras ou utilize `zypper` para instalar no SUSE, por exemplo).

Primeiro, SSH para a VM. O exemplo seguinte estabelece ligação à VM com o nome *myvm.westus.cloudapp.azure.com* com o nome de utilizador de *azureuser*:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se estiver a utilizar o Windows e precisar de mais informações sobre como utilizar o SSH, consulte [das chaves de como utilizar o SSH com o Windows](ssh-from-windows.md).

Em seguida, instale xfce utilizando `apt` da seguinte forma:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalar e configurar um servidor de ambiente de trabalho remoto
Agora que tem instalado um ambiente de trabalho, configure um serviço de ambiente de trabalho remoto para escutar conexões de entrada. [xrdp](http://xrdp.org) é um servidor de protocolo RDP (Remote Desktop Protocol) de open source para que não está disponível na maioria das distribuições de Linux e funciona bem com xfce. Instale xrdp a VM com Ubuntu da seguinte forma:

```bash
sudo apt-get install xrdp
```

Indique xrdp o ambiente de trabalho a utilizar quando iniciar a sessão. Configure xrdp utilizar xfce como o seu ambiente de trabalho da seguinte forma:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço de xrdp para que as alterações entrem em vigor da seguinte forma:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir uma palavra-passe da conta de utilizador local
Se tiver criado uma palavra-passe para a sua conta de utilizador quando criou a VM, ignore este passo. Se utilizar a autenticação de chave SSH apenas e não dispõe de uma palavra-passe de conta local definido, especifique uma palavra-passe antes de utilizar xrdp para iniciar sessão VM. xrdp não pode aceitar chaves SSH para a autenticação. O exemplo seguinte especifica uma palavra-passe da conta de utilizador *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Especificar uma palavra-passe não atualizar a configuração de SSHD para permitir inícios de sessão de palavra-passe se atualmente não existir. Numa perspetiva de segurança, poderá pretender ligar à VM com um túnel SSH através da autenticação baseada em chave e, em seguida, ligar a xrdp. Se assim for, ignore o passo seguinte sobre a criação de uma regra de grupo de segurança de rede para permitir tráfego de ambiente de trabalho remoto.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra de grupo de segurança de rede para tráfego de ambiente de trabalho remoto
Para permitir tráfego de ambiente de trabalho remoto para aceder a VM com Linux, de segurança de rede tem de regra de grupo ser criado que permite que o TCP na porta 3389 alcançar a VM. Para obter mais informações sobre regras do grupo de segurança de rede, consulte [que é um grupo de segurança de rede?](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Também pode [utilizar o portal do Azure para criar uma regra de grupo de segurança de rede](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Os exemplos seguintes criar uma regra de grupo de segurança de rede com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create) denominado *myNetworkSecurityGroupRule* para *permitir* tráfego na *tcp* porta *3389*.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1010 \
    --destination-port-range 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ligar a VM com Linux com um cliente de ambiente de trabalho remoto
Abra o seu cliente de ambiente de trabalho remoto local e ligar ao endereço IP ou nome DNS da sua VM do Linux. Introduza o nome de utilizador e palavra-passe da conta de utilizador na sua VM da seguinte forma:

![Ligar ao xrdp utilizando o cliente de ambiente de trabalho remoto](./media/use-remote-desktop/remote-desktop-client.png)

Após a autenticação, o ambiente de trabalho xfce irá carregar e ter um aspeto semelhante ao seguinte exemplo:

![ambiente de trabalho xfce através de xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Resolução de problemas
Se não conseguir ligar à VM com Linux utilizando um cliente de ambiente de trabalho remoto, utilize `netstat` na sua VM do Linux para verificar que a VM está à escuta para ligações RDP da seguinte forma:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo seguinte mostra a VM está a escutar a porta TCP 3389 conforme esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o serviço de xrdp não está a escutar, numa VM com Ubuntu reinicie o serviço da seguinte forma:

```bash
sudo service xrdp restart
```

Reveja os registos na */var/registo*Thug na VM com Ubuntu indicações sobre por que motivo o serviço pode não estar a responder. Também pode monitorizar o syslog durante uma tentativa de ligação de ambiente de trabalho remoto para ver os erros:

```bash
tail -f /var/log/syslog
```

Outras as distribuições do Linux, tais como Red Hat Enterprise Linux e SUSE podem ter diferentes formas para reiniciar os serviços e as localizações de ficheiros de registo alternativo para rever.

Se não recebeu qualquer resposta no seu cliente de ambiente de trabalho remoto e não vir quaisquer eventos no registo do sistema, este comportamento indica que o tráfego de ambiente de trabalho remoto não é possível contactar a VM. Reveja as regras de grupo de segurança de rede para se certificar de que tem uma regra para permitir o TCP na porta 3389. Para obter mais informações, consulte [resolver problemas de conectividade de aplicação](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como criar e utilizar chaves SSH com VMs com Linux, consulte [criar SSH chaves para VMs com Linux no Azure](mac-create-ssh-keys.md).

Para obter informações sobre como utilizar o SSH do Windows, consulte [das chaves de como utilizar o SSH com o Windows](ssh-from-windows.md).

