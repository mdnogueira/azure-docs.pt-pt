---
title: "Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços em nuvem do Azure | Microsoft Docs"
description: "Como configurar a aplicação do serviço em nuvem do azure para permitir ligações de ambiente de trabalho remotas"
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: b9ae4442f57170746eb0de94849b09625be51264
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços em nuvem do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Ambiente de trabalho remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a sua aplicação enquanto estiver em execução.

Pode ativar uma ligação de ambiente de trabalho remoto na sua função durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na sua definição de serviço ou pode optar por ativar o ambiente de trabalho remoto através da extensão de ambiente de trabalho remoto. A abordagem preferida consiste em utilizar a extensão de ambiente de trabalho remoto, pode ativar o ambiente de trabalho remoto, mesmo depois da aplicação for implementada sem ter de voltar a implementar a aplicação.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurar o ambiente de trabalho remoto a partir do portal do Azure
O portal do Azure utiliza a abordagem de extensão de ambiente de trabalho remoto, pelo que pode ativar o ambiente de trabalho remoto, mesmo depois da aplicação é implementada. O **ambiente de trabalho remoto** painel do serviço em nuvem permite-lhe ativar o ambiente de trabalho remoto, alterar a conta de administrador local utilizada para ligar às máquinas virtuais, o certificado utilizado na autenticação e definir a data de expiração.

1. Clique em **serviços em nuvem**, clique no nome do serviço de nuvem e, em seguida, clique em **ambiente de trabalho remoto**.

    ![Ambiente de trabalho remoto do serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Escolha se pretende ativar o ambiente de trabalho remoto para uma função individuais ou para todas as funções, em seguida, altere o valor do alternador a **ativado**.

3. Preencha os campos obrigatórios para nome de utilizador, palavra-passe, expiração e certificado.

    ![Ambiente de trabalho remoto do serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando ativar o ambiente de trabalho remoto e clique em OK (marca de verificação) pela primeira vez. Para impedir que um reinício, o certificado utilizado para encriptar a palavra-passe tem de estar instalado na função. Para impedir que um reinício, [carregar um certificado para o serviço em nuvem](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e, em seguida, regresse a esta caixa de diálogo.
   >
   >
3. No **funções**, selecione a função que pretende atualizar ou selecione **todos os** para todas as funções.

4. Quando concluir as atualizações de configuração, clique em **guardar**. Irá demorar algum tempo antes das instâncias da função estão prontas para receber as ligações.

## <a name="remote-into-role-instances"></a>Remoto em instâncias de função
Depois de ambiente de trabalho remoto está ativado nas funções, pode iniciar uma ligação diretamente a partir do Portal do Azure:

1. Clique em **instâncias** para abrir o **instâncias** painel.
2. Selecione uma instância de função que tenha o ambiente de trabalho remoto configuradas.
3. Clique em **Connect** para transferir um ficheiro RDP para a instância de função.

    ![Ambiente de trabalho remoto do serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Clique em **abra** e, em seguida, **Connect** para iniciar a ligação de ambiente de trabalho remoto.

>[!NOTE]
> Se estiver junto do seu serviço em nuvem atrás de um NSG, poderá ter de criar regras que permitam o tráfego de portas **3389** e **20000**.  Ambiente de trabalho remoto utiliza a porta **3389**.  Instâncias de serviço de nuvem são carga balanceada, pelo diretamente não é possível controlar que instância para ligar a.  O *RemoteForwarder* e *RemoteAccess* agentes gerir o tráfego RDP e permitir que o cliente enviar um cookie RDP e especifique uma instância individual para ligar a.  O *RemoteForwarder* e *RemoteAccess* agentes requerem essa porta **20000*** ser aberto, que pode ser bloqueado se tiver um NSG.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar os serviços em nuvem](cloud-services-how-to-configure-portal.md)
[Cloud services FAQ – ambiente de trabalho remoto](cloud-services-faq.md)
