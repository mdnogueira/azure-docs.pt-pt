---
title: "Armazém de dados SQL do Azure perguntas mais frequentes | Microsoft Docs"
description: "Este artigo apresenta uma lista saída perguntas mais frequentes sobre o Azure SQL Data Warehouse de clientes e os programadores"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 3/1/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 4c00710ecc0c91f8407eca81b78176075fcbd6ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse perguntas mais frequentes

## <a name="general"></a>Geral

Q. O que oferecem armazém de dados do SQL Server para a segurança dos dados?

A. Armazém de dados do SQL Server oferece várias soluções para proteger dados, tais como o TDE e auditoria. Para obter mais informações, consulte [segurança].

Q. Onde posso encontrar as normas legais ou horário comercial é o armazém de dados do SQL em conformidade com?

A. Visite o [Microsoft Compliance] página para vários ofertas de compatibilidade ao produto, tais como certificados SOC e ISO. Primeiro escolha ao título de conformidade, em seguida expanda do Azure na Microsoft no âmbito de nuvem serviços secção no lado direito da página para ver os serviços que estão Azure serviços estão em conformidade.

Q. Posso ligar o Power BI?

A. Sim! Embora o Power BI suporta a consulta direta com o armazém de dados do SQL Server, não destina grande número de utilizadores ou dados em tempo real. Para utilização em produção PowerBI, recomendamos que utilize PowerBI por cima do Azure Analysis Services ou IaaS de serviço do Analysis Services. 

Q. Quais são os limites de capacidade do SQL Data Warehouse?

A. Consulte o nosso atual [os limites de capacidade] página. 

Q. Por que motivo o meu escala/pausa/retomar demorar tempo?

A. Vários fatores podem influenciar a hora para operações de gestão de computação. Um comuns maiúsculas e minúsculas para operações de execução longa é transacional reversão. Quando é iniciada uma operação de escala ou de pausa, todas as sessões de entrada estão bloqueadas e consultas são drained. Para deixar o sistema num estado estável, transações tem de ser revertidas novamente antes de pode começar uma operação. O maior número e maiores o tamanho do registo de transações, será parada mais tempo a operação de restauro do sistema para um estado estável.

## <a name="user-support"></a>Suporte de utilizador

Q. Tenho um pedido de funcionalidade, onde submetê-las?

A. Se tiver um pedido de funcionalidade, submetê-las no nosso [UserVoice] página

Q. Como posso fazer x?

A. Para obter ajuda na programar com o SQL Data Warehouse, pode colocar perguntas no nosso [Stack Overflow] página. 

Q. Como posso submeter um pedido de suporte?

A. [Pedidos de suporte] pode ser registada através do portal do Azure.

## <a name="sql-languagefeature-support"></a>Suporte de idioma/funcionalidades do SQL Server 

Q. Que tipos de dados suporta o SQL Data Warehouse?

A. Consulte o SQL Data Warehouse [tipos de dados].

Q. Quais as funcionalidades de tabela suportam?

A. Enquanto o armazém de dados SQL suporta muitas funcionalidades, algumas não são suportadas e estão documentadas em [funcionalidades não suportadas de tabela].

## <a name="tooling-and-administration"></a>Ferramentas e administração

Q. Suporta projetos de base de dados no Visual Studio.

A. Atualmente não suportamos projetos de base de dados no Visual Studio para o SQL Data Warehouse. Se pretender converter um voto de obter esta funcionalidade, visite o nosso voz do utilizador [projetos de base de dados de funcionalidade pedido].

Q. Armazém de dados SQL suporta REST APIs?

A. Sim. A maioria das funcionalidades REST que podem ser utilizada com a base de dados do SQL Server também estão disponível no SQL Data Warehouse. Pode encontrar informações de API dentro páginas da documentação de REST ou [MSDN].


## <a name="loading"></a>A carregar

Q. Os controladores cliente que suportam?

A. Suporte de controladores para o armazém de dados pode ser encontrado no [cadeias de ligação] página

P: quais formatos de ficheiro são suportados pelo PolyBase com o SQL Data Warehouse?

R: Orc, RC, Parquet e simples texto delimitado

P: o que pode ligar a partir do armazém de dados do SQL Server utilizando o PolyBase? 

R: [do Azure Data Lake Store] e [Blobs de armazenamento do Azure]

P: é possível pushdown cálculo quando ligar para o Blobs Storage do Azure ou ADLS? 

R: não, o PolyBase de armazém de dados do SQL Server interage apenas os componentes de armazenamento. 

P: ligar ao HDI?

R: HDI pode utilizar ADLS ou WASB como a camada HDFS. Se tiver como a camada HDFS, pode carregar os dados no armazém de dados do SQL Server. No entanto, não é possível gerar o cálculo de pushdown para a instância HDI. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações no armazém de dados do SQL Server como um todo, consulte a nossa [descrição geral] página.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[cadeias de ligação]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Pedidos de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[segurança]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[os limites de capacidade]: ./sql-data-warehouse-service-capacity-limits.md
[tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[funcionalidades não suportadas de tabela]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[do Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Blobs de armazenamento do Azure]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[projetos de base de dados de funcionalidade pedido]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[descrição geral]: ./sql-data-warehouse-overview-faq.md