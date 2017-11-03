---
title: "Descrição geral de ponto final de transmissão em fluxo de serviços em suportes de dados do Azure | Microsoft Docs"
description: "Este tópico fornece uma descrição geral dos Media Services do Azure pontos finais de transmissão em fluxo."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: cfowler
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: e454778c558b9c17c47ad9eb651737aa0b5e2605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="streaming-endpoints-overview"></a>Descrição geral de pontos finais de transmissão em fluxo 

##<a name="overview"></a>Descrição geral

No Microsoft Azure dos Media Services (AMS), um **ponto final de transmissão em fluxo** representa um serviço de transmissão em fluxo que pode proporcionar o conteúdo diretamente a uma aplicação de leitor de cliente ou a rede de entrega um conteúdos (CDN) para uma maior distribuição. Os Media Services também fornecem uma integração perfeita CDN do Azure. O fluxo de saída de um serviço de StreamingEndpoint pode ser uma transmissão em fluxo em direto, um vídeo a pedido ou transferência progressiva do seu elemento na sua conta de Media Services. Cada conta de Media Services do Azure inclui um predefinido StreamingEndpoint. Lidam adicionais pode ser criadas com a conta. Existem duas versões do lidam, 1.0 e 2.0. A partir de com 10 de Janeiro de 2017, as contas recentemente criadas do AMS irão incluir versão 2.0 **predefinido** StreamingEndpoint. Os pontos finais transmissão em fluxo adicionais que adicionar a esta conta também será versão 2.0. Esta alteração não irá afetar as contas existentes; lidam existente será versão 1.0 e pode ser atualizados para a versão 2.0. Com esta alteração vão ocorrer alterações de comportamento, faturação e funcionalidade (para obter mais informações, consulte o **tipos e versões de transmissão em fluxo** secção documentados abaixo).

Além disso, a partir da versão 2.15 (lançadas em Janeiro de 2017), Media Services do Azure adicionadas as seguintes propriedades para a entidade de ponto final de transmissão em fluxo: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Para uma descrição geral detalhada destas propriedades, consulte [isto](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Quando cria uma conta de Media Services do Azure uma predefinição de ponto final padrão de transmissão em fluxo é criado para si no **parado** estado. Não é possível eliminar o ponto final de transmissão em fluxo de predefinido. Consoante a disponibilidade da CDN do Azure na região de destino, por predefinição recém-criado predefinição ponto final de transmissão em fluxo também inclui "StandardVerizon" CDN integração do fornecedor. 

>[!NOTE]
>Integração da CDN do Azure pode ser desativada antes de iniciar o ponto final de transmissão em fluxo.

Este tópico fornece uma descrição geral sobre as funcionalidades principais que são fornecidos por pontos finais de transmissão em fluxo.

## <a name="streaming-types-and-versions"></a>Tipos de transmissão em fluxo e versões

### <a name="standardpremium-types-version-20"></a>Tipos de padrão/Premium (versão 2.0)

Começando com o lançamento de Janeiro de 2017 dos Media Services, tem dois tipos de transmissão em fluxo: **padrão** e **Premium**. Estes tipos são parte da versão de ponto final de transmissão em fluxo "2.0".

Tipo|Descrição
---|---
**Standard**|Esta é a opção predefinida que funciona para a maioria dos cenários.<br/>Com esta opção, obter o SLA/limitado, primeiro 15 dias depois de iniciar o ponto final de transmissão em fluxo está livre.<br/>Se criar mais de uma transmissão em fluxo pontos finais, apenas a primeira é gratuita para os primeira 15 dias, outros são faturados assim que iniciá-los. <br/>Tenha em atenção que versão de avaliação gratuita aplica-se apenas às contas de serviços de suporte de dados recentemente criado e o ponto final de transmissão em fluxo predefinido. Os pontos finais de transmissão em fluxo existentes e os pontos finais de transmissão em fluxo adicionalmente criados não inclui o período de avaliação gratuita, mesmo que sejam atualizados para a versão 2.0 ou são criadas como versão 2.0.
**Premium**|Esta opção é adequada para profissionais cenários que necessitam de escala superior ou um controlo.<br/>SLA de variável com base na capacidade premium transmissão em fluxo unidade (SU) adquirida, dedicados pontos finais de transmissão em fluxo em direto num ambiente isolado e não competem para ter recursos.

Para obter mais informações, consulte o **transmissão em fluxo comparar tipos** secção a seguir.

### <a name="classic-type-version-10"></a>Tipo clássico (versão 1.0)

Para os utilizadores que criar contas de AMS antes do lançamento 10 de Janeiro de 2017, tem um **clássico** tipo de um ponto final de transmissão em fluxo. Este tipo faz parte da versão de ponto final de transmissão em fluxo "1.0".

Se o **versão "1.0"** ponto final de transmissão em fluxo tem > = 1 premium de transmissão em fluxo unidades (SU), este ponto final de transmissão em fluxo premium e fornecem todas as funcionalidades de AMS (tal como o **padrão/Premium** tipo) sem quaisquer passos de configuração adicionais.

>[!NOTE]
>**Clássico** pontos finais de transmissão em fluxo (versão "1.0" e 0 SU), fornece funcionalidades limitadas e não inclui um SLA. Recomenda-se para migrar para **padrão** tipo para obter uma melhor experiência e a utilizar funcionalidades como o empacotamento dinâmico ou encriptação e outras funcionalidades que são fornecidos com o **padrão** tipo. Para migrar para o **padrão** tipo, vá para o [portal do Azure](https://portal.azure.com/) e selecione **recusa no padrão**. Para obter mais informações sobre a migração, consulte o [migração](#migration-between-types) secção.
>
>Cuidado com que esta operação não pode ser revertida e tem um impacto de preço.
>
 
## <a name="comparing-streaming-types"></a>Comparar tipos de transmissão em fluxo

### <a name="versions"></a>Versões

|Tipo|StreamingEndpointVersion|ScaleUnits|CDN|Faturação|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Clássica|1.0|0|ND|Gratuito|ND|
|Ponto Final de Transmissão em Fluxo Standard|2.0|0|Sim|Pago|Sim|
|Unidades de Transmissão em Fluxo Premium|1.0|>0|Sim|Pago|Sim|
|Unidades de Transmissão em Fluxo Premium|2.0|>0|Sim|Pago|Sim|

### <a name="features"></a>Funcionalidades

Funcionalidade|Standard|Premium
---|---|---
Liberte primeiro 15 dias| Sim |Não
Débito |Até 600 Mbps quando não for utilizada o CDN do Azure. Escalas CDN.|200 Mbps por transmissão em fluxo unidade (SU). Escalas CDN.
SLA | 99.9|99,9 (200 Mbps por SU).
CDN|CDN do Azure, terceiros CDN ou nenhum CDN.|CDN do Azure, terceiros CDN ou nenhum CDN.
A faturação é proporcional| Diariamente|Diariamente
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Auto escalas até o débito de destino.|Unidades de transmissão em fluxo adicionais
Anfitrião de filtragem/G20/personalizada IP|Sim|Sim
Transferência progressiva|Sim|Sim
Utilização recomendada |Recomenda-se de que a vasta maioria dos cenários de transmissão em fluxo.|Utilização profissionais.<br/>Se considerar que poderá ter necessidades para além de padrão. Contacte-nos (amsstreaming microsoft.com,) se espera que um tamanho de audiência em simultâneo com mais de 50 000 visualizadores autorizados.


## <a name="migration-between-types"></a>Migração entre tipos

Do | Para | Ação
---|---|---
Clássica|Standard|Têm de optar ativamente por participar
Clássica|Premium| Escala (adicional de unidades de transmissão em fluxo)
Padrão/Premium|Clássica|Não está disponível (se a versão do ponto final de transmissão em fluxo é 1.0. É permitido alterar para clássico com a definição scaleunits para "0")
Padrão (com/sem CDN)|Premium com as mesmas configurações|Permitido no **iniciado** estado. (através do portal do Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no **iniciado** Estado (através do portal do Azure)
Padrão (com/sem CDN)|Premium com configuração diferente|Permitido no **parado** Estado (através do portal do Azure). Não é permitida no estado de execução.
Premium (com/sem CDN)|Standard com configuração diferente|Permitido no **parado** Estado (através do portal do Azure). Não é permitida no estado de execução.
Versão 1.0 com SU > = 1 de CDN|Padrão/Premium não CDN|Permitido no **parado** estado. Não é permitido no **iniciado** estado.
Versão 1.0 com SU > = 1 de CDN|Standard com/sem CDN|Permitido no **parado** estado. Não é permitido no **iniciado** estado. Versão 1.0 CDN irá ser eliminado e nova criada e iniciado.
Versão 1.0 com SU > = 1 de CDN|Premium com/sem CDN|Permitido no **parado** estado. Não é permitido no **iniciado** estado. CDN clássico irá ser eliminado e nova criada e iniciado.

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

