---
title: "Utilizar as funções do Azure para efetuar uma tarefa de limpeza da base de dados | Microsoft Docs"
description: "Utilize as funções do Azure para agendar uma tarefa que estabelece ligação à base de dados do Azure SQL para limpar periodicamente linhas."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: ac0399867e0cdab1825022c4ed73ce003cc8c7e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Utilizar as funções do Azure para ligar a uma base de dados do SQL do Azure
Este tópico mostra como utilizar as funções do Azure para criar uma tarefa agendada que limpa linhas numa tabela numa base de dados SQL do Azure. A nova função de c# é criada com base num modelo de Acionador de temporizador predefinidos no portal do Azure. Para suportar este cenário, também tem de definir uma cadeia de ligação de base de dados como uma definição de aplicação de função. Este cenário utiliza uma operação em massa na base de dados. Para que a sua função processar operações CRUD individuais numa tabela Mobile Apps, em vez disso, deve utilizar [enlaces de Mobile Apps](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Este tópico utiliza uma função de acionada do temporizador. Conclua os passos no tópico [criar uma função no Azure que é acionado por um temporizador](functions-create-scheduled-function.md) para criar uma versão de c# desta função.   

+ Este tópico demonstra um comando de Transact-SQL que executa uma operação de limpeza em massa no **SalesOrderHeader** tabela na base de dados de amostra AdventureWorksLT. Para criar a base de dados de amostra AdventureWorksLT, conclua os passos no tópico [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Obter informações da ligação

Tem de obter a cadeia de ligação da base de dados que criou quando concluído [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
 
3. Selecione **bases de dados SQL** no menu da esquerda e selecione a base de dados no **bases de dados SQL** página.

4. Selecione **Mostrar cadeias de ligação de base de dados** e copie todo **ADO.NET** cadeia de ligação.

    ![Copie a cadeia de ligação do ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de ligação 

Uma aplicação de função aloja a execução das suas funções no Azure. É uma melhor prática para armazenar cadeias de ligação e outros segredos nas definições da aplicação de função. Utilizar as definições da aplicação, impede que divulgação acidental da cadeia de ligação com o seu código. 

1. Navegue para a sua aplicação de função que criou [criar uma função no Azure que é acionado por um temporizador](functions-create-scheduled-function.md).

2. Selecione **funcionalidades da plataforma** > **definições da aplicação**.
   
    ![Definições da aplicação para a aplicação de função.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Desloque para baixo até **cadeias de ligação** e adicionar uma cadeia de ligação utilizando as definições conforme especificado na tabela.
   
    ![Adicione uma cadeia de ligação para as definições de aplicação de função.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Definição       | Valor sugerido | Descrição             | 
    | ------------ | ------------------ | --------------------- | 
    | **Nome**  |  sqldb_connection  | Utilizado para aceder a cadeia de ligação armazenadas no seu código de função.    |
    | **Valor** | Cadeia copiada  | Após a cadeia de ligação que copiou na secção anterior. |
    | **Tipo** | Base de Dados SQL | Utilize a ligação de base de dados SQL predefinido. |   

3. Clique em **Guardar**.

Agora, pode adicionar o função código c# que liga à sua base de dados do SQL Server.

## <a name="update-your-function-code"></a>Atualize o código de função

1. Na sua aplicação de função, selecione a função de acionada por temporizador.
 
3. Adicione as seguintes referências de assemblagem no topo do código de função existente:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```

3. Adicione o seguinte `using` instruções para a função:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Substituir a existente **executar** função com o seguinte código:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Este comando de exemplo de atualizações a **estado** coluna baseia-se a data de incorporadas. Deve atualizar o 32 linhas de dados.

5. Clique em **guardar**, veja o **registos** windows para a próxima execução de função, em seguida, tenha em atenção o número de linhas atualizadas no **SalesOrderHeader** tabela.

    ![Consulte os registos de função.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Passos seguintes

Em seguida, saiba como utilizar as funções com Logic Apps para integrar com outros serviços.

> [!div class="nextstepaction"] 
> [Criar uma função que se integra com Logic Apps](functions-twitter-email.md)

Para obter mais informações sobre as funções, consulte os tópicos seguintes:

* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.  
