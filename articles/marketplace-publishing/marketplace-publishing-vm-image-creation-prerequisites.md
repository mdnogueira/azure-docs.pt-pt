---
title: "Pré-requisitos técnicos para criar uma imagem de máquina virtual para o Azure Marketplace | Microsoft Docs"
description: "Compreenda os requisitos para criar e implementar uma imagem de máquina virtual no Azure Marketplace para outras pessoas para comprar."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: af3e2ad623d8d7bfafe676411f9ae3fbee78aab8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Pré-requisitos técnicos para criar uma imagem de máquina virtual para o Azure Marketplace
Leia o processo cuidadosamente antes de começar e compreenda o onde e por que motivo é efetuado cada passo. Quanto possível, deve preparar as informações da sua empresa e outros dados, descarregar as ferramentas necessárias, e/ou criar os componentes de técnicos antes de iniciar o processo de criação de oferta. Estes itens devem ser encriptados de rever este artigo.  

## <a name="download-needed-tools--applications"></a>Transferir as ferramentas necessárias & aplicações
Deve ter os seguintes itens prontos antes de iniciar o processo:

* Consoante o sistema operativo que está a filtrar, instale o [cmdlets Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) ou [ferramenta de interface de linha de comandos do Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) do [transferências do Azure](https://azure.microsoft.com/downloads/) página.
* Instale o Explorador de armazenamento do Azure a partir de CodePlex.
* Transferir e instalar a ferramenta de teste de certificação para o certificado do Azure:
  * [http://go.microsoft.com/fwlink/?LinkId=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Precisa de um computador baseado no Windows para executar a ferramenta de certificação. Se não tiver um computador baseado em Windows disponível, pode executar a ferramenta com uma VM baseada em Windows no Azure.

## <a name="platforms-supported"></a>Plataformas suportadas
Pode desenvolver as VMs baseadas no Azure no Windows ou Linux. Alguns elementos do processo de publicação - por exemplo, criar um Azure compatível com disco rígido virtual (VHD) – Utilize diferentes ferramentas e os passos, dependendo do sistema operativo que está a utilizar:  

* Se estiver a utilizar o Linux, consulte a secção "Criar um VHD compatível com o Azure (baseado em Linux)" do [guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).
* Se estiver a utilizar o Windows, consulte a secção "Criar um VHD compatível com o Azure (baseado no Windows)" do [guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Precisa de acesso a uma máquina baseados no Windows para:
> 
> * Execute a ferramenta de validação de certificação.
> * Crie o URL de assinatura de acesso VHD partilhado para a submissão de certificação de VHD.
> 
> 

## <a name="develop-your-vhd"></a>Desenvolver o VHD
Pode desenvolver VHDs do Azure na nuvem ou no local:

* Desenvolvimento baseado na nuvem significa que todos os passos de desenvolvimento são executados remotamente num VHD residente no Azure.
* Desenvolvimento no local requer um VHD de transferir e a desenvolvê-la utilizando a infraestrutura no local. Embora seja possível, recomendamos-lo. Tenha em atenção que desenvolver para Windows ou SQL Server no local requer que tenha as chaves de licenciamento relevantes no local. Não é possível incluir ou instalar o SQL Server depois de criar uma VM. Também deve basear a sua oferta uma imagem do SQL Server aprovados do portal do Azure. Se optar por desenvolver no local, tem de efetuar alguns passos de forma diferente se foram desenvolver na nuvem. Pode encontrar informações relevantes no [criar uma imagem VM no local](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
