---
title: "Utilizar o SAP nas máquinas virtuais Windows | Microsoft Docs"
description: "Lear sobre como utilizar o SAP em máquinas de virtuais (VMs) do Windows no Microsoft Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Utilizar o SAP nas máquinas virtuais do Windows no Azure
A Informática em Nuvem é um termo utilizado em grande escala que está a ganhar cada vez mais importância no setor de TI, desde as pequenas às grandes empresas e até multinacionais. O Microsoft Azure é a Plataforma de Serviços Cloud da Microsoft que oferece um amplo espetro de novas possibilidades. Agora os clientes podem aprovisionar rapidamente e anular o aprovisionamento de aplicações como Serviços Cloud, para que não estejam limitados a restrições técnicas ou orçamentais. Em vez de investirem tempo e dinheiro na infraestrutura de hardware, as empresas podem concentrar-se na aplicação, nos processos empresariais e nas vantagens para os clientes e utilizadores.

Com as máquinas virtuais do Microsoft Azure, a Microsoft oferece uma infraestrutura completa como uma plataforma de serviço (IaaS). As aplicações baseadas em SAP NetWeaver são suportadas em Máquinas Virtuais do Azure (IaaS). Os documentos técnicos abaixo descrevem como planear e implementar aplicações de NetWeaver SAP com base em máquinas virtuais do Windows no Azure. Também pode implementar aplicações de NetWeaver SAP com base em [máquinas virtuais do Linux](../../linux/classic/sap-get-started.md).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver no Azure - HA
Título: SAP NetWeaver no Azure - Clustering SAP ASCS/SCS instâncias utilizando o Cluster de ativação pós-falha do Windows Server no Azure com SIOS DataKeeper

Resumo: ' Este documento descreve como utilizar SIOS DataKeeper para configurar uma configuração de SAP ASCS/SCS altamente disponível no Azure. SAP protege o respetivo ponto único de componentes de falha, como o SAP ASCS/SCS ou colocar em fila os serviços de replicação com configurações de Cluster de ativação pós-falha do Windows Server que necessitam de discos partilhados. Estes componentes SAP são essenciais para a funcionalidade de um sistema SAP. Por conseguinte, a funcionalidade de elevada disponibilidade tem de ser colocada no local para se certificar de que esses componentes podem suportar uma falha de um servidor ou numa VM como feito com configurações de Cluster do Windows para ambientes Hyper-V e bare-metal. A partir de Agosto de 2015 Azure no próprio não pode fornecer elevadas configurações necessárias para estes componentes mais importantes do SAP de baseado em discos partilhados que seriam necessários para o Windows. No entanto com a ajuda do produto DataKeeper por SIOS, configurações de Cluster de ativação pós-falha do Windows Server, conforme necessário para SAP ASCS/SCS podem ser criadas na plataforma do IaaS do Azure. Este documento descreve uma abordagem de passo-a-passo, como instalar uma configuração de Cluster de ativação pós-falha do Windows Server com o disco partilhado fornecido pelo SIOS Datakeeper no Azure. O documento explicar detalhes em configurações do lado do Azure, o Windows e o SAP que facilitam a configuração de elevada disponibilidade funcionam de forma ideal. O documento complementa o SAP notas que representam os recursos para as instalações e implementações de software do SAP primários em determinadas plataformas e a documentação de instalação do SAP.

Atualizada: De Agosto de 2015

[Transferir este guia agora](http://go.microsoft.com/fwlink/?LinkId=613056)

