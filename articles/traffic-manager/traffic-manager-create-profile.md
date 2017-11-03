---
title: Criar um perfil do Traffic Manager no Azure | Microsoft Docs
description: Este artigo descreve como criar um perfil do Traffic Manager
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: e9ff7947e7801a9f352a7a947b09893b8f615d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Este artigo descreve como um perfil com **prioridade** tipo de encaminhamento pode ser criado para utilizadores de rota para dois pontos finais de Web Apps do Azure. Utilizando o **prioridade** encaminhamento tipo, todo o tráfego é encaminhado para o primeiro ponto final enquanto o segundo é guardado como uma cópia de segurança. Como resultado, os utilizadores podem ser encaminhados para o segundo ponto final se o primeiro ponto final fica em mau estado de funcionamento.

Neste artigo, dois pontos finais de aplicação Web do Azure criados anteriormente associadas a este recentemente criado o perfil do Traffic Manager. Para mais informações sobre como criar pontos finais de aplicação Web do Azure, visite o [página de documentação de Web Apps do Azure](https://docs.microsoft.com/azure/app-service/). Pode adicionar qualquer ponto final que tem um nome DNS e é acessível através da internet pública e que estamos a utilizar pontos finais de Web Apps do Azure como um exemplo.

### <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego
1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com). Se ainda não tiver uma conta, pode inscrição para uma [um mês avaliação gratuita](https://azure.microsoft.com/free/). 
2. No menu **Hub**, clique em **Novo** > **Rede** > **Ver todos**, clique no perfil do **Gestor de Tráfego** para abrir o painel **Criar Perfil do Gestor de Tráfego** e clique em **Criar**.
3. No painel **Criar Perfil do Gestor de Tráfego**, preencha com o seguinte:
    1. Em **Nome**, indique um nome para o perfil. Este nome tem de ser exclusivos dentro da zona de trafficmanager.net e resulta no nome do DNS <name>, trafficmanager.net que é utilizado para aceder ao perfil do Traffic Manager.
    2. Em **Método de encaminhamento**, selecione o método de encaminhamento **Prioridade**.
    3. Em **Subscrição**, selecione a subscrição na qual quer criar este perfil.
    4. Em **Grupo de Recursos**, crie um grupo de recursos novo onde colocar este perfil.
    5. Em **Localização do grupo de recursos**, selecione a localização do grupo de recursos. Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.
    6. Clique em **Criar**.
    7. Quando a implementação global do perfil do Gestor de Tráfego estiver concluída, é apresentada no respetivo grupo de recursos como um dos recursos.

    ![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de tráfego

1. Na barra de pesquisa do portal, procure o **perfil do Traffic Manager** nome que criou no precedente secção e clique no perfil do Gestor de tráfego nos resultados de que o apresentados.
2. No painel **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais**.
3. No painel **Pontos Finais** apresentado, clique em **Adicionar**.
4. No painel **Adicionar ponto final**, faça o seguinte:
    1. Em **Tipo**, clique em **Ponto final do Azure**.
    2. Indique um **Nome** pelo qual pretende reconhecer este ponto final.
    3. Para **tipo de recurso de destino**, clique em **do serviço de aplicações**.
    4. Para **recurso de destino**, clique em **escolher um serviço de aplicações** para mostrar a listagem das aplicações Web na mesma subscrição. No **recursos** painel que é apresentado, escolha o serviço de aplicações que pretende adicionar como o primeiro ponto final.
    5. Em **Prioridade**, selecione **1**. Se estiver em bom estado de funcionamento, todo o tráfego vai para este ponto final.
    6. Mantenha a caixa **Adicionar como desativado** desmarcada.
    7. Clique em **OK**
5.  Repita os passos 3 e 4 para o próximo ponto final de Web Apps do Azure. Adicione-o com o valor de **Prioridade** definido em **2**.
6.  Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados no painel **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

    ![Adicionar um ponto final do Gestor de tráfego](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Utilizar o perfil do Gestor de tráfego
1.  Na barra de pesquisa do portal, procure o **perfil do Traffic Manager** nome que criou na secção anterior. Nos resultados que são apresentados, clique o perfil do Gestor de tráfego.
2. No **perfil do Traffic Manager** painel, clique em **descrição geral**.
3. O **perfil do Traffic Manager** painel apresenta o nome DNS do perfil do Traffic Manager recentemente criado. Isto pode ser utilizado por quaisquer clientes (por exemplo, navegando para a mesma utilizando um browser) obter encaminhados para o ponto final à direita como determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o primeiro ponto final e se o Gestor de tráfego detetar-ser mau estado de funcionamento, o tráfego automaticamente a ativação pós-falha para o próximo ponto final.

## <a name="delete-the-traffic-manager-profile"></a>Eliminar o perfil do Gestor de tráfego
Quando já não é necessário, elimine o grupo de recursos e o perfil do Gestor de tráfego que criou. Para tal, selecione o grupo de recursos do **perfil do Traffic Manager** painel e clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [tipos de encaminhamento](traffic-manager-routing-methods.md).
- Saiba mais sobre os tipos de ponto final [tipos de ponto final](traffic-manager-endpoint-types.md).
- Saiba mais sobre [monitorização de pontos finais](traffic-manager-monitoring.md).



