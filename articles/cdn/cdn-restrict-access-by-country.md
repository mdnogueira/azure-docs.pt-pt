---
title: "Restringir o conteúdo da CDN do Azure por país | Microsoft Docs"
description: "Saiba como restringir o acesso ao seu conteúdo da CDN do Azure utilizando a funcionalidade de filtragem de Georreplicação."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-azure-cdn-content-by-country"></a>Restringir o conteúdo da CDN do Azure por país

## <a name="overview"></a>Descrição geral
Quando um utilizador solicita o conteúdo, por predefinição, o conteúdo for servido independentemente de onde o utilizador efetuou este pedido de. Em alguns casos, poderá querer restringir o acesso ao conteúdo por país. Este tópico explica como utilizar o **filtragem Georreplicação** funcionalidade para configurar o serviço para permitir ou bloquear o acesso por país.

> [!IMPORTANT]
> Os produtos da Verizon e Akamai fornecem a mesma funcionalidade filtragem de georreplicação mas tem uma diferença pequena nos indicativos de país te suportam. Consulte o passo 3 para uma ligação para as diferenças.


Para obter informações sobre as considerações que se aplicam ao configurar este tipo de restrição, consulte o [considerações](cdn-restrict-access-by-country.md#considerations) secção no final do tópico.  

![Filtragem de país](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Passo 1: Definir o caminho do diretório
Selecione o ponto final no portal e localizar o separador Georreplicação filtragem no painel de navegação esquerdo para encontrar esta funcionalidade.

Quando configurar um filtro de país, tem de especificar o caminho relativo para a localização a que os utilizadores serão permitidos ou negados o acesso. Pode aplicar a filtragem de georreplicação para todos os seus ficheiros com "/" ou pastas selecionadas ao especificar os caminhos de diretório "imagens /". Também pode aplicar a filtragem de georreplicação para um único ficheiro especificando o ficheiro e deixando de fora da barra "/ imagens/cidade.png".

Filtro de caminho de diretório de exemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Passo 2: Definir a ação: bloquear ou permitir
**Bloco:** de países/regiões especificados será negado a utilizadores acesso a recursos de pedidos a partir de que o caminho de recursiva. Se não existem outras opções de filtragem de país tiverem sido configuradas para essa localização, em seguida, todos os outros utilizadores terão permissão de acesso.

**Permitir:** apenas os utilizadores de países/regiões especificados terão permissão de acesso a recursos de pedidos a partir de que o caminho de recursiva.

## <a name="step-3-define-the-countries"></a>Passo 3: Definir países/regiões
Selecione países/regiões que pretende bloquear ou permitir para o caminho. 

Por exemplo, a regra do bloqueio /Photos/Strasbourg/irá filtrar ficheiros, incluindo:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Indicativos de país
O **filtragem Georreplicação** funcionalidade utiliza indicativos de país para definir países/regiões do que um pedido será permitido ou bloqueado para um diretório protegido. Encontrará os indicativos de país na [indicativos de país do Azure CDN](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Considerações
* Pode demorar até 90 minutos para da Verizon ou alguns minutos com Akamai, para que as alterações ao seu país filtragem configuração entrem em vigor.
* Esta funcionalidade não suporta carateres universais (por exemplo, ' *').
* A configuração de filtragem de georreplicação associada com o caminho relativo será aplicado em modo recursivo para esse caminho.
* Apenas uma regra pode ser aplicada para o mesmo caminho relativo (não é possível criar vários filtros de país que apontam para o mesmo caminho relativo. No entanto, uma pasta pode ter vários filtros de país. Isto acontece devido à natureza recursiva de filtros de país. Por outras palavras, pode ser atribuída uma subpasta da pasta configurada anteriormente um filtro de noutro país.

