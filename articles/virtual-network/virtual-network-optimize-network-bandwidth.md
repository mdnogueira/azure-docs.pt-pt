---
title: "Otimizar o débito de rede VM | Microsoft Docs"
description: "Saiba como otimizar o débito de rede de máquina virtual do Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: 914747983d4d974810836be66d6c6af343f58b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar o débito de rede para máquinas virtuais do Azure

Máquinas virtuais do Azure (VM) tem predefinições de rede que podem ser mais otimizadas para débito de rede. Este artigo descreve como otimizar o débito de rede para o Microsoft Azure Windows e VMs do Linux, incluindo as distribuições principais como Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM do Windows

Se a VM do Windows é suportada com [acelerados redes](virtual-network-create-vm-accelerated-networking.md), ativar essa funcionalidade seria a configuração ideal para débito. Para todas as outras VMs do Windows, utilizar o dimensionamento do lado da receção (RSS) pode contactar um maior débito garantido que uma VM sem RSS. O RSS pode estar desativado por predefinição numa VM do Windows. Conclua os passos seguintes para determinar se o RSS está ativado e ativá-la se está desativada.

1. Introduza o `Get-NetAdapterRss` comando do PowerShell para ver se o RSS está ativado para um adaptador de rede. No seguinte exemplo de saída devolvido do `Get-NetAdapterRss`, o RSS não está ativado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Introduza o seguinte comando para ativar o RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não possui uma saída. O comando Alterar definições da NIC, a causar a perda de conectividade temporário para cerca de um minuto. É apresentada uma caixa de diálogo de Reconnecting durante a perda de conectividade. Normalmente, conectividade ser restaurada depois da tentativa de terceira.
3. Confirme que o RSS está ativado na VM introduzindo o `Get-NetAdapterRss` novamente o comando. Se tiver êxito, é devolvido o seguinte exemplo de saída:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>VM com Linux

O RSS está sempre ativado por predefinição no VM Linux do Azure. Kernels Linux lançadas desde de Janeiro de 2017 incluem novas opções de otimização de rede que permitem uma VM com Linux alcançar o débito de rede superior.

### <a name="ubuntu"></a>Ubuntu

Para obter a otimização, primeiro atualizar para a versão mais recente suportada, a partir de Junho de 2017, que é:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Após a atualização estiver concluída, introduza os seguintes comandos para obter o kernel mais recente:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Comando opcional:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-preview-kernel"></a>Kernel de pré-visualização do Ubuntu do Azure
> [!WARNING]
> Este kernel de pré-visualização do Azure Linux pode não ter o mesmo nível de disponibilidade e fiabilidade como imagens do Marketplace e kernels que estão em geral disponibilidade versão. A funcionalidade não é suportada, pode ter restrita capacidades e não pode ser tão fiável como kernel predefinido. Não utilize este kernel para cargas de trabalho de produção.

Desempenho de débito significativas pode ser alcançado instalando o kernel proposto do Linux do Azure. Para experimentar este kernel, adicione esta linha para /etc/apt/sources.list

```bash
#add this to the end of /etc/apt/sources.list (requires elevation)
deb http://archive.ubuntu.com/ubuntu/ xenial-proposed restricted main multiverse universe
```

Em seguida, execute estes comandos como raiz.
```bash
apt-get update
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter a otimização, primeiro atualizar para a versão mais recente suportada, a partir de Julho de 2017, que é:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
Após a atualização estiver concluída, instale os serviços de integração de Linux (LIS) mais recente.
A otimização de débito está a ser LIS, começando 4.2.2-2. Introduza os seguintes comandos para instalar o LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter a otimização, primeiro atualizar para a versão mais recente suportada, a partir de Julho de 2017, que é:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017071923"
```
Após a atualização estiver concluída, instale os serviços de integração de Linux (LIS) mais recente.
A otimização de débito está a ser LIS, começando 4.2. Introduza os comandos seguintes para transferir e instalar o LIS:

```bash
mkdir lis4.2.2-2
cd lis4.2.2-2
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
tar xvzf lis-rpms-4.2.2-2.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre 4.2 de versão de serviços de integração Linux para Hyper-V, visualizando o [página de transferência](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passos seguintes
* Agora que a VM está otimizada, consulte o resultado com [testar VM do Azure de largura de banda/débito](virtual-network-bandwidth-testing.md) para o seu cenário.
* Saiba mais com [Azure Virtual Network perguntas mais frequentes sobre (FAQ)](virtual-networks-faq.md)
