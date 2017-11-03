---
title: "Testar a implementação de um serviço em nuvem (Node.js) | Microsoft Docs"
description: "Saiba como implementar a aplicação do Azure para um ambiente de teste, em seguida, implementar um ambiente de produção com a troca de IP Virtual (VIP)."
services: cloud-services
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d65d26a6-b424-49cd-a88c-7ef46bb112a8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tarcher
ms.openlocfilehash: b3000ed769e8c60eccb21e26f53ce7ccb7e68d7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="staging-an-application-in-azure"></a>Testar uma aplicação no Azure
Uma aplicação em pacote pode ser implementada para o ambiente de teste do Azure para ser testada antes de prosseguir para o ambiente de produção em que a aplicação está acessível na Internet. O ambiente de teste é exatamente como o ambiente de produção, exceto que só pode aceder a aplicação testada com um URL oculto que é gerado pelo Azure. Depois de ter verificado que a aplicação está a funcionar corretamente, pode ser implementado no ambiente de produção, efetuando uma troca de IP Virtual (VIP).

> [!NOTE]
> Os passos neste artigo aplicam-se apenas às aplicações de nó alojadas como um serviço em nuvem do Azure.
> 
> 

## <a name="step-1-stage-an-application"></a>Passo 1: Testar uma aplicação
Esta tarefa abrange como pré-configurar uma aplicação utilizando o **do Microsoft Azure PowerShell**.

1. Quando um serviço de publicação, basta passar o **-ranhura** parâmetro para o **Publish-AzureServiceProject** cmdlet.
   
   ```powershell
   Publish-AzureServiceProject -Slot staging
   ```
2. Inicie sessão no [portal clássico do Azure] e selecione **serviços em nuvem**. Depois da nuvem é criado um serviço e o **transição** Estado coluna foi atualizado para **executar**, clique no nome do serviço.
   
   ![Portal de apresentar um serviço em execução][cloud-service]
3. Selecione o **Dashboard**e, em seguida, selecione **transição**.
   
   ![dashboard de serviço de nuvem][cloud-service-dashboard]
4. Tenha em atenção o valor de **URL do Site** entrada à direita. O nome DNS é um ID interno oculto que gerou do Azure.
   
    ![url do site][cloud-service-staging-url]

Agora pode verificar se a aplicação está a funcionar corretamente no ambiente de teste utilizando o URL do site de teste.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Passo 2: Atualizar uma aplicação em produção ao trocar VIPs
Depois de ter verificado a versão atualizada de uma aplicação no ambiente de teste, pode rapidamente tornar disponível em produção através da troca de IPs virtuais (VIP) de ambientes de teste e produção.

> [!NOTE]
> Este passo parte do princípio de que já tem implementada uma aplicação para produção e testado a versão atualizada da aplicação.
> 
> 

1. Inicie sessão no [portal clássico do Azure], clique em **serviços em nuvem** e, em seguida, selecione o nome do serviço.
2. Do **Dashboard**, selecione **transição**e, em seguida, clique em **comutação** na parte inferior da página. Esta ação abre a caixa de diálogo de alternância de VIP.
   
   ![caixa de diálogo de alternância de VIP][vip-swap-dialog]
3. Reveja as informações e, em seguida, clique em **OK**. As duas implementações começam a atualizar a implementação de teste muda para produção e a implementação de produção muda para o teste.

Testada uma implementação com êxito e atualizada uma implementação de produção pelo trocar VIPs com a implementação no modo de transição.

## <a name="additional-resources"></a>Recursos Adicionais
* [Como implementar uma atualização de serviço para produção por trocar VIPs no Azure]

[portal clássico do Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Como implementar uma atualização de serviço para produção por trocar VIPs no Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
