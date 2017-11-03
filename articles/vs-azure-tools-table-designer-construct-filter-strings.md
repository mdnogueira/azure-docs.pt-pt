---
title: "A construção de cadeias de filtro para o estruturador de tabela | Microsoft Docs"
description: "A construção de cadeias de filtro para o estruturador de tabela"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 069224d84462b4955912ce1462a65298a5acc04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>A construção de cadeias de filtro para o estruturador de tabela
## <a name="overview"></a>Descrição geral
Para os dados de filtro uma tabela do Azure que é apresentado no Visual Studio **tabela Designer**, construir uma string de filtragem e introduza-o para o campo de filtro. A sintaxe da cadeia de filtro é definida pela WCF Data Services e é semelhante a uma cláusula onde SQL, mas é enviada para o serviço tabela através de um pedido de HTTP. O **tabela Designer** processa a codificação adequada para si, de modo a filtrar um valor de propriedade pretendido, só tem de introduzir o nome da propriedade, operador de comparação, valor dos critérios e, opcionalmente, booleano operador no campo de filtro. Não é necessário incluir a opção de consulta $filter, tal como faria se foram construir um URL para consultar a tabela através de [referência da API REST dos serviços de armazenamento](http://go.microsoft.com/fwlink/p/?LinkId=400447).

O WCF Data Services baseiam-se no [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Para obter detalhes sobre a opção de consulta do sistema de filtro (**$filter**), consulte o [especificação do OData URI convenções](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operadores de Comparação
São suportados os seguintes operadores lógicos para todos os tipos de propriedade:

| Operador lógico | Descrição | Cadeia de filtro de exemplo |
| --- | --- | --- |
| EQ |Igual a |Cidade eq 'Redmond' |
| gt |Mais do que |Preços gt 20 |
| ge |Maior ou igual a |Preços ge 10 |
| lt |Menos do que |Preços lt 20 |
| Le |Menor ou igual |Le preços 100 |
| ne |Não é igual |Cidade ne 'Londres' |
| e |E |Le preços 200 e preços gt 3.5 |
| ou |Ou |Le preços 3.5 ou preços gt 200 |
| não |não |não isAvailable |

Quando construir uma string de filtragem, as seguintes regras são importantes:

* Utilize os operadores lógicos para comparar uma propriedade para um valor. Tenha em atenção que não é possível comparar uma propriedade para um valor dinâmico; lado da expressão tem de ser uma constante.
* Todas as partes da cadeia de filtro diferenciam maiúsculas de minúsculas.
* O valor constante tem de ser o mesmo tipo de dados como a propriedade por ordem para o filtro para devolver resultados válidos. Para obter mais informações sobre os tipos de propriedade suportados, consulte [compreender o modelo de dados do serviço tabela](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtragem nas propriedades de cadeia
Ao filtrar nas propriedades de cadeia, coloque a constante de cadeia entre plicas.

O exemplo seguinte filtra no **PartitionKey** e **RowKey** propriedades; sem chave adicional também foi possível adicionar propriedades para a cadeia de filtro:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Pode coloque cada expressão de filtro parênteses, embora não seja necessário:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Tenha em atenção que o serviço de tabela não suporta consultas de carateres universais e não são suportados no estruturador de tabela está. No entanto, pode realizar utilizando operadores de comparação do prefixo pretendido de correspondência de prefixos. O exemplo seguinte devolve entidades com um início de propriedade LastName com a letra "A":

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtragem nas propriedades de um valor numérico
Para filtrar um número inteiro ou o número de vírgula flutuante, especifique o número sem as aspas.

Neste exemplo devolve todas as entidades com uma propriedade de idade cujo valor é superior a 30:

    Age gt 30

Neste exemplo devolve todas as entidades com uma propriedade de AmountDue cujo valor é menor ou igual a 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtragem nas propriedades booleanos
Para filtrar um valor booleano, especifique **verdadeiro** ou **falso** sem as aspas.

O exemplo seguinte devolve todas as entidades em que a propriedade quando IsActive está definida como **verdadeiro**:

    IsActive eq true

Também pode escrever nesta expressão de filtro sem o operador lógico. No exemplo seguinte, o serviço de tabela será também devolvem todas as entidades em que é quando IsActive **verdadeiro**:

    IsActive

Devolver todas as entidades em que seja false quando IsActive, pode utilizar o não operador:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtragem nas propriedades de DateTime
Para filtrar um valor DateTime, especifique o **datetime** palavra-chave, seguido de constante de data/hora entre plicas. A constante de data/hora tem de estar no formato UTC combinado, conforme descrito em [os valores de propriedade de DateTime formatação](http://go.microsoft.com/fwlink/p/?LinkId=400449).

O exemplo seguinte devolve entidades em que a propriedade CustomerSince é igual para 10 de Julho de 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
