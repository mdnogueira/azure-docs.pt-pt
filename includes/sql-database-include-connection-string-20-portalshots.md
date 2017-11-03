
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obter a cadeia de ligação do portal do Azure
Utilize o [portal do Azure](https://portal.azure.com/) para obter a cadeia de ligação necessária para o seu programa cliente interagir com a SQL Database do Azure: 

1. Clique em **procurar** > **bases de dados SQL**.
2. Introduza o nome da base de dados para a caixa para filtrar texto perto do canto superior esquerdo do **bases de dados SQL** painel.
3. Clique na linha de base de dados.
4. Depois de aparece o painel da base de dados, para sua comodidade visual pode clicar em controlos padrão minimizar para fechar os painéis que utilizou para navegação e a filtragem de base de dados. 
   
    ![Filtro de isolar a base de dados][10-FilterDatabase]
5. No painel da base de dados, clique em **Mostrar cadeias de ligação de base de dados**.
6. Se pretender utilizar a biblioteca de ligação do ADO.NET, copie a cadeia de etiqueta **ADO**. 
   
    ![Copie a cadeia de ligação da base de dados ADO][20-CopyAdoConnectionString]
7. Em formato de um ou outro, cole as informações de cadeia de ligação para o seu código do programa de cliente.

Para obter mais informações, consulte:<br/>[Cadeias de ligação e ficheiros de configuração](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
