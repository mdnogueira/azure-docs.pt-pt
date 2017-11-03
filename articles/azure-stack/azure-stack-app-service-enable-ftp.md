---
title: Ativar o FTP no App Service na pilha do Azure | Microsoft Docs
description: Passos para concluir para ativar o FTP no App Service na pilha do Azure
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Ativar o FTP no Serviço de Aplicações no Azure Stack

Assim que tiver implementado com êxito do serviço de aplicações na pilha do Azure se pretender ativar a publicação de FTP, para que os inquilinos podem carregar os respetivos ficheiros de aplicação e o conteúdo, existem alguns passos adicionais que têm de ser concluídos.  Em versões futuras estes passos irão ser automatizados.

> [!NOTE]
> Estes passos são para configuração de um serviço de aplicações no fornecedor de recursos do Azure pilha administradores da empresa ou serviço.

## <a name="enable-ftp"></a>Ativar o FTP

1.  Inicie sessão no portal do Azure pilha como o administrador de serviço.
2.  Navegue até à **interfaces de rede** e selecione o **FTP NIC** em **grupo de recursos** - **AppService LOCAL**. ![Interfaces de rede de pilha do Azure][1]
3.  Tenha em atenção o **endereço IP público** do **FTP NIC**. 
![Detalhes de Interface de rede de pilha do Azure][2]
4.  Em seguida, navegue para **máquinas virtuais** e selecione o **FTP0 VM**. ![Máquinas virtuais de pilha do Azure][3]
5.  Abra uma sessão de ambiente de trabalho remoto para a VM utilizando o **Connect** botão e início de sessão para a sessão utilizando as credenciais de administrador que definiu durante a implementação do serviço de aplicações.  
![Detalhes da Máquina Virtual de pilha do Azure][4]
6.  Abra **Gestor de serviços (IIS) da informação de Internet** na VM FTP (FTP0-VM).
7.  Em **Sites** selecione **que aloja o Site de FTP**.
8.  Abra **suporte de Firewall de FTP**. ![Gestor do IIS no serviço de aplicações FTP0-VM][5]
9.  Introduza o endereço IP público do FTP NIC e clique em **aplicar** ![suporte de Firewall de FTP de Gestor de IIS][6]

## <a name="validate-the-enabling-of-ftp"></a>Validar a ativação de FTP

1.  Inicie sessão no portal do Azure pilha como o administrador de serviço ou como um inquilino.
2.  Navegue até à **serviços aplicacionais** e selecione um Web, móveis ou aplicação API que criou. ![Serviços Aplicacionais][7]
3.  Na aplicação detalhes nota o **FTP Hostname** e **nome de utilizador FTP/implementação**. ![Detalhes da aplicação do serviço de aplicações][8]
> [!NOTE]
> Se não vir uma entrada em **nome de utilizador FTP/implementação**, tem de definir a implementação credenciais primeiro utilizando o **as credenciais de implementação** painel.

4.  Abra o Explorador do Windows, introduza o nome de anfitrião do FTP para o endereço de ficheiro barra ftp://ftp.appservice.azurestack.local por exemplo,
5.  Quando lhe for pedido introduza o **as credenciais de implementação** que anotou no passo 3, se tiver sido ativada a funcionalidade, verá uma listagem de diretórios de conteúdo a aplicação de serviço de aplicações. ![Ficheiro de FTP listagem][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
