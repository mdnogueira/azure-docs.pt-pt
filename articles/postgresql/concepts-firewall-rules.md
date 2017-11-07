---
title: Base de dados do Azure para as regras de firewall do servidor de PostgreSQL | Microsoft Docs
description: Descreve as regras de firewall para a base de dados do Azure para o servidor de PostgreSQL.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 7fec71f621ffeff2fc42a5a9464ae9011b2e2fee
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Base de dados do Azure para as regras de firewall do servidor de PostgreSQL
Base de dados do Azure para a firewall do servidor de PostgreSQL impede que todos os acessos ao seu servidor de base de dados até especificar quais os computadores que tem permissão. A firewall concede acesso ao servidor com base no endereço IP de origem de cada pedido.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** estas regras permitem que os clientes aceder à sua base de dados do Azure completa para o servidor de PostgreSQL, ou seja, todas as bases de dados no mesmo servidor lógico. Regras de firewall ao nível do servidor podem ser configuradas ao utilizar o portal do Azure ou utilizar comandos da CLI do Azure. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte da subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Por predefinição, todos os acessos de base de dados na base de dados do Azure para o servidor de PostgreSQL está bloqueado pela firewall. Para começar a utilizar o servidor a partir de outro computador, tem de especificar uma ou mais regras de firewall ao nível do servidor para ativar o acesso ao seu servidor. Utilize as regras de firewall para especificar que IP intervalos de endereços da Internet para permitir. Acesso para o site do portal do Azure em si não é afetado pelas regras de firewall.
Tentativas de ligação provenientes da Internet e o Azure tem primeiro de passar através da firewall antes que podem aceder a base de dados PostgreSQL, conforme mostrado no diagrama seguinte:

![Fluxo de exemplo de como funciona a firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
Regras de firewall ao nível do servidor aplicam-se a todas as bases de dados a mesma base de dados do Azure para o servidor de PostgreSQL. Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida ligação.
Se o endereço IP do pedido não está dentro dos intervalos especificados em qualquer uma das regras de firewall ao nível do servidor, o pedido de ligação falha.
Por exemplo, se a sua aplicação estabelece ligação com o controlador JDBC PostgreSQL, pode encontrar este erro ao tentar ligar quando a firewall está a bloquear a ligação.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: nenhum grupo de proteção\_hba.conf entrada para o anfitrião "123.45.67.890", o utilizador "adminuser", base de dados "postgresql", SSL

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Para além de portal do Azure, as regras de firewall podem ser geridas através de programação utilizando a CLI do Azure.
Consulte também [criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Resolução de problemas da firewall do servidor de base de dados
Quando o acesso à base de dados do Microsoft Azure para o serviço de servidor PostgreSQL não comportar-se como esperado, considere os seguintes pontos:

* **Alterações à lista de permissões não efetuou efeito ainda:** poderão existir como sucederia com um atraso de cinco minutos para as alterações à base de dados do Azure para a configuração da firewall do servidor de PostgreSQL entrem em vigor.

* **O início de sessão não está autorizado ou foi utilizada uma palavra-passe incorreta:** se um início de sessão não tem permissões na base de dados do Azure para o servidor de PostgreSQL ou a palavra-passe utilizada está incorreta, a ligação à base de dados do Azure para o servidor de PostgreSQL é negada. Criar uma definição de firewall apenas fornece aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente ainda tem de fornecer as credenciais de segurança necessário.

Por exemplo, utilizando um cliente JDBC, poderá aparecer o erro seguinte.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: Falha na autenticação de palavra-passe para o utilizador "yourusername"

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.

* Peça ao seu fornecedor de serviços Internet (ISP) para o intervalo de endereços IP atribuído aos computadores cliente que acedem a base de dados do Azure para o servidor de PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Obter estático endereçamento IP em vez disso, para os computadores cliente e, em seguida, adicione o endereço IP estático como uma regra de firewall.

## <a name="next-steps"></a>Passos seguintes
Para artigos sobre a criação de regras de firewall ao nível do servidor, consulte:
* [Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure](howto-manage-firewall-using-portal.md).
* [Criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure](howto-manage-firewall-using-cli.md).
