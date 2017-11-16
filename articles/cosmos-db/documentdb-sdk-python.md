---
title: Azure Cosmos DB Python API, SDK & recursos | Microsoft Docs
description: "Saiba tudo sobre Python API e o SDK, incluindo as datas de versão, as datas de extinção e as alterações efetuadas entre cada versão do Azure Cosmos DB Python SDK."
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 11/14/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6aecdce1de2e852c8683df0ec29e91de940ba25
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-python-sdk-release-notes-and-resources"></a>SDK de Python Cosmos BD do Azure: Notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed de alteração de .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Transferir o SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência da API de Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Instruções de instalação do SDK**</td><td>[Instruções de instalação do Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao Python SDK](documentdb-python-application.md)</td></tr>

<tr><td>**Plataforma suportada atual**</td><td>[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão
### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Esta versão do SDK requer a versão mais recente do Azure Cosmos DB emulador disponível para transferência a partir do https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Correção de erros para agregação dicionário.
* Correção de erros para corte barras na hiperligação do recurso.
* Foram adicionados testes para codificação Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Suporte adicionado para um novo nível de consistência chamado ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Suporte adicionado para consultas de agregação (CONTAGEM, MIN, MAX, soma e média).
* Adicionar uma opção para desativar a verificação de SSL quando em execução contra o emulador de BD do Cosmos.
* Remover a restrição do módulo de pedidos dependentes ser exatamente 2.10.0.
* Lowered débito mínimo em coleções particionadas de 10,100 RU/s ao 2500 RU/s.
* Suporte adicionado para ativar o registo de script durante a execução do procedimento armazenado.
* Versão de REST API bumped para ' 2017-01-19' com esta versão.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Efetuou alterações editoriais a comentários de documentação.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Suporte adicionado para o Python 3.5.
* Suporte adicionado para utilizar um módulo de pedidos de agrupamento de ligações.
* Adicionado suporte para a consistência de sessão.
* Suporte adicionado para consultas de parte superior/ORDERBY para coleções particionadas.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Suporte de política de repetição adicionado para pedidos otimizados. (Pedidos otimizados recebem uma pedido taxa demasiado grande exceção, o código de erro 429.) Por predefinição, base de dados do Azure Cosmos repete nove vezes para cada pedido quando for detetado o código de erro 429, para respeitar o tempo de retryAfter no cabeçalho de resposta. Um período de tempo do intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretender ignorar a hora de retryAfter devolvida pelo servidor entre as repetições. BD do Azure do Cosmos aguarda agora um máximo de 30 segundos para cada pedido que está a ser limitado (independentemente da contagem de repetições) e devolve a resposta com o código de erro 429. Neste momento também pode ser substituído na propriedade RetryOptions ConnectionPolicy objeto.
* BD do cosmos devolve agora x-ms-limitação--contagem de repetições e x-ms-throttle-retry-wait-time-ms como cabeçalhos de resposta de cada pedido para indicar a limitação de Repetir contagem e a hora de cummulative aguardaram o pedido entre as repetições.
* Remover a classe de RetryPolicy e a propriedade correspondente (retry_policy) exposta na classe document_client e introduzidas em vez disso, uma classe de RetryOptions exposição a propriedade RetryOptions na classe ConnectionPolicy que pode ser utilizada para substituir o algumas das opções predefinidas de repetição.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* O suporte adicionado para contas de multirregião base de dados.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Foi adicionado o suporte para a funcionalidade de tempo para Live(TTL) para documentos.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Correções de erros relacionados com o servidor lado a criação de partições para permitir carateres especiais no caminho de partitionkey.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementado [particionada coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicionar Hash & intervalo de partição resoluções para ajudá-lo com aplicações de fragmentação entre várias partições.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implemente Upsert. Novos métodos de UpsertXXX adicionados para suportar a funcionalidade de Upsert.
* ID de implementar baseado Routing. Sem alterações de API públicas, todas as alterações internas.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Índice de Geoespacial suporta.
* Valida a propriedade id de todos os recursos. Os IDs de recursos não podem conter?, /, #, \, carateres nem terminar com um espaço.
* Adiciona ResourceResponse novo cabeçalho "índice transformação curso".

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Suporta a ligação de proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Versão & extinção datas
A Microsoft vai fornecer pelo menos notificação **12 meses** previamente extinguir um SDK para smooth a transição para uma versão mais recente/suportado.

Novas funcionalidades e a funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal, recomendamos que atualize sempre para a versão mais recente SDK como antecipadamente quanto possível. 

Qualquer pedido de BD do Cosmos utilizando um SDK extinto será rejeitado pelo serviço.

> [!WARNING]
> Todas as versões do Azure DocumentDB SDK para Python anteriores à versão **1.0.0** serão descontinuados no **29 de Fevereiro de 2016**. 
> 
> 

<br/>

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [2.3.0](#2.3.0) |10 de Novembro de 2017 |--- |
| [2.2.1](#2.2.1) |Sep 29, 2017 |--- |
| [2.2.0](#2.2.0) |10 de maio de 2017 |--- |
| [2.1.0](#2.1.0) |01 de Maio de 2017 |--- |
| [2.0.1](#2.0.1) |30 de Outubro de 2016 |--- |
| [2.0.0](#2.0.0) |29 de Setembro de 2016 |--- |
| [1.9.0](#1.9.0) |07 de Julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.0](#1.7.0) |26 de Abril de 2016 |--- |
| [1.6.1](#1.6.1) |08 de Abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de Março de 2016 |--- |
| [1.5.0](#1.5.0) |03 de Janeiro de 2016 |--- |
| [1.4.2](#1.4.2) |06 de Outubro de 2015 |--- |
| [1.4.1](#1.4.1) |06 de Outubro de 2015 |--- |
| [1.2.0](#1.2.0) |06 de Agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de Julho de 2015 |--- |
| [1.0.1](#1.0.1) |25 de Maio de 2015 |--- |
| [1.0.0](#1.0.0) |07 de Abril de 2015 |--- |
| 0.9.4-prelease |14 de Janeiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.3-prelease |09 de Dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.2-prelease |25 de Novembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.1-prelease |23 de Setembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.0-prelease |21 de Agosto de 2014 |29 de Fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consultar também
Para saber mais sobre a base de dados do Cosmos, consulte [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

