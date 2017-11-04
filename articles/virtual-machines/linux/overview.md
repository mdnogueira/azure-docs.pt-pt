---
title: "Descrição geral das VMs com Linux no Azure | Microsoft Docs"
description: "Descreve os serviços de computação do Azure, armazenamento e redes com máquinas virtuais do Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: ae4b8f423489bf417f1086368db9b1043cd7f396
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-and-linux"></a>Azure e Linux
Microsoft Azure é uma crescente coleção de público integrada, incluindo bases de dados de análise, as máquinas virtuais, móveis, funcionamento em rede, armazenamento, de serviços em nuvem e web&mdash;ideal para alojar as suas soluções.  O Microsoft Azure fornece uma plataforma informática dimensionável que lhe permite pagar apenas aquilo que utiliza, quando quer utilizar, sem que precise de investir em hardware no local.  O Azure está sempre pronto quando precisa de aumentar as suas soluções vertical e horizontalmente até atingir a dimensão de que necessita para satisfazer as necessidades dos seus clientes.

Se estiver familiarizado com as várias funcionalidades do Amazon AWS, pode examinar os Azure vs AWS [documento de definição do mapeamento](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Regiões
Recursos do Microsoft Azure estão distribuídos por várias regiões geográficas em todo o mundo.  "Região" representa múltiplos centros de dados numa única área geográfica.  Temos 34 regiões geralmente disponíveis em todo o mundo com um regiões 4 adicionais anunciada. Porque é são continuar expandir o nosso cobertura de global - vamos manter que uma lista atualizada de existente e recentemente comunicada regiões.

* [Regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
Iremos anunciou a que uma máquina de virtual de instância única líder da indústria contrato de nível de serviço de 99,9% fornecido a que implementar a VM com o armazenamento premium para todos os discos.  Para a implementação para se qualificar para a nossa 99,95% padrão contrato de nível de serviço VM, é ainda necessário implementar duas ou mais VMs com a sua carga de trabalho dentro de um conjunto de disponibilidade. Isto irá garantir que as suas VMs são distribuídas por vários domínios de falhas nos nossos centros de dados, bem como implementadas em anfitriões com janelas de manutenção diferente. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="managed-disks"></a>Managed Disks

Gerido discos identificadores de armazenamento do Azure conta criação e gestão em segundo plano para si e assegura que não têm de se preocupar com os limites de escalabilidade da conta de armazenamento. Basta especificar o tamanho do disco e a camada de desempenho (Standard ou Premium) e Azure cria e gere o disco para si. Mesmo que adicione discos ou aumente e reduza verticalmente a VM, não terá de se preocupar se o armazenamento está a ser utilizado. Se estiver a criar novas VMs, [utilizar 2.0 de CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou o portal do Azure para criar as VMs com discos de dados e SO gerido. Se tiver VMs com discos não geridos, pode [converter as VMs para a cópia de discos geridos](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Também pode gerir as imagens personalizadas numa conta de armazenamento por região do Azure e utilizá-las para criar centenas de VMs na mesma subscrição. Para mais informações sobre os Managed Disks, consulte [Managed Disks Overview (Descrição geral dos Managed Disks)](../windows/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Máquinas virtuais do Azure e instâncias
Microsoft Azure suporta a execução de um número de distribuições em Linux populares fornecido e mantida por um número de parceiros.  Encontrará as distribuições como Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e mais no Azure Marketplace. Trabalhamos ativamente com várias comunidades de Linux para adicionar ainda mais tipos para o [Linux Distros aprovadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lista.

Se não estiver atualmente presente na galeria do seu distro Linux preferencial escolhidas, pode "traga a sua própria Linux" VM por [criar e carregar um VHD do Linux no Azure](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Máquinas virtuais do Azure permitem-lhe implementar uma vasta gama de soluções de informática de uma forma seja ágil. Pode implementar praticamente qualquer carga de trabalho e qualquer idioma em praticamente qualquer sistema operativo - Windows, Linux, ou um personalizado criado um de qualquer um dos nosso lista crescente de parceiros. Ainda não for apresentado aquilo procura?  Não se preocupe, também pode colocar as suas próprias imagens no local.

## <a name="vm-sizes"></a>Tamanhos de VM
Quando implementa uma VM no Azure, se pretender selecionar um tamanho VM dentro de um dos nosso série de tamanhos de que é adequado para a carga de trabalho. O tamanho afetará também a capacidade de energia, memória e armazenamento de processamento da máquina virtual. É-lhe cobrada com base na quantidade de tempo a VM está a executar e respetivos recursos alocados a consumir. Uma lista completa das [tamanhos de máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Seguem-se algumas orientações básicas para selecionar um tamanho VM a partir de um dos nosso série (A, D, DS, G e GS).
* A série VMs são nosso valor um preço entry-level VMs de cargas de trabalho leves e cenários de desenvolvimento/teste. Estão amplamente disponíveis em todas as regiões e pode ligar e utilizar todos os recursos padrão disponíveis para máquinas virtuais.
* A série (A8 - A11) são configurações intensivas de computação especial adequadas para aplicações de cluster computacional de elevado desempenho.
* As VMs da série D foram concebidos para executar aplicações que exigem um maior desempenho de poder de computação e disco temporário. As VMs da série D fornecem processadores mais rápidos, um rácio de memória para núcleo superior e uma unidade de estado sólida (SSD) para o disco temporário.
* Série Dv2, é a versão mais recente do nosso série D, funcionalidades uma CPU mais poderosa. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Se baseia na geração de mais recente 2.4 GHz Intel Xeon® E5-2673 v3 processador (Haskell) e com o Intel Turbo intensificação tecnologia 2.0, pode ir até 3,2 GHz. A série Dv2 tem as mesmas configurações de memória e disco da série D.
* As VMs da série G oferecem a maior capacidade de memória e são executadas em anfitriões com processadores da família Intel Xeon E5 V3.

Nota:-Série DS e VMs de série GS têm acesso para o Premium Storage - armazenamento de elevado desempenho, baixa latência para cargas de trabalho exigente em termos de e/s de segurança do nosso SSD. O Armazenamento Premium está disponível em determinadas regiões. Para obter mais detalhes, veja:

* [Armazenamento Premium: Armazenamento de elevado desempenho para cargas de trabalho de máquina virtual do Azure](../windows/premium-storage.md)

## <a name="automation"></a>Automatização
Para alcançar uma cultura de DevOps adequada, todos os infraestrutura tem de ser código.  Quando todos os a infraestrutura se encontra no código pode ser facilmente recriados (Phoenix servidores).  Azure funciona com a automatização de principais ferramentas como Ansible, Chef, SaltStack e Puppet.  O Azure também tem as suas próprias ferramentas de automatização:

* [Modelos do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [VMAccess do Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure está a implementar o suporte para [nuvem init](http://cloud-init.io/) entre a maioria das Distros de Linux que o suportam.  Atualmente Ubuntu VMs do Canonical implementadas com a nuvem-init ativada por predefinição.  Vermelho Hats RHEL, CentOS e Fedora suportam cloud init, no entanto as imagens do Azure mantido pela VM de RedHat não dispõe de nuvem-init instalado.  Para utilizar a cloud init numa família de VM de RedHat SO, tem de criar uma imagem personalizada com nuvem-init instalado.

* [Utilizar init nuvem em VMs do Linux do Azure](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Quotas
Cada subscrição do Azure tem limites de quota predefinidos que pode afetar a implementação de um grande número de VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região.  Limites de quota podem ser gerados rapidamente e facilmente ao apresentação de um pedido de suporte que solicitam um limite aumentam.  Para obter mais detalhes sobre os limites de quota:

* [Limites de serviço de subscrição do Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Parceiros
A Microsoft trabalha diretamente com os nossos parceiros de garantir que as imagens disponíveis são atualizadas e otimizadas para um tempo de execução do Azure.  Para obter mais informações sobre os nossos parceiros de Verifique as respetivas páginas de marketplace abaixo.

* Linux no Azure - [distribuições aprovadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE - [do Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* VM de Redhat - [do Azure Marketplace - VM de RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [do Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [do Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [do Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [do Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [do Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami biblioteca do Azure](https://azure.bitnami.com/)
* Mesosphere - [do Azure Marketplace - Mesosphere DC/SO no Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [do Azure Marketplace - serviço de contentor do Azure com o Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [do Azure Marketplace - CloudBees Jenkins plataforma](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Introdução ao Linux no Azure
Para começar a utilizar o Azure tem uma conta do Azure, a CLI do Azure instalados e um par de chaves públicas e privadas de SSH.

### <a name="sign-up-for-an-account"></a>Inscrever-se para obter uma conta
É o primeiro passo na nuvem do Azure a utilizar para se inscrever numa conta do Azure.  Vá para o [inscrição da conta do Azure](https://azure.microsoft.com/pricing/free-trial/) página para começar a utilizar.

### <a name="install-the-cli"></a>Instalar a CLI
Com a sua nova conta do Azure, pode começar a utilizar imediatamente no portal do Azure, que é um painel de administração baseada na web.  Para gerir a nuvem do Azure através da linha de comandos, instalar o `azure-cli`.  Instalar o [Azure CLI 2.0](/cli/azure/install-azure-cli) na estação de trabalho Mac ou Linux.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Tem agora uma conta do Azure, o portal web do Azure e a CLI do Azure.  O passo seguinte consiste em criar um par de chaves SSH é utilizado para SSH no Linux sem utilizar uma palavra-passe.  [Criar chaves SSH no Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para permitir inícios de sessão sem palavra-passe e uma melhor segurança.

### <a name="create-a-vm-using-the-cli"></a>Criar uma VM com a CLI
Criar uma VM com Linux utilizando a CLI é uma forma rápida de implementar uma VM, sem sair terminal que estão a funcionar.  Tudo o que pode especificar no web portal está disponível através de um sinalizador de linha de comandos ou comutador.  

* [Criar uma VM com Linux utilizando a CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Criar uma VM no portal
Criar uma VM com Linux no portal web do Azure é uma forma fácil ponto e clique em através de várias opções para obter a uma implementação.  Em vez de utilizar os sinalizadores de linha de comandos ou de comutadores, conseguir ver um esquema nice web das várias opções e definições.  Tudo disponíveis através da interface de linha de comandos também está disponível no portal.

* [Criar uma VM com Linux através do Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Utilização do SSH sem uma palavra-passe de início de sessão
A VM está agora em execução no Azure e está pronto para iniciar sessão.  A utilização de palavras-passe para iniciar sessão através de SSH é inseguras e demorada.  Utilizar chaves SSH é a forma mais segura e também a forma mais rápida para início de sessão.  Ao criar a VM com Linux através do portal ou a CLI, tem duas opções de autenticação.  Se escolher uma palavra-passe para SSH, o Azure configura a VM para permitir inícios de sessão através de palavras-passe.  Se optar por utilizar uma chave pública SSH, o Azure configura a VM para que apenas os inícios de sessão através de chaves SSH e desativa inícios de sessão de palavra-passe. Para proteger a VM com Linux, permitindo apenas a chaves SSH de inícios de sessão, utilize o SSH de opção de chave pública durante a criação de VM no portal ou a CLI.

## <a name="related-azure-components"></a>Componentes do Azure relacionados
## <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Adicionar um disco a uma VM com Linux utilizando a cli do azure](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Como anexar um disco de dados para uma VM com Linux no portal do Azure](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Redes
* [Descrição geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP do Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [A abertura de portas para uma VM com Linux no Azure](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar um nome de domínio completamente qualificado no portal do Azure](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Contentores
* [Máquinas virtuais e contentores no Azure](containers.md)
* [Introdução do serviço de contentor do Azure](../../container-service/container-service-intro.md)
* [Implementar um cluster do Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Passos seguintes
Tem agora uma descrição geral do Linux no Azure.  O passo seguinte consiste em explore e criar algumas VMs!

* [Explorar a nossa lista crescente de Scripts de exemplo para tarefas comuns através de AzureCLI](cli-samples.md)
