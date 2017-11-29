---
title: Conceitos de servidor na base de dados do Azure para MySQL | Microsoft Docs
description: "Este tópico fornece considerações e diretrizes para trabalhar com a base de dados do Azure para servidores MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d3de3fdf28997b63321bf23443472db43ebb5c52
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor na base de dados do Azure para MySQL
Este artigo fornece considerações e diretrizes para trabalhar com a base de dados do Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é uma base de dados do Azure para o servidor de MySQL?

Uma base de dados do Azure para o servidor de MySQL é um ponto de administração central para várias bases de dados. É a construção de servidor MySQL mesma que poderá estar familiarizado no mundo no local. Especificamente, a base de dados do Azure para o serviço de MySQL é gerida, fornece garantias de desempenho e expõe e funcionalidades ao nível do servidor de acesso.

Uma base de dados do Azure para o servidor de MySQL:

- É criada dentro de uma subscrição do Azure.
- É o recurso principal para bases de dados.
- Fornece um espaço de nomes para bases de dados.
- É um contentor com semântica de duração strong - eliminar um servidor e elimina as bases de dados contidas.
- Collocates recursos numa região.
- Fornece um ponto final da ligação de servidor e acesso de base de dados.
- Fornece o âmbito de políticas de gestão que se aplicam às respetivas bases de dados: início de sessão, firewall, os utilizadores, funções, configurações, etc.
- Está disponível em múltiplas versões. Para obter mais informações, consulte [suportado Database do Azure para as versões de base de dados MySQL](./concepts-supported-versions.md).

Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Pode optar por criar uma base de dados individual por servidor para utilizar todos os recursos ou para criar várias bases de dados para partilhar os recursos. Os preços é estruturado por cada servidor, com base na configuração de armazenamento (GB), unidades de computação e escalão de preço. Para obter mais informações, consulte [escalões de preço](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como ligar e autenticar para uma base de dados do Azure para o servidor de MySQL?

Os elementos seguintes ajudam a garantir o acesso seguro à sua base de dados.
|||
| :-- | :-- |
| **Autenticação e autorização** | Base de dados do Azure para o servidor de MySQL suporta a autenticação de MySQL nativa. Pode estabelecer ligação e autenticar para um servidor com início de sessão de administrador do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagem utilizado pelo MySQL. |
| **TCP/IP** | O protocolo é suportado por TCP/IP e através de sockets de domínio do Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede que todos os acessos ao seu servidor de base de dados, até especificar quais os computadores que tem permissão. Consulte [base de dados do Azure para as regras de firewall do servidor de MySQL](./concepts-firewall-rules.md). |
| **SSL** | O serviço suporta a imposição de ligações de SSL entre as suas aplicações e o servidor de base de dados.  Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL). |

## <a name="how-do-i-manage-a-server"></a>Como gerir a um servidor?
Pode gerir a base de dados do Azure para servidores MySQL, utilizando o portal do Azure ou a CLI do Azure.

## <a name="next-steps"></a>Passos seguintes
- Para obter uma descrição geral do serviço, consulte [base de dados do Azure para descrição geral de MySQL](./overview.md)
- Para obter informações sobre recursos específico quotas e limitações com base na sua **camada de serviço**, consulte [escalões de serviço](./concepts-service-tiers.md)
- Para obter informações sobre a ligação ao serviço, consulte [bibliotecas de ligação para base de dados do Azure para MySQL](./concepts-connection-libraries.md).
