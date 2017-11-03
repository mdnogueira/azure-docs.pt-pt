---
title: "Descrição geral de redirecionamento para o Gateway de aplicação do Azure | Microsoft Docs"
description: "Saiba mais sobre a capacidade de redirecionamento no Gateway de aplicação do Azure"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-redirect-overview"></a>Descrição geral de redirecionamento do Gateway de aplicação

É um cenário comum para muitas aplicações web para suportar HTTP automática para o redirecionamento de HTTPS para garantir que todas as comunicações entre aplicações e os seus utilizadores ocorre através de um caminho encriptado. No passado, os clientes utilizou técnicas, como criar um conjunto de back-end dedicado cujo objetivo único é redirecionar pedidos a receber de HTTP para HTTPS.  Gateway de aplicação suporta agora a capacidade de redirecionar o tráfego no Gateway de aplicação. Isto simplifica a configuração da aplicação, otimiza a utilização de recursos e suporta novos cenários de redirecionamento, incluindo global e com base no caminho de redirecionamento. Suporte de redirecionamento do Gateway de aplicação não está limitado a HTTP -> redirecionamento HTTPS individualmente. Este é um mecanismo de redirecionamento genérico, que permite o redirecionamento de tráfego recebido na entidade um serviço de escuta para outro serviço de escuta no Gateway de aplicação. Também suporta redirecionamento a um site de externo. Suporte de redirecionamento do Gateway de aplicação oferece as seguintes capacidades:

1. Redirecionamento global de um serviço de escuta para outro serviço de escuta no Gateway. Isto permite que o HTTP para redirecionamento de HTTPS, num site.
2. Com base no caminho de redirecionamento. Este tipo de redirecionamento de permite HTTP para o redirecionamento de HTTPS apenas na área de um site específico, por exemplo uma área de carrinho de compras em falta por que/carrinho / *.
3. Redirecionar para sites externos.

![redirecionar](./media/application-gateway-redirect-overview/redirect.png)

Com esta alteração, os clientes seriam necessário criar um novo objeto de configuração de redirecionamento, que especifica o serviço de escuta de destino ou um site externo ao qual o redirecionamento for pretendido. O elemento de configuração também suporta opções para ativar a acrescentar a cadeia de caminho e consulta URI para o URL redirecionado. Os clientes também poderia escolher se o redirecionamento é um temporário (código de estado HTTP 302) ou um redirecionamento permanente (código de estado HTTP 301). Uma vez criada esta configuração de redirecionamento está ligado ao serviço de escuta de origem através de uma nova regra. Quando utilizar uma regra básica, a configuração de redirecionamento está associada um serviço de escuta de origem e é um redirecionamento global. Quando é utilizada uma regra com base no caminho, a configuração de redirecionamento está definida no mapa de caminho do URL e, por conseguinte, apenas se aplica a área de caminho específico de um site.

### <a name="next-steps"></a>Passos seguintes

[Configurar redirecionamento de URL num gateway de aplicação](application-gateway-configure-redirect-powershell.md)
