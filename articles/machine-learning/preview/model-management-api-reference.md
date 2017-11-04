---
title: "Criar uma imagem de Docker para gestão de modelo no Azure Machine Learning | Microsoft Docs"
description: "Este artigo descreve os passos para criar uma imagem de Docker para o seu serviço web."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 1939a18fbaf0aed0f62ac9e7641b9901ec4762ea
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Referência da API de conta de gestão do Machine Learning modelo do Azure

Para obter informações sobre como configurar o ambiente de implementação, consulte [configuração da conta de gestão de modelo](model-management-configuration.md).

A API de gestão de conta do Azure Machine Learning modelo implementa as seguintes operações:

- Registo de modelo
- Criação do manifesto
- Criação de imagens do docker
- Criação do serviço Web

Pode utilizar esta imagem para criar um serviço web ou localmente ou um cluster do serviço de contentor Azure remoto ou outro ambiente suportado de Docker à sua escolha.

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que já leu os passos de instalação no [instalar e criar o guia de introdução](quickstart-installation.md) documento.

É necessário o seguinte antes de continuar:
1. Modelo de aprovisionamento da conta de gestão
2. Criação do ambiente para implementar e gerir modelos
3. Um modelo de Machine Learning

### <a name="azure-ad-token"></a>Token do Azure AD
Quando estiver a utilizar a CLI do Azure, inicie sessão utilizando `az login`. A CLI utiliza o token do Azure Active Directory (Azure AD) a partir do ficheiro .azure. Se pretender utilizar as APIs, tem as seguintes opções.

##### <a name="acquire-the-azure-ad-token-manually"></a>Adquirir o token do Azure AD manualmente
Pode utilizar `az login` e obter a versão mais recente token a partir do ficheiro .azure no seu diretório raiz.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Adquirir o token do Azure AD através de programação
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Depois de criar o principal de serviço, guarde o resultado. Agora pode utilizá-la para obter um token do Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
O token é colocado num cabeçalho de autorização para chamadas de API.


## <a name="register-a-model"></a>Registar um modelo

O passo de registo do modelo regista o seu modelo de Machine Learning com a conta de gestão de modelo do Azure que criou. Este registo permite que os modelos e as respetivas versões que estão atribuídos ao tempo de registo de controlo. O utilizador fornece o nome do modelo. Registo subsequente dos modelos com o mesmo nome gera uma nova versão e um ID.

### <a name="request"></a>Pedir

| Método | URI do pedido |
|------------|------------|
| POST |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / modelos 
 |
### <a name="description"></a>Descrição
Regista um modelo.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| Modelo | Corpo | Payload que é utilizada para registar um modelo. | Sim | [Modelo](#model) |


### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | OK. O registo do modelo foi concluída com êxito. | [Modelo](#model) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>A lista de modelos de uma conta de consulta
### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / modelos 
 |
### <a name="description"></a>Descrição
Consulta a lista de modelos de uma conta. Pode filtrar a lista de resultados por etiqueta e o nome. Se nenhum filtro é transmitido, a consulta lista todos os modelos na conta. A lista devolvida é paginated e a contagem de itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| nome | consulta | Nome do objeto. | Não | Cadeia |
| Etiqueta | consulta | Etiqueta de modelo. | Não | Cadeia |
| Contagem | consulta | Número de itens para obter uma página de início. | Não | Cadeia |
| $skipToken | consulta | Token de continuação para obter a página seguinte. | Não | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [PaginatedModelList](#paginatedmodellist) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obter os detalhes de modelo
### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /models/ {id}  
 |

### <a name="description"></a>Descrição
Obtém um modelo por ID.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de objeto. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [Modelo](#model) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registar um manifesto com o modelo registado e todas as dependências

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| POST |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / manifestos | 

### <a name="description"></a>Descrição
Regista um manifesto com o modelo registado e todas as respetivas dependências.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| manifestRequest | Corpo | Payload que é utilizada para registar um manifesto. | Sim | [Manifesto](#manifest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Registo de manifesto foi concluída com êxito. | [Manifesto](#manifest) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>A lista de manifestos numa conta de consulta

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / manifestos | 

### <a name="description"></a>Descrição
Consulta a lista de manifestos numa conta. Pode filtrar a lista de resultados por ID de modelo e nome de manifesto. Se nenhum filtro é transmitido, a consulta lista todos os manifestos na conta. A lista devolvida é paginated e a contagem de itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| modelId | consulta | ID de modelo. | Não | Cadeia |
| ManifestName | consulta | Nome do manifesto. | Não | Cadeia |
| Contagem | consulta | Número de itens para obter uma página de início. | Não | Cadeia |
| $skipToken | consulta | Token de continuação para obter a página seguinte. | Não | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [PaginatedManifestList](#paginatedmanifestlist) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obter os detalhes de manifesto

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /manifests/ {id} | 

### <a name="description"></a>Descrição
Obtém o manifesto por ID.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de objeto. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [Manifesto](#manifest) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Criar uma imagem

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| POST |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / imagens | 

### <a name="description"></a>Descrição
Cria uma imagem como uma imagem de Docker no registo de contentor do Azure.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| imageRequest | Corpo | Payload que é utilizado para criar uma imagem. | Sim | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de localização de operação assíncrona. Uma chamada GET irá mostrar o estado da tarefa de criação de imagem. | Localização de operação |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>A lista de imagens numa conta de consulta

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / imagens | 

### <a name="description"></a>Descrição
Consulta a lista de imagens de uma conta. Pode filtrar a lista de resultados ao manifesto ID e nome. Se nenhum filtro é transmitido, a consulta lista todas as imagens na conta. A lista devolvida é paginated e a contagem de itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| ManifestId | consulta | ID de manifesto. | Não | Cadeia |
| ManifestName | consulta | Nome do manifesto. | Não | Cadeia |
| Contagem | consulta | Número de itens para obter uma página de início. | Não | Cadeia |
| $skipToken | consulta | Token de continuação para obter a página seguinte. | Não | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [PaginatedImageList](#paginatedimagelist) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Obter os detalhes da imagem

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /images/ {id} | 

### <a name="description"></a>Descrição
Obtém uma imagem por ID.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de imagem. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [Imagem](#image) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Criar um serviço

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| POST |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / serviços | 

### <a name="description"></a>Descrição
Cria um serviço a partir de uma imagem.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| serviceRequest | Corpo | Payload que é utilizado para criar um serviço. | Sim | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de localização de operação assíncrona. Uma chamada GET irá mostrar o estado da tarefa de criação de serviço. | Localização de operação |
| 409 | Já existe um serviço com o nome especificado. |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>A lista de serviços de uma conta de consulta

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / serviços | 

### <a name="description"></a>Descrição
Consulta a lista de serviços de uma conta. Pode filtrar a lista de resultados por nome/ID de modelo, manifesto/ID de nome, ID de imagem, nome do serviço ou ID de recurso de computação para Machine Learning. Se nenhum filtro é transmitido, a consulta lista todos os serviços na conta. A lista devolvida é paginated e a contagem de itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| ServiceName | consulta | Nome do serviço. | Não | Cadeia |
| modelId | consulta | Nome do modelo. | Não | Cadeia |
| modelName | consulta | ID de modelo. | Não | Cadeia |
| ManifestId | consulta | ID de manifesto. | Não | Cadeia |
| ManifestName | consulta | Nome do manifesto. | Não | Cadeia |
| imageId | consulta | ID de imagem. | Não | Cadeia |
| computeResourceId | consulta | ID de recurso de computação Machine Learning. | Não | Cadeia |
| Contagem | consulta | Número de itens para obter uma página de início. | Não | Cadeia |
| $skipToken | consulta | Token de continuação para obter a página seguinte. | Não | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [PaginatedServiceList](#paginatedservicelist) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obter os detalhes do serviço

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Descrição
Obtém um serviço por ID.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de objeto. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [ServiceResponse](#serviceresponse) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Atualizar um serviço

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| COLOCAR |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Descrição
As atualizações de serviço existente.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de objeto. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| serviceUpdateRequest | Corpo | Payload que é utilizado para atualizar um serviço existente. | Sim |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de localização de operação assíncrona. Uma chamada GET irá mostrar o estado da tarefa de atualização do serviço. | Localização de operação |
| 404 | O serviço com o ID especificado não existe. |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Eliminar um serviço

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| ELIMINAR |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Descrição
Elimina um serviço.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de objeto. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. |  |
| 204 | O serviço com o ID especificado não existe. |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obter chaves de serviço

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / chaves | 

### <a name="description"></a>Descrição
Obtém as chaves de serviço.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID do serviço. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [AuthKeys](#authkeys)
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Voltar a gerar chaves de serviço

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| POST |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / chaves | 

### <a name="description"></a>Descrição
Gera de novo chaves de serviço e devolve-os.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID do serviço. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| regenerateKeyRequest | Corpo | Payload que é utilizado para atualizar um serviço existente. | Sim | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [AuthKeys](#authkeys)
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>A lista de implementações de uma conta de consulta

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /accounts/ /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} {accountName} / implementações | 

### <a name="description"></a>Descrição
Consulta a lista de implementações de uma conta. Pode filtrar a lista de resultados por ID de serviço, que irá devolver apenas as implementações criadas para o serviço específico. Se nenhum filtro é transmitido, a consulta lista todas as implementações na conta.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |
| serviceId | consulta | ID do serviço. | Não | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [DeploymentList](#deploymentlist) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obter os detalhes da implementação

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /deployments/ {id} | 

### <a name="description"></a>Descrição
Obtém a implementação por ID.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de implementação. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [Implementação](#deployment) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obter os detalhes de operação

### <a name="request"></a>Pedir
| Método | URI do pedido |
|------------|------------|
| INTRODUÇÃO |  /API subscrições / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /operations/ {id} | 

### <a name="description"></a>Descrição
Obtém o estado da operação assíncrona por ID de operação.

### <a name="parameters"></a>Parâmetros
| Nome | Localizado na | Descrição | Necessário | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Caminho | ID da subscrição do Azure. | Sim | Cadeia |
| resourceGroupName | Caminho | Nome do grupo de recursos no qual a conta de gestão de modelo está localizada. | Sim | Cadeia |
| accountName | Caminho | Nome da conta de gestão de modelo. | Sim | Cadeia |
| ID | Caminho | ID de operação. | Sim | Cadeia |
| versão de API | consulta | Versão do fornecedor de recursos Microsoft.Machine.Learning API a utilizar. | Sim | Cadeia |
| Autorização | cabeçalho | Token de autorização. Deve ser algo semelhante a "Portador XXXXXX." | Sim | Cadeia |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Êxito. | [OperationStatus](#asyncoperationstatus) |
| predefinição | Resposta de erro que descreve o motivo pelo qual a operação falhou. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definições

<a name="asset"></a>
### <a name="asset"></a>Recurso
O objeto de recurso que será necessária durante a criação de imagens do Docker.


|Nome|Descrição|Esquema|
|---|---|---|
|**ID**  <br>*opcional*|ID de recurso.|Cadeia|
|**mimeType**  <br>*opcional*|Tipo MIME de conteúdo de modelos. Para obter mais informações sobre o tipo de MIME, consulte o [lista de tipos de suportes de dados IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|Cadeia|
|**Descompactar**  <br>*opcional*|Indica onde é necessário descompactar o conteúdo durante a criação de imagens do Docker.|Valor booleano|
|**URL**  <br>*opcional*|URL de localização do recurso.|Cadeia|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
O estado da operação assíncrona.

*Tipo*: enum (NotStarted, em execução, cancelada, com êxito, falha)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
O estado da operação.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdTime**  <br>*opcional*  <br>*só de leitura*|Hora de criação de operação de assíncrona (UTC).|cadeia (data / hora)|
|**endTime**  <br>*opcional*  <br>*só de leitura*|Hora de fim da operação assíncrona (UTC).|cadeia (data / hora)|
|**erro**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*opcional*|ID de operação assíncrona.|Cadeia|
|**operationType**  <br>*opcional*|Tipo de operação assíncrona.|enumeração (imagem, Service)|
|**resourceLocation**  <br>*opcional*|Recursos criados ou atualizados pela operação assíncrona.|Cadeia|
|**Estado**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
As chaves de autenticação para um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**primaryKey**  <br>*opcional*|Chave primária.|Cadeia|
|**secondaryKey**  <br>*opcional*|Chave secundária.|Cadeia|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Definições para o autoscaler.


|Nome|Descrição|Esquema|
|---|---|---|
|**autoscaleEnabled**  <br>*opcional*|Ativar ou desativar o autoscaler.|Valor booleano|
|**maxReplicas**  <br>*opcional*|Número máximo de réplicas pod até a dimensionar.  <br>**Valor mínimo**:`1`|número inteiro|
|**minReplicas**  <br>*opcional*|Número mínimo de réplicas pod Dimensionar para baixo para.  <br>**Valor mínimo**:`0`|número inteiro|
|**refreshPeriodInSeconds**  <br>*opcional*|Atualize o tempo para o acionador de dimensionamento automático.  <br>**Valor mínimo**:`1`|número inteiro|
|**targetUtilization**  <br>*opcional*|Percentagem de utilização que aciona o dimensionamento automático.  <br>**Valor mínimo**:`0`  <br>**O valor máximo**:`100`|número inteiro|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
O recurso de computação de Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**ID**  <br>*opcional*|ID de recurso.|Cadeia|
|**tipo**  <br>*opcional*|Tipo de recurso.|enumeração (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuração para reservar os recursos para um contentor no cluster.


|Nome|Descrição|Esquema|
|---|---|---|
|**CPU**  <br>*opcional*|Especifica a reserva da CPU. Formato para Kubernetes: consulte [significado da CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|Cadeia|
|**memória**  <br>*opcional*|Especifica a reserva de memória. Formato para Kubernetes: consulte [significado da memória](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|Cadeia|


<a name="deployment"></a>
### <a name="deployment"></a>Implementação
Uma instância de uma implementação do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*só de leitura*|Hora de criação de implementação (UTC).|cadeia (data / hora)|
|**expiredAt**  <br>*opcional*  <br>*só de leitura*|Implementação expirou hora (UTC).|cadeia (data / hora)|
|**ID**  <br>*opcional*|ID de implementação.|Cadeia|
|**imageId**  <br>*opcional*|ID de imagem associado esta implementação.|Cadeia|
|**serviceName**  <br>*opcional*|Nome do serviço.|Cadeia|
|**Estado**  <br>*opcional*|Estado da implementação atual.|Cadeia|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Uma matriz de objetos de implementação.

*Tipo*: <[implementação](#deployment)> matriz


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Detalhe do erro de serviço de gestão de modelo.


|Nome|Descrição|Esquema|
|---|---|---|
|**código**  <br>*necessário*|Código de erro.|Cadeia|
|**mensagem**  <br>*necessário*|Mensagem de erro.|Cadeia|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Um objeto de erro do serviço de gestão de modelo.


|Nome|Descrição|Esquema|
|---|---|---|
|**código**  <br>*necessário*|Código de erro.|Cadeia|
|**Detalhes**  <br>*opcional*|Matriz de objectos de detalhe do erro.|<[ErrorDetail](#errordetail)> matriz|
|**mensagem**  <br>*necessário*|Mensagem de erro.|Cadeia|
|**statusCode**  <br>*opcional*|Código de estado HTTP.|número inteiro|


<a name="image"></a>
### <a name="image"></a>Imagem
A imagem do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**computeResourceId**  <br>*opcional*|ID do ambiente criado no recurso de computação de Machine Learning.|Cadeia|
|**createdTime**  <br>*opcional*|Hora de criação de imagens (UTC).|cadeia (data / hora)|
|**creationState**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**Descrição**  <br>*opcional*|Texto de descrição da imagem.|Cadeia|
|**erro**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*opcional*|ID de imagem.|Cadeia|
|**imageBuildLogUri**  <br>*opcional*|URI dos registos carregados na compilação de imagem.|Cadeia|
|**imageLocation**  <br>*opcional*|Cadeia de localização de registo de contentor do Azure para a imagem de criação.|Cadeia|
|**imageType**  <br>*opcional*||[ImageType](#imagetype)|
|**manifesto**  <br>*opcional*||[Manifesto](#manifest)|
|**nome**  <br>*opcional*|Nome da imagem.|Cadeia|
|**versão**  <br>*opcional*|Versão da imagem definido pelo serviço de gestão de modelo.|número inteiro|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
Um pedido para criar uma imagem do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**computeResourceId**  <br>*necessário*|ID do ambiente criado no recurso de computação de Machine Learning.|Cadeia|
|**Descrição**  <br>*opcional*|Texto de descrição da imagem.|Cadeia|
|**imageType**  <br>*necessário*||[ImageType](#imagetype)|
|**manifestId**  <br>*necessário*|ID do manifesto a partir da qual a imagem será criada.|Cadeia|
|**nome**  <br>*necessário*|Nome da imagem.|Cadeia|


<a name="imagetype"></a>
### <a name="imagetype"></a>TipoImagem
Especifica o tipo da imagem.

*Tipo*: enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifesto
O manifesto do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**ativos**  <br>*necessário*|Lista de recursos.|<[Asset](#asset)> matriz|
|**createdTime**  <br>*opcional*  <br>*só de leitura*|Hora de criação de manifesto (UTC).|cadeia (data / hora)|
|**Descrição**  <br>*opcional*|Manifesto do texto de descrição.|Cadeia|
|**driverProgram**  <br>*necessário*|Programa de controlador do manifesto.|Cadeia|
|**ID**  <br>*opcional*|ID de manifesto.|Cadeia|
|**modelIds**  <br>*opcional*|Lista de IDs de modelo de modelos de registado. O pedido irá falhar se qualquer um dos modelos de incluídos não são registados.|<string>matriz|
|**modelType**  <br>*opcional*|Especifica se os modelos já estão registados com o serviço de gestão de modelo.|enumeração (registada)|
|**nome**  <br>*necessário*|Nome do manifesto.|Cadeia|
|**targetRuntime**  <br>*necessário*||[TargetRuntime](#targetruntime)|
|**versão**  <br>*opcional*  <br>*só de leitura*|Versão do manifesto atribuído pelo serviço de gestão de modelo.|número inteiro|
|**webserviceType**  <br>*opcional*|Especifica o tipo de serviço web que será criado a partir do manifesto pretendido.|enumeração (em tempo real)|


<a name="model"></a>
### <a name="model"></a>Modelo
Uma instância de um modelo do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*só de leitura*|Hora de criação de modelo (UTC).|cadeia (data / hora)|
|**Descrição**  <br>*opcional*|Texto de descrição do modelo.|Cadeia|
|**ID**  <br>*opcional*  <br>*só de leitura*|ID de modelo.|Cadeia|
|**mimeType**  <br>*necessário*|Tipo MIME do conteúdo do modelo. Para obter mais informações sobre o tipo de MIME, consulte o [lista de tipos de suportes de dados IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|Cadeia|
|**nome**  <br>*necessário*|Nome do modelo.|Cadeia|
|**etiquetas**  <br>*opcional*|Lista de etiquetas de modelo.|<string>matriz|
|**Descompactar**  <br>*opcional*|Indica se é necessário descompactar o modelo durante a criação de imagens do Docker.|Valor booleano|
|**URL**  <br>*necessário*|URL do modelo. Normalmente, vamos colocar um URL de assinatura de acesso partilhado aqui.|Cadeia|
|**versão**  <br>*opcional*  <br>*só de leitura*|Versão de modelo atribuído pelo serviço de gestão de modelo.|número inteiro|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
As informações de recolha de dados do modelo.


|Nome|Descrição|Esquema|
|---|---|---|
|**eventHubEnabled**  <br>*opcional*|Ative um hub de eventos para um serviço.|Valor booleano|
|**storageEnabled**  <br>*opcional*|Ative o armazenamento para um serviço.|Valor booleano|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Uma lista paginated de imagens.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Ligação de continuação (URI absoluto) para a página de resultados na lista seguinte.|Cadeia|
|**valor**  <br>*opcional*|Matriz de objetos de modelo.|<[Imagem](#image)> matriz|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Uma lista paginated de manifestos.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Ligação de continuação (URI absoluto) para a página de resultados na lista seguinte.|Cadeia|
|**valor**  <br>*opcional*|Matriz de objetos de manifesto.|<[Manifesto](#manifest)> matriz|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Uma lista paginated de modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Ligação de continuação (URI absoluto) para a página de resultados na lista seguinte.|Cadeia|
|**valor**  <br>*opcional*|Matriz de objetos de modelo.|<[Modelo](#model)> matriz|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Uma lista paginated de serviços.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Ligação de continuação (URI absoluto) para a página de resultados na lista seguinte.|Cadeia|
|**valor**  <br>*opcional*|Matriz de objetos do serviço.|<[ServiceResponse](#serviceresponse)> matriz|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Um pedido para criar um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Ative o application insights para um serviço.|Valor booleano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*necessário*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*necessário*|Imagem ao criar o serviço.|Cadeia|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de pedidos em simultâneo.  <br>**Valor mínimo**:`1`|número inteiro|
|**nome**  <br>*necessário*|Nome do serviço.|Cadeia|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod em execução em qualquer altura. Não é possível especificar se Autoscaler está ativado.  <br>**Valor mínimo**:`0`|número inteiro|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Um pedido para voltar a gerar uma chave para um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**keyType**  <br>*opcional*|Especifica qual a chave para voltar a gerar.|enumeração (site primário, secundário)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Estado detalhado do serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*|Hora de criação de serviço (UTC).|cadeia (data / hora)|
|**ID**  <br>*opcional*|ID do serviço.|Cadeia|
|**imagem**  <br>*opcional*||[Imagem](#image)|
|**manifesto**  <br>*opcional*||[Manifesto](#manifest)|
|**modelos**  <br>*opcional*|Lista de modelos.|<[Modelo](#model)> matriz|
|**nome**  <br>*opcional*|Nome do serviço.|Cadeia|
|**scoringUri**  <br>*opcional*|URI para o serviço de classificação.|Cadeia|
|**Estado**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*opcional*|Hora (UTC) da última atualização.|cadeia (data / hora)|
|**appInsightsEnabled**  <br>*opcional*|Ative o application insights para um serviço.|Valor booleano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*necessário*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de pedidos em simultâneo.  <br>**Valor mínimo**:`1`|número inteiro|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod em execução em qualquer altura. Não é possível especificar se Autoscaler está ativado.  <br>**Valor mínimo**:`0`|número inteiro|
|**erro**  <br>*opcional*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>serviceUpdateRequest
Um pedido para atualizar um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Ative o application insights para um serviço.|Valor booleano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*opcional*|Imagem ao criar o serviço.|Cadeia|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de pedidos em simultâneo.  <br>**Valor mínimo**:`1`|número inteiro|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod em execução em qualquer altura. Não é possível especificar se Autoscaler está ativado.  <br>**Valor mínimo**:`0`|número inteiro|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
O tipo de tempo de execução de destino.


|Nome|Descrição|Esquema|
|---|---|---|
|**Propriedades**  <br>*necessário*||< cadeia, cadeia > mapa|
|**runtimeType**  <br>*necessário*|Especifica o tempo de execução.|enumeração (SparkPython, Python)|

