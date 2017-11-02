---
title: Utilizar o .Visual Studio e o .NET para consultar a Base de Dados SQL do Azure | Microsoft Docs
description: "Este tópico mostra-lhe como utilizar o Visual Studio para criar um programa que se liga a uma base de dados SQL do Azure e a consulta com instruções Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: devcenter
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: 5fc50c78af7c88cc2d0c537456cb8323002f8696
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>Utilizar o .NET (C#) com o Visual Studio para ligar e consultar uma base de dados SQL do Azure

Este tutorial de início rápido demonstra como utilizar o [.NET framework](https://www.microsoft.com/net/) para criar um programa C# com o Visual Studio que se liga a uma base de dados SQL do Azure e como utilizar instruções Transact-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial de início rápido, certifique-se de que tem o seguinte:

- Uma base de dados SQL do Azure. Este início rápido utiliza os recursos criados num dos inícios rápidos seguintes: 

   - [Criar BD - Portal](sql-database-get-started-portal.md)
   - [Criar BD - CLI](sql-database-get-started-cli.md)
   - [Criar BD - PowerShell](sql-database-get-started-powershell.md)

- Uma [regra de firewall ao nível do servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para o endereço IP público do computador que utilizar para este tutorial de início rápido.
- Uma instalação do [Visual Studio Community 2017, Visual Studio Professional 2017 ou Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

## <a name="sql-server-connection-information"></a>Informações de ligação do servidor SQL

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem seguinte. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Caso se tenha esquecido das informações de início de sessão do servidor da Base de Dados SQL do Azure, navegue até à pagina do servidor da Base de Dados SQL para ver o nome de administrador do servidor. Pode repor a palavra-passe, se necessário.

5. Clique em **Mostrar cadeias de ligação da base de dados**.

6. Reveja a cadeia de ligação **ADO.NET** completa.

    ![Cadeia de ligação de ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Tem de ter implementada uma regra de firewall para o endereço IP público do computador no qual executar este tutorial. Se estiver num computador diferente ou tiver um endereço IP público diferente, crie uma [regra de firewall ao nível do servidor com o portal do Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-visual-studio-project"></a>Criar um novo projeto do Visual Studio

1. No Visual Studio, escolha **Ficheiro**, **Novo**, **Projeto**. 
2. Na caixa de diálogo **Novo Projeto**, expanda **Visual C#**.
3. Selecione **Aplicação de Consola** e introduza *sqltest* no nome do projeto.
4. Clicar em **OK** para criar e abrir o novo projeto no Visual Studio
4. No Explorador de Soluções, clique com o botão direito do rato em **sqltest** e clique em **Gerir Pacotes NuGet**. 
5. Em **Procurar**, procure ```System.Data.SqlClient``` e, quando o encontrar, selecione-o.
6. Na página **System.Data.SqlClient**, clique em **Instalar**.
7. Quando a instalação estiver concluída, reveja as alterações e, em seguida, clique em **OK** para fechar a janela **Pré-visualização**. 
8. Se for apresentada uma janela **Aceitação de Licença**, clique em **Aceito**.

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar a base de dados do SQL
1. Mudar para a (ou abrir se necessário) **Program.cs**

2. Substitua os conteúdos de **Program.cs** pelo código seguinte e adicione os valores corretos para o seu servidor, a sua base de dados, o seu utilizador e a sua palavra-passe.

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadLine();
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

1. Prima **F5** para executar a aplicação.
2. Confirme que as 20 linhas superiores são devolvidas e, em seguida, feche a janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [ligar e consultar uma base de dados SQL do Azure com o .NET core](sql-database-connect-query-dotnet-core.md) no Linux/Windows/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Aprenda a [Criar a sua primeira base de dados SQL do Azure com o SSMS](sql-database-design-first-database.md) ou a [Criar a sua primeira base de dados SQL do Azure com o .NET](sql-database-design-first-database-csharp.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](https://docs.microsoft.com/dotnet/).
