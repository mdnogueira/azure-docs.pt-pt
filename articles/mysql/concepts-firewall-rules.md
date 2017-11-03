---
title: Base de dados do Azure para as regras de firewall do servidor MySQL | Microsoft Docs
description: Descreve as regras de firewall para a base de dados do Azure para o servidor de MySQL.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/20/2017
ms.openlocfilehash: aea561b526d6f3f818fd75771dd8c65c9f25051a
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Base de dados do Azure para as regras de firewall do servidor MySQL
Firewalls impedir que todo o acesso ao seu servidor de base de dados até especificar quais os computadores que tem permissão. A firewall concede acesso ao servidor com base no endereço IP de origem de cada pedido.

Para configurar uma firewall, crie regras de firewall que especifique os intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** estas regras permitem que os clientes aceder à sua base de dados do Azure completo para o servidor de MySQL, ou seja, todas as bases de dados no mesmo servidor lógico. Regras de firewall ao nível do servidor podem ser configuradas através do portal do Azure ou comandos da CLI do Azure. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte da subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todos os acessos de base de dados na base de dados do Azure para o servidor de MySQL está bloqueada pela firewall por predefinição. Para começar a utilizar o servidor a partir de outro computador, tem de especificar uma ou mais regras de firewall ao nível do servidor para ativar o acesso ao seu servidor. Utilize as regras de firewall para especificar que IP intervalos de endereços da Internet para permitir. Acesso para o site do portal do Azure em si não é afetado pelas regras de firewall.

Tentativas de ligação provenientes da Internet e o Azure tem primeiro de passar através da firewall antes que podem aceder a base de dados do Azure para a base de dados MySQL, conforme mostrado no diagrama seguinte:

![Fluxo de exemplo de como funciona a firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
Regras de firewall ao nível do servidor aplicam-se a todas as bases de dados na base de dados do Azure para o servidor de MySQL.

Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, a ligação é concedida.

Se o endereço IP do pedido está fora dos intervalos especificados em qualquer uma das regras de firewall ao nível da base de dados ou ao nível do servidor, o pedido de ligação falha.

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Para além de portal do Azure, as regras de firewall podem ser geridas através de programação utilizando a CLI do Azure. Consulte também [criar e gerir a base de dados do Azure para as regras de firewall de MySQL utilizando a CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Resolver problemas da firewall de base de dados
Quando o acesso à base de dados do Microsoft Azure para o serviço de servidor MySQL não comportar-se conforme esperado, considere os seguintes pontos:

* **Alterações à lista de permissões não efetuou efeito ainda:** poderão existir como sucederia com um atraso de cinco minutos para as alterações à base de dados do Azure para a configuração da firewall do servidor de MySQL entrem em vigor.

* **O início de sessão não está autorizado ou foi utilizada uma palavra-passe incorreta:** se um início de sessão não tem permissões na base de dados do Azure para o servidor de MySQL ou a palavra-passe utilizada está incorreta, a ligação à base de dados do Azure para o servidor de MySQL é negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento de IP dinâmico e estiver a ter problemas em obter através da firewall, pode tentar uma das seguintes soluções:

* Peça ao seu fornecedor de serviços Internet (ISP) para o intervalo de endereços IP atribuído aos computadores cliente que acedem a base de dados do Azure para o servidor de MySQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

## <a name="next-steps"></a>Passos seguintes

[Criar e gerir a base de dados do Azure MySQL das regras de firewall com o portal do Azure](./howto-manage-firewall-using-portal.md)
[criar e gerir a base de dados do Azure para as regras de firewall de MySQL utilizando a CLI do Azure](./howto-manage-firewall-using-cli.md)
