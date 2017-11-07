---
title: Ligar o Excel ao Hadoop com o controlador ODBC do Hive - o Azure HDInsight | Microsoft Docs
description: Saiba como configurar e utilizar o controlador Microsoft ODBC do Hive para Excel para consultar dados nos clusters do HDInsight a partir do Microsoft Excel.
keywords: hadoop, excel, o hive do excel, ramo de registo de odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 7d775da82cb2dcdc02337cd19f0ef02e6562e41a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ligar o Excel ao Hadoop no Azure HDInsight com o controlador Microsoft ODBC do Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Solução de macrodados da Microsoft integra-se os componentes do Microsoft Business Intelligence (BI) com clusters do Apache Hadoop que foram implementadas pelo Azure HDInsight. Um exemplo desta integração é a capacidade de ligar o Excel para o armazém de dados do ramo de registo de um cluster de Hadoop no HDInsight com o Microsoft ramo de registo abertos da base de dados (controlador Connectivity).

Também é possível ligar os dados associados um cluster do HDInsight e outras origens de dados, incluindo outros clusters do Hadoop (não HDInsight), a partir do Excel utilizando o suplemento do Microsoft Power Query para Excel. Para obter informações sobre como instalar e utilizar o Power Query, consulte [ligar o Excel para o HDInsight com o Power Query][hdinsight-power-query].

> [!NOTE]
> Enquanto os passos neste artigo podem ser utilizados com o cluster de um Linux ou o HDInsight baseado em Windows, Windows é necessário para a estação de trabalho do cliente.
> 
> 

**Pré-requisitos**:

Antes de começar este artigo, tem de ter os seguintes itens:

* **Um cluster do HDInsight**. Para criar um, consulte [introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Uma estação de trabalho** com o Office 2013 Professional Plus, Office 365 Pro Plus, autónomo do Excel 2013 ou Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o controlador ODBC do Hive do Microsoft
Transfira e instale o Microsoft controlador ODBC do Hive do [Centro de transferências][hive-odbc-driver-download].

Este controlador pode ser instalado em versões de 32 bits ou 64 bits do Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012. O controlador permite a ligação ao Azure HDInsight (versão 1.6 e posterior) e o Azure HDInsight emulador (v.1.0.0.0 e posterior). Deverá instalar a versão que corresponde à versão da aplicação onde utiliza o controlador ODBC. Para este tutorial, é utilizado o controlador a partir do Office Excel.

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive
Os passos seguintes mostram como criar uma origem de dados de ODBC do Hive.

1. Do Windows 8 ou Windows 10, prima a tecla Windows para abrir o ecrã de início e, em seguida, escreva **origens de dados**.
2. Clique em **configurar origens de dados de ODBC (32-bit)** ou **configurar origens de dados de ODBC (64-bit)** dependendo da versão do Office. Se estiver a utilizar o Windows 7, escolha **origens de dados de ODBC (32 bits)** ou **origens de dados de ODBC (64 bits)** de **ferramentas administrativas**. Deverá ver o **administrador de origem de dados de ODBC** caixa de diálogo.
   
    ![Administrador de origem de dados OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "configurar um DSN a utilizar o administrador de origem de dados ODBC")

3. No DNS do utilizador, clique em **adicionar** para abrir o **criar uma nova origem de dados** assistente.
4. Selecione **controlador ODBC do Hive Microsoft**e, em seguida, clique em **concluir**. Deverá ver o **Hive ODBC controlador DNS configuração do Microsoft** caixa de diálogo.
5. Escreva ou selecione os seguintes valores:
   
   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Anfitrião |Introduza &lt;HDInsightClusterName>.azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net |
   |  Porta |Utilize <strong>443</strong>. (Esta porta foi alterada de 563 para 443.) |
   |  Base de Dados |Use <strong>Predefinição</strong>. |
   |  Mecanismo |Selecione <strong>Serviço do Azure HDInsight</strong> |
   |  Nome de Utilizador |Introduza o nome de utilizador do utilizador do HDInsight cluster HTTP. O nome de utilizador predefinido é <strong>admin</strong>. |
   |  Palavra-passe |Introduza a palavra-passe do utilizador de cluster do HDInsight. |
   
    </table>
   
    Existem alguns parâmetros importantes a ter em consideração quando clicar em **opções avançadas**:
   
   | Parâmetro | Descrição |
   | --- | --- |
   |  Utilizar consulta nativa |Quando estiver selecionada, o controlador ODBC não tenta converter TSQL em HiveQL. Deverá utilizá-lo apenas se tiver a 100% se que submeter o pura declarações HiveQL. Ao ligar ao SQL Server ou SQL Database do Azure, deve deixar a opção estiver desmarcada. |
   |  Linhas obtidas por blocos |Ao obter um grande número de registos, a otimização este parâmetro pode ser necessário para garantir que os desempenhos ideal. |
   |  O comprimento da coluna predefinido cadeia, comprimento da coluna de binário, escala da coluna Decimal |O tipo de dados comprimentos e precisions pode afetar a forma como são devolvidos dados. Causarem informações incorretas devem ser devolvidos devido a perda de precisão e/ou truncagem. |

    ![Opções Avançadas](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "opções de configuração avançadas DSN")

1. Clique em **testar** para testar a origem de dados. Quando a origem de dados está configurada corretamente, mostra *testes foi CONCLUÍDO com êxito!*.
2. Clique em **OK** para fechar a caixa de diálogo de teste. A nova origem de dados deverá estar listada no **administrador de origem de dados de ODBC**.
3. Clique em **OK** para sair do assistente.

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight
Os passos seguintes descrevem a forma de importar dados a partir de uma tabela do Hive para um livro do Excel utilizando a origem de dados ODBC que criou na secção anterior.

1. Abra um livro novo ou existente no Excel.
2. Do **dados** separador, clique em **obter dados**, clique em **de outras origens**e, em seguida, clique em **de ODBC** para iniciar o **Assistente de ligação de dados**.
   
    ![Assistente de ligação de dados aberto](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Assistente de ligação de dados aberto")
4. Selecione o nome da origem de dados que criou na última secção e, em seguida, clique em **OK**.
5. Introduza o nome de utilizador do Hadoop (o nome predefinido é admin) e a palavra-passe e, em seguida, clique em **Connect**.
6. No navegador, expanda **HIVE**, expanda **predefinido**, clique em **hivesampletable**e, em seguida, clique em **carga**. Demora alguns segundos antes de os dados serem importados para o Excel.

    ![Navegador de ODBC do Hive do HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Assistente de ligação de dados aberto")


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o controlador ODBC do Hive do Microsoft para obter dados do serviço do HDInsight para o Excel. Da mesma forma, poderá obter dados a partir do serviço de HDInsight na base de dados do SQL Server. Também é possível carregar dados para um serviço de HDInsight. Para saber mais, consulte:

* [Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel ao Hadoop com o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ligar ao Azure HDInsight e executar consultas do Hive, utilizando ferramentas do Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilizar a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


