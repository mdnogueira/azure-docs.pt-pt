---
title: "Configurar a descarga de SSL - Gateway de aplicação do Azure - Portal do Azure | Microsoft Docs"
description: "Este artigo fornece instruções para criar um gateway de aplicação com SSL offload utilizando o portal do Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Configurar um gateway de aplicação para a descarga de SSL com o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI 2.0 do Azure](application-gateway-ssl-cli.md)

Pode configurar o Azure Application Gateway para terminar a sessão SSL (Secure Sockets Layer) no gateway para evitar tarefas dispendiosas de desencriptação de SSL que ocorrem no farm Web. A descarga de SSL simplifica ainda a configuração do servidor de front-end e a gestão da aplicação Web.

## <a name="scenario"></a>Cenário

O cenário seguinte orienta-configurar a descarga de SSL num gateway de aplicação existente. O cenário pressupõe que já tiver seguido os passos para [criar um gateway de aplicação](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Para configurar a descarga de SSL com um gateway de aplicação, é necessário um certificado. Este certificado é carregado no gateway de aplicação e é utilizado para encriptar e desencriptar o tráfego enviado através de SSL. O certificado tem de estar no formato Personal Information Exchange (. pfx). Este formato de ficheiro permite-lhe exportar a chave privada que é necessário para o gateway de aplicação para efetuar a encriptação e desencriptação de tráfego.

## <a name="add-an-https-listener"></a>Adicionar um serviço de escuta HTTPS

O serviço de escuta HTTPS procura tráfego com base na respetiva configuração e ajuda a encaminhar o tráfego para os conjuntos de back-end. Para adicionar um serviço de escuta HTTPS, siga estes passos:

   1. Navegue para o portal do Azure e selecione um gateway de aplicação existente.

   2. Selecione **os serviços de escuta**e, em seguida, selecione o **adicionar** botão para adicionar um serviço de escuta.

   ![Painel de descrição geral do Gateway de aplicação][1]


   3. Preencha as informações seguintes necessárias para o serviço de escuta e carregar o certificado. pfx:
      - **Nome**: O nome amigável do serviço de escuta.

      - **Configuração de IP de front-end**: A configuração de IP Front-end que é utilizada para o serviço de escuta.

      - **Porta de front-end (nome/Port)**: um nome amigável para a porta utilizada no front-end do gateway de aplicação e a porta real é utilizada.

      - **Protocolo**: um comutador para determinar se é utilizado para HTTPS ou HTTP para o front-end.

      - **O certificado (nome/palavra-passe)**: descarga de SSL se for utilizada, é necessário um certificado. pfx para esta definição. Também são necessários um nome amigável e uma palavra-passe.

   4. Selecione **OK**.

![Adicionar um painel do serviço de escuta][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Criar uma regra e associá-la para o serviço de escuta

O serviço de escuta agora foi criado. Em seguida, crie uma regra para processar o tráfego do serviço de escuta. As regras definem como o tráfego é encaminhado para os conjuntos de back-end com base nas várias definições de configuração. Estas definições incluem o protocolo, a porta e as sondas de estado de funcionamento, e se a afinidade com base no cookie de sessão é utilizada. Para criar e associar uma regra para o serviço de escuta, siga estes passos:


   1. Selecione o **regras** do gateway de aplicação e, em seguida, selecione **adicionar**.

   ![Painel de regras de Gateway de aplicação][3]


   2. Em **Adicionar regra básica**, introduza um nome amigável para a regra no **nome** campo e, em seguida, selecione o **escuta** criado no passo anterior. Selecione as adequadas **conjunto back-end** e **definição HTTP**e, em seguida, selecione **OK**.

   ![Janela definições de HTTPS][4]

As definições são agora guardadas para o gateway de aplicação. A guardar processar para estas definições podem demorar algum tempo antes de serem disponíveis para ver através do portal ou através do PowerShell. Depois de é guardada, o gateway de aplicação processa a encriptação e desencriptação de tráfego. Todo o tráfego entre o gateway de aplicação e os servidores web de back-end será processado através de HTTP. Qualquer comunicação de volta para o cliente se iniciada através de HTTPS, será devolvida ao cliente encriptado.

## <a name="next-steps"></a>Passos seguintes

Para saber como configurar uma sonda do Estado de funcionamento personalizado com Gateway de aplicação do Azure, consulte [criar uma sonda do Estado de funcionamento personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

