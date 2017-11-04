---
title: Utilizar imagens de cliente do Windows no Azure | Microsoft Docs
description: "Como utilizar os benefícios de subscrição de Visual Studio para implementar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.openlocfilehash: 207a6562965b4913416bd4dbf3eb132b42938dc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Utilizar o cliente do Windows no Azure para cenários de desenvolvimento/teste
Pode utilizar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste fornecida que tem uma subscrição do Visual Studio (anteriormente MSDN) adequada. Este artigo descreve os requisitos de elegibilidade para o cliente Windows em execução no Azure e a utilização das imagens da galeria do Azure.

## <a name="subscription-eligibility"></a>Elegibilidade de subscrição
Subscritores ativos do Visual Studio (pessoas que tem adquirido uma licença de subscrição do Visual Studio) podem utilizar o cliente do Windows para desenvolvimento e testes. Cliente Windows pode ser utilizado nas suas próprias hardware e as máquinas virtuais do Azure em execução em qualquer tipo de subscrição do Azure. Cliente Windows não pode ser implementada ou utilizada no Azure para utilização em produção normal ou utilizada por pessoas que não sejam subscritores ativos do Visual Studio.

Para sua comodidade, efetuamos determinadas imagens do Windows 10 disponíveis na galeria do Azure dentro [elegível dev/teste oferece](#eligible-offers). Visual Studio os subscritores dentro de qualquer tipo de oferta também podem [adequadamente preparar e criar](prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e, em seguida, [carregar para o Azure](upload-generalized-managed.md). A utilização continua a ser limitada para dev/teste por subscritores ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas elegíveis
A tabela seguinte fornece detalhes sobre a oferta IDs que são elegíveis para implementar o Windows 10 através da galeria do Azure. As imagens do Windows 10 só estão visíveis para as seguintes ofertas. Visual Studio subscritores tem de executar o cliente Windows de um tipo de oferta diferentes exigem que [adequadamente preparar e criar](prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e [, em seguida, carregar para o Azure](upload-generalized-managed.md).

| Nome da Oferta | Número da oferta | Imagens de cliente disponíveis |
|:--- |:---:|:---:|
| [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Subscritores do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Subscritores do Visual Studio Professional de teste](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subscritores do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise programador/teste](https://azure.microsoft.com/ofers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verifique a sua subscrição do Azure
Se não souber o ID de oferta, pode obtê-lo através do portal do Azure de uma das seguintes duas formas:  

- No painel "Subscrições":

  ![Detalhes de ID de oferta do portal do Azure](./media/client-images/offer-id-azure-portal.png) 

- Ou, clique em **faturação** e, em seguida, clique em seu ID de subscrição. O ID de oferta é apresentada no painel de faturação.

Também pode ver o ID de oferta do [separador "Subscrições"](http://account.windowsazure.com/Subscriptions) do portal de conta do Azure:

![Detalhes de ID de oferta do portal de contas do Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passos seguintes
Agora pode implementar as suas VMs utilizando [PowerShell](quick-create-powershell.md), [modelos do Resource Manager](ps-template.md), ou [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

