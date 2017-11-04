---
title: "Configurar o método de encaminhamento de tráfego de prioridade utilizando o Gestor de tráfego do Azure | Microsoft Docs"
description: "Este artigo explica como configurar o método de encaminhamento de tráfego de prioridade no Gestor de tráfego"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Configurar o método de encaminhamento de tráfego de prioridade no Gestor de tráfego

Independentemente do modo do site, Web sites do Azure já fornece a funcionalidade de ativação pós-falha para os Web sites num centro de dados (também conhecido como região). Gestor de tráfego fornece ativação pós-falha para Web sites em datacenters diferentes.

É um padrão comum para ativação pós-falha do serviço enviar o tráfego para um serviço principal e fornecem um conjunto de serviços de cópia de segurança idênticos para ativação pós-falha. Os passos seguintes explicam como configurar esta ativação pós-falha prioritários com Web sites e serviços em nuvem do Azure:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método de encaminhamento de tráfego de prioridade

1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure o **perfis do Traffic Manager** e, em seguida, clique no nome de perfil que pretende configurar o método de encaminhamento.
3. No **perfil do Traffic Manager** painel, certifique-se de que os serviços em nuvem e de Web sites que pretende incluir na sua configuração estão presentes.
4. No **definições** secção, clique em **configuração**e, no **configuração** painel, concluída, da seguinte forma:
    1. Para **definições do método de encaminhamento de tráfego**, certifique-se de que o método de encaminhamento de tráfego **prioridade**. Se não for, clique em **prioridade** na lista pendente.
    2. Definir o **definições de Endpoint Protection do monitor** idêntica para todas as cada ponto final dentro deste perfil da seguinte forma:
        1. Selecione as adequadas **protocolo**e especifique o **porta** número. 
        2. Para **caminho** escreva uma barra  */* . Monitorizar pontos finais, tem de especificar um caminho e nome de ficheiro. A barra de reencaminhar "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório de raiz (predefinição).
        3. Na parte superior da página, clique em **guardar**.
5. No **definições** secção, clique em **pontos finais**.
6. No **pontos finais** painel, reveja a ordem de prioridade para os pontos finais. Quando seleciona o **prioridade** método de encaminhamento de tráfego, a ordem do é importante pontos finais selecionado. Certifique-se a ordem de prioridade de pontos finais.  O ponto final principal está na parte superior. Verificar na ordem que é apresentado. todos os pedidos serão encaminhados para o primeiro ponto final e se o Gestor de tráfego detetar-ser mau estado de funcionamento, o tráfego automaticamente a ativação pós-falha para o próximo ponto final. 
7. Para alterar a ordem de prioridade de ponto final, clique no ponto final e, no **Endpoint** painel que é apresentado, clique em **editar** e altere o **prioridade** valor conforme necessário. 
8. Clique em **guardar** para guardar, alterar as definições de ponto final.
9. Depois de concluir as alterações de configuração, clique em **guardar** na parte inferior da página.
10. Teste as alterações na configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego e clique no perfil do Traffic Manager nos resultados de que o apresentados.
    2.  No **Gestor de tráfego** painel do perfil, clique em **descrição geral**.
    3.  O **perfil do Traffic Manager** painel apresenta o nome DNS do perfil do Traffic Manager recentemente criado. Isto pode ser utilizado por quaisquer clientes (por exemplo, navegando para a mesma utilizando um browser) obter encaminhados para o ponto final à direita como determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o primeiro ponto final e se o Gestor de tráfego detetar-ser mau estado de funcionamento, o tráfego automaticamente a ativação pós-falha para o próximo ponto final.
11. Assim que o perfil do Traffic Manager está a funcionar, edite o registo DNS no seu servidor DNS autoritativo para apontar o nome de domínio da empresa para o nome de domínio do Traffic Manager.

![Configurar o método de encaminhamento de tráfego de prioridade utilizando o Gestor de tráfego][1]

## <a name="next-steps"></a>Passos seguintes


- Saiba mais sobre [ponderado método de encaminhamento de tráfego](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre [método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md).
- Saiba mais sobre [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png