

## Conceder permissões db_owner a um novo utilizador da base de dados
Utilize os seguintes passos para conceder permissões db_owner a um utilizador existente da base de dados

Estes passos presumem que está ligado à SQL Database no Object Explorer no SSMS e que está ligado ao servidor lógico da SQL Database como um administrador principal ao nível do servidor ou com uma conta de utilizador com permissões para conceder permissões a utilizadores. 

1. No Object Explorer, expanda o nó Bases de Dados e selecione a base de dados com o utilizador a quem pretende conceder permissões dbo.
   
     ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)
2. Clique com o botão direito do rato na base de dados e, em seguida, clique em **Consulta**.
   
     ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)
3. Na janela de consulta, edite e utilize a seguinte instrução Transact-SQL para conceder permissões dbo a um utilizador especificado. 
   
    '''ALTER ROLE db_owner ADD MEMBER user1;
    ```
   
     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!--HONumber=Sep16_HO3-->


