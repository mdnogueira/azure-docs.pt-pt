---
title: "Cliente de base de dados SQL do Azure technical implementações | Microsoft Docs"
description: "Saiba os detalhes técnicos sobre implementatons de cliente da base de dados do Azure SQL Server para resolver problemas de negócio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 03/03/2017
ms.author: carlrab
ms.openlocfilehash: b22307ee0cade40370b096ee49bb15dcfd2f0071
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Azure SQL Database cliente implementação técnicos práticos

- [Daxko](sql-database-implementation-daxko.md): Daxko/CSI Software deparam um desafio: base dos centros de adequação a e a recriação do cliente foi crescer rapidamente, graças ao sucesso da sua solução de software de empresarial abrangente, mas mantendo cópias de segurança com as necessidades de infraestrutura de TI para que o cliente a crescer base foi testar a empresa 's equipa de TI. A empresa foi cada vez mais restrita por crescente overhead de operações, especialmente para gerir as suas bases de dados crescentes. Worse, esse overhead de operações foi cortando para recursos de desenvolvimento para o novo iniciativas, como novas funcionalidades de mobilidade para software da empresa.

- [GEP](sql-database-implementation-gep.md): GEP fornece serviços que permitem líderes de aprovisionamento, todo o mundo maximizar o respetivo impacto nas operações dos respetivos empresas, estratégias e os desempenhos financeiros e software. Para além dos serviços de consultoria e geridos, a empresa oferece SMART por GEP®, uma plataforma de software de aprovisionamento baseado na nuvem, abrangente. No entanto, ocorreu GEP limitações tentar suporta soluções como SMART por GEP com a sua própria nas instalações dos centros de dados: investimentos necessários foram muito acentuados e requisitos de regulamentação noutros países seriam efetuou os necessários investimentos hercúleas ainda mais. Ao mover para a nuvem, GEP foi libertado dos recursos de TI, permitindo que se preocupe menor sobre as operações de TI e concentrar-se mais informações sobre desenvolver novas origens de valor para que os seus clientes em todo o mundo.

- [SnelStart](sql-database-implementation-snelstart.md): SnelStart torna software popular financeiros-empresarial-gestão e para empresas pequena e média (PMEs) no países baixos. Que os seus 55,000 clientes são servidos por uma equipa de pessoal de 110 funcionários, incluindo uma equipa de TI de 35. Ao mover o software de ambiente de trabalho para um software-como-um-serviço (SaaS) oferta incorporadas no Azure, SnelStart efetuada a maioria dos serviços incorporados, automatizar a gestão através do ambiente familiar em c# e otimizar o desempenho e escalabilidade por nenhuma das empresas de ativação pós-falha ou em-aprovisionamento de utilizam conjuntos elásticos. Utilizar o Azure fornece SnelStart fluidity dos clientes mover entre no local e na nuvem.

- [Umbraco](sql-database-implementation-umbraco.md): para simplificar as implementações de cliente, Umbraco adicionado Umbraco-como-um-serviço (UaaS): uma oferta de software-como-um-serviço (SaaS) que elimina a necessidade de implementações no local, fornece a ampliação incorporadas e remove a gestão de overhead, permitindo aos programadores focar-se na inovação em vez de solução de gestão do produto. Umbraco é capaz de fornecer todas as vantagens pela entidade confiadora no flexível plataforma-como-um-serviço (PaaS) modelo de oferecidas pelo Microsoft Azure.

- [Quórum](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database): quórum duplica carga de trabalho de chave da base de dados ao reduzirem DTU por 70% com base de dados do SQL Server.

- [Vação](https://customers.microsoft.com/en-US/story/quest): vação oferece respetivo Spotlight no serviço do SQL Server para empresas com um objetivo em mente: para dar a profissionais de base de dados melhor ferramentas disponíveis para proteger dados, mover os dados em torno e operações de base de dados de monitorização. Com Spotlight, utilizando o Microsoft Azure e SQL Database do Azure, os administradores de base de dados do SQL Server podem monitorizar, detetar, diagnosticar e fornecer uma forma de resolver problemas de desempenho no SQL Server, se de que está a sentado à frente da respetiva suportes técnicos ou estiver a trabalhar em casa.

- [Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam): este caso prático breve realça as melhores práticas e lições aprendidas na 365 Dynamics para uma experiência da equipa de produto de operações da migração para o SQL Database do Azure para fornecer aos clientes com um Software completamente gerida como um oferta de serviço (SaaS). Utilizar a SQL Database do Azure, a 365 Dynamics para a equipa de operações foi capaz de gerir e operar o serviço com significativamente menor técnico e dimensionar facilmente com funcionalidades de capacidade de gestão out of box, tais como a base de dados automática as cópias de segurança, retenção de cópias de segurança da base de dados , funcionalidades de recuperação após desastre e disponibilidade elevadas. Isto, juntamente com a capacidade de bases de dados de aprovisionamento com a automatização trivial lent SQL Database do Azure para a ser uma plataforma excelente para colocado dos serviços em grande escala.

- [Microsoft OneDrive e ao SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states): este caso prático breve informa o bloco atrás de movimentação do Microsoft OneDrive e do SharePoint Online a SQL Database do Azure e descreve a forma como esta migração ativada gestão de capacidade elástica praticamente ilimitados ao reduzir significativamente a sobrecarga de infraestrutura e os custos operacional.
