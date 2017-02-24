---
title: Perguntas mais frequentes sobre o Azure Data Lake Store | Microsoft Docs
description: "Documentação de orientação sobre a resolução de problemas ou mitigação de problemas do Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf7fd555-3e30-43ce-b28c-c3ad0a241fdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a3629845014cb401df96d2d8bf7b9801a0664150
ms.openlocfilehash: 2f184f5289b9394572023fe9d1aec2d28a73c4f7


---
# <a name="frequently-asked-questions-for-azure-data-lake-store"></a>Perguntas mais frequentes sobre o Azure Data Lake Store
Neste artigo, ficará a conhecer as perguntas mais frequentes relacionadas com o Azure Data Lake Store.

## <a name="how-can-i-further-protect-my-data-from-region-wide-disasters-or-accidental-deletions"></a>Como posso proteger ainda mais os meus dados de desastres de toda a região ou eliminações acidentais?
Os dados na sua conta do Azure Data Lake Store são resilientes a falhas transitórias de hardware numa região através de réplicas automatizadas. Isto garante durabilidade e elevada disponibilidade, cumprindo o SLA do Azure Data Lake Store. Eis algumas orientações sobre como proteger ainda mais os seus dados de indisponibilidades raras de toda a região ou eliminações acidentais.

### <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre
É fundamental para cada cliente preparar o seu próprio plano de recuperação após desastre. Consulte a documentação do Azure abaixo para criar o seu plano de recuperação após desastre. Eis alguns recursos que o podem ajudar a criar o seu plano.

* [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

#### <a name="best-practices"></a>Melhores práticas
Recomendamos que copie os dados críticos para outra conta do Data Lake Store noutra região com uma frequência alinhada com as necessidades do seu plano de recuperação após desastre. Existem vários métodos para copiar dados, incluindo [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md). O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Se ocorrer uma indisponibilidade regional, pode aceder aos seus dados na região onde os dados foram copiados. Pode monitorizar o [Dashboard do Estado de Funcionamento do Serviço do Azure](https://azure.microsoft.com/status/) para determinar o estado do serviço do Azure em todo o mundo.

### <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientações sobre a recuperação após danos em dados ou eliminação acidental
Embora o Azure Data Lake Store forneça resiliência de dados através de réplicas automatizadas, isto não impede que a aplicação (ou os programadores/utilizadores) danifique dados ou os elimine acidentalmente.

#### <a name="best-practices"></a>Melhores práticas
Para impedir a eliminação acidental, recomendamos que defina primeiro as políticas de acesso corretas para a sua conta do Data Lake Store.  Isto inclui aplicar [bloqueios de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) para bloquear recursos importantes, bem como aplicar controlo de acesso de nível de ficheiro e da conta, através de [funcionalidades de segurança do Data Lake Store](data-lake-store-security-overview.md) disponíveis. Também recomendamos que crie regularmente cópias dos dados críticos com [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) noutra conta do Data Lake Store, pasta ou subscrição do Azure.  Esta ação pode ser utilizada para recuperar de um incidente de eliminação ou danos em dados. O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

As organizações também podem ativar o [registo de diagnósticos](data-lake-store-diagnostic-logs.md) na respetiva conta do Azure Data Lake Store para recolher registos de auditoria de acesso a dados que fornecem informações sobre quem poderá ter eliminado ou atualizado um ficheiro.

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)




<!--HONumber=Feb17_HO2-->


