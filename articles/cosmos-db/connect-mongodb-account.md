---
title: "Cadeia de ligação do MongoDB para uma conta de base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como ligar a aplicação de MongoDB a uma conta de base de dados do Azure Cosmos através da utilização de uma cadeia de ligação do MongoDB."
keywords: "cadeia de ligação do mongodb"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.openlocfilehash: 5a47001705531d971d3181df9c0aa8f957168845
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ligar uma aplicação do MongoDB à base de dados do Azure Cosmos
Saiba como ligar a aplicação de MongoDB a uma conta de base de dados do Azure Cosmos através da utilização de uma cadeia de ligação do MongoDB. Em seguida, pode utilizar uma base de dados de base de dados do Azure Cosmos como os dados de arquivo para a sua aplicação do MongoDB. 

Este tutorial fornece duas formas de obter as informações de cadeia de ligação:

- [Método de início rápido](#QuickstartConnection), para utilização com controladores de .NET, Node.js, MongoDB Shell, Java e Python
- [O método de cadeia de ligação personalizada](#GetCustomConnection), para utilização com outros controladores

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta do Azure, crie um [conta do Azure gratuita](https://azure.microsoft.com/free/) agora. 
- Uma conta do Azure Cosmos DB. Para obter instruções, consulte [compilar uma aplicação de web API do MongoDB com .NET e o portal do Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obter a cadeia de ligação do MongoDB utilizando o início rápido
1. Num browser da Internet, a iniciar sessão para o [portal do Azure](https://portal.azure.com).
2. No **Azure Cosmos DB** painel, selecione a API para a conta do MongoDB. 
3. No painel esquerdo do painel de conta, clique em **início rápido**. 
4. Escolha a sua plataforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Se não vir o controlador ou a ferramenta listado, não se preocupe – é continuamente o documento mais fragmentos de código de ligação. Comente abaixo na qual gostaria de ver. Para saber como craft a sua própria ligação, leia [obter as informações de cadeia de ligação da conta](#GetCustomConnection).
5. Copie e cole o fragmento de código a sua aplicação do MongoDB.

    ![Painel de início rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>Obter a cadeia de ligação do MongoDB para personalizar
1. Num browser da Internet, a iniciar sessão para o [portal do Azure](https://portal.azure.com).
2. No **Azure Cosmos DB** painel, selecione a API para a conta do MongoDB. 
3. No painel esquerdo do painel de conta, clique em **cadeia de ligação**. 
4. O **cadeia de ligação** abre o painel. Tem todas as informações necessárias para ligar à conta utilizando um controlador para o MongoDB, incluindo uma cadeia de ligação preconstructed.

    ![Painel de cadeia de ligação](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de cadeia de ligação
> [!Important]
> BD do Azure do Cosmos tem requisitos de segurança estritos e normas. As contas do Azure do Cosmos DB requerem autenticação e comunicação segura via *SSL*. 
>
>

BD do Azure do Cosmos suporta o MongoDB ligação cadeia URI formato padrão, embora com algumas requisitos específicos: as contas de base de dados do Azure Cosmos requerem comunicação segura através de SSL e autenticação. Por isso, o formato de cadeia de ligação é:

    mongodb://username:password@host:port/[database]?ssl=true

Os valores desta cadeia estão disponíveis no **cadeia de ligação** painel apresentada anteriormente:

* Nome de utilizador (obrigatório): o nome de conta de base de dados do Azure Cosmos.
* Palavra-passe (obrigatório): palavra-passe da conta de base de dados do Azure Cosmos.
* Anfitrião (obrigatório): o FQDN da conta de base de dados do Azure Cosmos.
* Porta (obrigatório): 10255.
* Base de dados (opcional): A base de dados que utiliza a ligação. Se nenhuma base de dados for fornecido, a base de dados predefinido é de "teste".
* SSL = true (obrigatório)

Por exemplo, considere a conta apresentada no **cadeia de ligação** painel. Uma cadeia de ligação válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar MongoChef](mongodb-mongochef.md) com uma API de BD do Cosmos do Azure para a conta do MongoDB.
* Explore a API de BD do Cosmos do Azure para o MongoDB visualizando [amostras](mongodb-samples.md).
