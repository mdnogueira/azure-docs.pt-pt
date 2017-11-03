---
title: "Códigos de erro de Media Services do Azure | Microsoft Docs"
description: "O tópico fornece uma descrição geral dos códigos de erro de Media Services do Azure."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 39886a955124429302609dd9d5a7c20ae7f498d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-error-codes"></a>Códigos de erro de Media Services do Azure
Quando utilizar os Media Services do Microsoft Azure, poderá receber os códigos de erro HTTP do serviço, dependendo de problemas, tais como os tokens de autenticação expirar a ações que não são suportadas nos Media Services. Segue-se uma lista de **códigos de erro HTTP** que podem ser devolvidos por serviços de suporte de dados e as possíveis causas para os mesmos.  

## <a name="400-bad-request"></a>Pedido de 400 incorreta
O pedido contém informação inválida e foi rejeitado devido a um dos seguintes motivos:

* Uma versão de API não suportada está especificada. Para a versão mais recente, consulte [programa de configuração para o desenvolvimento de API de REST de serviços de suporte de dados](media-services-rest-how-to-use.md).
* A versão de API dos serviços de suporte de dados não está especificada. Para obter informações sobre como especificar a versão de API, consulte [referência da API do REST do suporte de dados de serviços operações](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se estiver a utilizar o .NET ou SDKs de Java para ligar aos Media Services, a versão da API é especificada por si sempre que tente e efetuar algumas ação contra os Media Services.
  > 
  > 
* Foi especificada uma propriedade não definida. O nome de propriedade é na mensagem de erro. Podem ser especificadas apenas essas propriedades que são membros de uma determinada entidade. Consulte [referência da API REST do Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) para uma lista de entidades e as respetivas propriedades.
* Foi especificado um valor de propriedade inválido. O nome de propriedade é na mensagem de erro. Consulte a hiperligação anterior para tipos de propriedade válido e os respetivos valores.
* Um valor de propriedade está em falta e não é necessário.
* Parte do URL especificado contém um valor incorreto.
* Foi efetuada uma tentativa para atualizar uma propriedade de WriteOnce.
* Foi efetuada uma tentativa para criar uma tarefa que tenha um recurso de entrada com um AssetFile primário que não foi especificado ou não foi possível determinar.
* Foi efetuada uma tentativa de atualização de um localizador SAS. Os localizadores SAS só podem ser criados ou eliminados. Os localizadores de transmissão em fluxo pode ser atualizada. Para obter mais informações, consulte [localizadores](https://docs.microsoft.com/rest/api/media/operations/locator).
* Foi submetida uma consulta ou operação não suportada.

## <a name="401-unauthorized"></a>401 não autorizado
O pedido não foi possível autenticar (antes de pode ser autorizado) devido a um dos seguintes motivos:

* Cabeçalho de autenticação em falta.
* Valor de cabeçalho de autenticação incorreto.
  * O token expirou. 
  * O token contém uma assinatura inválida.

## <a name="403-forbidden"></a>403 Proibido
O pedido não é permitido devido a um dos seguintes motivos:

* A conta de Media Services não é possível localizar ou foi eliminada.
* A conta de Media Services está desativada e o tipo de pedido não é HTTP GET. Operações de serviço irão devolver uma resposta de 403.
* O token de autenticação não contém informações de credenciais do utilizador: AccountName e/ou SubscriptionId. Pode encontrar estas informações na extensão de IU dos serviços de suporte de dados para a sua conta de Media Services no Portal de gestão do Azure.
* Não é possível aceder ao recurso.
  
  * Foi efetuada uma tentativa para utilizar um MediaProcessor que não está disponível para a sua conta de Media Services.
  * Foi efetuada uma tentativa para atualizar um JobTemplate definido pelos Media Services.
  * Foi efetuada uma tentativa de substituir localizador do alguns outros conta dos Media Services.
  * Foi efetuada uma tentativa de substituir ContentKey alguns outros serviços de multimédia da conta.
* Não foi possível criar o recurso devido a uma quota de serviço que foi atingida para a conta de Media Services. Para obter mais informações sobre as quotas de serviço, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Não Encontrado
O pedido não é permitido num recurso devido a um dos seguintes motivos:

* Foi efetuada uma tentativa para atualizar uma entidade que não existe.
* Foi efetuada uma tentativa de eliminar uma entidade que não existe.
* Foi efetuada uma tentativa de criar uma entidade que liga a uma entidade que não existe.
* Foi efetuada uma tentativa de obter uma entidade que não existe.
* Foi efetuada uma tentativa para especificar uma conta de armazenamento que não está associada a conta de Media Services.  

## <a name="409-conflict"></a>Conflito 409
O pedido não é permitido devido a um dos seguintes motivos:

* Mais do que um AssetFile tem o nome especificado no elemento.
* Foi efetuada uma tentativa de criar uma segunda AssetFile primário dentro do elemento.
* Foi efetuada uma tentativa de criar um ContentKey com o Id especificado já é utilizado.
* Foi efetuada uma tentativa de criar um localizador com o Id especificado já é utilizado.
* Mais do que um IngestManifestFile tem o nome especificado dentro do IngestManifest.
* Foi efetuada uma tentativa para ligar uma segundo encriptação de armazenamento ContentKey ao elemento de encriptação de armazenamento.
* Foi efetuada uma tentativa para ligar o mesmo ContentKey para o elemento.
* Foi efetuada uma tentativa de criar um localizador para um recurso cujo contentor de armazenamento está em falta ou já não está associado o elemento.
* Foi efetuada uma tentativa de criar um localizador para um recurso que já tenha 5 localizadores em utilização. (O armazenamento do azure impõe o limite de cinco políticas de acesso partilhado num contentor de armazenamento.)
* Conta de armazenamento de um recurso de ligação para uma IngestManifestAsset não é o mesmo que a conta de armazenamento utilizada pela IngestManifest principal.  

## <a name="500-internal-server-error"></a>Erro interno do servidor 500
Durante o processamento do pedido, os Media Services encontra um erro que impede o processamento de continuar. Isto pode dever-se a um dos seguintes motivos:

* Criar um recurso ou a tarefa falha porque as informações sobre a quota da conta de Media Services serviço está temporariamente indisponível.
* Criar um contentor de armazenamento de BLOBs de recurso ou IngestManifest falha porque as informações de conta de armazenamento da conta estão temporariamente indisponíveis.
* Outro erro inesperado.

## <a name="503-service-unavailable"></a>Serviço 503 não está disponível
O servidor está atualmente não é possível receber pedidos. Este erro pode dever-se ao excessivos pedidos para o serviço. Os Media Services limitação mecanismo restringe a utilização de recursos para aplicações que tornam o pedido excessivo no serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de código de erro para obter informações mais detalhadas sobre o motivo obteve o erro 503. Este erro não significa sempre a limitação.
> 
> 

Descrições de estado possíveis são:

* "O servidor está ocupado. Execução anterior deste tipo de pedido demorou mais do que {0} segundos."
* "O servidor está ocupado. Mais do que {0} pedidos por segundo podem ser limitado."
* "O servidor está ocupado. Mais do que os pedidos de {0} num {1} segundos podem ser limitado."

Para resolver este erro, recomendamos que utilize a lógica de repetição de término exponencial. Isto significa utilizar progressivamente tempo decorrido entre tentativas de respostas de erro consecutivos.  Para obter mais informações, consulte [transitório falhas de processamento Application Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Se estiver a utilizar [Azure SDK de Media Services para .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), a lógica de repetição para o erro 503 tenha sido implementada pelo SDK.  
> 
> 

## <a name="see-also"></a>Veja Também
[Códigos de erro de gestão de serviços de multimédia](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

