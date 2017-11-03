---
title: Ligar ao Azure Analysis Services | Microsoft Docs
description: Saiba como ligar a e obter dados a partir de um servidor de Analysis Services no Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: d6cafc72f74dc0ec891591d3311f93ba1649f922
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="connect-to-an-azure-analysis-services-server"></a>Ligar a um servidor de Analysis Services do Azure

Este artigo descreve a ligar a um servidor utilizando modelação de dados e aplicações de gestão como o SQL Server Management Studio (SSMS) ou o SQL Server Data Tools (SSDT). Em alternativa, no cliente relatórios aplicações como o Microsoft Excel, Power BI Desktop ou aplicações personalizadas. Ligações ao Azure Analysis Services utilizam HTTPS.

## <a name="client-libraries"></a>Bibliotecas de cliente
[Obtenha as bibliotecas de cliente mais recentes](analysis-services-data-providers.md)

Todas as ligações a um servidor, independentemente do tipo, requerem atualizadas bibliotecas de cliente AMO, ADOMD.NET e OLEDB para ligar a e a interface com um servidor de Analysis Services. Para SSMS, o SSDT, o Excel 2016 e o Power BI, as bibliotecas de cliente mais recentes estão instaladas ou atualizadas com as versões mensais. No entanto, em alguns casos, é possível que uma aplicação poderá não ter a versão mais recente. Por exemplo, quando atualiza o atraso de políticas ou atualizações do Office 365 são no canal diferida.

## <a name="server-name"></a>Nome do servidor

Quando criar um servidor de Analysis Services no Azure, especifique um nome único e a região onde o servidor está a ser criado. Quando especificar o nome do servidor numa ligação, o esquema de nomenclatura de servidor é:

```
<protocol>://<region>/<servername>
```
 Em que o protocolo é cadeia **asazure**, região é o Uri de onde o servidor foi criado (por exemplo, westus.asazure.windows.net) e servername é o nome do seu servidor exclusivo dentro da região.

### <a name="get-the-server-name"></a>Obter o nome do servidor
No **portal do Azure** > servidor > **descrição geral** > **nome do servidor**, copie o nome de servidor completo. Se outros utilizadores na sua organização estão a ligar a este servidor demasiado, pode partilhar este nome de servidor com as mesmas. Quando especificar um nome de servidor, o caminho completo tem de ser utilizado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Cadeia de ligação

Ao ligar ao Azure Analysis Services utilizando o modelo de objeto de tabela, utilize os seguintes formatos de cadeia de ligação:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Azure Active Directory
A autenticação integrada escolherá a cache de credenciais do Azure Active Directory, se disponível. Caso contrário, é apresentada a janela de início de sessão do Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Active Directory do Azure com o nome de utilizador e palavra-passe

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticação do Windows (a segurança integrada)
Utilize a conta do Windows com o processo atual.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```



## <a name="connect-using-an-odc-file"></a>Ligar utilizando um ficheiro. odc
Com as versões mais antigas do Excel, os utilizadores podem ligar a um servidor de Analysis Services do Azure, utilizando um ficheiro de ligação de dados do Office (. odc). Para obter mais informações, consulte [criar um ficheiro de ligação de dados do Office (. odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Passos seguintes
[Estabelecer ligação com o Excel](analysis-services-connect-excel.md)    
[Estabelecer ligação com o Power BI](analysis-services-connect-pbi.md)   
[Gerir o seu servidor](analysis-services-manage.md)   

