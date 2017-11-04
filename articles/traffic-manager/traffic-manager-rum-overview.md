---
title: Medidas de utilizador real no Traffic Manager do Azure | Microsoft Docs
description: "Introdução ao utilizador Real medidas no Gestor de tráfego"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Descrição geral de medidas de utilizador reais do Gestor de tráfego

>[!NOTE]
>A funcionalidade de medidas de utilizador Real no Traffic Manager está em pré-visualização pública e não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. A funcionalidade não é suportada, pode ter restrita capacidades e poderão não estar disponível em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado desta funcionalidade, consulte o [Traffic Manager do Azure atualiza](https://azure.microsoft.com/updates/?product=traffic-manager) página.

Quando configurar um perfil do Traffic Manager para utilizar o método de encaminhamento de desempenho, o serviço procura onde os pedidos de consulta DNS são feitos e toma decisões de encaminhamento para direcionar os requerentes para a região do Azure fornece-lhes a latência mais baixa. Isto é conseguido utilizando o intelligence de latência de rede que mantém o Gestor de tráfego para redes de outro utilizador final.

Medidas de utilizador reais permite-lhe medir medidas de latência de rede para regiões do Azure, das aplicações de cliente que aos utilizadores finais utilizarem, e ter Gestor de tráfego considere essas informações, bem como quando decisões de encaminhamento. Ao escolher esta opção utilizar os valores reais do utilizador, pode aumentar a precisão do encaminhamento de pedidos feitos essas redes onde residem os utilizadores finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam os valores reais do utilizador

Medidas de utilizador reais de trabalho, fazendo com que a latência de medidas de aplicações de cliente para regiões do Azure visto que é das redes de utilizador final no qual são utilizados. Por exemplo, se tiver uma página web que são acedidos por utilizadores em localizações diferentes (por exemplo, em regiões Norte American), pode tirar partido potência de medidas de utilizador reais ao utilizar o método de encaminhamento de desempenho para esforçar-nos para a região do Azure melhor no qual a aplicação de servidor está alojada.

Começa por incorporar um JavaScript fornecido do Azure (com uma chave exclusiva no mesmo) nas suas páginas web. Depois de o fazer, sempre que um utilizador aceder a essa página Web, o JavaScript consulta o Gestor de tráfego para obter informações sobre regiões do Azure deve medir. O serviço devolve um conjunto de pontos finais para o script que, em seguida, medidas, estas regiões consecutivamente ao transferir uma imagem de pixel único alojada essas regiões do Azure e a pena realçar a latência entre o momento o pedido foi enviado e a hora quando foi recebido o primeiro byte . Estas medidas, em seguida, são reportadas novamente para o serviço do Gestor de tráfego.

Ao longo do tempo, isto acontece demasiadas vezes e em redes muitos esquerda ao Gestor de tráfego de obter informações mais precisos sobre as características de latência das redes na qual residem os utilizadores finais. Estas informações começa a obter a ser incluído nas decisões de encaminhamento efetuadas pelo Gestor de tráfego. Como resultado, leva a maior precisão nessas decisões são baseada em medidas de utilizador reais enviados.

Quando utilizar medidas de utilizador reais, é-lhe cobrada com base no número de valores enviados para o Gestor de tráfego. Para obter mais detalhes sobre os preços, visite o [Gestor de tráfego a página de preços](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Passos seguintes
- Saiba como utilizar [Real de utilizador de medidas com páginas web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil de Gestor de tráfego](traffic-manager-create-profile.md)

