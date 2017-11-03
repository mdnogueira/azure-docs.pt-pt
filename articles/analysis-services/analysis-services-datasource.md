---
title: Origens de dados suportadas no Azure Analysis Services | Microsoft Docs
description: Descreve as origens de dados suportadas para modelos de dados no Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 5ba7ef5aa2cccad7cda3cb39459a5a5722516524
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origens de dados suportadas no Azure Analysis Services
Servidores de Analysis Services do Azure suportam a ligar a origens de dados na nuvem e no local na sua organização. Origens de dados suportadas adicionais estão a ser adicionadas sempre. A verificar frequentemente. 

Atualmente são suportadas as seguintes origens de dados:

| Nuvem  |
|---|
| Armazenamento de Blobs do Azure *  |
| Base de Dados SQL do Azure  |
| Armazém de dados do Azure |


| Local  |   |   |   |
|---|---|---|---|
| Base de dados de acesso  | Pasta * | Base de dados Oracle  | Teradata Database |
| Active Directory *  | Documento JSON *  | Base de dados Postgre SQL *  |Tabela XML * |
| Analysis Services  | Linhas de binário *  | SAP HANA *  |
| Analytics Platform System  | Base de Dados MySQL  | SAP Business Warehouse *  | |
| Dynamics CRM *  | Feed de OData *  | SharePoint *  |
| Livro do Excel  | Consulta ODBC  | Base de Dados SQL  |
| Exchange *  | OLE DB  | Base de dados Sybase  |

\*Modelos em tabela 1400 apenas. 

> [!IMPORTANT]
> A ligar a origens de dados no local requer um [gateway de dados no local](analysis-services-gateway.md) instalado num computador no seu ambiente.

## <a name="data-providers"></a>Fornecedores de dados

Modelos de dados no Azure Analysis Services podem necessitar de fornecedores de dados diferentes ao ligar a determinadas origens de dados. Em alguns casos, os modelos em tabela a ligar a origens de dados a utilizar fornecedores nativos, tais como o SQL Server Native Client (SQLNCLI11) podem devolver um erro.

Para modelos de dados que se ligam aos dados de nuvem de origem, tais como a base de dados do Azure SQL, se utilizar fornecedores nativos que não sejam SQLOLEDB, poderá ver a mensagem de erro: **"O fornecedor 'SQLNCLI11.1' não está registado."** Ou, se tiver um modelo do DirectQuery a ligar a origens de dados no local, se utilizar fornecedores nativos poderá ver a mensagem de erro: **"Erro ao criar o conjunto de linhas OLE DB. Sintaxe incorreta perto de 'Limite' "**.

Os fornecedores de origem de dados seguintes são suportados para dentro da memória ou modelos de dados de DirectQuery ao ligar a origens de dados na nuvem ou no local:

### <a name="cloud"></a>Nuvem
| **Origem de dados** | **Dentro da memória** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |Fornecedor de dados .NET framework para o SQL Server |Fornecedor de dados .NET framework para o SQL Server |
| Base de Dados SQL do Azure |Fornecedor de dados .NET framework para o SQL Server |Fornecedor de dados .NET framework para o SQL Server | |

### <a name="on-premises-via-gateway"></a>No local (através do Gateway)
|**Origem de dados** | **Dentro da memória** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |Fornecedor de dados .NET framework para o SQL Server |
| SQL Server |Fornecedor Microsoft OLE DB para SQL Server |Fornecedor de dados .NET framework para o SQL Server | |
| SQL Server |Fornecedor de dados .NET framework para o SQL Server |Fornecedor de dados .NET framework para o SQL Server | |
| Oracle |Fornecedor Microsoft OLE DB para Oracle |Fornecedor de dados Oracle para .NET | |
| Oracle |Fornecedor de dados Oracle para .NET |Fornecedor de dados Oracle para .NET | |
| Teradata |Fornecedor OLE DB para Teradata |Fornecedor de dados Teradata para .NET | |
| Teradata |Fornecedor de dados Teradata para .NET |Fornecedor de dados Teradata para .NET | |
| Analytics Platform System |Fornecedor de dados .NET framework para o SQL Server |Fornecedor de dados .NET framework para o SQL Server | |

> [!NOTE]
> Certifique-se de que os fornecedores de 64 bits estão instalados quando utilizar o gateway no local.
> 
> 

Quando migrar um modelo de tabela do SQL Server Analysis Services no local ao Azure Analysis Services, poderá ser necessário alterar o fornecedor.

**Para especificar um fornecedor de origem de dados**

1. No SSDT > **Explorador de modelo em tabela** > **origens de dados**, faça duplo clique uma ligação à origem de dados e, em seguida, clique em **editar a origem de dados**.
2. No **editar ligação**, clique em **avançadas** para abrir a janela de propriedades avançadas.
3. No **definir propriedades avançadas** > **fornecedores**, em seguida, selecione o fornecedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, poderá ser necessário especificar uma conta de representação diferentes. Conta de representação pode ser especificada no SSDT ou SSMS.

Para origens de dados no local:

* Se utilizar a autenticação de SQL, representação deve ser a conta de serviço.
* Se utilizar a autenticação do Windows, defina Windows utilizador/palavra-passe. Para o SQL Server, a autenticação do Windows com uma conta de representação específico só é suportada para modelos de dados em memória.

Para origens de dados de nuvem:

* Se utilizar a autenticação de SQL, representação deve ser a conta de serviço.

## <a name="next-steps"></a>Passos seguintes
Se tiver de origens de dados no local, não se esqueça de instalar o [gateway no local](analysis-services-gateway.md).   
Para saber mais sobre como gerir o servidor no SSDT ou SSMS, veja [gerir o seu servidor](analysis-services-manage.md).

