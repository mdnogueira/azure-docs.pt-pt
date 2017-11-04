---
title: Teste de SAP NetWeaver em VMs do Microsoft Azure SUSE Linux | Microsoft Docs
description: Testar o SAP NetWeaver em VMs do Linux do Microsoft Azure SUSE
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: hermannd
ms.openlocfilehash: f7dd532e96540fa297cac8fa3736f9f4a6ccd82f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Executar o SAP NetWeaver em VMs SUSE Linux do Microsoft Azure
Este artigo descreve vários aspetos a considerar quando estiver a executar o SAP NetWeaver em máquinas virtuais do Microsoft Azure SUSE Linux (VMs). A partir de 19 de Maio de 2016 SAP NetWeaver é oficialmente suportado no SUSE Linux VMs no Azure. Todos os detalhes sobre as versões de Linux, as versões do SAP kernel e outros pré-requisitos podem ser encontrados na 1928533 de nota SAP "aplicações SAP no Azure: suportada produtos e os tipos VM do Azure".
Documentação adicional sobre SAP em VMs do Linux pode ser encontrada aqui: [utilizando SAP em máquinas virtuais do Linux (VMs)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As seguintes informações devem ajudar a evitar algumas pitfalls potenciais.

## <a name="suse-images-on-azure-for-running-sap"></a>Imagens do SUSE no Azure para executar o SAP
Para executar o SAP NetWeaver no Azure, utilize o SUSE Linux Enterprise Server SLES 12 (SPx) ou SLES para SAP - Consulte também a nota SAP 1928533. É uma imagem SUSE especial no Azure Marketplace ("SLES 11 SP3 para SAP CAL"), mas a imagem não foi concebida para utilização geral. Não utilize esta imagem porque está reservado para o [biblioteca de aplicação de nuvem do SAP](https://cal.sap.com/) solução.  

Tem de utilizar a estrutura de implementação Azure Resource Manager para todas as instalações no Azure. Para procurar as imagens do SUSE SLES e versões utilizando o Azure PowerShell ou a interface de linha de comandos do Azure (CLI), utilize os comandos mostrados abaixo. Em seguida, pode utilizar o resultado, por exemplo, para definir a imagem do SO num modelo JSON para implementar uma nova VM do SUSE Linux.
Estes comandos do PowerShell estão válido para o Azure PowerShell versão 1.0.1 e versões posteriores.

Embora seja possível utilizar as imagens SLES padrão para instalações de SAP, é recomendado para utilizar o novo SLES para imagens SAP. Estas imagens estão agora disponíveis na galeria da imagem do Azure. Podem encontrar mais informações sobre estas imagens no correspondente [página do Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) ou [página web de SUSE FAQ sobre SLES para SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Procure publicadores existentes, incluindo SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Procure ofertas existentes do SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Procure ofertas SUSE SLES:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Procure uma versão específica de um SKU SLES:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalar WALinuxAgent numa SUSE VM
O agente chamado WALinuxAgent faz parte das imagens SLES no Azure Marketplace. Para obter informações sobre a instalação-lo manualmente (por exemplo, quando carregar um disco de rígido virtual SO SLES (VHD) no local), consulte:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "monitorização avançada"
SAP "avançada de monitorização" é um pré-requisito obrigatório para executar o SAP no Azure. Consulte os detalhes no SAP tenha em atenção 2191498 "SAP no Linux com monitorização do Azure: avançada".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Anexar os discos de dados do Azure para uma VM com Linux do Azure
Nunca discos de dados do Azure de montagem para uma VM com Linux do Azure utilizando o ID de dispositivo. Em alternativa, utilize o identificador exclusivo universalmente (UUID). Seja cuidadoso ao utilizar ferramentas gráficas para discos de dados do Azure de montagem, por exemplo. Verifique as entradas no /etc/fstab.

O problema com o ID de dispositivo é que poderá alterar e, em seguida, a VM do Azure pode bloquear o processo de arranque. Para mitigar o problema, pode adicionar o parâmetro nofail no /etc/fstab. Mas, tenha cuidado com nofail porque poderão utilizar o ponto de montagem como antes de aplicações e poderão escrever para o sistema de ficheiros de raiz no caso de um disco de dados do Azure externos não foi montado durante o arranque.

A única exceção a montagem através do UUID é anexar um disco de SO para a resolução de problemas, conforme descrito na secção que se seguem.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Resolução de problemas de uma VM de SUSE que não se encontra acessível já
Podem existir situações em que uma VM SUSE no Azure bloqueia o processo de arranque (por exemplo, com um erro relacionado com para montar discos). Pode verificar este problema ao utilizar a funcionalidade de diagnóstico de arranque para v2 de Virtual Machines do Azure no portal do Azure. Para obter mais informações, consulte [diagnóstico de arranque](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Uma forma de resolver o problema é a anexar o disco de SO da VM danificada para outra SUSE VM no Azure. Em seguida, efetue alterações adequadas como editar /etc/fstab ou remover regras de udev de rede, conforme descrito na secção seguinte.

Não há um aspeto importante a ter em consideração. Implementação de várias VMs SUSE partir da mesma imagem do Azure Marketplace (por exemplo, SLES 11 SP4) faz com que o disco de SO sempre possível montá-la pelo mesmo UUID. Por conseguinte, utilizando o UUID para anexar um disco de SO de VM diferente que tenha sido implementada, utilizando os mesmos resultados de imagem do Azure Marketplace em UUIDs não idênticos dois. Dois causa UUIDs não idênticos a VM utilizado para resolução de problemas, arrancar a partir do disco de SO ligado e danificado em vez do disco de SO original.

Existem duas formas para evitar problemas:

* Utilize uma imagem do Azure Marketplace diferente para a VM de resolução de problemas (por exemplo, SLES 11 SPx em vez de SLES 12).
* Não anexar o disco de SO danificado de outra VM utilizando o UUID – utilizar algo pessoa.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Carregamento de uma VM SUSE no local para o Azure
Para obter uma descrição dos passos para carregar uma VM SUSE no local para o Azure, consulte [preparar uma máquina virtual SLES ou openSUSE para o Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se pretender carregar uma VM sem o passo de desaprovisionamento no fim (por exemplo, para manter uma instalação existente do SAP, bem como o nome de anfitrião), verifique os seguintes itens:

* Certifique-se de que o disco do SO está montado utilizando UUID e não o ID de dispositivo. Alterar para UUID apenas na /etc/fstab não é suficiente para o disco de SO. Além disso, não se esqueça de adaptar o carregador de arranque através de YaST ou editando /boot/grub/menu.lst.
* Se utilizar o formato VHDX para o disco de SO SUSE e convertê-lo para o VHD para carregar para o Azure, é provável que o dispositivo de rede é alterada de eth0 para eth1. Para evitar problemas quando está a arrancar no Azure mais tarde, altere para eth0, conforme descrito em [corrigir eth0 no clonado SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Para além das que está descrito no artigo, recomendamos que remova este ficheiro:

   /lib/udev/Rules.d/75-persistent-NET-Generator.Rules

Também pode instalar o agente Linux do Azure (waagent) para o ajudar a evitar potenciais problemas, desde que não existem vários NICs.

## <a name="deploying-a-suse-vm-on-azure"></a>Implementar uma VM SUSE no Azure
Deve criar novas VMs SUSE utilizando ficheiros de modelo JSON no novo modelo Azure Resource Manager. Depois de criado o ficheiro de modelo JSON, pode implementar a VM utilizando o seguinte comando da CLI como alternativa do PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para obter mais informações sobre os ficheiros de modelo JSON, consulte [modelos Authoring Azure Resource Manager](../../../resource-group-authoring-templates.md) e [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).

Para obter mais informações sobre a CLI e o Azure Resource Manager, consulte [utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Chave de licença e hardware do SAP
Para a certificação oficial do SAP Azure, foi introduzido um mecanismo de novo para calcular a chave de hardware do SAP que é utilizada para a licença do SAP. O kernel do SAP que tiveram de ser adaptada para se utilizar o novo algoritmo. Versões anteriores de kernel SAP para Linux não incluiu esta alteração de código. Por conseguinte, em determinadas situações (por exemplo, a VM do Azure redimensionamento), a chave de hardware do SAP alterada e a licença do SAP foi já não ser válido. Uma solução é fornecida com kernels mais recentes do SAP Linux.  Os patches de kernel SAP detalhados estão documentados na nota SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Pacote de sapconf SUSE / adm otimizados
SUSE fornece um pacote designado "sapconf", que gere um conjunto de definições específicas do SAP. Para mais informações sobre o que faz este pacote e como instalar e utilizá-lo, consulte: [utilizar sapconf para preparar um SUSE Linux Enterprise Server para executar os sistemas SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [Novidades sapconf ou como preparar uma SUSE Linux Enterprise Servidor para executar os sistemas SAP? ](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Entretanto, não há uma nova ferramenta, que substitui 'sapconf - adm otimizados'. Um pode encontrar mais informações sobre esta ferramenta seguintes duas ligações:

- Pode encontrar documentação SLES sobre 'adm otimizados' perfil sap-hana [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- É possível encontrar a otimização de sistemas para cargas de trabalho de SAP com 'otimizados-adm' - [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) no capítulo 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>Nas instalações de SAP distribuídas de partilha NFS
Se tiver uma instalação distribuída – por exemplo, onde pretende instalar a base de dados e os servidores de aplicações SAP em separado VMs – podem partilhar o diretório de /sapmnt através de sistema de ficheiros de rede (NFS). Se tiver problemas com os passos de instalação depois de criar a partilha NFS para /sapmnt, verifique se "no_root_squash" estiver definido para a partilha.

## <a name="logical-volumes"></a>Volumes lógicas
No passado, se um for necessário um grande volume lógico entre múltiplos discos de dados do Azure (por exemplo, para a base de dados SAP), recomenda-foi que utilize a ferramenta de gestão de Raid MDADM, uma vez que Linux lógica Volume Manager (LVM) não foi totalmente validada ainda no Azure. Para saber como configurar o RAID Linux no Azure utilizando mdadm, consulte [configurar software RAID no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Entretanto, a partir do início de Maio de 2016, Gestor de volumes lógica Linux é totalmente suportado no Azure e pode ser utilizado como uma alternativa à MDADM. Para obter mais informações sobre LVM no Azure, leia o artigo:  
[Configurar uma VM com Linux no Azure LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Repositório SUSE do Azure
Se tiver um problema com o acesso ao repositório Azure SUSE padrão, pode utilizar um comando para repô-lo. Esses problemas podem acontecer se criar uma imagem de SO privada uma região do Azure e, em seguida, copie a imagem noutra região do Azure para implementar novas VMs que se baseiam nesta imagem de SO privada. Execute o seguinte comando dentro da VM:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Ambiente de trabalho gnome
Se pretender utilizar o ambiente de trabalho Gnome para instalar um sistema completo para a demonstração SAP dentro de uma única VM – incluindo um GUI SAP, browser e consola de gestão do SAP-- utilizam esta sugestão para instalá-lo nas imagens do Azure SLES:

   Para o SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para o SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Suporte SAP, Oracle no Linux na nuvem
Há uma restrição de suporte do Oracle no Linux em ambientes virtualizados. Embora esta restrição de suporte não é um tópico específicos do Azure, é importante compreender. SAP não suporta Oracle SUSE ou Red Hat numa nuvem pública, como o Azure. Para discutir neste tópico, contacte diretamente Oracle.

