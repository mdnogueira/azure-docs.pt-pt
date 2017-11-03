---
title: "Origens de dados suportadas no catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo apresenta uma lista de especificações de origens de dados atualmente suportadas."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Origens de dados suportadas no catálogo de dados do Azure

Pode publicar metadados utilizando uma API pública ou de um clique-registo uma vez por introduzir manualmente as informações diretamente para o catálogo de dados do Azure web portal ou da ferramenta. A tabela seguinte resume todas as origens de dados que são suportadas pelo hoje o catálogo e as capacidades de publicação para cada. Também incluídos estão as ferramentas de dados externos que cada origem de dados pode iniciar da nossa experiência portal "open-in". A segunda tabela contém uma especificação de mais técnica de cada propriedade de ligação da origem de dados.


## <a name="list-of-supported-data-sources"></a>Lista de origens de dados suportadas

<table>
    <tr>
       <td><b>Objeto de origem de dados</b></td>
       <td><b>API</b></td>
       <td><b>Entrada manual</b></td>
       <td><b>Ferramenta de registo</b></td>
       <td><b>Ferramentas Open-in</b></td>
       <td><b>Notas</b></td>
    </tr>
    <tr>
      <td>Diretório do Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ficheiros do Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Armazenamento de Blobs do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Diretório de armazenamento do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de armazenamento do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>Diretório do HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ficheiro HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista do Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de base de dados Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de base de dados Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Outros (asset genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de armazém de dados SQL do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, Power BI, do SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, Power BI, do SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Dimensão do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Medida de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Relatório do SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Apenas a servidores de modo nativo. Modo de tabela não é suportado.</font></td>
    </tr>
    <tr>
      <td>Tabela de SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, Power BI, do SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, Power BI, do SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SAP HANA</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de DB2</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de DB2</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ficheiro de sistema de ficheiros</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Diretório de FTP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ficheiro FTP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Relatório HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ponto final de HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ficheiro HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Conjunto de entidades de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Função de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de PostgreSQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de PostgreSQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SAP HANA</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Objeto do Salesforce</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Lista do SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Coleção de Cosmos BD do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela ODBC genérica</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista ODBC genérica</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de Cassandra</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um recurso ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Vista de Cassandra</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um recurso ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Tabela de Sybase</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de Sybase</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela de MongoDB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um recurso ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Vista do MongoDB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um recurso ODBC genérico</font></td>
    </tr>
</table>

Se precisar de suporte para origens adicionais, submeter um pedido de funcionalidade para o [fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


## <a name="data-source-reference-specification"></a>Especificação de referência da origem de dados
> [!NOTE]
> O **estrutura DSL** coluna na tabela seguinte apresenta apenas as propriedades de ligação para a matriz de propriedades "address" que são utilizadas pelo catálogo de dados do Azure. Ou seja, a matriz de propriedades "address" pode conter outras propriedades de ligação da origem de dados que o catálogo de dados do Azure persistir, mas não utiliza.

<table>
    <tr>
       <td><b>Tipo de origem</b></td>
       <td><b>Tipo de recurso</b></td>
       <td><b>Tipos de objeto</b></td>
       <td><b>Estrutura DSL<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Contentor</td>
      <td>Data Lake</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>Autenticação: {oauth básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Tabela</td>
      <td>Diretório, ficheiro</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>Autenticação: {oauth básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Storage do Azure</td>
      <td>Contentor</td>
      <td>Contentor</td>
      <td>
        <font size=2>Protocolo: blobs do azure <br>Autenticação: {chave de acesso de azure} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contentor</font>
      </td>
    </tr>
    <tr>
      <td>Storage do Azure</td>
      <td>Tabela</td>
      <td>BLOB, directory</td>
      <td>
        <font size=2>Protocolo: blobs do azure <br>Autenticação: {chave de acesso de azure} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contentor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Storage do Azure</td>
      <td>Contentor</td>
      <td>Contentor</td>
      <td>
        <font size=2>Protocolo: tabelas do azure <br>Autenticação: {chave de acesso de azure} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta</font>
      </td>
    </tr>
    <tr>
      <td>Storage do Azure</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2>Protocolo: tabelas do azure <br>Autenticação: {chave de acesso de azure} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contentor</td>
      <td>Virtual cluster</td>
      <td>
        <font size=2>Protocolo: cosmos <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Fluxo, stream conjunto, vista</td>
      <td>
        <font size=2>Protocolo: cosmos <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Contentor</td>
      <td>site</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Relatório</td>
      <td>Relatório, o dashboard</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: db2 <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: db2 <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de ficheiros</td>
      <td>Tabela</td>
      <td>Ficheiro</td>
      <td>
        <font size=2>Protocolo: ficheiro <br>Autenticação: {none, basic, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;caminho</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Diretório, ficheiro</td>
      <td>
        <font size=2>Protocolo: ftp <br>Autenticação: {none, basic, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de ficheiros distribuído de Hadoop</td>
      <td>Contentor</td>
      <td>Cluster</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>Autenticação: {oauth básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de ficheiros distribuído de Hadoop</td>
      <td>Tabela</td>
      <td>Diretório, ficheiro</td>
      <td>
        <font size=2>Protocolo: webhdfs <br>Autenticação: {oauth básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: ramo de registo <br>Autenticação: {HDInsight, basic, o nome de utilizador, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: ramo de registo <br>Autenticação: {HDInsight, basic, o nome de utilizador, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Contentor</td>
      <td>site</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Relatório</td>
      <td>Relatório, o dashboard</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tabela</td>
      <td>Ponto final, ficheiro</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: mysql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: mysql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contentor</td>
      <td>Contentor de entidades</td>
      <td>
        <font size=2>Protocolo: odata <br>Autenticação: {none, basic, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabela</td>
      <td>Conjunto de entidades, função</td>
      <td>
        <font size=2>Protocolo: odata <br>Autenticação: {none, basic, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;recursos</font>
      </td>
    </tr>
    <tr>
      <td>Base de dados Oracle</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: oracle <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Base de dados Oracle</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: oracle <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: postgresql <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: postgresql <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contentor</td>
      <td>site</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Relatório</td>
      <td>Relatório, o dashboard</td>
      <td>
        <font size=2>Protocolo: http <br>Autenticação: {none, basic, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabela</td>
      <td>Aplicação híbrida de dados</td>
      <td>
        <font size=2>Protocolo: consulta de energia <br>Autenticação: {oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabela</td>
      <td>Objeto</td>
      <td>
        <font size=2>Protocolo: salesforce-com <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;itemName</font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Contentor</td>
      <td>Servidor</td>
      <td>
        <font size=2>Protocolo: sap-hana-sql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor</font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Tabela</td>
      <td>Vista</td>
      <td>
        <font size=2>Protocolo: sap-hana-sql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabela</td>
      <td>Lista</td>
      <td>
        <font size=2>Protocolo: lista sharepoint <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Comando</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Função de valor de tabela</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Função de valor de tabela</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Contentor</td>
      <td>Modelo</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>medidas</td>
      <td>medidas</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Measure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Tabela</td>
      <td>Dimensão</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {dimensão}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>Contentor</td>
      <td>Modelo</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>medidas</td>
      <td>medidas</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Measure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services em tabela</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2>Protocolo: serviços de análise <br>Autenticação: {windows, basic anónimo, none} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Table}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contentor</td>
      <td>Servidor</td>
      <td>
        <font size=2>Protocolo: serviços de relatórios <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>
        <font size=2>Protocolo: serviços de relatórios <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;caminho <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: teradata <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: teradata <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server do Master Data Services</td>
      <td>Contentor</td>
      <td>Modelo</td>
      <td>
        <font size="2">Protocolo: mssql-mds <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server do Master Data Services</td>
      <td>Tabela</td>
      <td>Entidade</td>
      <td>
        <font size="2">Protocolo: mssql-mds <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entidade</font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: documento-db <br>Autenticação: {chave de acesso de azure} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Coleção</td>
      <td>Coleção</td>
      <td>
        <font size=2>Protocolo: documento-db <br>Autenticação: {chave de acesso de azure} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;coleção</font>
      </td>
    </tr>
    <tr>
      <td>ODBC genérico</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>Protocolo: odbc <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Opções <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>ODBC genérico</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>Protocolo: odbc <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Opções <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema</font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Contentor</td>
      <td>Base de Dados</td>
      <td>
        <font size=2>protocolo: sybase <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados</font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Tabela</td>
      <td>Tabela, vista</td>
      <td>
        <font size=2>protocolo: sybase <br>Autenticação: {windows básico,} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Outros (nenhuma das respostas anteriores)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>Protocolo: recurso genérico <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;assetId</font>
      </td>
    </tr>
</table>
