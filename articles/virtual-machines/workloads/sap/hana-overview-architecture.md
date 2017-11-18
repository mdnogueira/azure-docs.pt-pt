---
title: "Descrição geral e arquitetura de SAP HANA no Azure (instâncias de grande) | Microsoft Docs"
description: "Descrição geral da arquitetura de como implementar o SAP HANA no Azure (instâncias de grande)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2461e5fbf620fa2651792b47d41e9835d4d6ef8c
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Descrição geral de SAP HANA (instâncias de grandes dimensões) e arquitetura no Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é o SAP HANA no Azure (instâncias de grande)?

SAP HANA no Azure (instância grande) é uma solução exclusiva para o Azure. Além de fornecer as máquinas virtuais do Azure com o objetivo de implementação e execução de SAP HANA, Azure oferece a possibilidade da executar e implementar SAP HANA nos servidores de bare-metal dedicadas para o utilizador como um cliente. O SAP HANA na solução do Azure (instâncias de grande) baseia-se em hardware bare-metal não partilhado/servidor de anfitrião que está atribuída como um cliente. O hardware do servidor está incorporado no carimbos de data / maiores que contêm/servidor de computação, rede e infraestrutura de armazenamento. Que, como uma combinação é TDI HANA certificado. A oferta de serviço de SAP HANA no Azure (instâncias de grande) oferece várias SKUs de servidor diferente ou tamanhos de carateres começadas unidades que tenham 72 CPUs e 768 GB de memória às unidades que tenham 960 CPU e memória de 20 TB.

O isolamento de cliente no carimbo de infraestrutura é efetuado na inquilinos, que em detalhe aspeto:

- Rede: Isolamento de clientes na pilha de infraestrutura através de redes virtuais por inquilino atribuído do cliente. Um inquilino é atribuído a um único cliente. Um cliente pode ter vários inquilinos. O isolamento de rede de inquilinos proíbe a comunicação de rede entre os inquilinos no nível de carimbo de data / da infraestrutura. Mesmo que os inquilinos pertencerem ao mesmo cliente.
- Componentes de armazenamento: isolamento através da máquinas de virtuais de armazenamento que tenham volumes de armazenamento atribuídas. Volumes de armazenamento podem ser atribuídos a um armazenamento máquina apenas. Uma máquina virtual de armazenamento é atribuída exclusivamente a um inquilino único na pilha de infraestrutura de certificados de SAP HANA TDI. Como resultado, os volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acedidos num específico e relacionado inquilino apenas. E são invisível entre os diferentes inquilinos implementados.
- Anfitrião ou servidor: uma unidade de anfitrião do servidor ou não é partilhada entre os clientes ou inquilinos. Um servidor ou o anfitrião implementada para um cliente, é uma unidade de computação do bare-metal atómica que está atribuída a um único inquilino. **Não** criação de partições de hardware ou a criação de partições de forma recuperável que pode resultar numa, como um cliente, a partilha de um anfitrião ou um servidor com outro cliente. Armazenamento que estão atribuídas à máquina virtual de armazenamento do inquilino específico estão montados para esse servidor. Um inquilino pode ter um muitas unidades de servidor de SKUs diferentes atribuídos exclusivamente.
- Dentro de um SAP HANA no carimbo de infraestrutura do Azure (instância grande), muitos inquilinos diferentes são implementados e isolados contra entre si através dos conceitos de inquilino na rede, armazenamento e computação nível. 


Estas unidades bare-metal server são suportadas para executar apenas o SAP HANA. O SAP aplicação camada ou carga de trabalho meio-ware maligno está em execução em máquinas virtuais do Microsoft Azure. Os carimbos de infraestrutura com o SAP HANA nas unidades do Azure (instância grande) estão ligados as rede de Azure backbones, por isso, que é a conectividade de latência baixa entre SAP HANA nas unidades do Azure (instância grande) e Virtual Machines do Azure fornecida.

Este documento é um dos cinco documentos, que abrangem o tópico de SAP HANA no Azure (grande instância). Neste documento, vamos aceda através de arquitetura básica, responsabilidades, serviços fornecidos e um nível elevado através de funcionalidades da solução. Para a maioria das áreas, como o funcionamento em rede e a conectividade, os documentos de quatro outros são que abrangem os detalhes e desagregar pendentes. A documentação do SAP HANA no Azure (instância grande) não abrange aspetos da instalação do SAP NetWeaver ou implementações de SAP NetWeaver em VMs do Azure. Este tópico é descrito na documentação separada localizada no contentor de documentação do mesmo. 


As cinco partes deste guia abrangem os seguintes tópicos:

- [Descrição geral de SAP HANA (instância grande) e arquitetura no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestrutura de SAP HANA (instâncias de grandes dimensões) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Como instalar e configurar o SAP HANA (instâncias de grande) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Resolução de problemas de SAP HANA (instâncias de grandes dimensões) e monitorização no Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Definições

Várias definições comuns em grande escala são utilizadas na arquitetura e o guia de implementação técnica. Tenha em atenção os seguintes termos e os respetivos significados:

- **IaaS:** infraestrutura como serviço
- **PaaS:** plataforma como serviço
- **SaaS:** Software como serviço
- **Componente SAP:** uma aplicação de SAP individuais, tal como ECC, BW, Gestor de solução ou EP. Componentes SAP podem ser baseados em tecnologias tradicionais ABAP ou Java ou uma aplicação não baseada em NetWeaver como objetos de negócio.
- **Ambiente de SAP:** um ou mais componentes do SAP logicamente agrupados para efetuar uma função de negócio, como o desenvolvimento, QAS, formação, DR ou de produção.
- **SAP horizontal:** refere-se aos ativos SAP completos no seu horizontal IT. O horizontal SAP inclui todos os ambientes de não produção e produção.
- **Sistema SAP:** a combinação de camada DBMS e a camada de aplicação de um sistema de desenvolvimento do SAP ERP, sistema de teste de SAP BW, sistema de produção do SAP CRM, etc. Implementações do Azure não suportam dividindo estas duas camadas entre no local e o Azure. Significa que um sistema SAP é implementado no local ou está implementado no Azure. No entanto, pode implementar os sistemas diferentes de um horizontal SAP para o Azure ou no local. Por exemplo, pode implementar os sistemas de desenvolvimento e teste de SAP CRM no Azure, durante a implementação de SAP CRM produção sistema no local. Para SAP HANA no Azure (instâncias de grande), destina-se que alojar a camada de aplicação de SAP dos sistemas SAP em VMs do Azure e a instância de SAP HANA relacionada numa unidade no carimbo de instância grande HANA.
- **Carimbo de instância grande:** uma pilha de infraestrutura de hardware que é o SAP HANA TDI certificada e dedicados para executar instâncias de SAP HANA no Azure.
- **SAP HANA no Azure (instâncias de grande):** oficial nome para a oferta no Azure executar HANA instâncias no SAP HANA TDI certificadas hardware que é implementada em carimbos de data / instância grande em diferentes regiões do Azure. O termo relacionado **instância grande HANA** é curto para SAP HANA no Azure (instâncias de grandes dimensões) e é amplamente utilizado neste guia de implementação técnica.
- **Em vários locais:** descreve um cenário onde as VMs implementadas para uma subscrição do Azure com o site para site, multilocal ou ExpressRoute conectividade entre o datacenter(s) no local e o Azure. Documentação do Azure em comum, estes tipos de implementações também estão descritos como cenários de vários locais. O motivo para a ligação está a expandir domínios no local/OpenLDAP de diretório Active Directory no local, DNS e no local no Azure. O horizontal no local é expandido para os recursos do Azure das subscrições do Azure. Com esta extensão, as VMs podem fazer parte do domínio no local. Utilizadores de domínio do domínio no local podem aceder aos servidores e podem executar serviços em dessas VMs (por exemplo, o DBMS serviços). É possível resolução do nome e a comunicação entre as VMs implementadas no local e as VMs implementadas do Azure. Como é o cenário típico no qual SAP a maioria dos recursos são implementados. Consulte os guias de [planeamento e design para o Gateway de VPN](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [criar uma VNet com uma ligação Site a Site utilizando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para informações mais detalhadas.
- **O inquilino:** um cliente implementado no stamp instâncias grande HANA obtém isolado para um "inquilino". Um inquilino esteja isolado na rede, armazenamento e camada de computação de outros inquilinos. Por isso, se as unidades de armazenamento e computação atribuídas a diferentes inquilinos não podem ver entre si ou comunicam entre si no nível de carimbo de data / instância grande HANA. Um cliente pode optar por implementações em diferentes inquilinos. Mesmo assim, não há nenhuma comunicação entre inquilinos no nível de carimbo de data / instância grande HANA.
- **Categoria SKU:** para as instâncias de grande HANA, são fornecidas as seguintes duas categorias de SKUs.
    - **Escreva posso classe:** S72, S72m, S144, S144m, S192 e S192m
    - **Classe do tipo II:** S384, S384m, S384xm, S576, S768 e S960


Existem uma variedade de recursos adicionais que foram publicados no tópico de implementação de carga de trabalho SAP na nuvem pública do Microsoft Azure. Recomenda-se vivamente que qualquer pessoa planeamento e a execução de uma implementação de SAP HANA no Azure está experiente e em consideração os principais do IaaS do Azure e a implementação de cargas de trabalho do SAP no IaaS do Azure. Os seguintes recursos fornecem mais informações e devem ser referenciados antes de continuar:


- [Utilizando soluções SAP em máquinas virtuais do Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certificação

Para além de certificação NetWeaver, SAP requer uma certificação especial para SAP HANA suportar o SAP HANA em determinados infraestruturas, como o IaaS do Azure.

É o núcleo nota SAP NetWeaver e a certificação de SAP HANA um grau, [1928533 de n. º de nota SAP – aplicações SAP no Azure: os tipos de produtos suportados e VM do Azure](https://launchpad.support.sap.com/#/notes/1928533).

Isto [2316233 de n. º de nota SAP - SAP HANA no Microsoft Azure (instâncias de grande)](https://launchpad.support.sap.com/#/notes/2316233/E) também é significativa. Aborda a solução descrita neste guia. Além disso, são suportados para executar o SAP HANA no tipo GS5 VM do Azure. [Informações para este cenário são publicadas no Web site do SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

O SAP HANA na solução do Azure (instâncias de grande) referida na SAP nota #2316233 fornece os clientes Microsoft e SAP a capacidade de implementar grandes SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA ou outras cargas de trabalho de SAP HANA no Azure. A solução baseia-se no carimbo SAP HANA certificadas hardware dedicado ([SAP HANA adaptados Datacenter integração – TDI](https://scn.sap.com/docs/DOC-63140)). A ser executado como um TDI de HANA SAP solução configurada fornece-lhe a confiança de saber que todas as aplicações baseadas em SAP HANA (incluindo o SAP Business Suite no SAP HANA, SAP Business armazém (BW) em SAP HANA, S4/HANA e BW4/HANA) vão continuar a trabalhar no hardware infraestrutura.

Em comparação com SAP HANA no Azure máquinas virtuais em execução nesta solução tem uma vantagem — proporciona muito maiores volumes de memória. Se pretender ativar esta solução, existem alguns aspetos fundamentais para compreender:

- As aplicações de camada e não SAP da aplicação de SAP executam no Azure máquinas virtuais (VMs) que estão alojadas no carimbos habitual de hardware do Azure.
- Infraestrutura no local do cliente, centros de dados e as implementações de aplicações estão ligadas à plataforma de nuvem do Microsoft Azure através do ExpressRoute do Azure (recomendado) ou rede privada Virtual (VPN). Do Active Directory (AD) e DNS também é expandido no Azure.
- A instância de base de dados SAP HANA para carga de trabalho HANA é executada em SAP HANA no Azure (instâncias de grande). O carimbo de instância grande está ligado em funcionamento em rede do Azure, para que o software em execução em VMs do Azure pode interagir com a instância HANA em execução em instâncias de grande HANA.
- Hardware de SAP HANA no Azure (instâncias de grande) é o hardware dedicado fornecido numa infraestrutura como serviço (IaaS) com SUSE Linux Enterprise Server ou Red Hat Enterprise Linux, pré-instaladas. Como com máquinas de virtuais do Azure, obter atualizações e manutenção para o sistema operativo é da responsabilidade do cliente.
- Instalação de HANA nem quaisquer componentes adicionais necessários para executar o SAP HANA em unidades de instâncias HANA grande é da responsabilidade do cliente, como todas as respetivas operações em curso e as administrações de SAP HANA no Azure.
- Para além das soluções descritas aqui, pode instalar outros componentes na sua subscrição do Azure que liga ao SAP HANA no Azure (instâncias de grande).  Por exemplo, os componentes que ativar a comunicação com e/ou diretamente para a base de dados SAP HANA (ir servidores, servidores de RDP SAP HANA Studio, serviços de dados SAP para cenários de BI do SAP, ou soluções de monitorização de rede).
- Como no Azure, instâncias de grande HANA oferecem suporte funcionalidade de elevada disponibilidade e recuperação após desastre.

## <a name="architecture"></a>Arquitetura

A um nível elevado, o SAP HANA na solução do Azure (instâncias de grande) tem a camada de aplicação de SAP residentes nas VMs do Azure e a camada de base de dados que reside no hardware TDI SAP configurado localizada no carimbo instância grande na mesma região do Azure que está ligada ao IaaS do Azure .

> [!NOTE]
> Tem de implementar a camada de aplicação de SAP na mesma região do Azure como a camada de SAP DBMS. Esta regra é bem documentada publicados informações sobre a carga de trabalho do SAP no Azure. 

A arquitetura geral de SAP HANA no Azure (instâncias de grande) fornece uma configuração de hardware certificado SAP TDI (não virtualizados, bare metal, o servidor de elevado desempenho para a base de dados SAP HANA) e a capacidade e a flexibilidade do Azure para recursos de escala de a camada da aplicação SAP para satisfazer as suas necessidades.

![Descrição geral da arquitetura de SAP HANA no Azure (instâncias de grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada está dividida nas três secções:

- **Direito:** uma infraestrutura no local a executar aplicações diferentes nos centros de dados com os utilizadores finais aceder a aplicações de LOB (como SAP). Idealmente, isto no local infraestrutura está ligada, em seguida, para o Azure com o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **System Center:** mostra Azure IaaS e, neste caso, a utilização de VMs do Azure para alojar o SAP ou outras aplicações que utilizam o SAP HANA como um sistema DBMS. Instâncias HANA mais pequenas que fornecem a função com a memória VMs do Azure são implementadas em VMs do Azure, juntamente com a respetiva camada de aplicação. Saiba mais sobre [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).
<br />Rede do Azure é utilizado para agrupar sistemas SAP, juntamente com outras aplicações em redes virtuais do Azure (VNets). Nestas VNets ligar a sistemas no local, bem como SAP HANA no Azure (instâncias de grande).
<br />Para aplicações SAP NetWeaver e bases de dados que são suportados para executar no Microsoft Azure, consulte [1928533 de n. º de nota de suporte de SAP – aplicações SAP no Azure: os tipos de produtos suportados e VM do Azure](https://launchpad.support.sap.com/#/notes/1928533). Para obter a documentação sobre como implementar soluções SAP no Azure, consulte:

  -  [Utilizando o SAP em máquinas de virtuais (VMs) do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utilizando soluções SAP em máquinas virtuais do Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **À esquerda:** mostra TDI de HANA SAP certificadas hardware no carimbo de instância de grande de Azure. As unidades de instância grande HANA estão ligadas a VNets do Azure da sua subscrição utilizando a mesma tecnologia como a conectividade no local no Azure.

O carimbo de instância de grande de Azure próprio combina os componentes seguintes:

- **Computação:** servidores baseados em Intel Xeon E7-8890v3 ou Intel Xeon E7-8890v4 processadores que fornecem a capacidade informática necessária e que são certificadas do SAP HANA.
- **Rede:** A unified recursos de infraestrutura de rede de alta velocidade interconnects computação, armazenamento e componentes de rede local.
- **Armazenamento:** uma infraestrutura de armazenamento que é acedida através de uma infraestrutura de rede unificada. Capacidade de armazenamento específico é fornecida, consoante o SAP HANA específicas sobre a configuração do Azure (instâncias de grande) que está a ser implementada (mais capacidade de armazenamento está disponível um custo mensal adicionais).

Dentro da infraestrutura de multi-inquilino do carimbo instância grande, os clientes são implementados como inquilinos isolados. Na implementação do inquilino, terá de nome de uma subscrição do Azure dentro da sua inscrição do Azure. Este vai ser a subscrição do Azure, as instâncias de grande HANA vai ser-lhe faturado contra. Estes inquilinos tem uma relação de 1:1 à subscrição do Azure. Rede aconselhado é possível aceder a uma unidade de instância grande HANA implementada num inquilino na região do Azure um do outro as VNets do Azure, que pertencem a diferentes subscrições do Azure. Apesar das subscrições do Azure têm de pertencer a mesma inscrição do Azure. 

Tal como acontece com as VMs do Azure, SAP HANA no Azure (instâncias de grande) é fornecido em várias regiões do Azure. Para oferecer funcionalidades de recuperação após desastre, pode optar por participar no. Carimbos de instância grande diferentes dentro de uma região georreplicação afiliações ligados entre si. Por exemplo, HANA grande instância carimbos dos EUA Oeste como nos EUA Leste estão ligados através de uma ligação de rede dedicada para fins de replicação de DR. 

Tal como pode escolher entre diferentes tipos VM com máquinas virtuais do Azure, pode escolher entre os SKUs de HANA grande instâncias diferentes adaptados para tipos diferentes de carga de trabalho de SAP HANA. SAP aplica-se memória rácios de socket de processador para diferentes cargas de trabalho com base no gerações de processadores Intel. A tabela seguinte mostra os tipos SKU oferecidos.

A partir de Julho de 2017, SAP HANA no Azure (instâncias de grande) está disponível em várias configurações no Azure regiões dos EUA oeste e dos EUA leste, leste da Austrália, Sudeste da Austrália, Europa Ocidental e Europa do Norte:

| Solução SAP | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho OLAP genérica | SAP HANA no S72 do Azure<br /> – 2x processador Intel Xeon de®® E7 8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  768 GB |  3 TB | Disponível |
| --- | SAP HANA no S144 do Azure<br /> – 4 x processador Intel Xeon de®® E7 8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  1,5 TB |  6 TB | Não oferecido já |
| --- | SAP HANA no S192 do Azure<br /> – 4 x processador Intel Xeon de®® E7 8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  2.0 TB |  8 TB | Disponível |
| --- | SAP HANA no S384 do Azure<br /> – 8 x processador Intel Xeon de®® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4.0 TB |  16 TB | Pronto para ordenação |
| Otimizado para OLTP: SAP Business Suite<br /> SAP HANA ou S/4HANA (OLTP)<br /> OLTP genérico | SAP HANA no S72m do Azure<br /> – 2x processador Intel Xeon de®® E7 8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  1,5 TB |  6 TB | Disponível |
|---| SAP HANA no S144m do Azure<br /> – 4 x processador Intel Xeon de®® E7 8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  3.0 TB |  12 TB | Não oferecido já |
|---| SAP HANA no S192m do Azure<br /> – 4 x processador Intel Xeon de®® E7 8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU  |  4.0 TB |  16 TB | Disponível |
|---| SAP HANA no S384m do Azure<br /> – 8 x processador Intel Xeon de®® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6.0 TB |  18 TB | Pronto para ordenação |
|---| SAP HANA no S384xm do Azure<br /> – 8 x processador Intel Xeon de®® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8.0 TB |  22 TB |  Pronto para ordenação |
|---| SAP HANA no S576 do Azure<br /> – 12 x processador Intel Xeon de®® E7 8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12.0 TB |  28 TB | Pronto para ordenação |
|---| SAP HANA no S768 do Azure<br /> – 16 x processador Intel Xeon de®® E7 8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16.0 TB |  36 TB | Pronto para ordenação |
|---| SAP HANA no S960 do Azure<br /> – 20 x processador Intel Xeon de®® E7 8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20.0 TB |  46 TB | Pronto para ordenação |

- Núcleos de CPU = soma de núcleos de CPU não-com hyper-threading da soma de processadores da unidade do servidor.
- Threads de CPU = soma de threads de computação fornecidos pelo núcleos de CPU com hyper-threading da soma de processadores da unidade do servidor. Todas as unidades estão configuradas por predefinição para utilizar o Hyper-Threading.


As configurações diferentes acima que se encontram disponíveis ou 'não poderão utilizar deixam de ser' referenciadas na [2316233 de n. º de nota de suporte de SAP – SAP HANA no Microsoft Azure (instâncias de grande)](https://launchpad.support.sap.com/#/notes/2316233/E). As configurações que estão marcadas como 'Pronto para ordenação' irão encontrar os respetivos entrada para a nota SAP em breve. No entanto, os SKUs de instância pode ser ordenados já para seis diferentes regiões do Azure que o serviço de instância grande HANA está disponível.

As configurações específicas escolhidas são dependentes de carga de trabalho, recursos de CPU e memória pretendida. É possível utilizar os SKUs que estão otimizados para carga de trabalho do OLAP de carga de trabalho OLTP. 

A base de hardware para todas as ofertas estão SAP HANA TDI certificados. No entanto, iremos distinguir entre duas classes diferentes de hardware, o qual divide os SKUs para:

- S72, S72m, S144, S144m, S192 e S192m, que denominamos 'Type posso de classe' de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que denominamos 'class II de tipo' de SKUs.

É importante ter em atenção que um carimbo de instância grande HANA completo não está alocado exclusivamente para um único cliente &#39; utilize s. Este facto aplica-se a bastidores dos recursos de computação e armazenamento ligados através de uma infraestrutura de rede implementada no Azure, bem como. Infraestrutura de instâncias de grande HANA, como o Azure, implementa o cliente diferentes &quot;inquilinos&quot; que estão isoladas umas nas seguintes três níveis:

- Rede: Isolamento através de redes virtuais no carimbo de instância grande HANA.
- Armazenamento: Isolamento através da máquinas de virtuais de armazenamento que tenham volumes de armazenamento atribuída e isolar os volumes de armazenamento entre inquilinos.
- Computação: Dedicado a atribuição de unidades de servidor para um único inquilino. Não difícil ou configuração soft-criação de partições de unidades do servidor. Nenhuma partilha de uma única unidade de anfitrião do servidor ou entre inquilinos. 

Como tal, as implementações de unidades de instâncias de grande HANA entre diferentes inquilinos não estão visíveis para si. Nem HANA grande instância unidades implementada em diferentes inquilinos podem comunicar diretamente entre si no nível de carimbo de data / instância grande HANA. Apenas HANA grande instância unidades dentro de um inquilino podem comunicar entre si no nível de carimbo de data / instância grande HANA.
Um inquilino implementado no carimbo de instância grande é atribuído a faturação aconselhado uma subscrição do Azure. No entanto, rede aconselhado que pode ser acedido a partir as VNets do Azure de outras subscrições do Azure dentro da mesma inscrição do Azure. Se implementar com outra subscrição do Azure na mesma região do Azure, também pode optar por voltar a pedir um inquilino de instância grande HANA separado.

Existem diferenças significativas que executem SAP HANA instâncias grande HANA e SAP HANA em execução em VMs do Azure implementado no Azure:

- Não há nenhuma camada de virtualização para SAP HANA no Azure (instâncias de grande). Obter o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o SAP HANA no servidor do Azure (instâncias de grande) está dedicado a um cliente específico. Não há nenhum possibilidade de um anfitrião ou a unidade do servidor é difícil ou particionada de forma recuperável. Como resultado, uma unidade de instância grande HANA é utilizada como atribuída como um todo para um inquilino e com que como um cliente. Um reinício ou encerramento do servidor não pode automaticamente para o sistema operativo e a implementar noutro servidor do SAP HANA. (Para o tipo de classe posso SKUs, a única exceção é se um servidor poderão surgir problemas e reimplementação tem de ser efetuada noutro servidor.)
- Ao contrário do Azure, onde os tipos de processadores do anfitrião estão selecionados para o rácio de preço/desempenho melhor, os tipos de processador escolhidos para SAP HANA no Azure (instâncias de grande) são efetuar mais elevado da linha de processador Intel E7v3 e E7v4.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>A executar várias instâncias de SAP HANA numa unidade de instância grande HANA
É possível mais do que uma instância de SAP HANA ativa das unidades de instância grande HANA do anfitrião. Para fornecer ainda as capacidades de instantâneos de armazenamento e recuperação após desastre, tal configuração de requer um volume definido por instância. A partir de agora, as unidades de instância grande HANA podem subdivididas da seguinte forma:

- S72, S72m, S144, S192: em incrementos de 256 GB com 256 GB a unidade de menor inicial. Incrementos diferentes como 256 GB, 512 GB e assim sucessivamente, podem ser combinados para o número máximo de memória da unidade.
- S144m e S192m: em incrementos de 256 GB com GB 512 a unidade mais pequena. Incrementos diferentes como 512 GB, 768 GB e assim sucessivamente, podem ser combinados para o número máximo de memória da unidade.
- Tipo de classe II: em incrementos de 512 GB com a unidade inicial menor do que 2 TB. Incrementos diferentes como 512 GB, 1 TB, 1,5 TB e assim sucessivamente, podem ser combinados para o número máximo de memória da unidade.

Alguns exemplos de executar várias instâncias de SAP HANA podem ter o seguinte aspeto:

| SKU | Tamanho da Memória | Tamanho de Armazenamento | Tamanhos com várias bases de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA instância<br /> ou instância de 1 x 512 GB + 1 x 256 GB instância<br /> ou instâncias de 3 x 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA instâncias<br />ou instância de 1 x 512 GB + 1 1 TB instância<br />ou instâncias de 6 x 256 GB<br />ou instância de 1x1.5 TB | 
| S192m | 4 TB | 16 TB | Instâncias de 8 x 512 GB<br />ou instâncias de 4 1 TB<br />ou instâncias de 4 x 512 GB + 2 1 TB instâncias<br />ou instâncias de 4 x 768 GB + instâncias de 2 x 512 GB<br />a instância de 1 4 TB ou |
| S384xm | 8 TB | 22 TB | Instâncias de 4 x 2 TB<br />ou instâncias de 2 4 TB<br />ou instâncias de 2, 3 TB + 1 x 2 TB instâncias<br />ou instâncias de 2x2.5 TB + 1 x 3 TB instâncias<br />a instância de 1 x 8 TB ou |


Obter a ideia. Existem certamente bem outras variações. 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>Utilizando nós de SAP HANA dados camadas e de extensão
SAP suporta um modelo de camadas de dados para SAP BW de diferentes versões do SAP NetWeaver e SAP BW/4HANA. Detalhes relativos para as camadas de dados modelo pode encontrar este documento e o blogue referenciado neste documento por SAP: [SAP BW/4HANA SAP BW ON HANA com SAP HANA extensão nós e](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com instâncias de grande HANA, pode utilizar a opção-1 a configuração de SAP HANA extensão nós conforme detalhado em documentos de blogue neste FAQ e SAP. Opção 2 configurações podem ser configuradas com as seguintes instâncias SKUs HANA grande: S72m, S192, S192m, S384 e S384m.  
Observar a documentação a vantagem poderá não estar visível imediatamente. Mas procura para as diretrizes de dimensionamento de SAP, pode ver uma vantagem utilizando a opção-1 e 2 opção nós de extensão de SAP HANA. Um exemplo aqui:

- Diretrizes de dimensionamento de SAP HANA normalmente necessitam de duplo a quantidade de volume de dados como memória. Por isso, quando estiver a executar a instância de SAP HANA com os dados, só tem de 50% ou inferior a memória preenchida com dados. O resto da memória ideal é mantido para SAP HANA fazer o seu trabalho.
- Esse significa numa unidade de HANA grande instância S192 com 2 TB de memória, com uma base de dados SAP BW, só tem de 1 TB como volume de dados.
- Se utilizar um nó de extensões do SAP HANA adicional de opção-1, também um S192 HANA grande instância SKU, este seria dão-lhe uma capacidade de 2 TB adicional para o volume de dados. No TB a configuração de opção-2 4 adicionais e até mesmo volume de dados transfira. Em comparação com o nó de acesso frequente, a capacidade da memória total do nó de extensão 'transfira' pode ser utilizada para dados duplos e armazenar para a opção-1 que a memória pode ser utilizada para o volume de dados na configuração de nó da extensão de SAP HANA de opção-2.
- Como resultado acaba por ficar com uma capacidade de 3 TB para os dados e um rácio de acesso frequente-para-transfira de 2:1 para a opção-1 e 5 TB de dados e um rácio de 1:4 na configuração de nó de extensão de opção-2.

No entanto, quanto maior for o volume de dados em comparação comparado a memória, quanto maior for as possibilidades são que os dados transfira é solicitando é armazenado no armazenamento de disco.


## <a name="operations-model-and-responsibilities"></a>Modelo de operações e responsabilidades

O serviço fornecido com o SAP HANA no Azure (instâncias de grande) está alinhado com os serviços do IaaS do Azure. Obter uma instância de instâncias de grande HANA com um sistema operativo instalado que está otimizada para SAP HANA. Tal como acontece com as VMs IaaS do Azure, a maioria das tarefas de proteção do SO, instalar o software adicional necessita, instalar HANA, o SO e HANA, a funcionar e de atualizar o SO e HANA é da responsabilidade do cliente. Microsoft não forçar atualizações do SO ou atualizações HANA no utilizador.

![Responsabilidades de SAP HANA no Azure (instâncias de grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Como pode ver no diagrama acima, o SAP HANA no Azure (instâncias de grande) é um infraestrutura de multi-inquilino como uma oferta de serviço. Como resultado, a divisão de responsabilidade se encontrando o limite de infraestrutura do SO, pela maioria das vezes. Microsoft é responsável por todos os aspetos do serviço abaixo da linha do sistema operativo e é responsáveis acima da linha, incluindo o sistema operativo. Por isso, mais atuais no local métodos que pode ser empregar para gestão de compatibilidade, segurança, gestão de aplicações, base e SO podem continuar a ser utilizado. Os sistemas de aparecem como se estão na sua rede no que diz respeito todos os.

No entanto, este serviço está otimizado para SAP HANA, pelo que existem áreas onde o utilizador e a Microsoft têm funcionam em conjunto para utilizar as capacidades de infraestrutura subjacente para obter os melhores resultados.

A lista seguinte fornece mais detalhes em cada uma das suas responsabilidades e as camadas:

**Redes:** todas as redes internas para o carimbo de grande instância em execução SAP HANA, o acesso ao armazenamento, a conectividade entre o instâncias (para Escalamento horizontal e outras funções), a conectividade a horizontal e conectividade para o Azure onde camada da aplicação de SAP está alojada no Azure Virtual Machines. Também inclui conectividade WAN entre centros de dados do Azure para replicação de objetivos de recuperação após desastre. Todas as redes são divididos em partições pelo inquilino e aplicou QOS.

**Armazenamento:** o virtualizado particionada armazenamento para todos os volumes necessários para os servidores de SAP HANA, bem como para instantâneos. 

**Servidores:** os servidores físicos dedicados para executar as bds de HANA SAP atribuído aos inquilinos. Os servidores do tipo posso classe de SKUs são abstracted de hardware. Com estes tipos de servidores, a configuração do servidor é recolhida e mantida em perfis, que podem ser movidas um hardware físico para outro hardware físico. Essa uma (manual) mudança de um perfil por operações pode ser comparado com um pouco autorrecuperação do serviço do Azure. Os servidores de SKUs de classe II de tipo não são oferta essa uma capacidade.

**SDDC:** centros do software de gestão que é utilizado para gerir os dados como entidades definidas por software. -Permite à Microsoft para recursos de conjunto de dimensionamento, disponibilidade e motivos de desempenho.

**/ S:** SO escolher (SUSE Linux ou Red Hat Linux) que está em execução nos servidores. As imagens de SO que são fornecidas são as imagens fornecidas pelo fornecedor de Linux de individuais para a Microsoft para fins de execução de SAP HANA. É necessário ter uma subscrição com o fornecedor do Linux para a imagem específica com otimização de SAP HANA. As suas responsabilidades incluem registar as imagens com o fornecedor do SO. A partir do ponto de handover pela Microsoft, é também responsável por quaisquer ainda mais a aplicação de patches do sistema operativo Linux. Esta aplicação de patches também inclui pacotes adicionais que poderão ser necessários para uma instalação de SAP HANA com êxito (consulte a documentação de instalação HANA e notas SAP do SAP) e que não foi incluído pelo fabricante do Linux específico no respetivo SAP HANA otimizado SO imagens. Responsabilidade do cliente também inclui a aplicação de patches do SO que está relacionado com avaria/otimização de sistema operativo e os controladores relacionados com o hardware de servidor específico. Qualquer segurança ou a aplicação de patches funcional do SO. Responsabilidade do cliente é bem monitorização e planeamento de capacidade de:

- Consumo de recursos de CPU
- Consumo de memória
- Volumes de disco relacionados com o espaço livre, o IOPS e latência
- Tráfego de volume de rede entre a instância de grande HANA e a camada de aplicação do SAP

A infraestrutura subjacente de instâncias de grande HANA fornece a funcionalidade cópia de segurança e restauro do volume de SO. Utilizar esta funcionalidade também é da responsabilidade do cliente.

**Middleware:** SAP HANA instância, principalmente. Administração, operações e monitorização são da responsabilidade do cliente. Não há funcionalidade desde que permite-lhe utilizar instantâneos de armazenamento de cópia de segurança/restauro e objetivos de recuperação após desastre. Estas funcionalidades são fornecidas pela infraestrutura. No entanto, as suas responsabilidades também incluem conceber elevada disponibilidade ou recuperação de desastre com estas capacidades, tirar partido dos mesmos, monitorização e instantâneos de armazenamento tem sido executados com êxito.

**Dados:** os dados geridos pelo SAP HANA e outros dados, como cópias de segurança de partilhas de ficheiro ou ficheiros localizados em volumes. As suas responsabilidades incluem monitorização espaço livre em disco, gerir o conteúdo nos volumes e monitorizar a execução bem-sucedida de cópias de segurança de volumes de disco e instantâneos de armazenamento. No entanto, uma execução bem sucedida da replicação de dados para sites de DR é da responsabilidade do Microsoft.

**Aplicações:** as instâncias de aplicações SAP ou, em caso de aplicações não SAP, a camada de aplicação dessas aplicações. As suas responsabilidades incluem a implementação, administração, operações e monitorização dessas aplicações relacionadas com o planeamento da capacidade do consumo de recursos de CPU, o consumo de memória, o consumo de armazenamento do Azure e o consumo de largura de banda de rede dentro do As VNets do Azure e às Azure VNets para SAP HANA no Azure (instâncias de grande).

**WANs:** ligações estabelecer no local para implementações do Azure para cargas de trabalho. Todos os nossos clientes com instâncias de grande HANA utilizam Azure ExpressRoute para conectividade. Esta ligação não é parte de SAP HANA na solução do Azure (instâncias de grande), pelo que é responsáveis pelo programa de configuração desta ligação.

**Arquivo:** poderá preferir arquivar cópias dos dados utilizando os suas próprias métodos em contas de armazenamento. Arquivar necessita de operações, os custos, conformidade e gestão. É responsável pela geração de cópias de arquivo e as cópias de segurança do Azure e armazene-os de forma em conformidade.

Consulte o [SLA para SAP HANA no Azure (instâncias de grande)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Dimensionamento

Dimensionamento para instâncias de grande HANA é não diferem Dimensionar para HANA em geral. Para existente e implementados sistemas, pretender mover de outro RDBMS para HANA, SAP fornece um conjunto de relatórios que são executadas em sistemas de SAP existentes. Se a base de dados é movido para HANA, estes relatórios Verifique os dados e calcular os requisitos de memória para a instância HANA. Leia as notas de SAP seguintes para obter mais informações sobre como executar estes relatórios e como obter as respetivas versões/patches mais recentes:

- [A nota #1793345 - Dimensionar para SAP Suite no HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP nota #1872170 - Suite HANA e S/4 HANA relatório de dimensionamento](https://launchpad.support.sap.com/#/notes/1872170)
- [A nota #2121330 - FAQ: SAP BW no HANA relatório de dimensionamento](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP nota #1736976 - relatório de dimensionamento para BW no HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP nota #2296290 - novo relatório de dimensionamento para BW no HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde, Sizer rápida SAP está disponível para calcular os requisitos de memória da implementação do software SAP por cima HANA.

Requisitos de memória para HANA estão aumentar conforme o crescimentos de volume de dados, pelo que deve ter em consideração o consumo de memória agora e conseguir prever que se passa a estar no futuro. Com base nos requisitos de memória, pode mapear, em seguida, o pedido dos SKUs de instância grande HANA.

## <a name="requirements"></a>Requisitos

Esta lista monta requisitos para executar o SAP HANA no Azure (instâncias superior).

**Microsoft Azure:**

- Uma subscrição do Azure que pode ser associada a SAP HANA no Azure (instâncias de grande).
- Contrato do suporte do Microsoft Premier. Consulte [2015553 de n. º de nota de suporte de SAP – SAP no Microsoft Azure: pré-requisitos do suporte](https://launchpad.support.sap.com/#/notes/2015553) para obter informações específicas relacionadas com o SAP no Azure. Utilizar unidades de instância grande HANA com 384 e mais CPUs, também tem de alargar o contrato do suporte Premier para incluir resposta rápida do Azure (ARR).
- Deteção de instâncias grande SKUs necessário depois de efetuar um dimensionamento exerce com SAP HANA.

**Conectividade de rede:**

- Azure ExpressRoute no local para o Azure: ligar o seu centro de dados no local ao Azure, certifique-se ordenar, pelo menos, uma ligação de Gbps 1 a partir do seu ISP. 

**Sistema operativo:**

- Licenças do SUSE Linux Enterprise Server 12 para aplicações SAP.

> [!NOTE] 
> O sistema operativo disponibilizadas pela Microsoft não está registado no SUSE, nem está ligado com uma instância SMT.

- SUSE Linux subscrição gestão ferramenta (SMT) implementados no Azure numa VM do Azure. Isto fornece a capacidade para SAP HANA no Azure (instâncias de grande) para ser registado e respetivamente atualizado pelo SUSE (uma vez que não há nenhum acesso à internet no Centro de dados de instâncias de grande HANA). 
- Licenças do Red Hat Enterprise Linux 6.7 ou 7.2 para SAP HANA.

> [!NOTE]
> O sistema operativo disponibilizadas pela Microsoft não está registado no Red Hat, nem está ligado a uma instância do Red Hat subscrição Manager.

- Red Hat subscrição Manager implementado no Azure numa VM do Azure. O Gestor de subscrição do Red Hat fornece a capacidade para SAP HANA no Azure (instâncias de grande) para ser registado e respetivamente atualizado pelo Red Hat (uma vez que não há nenhum acesso direto à internet de dentro do inquilino implementado no carimbo instância grande de Azure).
- SAP requer que tenha um suporte de contrato, bem como o seu fornecedor de Linux. Este requisito não é apagar pela solução de instâncias de grande HANA ou o facto de que a executar Linux no Azure. Ao contrário com algumas das imagens de galeria do Azure do Linux, a taxa de serviço não está incluída na oferta de soluções de instâncias de grande HANA. É, como um cliente para satisfazer os requisitos de SAP relativos contratos de suporte com o distribuidor de Linux.   
   - Procurar para SUSE Linux, os requisitos do contrato de suporte no [1984787 de n. º de nota SAP - SUSE LINUX Enterprise Server 12: notas instalação](https://launchpad.support.sap.com/#/notes/1984787) e [&#1056161; nota SAP - SUSE prioridade suporte para aplicações SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para Red Hat Linux, tem de ter os níveis de subscrição correta que incluem suporte e de serviço (atualizações para os sistemas operativos de instâncias de grande HANA. Red Hat recomenda a obter um "RHEL para [SAP soluções](https://access.redhat.com/solutions/3082481)" subscrição. 

Para a matriz de suporte com a versão de SAP HANA diferente com as diferentes versões de Linux, consulte [SAP nota #2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Base de dados:**

- As licenças e componentes de instalação de software para SAP HANA (plataforma ou enterprise edition).

**Aplicações:**

- As licenças e componentes de instalação de software para quaisquer aplicações SAP a ligar ao SAP HANA e relacionadas com o SAP suportam contratos.
- Licenças e componentes de instalação de software para quaisquer aplicações SAP não utilizada relativamente SAP HANA no ambiente do Azure (instâncias de grandes dimensões) e relacionadas com contratos de suporte.

**Competências:**

- Experiência e dados de conhecimento no IaaS do Azure e os respetivos componentes.
- Experiência e conhecimentos sobre a implementação da carga de trabalho do SAP no Azure.
- Instalação do SAP HANA certificados pessoal.
- Competências de arquiteto SAP para o design de elevada disponibilidade e recuperação após desastre em torno de SAP HANA.

**SAP:**

- Expectativas são de que já for um cliente SAP e tem um suporte de contrato com o SAP
- Especialmente para implementações na classe II de tipo de instância SKUs de grande HANA, recomenda vivamente consultar com SAP em versões do SAP HANA e eventual configurações de hardware de cópia de segurança de escala de tamanho grande.


## <a name="storage"></a>Armazenamento

O esquema de armazenamento para SAP HANA no Azure (instâncias de grande) está configurado por SAP HANA na gestão de serviço do Azure através do SAP recomendado diretrizes, documentadas no [os requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) documento técnico.

As instâncias de grande HANA do tipo de classe posso vêm com quatro vezes o volume de memória como volume de armazenamento. Para o tipo de classe II de unidades de instância grande HANA, o armazenamento não vai ser mais de quatro vezes. As unidades vêm com um volume, o que é dirigido para armazenar cópias de segurança de registos de transação de HANA. Encontrar mais detalhes em [como instalar e configurar o SAP HANA (instâncias de grande) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consulte a tabela seguinte em termos de alocação de armazenamento. A tabela apresenta uma lista de aproximadamente a capacidade para os volumes diferentes fornecido com as diferentes unidades de instância grande HANA.

| HANA grande instância SKU | Hana/dados | Hana/registo | Hana/partilhado | Hana / / cópia de segurança |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16,000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36,000 GB | 4100 GB | 2050 GB | 4100 GB |


Volumes implementados reais podem variar um pouco com base na implementação e a ferramenta que é utilizada para mostrar os tamanhos de volume.

Se subdividir um SKU de instância grande HANA, alguns exemplos de peças de divisão possíveis deverá ter o seguinte aspeto:

| Partição de memória em GB | Hana/dados | Hana/registo | Hana/partilhado | Hana / / cópia de segurança |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


Estes são números de volume aproximada que podem diferir ligeiramente com base na implementação e ferramentas utilizadas para ver os volumes. Também existem outros tamanhos de partição thinkable, como 2,5 TB. Estes tamanhos de armazenamento deverá ser calculados com uma fórmula semelhante como utilizado para as partições acima. 'partitions' não indicam que o sistema operativo, memória ou recursos da CPU estão em qualquer forma particionada. Indica apenas as partições de armazenamento para as diferentes instâncias HANA que pode querer implementar numa única unidade de instância grande HANA. 

Como um cliente pode ter precisa de mais armazenamento, terá a possibilidade de adicionar o armazenamento para adquirir armazenamento adicional em unidades de 1 TB. Este tipo de armazenamento adicional pode ser adicionado como volume adicional ou pode ser utilizado para expandir um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes que originalmente implementadas e principalmente documentado tabelas acima. Também não é possível alterar os nomes dos volumes ou os nomes de montagem. Os volumes de armazenamento, conforme descrito acima estão anexados às unidades instância grande HANA como NFS4 volumes.

Como um cliente pode optar por utilizar instantâneos de armazenamento para fins de recuperação após desastre e de cópia de segurança/restauro. Obter mais detalhes sobre este tópico passo são detalhados no [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Encriptação de dados inativos
O armazenamento utilizado para instâncias de grande HANA permite que a encriptação transparente de dados que são armazenadas nos discos. No momento da implementação de uma unidade de instância grande HANA, terá a opção para que este tipo de encriptação ativada. Também pode optar por alterar para volumes encriptados após a implementação já. A movimentação de não encriptados para volumes encriptados é transparente e não necessita de um tempo de inatividade. 

Com o tipo de que classe posso SKUs, o volume de arranque é armazenado LUN, é encriptado. Em caso do tipo de classe II de SKUs de HANA grande instâncias, terá de encriptar o arranque LUN com métodos de SO. Para obter mais informações, contacte a equipa de gestão de serviço da Microsoft.


## <a name="networking"></a>Redes

A arquitetura de redes do Azure é um componente fundamental para uma implementação efetuada com êxito das aplicações SAP HANA instâncias de grandes dimensões. Normalmente, SAP HANA em implementações do Azure (instâncias de grande) tem uma maior horizontal SAP com várias soluções SAP diferentes com diferentes tamanhos de bases de dados, o consumo de recursos de CPU e utilização da memória. Provavelmente não todos esses sistemas SAP baseiam SAP HANA, para que a sua horizontal SAP, provavelmente, seria uma versão híbrida que utiliza:

- Implementação de SAP sistemas no local. Devido ao respetivos tamanhos, estes sistemas atualmente não podem ser alojados no Azure; um exemplo de clássico seria um sistema de SAP ERP de produção em execução no Microsoft SQL Server (como a base de dados) que necessita de mais CPU ou de recursos de memória que pode fornecer as VMs do Azure.
- Implementada com base em SAP HANA SAP sistemas no local.
- Sistemas SAP implementados em VMs do Azure. Estes sistemas ser desenvolvimento, teste, sandbox, ou instâncias de produção para qualquer uma das aplicações com base em SAP NetWeaver que podem implementar com êxito no Azure (em VMs), com base no pedido de memória e o consumo de recursos. Estes sistemas também podem basear nas bases de dados como o SQL Server (consulte [1928533 de n. º de nota de suporte de SAP – aplicações SAP no Azure: os tipos de produtos suportados e VM do Azure](https://launchpad.support.sap.com/#/notes/1928533/E)) ou SAP HANA (consulte [SAP HANA certificadas IaaS as plataformas](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)).
- Implementar servidores de aplicações SAP no Azure (em VMs) que tiram partido de SAP HANA no Azure (instância grande) no carimbos de data / instância grande de Azure.

Enquanto uma versão híbrida horizontal SAP (com quatro ou mais diferentes cenários de implementação) é típica, há muitos cenários de cliente de horizontal SAP completa em execução no Azure. Como VMs do Microsoft Azure são cada vez mais poderosas, está a aumentar o número de clientes mover todas as soluções de SAP no Azure.

Não é complicado Azure redes no contexto de sistemas SAP implementado no Azure. Baseia-se nos seguintes princípios:

- Redes virtuais do Azure (VNets) tem de estar ligado ao circuito ExpressRoute do Azure que estabelece ligação à rede no local.
- Um circuito de ExpressRoute ligar no local para o Azure, normalmente, deve ter uma largura de banda de 1 Gbps ou superior. Esta largura de banda mínima permite largura de banda suficiente para transferir dados entre sistemas no local e em execução em VMs do Azure (bem como a ligação ao Azure sistemas que os utilizadores finais no local).
- Todos os sistemas SAP no Azure tem de ser configurados em VNets do Azure para comunicar entre si.
- Active Directory e DNS alojadas no local são expandidas no Azure através do ExpressRoute do local.


> [!NOTE] 
> A partir de um ponto de vista faturação, apenas uma única subscrição do Azure pode ser associada apenas a um único inquilino num carimbo de instância grande numa região do Azure específico e, por outro lado um inquilino de carimbo de data / instância grande único pode ser associado apenas a uma subscrição do Azure. Facto deste não é diferente para os outros objetos sujeito a faturação no Azure

Implementação de SAP HANA no Azure (instâncias de grande) em várias regiões do Azure diferentes, resulta de um inquilino separado para ser implementada no carimbo de instância grandes. No entanto, pode executar ambas na mesma subscrição do Azure, desde que estas instâncias fazem parte do mesmo horizontal SAP. 

> [!IMPORTANT] 
> Apenas a implementação de gestão de recursos do Azure é suportada com SAP HANA no Azure (instâncias de grande).

 

### <a name="additional-azure-vnet-information"></a>Informações adicionais de VNet do Azure

Para poder ligar uma VNet do Azure expressroute, tem de ser criado um gateway do Azure (consulte [sobre gateways de rede virtual para o ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Um gateway do Azure pode ser utilizado com o ExpressRoute para uma infraestrutura fora do Azure (ou para um carimbo de instância do Azure grande) ou para estabelecer ligação entre as VNets do Azure (consulte [configurar uma ligação VNet a VNet para o Resource Manager com o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Pode ligar o gateway do Azure para um máximo de quatro ligações ExpressRoute diferentes, desde que essas ligações são feitos routers MS Enterprise contornos (MSEE) diferentes.  Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias de grandes dimensões) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> O débito fornece um gateway do Azure é diferente para ambos os casos de utilização (consulte [sobre o Gateway de VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). O débito máximo que pode alcançar com um gateway de VNet é de 10 Gbps, através de uma ligação ExpressRoute. Tenha em atenção que a copiar ficheiros entre uma VM do Azure que reside numa VNet do Azure e um sistema local (como uma sequência de cópia única) não conseguir do débito total dos SKUs de gateway diferentes. Para tirar partido de largura de banda completa do gateway de VNet, tem de utilizar vários fluxos ou copiar ficheiros diferentes em fluxos paralelos de um único ficheiro.


### <a name="networking-architecture-for-hana-large-instances"></a>Arquitetura de rede para instâncias de grande HANA
A arquitetura de rede para instâncias de grande HANA conforme mostrado abaixo, pode ser separada em quatro partes distintas:

- Redes no local e a ligação do ExpressRoute para o Azure. Esta parte é o domínio de clientes e ligado ao Azure através do ExpressRoute. Esta é a parte na parte inferior direita de gráficos abaixo.
- Funcionamento em rede do Azure como brevemente discutido acima com as VNets do Azure, novamente com gateways. Trata-se de uma área onde tem de localizar as estruturas adequadas de requisitos de aplicações, segurança e os requisitos de conformidade. Utilizando as instâncias grande HANA é outro ponto de consideração em termos de número de SKUs à sua escolha de gateway VNets e o Azure. Esta é a parte no canto superior direito de gráficos.
- Conectividade de instâncias de grande HANA através de tecnologia de ExpressRoute para o Azure. Esta peça é implementada e processada pela Microsoft. Tudo o que precisa para fazer um cliente está a fornecer alguns IP intervalos de endereços e após a implementação dos seus ativos em instâncias de grande HANA ligação ExpressRoute circuitos para o Azure VNet(s) (consulte [infraestrutura de SAP HANA (instâncias de grandes dimensões) e conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
- Funcionamento em rede em instâncias de grande HANA, que é principalmente transparente, para, como um cliente.

![VNet do Azure ligado ao SAP HANA no Azure (instâncias de grandes dimensões) e no local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

O facto de a utilização de instâncias HANA grande não alterar o requisito para obter os seus recursos no local ligados através do ExpressRoute para o Azure. Também não altera a necessidade de ter uma ou várias VNets que executar as VMs do Azure que anfitrião camada da aplicação que liga as instâncias HANA alojada em unidades de instância grande HANA. 

A diferença para implementações de SAP no Azure puro, é fornecido com os factos que:

- As unidades de instância grande HANA do seu inquilino do cliente estão ligadas através de outro circuito do ExpressRoute para o seu VNet(s) do Azure. Para separar as condições de carga, o local para ligações do ExpressRoute das VNets do Azure e ligações entre as VNets do Azure e instâncias HANA grandes não partilham os mesmos routers.
- O perfil de carga de trabalho entre a camada de aplicação SAP e a instância de HANA é uma natureza diferente do número de pedidos pequeno e rajada como dados transferidos (conjuntos de resultados) de SAP HANA na camada da aplicação.
- A arquitetura de aplicações SAP é mais sensível à latência de rede do que cenários típicos onde os dados obtém trocados entre no local e o Azure.
- O gateway de VNet tem, pelo menos, duas ligações ExpressRoute e ambas as ligações partilham a largura de banda máxima para dados recebidos do gateway de VNet.

A latência de rede experiente entre as VMs do Azure e HANA grandes unidades de instância pode ser superior a uma típica latência reportadas round-trip de rede de VM para VM. Depende a região do Azure, os valores de medida podem exceder a latência de reportadas round-trip 0.7 ms classificada como abaixo média [1100926 de n. º de nota SAP - FAQ: desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Contudo clientes baseados em SAP HANA produção SAP aplicações muito com êxito no SAP HANA grande instâncias implementadas. Os clientes que implementado comunicadas excelente melhoramentos ao executar as aplicações SAP no SAP HANA utilizar unidades de instância grande HANA. Contudo deverá testar cuidadosamente os processos empresariais no Azure HANA grande instâncias.
 
Para fornecer a latência de rede determinista entre as VMs do Azure e a instância de grande HANA, é essencial a escolha do SKU de Gateway de VNet do Azure. Ao contrário de padrões de tráfego entre no local e as VMs do Azure, o padrão de tráfego entre VMs do Azure e instâncias de grande HANA pode desenvolver bursts pequenos mas elevados de volumes de dados e pedidos a ser transmitidos. Para poder efetuar essas bursts processados bem, recomendamos vivamente a utilização do SKU de Gateway UltraPerformance. Para a classe de II de tipo de instância SKUs de grande HANA, a utilização do gateway UltraPerformance SKU como Gateway de VNet do Azure é obrigatória.  

> [!IMPORTANT] 
> Tendo em conta o tráfego de rede global entre camadas de base de dados de aplicação de SAP e, apenas o HighPerformance ou UltraPerformance SKUs de gateway para VNets é suportada para ligar ao SAP HANA no Azure (instâncias de grande). A instância do tipo II SKUs HANA grande, apenas o SKU de Gateway UltraPerformance é suportado como Gateway de VNet do Azure.



### <a name="single-sap-system"></a>Sistema SAP única

A infraestrutura no local mostrada acima está ligada através do ExpressRoute para o Azure e o circuito de ExpressRoute liga-se para um Router de limite do Microsoft Enterprise (MSEE) (consulte [descrição geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Depois de estabelecido, essa rota liga-se para a estrutura de principal do Microsoft Azure, e todas as regiões do Azure estão acessíveis.

> [!NOTE] 
> Para executar SAP landscapes no Azure, estabelecer a ligação do MSEE mais próximo da região do Azure na horizontal SAP. Carimbos de grande instância do Azure estão ligados através de dispositivos MSEE dedicados para minimizar a latência de rede entre as VMs do Azure no IaaS do Azure e a instância grande carimbos de data /.

O gateway de VNet para as VMs do Azure, instâncias de aplicações SAP, de alojamento está ligado a essa circuito do ExpressRoute e a mesma VNet está ligada a um Router MSEE separado dedicado para ligar a carimbos de data / instância grandes.

Este é um exemplo simples de um único sistema SAP, em que camada da aplicação de SAP está alojada no Azure e a base de dados SAP HANA é executado no SAP HANA no Azure (instâncias de grande). Pressuposto é que a largura de banda de gateway de VNet de 2 Gbps ou de 10 Gbps débito não representa um estrangulamento.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

Se vários SAP sistemas ou grandes SAP sistemas são implementados ligar ao SAP HANA no Azure (instâncias de grande) &#39; s razoável assuma o débito do gateway de VNet podem tornar-se um engarrafamento. Nesse caso, precisa de dividir as camadas da aplicação em várias VNets do Azure. Também poderá ser recommendable criar VNets especiais que se ligam a instâncias de grande HANA para casos, como:

- Efetuar cópias de segurança diretamente a partir de instâncias HANA em instâncias de grande HANA para uma VM no Azure que aloja partilhas NFS
- Copiar grande cópias de segurança ou outros ficheiros de unidades de instância grande HANA para espaço de disco gerido no Azure.

Utilizar VNets separadas que anfitrião VMs que gerir o armazenamento evita impacto por ficheiros grandes ou transferência de dados de instâncias de grande HANA no Azure no Gateway de VNet que serve as VMs com a camada da aplicação de SAP. 

Para uma arquitetura de rede mais escalável:

- Tire partido de várias VNets do Azure para uma camada de aplicação de SAP única e superior.
- Implemente uma VNet do Azure separado para cada sistema SAP implementado, comparada com a combinar estes sistemas SAP sub-redes separadas sob a mesma VNet.

 Uma arquitetura de rede mais dimensionável para SAP HANA no Azure (instâncias de grande):

![Implementar a camada de aplicação de SAP através de várias VNets do Azure](./media/hana-overview-architecture/image4-networking-architecture.png)

Implementar a camada de aplicação do SAP ou componentes, através de várias VNets do Azure conforme mostrado acima, introduzida overhead de latência de obrigatório que ocorreram durante a comunicação entre as aplicações alojadas nesses VNets do Azure. Por predefinição, o tráfego de rede entre as VMs do Azure localizadas no diferentes VNets encaminhar através de routers MSEE nesta configuração. No entanto, dado que Setembro de 2016 este encaminhamento pode ser otimizado. Peering as VNets do Azure na mesma região é a forma de otimizar e diminuir a latência de comunicação entre duas VNets. Mesmo que esses VNets estão em subscrições diferentes. Utilizar o Azure VNet peering, a comunicação entre VMs em duas VNets diferentes do Azure pode utilizar o principal de rede do Azure para comunicar diretamente entre si. Deste modo, mostrando a latência semelhante como se as VMs seria na mesma VNet. Enquanto que o tráfego, endereçamento intervalos de endereços IP que estão ligados através do gateway de VNet do Azure, é encaminhado através do gateway de VNet individuais da VNet. Pode obter detalhes sobre o Azure VNet peering no artigo [o VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Encaminhamento no Azure

Existem duas considerações de encaminhamento de rede importantes para SAP HANA no Azure (instâncias de grande):

1. SAP HANA no Azure (instâncias de grande) só pode ser acedido por VMs do Azure na ligação de ExpressRoute dedicada; não diretamente a partir do local. Alguns clientes de administração e quaisquer aplicações que necessitam acesso direto, tal como o SAP solução Manager em execução no local, não é possível ligar à base de dados SAP HANA.

2. SAP HANA nas unidades do Azure (instâncias de grande) têm um endereço IP atribuído o endereço de conjunto de IP do servidor de intervalo, como o cliente submetido (consulte [infraestrutura de SAP HANA (instâncias de grandes dimensões) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes).  Este endereço IP é acessível através de subscrições do Azure e ExpressRoute que liga as VNets do Azure para HANA no Azure (instâncias de grande). O endereço IP atribuído fora desse conjunto de IP do servidor intervalo de endereços lhe esteja diretamente atribuído à unidade de hardware e já não faz NAT'ed deste foi as maiúsculas e minúsculas nas implementações primeiro desta solução. 

> [!NOTE] 
> Se necessitar de ligar ao SAP HANA no Azure (instâncias de grande) num _do armazém de dados_ cenário, onde as aplicações e/ou os utilizadores finais necessitam para ligar à base de dados SAP HANA (diretamente em execução), tem de ser utilizado outro componente de rede: um proxy inverso para encaminhar os dados, de e para. Por exemplo, F5 BIG-IP, NGINX com o Gestor de tráfego seja implementado no Azure como uma solução de encaminhamento de tráfego/firewall virtual.

### <a name="internet-connectivity-of-hana-large-instances"></a>Acesso à Internet de instâncias de grande HANA
Instâncias de grande HANA não tem conectividade internet direta. Isto é restringir as capacidades para, por exemplo, registar a imagem do SO diretamente com o fornecedor do SO. Por conseguinte, poderá ter de trabalhar com o servidor de SLES SMT local ou o Gestor de subscrição de RHEL

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Encriptação de dados entre as VMs do Azure e instâncias de grande HANA
Dados transferidos entre instâncias de grande HANA e as VMs do Azure não estão encriptados. No entanto, puramente para a troca entre o lado HANA DBMS e as aplicações de JDBC/ODBC com base pode ativar a encriptação de tráfego. Referência [esta documentação por SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Utilizar unidades de instância grande HANA em várias regiões

Poderá ter outros motivos para implementar o SAP HANA no Azure (instâncias de grande) em várias regiões do Azure, para além de recuperação após desastre. Talvez pretende aceder instâncias grande HANA a partir de cada uma das VMs implementadas nas VNets diferentes nas regiões. Uma vez que os endereços IP atribuídos às unidades HANA instâncias de grandes dimensões diferentes não são propagados para além das VNets do Azure (que estão ligados diretamente através do seu gateway as instâncias), não há uma alteração ligeiras a conceção de VNet introduzida acima: uma VNet do Azure Gateway consegue processar quatro circuitos do ExpressRoute diferentes fora MSEEs diferentes e cada VNet que está ligada a um dos carimbos de instância de grandes dimensões pode ser ligada ao carimbo instância grande noutra região do Azure.

![VNets do Azure ligadas a carimbos de instância de grande de Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura acima mostra como as VNets do Azure diferente em ambas as regiões estão ligadas à dois circuitos do ExpressRoute diferentes que são utilizados para ligar ao SAP HANA no Azure (instâncias de grande) em ambas as regiões do Azure. As ligações recentemente introduzidas são as linhas vermelhos retangular. Com estas ligações, sem as VNets do Azure, as VMs em execução desses VNets podem aceder a cada uma das unidades de instâncias de grande HANA diferentes implementadas em duas regiões. Como ver na gráficos acima, presume-se que tem duas ligações ExpressRoute no local para as duas regiões do Azure; recomendado por motivos de recuperação após desastre.

> [!IMPORTANT] 
> Se forem utilizados vários circuitos ExpressRoute, prefixação como caminho e as definições de BGP de preferência Local devem ser utilizadas para garantir o correto de encaminhamento de tráfego.


