---
title: "Criar relatórios de instantâneo de Cloud App Discovery no Azure Active Directory | Microsoft Docs"
description: "Fornece informações sobre como localizar e gerir aplicações com o Cloud App Discovery, quais são as vantagens e como funciona."
services: active-directory
keywords: "o cloud app discovery, gestão de aplicações"
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: dd4cf725689739ce4c8b86a4d8203ffc5aff016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Criar relatórios de instantâneo de Cloud App Discovery

Antes de configurar o recoletor de registos automática, carregar manualmente um registo e permita que o Cloud App Security analisá-lo. Se ainda não tem um registo e pretender ver um exemplo de que o início de sessão deve ter o seguinte aspeto, utilize o procedimento abaixo para transferir um ficheiro de registo de exemplo para ver o que o início de sessão deveria ter o seguinte aspeto.

## <a name="to-create-a-snapshot-report"></a>Para criar um relatório de instantâneo

1. Recolha ficheiros de registo do seu servidor de firewall e proxy através do qual os utilizadores na sua organização acedem à Internet. Recolha registos fora das horas de pico de tráfego que sejam representativas da atividade de utilizador na sua organização.
2. No [barra de menus do Cloud App Security](https://portal.cloudappsecurity.com), selecione **detetar**e, em seguida, **criar instantâneo relatório**.
  
  ![Criar novo relatório de instantâneo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Introduza um **o nome do relatório** e um **Descrição**.
    
  ![Novo relatório de instantâneo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Selecione o **origem de dados** partir da qual pretende carregar os ficheiros de registo.
5. Verifique o formato de registo para se certificar de que está formatado corretamente, de acordo com o exemplo, que pode transferir. Clique em **vista e certifique-se** e, em seguida, clique em **registo de exemplo da transferência**. Em seguida, compare o registo com o exemplo fornecido para se certificar de que é compatível.
  
  ![Verifique o formato do registo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > O formato de exemplo FTP é suportado no instantâneos e automatizada carregamento enquanto syslog é suportado apenas o carregamento automático. Transferência de um registo de exemplo transfere um registo de FTP de exemplo.
6. **Escolha os registos de tráfego** que pretende carregar. Pode carregar até 20 ficheiros ao mesmo tempo. Também são suportados ficheiros comprimidos e zipped.
  
7. Clique em **Criar**. Após a conclusão do carregamento, poderá demorar algum tempo para que estes serem examinados e analisados. Se existir, o Cloud App Discovery envia uma notificação por e-mail quando que estão prontos.

8. Selecione **gerir relatórios de instantâneo** e selecione o relatório de instantâneo.
  
  ![Gestão de relatórios de instantâneo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a utilizar a Cloud App Discovery no Azure AD](cloudappdiscovery-get-started.md)
* [Configurar o carregamento de registos automática para relatórios contínua](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Utilizar um analisador de registos personalizados](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
