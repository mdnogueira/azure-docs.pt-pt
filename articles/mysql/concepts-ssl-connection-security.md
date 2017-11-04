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
ms.date: 11/02/2017
ms.openlocfilehash: 37e2bbbe1ed4b6a9cca0e0b001e5e632b9b73be2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL na base de dados do Azure para MySQL
Base de dados do Azure para MySQL suporta a ligação do servidor de base de dados para aplicações de cliente, utilizando Secure Sockets Layer (SSL). Imposição de ligações de SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man no meio" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="default-settings"></a>As predefinições
Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar a MySQL.  É recomendado para evitar a desativar a opção SSL, sempre que possível. 

Quando aprovisionar uma nova base de dados do Azure para o servidor de MySQL através do portal do Azure e a CLI, a imposição de ligações de SSL está ativada por predefinição. 

Cadeias de ligação para várias linguagens de programação são apresentadas no portal do Azure. As cadeias de ligação incluem os parâmetros SSL necessários para ligar à base de dados. No portal do Azure, selecione o seu servidor. Sob o **definições** cabeçalho, selecione o **cadeias de ligação**. O parâmetro SSL varia consoante o conector, por exemplo "ssl = true" ou "sslmode = requerem" ou "sslmode = necessária" e outras variações.

Para saber como ativar ou desativar a ligação SSL quando desenvolver aplicações, consulte [como configurar o SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Passos seguintes
[Bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md)
