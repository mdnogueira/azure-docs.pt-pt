---
title: Conectividade SSL para a base de dados do Azure para MySQL | Microsoft Docs
description: "Informações para configurar a base de dados do Azure para o MySQL e aplicações associadas a corretamente utilizar ligações SSL"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: 4b03b3a2dbfad92cc0cfa84777b38ddff90452cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL na base de dados do Azure para MySQL
Base de dados do Azure para MySQL suporta a ligação do servidor de base de dados para aplicações de cliente, utilizando Secure Sockets Layer (SSL). Imposição de ligações de SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man no meio" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="default-settings"></a>As predefinições
Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar a MySQL.  Recomenda-se evitar desativar a opção SSL, sempre que possível. 

Quando aprovisionar uma nova base de dados do Azure para o servidor de MySQL através do portal do Azure e a CLI, a imposição de ligações de SSL está ativada por predefinição. 

Da mesma forma, as cadeias de ligação previamente definidas nas definições do "Cadeias de ligação" sob o servidor no portal do Azure incluem os parâmetros necessários para idiomas comuns ligar ao seu servidor de base de dados através de SSL. O parâmetro SSL varia consoante o conector, por exemplo "ssl = true" ou "sslmode = requerem" ou "sslmode = necessária" e outras variações.

Para saber como ativar ou desativar a ligação SSL quando desenvolver aplicações, consulte [como configurar o SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passos seguintes
[Bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md)
