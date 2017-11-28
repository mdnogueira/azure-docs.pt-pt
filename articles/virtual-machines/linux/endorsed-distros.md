---
title: "Distribuições de Linux aprovadas pelo | Microsoft Docs"
description: "Saiba mais sobre Linux no distribuições aprovadas pelo Azure, incluindo as diretrizes para Ubuntu, CentOS, Oracle e SUSE."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: 811769443e322af3a2981c58979040a1e33b06e9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux em distribuições aprovadas pelo Azure
Parceiros fornecer imagens de Linux no Azure Marketplace. Estamos a trabalhar com várias comunidades de Linux para adicionar tipos ainda mais a lista de distribuição aprovadas. Entretanto, para as distribuições que não estão disponíveis no Marketplace, que pode sempre colocar o seus próprios Linux ao seguir as diretrizes no [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="supported-distributions-and-versions"></a>Distribuições suportadas e as versões
A tabela seguinte lista as distribuições de Linux e versões que são suportadas no Azure. Consulte [suporte para imagens de Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obter mais informações sobre o suporte para Linux e tecnologia de código aberto no Azure.

Os controladores de serviços de integração Linux (LIS) para o Hyper-V e do Azure são módulos de kernel que Microsoft contribui diretamente para o kernel do Linux a montante.  Alguns controladores LIS estão incorporados em kernel a distribuição por predefinição. Distribuições mais antigas que se baseiam no Red Hat Enterprise (RHEL) CentOS são disponíveis como uma transferência individual no [4.1 versão de serviços de integração do Linux para Hyper-V](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Consulte [requisitos de kernel do Linux](create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os controladores de LIS.

O agente Linux do Azure já está instalado previamente nas imagens do Azure Marketplace e está normalmente disponível a partir do repositório de pacote de distribuição. Código de origem pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).

  
| Distribuição | Versão | Controladores | Agente |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 + 7.0 + |CentOS 6.3: [LIS transferir](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: na kernel |Pacote: [repositório](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código de origem: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Na kernel |Código de origem: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 + |Na kernel |Pacote de: no repositório em "waagent" <br/>Código de origem: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Na kernel |Pacote de: no repositório em "WALinuxAgent" <br/>Código de origem: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 + 7.1 + |Na kernel |Pacote de: no repositório em "WALinuxAgent" <br/>Código de origem: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES para SAP<br>11 SP4<br>12 SP1 +|Na kernel |Pacote de:<p> para 11 no [nuvem: ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repositório<br>para 12 incluídos no módulo "Nuvem pública" em "python-azure-agente"<br/>Código de origem: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE bissexto 42.2 + |Na kernel |Pacote: [nuvem: ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repositório em "python-azure-agente" <br/>Código de origem: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |Na kernel |Pacote de: no repositório em "walinuxagent" <br/>Código de origem: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  para Ubuntu 12.04 suporte no Azure, consulte o [aviso EOL](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS
[https://coreos.com/Docs/Running-coreos/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

A partir do site CoreOS:

*CoreOS foi concebida para segurança, da consistência e fiabilidade. Em vez de instalar os pacotes através de yum ou apt, CoreOS utiliza contentores de Linux para gerir os seus serviços com um nível superior de abstração. Código de um único serviço e todas as dependências são reunidas num contentor que pode ser executado numa ou várias máquinas de CoreOS.*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-Images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ é um consultadoria independente e a empresa de serviços specializes no desenvolvimento e implementação de soluções profissionais ao utilizar software gratuito. Como especialistas de open source à esquerda, Credativ tem reconhecimento internacional com muitos departamentos de TI que utilizam o suporte. Em conjunto com a Microsoft, Credativ está atualmente a preparar imagens Debian correspondentes para 8 Debian (Jessie) e Debian antes de 7 (Wheezy). Ambas as imagens são especialmente concebidas para ser executado no Azure e podem ser facilmente geridas através da plataforma. Credativ será também suporta a manutenção de longa duração e a atualização das imagens Debian do Azure através do seus centros de suporte de origem Open.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Estratégia do Oracle é oferecer um amplo portefólio de soluções para nuvens públicas e privadas. A estratégia de fornece aos clientes opção e a flexibilidade na forma como implementam software Oracle em nuvens Oracle e de outras nuvens. A parceria da Oracle com a Microsoft permite aos clientes implementar software Oracle nas clouds pública e privada da Microsoft sabendo que conta com a certificação e o suporte da Oracle.  Compromisso da Oracle e o investimento em soluções de nuvens públicas e privadas Oracle permanece inalterado.

### <a name="red-hat"></a>Red Hat
[http://www.Redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Fornecedor de à esquerda do mundo das soluções de código aberto, Red Hat ajuda mais de 90% das empresas de Fortune 500 resolver desafios de negócio, alinhar os respetivos IT e estratégias de negócio e preparar para o futuro da tecnologia. Red Hat efetua este procedimento ao fornecer soluções seguras através de um modelo de subscrição previsível económica e um modelo de negócio aberta.

### <a name="suse"></a>SUSE
[http://www.SUSE.com/SUSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server no Azure é uma plataforma comprovada que fornece segurança e fiabilidade superior a informática em nuvem. Plataforma de Linux versátil do SUSE integra-se na perfeição com cloud services do Azure para fornecer um ambiente de nuvem facilmente gerível. Com mais do que 9,200 certificadas as aplicações de 1,800 mais do que fornecedores independentes de software para o SUSE Linux Enterprise Server, SUSE assegura que as cargas de trabalho em execução suportados no Centro de dados podem ser implementadas com confidencialidade no Azure.

### <a name="canonical"></a>Canónico
[http://www.ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Governação de Comunidade aberta e de engenharia canónica unidade êxito do Ubuntu no cliente, o servidor e de computação na nuvem, que inclui serviços na nuvem pessoal para consumidores. Visão do canónica de uma plataforma unificada, livre no Ubuntu, de telefone para a nuvem, fornece uma família de coherent interfaces para o telemóvel, tablet, TV e ambiente de trabalho. Esta visão torna Ubuntu a primeira escolha para instituições diversas fornecedores de nuvem pública criadores de electronics de consumidor e um favorito entre technologists individuais.

Com os programadores e centros de engenharia em todo o mundo, Canonical exclusivamente está posicionado para as parcerias com os criadores de hardware, fornecedores de conteúdos e os programadores de software para colocar a soluções Ubuntu colocação no mercado para PCs, servidores e dispositivos handheld.
