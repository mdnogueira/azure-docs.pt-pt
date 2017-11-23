---
title: "Melhorar o desempenho através da compressão de ficheiros na CDN do Azure | Microsoft Docs"
description: "Saiba como melhorar a velocidade de transferência de ficheiro e aumenta o desempenho de carregamento de página por a compressão os ficheiros na CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b4e27ec57543daed35811fff347f457b0dd2cd5c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Melhorar o desempenho através da compressão de ficheiros na CDN do Azure
A compressão é um método simple e eficaz para melhorar a velocidade de transferência de ficheiro e aumentar o desempenho de carregamento da página ao reduzir o tamanho do ficheiro antes de ser enviada do servidor. Esta reduz os custos de largura de banda e fornece uma experiência mais reativa para os seus utilizadores.

Existem duas formas de ativar a compressão:

* Ative a compressão no seu servidor de origem. Neste caso, a CDN atravessa os ficheiros comprimidos e fornece-los aos clientes que solicitam-los.
* Ative a compressão diretamente nos servidores de limite de CDN. Neste caso, a CDN comprime os ficheiros e serve-las para os utilizadores finais, mesmo não são comprimidos pelo servidor de origem.

> [!IMPORTANT]
> As alterações de configuração da CDN podem demorar algum tempo para propagar através da rede.  Para <b>CDN do Azure da Akamai</b> perfis, propagação normalmente conclusão num minuto.  Para <b>CDN do Azure da Verizon</b> perfis, as suas alterações, normalmente, aplicam-se dentro de 90 minutos.  Se estiver a configurar a compressão pela primeira vez para o ponto final CDN, considere a aguardar 1-2 horas antes de resolver para garantir que as definições de compressão terem sido propagados para os POPs.
> 
> 

## <a name="enabling-compression"></a>Ativar a compressão
> [!NOTE]
> As camadas Standard e Premium CDN fornecem a mesma funcionalidade de compressão, mas a interface de utilizador diferente.  Para obter mais informações sobre as diferenças entre camadas Standard e Premium CDN, consulte [descrição geral da CDN do Azure](cdn-overview.md).
> 
> 

### <a name="standard-tier"></a>Escalão Standard
> [!NOTE]
> Esta secção aplica-se a **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis.
> 
> 

1. Na página de perfil de CDN, clique em ponto final de CDN que pretende gerir.
   
    ![Pontos finais de perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    Abre a página de ponto final da CDN.
2. Clique em de **configurar** botão.
   
    ![Botão de gerir do perfil de CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    Abre a página de configuração da CDN.
3. Ativar **compressão**.
   
    ![Opções de compressão de CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Utilize os tipos predefinidos ou modifique a lista por remover ou a adição de tipos de ficheiro.
   
   > [!TIP]
   > Embora seja possível, não se recomenda a aplicar a compressão para formatos comprimidos. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 
 
5. Depois de efetuar as alterações, clique em de **guardar** botão.

### <a name="premium-tier"></a>Escalão Premium
> [!NOTE]
> Esta secção aplica-se a **CDN do Azure Premium da Verizon** perfis.
> 
> 

1. Na página de perfil de CDN, clique em de **gerir** botão.
   
    ![Botão de gerir do perfil de CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **HTTP grande** separador, em seguida, coloque o cursor sobre o **definições da Cache** flyout.  Clique em **compressão**.

    ![Seleção de compressão do ficheiro](./media/cdn-file-compression/cdn-compress-select.png)
   
    Opções de compressão são apresentadas.
   
    ![Opções de compressão do ficheiro](./media/cdn-file-compression/cdn-compress-files.png)
3. Ativar a compressão clicando a **ativar a compressão** botão de opção.  Introduza os tipos de MIME que pretende comprimir como uma lista delimitada por vírgulas (sem espaços) a **tipos de ficheiro** caixa de texto.
   
   > [!TIP]
   > Embora seja possível, não se recomenda a aplicar a compressão para formatos comprimidos. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 
4. Depois de efetuar as alterações, clique em de **atualização** botão.

## <a name="compression-rules"></a>Regras de compressão
Estas tabelas descrevem o comportamento de compressão da CDN do Azure para cada cenário.

> [!IMPORTANT]
> Para **CDN do Azure da Verizon** perfis são comprimidos (Standard e Premium), ficheiros apenas elegíveis.  Para ser elegível para compressão, um ficheiro tem de:
> 
> * Ser maior do que 128 bytes.
> * Pode ser inferior a 1 MB.
> 
> Estes perfis suportam **gzip** (GNU zip), **deflate**, **bzip2**, ou **br** (Brotli) codificação. Para Brotli codificação, a compressão tem de ser feito na origem. O browser/cliente terá de enviar o pedido de codificação Brotli e o elemento comprimido tem comprimido no lado origem primeiro. 

> [!IMPORTANT]
> Para **CDN do Azure da Akamai** perfis, todos os ficheiros são elegíveis para compressão. No entanto, um ficheiro tem de ser um tipo de MIME que foi [configurado para compressão](#enabling-compression).
> 
>Estes perfis suportam apenas **gzip** codificação. Quando solicita um ponto final do perfil **gzip** codificado ficheiros, que são sempre pediram da origem, independentemente do pedido do cliente. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compressão desativada ou o ficheiro não é elegível para compressão
| Formato de pedido de cliente (através do cabeçalho de codificação de aceitar) | Formato de ficheiro em cache | Resposta da CDN para o cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos | |
| Comprimidos |Não comprimida |Não comprimida | |
| Comprimidos |Não colocar em cache |Comprimidos ou descomprimidos |Depende da resposta de origem |
| Não comprimida |Comprimidos |Não comprimida | |
| Não comprimida |Não comprimida |Não comprimida | |
| Não comprimida |Não colocar em cache |Não comprimida | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Compressão ativada e o ficheiro é elegível para compressão
| Formato de pedido de cliente (através do cabeçalho de codificação de aceitar) | Formato de ficheiro em cache | Resposta da CDN para o cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos |CDN transcodes entre formatos suportados |
| Comprimidos |Não comprimida |Comprimidos |CDN efetua a compressão |
| Comprimidos |Não colocar em cache |Comprimidos |CDN executa compressão se origem devolve descomprimida.  **CDN do Azure da Verizon** passa o um ficheiro no primeiro pedido e, em seguida, comprimir e coloca em cache o ficheiro para pedidos subsequentes.  Ficheiros com o `Cache-Control: no-cache` cabeçalho nunca são comprimidos. |
| Não comprimida |Comprimidos |Não comprimida |A descompressão efetua a CDN |
| Não comprimida |Não comprimida |Não comprimida | |
| Não comprimida |Não colocar em cache |Não comprimida | |

## <a name="media-services-cdn-compression"></a>Serviços de multimédia compressão de CDN
Para pontos finais de transmissão em fluxo CDN de serviços de suporte de dados ativada, a compressão está ativada por predefinição para os seguintes tipos de conteúdo: 
- aplicação/vnd.ms-sstr + xml 
- aplicação/dash + xml
- application/vnd.Apple.mpegurl
- aplicação/f4m + xml. 

Não é possível ativar ou desativar a compressão para os tipos de mencionadas utilizando o portal do Azure.  

## <a name="see-also"></a>Consultar também
* [Troubleshooting CDN file compression](cdn-troubleshoot-compression.md) (Resolver problemas de compressão de ficheiros da CDN)    

