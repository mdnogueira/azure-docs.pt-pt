---
title: "Como configurar um serviço em nuvem (portal) | Microsoft Docs"
description: "Saiba como configurar os serviços em nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar o acesso remoto para instâncias de função. Estes exemplos utilizam o portal do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: 409b3bb26648ef1b811dfaaf37690c8220046729
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-configure-cloud-services"></a>Como configurar os serviços Cloud
Pode configurar as definições utilizadas com mais frequência para um serviço em nuvem no portal do Azure. Ou, se pretender atualizar diretamente os seus ficheiros de configuração, transfira um ficheiro de configuração de serviços para atualizar e, em seguida, carregue o ficheiro atualizado e atualize o serviço cloud com as alterações de configuração. De uma forma ou de outra, as atualizações de configuração são integradas em todas as instâncias de funções.

Também pode gerir as instâncias das suas funções de serviço na nuvem ou ambiente de trabalho remoto em-los.

Azure apenas pode garantir uma disponibilidade do serviço de percentagem de 99,95 durante as atualizações de configuração se tiver, pelo menos, duas instâncias de função para cada função. Permite que uma máquina virtual processar pedidos de cliente, enquanto o outro está a ser atualizado. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço em nuvem
Depois de abrir o [portal do Azure](https://portal.azure.com/), navegue até ao seu serviço em nuvem. Aqui, gerir vários aspetos do mesmo.

![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)

O **definições** ou **todas as definições** ligações abre-se a **definições** painel onde pode alterar o **propriedades**, altere o  **Configuração**, gerir o **certificados**, configuração **regras de alerta**e gerir o **utilizadores** quem tem acesso a este serviço em nuvem.

![Painel de definições de serviço em nuvem do Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerir a versão de SO convidado

Por predefinição, o Azure atualiza periodicamente o SO convidado para a imagem suportado mais recentes dentro da família de SO especificadas na sua configuração de serviço (. cscfg), como o Windows Server 2016.

Se precisar de uma versão de SO específica de destino, pode defini-lo no **configuração** painel.

![Definir a versão de SO](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)


>[!IMPORTANT]
> Escolher que uma versão de SO específica desativa SO automático de atualizações e faz a responsabilidade de aplicação de patches. Tem de garantir que as instâncias da função estão a receber atualizações ou pode expor a aplicação vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitorização
Pode adicionar alertas ao seu serviço em nuvem. Clique em **definições** > **regras de alerta** > **Adicionar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Aqui, pode configurar um alerta. Com o **métrica** pendente caixa, pode configurar um alerta para os seguintes tipos de dados.

* Lidos de disco
* Escrita de disco
* Rede no
* Limite de rede
* Percentagem de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar a monitorização a partir de um mosaico métrico
Em vez de utilizar **definições** > **regras de alerta**, pode clicar dos mosaicos métricos no **monitorização** secção o **serviço em nuvem**  painel.

![Monitorização do serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Aqui pode personalizar o gráfico utilizado com o mosaico ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reiniciar o computador, a recriação de imagem ou o ambiente de trabalho remoto
Pode configurar o ambiente de trabalho remoto através de [portal do Azure (ambiente de trabalho remoto do programa de configuração)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), ou através de [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Para reiniciar o computador, recriação de imagem ou remoto para um serviço em nuvem, clique na instância do serviço de nuvem.

![Instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

No painel que abre pode iniciar uma ligação de ambiente de trabalho remota, remotamente, reiniciar a instância ou remotamente recriar (começar com uma nova imagem) a instância.

![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigure o. cscfg
Poderá ter de reconfigurar o serviço em nuvem através de [a configuração do serviço (. cscfg)](cloud-services-model-and-package.md#cscfg) ficheiro. Primeiro tem de transferir o ficheiro. cscfg, modificá-lo, em seguida, carregá-la.

1. Clique no **definições** ícone ou **todas as definições** ligação para abrir o **definições** painel.

    ![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Clique em de **configuração** item.

    ![Painel de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Clique em de **transferir** botão.

    ![Transferência](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Depois de atualizar o ficheiro de configuração de serviço, carregar e aplicar as atualizações de configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecione o ficheiro. cscfg e clique em **OK**.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [implementar um serviço em nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).
