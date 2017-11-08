---
title: "Descrição geral de como criar e implementar uma oferta do Marketplace | Microsoft Docs"
description: "Compreender os passos necessários para se tornar um programador aprovado da Microsoft e criar e implementar uma imagem de máquina virtual, modelo, o serviço de dados ou serviço do Programador no Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: fa00f176ff56c13033907e134fc236c291308028
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicar e gerir uma oferta no Azure Marketplace
Este artigo é fornecido para ajudar os programadores criar, implementar e gerir as respetivas soluções listadas no Azure Marketplace para outros clientes do Azure e os parceiros de compra e utilizar.

## <a name="marketplace-publishing"></a>Publicação do Marketplace
Como um publicador do Azure, pode distribuir e propor a solução inovadora ou o serviço para outros programadores, ISVs e profissionais de TI no Marketplace. Através do Marketplace, pode aceder aos clientes que pretendem rapidamente desenvolver as aplicações baseadas na nuvem e soluções móveis. Se a sua solução destina-se os utilizadores empresariais, deverá considerar o [AppSource](http://appsource.microsoft.com) marketplace.


## <a name="supported-types-of-solutions"></a>Tipos de soluções suportados
A primeira coisa que quiser fazê-lo como um publicador é definir o tipo de solução da sua empresa é a oferta. O Marketplace suporta os seguintes tipos de ofertas:

|Tipo de solução|Máquina virtual|Modelo de solução|
|---|---|---|
|**Definição**|Imagens pré-configuradas com um sistema operativo completamente instalado e uma ou mais aplicações. Uma imagem de máquina virtual fornece as informações necessárias para criar e implementar máquinas virtuais no serviço de Virtual Machines do Azure.|Uma estrutura de dados que pode fazer referência a um ou mais serviços do Azure distintos, incluindo serviços publicados por outros sellers. Os subscritores do Azure podem utilizá-lo para implementar uma ou mais ofertas de forma única e coordenada.|
|**Exemplo**|Como um publicador do Azure, tiver criado e validar uma VM com um serviço de base de dados inovadoras. Outros subscritores do Azure pretendem obter e implementar esta VM para os seus ambientes de serviço em nuvem.|Como um publicador do Azure, tiver incluídas um conjunto de serviços a partir de através do Azure que o tornam rápido implementar serviços em nuvem com balanceamento de carga, a segurança avançada e elevada disponibilidade. Outros subscritores do Azure podem poupar tempo ao aquisição o modelo de solução que cumpra o respetivo objetivo. Não tiverem manualmente, localizar, obter, implementar e configurar os serviços do Azure da mesmos ou semelhantes.|

> [!NOTE]
> Alguns passos são partilhados entre os diferentes tipos de soluções e outros são diferentes para o respetivo tipo de solução. Este artigo fornece uma breve descrição geral sobre os passos que necessários para qualquer tipo de solução.

## <a name="publish-a-solution"></a>Publicar uma solução
![Para indicar, registar, publicar](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Para indicar a sua solução para pré-aprovação de
Para publicar uma máquina virtual [solução](https://createopportunity.azurewebsites.net) no Marketplace, concluir o certificado do Microsoft Azure **solução Nomination formulário**.

>[!NOTE]
> Se estiver a trabalhar com um Gestor de conta de parceiro ou um Gestor de parceiro para DX, peça-lhes para indicar a sua solução para o programa de certificados do Azure. Também pode ir para o [certificadas do Microsoft Azure](http://createopportunity.azurewebsites.net) página Web e preencher o formulário de aplicação. Introduza o e-mail da sua Gestor de conta de parceiro ou o Gestor de parceiro para DX no **Microsoft patrocinador contacte** caixa.

Caso cumpra os critérios de elegibilidade no [políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) e a aplicação for aprovada, podemos começar a trabalhar consigo para carregar a solução ao mercado.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registar a sua conta como um vendedor da Microsoft
Registar a sua conta Microsoft como um [conta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publicar a sua solução
Para publicar uma solução no Marketplace, siga estes passos:
1. Cumprir os requisitos nontechnical.

    a. Satisfazer a [pré-requisitos nontechnical](marketplace-publishing-pre-requisites.md).

    b. Satisfazer a [pré-requisitos técnicos VM](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Satisfazer a [requisitos técnicos do modelo de solução](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Crie a sua oferta.

    a. Criar um [máquina virtual](marketplace-publishing-vm-image-creation.md) oferecem.

    b. Criar um [modelo de solução](marketplace-publishing-solution-template-creation.md) oferecem.

3. Criar a sua oferta [conteúdo de marketing](marketplace-publishing-push-to-staging.md).

4. Teste a sua oferta no modo de transição.

    a. Testar a sua oferta VM na [transição](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testar a sua oferta do modelo de solução em [transição](marketplace-publishing-solution-template-test-in-staging.md).

5. Implementar a sua oferta para a [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Criar e gerir uma imagem de máquina virtual
Criar e gerir uma imagem de VM ao utilizar estes recursos:
* Criar uma imagem VM [no local](marketplace-publishing-vm-image-creation-on-premise.md).
* Criar uma máquina virtual em execução [Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Criar uma máquina virtual em execução [Linux no portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Resolver problemas comuns encontrados durante [criação do VHD](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Gerir a sua solução
Gerir a sua solução com ajuda partir os seguintes recursos:
* [Ler o guia de produção de pós-implementação para ofertas de máquina virtual](marketplace-publishing-vm-image-post-publishing.md)
* [Atualizar os detalhes nontechnical de uma oferta ou um SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Atualizar os detalhes técnicos de uma oferta ou um SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Adicionar um novo SKU sob uma oferta listada](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Alterar a contagem de discos de dados para um SKU listada](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Eliminar uma oferta listada no Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Eliminar um SKU listado no Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Eliminar a versão atual de um SKU listada no Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Reverter o preço de listagem para valores de produção](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Reverter o modelo de faturação para valores de produção](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Reverter a definição de visibilidade de um SKU listado para o valor de produção](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Recursos adicionais
[Configurar o Azure PowerShell](marketplace-publishing-powershell-setup.md)
