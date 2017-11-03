---
title: "Lista de verificação de segurança de base de dados do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de lista de verificação de segurança da base de dados do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: 5047635555a6b4592c0714677c2b942e50bad344
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-checklist"></a>Lista de verificação de segurança de base de dados do Azure

Para ajudar a melhorar a segurança, a base de dados do Azure inclui um número de controlos de segurança incorporadas que pode utilizar para o limite e controlar o acesso.

Estas incluem:

-   Uma firewall que permite-lhe criar [regras de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) limitar a conectividade ao endereço IP,
-   Firewall ao nível do servidor acessível a partir do portal do Azure
-   Regras de firewall ao nível da base de dados acessíveis a partir do SSMS
-   Conectividade segura à base de dados através de cadeias de ligação segura
-   Utilize a gestão de acesso
-   Encriptação de dados
-   Auditoria de base de dados SQL
-   Deteção de ameaças de base de dados SQL

## <a name="introduction"></a>Introdução
Requer nova paradigmas de segurança que estão familiarizadas muitos utilizadores da aplicação, os administradores de base de dados e programadores de informática em nuvem. Como resultado, algumas organizações são hesitantes implementar uma infraestrutura de nuvem para a gestão de dados devido a percetível riscos de segurança. No entanto, muitas com esta preocupação podem ser alleviated através de uma melhor compreensão das funcionalidades de segurança incorporadas no Microsoft Azure e a base de dados do Microsoft Azure SQL.

## <a name="checklist"></a>Lista de verificação
Recomendamos que leia o [melhores práticas de segurança do Azure da base de dados](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices) artigo antes de rever esta lista de verificação. Poderá tirar o máximo partido desta lista de verificação depois de compreender as melhores práticas. Em seguida, pode utilizar esta lista de verificação para se certificar de que tiver resolvido problemas importantes na segurança de base de dados do Azure.


|Categoria de lista de verificação| Descrição|
| ------------ | -------- |
|**Proteger os dados**||
| <br> Encriptação em trânsito/movimento| <ul><li>[Segurança de camada de transporte](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol), para encriptação de dados quando os dados são movidos para as redes.</li><li>Base de dados requer uma comunicação segura de clientes com base no [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) protocolo sobre TLS (Transport Layer Security).</li></ul> |
|<br>Encriptação inativa| <ul><li>[A encriptação transparente de dados](http://go.microsoft.com/fwlink/?LinkId=526242), quando Inativos os dados são armazenados fisicamente em qualquer formato digital.</li></ul>|
|**Controlo de acesso**||  
|<br> Acesso à Base de Dados | <ul><li>[Autenticação](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) autenticação (Azure Active Directory Authentication) AD utiliza identidades geridas pelo Azure Active Directory.</li><li>[Autorização](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) conceder aos utilizadores o mínimo de privilégios necessário.</li></ul> |
|<br>Acesso de aplicação| <ul><li>[Nível de segurança de linhas](https://msdn.microsoft.com/library/dn765131) (utilizando a política de segurança, ao mesmo tempo restringir o acesso ao nível da linha com base no contexto de identidade, a função ou a execução de um utilizador).</li><li>[Máscara de dados dinâmicos](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) (permissão utilizando & política, limita a exposição de dados confidenciais através da máscara-lo para utilizadores não privilegiados)</li></ul>|
|**Monitorização proativa**||  
| <br>Controlo & detetar| <ul><li>[Auditoria](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) controla os eventos de base de dados e escreve-as para um registo de auditoria / atividade iniciar sessão na sua [conta de armazenamento do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account).</li><li>Controlar base de dados do Azure utilizando o estado de funcionamento [registos de atividade do Monitor do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Deteção de ameaças](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection) Deteta atividades de base de dados anómalas, indicando potenciais ameaças de segurança para a base de dados. </li></ul> |
|<br>Centro de Segurança do Azure| <ul><li>[Monitorização de dados](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases) utilize o Centro de segurança do Azure como um solução de monitorização para SQL e outros serviços do Azure de segurança centralizada.</li></ul>|     

## <a name="conclusion"></a>Conclusão
Base de dados do Azure é uma plataforma de base de dados robusta, com um conjunto completo de funcionalidades de segurança que cumprem os requisitos de conformidade de regulamentação e organizacional muitos. Pode facilmente proteger dados controlar o acesso físico aos seus dados e utilizando uma variedade de opções de segurança de dados no ficheiro-, -de coluna ou ao nível da linha com a encriptação transparente de dados, a encriptação de nível de células ou a segurança ao nível da linha. Sempre encriptado também permite operações contra os dados encriptados, simplificar o processo de atualizações da aplicação. Por sua vez, acesso a auditoria de registos de atividade de base de dados do SQL Server fornece-lhe as informações que necessárias, permitindo-lhe saber como e quando os dados são acedidos.

## <a name="next-steps"></a>Passos seguintes
Pode melhorar a proteção da base de dados contra utilizadores mal intencionados ou acesso não autorizado com apenas alguns passos simples. Neste tutorial que aprende a:

- Configurar [regras de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) para o seu servidor e ou base de dados.
- Proteger os seus dados com [encriptação](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption).
- Ativar [auditoria de base de dados SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing).

