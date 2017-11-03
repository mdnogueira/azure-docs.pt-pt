---
title: "Gerir pontos finais de transmissão em fluxo com o portal do Azure | Microsoft Docs"
description: "Este tópico mostra como gerir pontos finais de transmissão em fluxo com o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: cfowler
editor: 
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: 89b4f7cee6d00b5bbbca4799eeca74a280147fc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerir pontos finais de transmissão em fluxo com o portal do Azure

Este tópico mostra como utilizar o portal do Azure para gerir pontos finais de transmissão em fluxo. 

>[!NOTE]
>Certifique-se de que revê o [descrição geral](media-services-streaming-endpoints-overview.md) tópico. 

Para obter informações sobre como dimensionar o ponto final de transmissão em fluxo, consulte [isto](media-services-portal-scale-streaming-endpoints.md) tópico.

## <a name="start-managing-streaming-endpoints"></a>Começar a gerir pontos finais de transmissão em fluxo 

Para começar a gerir pontos finais de transmissão em fluxo para a sua conta, efetue o seguinte.

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. No **definições** painel, selecione **pontos finais de transmissão em fluxo**.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> É-lhe faturado apenas quando o ponto final de transmissão em fluxo está no estado de execução.

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/eliminar um ponto final de transmissão em fluxo

>[!NOTE]
>Não é possível eliminar o ponto final de transmissão em fluxo predefinido.

Para adicionar/eliminar ponto final de transmissão em fluxo utilizando o portal do Azure, efetue o seguinte:

1. Para adicionar um ponto final de transmissão em fluxo, clique o **+ ponto final** na parte superior da página. 

    Pode querer vários pontos finais de transmissão em fluxo se planear ter CDNs diferentes ou uma CDN e acesso direto.

2. Para eliminar um ponto final de transmissão em fluxo, prima **eliminar** botão.      
3. Clique em de **iniciar** botão Iniciar o ponto final de transmissão em fluxo.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Configurar o ponto final de transmissão em fluxo
Ponto final de transmissão em fluxo permite-lhe configurar as seguintes propriedades:

* Controlo de acesso
* Controlo de cache
* Entre as políticas de acesso do site

Para obter informações detalhadas sobre estas propriedades, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando ativar a CDN não é possível aceder acesso IP. Acesso IP só é aplicável quando não tiver o CDN.

Pode configurar o ponto final de transmissão em fluxo efetuando o seguinte procedimento:

1. Selecione o ponto final de transmissão em fluxo que pretende configurar.
2. Clique em **definições**.

Segue uma breve descrição dos campos.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política de cache máximo: utilizado para configurar a duração da cache para ativos servidos através deste ponto final de transmissão em fluxo. Não se for definido nenhum valor, é utilizada a predefinição. Os valores predefinidos também podem ser definidos diretamente no armazenamento do Azure. Se a CDN do Azure está ativada para o ponto final de transmissão em fluxo, não deve definir o valor de política de cache para inferior a 600 segundos.  
2. Endereços IP permitidos: utilizado para especificar os endereços IP que seriam possível autorizados a ligar para o ponto final de transmissão em fluxo publicado. Se não houver endereços IP especificado, qualquer endereço IP será capaz de ligar. Endereços IP podem ser especificados como um endereço IP único (por exemplo, "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo IP com o endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo, ' 10.0.0.1(255.255.255.0)').
3. Configuração de autenticação da assinatura de cabeçalho Akamai: utilizado para especificar a forma como o pedido de autenticação de cabeçalho de assinatura da Akamai servidores está configurado. Expiração está em UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Dimensionar a sua Premium ponto final de transmissão em fluxo

Para obter mais informações, veja [este](media-services-portal-scale-streaming-endpoints.md) tópico.

## <a id="enable_cdn"></a>Ativar a integração da CDN do Azure

Quando cria uma nova conta, a integração de CDN do Azure de ponto final de transmissão em fluxo predefinido está ativada por predefinição.

Se, posteriormente, pretender desativar/ativar o CDN, o ponto final de transmissão em fluxo tem de constar da **parado** estado. Pode demorar até 2 horas para a integração da CDN do Azure obter ativada e para que as alterações estar ativas em todas as a CDN faz aparecer. No entanto, pode iniciar o ponto final de transmissão em fluxo e fluxo sem interrupções do ponto final da transmissão e assim que a integração estiver concluída, o fluxo serão entregues da CDN. Durante o período de aprovisionamento, o ponto final de transmissão em fluxo estarão disponíveis na **iniciar** estado e poderá reparar degredad desempenho.

Integração da CDN está ativada em todos os centros de dados do Azure execpt China e Federal Goverment regiões.

Assim que estiver ativado, o **controlo de acesso**, **personalizada hostname** e **autenticação da assinatura da Akamai** configuração obtém desativada.
 
> [!IMPORTANT]
> Integração de Media Services do Azure com a CDN do Azure é implementada em **CDN do Azure da Verizon** para padrão de transmissão em fluxo pontos finais. Premium pontos finais de transmissão em fluxo pode ser configurada com todos os **CDN do Azure camadas e fornecedores de preço**. Para obter mais informações sobre as funcionalidades da CDN do Azure, consulte o [descrição geral da CDN](../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Considerações adicionais

* Quando a CDN estiver ativada para um ponto final de transmissão em fluxo, os clientes não podem pedir conteúdo diretamente a partir da origem. Se precisar de capacidade para testar o conteúdo com ou sem CDN, pode criar outro ponto final transmissão em fluxo que não esteja CDN ativada.
* O hostname de ponto final de transmissão em fluxo permanece igual depois de ativar a CDN. Não precisa de fazer alterações ao seu fluxo de trabalho de serviços de suporte de dados depois de ativar a CDN. Por exemplo, se o nome de anfitrião de ponto final transmissão em fluxo é strasbourg.streaming.mediaservices.windows.net, depois de ativar a CDN, é utilizado o nome exato do mesmo anfitrião.
* Para pontos finais de transmissão em fluxo novo, pode ativar a CDN simplesmente através da criação de um novo ponto de final; para pontos finais de transmissão em fluxo existentes, terá de parar pela primeira vez o ponto final e, em seguida, ativar/desativar a CDN.
* Padrão de ponto final de transmissão em fluxo só pode ser configurada utilizando **fornecedor CDN Standard da Verizon** utilizando o portal de gestão do Azure. No entanto, pode ativar outros fornecedores de CDN do Azure através de REST APIs.

## <a name="configure-cdn-profile"></a>Configurar o perfil de CDN

Pode configurar o perfil CDN, selecionando o **gerir CDN** botão do nível superior.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

