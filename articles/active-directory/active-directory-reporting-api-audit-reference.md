---
title: "Auditoria de Active Directory do Azure referência da API | Microsoft Docs"
description: "Como começar com a API de auditoria do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 242fa094010694d7060b05e5892ce738d5b37a32
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-audit-api-reference"></a>Auditoria de Active Directory referência da API do Azure
Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API do relatório.  
Relatórios do Azure AD fornecem-lhe uma API que permite-lhe aceder a dados de auditoria utilizando código ou ferramentas relacionadas.
É o âmbito deste tópico para lhe fornecer informações de referência sobre o **API de auditoria**.

Consulte:

* [Registos de auditoria](active-directory-reporting-azure-portal.md#activity-reports) para obter mais informações concetuais

* [Começar a utilizar a API do Azure Active Directory Reporting](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API de relatórios.


Para:

- Perguntas mais frequentes, leia a nossa [FAQ](active-directory-reporting-faq.md) 

- Problemas,. [um pedido de suporte de ficheiros](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
* Utilizadores na função de Administrador de Segurança ou de Leitor de Segurança
* Administradores Globais
* Qualquer aplicação que tenha autorização para aceder à API do (autorização da aplicação pode ser o programa de configuração apenas com base nas permissões de Administrador Global)

## <a name="prerequisites"></a>Pré-requisitos
Para aceder a este relatório através da API de relatórios, tem de ter:

* Um [gratuito do Azure Active Directory ou a edição melhor](active-directory-editions.md)
* Concluída a [pré-requisitos para o Azure AD API do relatório de acesso](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Aceder à API
Se pode aceder a esta API através de [gráfico Explorer](https://graphexplorer2.cloudapp.net) ou através de programação utilizando, por exemplo, do PowerShell. Ordem de PowerShell para interpretar corretamente a sintaxe de filtro de OData utilizada nas chamadas REST de gráfico do AAD, tem de utilizar o backtick (aka: grave acentos) caráter para "" $ como caráter de escape. O caráter de backtick serve como [caráter de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell para fazer uma interpretação literal do caráter $ e evitar confusa-lo como um nome de variável do PowerShell (ie: $filter).

O foco deste tópico é o Explorador do gráfico. Para obter um exemplo do PowerShell, consulte este [script do PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Ponto final de API
Pode aceder a esta API utilizando o URI seguinte:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Não há nenhum limite no número de registos devolvidos pela API de auditoria do Azure AD (utilizando a paginação de OData).
Para limites de retenção de dados de relatórios, veja [relatórios de políticas de retenção](active-directory-reporting-retention.md).

Esta chamada devolve os dados em lotes. Cada lote tem um máximo de 1000 registos.  
Para obter o lote seguinte de registos, utilize a seguinte ligação. Obter as informações de skiptoken do primeiro conjunto de registos devolvidos. O token skip será no final do resultado definido.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros suportados
Pode reduzir o número de registos que são devolvidos por uma API chamada em forma de um filtro.  
Início de sessão da API de dados, relacionados com os seguintes filtros são suportados:

* **$top =\<número de registos devem ser devolvidos\>**  - para limitar o número de registos devolvidos. Esta é uma operação dispendiosa. Não deve utilizar este filtro, se pretender regressar a milhares de objetos.     
* **$filter =\<sua declaração de filtro\>**  - especificar, on the basis of campos de filtro suportado, o tipo de registos que mais lhe interessam

## <a name="supported-filter-fields-and-operators"></a>Operadores e campos de filtro suportado
Para especificar o tipo de registos que mais lhe interessam, pode criar uma instrução de filtro que pode conter um ou uma combinação dos seguintes campos de filtro:

* [activityDate](#activitydate) -define uma data ou um intervalo de datas
* [categoria](#category) -define a categoria que pretende filtrar.
* [activityStatus](#activitystatus) -define o estado de uma atividade
* [activityType](#activitytype) -define o tipo de uma atividade
* [atividade](#activity) -define a atividade como cadeia  
* [nome doactor/](#actorname) -define o ator no formato de nome do actor
* [ator/objectid](#actorobjectid) -define o ator no formato de ID de actor   
* [ator/upn](#actorupn) -define o ator no formato de nome de princípio de ator utilizador (UPN) 
* [nome do destino](#targetname) -define o destino no formato de nome do actor
* [destino/objectid](#targetobjectid) -define o destino no formato de ID de destino  
* [destino/upn](#targetupn) -define o ator no formato de nome de princípio de ator utilizador (UPN)   

- - -
### <a name="activitydate"></a>activityDate
**Suportado operadores**: eq, ge, le, gt, lt

**Exemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Notas**:

DateTime deve estar no formato UTC

- - -
### <a name="category"></a>categoria

**Valores suportados**:

| Categoria                         | Valor     |
| :--                              | ---       |
| Diretório do Núcleo                   | Diretório |
| Gestão de Palavra-passe Personalizada | SSPR      |
| Gestão de Grupos Personalizada    | SSGM      |
| Aprovisionamento de Contas             | Sync      |
| Rollover de Palavra-passe Automatizada      | Rollover de Palavra-passe Automatizada |
| Identity Protection              | IdentityProtection |
| Utilizadores Convidados                    | Utilizadores Convidados |
| Serviço MIM                      | Serviço MIM |



**Suportado operadores**: eq

**Exemplo**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>ActivityStatus

**Valores suportados**:

| Estado da atividade | Valor |
| :--             | ---   |
| Êxito         | 0     |
| Falha         | - 1   |

**Suportado operadores**: eq

**Exemplo**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**Suportado operadores**: eq

**Exemplo**:

    $filter=activityType eq 'User'    

**Notas**:

maiúsculas e minúsculas

- - -
### <a name="activity"></a>Atividade
**Suportado operadores**: eq, contém, startsWith

**Exemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Notas**:

maiúsculas e minúsculas

- - -
### <a name="actorname"></a>nome do actor /
**Suportado operadores**: eq, contém, startsWith

**Exemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Notas**:

Sensível

- - -
### <a name="actorobjectid"></a>ator/objectId
**Suportado operadores**: eq

**Exemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>nome do destino
**Suportado operadores**: eq, contém, startsWith

**Exemplo**:

    $filter=targets/any(t: t/name eq 'some name')    

**Notas**:

Sensível

- - -
### <a name="targetupn"></a>destino/upn
**Suportado operadores**: eq, startsWith

**Exemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Notas**:

* Sensível
* Tem de adicionar o espaço de nomes completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="targetobjectid"></a>destino/objectId
**Suportado operadores**: eq

**Exemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>ator/upn
**Suportado operadores**: eq, startsWith

**Exemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Notas**:

* Sensível 
* Tem de adicionar o espaço de nomes completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Passos Seguintes
* Pretende ver exemplos para atividades de sistema filtrado? Veja o [amostras de API de auditoria do Azure Active Directory](active-directory-reporting-api-audit-samples.md).
* Pretende saber mais sobre o Azure AD API do relatório? Consulte [começar a utilizar a API do Azure Active Directory Reporting](active-directory-reporting-api-getting-started.md).

