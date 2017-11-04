---
title: "Descrição geral do desenvolvimento de aplicações da base de dados para a base de dados do Azure para MySQL | Microsoft Docs"
description: "Apresenta as considerações de design que um programador deve seguir ao escrever o código da aplicação para ligar à base de dados do Azure para MySQL"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 6a9bd8f88383b5186e470163bc67f9233172fd49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Descrição geral do desenvolvimento de aplicações para a base de dados do Azure para MySQL 
Este artigo aborda considerações de design que um programador deve seguir ao escrever o código da aplicação para ligar à base de dados do Azure para MySQL. 

> [!TIP]
> Para um tutorial mostrar como criar um servidor, crie uma firewall baseada no servidor, ver as propriedades do servidor, criar a base de dados e ligar e consultar utilizando workbench e mysql.exe, consulte [conceber a sua primeira base de dados do Azure para a base de dados MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Pode encontrar ligações para os exemplos de código em: [bibliotecas de conectividade utilizadas para ligar à base de dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
Base de dados do Azure para MySQL utiliza a versão de Comunidade do MySQL, compatível com MySQL ferramentas de gestão comuns, tais como o Workbench ou MySQL utilitários, tais como mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)entre outros. Também pode utilizar o portal do Azure, CLI do Azure e REST APIs para interagir com o serviço de base de dados.

## <a name="resource-limitations"></a>Limitações de recursos
Base de dados do Azure para MySQL gere os recursos disponíveis para um servidor utilizando dois mecanismos diferentes: 
- Governação de recursos.
- Imposição dos limites.

## <a name="security"></a>Segurança
Base de dados do Azure para MySQL fornece recursos para acesso restritiva, proteção de dados, configurar utilizadores e funções e atividades de monitorização numa base de dados MySQL.

## <a name="authentication"></a>Autenticação
Base de dados do Azure para MySQL suporta a autenticação de servidor de utilizadores e os inícios de sessão.

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao ligar a uma base de dados MySQL, o seu código deverá repetir a chamada. Recomendamos que utilize a lógica de repetição novamente desativar a lógica para que não-sobrecarregar a base de dados do SQL Server com vários clientes em simultâneo a repetir.

- Exemplos de código: para obter exemplos de código que ilustram repetir lógica, consulte amostras para o idioma da sua preferência em: [bibliotecas de conectividade utilizadas para ligar à base de dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gerir ligações
Ligações de base de dados são um recurso limitado, pelo que recomendamos sensible utilização de ligações ao aceder a base de dados MySQL para melhorar o desempenho.
- A base de dados utilizando ligações persistentes ou agrupamento de ligações de acesso.
- Acesso a base de dados utilizando a ligação curto período de vida. 
- Lógica de repetição de utilização na sua aplicação at the point of a tentativa de ligação para detetar falhas resultantes de ligações simultâneas atingiu o máximo permitido. A lógica de repetição, definir um pequeno atraso e, em seguida, aguardar por um período de tempo aleatório antes das tentativas de ligação adicionais.