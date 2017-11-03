---
title: Lista de recursos de armazenamento do Azure com a biblioteca de clientes do Storage para C++ | Microsoft Docs
description: Saiba como utilizar a APIs de listagem na biblioteca de clientes do Microsoft Azure Storage para C++ ao enumerar os contentores, blobs, filas, tabelas e entidades.
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="list-azure-storage-resources-in-c"></a>Lista de recursos de armazenamento do Azure em C++
Operações de listagem são fundamentais para vários cenários de desenvolvimento com o Storage do Azure. Este artigo descreve como a forma mais eficiente enumerar objetos no Storage do Azure utilizando a listagem APIs fornecidas na biblioteca de cliente do Microsoft Azure Storage para C++.

> [!NOTE]
> Este guia destina-se a biblioteca de clientes de armazenamento do Azure para a versão de C++ 2, que está disponível através de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

A biblioteca de clientes do Storage fornece uma variedade de métodos para consulta ou lista de objetos no Storage do Azure. Este artigo aborda os seguintes cenários:

* Lista de contentores numa conta
* Lista de blobs num contentor ou diretório virtual blob
* Filas de lista numa conta
* Lista de tabelas numa conta
* Consulta de entidades numa tabela

Cada um destes métodos é apresentada com sobrecargas diferentes para diferentes cenários.

## <a name="asynchronous-versus-synchronous"></a>Assíncrona versus síncrona
Porque a biblioteca de clientes do Storage para C++ está incorporada do [biblioteca C++ REST](https://github.com/Microsoft/cpprestsdk), suportamos inerentemente operações assíncronas utilizando [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operações síncronas moldam as operações assíncronas correspondentes:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Se estiver a trabalhar com várias aplicações ou serviços thread, recomendamos que utilize o async APIs diretamente em vez de criar um thread para chamar a APIs, que afeta significativamente o desempenho de sincronização.

## <a name="segmented-listing"></a>Listagem segmentada
A escala de armazenamento na nuvem necessita de listagem segmentada. Por exemplo, pode ter um milhões BLOBs no contentor de Blobs do Azure ou através de mil milhões de entidades numa tabela do Azure. Não são números teórico, mas casos de utilização de clientes reais.

Consequentemente, é impractical para listar todos os objetos numa única resposta. Em vez disso, pode listar objetos utilizando a paginação. Cada um da listagem APIs tem um *segmentado* de sobrecarga.

A resposta de uma operação de listagem segmentados inclui:

* <i>_segment</i>, que contém o conjunto de resultados devolvidos de uma única chamada para a API de listagem.
* *continuation_token*, que é transferido para a chamada seguinte para obter a página seguinte do resultados. Quando existirem mais resultados para devolver, o token de continuação é nulo.

Por exemplo, uma chamada para listar todos os blobs num contentor típica poderá aparentar ser seguinte fragmento de código. O código está disponível no nosso [amostras](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_diretory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Tenha em atenção que o número de resultados devolvido numa página pode ser controlado pelo parâmetro *max_results* na sobrecarga de cada API, por exemplo:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Se não especificar o *max_results* parâmetro, a predefinição é devolvido o valor máximo de até 5000 resultados numa única página.

Tenha também em atenção que uma consulta ao Table storage do Azure pode devolver não registos ou registos menos que o valor da *max_results* parâmetros que especificou, mesmo que o token de continuação não está vazio. Uma razão poderá ser que a consulta não foi possível concluir em cinco segundos. Desde que o token de continuação não estiver vazio, a consulta deve continuar e código deve assume o tamanho dos resultados de segmento.

O padrão de codificação recomendado para a maioria dos cenários é segmentado listagem, que fornece o progresso explícito de listagem ou consultar e como o serviço responde a cada pedido. Particularmente serviços ou aplicações de C++, controlo de nível inferior do progresso listagem pode ajudar a memória de controlo e o desempenho.

## <a name="greedy-listing"></a>Listagem abrangente
Versões anteriores da biblioteca de clientes do Storage para C++ (0.5.0 de versões de pré-visualização e versões anteriores) incluído não-segmentado listagem de APIs para as tabelas e filas, como no exemplo seguinte:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Estes métodos foram implementados como wrappers de APIs segmentadas. Para cada resposta de listagem segmentada, o código anexado os resultados para um vetor e devolvidos todos os resultados depois dos contentores completos foram analisados.

Esta abordagem poderá funcionar quando a conta de armazenamento ou a tabela contém um pequeno número de objetos. No entanto, com um aumento no número de objetos, a memória necessária pode aumentar, sem limite, porque todos os resultados permanecer na memória. Uma operação de listagem pode demorar muito tempo, durante os quais o chamador não tinha informação sobre o progresso da mesma.

Estes não existem listagem abrangente APIs no SDK em c#, Java ou o ambiente de JavaScript Node.js. Para evitar potenciais problemas de utilizar estas APIs abrangente, removemos-los na versão 0.6.0 pré-visualização.

Se o seu código está a chamar estas APIs abrangente:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Em seguida, deve modificar o código para utilizar a APIs de listagem segmentada:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Ao especificar o *max_results* parâmetro do segmento, pode equilibrar entre os números de pedidos e a utilização de memória para satisfazer as considerações de desempenho para a sua aplicação.

Além disso, se estiver a utilizar a listagem segmentada APIs, mas armazenar os dados de uma coleção local num style "abrangente", também recomendamos vivamente que refatorar o código para processar a armazenar dados numa coleção local cuidadosamente à escala.

## <a name="lazy-listing"></a>Listagem lento
Embora a listagem abrangente gerado potenciais problemas, é conveniente se não existirem demasiados objetos no contentor.

Se também estiver a utilizar c# ou Oracle Java SDKs, deve estar familiarizado com o modelo de programação enumeráveis, que oferece um lento-estilo de listagem, onde os dados de um determinado desvio apenas são obtidos se for necessário. No C++, o modelo baseado em iterator também fornece uma abordagem semelhante.

Uma listagem lento típica API, utilizando **list_blobs** como exemplo, se parece com isto:

```cpp
list_blob_item_iterator list_blobs() const;
```

Um fragmento de código típicas que utiliza o padrão de listagem lento poderá ter o seguinte aspeto:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Tenha em atenção que a listagem lento só está disponível no modo síncrono.

Em comparação com listagem abrangente, listagem lento obtém dados apenas quando necessário. Nos bastidores, este obtém dados do armazenamento do Azure apenas quando o seguinte iterator desloca para segmento seguinte. Por conseguinte, a utilização de memória é controlada com um tamanho vinculado, e a operação é rápida.

Listagem lento APIs estão incluídos na biblioteca de clientes do Storage para C++ versão 2.2.0.

## <a name="conclusion"></a>Conclusão
Neste artigo, discutimos sobrecargas diferentes para listar as APIs para vários objetos de biblioteca de clientes do Storage para C++. Resumindo:

* APIs de Async são vivamente recomendados em vários cenários de thread.
* Para a maioria dos cenários, recomenda-se listagem segmentada.
* Listagem lento é fornecida na biblioteca como um wrapper em cenários síncronos conveniente.
* Listagem abrangente não é recomendada e foi removida da biblioteca.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o Storage do Azure e a biblioteca de clientes para C++, consulte os seguintes recursos.

* [Como utilizar o Blob Storage do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Table Storage do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como utilizar o armazenamento de filas do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteca de clientes de armazenamento do Azure para documentação da API de C++.](http://azure.github.io/azure-storage-cpp/)
* [Blogue da Equipa de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)

