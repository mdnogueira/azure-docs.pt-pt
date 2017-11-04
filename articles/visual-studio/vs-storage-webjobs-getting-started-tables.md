---
title: "Introdução ao storage do Azure e o Visual Studio ligado serviços (projetos de trabalho Web)"
description: "Como começar a utilizar o Table storage do Azure num projeto WebJobs do Azure no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligada a serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 4e0c77e08bff971277a09d6066f259db84617616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Introdução ao Azure Storage (projetos de trabalho Web do Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece c# exemplos de código que mostram mostram como utilizar a versão do SDK de WebJobs do Azure 1. x com o serviço de armazenamento de tabela do Azure. O código de amostras a utilizar o [SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) versão 1. x.

O serviço de armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.  Consulte [introdução ao Table storage do Azure através do .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) para obter mais informações.

Alguns da Mostrar fragmentos de código a **tabela** atributo utilizado nas funções que são denominadas manualmente, ou seja, não utilizando um dos atributos de Acionador.

## <a name="how-to-add-entities-to-a-table"></a>Como adicionar entidades a uma tabela
Para adicionar entidades a uma tabela, utilize o **tabela** atributo com um **ICollector<T>**  ou **IAsyncCollector<T>**  parâmetro onde **T** Especifica o esquema das entidades que pretende adicionar. O construtor de atributos assume um parâmetro de cadeia que especifica o nome da tabela.

O exemplo de código seguinte adiciona **pessoa** entidades a uma tabela chamada *entrada*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

Normalmente, o tipo que utiliza com **ICollector** deriva de **TableEntity** ou implementa **ITableEntity**, mas não tem. Um dos seguintes **pessoa** classes de trabalho com o código mostrado precedente **entrada** método.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Se pretende trabalhar diretamente com o armazenamento do Azure API, pode adicionar um **CloudStorageAccount** parâmetro para a assinatura de método.

## <a name="real-time-monitoring"></a>A monitorização em tempo real
Porque as funções do dados entrada frequentemente processam grandes volumes de dados, o dashboard do SDK de WebJobs fornece dados de monitorização em tempo real. O **invocação registo** secção indica se a função ainda está em execução.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

O **detalhes de invocação** página comunica o progresso da função (número de entidades escritos) enquanto está em execução e dá-lhe uma oportunidade para abortá-lo.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Quando a função estiver concluída, o **detalhes de invocação** página reporta o número de linhas escrito.

![Função de entrada concluída](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Como ler várias entidades de uma tabela
Para ler uma tabela, utilize o **tabela** atributo com um **IQueryable<T>**  parâmetro onde escreva **T** deriva de **TableEntity** ou implementa **ITableEntity**.

O seguinte exemplo de código lê e regista todas as linhas do **entrada** tabela:

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Como ler uma única entidade a partir de uma tabela
Não existe um **tabela** construtor de atributos com dois parâmetros adicionais que permitem-lhe especificar a chave de partição e a chave de linha quando pretender vincular a uma entidade única tabela.

O seguinte exemplo de código lê uma linha de tabela para um **pessoa** entidade com base na partição chave e a linha de valores de chave recebidos uma mensagem de fila:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


O **pessoa** classe neste exemplo não tem de implementar **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Como utilizar a API .NET do armazenamento diretamente para trabalhar com uma tabela
Também pode utilizar o **tabela** atributo com um **CloudTable** objeto para uma maior flexibilidade na trabalhar com uma tabela.

O seguinte código de exemplo utiliza um **CloudTable** objeto para adicionar uma entidade única para o *entrada* tabela.

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Para obter mais informações sobre como utilizar o **CloudTable** objeto, consulte [introdução ao Table storage do Azure através do .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tópicos relacionados abrangidos pelo artigo de procedimentos de filas
Para obter informações sobre como lidar com acionado por uma mensagem de fila de processamento de tabela, ou para cenários de SDK de WebJobs não específicos do processamento de tabela, consulte [introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (WebJob projetos)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com as tabelas do Azure. Para obter mais informações sobre como utilizar WebJobs do Azure e o SDK de WebJobs, consulte [recursos de documentação de WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

