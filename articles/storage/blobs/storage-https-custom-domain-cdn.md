---
title: "Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS"
description: "Saiba como integrar a CDN do Azure com o blob storage para aceder a blobs com domínios personalizados através de HTTPS"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.openlocfilehash: 6bad04df324a374f6e8473890345cf516322abd6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS

Azure entrega rede conteúdos (CDN) suporta agora o HTTPS para nomes de domínio personalizado.
Pode tirar partido desta funcionalidade para aceder aos blobs de armazenamento com o domínio personalizado através de HTTPS. Para tal, primeiro terá de ativar a CDN do Azure no seu ponto final do blob e mapear a CDN para um nome de domínio personalizado. Depois de seguir estes passos, ativar HTTPS para o domínio personalizado é simplificada através de ativação de um clique, gestão de certificados concluída e todos os sem custos adicionais para preços da CDN normal.

Esta capacidade é importante porque permite-lhe proteger a privacidade e a integridade dos dados dos seus dados de aplicação web confidenciais em trânsito. Utilizar o protocolo SSL para servir o tráfego através de HTTPS garante que os dados são encriptados quando é enviado através da internet. HTTPS fornece confiança e a autenticação e protege as suas aplicações web contra ataques.

> [!NOTE]
> Além de fornecer suporte SSL para nomes de domínio personalizado, a CDN do Azure pode ajudar a dimensionar a sua aplicação para fornecer conteúdo de largura de banda alta em todo o mundo.
> Para obter mais informações, consulte [descrição geral da CDN do Azure](../../cdn/cdn-overview.md).
>
>

## <a name="quick-start"></a>Início rápido

Estes são os passos necessários para ativar HTTPS para o ponto final de armazenamento de BLOBs personalizado:

1.  [Integrar uma conta de armazenamento do Azure com o Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Este artigo explica como criar uma conta do storage no Portal do Azure se não o fez, já.
2.  [Mapear conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Ativar HTTPS, num domínio personalizado de CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso partilhado

Se o ponto final de armazenamento de blob está configurado para não permitir acesso de leitura anónimo, terá de fornecer um [assinatura de acesso partilhado (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token em cada pedido de efetuar o domínio personalizado. Por predefinição, os pontos finais de armazenamento de BLOBs não permitir acesso de leitura anónimo. Consulte [gerir o acesso de leitura anónimo a contentores e blobs](storage-manage-access-to-resources.md) para obter mais informações sobre assinaturas de acesso partilhado.

CDN do Azure não Respeitamos a quaisquer restrições adicionadas para o token SAS. Por exemplo, todos os tokens SAS tem um período de tempo de expiração. Isto significa que o conteúdo ainda pode ser acedida com uma SAS expirados até esse conteúdo é removido de nós de limite CDN. Pode controlar quanto dados é colocado em cache na CDN ao definir o cabeçalho de resposta de cache. Consulte [gerir a expiração de blobs de armazenamento do Azure na CDN do Azure](../../cdn/cdn-manage-expiration-of-blob-content.md) para obter instruções.

Se criar vários URLs de SAS para o mesmo ponto final do blob, recomendamos a ativação da cadeia de consulta a colocação em cache para a CDN do Azure. Isto é para se certificar de que cada URL é tratado como uma entidade única. Consulte [controlar o comportamento com cadeias de consulta a colocação em cache do Azure CDN](../../cdn/cdn-query-string.md) para obter mais informações.

## <a name="http-to-https-redirection"></a>HTTP para o redirecionamento de HTTPS

Pode optar por redirecionar o tráfego HTTP para HTTPS. Isto requer a utilização de premium a CDN do Azure da Verizon da oferta. Terá de [comportamento substituir HTTP utilizando o motor de regras da CDN do Azure](../../cdn/cdn-rules-engine.md) com a seguinte regra:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Nome de ponto final de Cdn" refere-se para o nome que configurou para o ponto final CDN. Pode selecionar este valor na lista pendente. "Caminho de origem" refere-se para o caminho na sua conta de armazenamento de origem onde reside o conteúdo estático.
Se estiver a alojar todo o conteúdo estático num contentor único, substitua "caminho de origem" com o nome do contentor.

Para obter uma descrição mais aprofundada para as regras, consulte o [funcionalidades do motor de regras de CDN do Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Preços e faturação

Ao aceder a blobs através de uma CDN do Azure, paga [os preços de armazenamento de BLOBs](https://azure.microsoft.com/pricing/details/storage/blobs/) para o tráfego entre os nós de limite e a origem (armazenamento de BLOBs), e [os preços da CDN](https://azure.microsoft.com/pricing/details/cdn/) para dados acedidos a partir de nós edge.

Por exemplo, imagine que tem uma conta de armazenamento nos EUA oeste, que está a ser acedidos através de uma CDN do Azure. Se no RU alguém para o acesso a um dos blobs nessa conta de armazenamento através da CDN, o Azure verifica primeiro nó de extremidade mais próximo RU para esse blob. Se encontrado, acede essa cópia do blob e utilizará os preços da CDN, porque está a ser acedido na CDN. Se não for encontrado, Azure vai copiar o blob para o nó de extremidade, o que irá resultar em encargos de saída e a transação conforme especificado nos preços de armazenamento de Blob e, em seguida, aceder ao ficheiro no nó de extremidade, o que resultará em faturação da CDN.

Quando observar a [CDN página de preços](https://azure.microsoft.com/pricing/details/cdn/), tenha em atenção que o suporte de HTTPS para nomes de domínio personalizados só está disponível para a CDN do Azure da Verizon produtos (Standard e Premium).

## <a name="next-steps"></a>Passos seguintes

[Configurar um nome de domínio personalizado para o ponto de final do Blob storage](storage-custom-domain-name.md)
