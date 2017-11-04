
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Exemplo de programa c#

As secções seguintes deste artigo apresentam um programa c# que utiliza ADO.NET para enviar instruções Transact-SQL para a base de dados do SQL Server. O programa c# efetua as seguintes ações:

1. [Estabelece ligação à nossa base de dados do SQL Server utilizando ADO.NET](#cs_1_connect).
2. [Cria tabelas](#cs_2_createtables).
3. [Preenche as tabelas com dados, através da emissão de instruções INSERT T-SQL](#cs_3_insert).
4. [Atualizações de dados através de uma associação](#cs_4_updatejoin).
5. [Elimina os dados através de uma associação](#cs_5_deletejoin).
6. [Seleciona as linhas de dados através de uma associação](#cs_6_selectrows).
7. Fecha a ligação (que ignora quaisquer tabelas temporárias de tempdb).

O programa c# contém:

- Código c# para ligar à base de dados.
- Métodos de devolverem o código de origem de T-SQL.
- Dois métodos que submetem T-SQL na base de dados.

#### <a name="to-compile-and-run"></a>Para compilar e executar

Este programa c# está logicamente um ficheiro de CS. Mas aqui o programa fisicamente está dividido em vários blocos de código, para facilitar a cada bloco e compreender. Para compilar e executar este programa, efetue o seguinte:

1. Crie um projeto c# no Visual Studio.
    - O tipo de projeto deve ser um *consola* aplicação, de algo semelhante a hierarquia seguinte: **modelos** > **Visual c#** >  **Ambiente de trabalho clássico do Windows** > **(.NET Framework) de aplicação de consola**.
3. No ficheiro **Program.cs**, apagar as linhas de pequenas arranque de código.
3. Em Program.cs, copiar e colar cada um dos seguintes blocos, a mesma sequência que são apresentados aqui.
4. Em Program.cs, editar os seguintes valores no **Main** método:

   - **CB. Origem de dados**
   - **CD. ID de utilizador**
   - **CB. Palavra-passe**
   - **InitialCatalog**

5. Certifique-se de que a assemblagem **System.Data.dll** é referenciado. Para verificar, expanda o **referências** no nó de **Explorador de soluções** painel.
6. Para criar o programa no Visual Studio, clique em de **criar** menu.
7. Para executar o programa a partir do Visual Studio, clique em de **iniciar** botão. A saída de relatório é apresentada numa janela cmd.exe.

> [!NOTE]
> Tem a opção de editar o T-SQL para adicionar à esquerda  **#**  para os nomes de tabela, que cria tabelas temporárias como no **tempdb**. Isto pode ser útil para fins de demonstração, se não estiver disponível nenhuma base de dados de teste. Tabelas temporárias são eliminadas automaticamente quando fecha a ligação. Quaisquer referências para as chaves externas não são impostas para tabelas temporárias.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C# bloco 1: ligar utilizando ADO.NET

- [Seguinte](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C# bloco 2: T-SQL para criar tabelas

- [Anterior](#cs_1_connect) &nbsp;  /  &nbsp; [seguinte](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Diagrama de relação de entidade (ERD)

As instruções CREATE TABLE anteriores envolvem o **referências** palavra-chave para criar um *chave externa* relação (FK) entre duas tabelas.  Se estiver a utilizar tempdb, comente o `--REFERENCES` palavra-chave através de um par de traços à esquerda.

Em seguida, é um ERD que mostra a relação entre duas tabelas. Os valores existentes no #tabEmployee.DepartmentCode *subordinado* coluna estão limitados aos valores presentes no #tabDepartment.Department *principal* coluna.

![Chave externa da apresentação ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# bloco 3: T-SQL ao inserir dados

- [Anterior](#cs_2_createtables) &nbsp;  /  &nbsp; [seguinte](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C# bloco 4: T-SQL para associação de atualização

- [Anterior](#cs_3_insert) &nbsp;  /  &nbsp; [seguinte](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C# bloco 5: T-SQL para eliminar a associação

- [Anterior](#cs_4_updatejoin) &nbsp;  /  &nbsp; [seguinte](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C# bloco 6: T-SQL para selecionar as linhas

- [Anterior](#cs_5_deletejoin) &nbsp;  /  &nbsp; [seguinte](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C# bloco 6b: ExecuteReader

- [Anterior](#cs_6_selectrows) &nbsp;  /  &nbsp; [seguinte](#cs_7_executenonquery)

Este método é concebido para ser executado a instrução de SELECIONAR o T-SQL é composta pelo **Build_6_Tsql_SelectEmployees** método.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C# bloco 7: ExecuteNonQuery

- [Anterior](#cs_6b_datareader) &nbsp;  /  &nbsp; [seguinte](#cs_8_output)

Este método é denominado para operações que modificam o conteúdo de dados de tabelas sem devolver quaisquer linhas de dados.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# bloco 8: resultado do teste real para a consola do

- [Anterior](#cs_7_executenonquery)

Esta secção capturas de saída que o programa enviado para a consola. Apenas os valores de guid variam entre execuções do teste.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
