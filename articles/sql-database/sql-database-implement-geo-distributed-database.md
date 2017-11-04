---
title: "Implementar uma solução de base de dados do Azure SQL geo-distribuição | Microsoft Docs"
description: "Saiba como configurar o SQL Database do Azure e a aplicação para ativação pós-falha para uma base de dados replicada e ativação pós-falha de teste."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,business continuity
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: 910be8ff5f9a882c7bb8ae875b8bf5fc74d1fb9a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="implement-a-geo-distributed-database"></a>Implementar uma base de dados de georreplicação distribuída

Neste tutorial, pode configurar uma base de dados SQL do Azure e a aplicação para ativação pós-falha para uma região remota e, em seguida, testar o seu plano de ativação pós-falha. Saiba como: 

> [!div class="checklist"]
> * Criar utilizadores de base de dados e conceda-lhes as permissões
> * Configurar uma regra de firewall ao nível da base de dados
> * Criar um [grupo de ativação pós-falha de replicação geográfica](sql-database-geo-replication-overview.md)
> * Criar e compilar uma aplicação de Java para consultar uma base de dados SQL do Azure
> * Efetuar um exercício de recuperação após desastre

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- Instalar a versão mais recente [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs). 
- Instalar uma base de dados SQL do Azure. Este tutorial utiliza a base de dados de amostra AdventureWorksLT com um nome de **mySampleDatabase** a partir de um estes inícios rápidos:

   - [Criar BD - Portal](sql-database-get-started-portal.md)
   - [Criar BD - CLI](sql-database-get-started-cli.md)
   - [Criar BD - PowerShell](sql-database-get-started-powershell.md)

- Identificou um método para executar scripts SQL na base de dados, pode utilizar uma das seguintes ferramentas de consulta:
   - O editor de consultas no [portal do Azure](https://portal.azure.com). Para obter mais informações sobre como utilizar o editor de consultas no portal do Azure, consulte [ligar e consultar utilizando o Editor de consultas](sql-database-get-started-portal.md#query-the-sql-database).
   - A versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), que é um ambiente integrado para gerir a infraestrutura de SQL, do SQL Server para a base de dados do SQL Server para o Microsoft Windows.
   - A versão mais recente do [Visual Studio Code](https://code.visualstudio.com/docs), que é um editor de código gráfica para macOS, Linux e Windows que suporte extensões, incluindo o [mssql extensão](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, Base de dados SQL do Azure e do armazém de dados do SQL Server. Para obter mais informações sobre como utilizar esta ferramenta com a SQL Database do Azure, consulte [ligar e consultar com o código de VS](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Criar utilizadores de base de dados e conceder permissões

Ligar à base de dados e criar contas de utilizador utilizando uma das seguintes ferramentas de consulta:

- O editor de consultas no portal do Azure
- SQL Server Management Studio
- Visual Studio Code

Estas contas de utilizador repliquem automaticamente para o servidor secundário (e ser mantidas sincronizado). Para utilizar o SQL Server Management Studio ou Visual Studio Code, poderá ter de configurar uma regra de firewall se estiver a ligar a partir de um cliente um endereço de IP para o qual que ainda não foi configurada uma firewall. Para obter passos detalhados, consulte [criar uma regra de firewall ao nível do servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Na janela de consulta, execute a consulta seguinte para criar duas contas de utilizador na base de dados. Este script concede **db_owner** permissões para o **app_admin** conta e atribui **SELECIONE** e **ATUALIZAÇÃO** permissões para o **app_user** conta. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Criar firewall ao nível da base de dados

Criar um [regra de firewall ao nível da base de dados](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) da base de dados do SQL Server. Esta regra de firewall ao nível da base de dados replica automaticamente para o servidor secundário que criar neste tutorial. De simplicidade (neste tutorial), utilize o endereço IP público do computador no qual está a efetuar os passos neste tutorial. Para determinar o endereço IP utilizado para a regra de firewall ao nível do servidor para o seu computador atual, consulte [criar uma firewall ao nível do servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).  

- Na janela do consulta aberta, substitua a consulta anterior a consulta seguinte, substituindo os endereços IP com os endereços IP adequados para o seu ambiente.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Criar um grupo de ativação pós-falha automática georreplicação ativa 

Com o Azure PowerShell, crie um [grupo de ativação pós-falha automática georreplicação ativa](sql-database-geo-replication-overview.md) entre o servidor de SQL do Azure existente e novo vazia servidor SQL do Azure numa região do Azure e, em seguida, adicione a base de dados de exemplo para o grupo de ativação pós-falha.

> [!IMPORTANT]
> Estes cmdlets necessitam do Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Preencher as variáveis para os scripts do PowerShell, utilizando os valores para o servidor existente e a base de dados de exemplo e forneça um valor exclusivo global para o nome do grupo de ativação pós-falha.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Crie um servidor de cópia de segurança vazio na sua região de ativação pós-falha.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Crie um grupo de ativação pós-falha entre os dois servidores.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Adicione a base de dados para o grupo de ativação pós-falha.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Instalar o software de Java

Os passos nesta secção assumem que está familiarizado com programação com Java e que nunca trabalhou com a Base de Dados SQL do Azure. 

### <a name="mac-os"></a>**Mac OS**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu projeto Java. Instale **brew** e **Maven** ao introduzir os seguintes comandos: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Para instruções detalhadas sobre como instalar e configurar o ambiente Java e o Maven, aceda a [compilar uma aplicação utilizando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), selecione **Java**, selecione **MacOS**e, em seguida, siga o instruções detalhadas para configurar o Java e Maven no passo 1.2 e 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra o terminal e navegue para um diretório no qual pretenda criar o seu projeto Java. Instale **Maven** ao introduzir os seguintes comandos:

```bash
sudo apt-get install maven
```

Para instruções detalhadas sobre como instalar e configurar o ambiente Java e o Maven, aceda a [compilar uma aplicação utilizando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), selecione **Java**, selecione **Ubuntu**e, em seguida, siga as instruções detalhadas para configurar o Java e Maven no passo 1.2, 1.3 e 1.4.

### <a name="windows"></a>**Windows**
Instale [Maven](https://maven.apache.org/download.cgi) ao utilizar o instalador oficial. Utilize Maven para ajudar a gerir as dependências, criar, testar e executar o projeto de Java. Para instruções detalhadas sobre como instalar e configurar o ambiente Java e o Maven, aceda a [compilar uma aplicação utilizando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), selecione **Java**, selecione Windows e, em seguida, siga as instruções detalhadas para configurar o Java e Maven no passo 1.2 e 1.3.

## <a name="create-sqldbsample-project"></a>Criar projeto SqlDbSample

1. Na consola do comando (por exemplo, Bash), crie um projeto Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Tipo **Y** e clique em **Enter**.
3. Altere os diretórios no seu projeto criado recentemente.

   ```bash
   cd SqlDbSamples
   ```

4. Utilizar o seu editor favorito, abra o ficheiro pom.xml na pasta do projeto. 

5. Adicione o controlador de JDBC da Microsoft para a dependência de SQL Server ao seu projeto Maven ao abrir o editor de texto favorito e copiar e colar as seguintes linhas para o ficheiro pom.xml. Não substitua os valores existentes pré-preenchida no ficheiro. A dependência JDBC tem de ser colada dentro de (de secção "dependências de" superior).   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Especifique a versão do Java para compilar o projeto em relação ao adicionar a secção "Propriedades" para o ficheiro pom.xml depois da secção "dependências". 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Adicione a seguinte secção "Criar" para o ficheiro pom.xml depois da secção "Propriedades" para suportar os ficheiros de manifesto v7.       

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```
8. Guarde e feche o ficheiro pom.xml.
9. Abra o ficheiro App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) e substitua o conteúdo com o seguinte conteúdo. Substitua o nome do grupo de ativação pós-falha com o nome do seu grupo de ativação pós-falha. Se tiver alterado os valores para o nome de base de dados, o utilizador ou palavra-passe, alterar esses valores, bem como.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Guarde e feche o ficheiro App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Compilar e executar o projeto de SqlDbSample

1. Na consola de comandos, execute o seguinte comando.

   ```bash
   mvn package
   ```
2. Quando concluído, execute o seguinte comando para executar a aplicação (é executada por cerca de 1 hora, a menos que pare manualmente):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Efetuar exercício de recuperação de desastre

1. Chamada de ativação pós-falha manual do grupo de ativação pós-falha. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Observe os resultados da aplicação durante a ativação pós-falha. Alguns inserções falharem enquanto as atualizações da cache de DNS.     

3. Determinar qual a função do servidor de recuperação após desastre está a efetuar.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Reativação pós-falha.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Observe os resultados da aplicação durante a reativação pós-falha. Alguns inserções falharem enquanto as atualizações da cache de DNS.     

6. Determinar qual a função do servidor de recuperação após desastre está a efetuar.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [grupos de replicação geográfica e ativação pós-falha Active Directory](sql-database-geo-replication-overview.md).
