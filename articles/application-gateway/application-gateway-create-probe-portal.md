---
title: "Criar uma sonda personalizada - Gateway de aplicação do Azure - Portal do Azure | Microsoft Docs"
description: "Saiba como criar uma sonda personalizada para o Gateway de aplicação utilizando o portal"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma sonda personalizada para o Gateway de aplicação utilizando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, adicione uma sonda personalizada para um gateway de aplicação existente através do portal do Azure. Das sondas personalizadas são úteis para as aplicações que têm uma página de verificação do Estado de funcionamento específico ou para as aplicações que não fornecem uma resposta com êxito a aplicação web predefinida.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não tiver um gateway de aplicação, visite [criar um Gateway de aplicação](application-gateway-create-gateway-portal.md) para criar um gateway de aplicação para trabalhar com.

## <a name="createprobe"></a>Criar a sonda

As pesquisas estão configuradas num processo de dois passos através do portal. O primeiro passo consiste em criar a sonda. O segundo passo, adicione a sonda para as definições de http de back-end do gateway de aplicação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter um [um mês avaliação gratuita](https://azure.microsoft.com/free)

1. No painel Favoritos portal do Azure, clique em todos os recursos. Clique no gateway de aplicação no painel de todos os recursos. Se a subscrição que selecionou já tem vários recursos na mesma, pode introduzir partners.contoso.net no filtro de por nome... para aceder facilmente ao gateway de aplicação.

1. Clique em **sondas** e clique em de **adicionar** botão para adicionar uma pesquisa.

  ![Adicionar sonda painel com informações preenchidas][1]

1. No **sonda do Estado de funcionamento de adicionar** painel, preencha as informações necessárias para a sonda e, quando concluir clique **OK**.

  |**Definição** | **Valor** | **Detalhes**|
  |---|---|---|
  |**Nome**|customProbe|Este valor é um nome amigável para a pesquisa que está acessível no portal.|
  |**Protocolo**|HTTP ou HTTPS | O protocolo que utiliza a sonda de estado de funcionamento.|
  |**Anfitrião**|revertidos contoso.com|Este valor é o nome de anfitrião que é utilizado para a sonda. Aplicável apenas quando vários sites está configurada no Gateway de aplicação, caso contrário, utilize '127.0.0.1'. Este valor é diferente do nome de anfitrião VM.|
  |**Caminho**|/ ou outro caminho|O resto o url completo para a sonda personalizada. Um caminho válido começa por '/'. Para o caminho predefinido de http://contoso.com apenas utilizar '/' |
  |**Intervalo (seg)**|30|Frequência de sonda é executada para verificar a existência de estado de funcionamento. Não é recomendado para definir o inferior a 30 segundos.|
  |**Limite de tempo (seg)**|30|A quantidade de tempo a sonda aguarda antes de exceder o tempo limite. O intervalo de tempo limite tem de ser suficientemente alto para que pode ser efetuada uma chamada http para garantir que a página de estado de funcionamento de back-end está disponível.|
  |**Limiar de mau estado de funcionamento**|3|Número de tentativas falhadas para ser considerado em mau estado de funcionamento. Limiar de 0 significa que o se uma verificação de estado de funcionamento falhar back-end é determinado mau estado de funcionamento imediatamente.|

  > [!IMPORTANT]
  > O nome do anfitrião não é igual ao nome do servidor. Este valor é o nome de anfitrião virtual em execução no servidor de aplicações. A pesquisa é enviada para http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Adicionar a pesquisa para o gateway

Agora que a sonda foi criada, está na altura de adicioná-lo para o gateway. Definições de pesquisa são definidas nas definições de http de back-end do gateway de aplicação.

1. Clique em **definições HTTP** no gateway de aplicação, para trazer o painel Configuração, clique em back-end do atual definições http listado na janela.

  ![janela de definições de HTTPS][2]

1. No **appGatewayBackEndHttpSettings** painel Definições, consulte o **sonda personalizada utilize** caixa de verificação e escolha a sonda criada no [criar a sonda](#createprobe) secção sobre o  **Sonda personalizada** pendente...
Quando terminar, clique em **guardar** e as definições são aplicadas.

A sonda predefinida verifica o acesso de predefinido para a aplicação web. Agora que foi criada uma sonda personalizada, o gateway de aplicação utiliza o caminho personalizado definido para monitorizar o estado de funcionamento para os servidores de back-end. Com base nos critérios que foi definido, o gateway de aplicação verifica o caminho especificado na pesquisa. Se a chamada para o anfitrião: porta / caminho não devolve um HTTP 200 399 resposta de estado, o servidor é retirado rotação após ter sido atingido o limiar de mau estado de funcionamento. Pesquisar continua na instância do mau estado de funcionamento para determinar quando torna-se em bom estado novamente. Assim que a instância é adicionada novamente para o agrupamento de servidores de bom estado de funcionamento, tráfego começa a fluir novamente ao mesmo e pesquisa para a instância continua intervalo especificado do utilizador como habitualmente.

## <a name="next-steps"></a>Passos seguintes

Para saber como configurar a descarga de SSL com o Gateway de aplicação do Azure, consulte [configurar a descarga de SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

