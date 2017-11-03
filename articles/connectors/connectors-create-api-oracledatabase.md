---
title: Adicione o conector de base de dados Oracle nas suas Logic Apps do Azure | Microsoft Docs
description: "Utilizar o conector de base de dados Oracle numa aplicação lógica"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: mandia; ladocs
ms.openlocfilehash: cc64441617eb5e7d5e70c1cf5c491a672428bc51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-oracle-database-connector"></a>Começar a utilizar o conector de base de dados Oracle

Utilizar o conector de base de dados Oracle, criar organizacionais fluxos de trabalho que utilizam dados na base de dados existente. Este conector pode ligar a uma base de dados de Oracle no local ou uma máquina virtual do Azure com a base de dados Oracle instalado. Com este conector, pode:

* Crie o fluxo de trabalho ao adicionar um novo cliente para uma base de dados de clientes ou atualizar uma ordem numa base de dados ordens.
* Utilize ações para obter uma linha de dados, insira uma nova linha e até mesmo eliminar. Por exemplo, quando é criado um registo no Dynamics CRM Online (um acionador), em seguida, inserir uma linha numa base de dados Oracle (uma ação). 

Este tópico mostra como utilizar o conector de base de dados Oracle numa aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Versões suportadas do Oracle: 
    * Oracle 9 e posterior
    * Software de cliente Oracle 8.1.7 e posterior

* Instale o gateway de dados no local. [Ligar a dados no local a partir das logic apps](../logic-apps/logic-apps-gateway-connection.md) apresenta os passos. O gateway é necessária para ligar a uma base de dados de Oracle no local ou VM do Azure com o Oracle DB instalado. 

    > [!NOTE]
    > O gateway de dados no local atua como uma ponte e fornece uma transferência de dados seguros entre os dados no local (dados que não se encontra na nuvem) e as logic apps. O mesmo gateway pode ser utilizado com vários serviços e várias origens de dados. Por isso, só poderá ter de instalar o gateway de uma vez.

* Instale o cliente Oracle no computador onde instalou o gateway de dados no local. É necessário instalar o fornecedor de dados da Oracle de 64 bits para o .NET do Oracle:  

  [64-bit ODAC 12c versão 4 (12.1.0.2.4) para Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorre um erro ao tentar criar ou utilizar a ligação. Consulte os erros comuns deste tópico.


## <a name="add-the-connector"></a>Adicione o conector

> [!IMPORTANT]
> Este conector não ter acionadores. Tem apenas ações. Por isso, ao criar a sua aplicação lógica, adicione outro trigger para iniciar a sua aplicação lógica, tais como **agenda - periodicidade**, ou **pedido / resposta - resposta**. 

1. No [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco.

2. No início da sua aplicação lógica, selecione o **pedido / resposta - pedido** acionador: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecione **Guardar**. Quando guardar, o URL do pedido é gerado automaticamente. 

4. Selecione **novo passo**e selecione **adicionar uma ação**. Escreva `oracle` para ver as ações disponíveis: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Também é a forma mais rápida para ver os acionadores e ações disponíveis para os conectores. Escreva a parte do nome do conector, tal como `oracle`. O estruturador de lista acionadores e ações. 

5. Selecione uma das ações, como **base de dados Oracle - Get linha**. Selecione **ligar através do gateway de dados no local**. Introduza o nome do servidor Oracle, método de autenticação, nome de utilizador, palavra-passe e selecione o gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Assim que estiver ligado, selecione uma tabela a partir da lista e introduza o ID de linha para a tabela. Precisa de saber o identificador de tabela. Se não souber, contacte o administrador de Oracle DB e obter o resultado da `select * from yourTableName`. Isto dá-lhe as informações pessoais que necessárias para continuar.

    No exemplo seguinte, os dados da tarefa estão a ser devolvidos de uma base de dados de recursos humanos: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Neste passo seguinte, pode utilizar qualquer um dos outros conectores para compilar o fluxo de trabalho. Se pretender testar ao obter dados da Oracle, em seguida, envie por si uma mensagem de e-mail com os dados da Oracle através de um dos conectores envio de correio eletrónico, do Office 365 ou Gmail. Utilize os tokens dinâmicos da tabela Oracle para criar o `Subject` e `Body` do seu correio eletrónico:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Guardar** sua aplicação lógica e, em seguida, selecione **executar**. Fechar o designer e observe o histórico de execução para o estado. Se falhar, selecione a linha de mensagem de falha. O estruturador abre-se e mostra que o passo falhou e também mostra as informações de erro. Se tiver êxito, deverá receber um e-mail com as informações que adicionou.


### <a name="workflow-ideas"></a>Ideias de fluxo de trabalho

* Pretende monitorizar o hashtag #oracle e colocar os tweets numa base de dados para que possa ser consultadas e utilizados dentro de outras aplicações. Na aplicação lógica, adicione o `Twitter - When a new tweet is posted` acionar e introduza o **#oracle** hashtag. Em seguida, adicione o `Oracle Database - Insert row` ação e selecione a tabela:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas para uma fila do Service Bus. Pretende obter estas mensagens e colocá-los numa base de dados. Na aplicação lógica, adicione o `Service Bus - when a message is received in a queue` acionar e seleccione a fila. Em seguida, adicione o `Oracle Database - Insert row` ação e selecione a tabela:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro**: não é possível aceder ao Gateway

**Causa**: O gateway de dados no local não é possível estabelecer ligação com a nuvem. 

**Mitigação**: Certifique-se de que o gateway está em execução na máquina no local onde a instalou e que possa estabelecer ligação à internet.  Recomendamos que não instale o gateway num computador que pode ser desativada ou em suspensão. Também pode reiniciar o serviço de gateway de dados no local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-please-visit-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro**: O fornecedor utilizado foi preterido: ' System.Data.OracleClient requer Oracle versão do software de cliente 8.1.7 ou superior.'. Visite [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) para instalar o fornecedor oficial.

**Causa**: cliente Oracle o SDK não está instalado na máquina onde o gateway de dados no local está em execução.  

**Resolução**: transferir e instalar o SDK do cliente Oracle no mesmo computador como gateway de dados no local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro**: tabela '[Tablename]' não define quaisquer colunas de chaves

**Causa**: A tabela não tem nenhuma chave primária.  

**Resolução**: conector a base de dados Oracle requer que uma tabela com uma coluna de chave primária utilizada.

#### <a name="currently-not-supported"></a>Atualmente não suportado

* As vistas e procedimentos armazenados 
* Nenhuma tabela com as chaves compostas
* Tipos de objeto aninhado nas tabelas
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obter ajuda

O [fórum de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) um excelente local para colocar perguntas, responda às perguntas e ver que outros utilizadores Logic Apps estão a fazer. 

Pode ajudar a melhorar as Logic Apps e conectores ao voto e submeter as suas ideias em [http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)e explorar os conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).
