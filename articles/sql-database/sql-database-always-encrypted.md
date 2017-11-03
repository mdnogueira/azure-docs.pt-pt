---
title: 'Sempre encriptado: SQL Database do Azure - arquivo de certificados do Windows | Microsoft Docs'
description: "Este artigo mostra como proteger os dados confidenciais numa base de dados SQL com a encriptação de base de dados utilizando o assistente encriptado sempre no SQL Server Management Studio (SSMS). Mostra também como armazenar as chaves de encriptação no arquivo de certificados do Windows."
keywords: "encriptar dados, a encriptação do SQL Server, a encriptação de base de dados, dados confidenciais, sempre encriptado"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: ce7e052e-8bf6-4d7c-9204-4c6f4afeba4b
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: sstein
ms.openlocfilehash: 47154202d0a8f7704561676fa645b5ccdb5b8c01
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Sempre encriptado: Proteger dados sensíveis na base de dados SQL e armazenar as chaves de encriptação no arquivo de certificados do Windows

Este artigo mostra-lhe como proteger os dados confidenciais numa base de dados SQL com a encriptação de base de dados utilizando o [sempre o Assistente de encriptados](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Mostra também como armazenar as chaves de encriptação no arquivo de certificados do Windows.

Sempre encriptado é uma nova tecnologia de encriptação de dados no SQL Database do Azure e SQL Server que o ajuda a proteger os dados confidenciais Inativos no servidor, durante o movimento entre cliente e servidor e, enquanto os dados estão em utilização, garantir que os dados confidenciais nunca aparece como texto simples no interior do sistema de base de dados. Depois de encriptar dados, apenas as aplicações de cliente ou servidores de aplicações que têm acesso às chaves podem aceder a dados de texto simples. Para obter informações detalhadas, consulte [sempre encriptados (motor de base de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar sempre encriptados, irá criar uma aplicação de cliente em c# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos neste artigo para saber como configurar o sempre encriptado para uma base de dados SQL do Azure. Neste artigo, irá aprender a realizar as seguintes tarefas:

* Utilize o assistente sempre encriptado no SSMS para criar [encriptados chaves sempre](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Criar um [chave mestra de coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Criar um [chave de encriptação de colunas (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Criar uma tabela de base de dados e encriptar colunas.
* Crie uma aplicação que insere, seleciona e apresenta dados de colunas encriptadas.

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, precisa de:

* Uma conta e subscrição do Azure. Se não tiver uma, inscreva-se um [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (num computador cliente).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Criar uma base de dados do SQL Server em branco
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **novo** > **dados + armazenamento** > **base de dados SQL**.
3. Criar um **em branco** base de dados denominada **Clinic** num servidor novo ou existente. Para obter instruções detalhadas sobre como criar uma base de dados no portal do Azure, consulte [sua primeira base de dados SQL do Azure](sql-database-get-started-portal.md).
   
    ![Criar uma base de dados vazia](./media/sql-database-always-encrypted/create-database.png)

Irá precisar da cadeia de ligação mais tarde no tutorial. Depois de criar a base de dados, vá para a nova base de dados de Clinic e copie a cadeia de ligação. Pode obter a cadeia de ligação em qualquer altura, mas é fácil copiá-lo quando estiver no portal do Azure.

1. Clique em **bases de dados SQL** > **Clinic** > **Mostrar cadeias de ligação de base de dados**.
2. Copie a cadeia de ligação para **ADO.NET**.
   
    ![Copie a cadeia de ligação](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS
Abra o SSMS e ligar ao servidor de base de dados do Clinic.

1. Abra o SSMS. (Clique **Connect** > **motor de base de dados** para abrir o **ligar ao servidor** janela se não estiver aberto).
2. Introduza o nome do servidor e as credenciais. O nome do servidor pode ser encontrado no painel de base de dados SQL e na cadeia de ligação que copiou anteriormente. Escreva o nome de servidor completo, incluindo *database.windows.net*.
   
    ![Copie a cadeia de ligação](./media/sql-database-always-encrypted/ssms-connect.png)

Se o **nova regra de Firewall** janela abre-se, iniciar sessão no Azure e permitem que o SSMS criar uma nova regra de firewall para si.

## <a name="create-a-table"></a>Criar uma tabela
Nesta secção, irá criar uma tabela para armazenar dados patient. Este será inicialmente – uma tabela normal irá configurar a encriptação na secção seguinte.

1. Expanda **bases de dados**.
2. Clique com botão direito do **Clinic** da base de dados e clique em **nova consulta**.
3. Cole o Transact-SQL seguinte (T-SQL) numa nova janela de consulta e **executar** -lo.

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Encriptar colunas (configurar sempre encriptado)
SSMS fornece um Assistente para configurar facilmente encriptados sempre por configurar o CMK, CEK e colunas encriptadas por si.

1. Expanda **bases de dados** > **Clinic** > **tabelas**.
2. Clique com botão direito do **Patients** tabela e selecione **encriptar colunas** para abrir o assistente sempre encriptados:
   
    ![Encriptar colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

O assistente sempre encriptados inclui as seguintes secções: **seleção de coluna**, **configuração da chave mestra** (CMK), **validação**, e **resumo** .

### <a name="column-selection"></a>Seleção de coluna
Clique em **seguinte** no **introdução** página, para abrir o **seleção de coluna** página. Nesta página, irá selecionar as colunas que pretende encriptar, [o tipo de encriptação e que chave de encriptação de colunas (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) a utilizar.

Encriptar **SSN** e **data de nascimento** informações para cada patient. O **SSN** coluna irá utilizar a encriptação determinista, o qual suporta pesquisas de igualdade, associações e agrupar por. O **data de nascimento** coluna irá utilizar a encriptação aleatória, que não suporta operações.

Definir o **tipo de encriptação** para o **SSN** coluna **Deterministic** e o **data de nascimento** coluna **Randomized** . Clique em **Seguinte**.

![Encriptar colunas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave mestra
O **configuração da chave mestra** página é onde configurar a sua CMK e selecione o fornecedor de armazenamento de chaves onde será armazenado o CMK. Atualmente, pode armazenar um CMK no arquivo de certificados do Windows, o Cofre de chaves do Azure ou um módulo de hardware de segurança (HSM). Este tutorial mostra como armazenar as chaves no arquivo de certificados do Windows.

Certifique-se de que **arquivo de certificados do Windows** está selecionada e clique em **seguinte**.

![Configuração da chave mestra](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validação
Pode encriptar as colunas agora ou guardar um script do PowerShell para executar mais tarde. Para este tutorial, selecione **continuar para terminar agora** e clique em **seguinte**.

### <a name="summary"></a>Resumo
Certifique-se de que as definições estão corretas e clique em **concluir** para concluir a configuração para sempre encriptado.

![Resumo](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Certifique-se ações do Assistente
Depois de concluído o assistente, a base de dados está definida para sempre encriptado. O assistente efetuar as seguintes ações:

* Criar um CMK.
* Criar um CEK.
* Configurar as colunas selecionadas para a encriptação. O **Patients** tabela tem atualmente não existem dados, mas já estão encriptados quaisquer dados existentes nas colunas selecionadas.

Pode verificar a criação de chaves no SSMS acedendo a **Clinic** > **segurança** > **encriptados chaves sempre**. Agora, pode ver as chaves novas que o assistente gerado para si.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação cliente que funciona com os dados encriptados
Agora que sempre encriptado é configurado, pode criar uma aplicação que executa *insere* e *seleciona* nas colunas encriptadas. Para executar com êxito a aplicação de exemplo, tem de a executar no mesmo computador em que executou o assistente sempre encriptado. Para executar a aplicação noutro computador, tem de implementar os certificados sempre encriptado para o computador que executa a aplicação de cliente.  

> [!IMPORTANT]
> A aplicação tem de utilizar [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos ao transmitir dados de texto simples para o servidor com colunas sempre encriptado. Passar valores literais sem utilizar SqlParameter objetos resultará numa exceção.
> 
> 

1. Abra o Visual Studio e crie uma nova aplicação de consola do c#. Certifique-se de que o projeto está definido como **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleApp** e clique em **OK**.

![Nova aplicação de consola](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de ligação para ativar sempre encriptado
Esta secção explica como ativar sempre encriptados na sua cadeia de ligação de base de dados. Irá modificar a aplicação de consola que acabou de criar na secção seguinte, "Sempre encriptados exemplo de aplicação de consola."

Para ativar sempre encriptados, terá de adicionar o **definição de encriptação de coluna** palavra-chave para a ligação da cadeia e defina-o como **ativado**.

Pode definir esta diretamente na cadeia de ligação ou pode configurá-lo utilizando um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A aplicação de exemplo na secção seguinte mostra como utilizar **SqlConnectionStringBuilder**.

> [!NOTE]
> Esta é a única alteração necessária uma aplicação de cliente específica sempre encriptados. Se tiver uma aplicação existente que armazena externamente respetiva cadeia de ligação (ou seja, no ficheiro de configuração), poderá ativar sempre encriptados sem alterar qualquer código.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar sempre encriptados na cadeia de ligação
Adicione a seguinte palavra-chave para a cadeia de ligação:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com uma SqlConnectionStringBuilder
O código seguinte mostra como ativar sempre encriptado através da definição de [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) para [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Sempre encriptado aplicação de consola de exemplo
Este exemplo demonstra como:

* Modificar a cadeia de ligação para ativar sempre encriptado.
* Inserir dados de colunas encriptadas.
* Selecione um registo de filtragem para um valor específico numa coluna encriptada.

Substitua os conteúdos de **Program.cs** com o seguinte código. Substitua a cadeia de ligação para a variável global connectionString na linha de diretamente acima o método principal com a cadeia de ligação válido do portal do Azure. Esta é a única alteração que necessita para fazer este código.

Execute a aplicação para ver sempre encriptados em ação.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## <a name="verify-that-the-data-is-encrypted"></a>Certifique-se de que os dados são encriptados
Rapidamente pode verificar que os dados reais no servidor são encriptados consultando o **Patients** dados com o SSMS. (Utilizar a ligação atual em que a definição de encriptação de coluna ainda não está ativada.)

Execute a seguinte consulta na base de dados Clinic.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Pode ver se as colunas encriptadas não contêm quaisquer dados de texto simples.

   ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-encrypted.png)

Para utilizar o SSMS para aceder aos dados de texto simples, pode adicionar o **definição de encriptação de coluna = ativada** parâmetro para a ligação.

1. No SSMS, clique no seu servidor na **Object Explorer**e, em seguida, clique em **desligar**.
2. Clique em **Connect** > **motor de base de dados** para abrir o **ligar ao servidor** janela e, em seguida, clique em **opções**.
3. Clique em **parâmetros de ligação adicionais** e tipo **definição de encriptação de coluna = ativada**.
   
    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Execute a seguinte consulta **Clinic** base de dados.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Agora, pode ver os dados de texto simples nas colunas encriptadas.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Se ligar com o SSMS (ou qualquer cliente) de um computador diferente, não terá acesso às chaves de encriptação e não conseguirá desencriptar os dados.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Depois de criar uma base de dados que utiliza sempre encriptados, poderá pretender efetuar o seguinte:

* Execute este exemplo de um computador diferente. -Não têm acesso às chaves de encriptação, pelo que não terá acesso aos dados de texto simples e não será executado com êxito.
* [Roda e limpar as chaves](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrar os dados que já estão encriptados com sempre encriptados](https://msdn.microsoft.com/library/mt621539.aspx).
* [Implementar certificados sempre encriptado para outras máquinas de cliente](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte a secção "Efetuar certificados disponíveis para aplicações e os utilizadores").

## <a name="related-information"></a>Informações relacionadas
* [Sempre encriptado (desenvolvimento do cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Encriptação transparente de dados](https://msdn.microsoft.com/library/bb934049.aspx)
* [Encriptação do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
* [Sempre encriptado do Assistente](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blogue sempre encriptado](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

