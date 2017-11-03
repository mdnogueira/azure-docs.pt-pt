---
title: Mapeamentos de campo na indexadores de pesquisa do Azure
description: "Configurar os mapeamentos de campo de indexador de Azure Search para caber diferenças nos nomes de campo e representações de dados"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: 3f2ead208ea1525489a40d1fb637da47cd8a9b24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapeamentos de campo na indexadores de pesquisa do Azure
Quando utilizar indexadores de pesquisa do Azure, pode encontrar sozinho ocasionalmente em situações em que os dados de entrada não bastante corresponde ao esquema do seu índice de destino. Nesses casos, pode utilizar **campo mapeamentos** para transformar os dados para a forma pretendida.

Algumas situações em que os mapeamentos de campo são úteis:

* A origem de dados tem um campo `_id`, mas da Azure Search não permite que os nomes de campo começados por um caráter de sublinhado. Um mapeamento de campos permite-lhe "mudar o nome de" um campo.
* Pretende povoar vários campos no índice com os mesmo dados de origem, por exemplo, porque a que pretende aplicar analisadores diferentes para os campos. Mapeamentos de campo permitem-lhe "copiar" um campo da origem de dados.
* É necessário para Base64 codificar ou descodificar os seus dados. Mapeamentos de campo suportam vários **mapeamento de funções**, incluindo funções para Base64 e codificação e descodificação.   

## <a name="setting-up-field-mappings"></a>Configurar os mapeamentos de campo
Pode adicionar mapeamentos de campo ao criar um novo indexador com o [criar indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx) API. Pode gerir mapeamentos campo indexador uma indexação, utilizando o [atualização indexador](https://msdn.microsoft.com/library/azure/dn946892.aspx) API.

Um mapeamento de campo consiste em três partes:

1. A `sourceFieldName`, que representa um campo da sua origem de dados. Esta propriedade é necessária.
2. Opcional `targetFieldName`, que representa um campo no seu índice de pesquisa. Se for omitido, é utilizado o mesmo nome que aparece a origem de dados.
3. Opcional `mappingFunction`, que podem transformar os seus dados através de um dos vários predefinidas funções. A lista completa de funções é [abaixo](#mappingFunctions).

Mapeamentos de campos são adicionados para o `fieldMappings` matriz na definição do indexador.

Por exemplo, eis como pode acomodar as diferenças nos nomes de campo:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Um indexador pode ter vários mapeamentos de campo. Por exemplo, eis o que pode "copiar" um campo:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" },
]
```

> [!NOTE]
> A pesquisa do Azure utiliza comparação sensível para resolver os nomes de campo e função nos mapeamentos campo. Isto é conveniente (não tem de obter todas as maiúsculas e minúsculas corretas), mas significa que a origem de dados ou o índice não pode ter campos que só diferem a maiúsculas e minúsculas.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo
Estas funções são atualmente suportadas:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Efetua *URL segura* codificação Base64 da cadeia de entrada. Parte do princípio de que a entrada é codificado UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Caso de utilização de exemplo - pesquisa de chave do documento
Apenas URL segura carateres podem aparecer uma chave de documento de pesquisa do Azure (porque os clientes devem ser capazes de resolver o documento, utilizando o [pesquisa API](https://docs.microsoft.com/rest/api/searchservice/lookup-document), por exemplo). Se os dados contém carateres não seguro URL e pretende utilizá-la para preencher um campo de chave no seu índice de pesquisa, utilize esta função. Depois da chave é codificada, pode utilizar descodificação base64 para obter o valor original. Para obter mais informações, consulte o [base64 e codificação e descodificação](#base64details) secção.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Caso de utilização de exemplo - obter a chave original
Tem um indexador de blob índices blobs com os metadados do caminho de blob como a chave do documento. Após obter a chave do documento codificado, pretende descodificar o caminho e transferir o blob.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
```

Se não precisa de procurar documentos pelos chaves e também não precisa de descodificar o codificado conteúdo, pode apenas deixar `parameters` para a função de mapeamento, que predefinições `useHttpServerUtilityUrlTokenEncode` para `true`. Caso contrário, consulte [base64 detalhes](#base64details) secção para decidir quais as definições a utilizar.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Executa descodificação Base64 da cadeia de entrada. A entrada pressupõe-se para uma *URL segura* cadeia com codificação Base64.

### <a name="sample-use-case"></a>Caso de utilização de exemplo
Os valores de metadados personalizados do blob tem de ser codificado-ASCII. Pode utilizar a codificação Base64 para representar arbitrários UTF-8 cadeias no blob de metadados personalizados. No entanto, para efetuar a pesquisa significativo, pode utilizar esta função para ativar os dados codificados novamente nas cadeias "regulares" ao povoar o índice de pesquisa.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Se não especificar qualquer `parameters`, em seguida, o valor predefinido de `useHttpServerUtilityUrlTokenDecode` é `true`. Consulte [base64 detalhes](#base64details) secção para decidir quais as definições a utilizar.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Detalhes de base64 e codificação e descodificação
A pesquisa do Azure suporta dois codificações de base64: codificação do URL de HttpServerUtility base64 de token e URL segura sem preenchimento. Tem de utilizar a mesma codificação como as funções de mapeamento, se pretende codificar uma chave do documento para ver a cópia de segurança, codificar um valor possível descodificar pelo indexador ou descodificar um campo codificado pelo indexador.

Se utilizar o .NET Framework, pode definir `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` para `true`para codificação e descodificação respetivamente. Em seguida, `base64Encode` tem o mesmo comportamento [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` tem o mesmo comportamento [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se não estiver a utilizar o .NET Framework, em seguida, deverá definir `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` para `false`. Consoante a biblioteca a utilizar, o base64 codificar e descodificar utilitário funções podem ser diferentes da Azure Search.

A tabela seguinte compara as codificações de base64 diferente da cadeia `00>00?00`. Para determinar o processamento adicional necessário (se aplicável) para as suas funções base64, aplicam-se a sua biblioteca de codificar função a cadeia de `00>00?00` e comparar o resultado com o resultado esperado `MDA-MDA_MDA`.

| Encoding | Saída de codificar em Base64 | Processamento depois da codificação de biblioteca adicional | Processamento antes de biblioteca descodificar adicional |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Utilizar URL segura carateres e remover preenchimento | Utilize carateres base64 padrão e adicione o preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Utilizar URL segura carateres | Utilizar carateres base64 padrão |
| URL segura base64 com preenchimento | `MDA-MDA_MDA=` | Remova o preenchimento | Adicionar o preenchimento |
| URL segura base64 sem preenchimento | `MDA-MDA_MDA` | Nenhuma | Nenhuma |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Divide um campo de cadeia utilizando o delimitador especificado e escolhe o token da posição especificada na divisão resultante.

Por exemplo, se a entrada é `Jane Doe`, a `delimiter` é `" "`(espaço) e o `position` for 0, o resultado é `Jane`; se o `position` é 1, o resultado é `Doe`. Se a posição refere-se a um token que não existe, é devolvido um erro.

### <a name="sample-use-case"></a>Caso de utilização de exemplo
A origem de dados contém um `PersonName` campo e pretender índice-o como dois separado `FirstName` e `LastName` campos. Pode utilizar esta função para a entrada utilizando o caráter de espaço que o delimitador de divisão.

### <a name="parameters"></a>Parâmetros
* `delimiter`: uma cadeia para utilizar como o separador ao dividir a cadeia de entrada.
* `position`: uma posição baseada em zero de número inteiro do token e escolha após a divisão de cadeia de entrada.    

### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Transforma uma cadeia formatada como uma matriz JSON de cadeias para uma matriz de cadeia que pode ser utilizada para preencher um `Collection(Edm.String)` campo no índice.

Por exemplo, se a cadeia de entrada é `["red", "white", "blue"]`, em seguida, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `red`, `white`, e `blue`. Para os valores de entrada que não podem ser analisados como as matrizes de cadeia JSON, é devolvido um erro.

### <a name="sample-use-case"></a>Caso de utilização de exemplo
Base de dados SQL do Azure não tem um tipo de dados incorporados naturalmente mapeia para `Collection(Edm.String)` campos na Azure Search. Para preencher os campos de coleção de cadeia, formatar os dados de origem como uma matriz de cadeia JSON e utilizar esta função.

### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Ajude-na tornar o melhor da Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhoramentos, contacte-no nosso [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).
