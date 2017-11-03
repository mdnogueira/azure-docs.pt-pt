---
title: "Início rápido: Instalação Manual de instância única SAP HANA em Azure Virtual Machines | Microsoft Docs"
description: "Guia de introdução para instalação manual de instância única SAP HANA em Azure Virtual Machines"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 321a86d6ce355273820617e6de9df2b0816c73fa
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Início rápido: Instalação Manual de instância única SAP HANA em VMs do Azure
## <a name="introduction"></a>Introdução
Este guia ajuda-o a configurar uma instância única de SAP HANA em máquinas de virtuais (VMs) do Azure, quando instala o SAP NetWeaver 7.5 e SAP HANA 1.0 SP12 manualmente. O objetivo deste guia é na implementação de SAP HANA no Azure. Não substitui a documentação do SAP. 

>[!Note]
>Este guia descreve as implementações de SAP HANA em VMs do Azure. Para obter informações sobre a implementação SAP HANA em instâncias de grande HANA, consulte [SAP através do Azure máquinas virtuais (VMs)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia pressupõe que está familiarizado com essa infraestrutura como um Noções básicas de serviço (IaaS) como:
 * Como implementar máquinas virtuais e as redes virtuais através do portal do Azure ou do PowerShell.
 * A interface do Azure plataforma da linha de comandos (CLI), incluindo a opção para utilizar modelos de JavaScript Object Notation (JSON).

Este guia também parte do princípio de que está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Instalar e operar o SAP HANA e instâncias de aplicações SAP no Azure.
* Os seguintes conceitos e procedimentos:
   * Planear a implementação SAP no Azure, incluindo o planeamento de rede Virtual do Azure e a utilização do armazenamento do Azure. Consulte [SAP NetWeaver em Azure Virtual Machines (VMs) - guia de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Princípios de implementação e formas de implementar as VMs do Azure. Consulte [implementação de máquinas virtuais do Azure para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Elevada disponibilidade para SAP NetWeaver ASCS (ABAP SAP serviços Central), SCS (SAP Central serviços) e ERS (avaliada Settlement de receção) no Azure. Consulte [elevada disponibilidade para SAP NetWeaver em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Detalhes sobre como melhorar a eficiência na tirar partido de uma instalação de várias SID do ASCS/SCS no Azure. Consulte [criar uma configuração de várias SID do SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Princípios de execução SAP NetWeaver com base em Linux orientada por VMs no Azure. Consulte [executar SAP NetWeaver em VMs do Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Este guia fornece definições específicas para Linux em VMs do Azure e os detalhes sobre como anexar corretamente discos de armazenamento do Azure para VMs com Linux.

Neste momento, as VMs do Azure são certificadas pela SAP para apenas configurações de dimensionamento de SAP HANA. Configurações de escalamento horizontal com cargas de trabalho de SAP HANA ainda não são suportadas. Para SAP HANA elevada disponibilidade em casos das configurações de dimensionamento, consulte [elevada disponibilidade de SAP HANA em máquinas de virtuais (VMs) do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Se necessita de obter uma instância de SAP HANA S/4HANA ou o sistema BW/4HANA implementado no tempo muito rápido, deve considerar a utilização de [biblioteca de aplicação de nuvem do SAP](http://cal.sap.com). Pode encontrar documentação sobre a implementação, por exemplo, um sistema de S/4HANA através de CAL SAP no Azure no [neste guia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tudo o que precisa de ter é uma subscrição do Azure e um utilizador SAP que pode ser registado com a biblioteca de dispositivo do SAP na nuvem.

## <a name="additional-resources"></a>Recursos adicionais
### <a name="sap-hana-backup"></a>Cópia de segurança de SAP HANA
Para obter informações sobre a cópia de segurança das bases de dados SAP HANA em VMs do Azure, consulte:
* [Cópia de segurança manual para SAP HANA em Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Cópia de segurança do SAP HANA do Azure no nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Cópia de segurança de SAP HANA com base nos instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Biblioteca de aplicação de nuvem do SAP
Para obter informações sobre como utilizar a biblioteca de aplicação de nuvem do SAP para implementar o S/4HANA ou BW/4HANA, consulte [implementar SAP S/4HANA ou BW/4HANA no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA-sistemas operativos suportados pelo
Para obter informações sobre sistemas operativos suportados para SAP HANA pelo, consulte [2235581 de n. º de nota de suporte de SAP - SAP HANA: sistemas operativos suportados pelo](https://launchpad.support.sap.com/#/notes/2235581/E). As VMs do Azure suportam apenas um subconjunto destes sistemas operativos. Os seguintes sistemas operativos são suportados para implementar o SAP HANA no Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Para documentação adicional do SAP sobre SAP HANA e sistemas de operativos diferentes do Linux, consulte:

* [Nota de suporte SAP #171356 - Software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
* [Nota de suporte #1944799 - SAP HANA diretrizes para a instalação do sistema operativo SLES de SAP](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Nota de suporte SAP #2205917 - SAP HANA DB de definições de SO para o SLES 12 para aplicações SAP recomendadas](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Nota de suporte do SAP #1984787 - SUSE Linux Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
* [Nota de suporte SAP #1391070 - soluções do UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP suporte Nota #2009879 - SAP HANA diretrizes para o sistema operativo do Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: definições de SO recomendado para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP monitorização no Azure
Para obter informações sobre SAP monitorização no Azure, consulte:

* [A nota 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Aborda SAP "avançada de monitorização" desta nota com VMs com Linux no Azure. 
* [A nota 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Este nota aborda informações sobre SAPOSCOL no Linux. 
* [A nota 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Este nota aborda as principais métricas a monitorização de SAP no Microsoft Azure.

### <a name="azure-vm-types"></a>Tipos VM do Azure
Tipos VM do Azure e cenários de carga de trabalho suportadas de SAP utilizado com o SAP HANA estão documentados na [SAP certificadas IaaS plataformas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Tipos VM do Azure que são certificados pela SAP para SAP NetWeaver ou camada da aplicação de S/4HANA estão documentados na [SAP nota 1928533 - aplicações SAP no Azure: os tipos de produtos suportados e VM do Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integração de SAP Linux-do Azure é suportada apenas no Azure Resource Manager e não o modelo de implementação clássica. 

## <a name="manual-installation-of-sap-hana"></a>Instalação manual de SAP HANA
Este guia descreve como instalar manualmente o SAP HANA em VMs do Azure de duas formas diferentes:

* Utilizando o Gestor de aprovisionamento de Software SAP (SWPM) como parte de uma instalação distribuída a NetWeaver no passo "instância de base de dados de instalação"
* Utilizando o SAP HANA da base de dados ferramenta do Gestor de ciclo de vida, HDBLCM e, em seguida, instalar NetWeaver

Pode também utilizar SWPM para instalar todos os componentes (SAP HANA, o servidor de aplicações SAP e a instância ASCS) numa única VM, conforme descrito neste [anúncio de blogue de SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Esta opção não está descrita neste manual de início rápido, mas os problemas que deve tomar em consideração são os mesmos.

Antes de iniciar uma instalação, recomendamos que leia o "Preparar Azure VMs para a instalação manual de SAP HANA" secção posterior deste guia. Se o fizer, pode ajudar a evitar vários prende básico que pode ocorrer ao utilizar apenas uma configuração de VM do Azure predefinido.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Passos de fundamentais para a instalação de SAP HANA quando utilizar SAP SWPM
Esta secção lista os passos de chaves para uma instalação de SAP HANA manual e de instância única, quando utiliza o SAP SWPM para efetuar uma instalação distribuída do SAP NetWeaver 7.5. Os passos individuais são explicados com mais detalhe nas capturas de ecrã mais tarde neste guia.

1. Crie uma Azure virtual network que inclui o teste duas VMs.
2. Implemente as duas VMs do Azure com os sistemas operativos (no nosso exemplo, SUSE Linux Enterprise Server (SLES) e SLES para SAP aplicações 12 SP1), de acordo com o modelo Azure Resource Manager.
3. Anexe dois Azure padrão ou discos de armazenamento premium (por exemplo, discos 75 GB ou 500 GB) para o servidor de aplicações VM.
4. Anexe discos de armazenamento premium para o servidor de base de dados HANA VM. Para obter detalhes, consulte a secção "Configuração de disco" mais tarde neste guia.
5. Dependendo dos requisitos de tamanho ou de débito, anexar vários discos e, em seguida, criar volumes repartidos utilizando a gestão de volumes lógico ou uma ferramenta de administração de vários dispositivos (MDADM) ao nível do SO dentro da VM.
6. Crie sistemas de ficheiros XFS na lógicas volumes ou discos ligados.
7. Monte os sistemas de ficheiros XFS novo ao nível do SO. Utilize um sistema de ficheiros para todos os softwares SAP. Utilize o sistema de ficheiros para o diretório de /sapmnt e cópias de segurança, por exemplo. No servidor de base de dados do SAP HANA, monte os sistemas de ficheiros XFS nos discos de armazenamento premium como /hana e /usr/sap. Este processo é necessário para impedir que o sistema de ficheiros de raiz, o qual não grande em VMs do Linux do Azure, ser preenchida.
8. Introduza os endereços IP locais do teste VMs no ficheiro /etc/hosts.
9. Introduza o **nofail** parâmetro no ficheiro de fstab etc /.
10. Defina os parâmetros de kernel do Linux, de acordo com a versão de SO Linux que está a utilizar. Para obter mais informações, consulte as notas SAP adequadas que discutir HANA e a secção "Parâmetros de Kernel" neste guia.
11. Adicione espaço de comutação.
12. Opcionalmente, instale um ambiente de trabalho gráfico no teste de VMs. Caso contrário, utilize uma instalação de SAPinst remota.
13. Transferir o software SAP no Marketplace de serviço do SAP.
14. Instale a instância do SAP ASCS no servidor de aplicações VM.
15. Partilhe o diretório de /sapmnt entre o VMs de teste ao utilizar o NFS. O servidor de aplicações VM é o servidor NFS.
16. Instale a instância de base de dados, incluindo HANA, utilizando SWPM no servidor de base de dados de VM.
17. Instale o servidor de principal da aplicação (PAS) no servidor de aplicações VM.
18. Inicie a consola de gestão de SAP (SAP MC). Ligar com SAP GUI ou HANA Studio, por exemplo.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Passos de fundamentais para a instalação de SAP HANA quando utilizar HDBLCM
Esta secção lista os passos de chaves para uma instalação de SAP HANA manual e de instância única, quando utiliza o SAP HDBLCM para efetuar uma instalação distribuída do SAP NetWeaver 7.5. Os passos individuais são explicados com mais detalhe nas capturas de ecrã ao longo deste guia.

1. Crie uma Azure virtual network que inclui o teste duas VMs.
2. Implemente duas VMs do Azure com os sistemas operativos (no nosso exemplo, SLES e SLES para SAP aplicações 12 SP1), de acordo com o modelo Azure Resource Manager.
3. Anexe dois Azure padrão ou discos de armazenamento premium (por exemplo, discos 75 GB ou 500 GB) para a VM do servidor de aplicações.
4. Anexe discos de armazenamento premium para o servidor de base de dados HANA VM. Para obter detalhes, consulte a secção "Configuração de disco" mais tarde neste guia.
5. Dependendo dos requisitos de tamanho ou de débito, anexar vários discos e criar volumes repartidos utilizando a gestão de volumes lógico ou uma ferramenta de administração de vários dispositivos (MDADM) ao nível do SO dentro da VM.
6. Crie sistemas de ficheiros XFS na lógicas volumes ou discos ligados.
7. Monte os sistemas de ficheiros XFS novo ao nível do SO. Utilize um sistema de ficheiros para todos os softwares SAP e utilize o outro para o diretório de /sapmnt e cópias de segurança, por exemplo. No servidor de base de dados do SAP HANA, monte os sistemas de ficheiros XFS nos discos de armazenamento premium como /hana e /usr/sap. Este processo é necessário para o ajudar a impedir que o sistema de ficheiros de raiz, o qual não grande em VMs do Linux do Azure, ser preenchida.
8. Introduza os endereços IP locais do teste VMs no ficheiro /etc/hosts.
9. Introduza o **nofail** parâmetro no ficheiro de fstab etc /.
10. Definir os parâmetros de kernel, de acordo com a versão de SO Linux que está a utilizar. Para obter mais informações, consulte as notas SAP adequadas que discutir HANA e a secção "Parâmetros de Kernel" neste guia.
11. Adicione espaço de comutação.
12. Opcionalmente, instale um ambiente de trabalho gráfico no teste de VMs. Caso contrário, utilize uma instalação de SAPinst remota.
13. Transferir o software SAP no Marketplace de serviço do SAP.
14. Crie um grupo, sapsys, com grupo ID 1001, no servidor de base de dados HANA VM.
15. Instale SAP HANA no servidor de base de dados VM utilizando o Gestor de ciclo de vida de base de dados de HANA (HDBLCM).
16. Instale a instância do SAP ASCS no servidor de aplicações VM.
17. Partilhe o diretório de /sapmnt entre o VMs de teste ao utilizar o NFS. O servidor de aplicações VM é o servidor NFS.
18. Instale a instância de base de dados, incluindo HANA, utilizando SWPM no servidor de base de dados HANA VM.
19. Instale o servidor de principal da aplicação (PAS) no servidor de aplicações VM.
20. Inicie SAP MC. Ligar através da GUI do SAP ou HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparar as VMs do Azure para uma instalação manual de SAP HANA
Esta secção abrange os seguintes tópicos:

* Atualizações do SO
* Configuração do disco
* Parâmetros de kernel
* sistemas de ficheiros
* O ficheiro etc/hosts
* O ficheiro etc/fstab

### <a name="os-updates"></a>Atualizações do SO
Procurar com SO Linux atualizações e correções antes de instalar o software adicional. Ao instalar uma correção, poderá evitar uma chamada para o suporte técnico de suporte.

Certifique-se de que está a utilizar:
* SUSE Linux Enterprise Server para aplicações SAP.
* Red Hat Enterprise Linux para aplicações SAP ou Red Hat Enterprise Linux para SAP HANA. 

Se ainda não o fez, registe a implementação do SO com a sua subscrição do Linux do fornecedor do Linux. Tenha em atenção que o SUSE tem imagens de SO para aplicações SAP, já que incluem os serviços e que estão registados automaticamente.

Eis um exemplo de procurar patches disponíveis para o SUSE Linux utilizando o **zypper** comando:

 `sudo zypper list-patches`

Dependendo do tipo de problema, patches estão classificados por categoria e gravidade. Valores normalmente utilizados para a categoria são: **segurança**, **recomendado**, **opcional**, **funcionalidade**, **documento**, ou **yast**.
São frequentemente utilizados valores de gravidade: **críticos**, **importante**, **Moderada**, **baixa**, ou **não especificados**.

O **zypper** comando procura apenas as atualizações que necessitam da sua pacotes instalados. Por exemplo, pode utilizar este comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Pode adicionar o parâmetro `--dry-run` para testar a atualização sem atualizar, na verdade, o sistema.


### <a name="disk-setup"></a>Configuração do disco
O sistema de ficheiros de raiz numa VM com Linux no Azure tem uma limitação de tamanho. Por conseguinte, é necessário anexar espaço em disco adicional à VM do Azure para executar o SAP. Para o servidor de aplicações SAP VMs do Azure, a utilização de discos de armazenamento padrão do Azure poderão ser suficiente. No entanto, para as VMs do Azure do SAP HANA DBMS, a utilização de discos de Premium Storage do Azure para implementações de produção e não a produção é obrigatória.

Com base no [requisitos de armazenamento do SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a seguinte configuração de armazenamento do Azure Premium é sugerida: 

| SKU DE VM | RAM |  hana/dados e de hana/registo <br /> repartidos com LVM ou MDADM | hana/partilhado | /root volume | usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

A configuração do disco sugeridos, o volume de dados HANA e o volume de registo são colocados no mesmo conjunto de discos de armazenamento do Azure premium que estão repartidas com LVM ou MDADM. Não é necessário definir qualquer nível de redundância RAID, porque o Premium Storage do Azure mantém três imagens dos discos para redundância. Para se certificar de que configurou o armazenamento suficiente, consulte o [requisitos de armazenamento do SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e [guia de atualização e instalação do servidor do SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Considere também os volumes de débito do disco rígido virtual (VHD) diferente dos discos de armazenamento premium do Azure diferente conforme documentado no [elevado desempenho Premium Storage e discos geridos para VMs](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Pode adicionar mais discos de armazenamento premium para as VMs de DBMS HANA para armazenar cópias de segurança da base de dados ou a transação de registo.

Para obter mais informações sobre as duas ferramentas principais utilizado para configurar striping, consulte os artigos seguintes:

* [Configurar software RAID no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurar LVM numa VM com Linux no Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre a ligação de discos para VMs do Azure com o Linux como um SO convidado, consulte [adicionar um disco a uma VM com Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Armazenamento Premium do Azure permite-lhe definir o disco modos a colocação em cache. Para o conjunto repartido que contém /hana/data e /hana/log, colocação em cache do disco deve ser desativada. Para outros volumes (discos), o modo de colocação em cache deve ser definido como **ReadOnly**.

Para obter mais informações, consulte [Premium Storage: armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual do Azure](../../windows/premium-storage.md).

Para encontrar modelos JSON de exemplo para a criação de VMs, aceda a [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
O modelo de vm-simples-sles é um modelo básico. Inclui uma secção de armazenamento, com um disco de dados de 100 GB adicionais. Este modelo pode ser utilizado como base. Pode adaptar o modelo para a configuração específica.

>[!Note]
>É importante anexar o disco de armazenamento do Azure, utilizando um UUID conforme documentado no [a executar o SAP NetWeaver em VMs do Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

No ambiente de teste, os discos de dois armazenamento padrão do Azure foram ligados para o servidor de aplicações SAP VM, conforme mostrado na captura de ecrã seguinte. Um disco armazenados todos os softwares SAP (incluindo NetWeaver 7.5, SAP GUI e SAP HANA) para a instalação. O segundo disco certificar-se de que o espaço livre suficiente estaria disponível para requisitos adicionais (por exemplo, dados de cópia de segurança e de teste) e para o diretório de /sapmnt (ou seja, perfis SAP) a ser partilhado entre todas as VMs que pertencem a horizontal SAP mesmo.

![Janela discos SAP HANA de aplicação de servidor que apresenta os tamanhos de discos de dados de dois e](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parâmetros de kernel
SAP HANA requer definições específicas do Linux kernel, que não fazem parte das imagens da galeria do Azure standard e tem de ser definidas manualmente. Consoante utilize ou não SUSE ou Red Hat, os parâmetros poderão ser diferentes. As notas de SAP listados anteriormente fornecer informações sobre os parâmetros. Nas capturas de ecrã apresentadas, SUSE Linux 12 SP1 foi utilizado. 

SLES para SAP aplicações 12 GA e SLES para SAP aplicações 12 SP1 têm uma nova ferramenta **adm otimizados**, que substitui o antigo **sapconf** ferramenta. Um perfil especial de SAP HANA está disponível para **adm otimizados**. Para otimizar o sistema para SAP HANA, introduza o seguinte como utilizador raiz:

   `tuned-adm profile sap-hana`

Para obter mais informações sobre **adm otimizados**, consulte o [documentação SUSE sobre adm otimizados](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na captura de ecrã seguinte, pode ver como **adm otimizados** alterado o `transparent_hugepage` e `numa_balancing` valores, de acordo com as definições necessárias de SAP HANA.

![A ferramenta de adm otimizados altera valores, de acordo com as definições de SAP HANA necessárias](./media/hana-get-started/image005.jpg)

Para tornar as definições de kernel de SAP HANA permanentes, utilize **grub2** no SLES 12. Para obter mais informações sobre **grub2**, vá para o [estrutura de ficheiros de configuração](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) secção de documentação do SUSE.

Captura de ecrã seguinte mostra como as definições de kernel foram alteradas no ficheiro de configuração e, em seguida, compiladas utilizando **grub2 mkconfig**:

![Definições de kernel foi alterado no ficheiro de configuração e compilado utilizando grub2 mkconfig](./media/hana-get-started/image006.jpg)

Outra opção consiste em alterar as definições utilizando YaST e **carregador de arranque** > **parâmetros de Kernel** definições:

![O separador de definições de parâmetros de Kernel no YaST carregador de arranque](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>sistemas de ficheiros
A seguinte captura de ecrã mostra dois sistemas de ficheiros que foram criados no servidor de aplicações SAP VM em cima de dois discos de armazenamento padrão do Azure ligado. Ambos os sistemas de ficheiros são do tipo XFS e estão montados /sapdata e /sapsoftware.

Não é obrigatória para a estrutura de sistemas de ficheiros, desta forma. Tem outras opções para structuring o espaço em disco. As considerações mais importantes é para impedir que o sistema de ficheiros de raiz em execução sem espaço livre.

![Dois sistemas de ficheiros criados no servidor de aplicações SAP VM](./media/hana-get-started/image008.jpg)

Sobre a VM de BD do SAP HANA, durante uma instalação de base de dados, quando utiliza SAPinst (SWPM) e o **típico** opção de instalação, tudo está instalado em /hana e /usr/sap. A localização predefinida para a cópia de segurança do registo de SAP HANA está sob /usr/sap. Novamente, porque é importante impedir que o sistema de ficheiros de raiz ficar sem espaço de armazenamento, certifique-se de que não há espaço livre suficiente em /hana e /usr/sap antes de instalar o SAP HANA utilizando SWPM.

Para obter uma descrição do esquema de sistema de ficheiros padrão de SAP HANA do, consulte o [guia de atualização e instalação do servidor do SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Sistemas de ficheiros adicionais criados no servidor de aplicações SAP VM](./media/hana-get-started/image009.jpg)

Quando instalar o SAP NetWeaver num SLES/SLES padrão para a imagem de galeria do Azure de 12 de aplicações SAP, é apresentada uma mensagem que indica que não há nenhum espaço de comutação, conforme mostrado na captura de ecrã seguinte. Para dispensar esta mensagem, pode adicionar manualmente um ficheiro de comutação utilizando **dd**, **mkswap**, e **swapon**. Para saber como, procure "Adicionar um ficheiro de comutação manualmente" o [utilizando o Particionador YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) secção de documentação do SUSE.

Outra opção consiste em configurar o espaço de comutação utilizando o agente de VM com Linux. Para obter mais informações, consulte o [guia de utilizador de agente do Azure Linux](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![Mensagem de pop-up indicar que existe espaço suficiente de comutação](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>O ficheiro etc/hosts
Antes de começar a instalar o SAP, certifique-se de que incluem os nomes de anfitrião e endereços IP das SAP VMs no ficheiro /etc/hosts. Implementar todas as VMs de SAP dentro de uma rede virtual do Azure e, em seguida, utilizar os endereços IP internos, conforme mostrado aqui:

![Nomes de anfitriões e endereços IP das VMs SAP listado no ficheiro /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>O ficheiro etc/fstab

É útil adicionar o **nofail** parâmetro para o ficheiro fstab. Desta forma, se houver algum problema com os discos, a VM não bloquear no processo de arranque. Mas, lembre-se de que o espaço em disco adicional poderão não estar disponível e processos podem preencher o sistema de ficheiros de raiz. Se /hana está em falta, não inicie o SAP HANA.

![Adicione o parâmetro de nofail para o ficheiro fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Gráfico GNOME ambiente de trabalho no SLES 12/SLES para SAP aplicações 12
Esta secção abrange os seguintes tópicos:

* A instalar o ambiente de trabalho GNOME e xrdp num SLES 12/SLES para SAP aplicações 12
* MC de SAP baseada em Java em execução, utilizando o Firefox SLES 12/SLES para SAP aplicações 12

Também pode utilizar alternativas como Xterminal ou VNC (não descritas neste guia).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>A instalar o ambiente de trabalho GNOME e xrdp num SLES 12/SLES para SAP aplicações 12
Se tiver um fundo do Windows, pode facilmente utilizar um ambiente de trabalho gráfico diretamente nas VMs Linux do SAP para executar o Firefox, SAPinst, SAP GUI, SAP MC ou HANA Studio e ligar à VM através do protocolo de ambiente de trabalho remoto (RDP) a partir de um computador Windows. Sistemas baseados em dependente políticas da sua empresa sobre a adição de interfaces de utilizador gráfica a produção e não a produção Linux, pode querer instalar GNOME no seu servidor. Para instalar o ambiente de trabalho GNOME num Azure SLES 12/SLES para SAP aplicações 12 VM:

1. Instale o ambiente de trabalho GNOME introduzindo o seguinte comando (por exemplo, numa janela PuTTY):

   `zypper in -t pattern gnome-basic`

2. Instale xrdp para permitir uma ligação à VM através de RDP:

   `zypper in xrdp`

3. Editar /etc/sysconfig/windowmanager e defina o Gestor de janela predefinido para GNOME:

   `DEFAULT_WM="gnome"`

4. Executar **chkconfig** para se certificar de que xrdp inicia-se automaticamente após um reinício:

   `chkconfig -level 3 xrdp on`

5. Se tiver um problema com a ligação RDP, tente reiniciar o (a partir de uma janela PuTTY, por exemplo):

   `/etc/xrdp/xrdp.sh restart`

6. Se um reinício de xrdp mencionado no passo anterior não funcionar, verifique a existência de um ficheiro de .pid:

   `check /var/run` 

   Procure `xrdp.pid`. Se encontrá-lo, remova-o e tente reiniciar o novamente.

### <a name="starting-sap-mc"></a>Iniciar SAP MC
Depois de instalar o ambiente de trabalho GNOME, começando SAP MC gráfica baseada em Java Firefox durante a execução num Azure SLES 12/SLES para SAP aplicações 12 VM poderá apresentar um erro devido a falta Java-browser Plug-in.

O URL para iniciar o SAP MC é `<server>:5<instance_number>13`.

Para obter mais informações, consulte [iniciar a consola de gestão baseada na Web do SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

A seguinte captura de ecrã mostra a mensagem de erro que é apresentada quando o plug-in Java-browser está em falta:

![Mensagem de erro que indica falta Java baseadas no browser Plug-in](./media/hana-get-started/image013.jpg)

Uma forma de resolver o problema está a instalar o falta Plug-in utilizando YaST, conforme mostrado na captura de ecrã seguinte:

![Utilizar YaST para instalar a falta de plug-in](./media/hana-get-started/image014.jpg)

Quando voltar a introduzir o URL de consola de gestão do SAP, é apresentada uma mensagem a pedir-lhe para ativar o plug-in:

![Caixa de diálogo a pedir a ativação de plug-in](./media/hana-get-started/image015.jpg)

Também poderá receber uma mensagem de erro sobre um ficheiro em falta, javafx.properties. Isto está relacionada com os requisitos do Oracle Java 1.8 para SAP GUI 7.4. (Consulte [nota SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Nem a versão do IBM Java nem o pacote de openjdk entregue com SLES/SLES para SAP aplicações 12 inclui o ficheiro javafx.properties necessários. A solução é transferir e instalar o Java zar 8 do Oracle.

Para informações sobre um problema semelhante com openjdk openSUSE, consulte o thread de debate [SAPGui 7.4 Java para openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalação manual de SAP HANA: SWPM
A série de capturas de ecrã nesta secção mostra os principais passos para instalar o SAP NetWeaver 7.5 e SAP HANA SP12 quando utilizar SWPM (SAPinst). Como parte de uma instalação de NetWeaver 7.5, SWPM também pode instalar a base de dados HANA como uma única instância.

Num ambiente de teste de exemplo, é instalada apenas num servidor de aplicações avançadas de negócio Application Programming (ABAP). Conforme mostrado na captura de ecrã seguinte, utilizámos o **sistema distribuído** opção para instalar o ASCS e instâncias do servidor principal da aplicação numa VM do Azure e SAP HANA como o sistema de base de dados na outra VM do Azure.

![ASCS e instâncias do servidor principal da aplicação instaladas utilizando a opção de sistema distribuído](./media/hana-get-started/image012.jpg)

Depois da instância ASCS está instalada no servidor de aplicações VM e é definido como "verde" na consola de gestão do SAP (mostrado na seguinte captura de ecrã), o diretório de /sapmnt (incluindo o diretório de perfil do SAP) tem de ser partilhado com o servidor de base de dados do SAP HANA VM. O passo de instalação da base de dados precisa de aceder a estas informações. A melhor forma de fornecer acesso está a utilizar o NFS, que pode ser configurado utilizando YaST.

![Consola de gestão de SAP que mostra a instância ASCS instalado no servidor de aplicações VM e definido como "verde"](./media/hana-get-started/image016.jpg)

No servidor de aplicações VM, o diretório de /sapmnt deve ser partilhado através de NFS, utilizando o **rw** e **no_root_squash** opções. As predefinições são **ro** e **root_squash**, que pode originar problemas ao instalar a instância de base de dados.

![Partilhar o diretório de /sapmnt através de NFS, utilizando as opções de rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Como mostra a captura de ecrã seguinte, a partilha de /sapmnt do servidor de aplicação VM tem de ser configurada no servidor de base de dados do SAP HANA VM utilizando **cliente NFS** (e YaST).

![A partilha de /sapmnt configurada utilizando o cliente NFS](./media/hana-get-started/image018b.jpg)

Para efetuar uma instalação distribuída do NetWeaver 7.5 (**instância de base de dados**), como mostrado na captura de ecrã seguinte, inicie sessão no servidor de base de dados do SAP HANA VM e inicie SWPM.

![Instalar uma instância de base de dados ao iniciar sessão no servidor de base de dados do SAP HANA VM e ao iniciá SWPM](./media/hana-get-started/image019.jpg)

Depois de selecionar **típico** instalação e o caminho para o suporte de dados de instalação, introduza um SID de base de dados, o nome do anfitrião, o número de instância e a palavra-passe de administrador do sistema de base de dados.

![A SAP HANA página base de dados sistema administrador início de sessão](./media/hana-get-started/image035b.jpg)

Introduza a palavra-passe para o esquema DBACOCKPIT:

![A caixa de entrada de palavra-passe para o esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Introduza uma pergunta da palavra-passe do SAPABAP1 esquema:

![Introduza uma pergunta da palavra-passe do SAPABAP1 esquema](./media/hana-get-started/image037b.jpg)

Cada tarefa esteja concluída, é apresentada uma marca de verificação verde junto a cada fase do processo de instalação de base de dados. A mensagem "execução de... É apresentada a base de dados instância concluída".

![Tarefa concluída janela com a mensagem de confirmação](./media/hana-get-started/image023.jpg)

Após a instalação com êxito, a consola de gestão do SAP também deve mostrar a instância de base de dados como "verde" e apresentar a lista completa dos processos de SAP HANA (hdbindexserver, hdbcompileserver e assim sucessivamente).

![Janela de consola de gestão de SAP com a lista de processos de SAP HANA](./media/hana-get-started/image024.jpg)

A seguinte captura de ecrã mostra as partes da estrutura de ficheiros sob o diretório de /hana/shared SWPM criado durante a instalação de HANA. Porque não existe nenhuma opção para especificar um caminho diferente, é importante montar o espaço em disco adicional no diretório de /hana antes da instalação de SAP HANA utilizando SWPM. Isto impede que o sistema de ficheiros de raiz ficar sem espaço livre.

![A estrutura de ficheiros do diretório de /hana/shared criada durante a instalação de HANA](./media/hana-get-started/image025.jpg)

Nesta captura de ecrã mostra a estrutura de ficheiros do diretório /usr/sap:

![A usr/sap ficheiro estrutura de diretórios](./media/hana-get-started/image026.jpg)

O último passo da instalação ABAP distribuída é instalar a instância de servidor principal da aplicação:

![Instância do principal da aplicação de apresentação de instalação de ABAP como passo final](./media/hana-get-started/image027b.jpg)

Depois de instalar a instância do servidor principal da aplicação e o SAP GUI, utilize o **DBA Cockpit** transação para confirmar se a instalação de SAP HANA foi concluída corretamente:

![Janela DBA Cockpit confirmar o êxito da instalação](./media/hana-get-started/image028b.jpg)

Como passo final, que pretende instalar primeiro HANA Studio no servidor de aplicação de SAP VM e, em seguida, ligue à instância do SAP HANA que está em execução no servidor de base de dados de VM:

![Instalar o SAP HANA Studio no servidor de aplicação de SAP VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalação manual de SAP HANA: HDBLCM
Para além de instalar o SAP HANA como parte de uma instalação distribuída utilizando SWPM, pode instalar o autónomo HANA em primeiro lugar, utilizando HDBLCM. Em seguida, pode instalar SAP NetWeaver 7.5, por exemplo. As capturas de ecrã nesta secção mostram como funciona este processo.

Para obter mais informações sobre a ferramenta de HANA HDBLCM, consulte:

* [Escolher o HDBLCM de HANA SAP correto para a sua tarefa](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Ferramentas de gestão de ciclo de vida do SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [Guia de atualização e instalação do servidor do SAP HANA](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Para evitar problemas com uma definição de ID de grupo predefinido para o `\<HANA SID\>adm user` (criado pela ferramenta de HDBLCM), definir um novo grupo chamado `sapsys` através da utilização de ID do grupo `1001` antes de instalar o SAP HANA através de HDBLCM:

![1001 de ID de grupo do novo grupo "sapsys" definidos através da utilização](./media/hana-get-started/image030.jpg)

Quando iniciar HDBLCM pela primeira vez, é apresentado um menu início simples. O item Select 1, **instalar o novo sistema**, conforme mostrado na captura de ecrã seguinte:

![Opção "Instalar o novo sistema" na janela de início do HDBLCM](./media/hana-get-started/image031.jpg)

A seguinte captura de ecrã mostra todas as opções de chaves que selecionou anteriormente.

> [!IMPORTANT]
> Diretórios com o nome de registo HANA e volumes de dados, bem como o caminho de instalação (hana/partilhado neste exemplo) e /usr/sap, não devem fazer parte do sistema de ficheiros de raiz. Estes diretórios pertencem aos discos de dados do Azure que foram ligados à VM (descrita na secção "Configuração de disco"). Esta abordagem ajuda a impedir que o sistema de ficheiros de raiz em execução sem espaço. Na captura de ecrã seguinte, pode ver que o administrador de sistema HANA possuem um ID de utilizador `1005` e faz parte o `sapsys` grupo (ID `1001`) que foi definida antes da instalação.

![Lista de todos os componentes de SAP HANA chave selecionada anteriormente](./media/hana-get-started/image032.jpg)

Pode verificar o `\<HANA SID\>adm user` (`azdadm` na seguinte captura de ecrã) detalhes no diretório de passwd etc /:

![HANA \<HANA SID\>detalhes de utilizador do adm listados no diretório de passwd etc /](./media/hana-get-started/image033.jpg)

Depois de instalar o SAP HANA utilizando HDBLCM, pode ver a estrutura de ficheiros no SAP HANA Studio, conforme mostrado na captura de ecrã seguinte. O esquema de SAPABAP1, que inclui todas as tabelas do SAP NetWeaver, ainda não está disponível.

![Estrutura de ficheiros de SAP HANA no SAP HANA Studio](./media/hana-get-started/image034.jpg)

Depois de instalar o SAP HANA, pode instalar o SAP NetWeaver seguir. Conforme mostrado na captura de ecrã seguinte, a instalação foi executada como uma instalação distribuída com SWPM (conforme descrito na secção anterior). Quando instalar a instância de base de dados utilizando SWPM, introduza os mesmos dados através da utilização de HDBLCM (por exemplo, nome de anfitrião, HANA SID e número de instância). SWPM, em seguida, utiliza a instalação de HANA existente e adiciona mais esquemas.

![Uma instalação distribuída efetuada utilizando SWPM](./media/hana-get-started/image035b.jpg)

A seguinte captura de ecrã mostra o passo de instalação SWPM onde introduzir dados sobre o esquema DBACOCKPIT:

![O passo de instalação de SWPM onde os dados de esquema DBACOCKPIT são introduzidos](./media/hana-get-started/image036b.jpg)

Introduza dados sobre o esquema de SAPABAP1:

![Introduzir dados sobre o esquema de SAPABAP1](./media/hana-get-started/image037b.jpg)

Uma vez concluída a instalação de instância de base de dados SWPM, pode ver o esquema de SAPABAP1 no SAP HANA Studio:

![O esquema de SAPABAP1 no SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Por fim, depois do servidor de aplicações SAP e instalações de SAP GUI estiverem concluídas, pode verificar a instância de base de dados HANA utilizando o **DBA Cockpit** transação:

![A instância de BD HANA verificada com a transação DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Transferências de software do SAP
Pode transferir o software do Marketplace de serviço SAP, conforme mostrado nas capturas de ecrã seguintes.

Transferi NetWeaver 7.5 para Linux/HANA:

 ![Janela de instalação do serviço SAP e a atualização para a transferência NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Transferir HANA SP12 plataforma edição:

 ![Janela de instalação do serviço SAP e a atualização para transferir HANA SP12 plataforma edição](./media/hana-get-started/image002.jpg)

