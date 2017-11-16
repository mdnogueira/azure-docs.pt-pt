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
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 2f7a65d32f662d7e265e58c5fe7d9dea81a4e63c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar o débito de rede para máquinas virtuais do Azure

Máquinas virtuais do Azure (VM) tem predefinições de rede que podem ser mais otimizadas para débito de rede. Este artigo descreve como otimizar o débito de rede para o Microsoft Azure Windows e VMs do Linux, incluindo as distribuições principais como Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM do Windows

Se a VM do Windows é suportada com [acelerados redes](virtual-network-create-vm-accelerated-networking.md), ativar essa funcionalidade seria a configuração ideal para débito. Para todas as outras VMs do Windows, utilizar o dimensionamento do lado da receção (RSS) pode contactar um maior débito garantido que uma VM sem RSS. O RSS pode estar desativado por predefinição numa VM do Windows. Conclua os passos seguintes para determinar se o RSS está ativado e ativá-la se está desativada.

1. Introduza o `Get-NetAdapterRss` comando do PowerShell para ver se o RSS está ativado para um adaptador de rede. No seguinte exemplo de saída devolvido do `Get-NetAdapterRss`, o RSS não está ativado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Introduza o seguinte comando para ativar o RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não possui uma saída. O comando Alterar definições da NIC, a causar a perda de conectividade temporário para cerca de um minuto. É apresentada uma caixa de diálogo de Reconnecting durante a perda de conectividade. Normalmente, conectividade ser restaurada depois da tentativa de terceira.
3. Confirme que o RSS está ativado na VM introduzindo o `Get-NetAdapterRss` novamente o comando. Se tiver êxito, é devolvido o seguinte exemplo de saída:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>VM com Linux

O RSS está sempre ativado por predefinição no VM Linux do Azure. Kernels Linux lançadas desde Outubro de 2017 incluem novas opções de otimizações de rede que permitem uma VM com Linux alcançar o débito de rede superior.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implementações

O kernel do Ubuntu Azure fornece o melhor desempenho de rede no Azure e foi kernel predefinido desde o dia 21 de Setembro de 2017. Para obter esta kernel, primeiro de instalar mais recente versão suportada do 16.04-LTS, conforme descrito abaixo:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Depois de concluída a criação, introduza os seguintes comandos para obter as atualizações mais recentes. Estes passos também funcionam para as VMs em execução kernel Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

O conjunto de comandos opcionais seguintes pode ser útil para implementações de Ubuntu existentes que já tenham o kernel do Azure, mas que ocorreu uma falha ao obter atualizações com erros.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Atualização de kernel Ubuntu Azure para VMs existentes

Desempenho de débito significativas pode ser conseguido através da atualização para o kernel do Linux do Azure. Para verificar se tem este kernel, verifica a sua versão de kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Se a VM não tem o kernel do Azure, o número de versão normalmente, irá começar com "4.4". Nesses casos, execute os seguintes comandos como raiz.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter as otimizações mais recentes, é melhor criar uma VM com a versão mais recente suportada ao especificar os parâmetros seguintes:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
VMs novas e existentes podem beneficiar de instalar os serviços de integração de Linux (LIS) mais recente.
A otimização de débito está a ser LIS, começando 4.2.2-2, embora as versões posteriores contém ainda outras melhorias. Introduza os comandos seguintes para instalar os LIS mais recentes:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter as otimizações, é melhor criar uma VM com a versão mais recente suportada ao especificar os parâmetros seguintes:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
VMs novas e existentes podem beneficiar de instalar os serviços de integração de Linux (LIS) mais recente.
A otimização de débito está a ser LIS, começando 4.2. Introduza os comandos seguintes para transferir e instalar o LIS:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre 4.2 de versão de serviços de integração Linux para Hyper-V, visualizando o [página de transferência](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passos seguintes
* Agora que a VM está otimizada, consulte o resultado com [testar VM do Azure de largura de banda/débito](virtual-network-bandwidth-testing.md) para o seu cenário.
* Saiba mais com [Azure Virtual Network perguntas mais frequentes sobre (FAQ)](virtual-networks-faq.md)
