

## Criar um novo utilizador da base de dados com o SSMS

Utilize os seguintes passos para criar um novo utilizador da base de dados numa base de dados existente com o SSMS. 

Estes passos presumem que está ligado à SQL Database no Object Explorer com o SSMS e que está ligado ao servidor lógico da SQL Database como um administrador principal ao nível do servidor ou com uma conta de utilizador com permissões para criar um novo utilizador. 

1. No Object Explorer, expanda o nó Bases de Dados e selecione a base de dados em que pretende criar uma nova conta de utilizador.

     ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Clique com o botão direito do rato na base de dados e, em seguida, clique em **Consulta**.

     ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. Na janela da consulta, edite e utilize a seguinte instrução Transact-SQL para criar um utilizador contido na base de dados de utilizadores. 

    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';
    ```

     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)





<!--HONumber=Sep16_HO3-->


