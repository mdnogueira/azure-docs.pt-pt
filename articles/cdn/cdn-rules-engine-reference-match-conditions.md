---
title: "Condições de correspondência do motor de regras de CDN do Azure | Microsoft Docs"
description: "Documentação de referência para a CDN do Azure regras de funcionalidades e as condições de correspondência do motor."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Condições de corresponder ao motor de regras CDN do Azure
Este tópico lista as descrições detalhadas das condições de correspondência de rede de entrega de conteúdos (CDN) do Azure disponíveis [motor de regras](cdn-rules-engine.md).

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de pedidos para o qual será efetuado um conjunto de funcionalidades.

Por exemplo, pode ser utilizado para filtrar pedidos para o conteúdo num determinado local, pedidos gerado a partir de um determinado endereço IP ou o país ou informações de cabeçalho.

## <a name="always"></a>Sempre

A condição de correspondência de sempre foi concebida para aplicar um conjunto predefinido de funcionalidades para todos os pedidos.

## <a name="device"></a>Dispositivo

A condição de correspondência de dispositivo identifica os pedidos efetuados a partir de um dispositivo móvel com base nas respetivas propriedades.  Deteção de dispositivos móveis é conseguida através de [WURFL](http://wurfl.sourceforge.net/).  Capacidades WURFL e as respetivas variáveis do motor de regras de CDN são listados abaixo.

> [!NOTE] 
> As variáveis abaixo são suportadas o **modificar cabeçalho de pedido do cliente** e **modificar cabeçalho de resposta de cliente** funcionalidades.

Capacidade | Variável | Descrição | Valores de exemplo
-----------|----------|-------------|----------------
Nome de marca | % {wurfl_cap_brand_name} | Uma cadeia que indica o nome de marca do dispositivo. | Samsung
SO do dispositivo | % {wurfl_cap_device_os} | Uma cadeia que indica o sistema operativo instalado no dispositivo. | IOS
Versão do SO do dispositivo | % {wurfl_cap_device_os_version} | Uma cadeia que indica o número de versão do SO instalado no dispositivo. | 1.0.1
Orientação dupla | % {wurfl_cap_dual_orientation} | Um valor boleano que indica se o dispositivo suporta orientação dupla. | VERDADEIRO
HTML preferencial DTD | % {wurfl_cap_html_preferred_dtd} | Uma cadeia que indica documento preferencial definição o dispositivo móvel (DTD) para o conteúdo HTML. | Nenhum<br/>xhtml_basic<br/>HTML5
Imagem Inlining | % {wurfl_cap_image_inlining} | Um valor boleano que indica se o dispositivo suporta Base64 codificado imagens. | FALSO
É Android | % {wurfl_vcap_is_android} | Um valor boleano que indica se o dispositivo utiliza o SO Android. | VERDADEIRO
É IOS | % {wurfl_vcap_is_ios} | Um valor boleano que indica se o dispositivo utiliza iOS. | FALSO
É TV inteligente | % {wurfl_cap_is_smarttv} | Um valor boleano que indica se o dispositivo é um Televisor inteligente. | FALSO
É Smartphone | % {wurfl_vcap_is_smartphone} | Um valor boleano que indica se o dispositivo é um smartphone. | VERDADEIRO
É Tablet | % {wurfl_cap_is_tablet} | Um valor boleano que indica se o dispositivo é um tablet. Esta é uma descrição de SO independentes. | VERDADEIRO
É dispositivo sem fios | % {wurfl_cap_is_wireless_device} | Um valor boleano que indica se o dispositivo é considerado um dispositivo sem fios. | VERDADEIRO
Nome de marketing | % {wurfl_cap_marketing_name} | Uma cadeia que indica o nome do dispositivo de marketing. | Pearl blackBerry 8100
Browser móvel | % {wurfl_cap_mobile_browser} | Uma cadeia que indica o browser utilizado para pedir conteúdo a partir do dispositivo. | Chrome
Versão do Browser móveis | % {wurfl_cap_mobile_browser_version} | Uma cadeia que indica a versão do browser utilizado para pedir conteúdo a partir do dispositivo. | 31
Nome do modelo | % {wurfl_cap_model_name} | Uma cadeia que indica o nome do modelo do dispositivo. | S3
Transferência progressiva | % {wurfl_cap_progressive_download} | Um valor boleano que indica se o dispositivo suporta a reprodução de áudio/vídeo enquanto ainda está a ser transferido. | VERDADEIRO
Data da versão | % {wurfl_cap_release_date} | Uma cadeia que indica o ano e mês em que o dispositivo foi adicionado à base de dados WURFL.<br/><br/>Formato:`yyyy_mm` | 2013_december
Altura de resolução | % {wurfl_cap_resolution_height} | Um número inteiro que indica a altura do dispositivo em pixels. | 768
Largura de resolução | % {wurfl_cap_resolution_width} | Um número inteiro que indica a largura do dispositivo em pixéis. | 1024


## <a name="location"></a>Localização

Estas condições são concebidas para identificar os pedidos com base na localização do autor do pedido de correspondência.

Nome | Objetivo
-----|--------
COMO número | Identifica pedidos provenientes de uma rede específico.
País | Identifica pedidos provenientes de países/regiões especificados.


## <a name="origin"></a>Origem

Estes correspondência condições foram concebidas para identificar os pedidos que apontam para armazenamento CDN ou um servidor de origem do cliente.

Nome | Objetivo
-----|--------
Origem da CDN | Identifica os pedidos para o conteúdo armazenado no armazenamento do CDN.
Origem de cliente | Identifica os pedidos de conteúdo armazenado num servidor de origem do cliente específico.


## <a name="request"></a>Pedir

Estes correspondência condições foram concebidas para identificar os pedidos com base nas respetivas propriedades.

Nome | Objetivo
-----|--------
Endereço IP do cliente | Identifica pedidos provenientes de um endereço IP específico.
Parâmetro de cookie | Verifica os cookies associados a cada pedido para o valor especificado.
Cookie parâmetro Regex | Verifica os cookies associados a cada pedido para a expressão regular especificada.
Limite Cname | Identifica os pedidos que apontam para um limite específico CNAME.
Referência de domínio | Identifica os pedidos que foram consultados os hostname(s) especificado.
Literal de cabeçalho de pedido | Identifica os pedidos que contêm o cabeçalho especificado definido como um valor especificado (es).
Regex de cabeçalho de pedido | Identifica os pedidos que contêm o cabeçalho especificado definido como um valor que corresponde à expressão regular especificada.
Caráter universal de cabeçalho de pedido | Identifica os pedidos que contêm o cabeçalho especificado definido como um valor que corresponde ao padrão especificado.
Método de pedido | Identifica pedidos pelo respetivo método HTTP.
Esquema de pedido | Identifica pedidos pelo respetivo protocolo HTTP.

## <a name="url"></a>URL

Estas condições são concebidas para identificar os pedidos com base no respetivos URLs de correspondência.

Nome | Objetivo
-----|--------
Diretório de caminho de URL | Identifica pedidos pelo respetivo caminho relativo.
Extensão de caminho de URL | Identifica pedidos pela respetiva extensão de nome de ficheiro.
Nome de ficheiro de caminho de URL | Identifica pedidos por nome de ficheiro.
Literal de caminho de URL | Compara o caminho relativo de um pedido para o valor especificado.
Regex de caminho de URL | Compara o caminho relativo de um pedido para a expressão regular especificada.
Caminho de URL com carateres universais | Compara o caminho relativo de um pedido para o padrão especificado.
Literal de consulta de URL | Compara a cadeia de consulta de um pedido para o valor especificado.
Parâmetro de consulta de URL | Identifica os pedidos que contêm o parâmetro de cadeia de consulta especificada definido como um valor que corresponde ao padrão especificado.
Regex de consulta de URL | Identifica os pedidos que contêm o parâmetro de cadeia de consulta especificada definido como um valor que corresponde a uma expressão regular especificada.
Consulta de URL com carateres universais | Compara o valor (es) especificado na cadeia de consulta do pedido.


## <a name="next-steps"></a>Passos seguintes
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Expressões condicionais de motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Funcionalidades do motor de regras](cdn-rules-engine-reference-features.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)

