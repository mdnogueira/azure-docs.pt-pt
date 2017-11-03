---
title: "Auditoria e receber notificações sobre as ações importantes na sua subscrição do Azure | Microsoft Docs"
description: "Compreender o histórico de gestão de recursos, o estado de funcionamento do serviço e outra atividade de subscrição no registo de atividade, em seguida, utilizar um alerta de registo de atividade para receber uma notificação por e-mail quando é efetuada uma operação de elevada disponibilidade com privilégios na sua subscrição."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: 636dc0fcae1bc2647cd59add5957884971015ce2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="audit-and-receive-notifications-about-important-actions-in-your-azure-subscription"></a>Auditoria e receber notificações sobre as ações importantes na sua subscrição do Azure

O **registo de atividade do Azure** disponibiliza um histórico de eventos de nível de subscrição no Azure. Oferece informações sobre *quem* criados, atualizadas ou eliminadas *que* recursos e *quando* fizeram-lo. Pode criar um **alerta de registo de atividade** para receber notificações de correio eletrónico, SMS ou webhook quando ocorre a uma atividade que corresponde ao seu condições de alerta. Este passos de início rápido através da criação de um grupo de segurança de rede simples, o registo de atividade para compreender os eventos que ocorreram de navegação e, em seguida, criar um alerta de registo de atividade para se tornar notificado quando qualquer grupo de segurança de rede é criado reencaminhamentos.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **redes**, selecione **grupo de segurança de rede**.

3. Introduza "myNetworkSG" como o **nome** e criar um novo grupo de recursos com o nome **myResourceGroup**. Clique no botão **Criar**.

    ![Criar um grupo de segurança de rede no portal](./media/monitor-quick-audit-notify-action-in-subscription/create-network-security-group.png)

## <a name="browse-the-activity-log-in-the-portal"></a>Procurar o registo de atividade no portal

Agora foi adicionado um evento no registo de atividade que descreve a criação do grupo de segurança de rede. Utilize as instruções seguintes para identificar esse evento.

1. Clique em de **Monitor** encontrado na lista de navegação esquerdo do botão. Abre-se para a secção de registo de atividade. Esta secção contém um histórico de todas as ações que os utilizadores executaram recursos na sua subscrição, filtrável por várias propriedades, tais como o **grupo de recursos**, **Timespan**, e  **Categoria**.

2. No **registo de atividade** secção, clique em de **grupo de recursos** pendente e selecione **myResourceGroup**. Alterar o **Timespan** pendente para **última 1 hora**. Clique em **Aplicar**.

    ![Filtre o registo de atividade](./media/monitor-quick-audit-notify-action-in-subscription/browse-activity-log.png)

3. Clique em de **escrever NetworkSecurityGroups** eventos na tabela de eventos apresentados.

## <a name="browse-an-event-in-the-activity-log"></a>Procurar um evento no registo de atividade

A secção que aparece contém detalhes básicos da que foi efetuada, incluindo o nome, timestamp e o utilizador ou aplicação que efetuar esta operação.

![Ver evento resumo no registo de atividade](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-summary.png)

Clique em de **JSON** separador para ver os detalhes do evento completa. Isto inclui os detalhes de como o utilizador ou aplicação foi autorizada a efetuar a operação, a categoria de evento e nível e o estado da operação.

![Ver detalhes do evento no registo de atividade](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-json.png)

## <a name="create-an-activity-log-alert"></a>Criar um alerta de registo de atividade

1. Clique em de **resumo** separador para voltar para o resumo de eventos.

2. Na secção de resumo que é apresentado, clique em **Adicionar alerta de registo de atividade**.

    ![Criar um grupo de segurança de rede no portal](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-summary.png)

3. Na secção que aparece, dê o alerta de registo de atividade, um nome e descrição.

4. Em **critérios** Certifique-se de que **categoria de evento** está definido como **administração**, **tipo de recurso** está definido como **rede grupos de segurança**, **nome da operação** está definido como **Create ou grupo de atualização de segurança de rede**, **estado** está definido como  **Foi concluída com êxito** e todos os outros campos de critérios são em branco ou defina para **todos os**. Os critérios definem as regras utilizadas para determinar se o alerta deve ser activado quando for apresentado um novo evento no registo de atividade.

    ![Criar um grupo de segurança de rede no portal](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-alert-criteria.png)

5. Em **alerta através de** selecione **novo** ação grupo e fornecer um **nome** e **nome abreviado** para o grupo de ação. O grupo de ação define o conjunto de ações executadas quando o alerta é ativado (quando os critérios de corresponder ao novo evento).

6. Em **ações** adicione 1 ou mais ações, fornecendo um **nome** para a ação, o **tipo de ação** (por exemplo, e-mail ou um SMS), e **detalhes**para esse tipo de ação específica (por exemplo, um URL do webhook, o endereço de e-mail ou número SMS).

    ![Criar um grupo de segurança de rede no portal](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-alert-actions.png)

7. Clique em **Ok** para guardar o alerta de registo de atividade.

## <a name="test-the-activity-log-alert"></a>Testar o alerta de registo de atividade

> [!NOTE]
> Demora cerca de 10 minutos para que um alerta de registo de atividade completamente tornar-se ativado. Novos eventos que ocorrem antes do alerta de registo de atividade completamente está ativado não geram notificações.
>
>

Para testar o alerta, repetir a secção anterior para **criar um grupo de segurança de rede**, mas dar um nome diferente este grupo de segurança de rede e reutilizar o grupo de recursos existente. Dentro de alguns minutos, irá receber uma notificação que o grupo de segurança de rede foi criado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, elimine o grupo de recursos e o grupo de segurança de rede. Para tal, escreva o nome do grupo de recursos que criou na caixa de pesquisa na parte superior do portal e clique no nome do grupo de recursos. Na secção que é apresentada, clique o **eliminar grupo de recursos** botão, escreva o nome do grupo de recursos e clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Este início rápido, pode efetuar uma operação para gerar um evento de registo de atividade e, em seguida, criar um alerta de registo de atividade para ser notificado quando esta operação ocorre novamente no futuro. Em seguida, testar o alerta efetuando essa operação de novo. Azure torna disponíveis eventos de registo de atividade dos últimos 90 dias. Se precisar de reter os eventos mais de 90 dias, tente arquivar os dados de registo de atividade juntamente com os outros dados de monitorização.

> [!div class="nextstepaction"]
> [Arquivamento dos dados de monitorização](./monitor-tutorial-archive-monitoring-data.md)
