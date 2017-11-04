---
title: "Máscara de dados dinâmicos de base de dados SQL do Azure | Documentos da Microsoft"
description: "Máscara de dados dinâmicos da base de dados SQL limita a exposição de dados confidenciais através da máscara-lo para utilizadores não privilegiados"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 4b36d78e-7749-4f26-9774-eed1120a9182
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 03/09/2017
ms.author: ronitr; ronmat
ms.openlocfilehash: b75f170870a5f595fcda41196f4de81f237f88b8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database-dynamic-data-masking"></a>Máscara de dados dinâmicos da base de dados SQL

Máscara de dados dinâmicos da base de dados SQL limita a exposição de dados confidenciais através da máscara-lo para utilizadores não privilegiados. 

A máscara de dados dinâmica ajuda a evitar acessos não autorizados a dados confidenciais, ao permitir aos clientes designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

Por exemplo, um representante de serviço no Centro de atendimento telefónico pode identificar os chamadores por vários dígitos do seu número de cartão de crédito, mas esses itens de dados não devem ser expostas completamente para o representante de serviço. Uma regra de máscara pode ser definida, mas os últimos quatro dígitos de qualquer número de cartão de crédito nos resultados de máscaras de todos os definido qualquer consulta. Outro exemplo, pode ser definida uma máscara de dados adequada para proteger os dados de informação identificativa (PII), para que um programador pode consultar os ambientes de produção para fins de resolução de problemas sem violar as normas de conformidade.

## <a name="sql-database-dynamic-data-masking-basics"></a>Noções básicas de máscara de dados dinâmica base de dados SQL
Configurar um máscara de política no portal do Azure ao selecionar os operação no painel de configuração de base de dados SQL ou painel de definições de máscara de dados dinâmicos de dados dinâmica.

### <a name="dynamic-data-masking-permissions"></a>Permissões de máscara de dados dinâmicos
Máscara de dados dinâmicos pode ser configurada pelo administrador de base de dados do Azure, administrador de servidor ou funções de responsável pela segurança.

### <a name="dynamic-data-masking-policy"></a>Política de máscara de dados dinâmicos
* **Utilizadores SQL excluídos da máscara** - um conjunto de utilizadores do SQL Server ou resultados de consulta de identidades do AAD que obtêm dados sem máscara no SQL Server. Os utilizadores com privilégios de administrador são sempre excluídos da máscara e ver os dados originais sem qualquer máscara.
* **Regras de máscara** -um conjunto de regras que definem os campos designados para ser mascarado e a função de máscara que é utilizada. Os campos designados podem ser definidos utilizando um nome de esquema de base de dados, o nome da tabela e o nome da coluna.
* **As funções de máscara** -um conjunto de métodos que controlam a exposição de dados para diferentes cenários.

| Função de máscara | Lógica de máscara |
| --- | --- |
| **Predefinição** |**Máscara completa, de acordo com os tipos de dados dos campos designados**<br/><br/>• Utilize XXXX ou Xs menos se o tamanho do campo for inferior a 4 carateres para tipos de dados de cadeia (nchar, ntext, nvarchar).<br/>• Utilizar um valor de zero para tipos de dados numérico (bigint, bits, decimal, int, dinheiro, numérico, smallint, em smallmoney, tinyint, vírgula flutuante, real).<br/>• Utilizar 01-01-1900 para tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, hora).<br/>• Para variante do SQL Server, o valor predefinido do tipo atual é utilizado.<br/>• Para o documento XML <masked/> é utilizado.<br/>• Utilizar um valor vazio para tipos de dados especiais (timestamp de tabela, hierarchyid, GUID, binary, imagem, os tipos geográficos varbinary). |
| **Cartão de crédito** |**Máscara de método, o que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia constante como um prefixo sob a forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Correio eletrónico** |**Máscara de método, o que expõe a primeira letra e substitui o domínio por XXX.com** utilizando um prefixo de constante de cadeia no formato de um endereço de e-mail.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Máscara de método, o que gera um número aleatório** , de acordo com os limites selecionados e os tipos de dados real. Se os limites designados são iguais, a função de máscara é um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método, o que expõe o primeiro e último carateres de máscara** e adiciona uma cadeia de preenchimento personalizado no meio. Se for mais curta do que o prefixo exposto e o sufixo da cadeia original, é utilizada apenas a cadeia de preenchimento. <br/>sufixo de prefixo [preenchimento]<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar
O motor de recomendações de ddm de autor, sinalizadores determinados campos da sua base de dados como campos potencialmente confidenciais, que podem ser ideais para máscara. O painel da máscara de dados dinâmicos no portal, verá as colunas recomendadas para a base de dados. Tudo o que precisa de fazer é clique **adicionar máscara** para uma ou mais colunas e, em seguida, **guardar** para aplicar uma máscara para estes campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar para a base de dados utilizando cmdlets do Powershell de máscara de dados dinâmicos
Consulte [Cmdlets de base de dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar a máscara de dados dinâmicos para a base de dados utilizando a REST API
Consulte [operações para bases de dados SQL do Azure](https://msdn.microsoft.com/library/dn505719.aspx).

