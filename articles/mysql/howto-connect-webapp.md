---
title: "Ligar o App Service do Azure existente à base de dados do Azure para MySQL | Microsoft Docs"
description: "Instruções sobre como corretamente ligar um serviço de aplicações do Azure existente à base de dados do Azure para o MySQL"
services: mysql
author: v-chenyh
ms.author: v-chenyh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 0028b0c918b573f6884e6f63fa82dbe23079882a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ligar um serviço de aplicações do Azure existente à base de dados do Azure para o servidor de MySQL
Este tópico explica como ligar um serviço de aplicações do Azure existente na base de dados do Azure para o servidor de MySQL.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [Portal do Azure](https://portal.azure.com). Crie uma base de dados do Azure para o servidor de MySQL. Para obter detalhes, consulte [como criar a base de dados do Azure para o servidor de MySQL do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [como criar a base de dados do Azure para o servidor de MySQL utilizando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente, existem duas soluções para ativar o acesso a partir de um serviço de aplicações do Azure para uma base de dados do Azure para MySQL. Ambas as soluções envolvem configurar regras de firewall ao nível do servidor.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>Solução 1 - criar uma regra de firewall para permitir que todos os IPs
Base de dados do Azure para MySQL fornece segurança de acesso a utilizar uma firewall para proteger os seus dados. Quando ligar a partir de um serviço de aplicações do Azure para a base de dados do Azure para o servidor de MySQL, tenha em atenção que são dinâmicos natureza IPs saída do serviço de aplicações. 

Para garantir a disponibilidade do seu serviço de aplicações do Azure, recomendamos a utilização desta solução para permitir que todos os IPs.

> [!NOTE]
> Microsoft está a funcionar numa solução de longo prazo para evitar a permitir que todos os IPs para serviços do Azure ligar à base de dados do Azure para MySQL.

1. No painel de servidor MySQL, sob as definições, clique em **ligação segurança** para abrir o painel de segurança de ligação da base de dados do Azure para MySQL.

   ![Portal do Azure – clique em segurança de ligação](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Introduza **nome da regra**, **IP inicial**, e **IP final**e, em seguida, clique em **guardar**.
   - Nome da regra: permitir-All-IPs
   - Iniciar IP: 0.0.0.0
   - IP de fim: 255.255.255.255

   ![Portal do Azure – adicionar todos os IPs](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2 - criar uma regra de firewall para permitir explicitamente o IPs de saída
Pode adicionar explicitamente todos os IPs de saída do seu serviço de aplicações do Azure.

1. No painel de propriedades do serviço de aplicações, ver o **saída endereço de IP**.

   ![Portal do Azure – IPs de saída de vista](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. No painel de segurança de ligação do MySQL, adicione IPs saída um por um.

   ![Portal do Azure – adicionar IPs explícita](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Não se esqueça de **guardar** regras da firewall.

Embora o App service do Azure tenta manter os endereços IP constante ao longo do tempo, há casos em que podem alterar os endereços IP. Por exemplo, isto pode ocorrer quando recicla a aplicação ou uma operação de dimensionamento ocorre ou centros de quando os novos computadores são adicionados em dados regionais do Azure para aumentar a capacidade. Quando os endereços IP alteração, a aplicação foi tempo de inatividade nos eventos que já não consegue estabelecer ligação ao servidor MySQL. Manter esta consideração em mente quando escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração de SSL
Base de dados do Azure para MySQL tiver SSL ativado por predefinição. Se a aplicação não estiver a utilizar SSL para ligar à base de dados, terá de desativar o SSL no servidor de MySQL. Para obter mais informações sobre como configurar o SSL, consulte [utilizando o SSL com a base de dados do Azure para MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre cadeias de ligação, consulte [cadeias de ligação](howto-connection-string.md).
