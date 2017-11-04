---
title: "Configurar o método de encaminhamento de tráfego de desempenho utilizando o Gestor de tráfego do Azure | Microsoft Docs"
description: "Este artigo explica como configurar o Gestor de tráfego para encaminhar o tráfego para o ponto final com latência mais baixa"
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
ms.openlocfilehash: 014aa646459cd64fca7c697419324caa3edaeeea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-performance-traffic-routing-method"></a>Configurar o método de encaminhamento de tráfego de desempenho

O método de encaminhamento de tráfego de desempenho permite-lhe direcionar o tráfego para o ponto final com a menor latência de rede do cliente. Normalmente, o Centro de dados com a menor latência é o mais próximo na distância geográfica. Este método de encaminhamento do tráfego não é possível em conta as alterações em tempo real na configuração de rede ou de carga.

##  <a name="to-configure-performance-routing-method"></a>Para configurar o método de encaminhamento de desempenho

1. Num browser, inicie sessão no [portal do Azure](http://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter uma [avaliação gratuita durante um mês](https://azure.microsoft.com/free/). 
2. Na barra de pesquisa do portal, procure o **perfis do Traffic Manager** e, em seguida, clique no nome de perfil que pretende configurar o método de encaminhamento.
3. No **perfil do Traffic Manager** painel, certifique-se de que os serviços em nuvem e de Web sites que pretende incluir na sua configuração estão presentes.
4. No **definições** secção, clique em **configuração**e, no **configuração** painel, concluída, da seguinte forma:
    1. Para **definições do método de encaminhamento de tráfego**, para **método de encaminhamento** selecione **desempenho**.
    2. Definir o **definições de Endpoint Protection do monitor** idêntica para todas as cada ponto final dentro deste perfil da seguinte forma:
        1. Selecione as adequadas **protocolo**e especifique o **porta** número. 
        2. Para **caminho** escreva uma barra  */* . Monitorizar pontos finais, tem de especificar um caminho e nome de ficheiro. A barra de reencaminhar "/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório de raiz (predefinição).
        3. Na parte superior da página, clique em **guardar**.
5.  Teste as alterações na configuração da seguinte forma:
    1.  Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego e clique no perfil do Traffic Manager nos resultados de que o apresentados.
    2.  No **Gestor de tráfego** painel do perfil, clique em **descrição geral**.
    3.  O **perfil do Traffic Manager** painel apresenta o nome DNS do perfil do Traffic Manager recentemente criado. Isto pode ser utilizado por quaisquer clientes (por exemplo, navegando para a mesma utilizando um browser) obter encaminhados para o ponto final à direita como determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o ponto final com a menor latência da rede do cliente.
6. Assim que o perfil do Traffic Manager está a funcionar, edite o registo DNS no seu servidor DNS autoritativo para apontar o nome de domínio da empresa para o nome de domínio do Traffic Manager.

![Configurar o método de encaminhamento de tráfego de desempenho utilizando o Gestor de tráfego][1]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ponderado método de encaminhamento de tráfego](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre [método de encaminhamento de prioridade](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Saiba como [testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png