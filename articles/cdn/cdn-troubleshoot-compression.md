---
title: "Resolução de problemas de compressão do ficheiro na CDN do Azure | Microsoft Docs"
description: "Resolva problemas com a compressão do ficheiro de CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5ef8a8262eb40aa827161764f03a63d031e43273
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-file-compression"></a>Resolver problemas de compressão de ficheiros CDN
Este artigo ajuda-o a resolver problemas com [compressão do ficheiro CDN](cdn-improve-performance.md).

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [do MSDN Azure e os fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode também ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
A compressão para o ponto final está ativada, mas os ficheiros estão a ser devolvidos descomprimidos.

> [!TIP]
> Para verificar se os ficheiros estão a ser devolvidos comprimidos, tem de utilizar uma ferramenta como o [Fiddler](http://www.telerik.com/fiddler) ou do seu browser [ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Cabeçalhos de resposta de verificação do HTTP devolveu o CDN em cache conteúdos.  Se houver um cabeçalho com o nome `Content-Encoding` com um valor de **gzip**, **bzip2**, ou **deflate**, o conteúdo é comprimido.
> 
> ![Cabeçalho de codificação de conteúdo](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Causa
Existem várias causas possíveis, incluindo:

* O conteúdo solicitado não é elegível para compressão.
* Compressão não está ativada para o tipo de ficheiro pedida.
* O pedido HTTP não inclui um cabeçalho de pedir um tipo de compressão válido.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!TIP]
> Tal como com a implementação de novos pontos finais, as alterações de configuração da CDN demorar algum tempo para propagar através da rede.  Normalmente, as alterações sejam aplicadas dentro de 90 minutos.  Se esta for a primeira vez que tiver configurado a compressão para o ponto final CDN, deve considerar a aguardar 1-2 horas para garantir que as definições de terem sido propagados para os POPs a compressão. 
> 
> 

### <a name="verify-the-request"></a>Verifique se o pedido
Em primeiro lugar, iremos deve efetuar uma verificação rápida sanity no pedido.  Pode utilizar o seu browser [ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) para ver os pedidos efetuados.

* Certifique-se de que o pedido está a ser enviado para o seu URL de ponto final, `<endpointname>.azureedge.net`e não a sua origem.
* Certifique-se de que o pedido contém um **codificação de aceitar** cabeçalho e o valor para essa cabeçalho contém **gzip**, **deflate**, ou **bzip2**.

> [!NOTE]
> **CDN do Azure da Akamai** perfis suporte apenas **gzip** codificação.
> 
> 

![Cabeçalhos de pedido CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>Verifique as definições de compressão (perfil de Standard CDN)
> [!NOTE]
> Este passo aplica-se apenas se o perfil de CDN é uma **CDN do Azure Standard da Verizon** ou **CDN do Azure Standard da Akamai** perfil. 
> 
> 

Navegue para o ponto final no [portal do Azure](https://portal.azure.com) e clique em de **configurar** botão.

* Certifique-se de que a compressão está ativada.
* Certifique-se de que o tipo MIME para o conteúdo a comprimir está incluído na lista de formatos comprimidos.

![Definições de compressão de CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>Verifique as definições de compressão (perfil de Premium CDN)
> [!NOTE]
> Este passo aplica-se apenas se o perfil de CDN é uma **CDN do Azure Premium da Verizon** perfil.
> 
> 

Navegue para o ponto final no [portal do Azure](https://portal.azure.com) e clique em de **gerir** botão.  Irá abrir o portal suplementar.  Coloque o cursor sobre o **HTTP grande** separador, em seguida, coloque o cursor sobre o **definições da Cache** flyout.  Clique em **compressão**. 

* Certifique-se de que a compressão está ativada.
* Certifique-se a **tipos de ficheiro** lista contém uma lista separada por vírgulas (sem espaços) de tipos de MIME.
* Certifique-se de que o tipo MIME para o conteúdo a comprimir está incluído na lista de formatos comprimidos.

![Definições de compressão CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>Certifique-se de que o conteúdo é colocado em cache
> [!NOTE]
> Este passo aplica-se apenas se o perfil de CDN é uma **CDN do Azure da Verizon** perfil (Standard ou Premium).
> 
> 

Utilizar ferramentas de programador do seu browser, certifique-se os cabeçalhos de resposta para garantir que o ficheiro é colocado em cache na região onde está a ser pedida.

* Verifique o **servidor** cabeçalho de resposta.  O cabeçalho deve ter o formato **Platform (servidor/POP ID)**, como mostrado no exemplo seguinte.
* Verifique o **X-Cache** cabeçalho de resposta.  Deve ler o cabeçalho **ACESSOS**.  

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>Certifique-se de que o ficheiro cumpre os requisitos de tamanho
> [!NOTE]
> Este passo aplica-se apenas se o perfil de CDN é uma **CDN do Azure da Verizon** perfil (Standard ou Premium).
> 
> 

Para ser elegível para compressão, um ficheiro tem de cumprir os seguintes requisitos de tamanho:

* Maior do que 128 bytes.
* Menor que 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique o pedido no servidor de origem para um **através de** cabeçalho
O **através de** cabeçalho de HTTP indica ao servidor web que o pedido está a ser transferido por um servidor proxy.  Os servidores web do Microsoft IIS por predefinição não comprimir respostas quando o pedido contém um **através de** cabeçalho.  Para substituir este comportamento, execute o seguinte:

* **IIS 6**: [definir HcNoCompressionForProxies = "FALSE" nas propriedades de Metabase do IIS](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 e até**: [definidas **noCompressionForHttp10** e **noCompressionForProxies** como False na configuração do servidor](http://www.iis.net/configreference/system.webserver/httpcompression)

