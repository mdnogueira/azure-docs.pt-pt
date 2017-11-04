---
title: "Relatório de atividade de início de sessão do Azure Active Directory referência da API | Microsoft Docs"
description: "Referência da API de relatório de atividade de início de sessão do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5c407727fbc3adf7a089a13bfe09af959be9d2b9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Relatório de atividade de início de sessão do Azure Active Directory referência da API
Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API do relatório.  
Relatórios do Azure AD fornecem-lhe uma API que permite-lhe aceder a dados de relatório de atividade de início de sessão utilizando código ou ferramentas relacionadas.
É o âmbito deste tópico para lhe fornecer informações de referência sobre o **início de sessão API do relatório de atividade**.

Consulte:

* [As atividades de início de sessão](active-directory-reporting-azure-portal.md#activity-reports) para obter mais informações concetuais
* [Começar a utilizar a API do Azure Active Directory Reporting](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API de relatórios.


## <a name="who-can-access-the-api-data"></a>Quem pode aceder os dados de API?
* Os utilizadores e de principais de serviço na função de administrador de segurança ou de leitor de segurança
* Administradores Globais
* Qualquer aplicação que tenha autorização para aceder à API do (autorização da aplicação pode ser o programa de configuração apenas com base nas permissões de Administrador Global)

Para configurar o acesso a uma aplicação aceder à segurança APIs, tais como eventos de início de sessão, utilize o PowerShell seguinte para adicionar o Principal de serviço de aplicações para a função de leitor de segurança

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>Pré-requisitos
Para aceder a este relatório através da API de relatórios, tem de ter:

* Um [edição Azure Active Directory Premium P1 ou P2](active-directory-editions.md)
* Concluída a [pré-requisitos para o Azure AD API do relatório de acesso](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Aceder à API
Se pode aceder a esta API através de [gráfico Explorer](https://graphexplorer2.cloudapp.net) ou através de programação utilizando, por exemplo, do PowerShell. Ordem de PowerShell para interpretar corretamente a sintaxe de filtro de OData utilizada nas chamadas REST de gráfico do AAD, tem de utilizar o backtick (aka: grave acentos) caráter para "" $ como caráter de escape. O caráter de backtick serve como [caráter de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell para fazer uma interpretação literal do caráter $ e evitar confusa-lo como um nome de variável do PowerShell (ie: $filter).

O foco deste tópico é o Explorador do gráfico. Para obter um exemplo do PowerShell, consulte este [script do PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>Ponto final de API
Pode aceder a esta API utilizando o URI base seguinte:  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Devido ao volume de dados, esta API tem um limite de um milhão devolvido registos. 

Esta chamada devolve os dados em lotes. Cada lote tem um máximo de 1000 registos.  
Para obter o lote seguinte de registos, utilize a seguinte ligação. Obter o [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) informações a partir do primeiro conjunto de registos devolvidos. O token skip será no final do resultado definido.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtros suportados
Pode reduzir o número de registos que são devolvidos por uma API chamada em forma de um filtro.  
Início de sessão da API de dados, relacionados com os seguintes filtros são suportados:

* **$top =\<número de registos devem ser devolvidos\>**  - para limitar o número de registos devolvidos. Esta é uma operação dispendiosa. Não deve utilizar este filtro, se pretender regressar a milhares de objetos.  
* **$filter =\<sua declaração de filtro\>**  - especificar, on the basis of campos de filtro suportado, o tipo de registos que mais lhe interessam

## <a name="supported-filter-fields-and-operators"></a>Operadores e campos de filtro suportado
Para especificar o tipo de registos que mais lhe interessam, pode criar uma instrução de filtro que pode conter um ou uma combinação dos seguintes campos de filtro:

* [signinDateTime](#signindatetime) -define uma data ou um intervalo de datas
* [userId](#userid) -define uma específicas do utilizador com base em ID. do utilizador
* [userPrincipalName](#userprincipalname) -define específico nome principal de utilizador do utilizador (UPN) baseada no utilizador
* [appId](#appid) -define específicos de um aplicação com o ID da aplicação base
* [appDisplayName](#appdisplayname) -define específicos de um aplicação baseada em nome a apresentar da aplicação
* [loginStatus](#loginStatus) -define o estado de inícios de sessão (êxito / falha)

> [!NOTE]
> Ao utilizar o Explorador de gráfico, é a necessidade de utilizar as maiúsculas e minúsculas corretas para a letra de cada é os campos de filtro.
> 
> 

Para restringir o âmbito dos dados devolvidos, pode criar combinações dos filtros suportados e campos de filtro. Por exemplo, a seguinte instrução devolve os registos de 10 principais entre 1 de Julho de 2016 e 6th de Julho de 2016:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Suportado operadores**: eq, ge, le, gt, lt

**Exemplo**:

Utilizar uma data específica

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Utilizar um intervalo de datas    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Notas**:

O parâmetro datetime deve estar no formato UTC 

- - -
### <a name="userid"></a>ID de utilizador
**Suportado operadores**: eq

**Exemplo**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Notas**:

O valor do ID de utilizador é um valor de cadeia

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Suportado operadores**: eq

**Exemplo**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Notas**:

O valor de userPrincipalName é um valor de cadeia

- - -
### <a name="appid"></a>AppId
**Suportado operadores**: eq

**Exemplo**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Notas**:

O valor de appId é um valor de cadeia

- - -
### <a name="appdisplayname"></a>appDisplayName
**Suportado operadores**: eq

**Exemplo**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Notas**:

O valor de appDisplayName é um valor de cadeia

- - -
### <a name="loginstatus"></a>LoginStatus
**Suportado operadores**: eq

**Exemplo**:

    $filter=loginStatus+eq+'1'  


**Notas**:

Existem duas opções para o loginStatus: 0 - concluído com sucesso, 1 - Falha

- - -
## <a name="next-steps"></a>Passos seguintes
* Pretende ver exemplos para as atividades de início de sessão filtrados? Veja o [amostras de API do relatório de atividade de início de sessão do Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).
* Pretende saber mais sobre o Azure AD API do relatório? Consulte [começar a utilizar a API do Azure Active Directory Reporting](active-directory-reporting-api-getting-started.md).

