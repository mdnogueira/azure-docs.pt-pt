---
title: "Monitorizar com a análise de registos do Azure de Surface Hubs | Microsoft Docs"
description: "Utilize a solução de Surface Hub para controlar o estado de funcionamento do seu Surface Hubs e compreender como estão a ser utilizados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6ecd0d09589fec85c1633f528afc1165c346b7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorizar Surface Hubs com a análise de registos para controlar o respetivo estado de funcionamento

![Símbolo de Hub de superfície](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Este artigo descreve como pode utilizar a solução de Surface Hub na análise de registos para monitorizar Microsoft Surface Hub dispositivos com o Microsoft Operations Management Suite (OMS). Análise de registos ajuda-o a controlar o estado de funcionamento do seu Surface Hubs, bem como a compreender como estão a ser utilizados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalada. O através do agente que pode enviar dados a partir do seu Surface Hub OMS. Ficheiros de registo são lidos a partir do seu Surface Hubs e estão, em seguida, são enviados para o serviço do OMS. Problemas, como servidores estar offline, o calendário a sincronização não, ou se a conta de dispositivo não é possível iniciar sessão no Skype são apresentadas na OMS no dashboard do Surface Hub. Ao utilizar os dados no dashboard, pode identificar dispositivos que não estão em execução, ou que estão a ter outros problemas e potencialmente aplicar correções para os problemas detetados.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução. Para gerir o Surface Hubs do Microsoft Operations Management Suite (OMS), irá precisar do seguinte:

* Uma subscrição válida para [OMS](http://www.microsoft.com/oms).
* Um [subscrição OMS](https://azure.microsoft.com/pricing/details/log-analytics/) nível que irão suportar o número de dispositivos que pretende monitorizar. Preços do OMS variam consoante o número de dispositivos inscritos e a quantidade de dados-processos. Poderá ser útil tem isto em consideração ao planear a sua implementação Surface Hub.

Em seguida, irá adicionar uma subscrição do OMS à sua subscrição do Microsoft Azure existente ou criar uma nova área de trabalho diretamente através do portal do OMS. Instruções detalhadas para utilizando um dos métodos é [introdução à análise de registos](log-analytics-get-started.md). Depois de configurada a subscrição do OMS, existem duas formas de inscrever os dispositivos Surface Hub:

* Automaticamente através do Intune
* Manualmente através de **definições** no seu dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configurar a monitorização
Pode monitorizar o estado de funcionamento e a atividade do seu Hub a superfície de através da análise do registo no OMS. Pode inscrever o Surface Hub no OMS através do Intune ou localmente utilizando **definições** no Surface Hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Ligar de Surface Hubs ao OMS através do Intune
Terá do ID da área de trabalho e a chave de área de trabalho para a área de trabalho do OMS que irão gerir a sua Surface Hubs. Pode obter os partir do portal do OMS.

O Intune é um produto Microsoft que lhe permite gerir centralmente as definições de configuração do OMS que são aplicadas a um ou mais dos seus dispositivos. Siga estes passos para configurar os seus dispositivos através do Intune:

1. Iniciar sessão no Intune.
2. Navegue para **definições** > **ligado origens**.
3. Criar ou editar uma política baseada no modelo de Surface Hub.
4. Navegue para a secção do OMS (informações operacionais do Azure) da política e adicione o *ID da área de trabalho* e *chave da área de trabalho* para a política.
5. Guarde a política.
6. Associe a política de grupo adequado de dispositivos.

   ![Política do Intune](./media/log-analytics-surface-hubs/intune.png)

Intune, em seguida, sincroniza-se as definições do OMS com os dispositivos no grupo de destino, inscrevendo-os na sua área de trabalho do OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Ligar Surface Hubs ao utilizar a aplicação de definições do OMS
Terá do ID da área de trabalho e a chave de área de trabalho para a área de trabalho do OMS que irão gerir a sua Surface Hubs. Pode obter os partir do portal do OMS.

Se não utilizar o Intune para gerir o ambiente, pode inscrever dispositivos manualmente através de **definições** em cada Surface Hub:

1. A partir do seu Surface Hub, abra **definições**.
2. Introduza as credenciais de administrador do dispositivo quando lhe for pedido.
3. Clique em **este dispositivo**e em **monitorização**, clique em **configurar definições do OMS**.
4. Selecione **Ativar monitorização**.
5. Na caixa de diálogo de definições de OMS, escreva o **ID da área de trabalho** e escreva o **chave da área de trabalho**.  
   ![definições](./media/log-analytics-surface-hubs/settings.png)
6. Clique em **OK** para concluir a configuração.

Uma confirmação é apresentado a informar se pretende ou não a configuração do OMS foi aplicada com êxito para o dispositivo. Se tiver sido, é apresentada uma mensagem a indicar que o agente ligado com êxito para o serviço do OMS. O dispositivo, em seguida, começa a enviar dados para o OMS, onde pode ver e atuar no mesmo.

## <a name="monitor-surface-hubs"></a>Monitor de Surface Hubs
Monitorização do seu Surface Hubs utilizar OMS é muito como monitorizar todos os outros dispositivos inscritos.

1. Inicie sessão no portal do OMS.
2. Navegue para o dashboard de pacote de solução de Surface Hub.
3. É apresentado o estado de funcionamento do seu dispositivo.

   ![Dashboard de Hub de superfície](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Pode criar [alertas](log-analytics-alerts.md) com base na procura de registo existentes ou personalizados. Utilizar os dados que do OMS recolhe do seu Surface Hubs, pode procurar problemas e alerta nas condições que definem para os seus dispositivos.

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver os dados detalhados de Surface Hub.
* Criar [alertas](log-analytics-alerts.md) para ser notificado quando ocorrem problemas com o Surface Hubs.
