---
title: "Ativar o acesso à aplicação de contentor do Azure DC/SO | Microsoft Docs"
description: "Como ativar o acesso público ao contentores de DC/SO no serviço de contentor do Azure."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microserviços, Mesos, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: c9ef5913859cf3a55a2de2107a9304f1d28a4829
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Ativar o acesso público para uma aplicação de serviço de contentor do Azure
Qualquer contentor de DC/OS em ACS [agrupamento de agentes públicos](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automaticamente está exposto à internet. Por predefinição, as portas **80**, **443**, **8080** estão abertas e qualquer contentor (público) está a escutar as portas estão acessíveis. Este artigo mostra como abrir portas mais para as suas aplicações no serviço de contentor do Azure.

## <a name="open-a-port-portal"></a>Abrir uma porta (portal)
Em primeiro lugar, é necessário abrir a porta que queremos.

1. Inicie sessão no portal.
2. Localize o grupo de recursos que implementou o serviço de contentor do Azure.
3. Selecione o Balanceador de carga do agente (que é denominado semelhante **XXXX-agente-lb-XXXX**).
   
    ![Balanceador de carga do serviço de contentor do Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Clique em **sondas** e, em seguida, **adicionar**.
   
    ![Pesquisas de Balanceador de carga do serviço de contentor do Azure](./media/container-service-enable-public-access/add-probe.png)
5. Preencha o formulário de pesquisa e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo da sonda. |
   | Porta |A porta do contentor para testar. |
   | Caminho |(Quando no modo HTTP) O caminho relativo Web site para pesquisa. HTTPS não suportado. |
   | intervalo |A quantidade de tempo entre a sonda tenta, em segundos. |
   | Limiar de mau estado de funcionamento |Número de sonda consecutivas tentativas antes de considerar o contentor de mau estado de funcionamento. |
6. Novamente das propriedades do Balanceador de carga do agente, clique em **as regras de balanceamento de carga** e, em seguida, **adicionar**.
   
    ![Regras de Balanceador de carga de serviço de contentor do Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Preencha o formulário de Balanceador de carga e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo do Balanceador de carga. |
   | Porta |A porta de entrada pública. |
   | Porta de back-end |A porta pública interno do contentor para encaminhar o tráfego para. |
   | Conjunto back-end |Os contentores neste conjunto serão o destino para este Balanceador de carga. |
   | Sonda |A pesquisa utilizada para determinar se um destino no **conjunto back-end** está em bom estado. |
   | Persistência da sessão |Determina a forma como o tráfego de um cliente deve ser processado durante a sessão.<br><br>**Nenhum**: os pedidos sucessivos do mesmo cliente podem ser processados por qualquer contentor.<br>**Cliente IP**: os pedidos sucessivos do mesmo IP de cliente são processados pelo mesmo contentor.<br>**Cliente IP e protocolo**: os pedidos sucessivos da mesma combinação de IP e protocolo de cliente são processados pelo mesmo contentor. |
   | Tempo limite de inatividade |(Apenas TCP) Em minutos, o tempo para manter um cliente TCP/HTTP abrir sem depender *ligação keep-alive* mensagens. |

## <a name="add-a-security-rule-portal"></a>Adicionar uma regra de segurança (portal)
Em seguida, precisamos de adicionar uma regra de segurança que encaminha o tráfego da nossa porta aberta através da firewall.

1. Inicie sessão no portal.
2. Localize o grupo de recursos que implementou o serviço de contentor do Azure.
3. Selecione o **pública** grupo de segurança de rede do agente (que é denominado semelhante **XXXX-agente-público-nsg-XXXX**).
   
    ![Grupo de segurança de rede de serviço de contentor do Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Selecione **regras de segurança de entrada** e, em seguida, **adicionar**.
   
    ![Regras de grupo de segurança de rede de serviço de contentor do Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Preencher a regra de firewall para permitir que a porta pública e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo da regra de firewall. |
   | Prioridade |Classificação de prioridade da regra. O o número maior de prioridade inferior. |
   | Origem |Restringir o intervalo de endereços IP entrado ser permitido ou negado por esta regra. Utilize **qualquer** não especificar uma restrição. |
   | Serviço |Selecione um conjunto de serviços predefinidos que destina-se a esta regra de segurança. Caso contrário, utilize **personalizada** para criar os seus próprios. |
   | Protocolo |Restringir o tráfego com base no **TCP** ou **UDP**. Utilize **qualquer** não especificar uma restrição. |
   | Intervalo de portas |Quando **serviço** é **personalizada**, especifica o intervalo de portas de que esta regra afeta. Pode utilizar uma porta única, tal como **80**, ou como um intervalo **1024-1500**. |
   | Ação |Permitir ou negar o tráfego que satisfaça os critérios. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a diferença entre [públicos e privados agentes DC/OS](container-service-dcos-agents.md).

Leia mais informações sobre [gerir os contentores de DC/SO](container-service-mesos-marathon-ui.md).

