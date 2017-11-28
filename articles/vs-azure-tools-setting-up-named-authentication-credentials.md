---
title: "Configurar as credenciais de autenticação nomeado | Microsoft Docs"
description: "Saiba como fornecer credenciais que pode utilizar o Visual Studio para autenticar pedidos para o Azure, pelo que pode publicar uma aplicação no Azure a partir do Visual Studio ou monitorizar um serviço em nuvem existente."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2017
ms.author: kraigb
ms.openlocfilehash: 12250adbfaf8621b80acd5de5de06b21e05ef07c
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="set-up-named-authentication-credentials"></a>Configurar as credenciais de autenticação com o nome

Para publicar uma aplicação no Azure ou para monitorizar um serviço em nuvem existente, o Visual Studio requer credenciais para autenticar pedidos para o Azure, nomeadamente o ID de subscrição do Azure e um certificado válido para a v3 de x. 509 com uma chave de, pelo menos, 2048 bits. Forneça estas credenciais através de um dos seguintes métodos:

- Em select do Visual Studio **vista > Explorador de servidores**, clique com botão direito do **Azure** nó, selecione **ligar à subscrição do Microsoft Azure**e inicie sessão.
- Criar um ficheiro de subscrição (`.publishsettings`), que contém uma chave pública do certificado. O ficheiro de subscrição pode conter as credenciais para a mais do que uma subscrição, conforme descrito neste artigo.

Nota: estas credenciais são diferentes das credenciais utilizadas para autenticar pedidos para serviços de armazenamento do Azure.

## <a name="create-a-subscription-file"></a>Criar um ficheiro de subscrição

No Explorador de servidores, clique com botão direito do **Azure** nó e selecione **gerir e subscrições de filtro**. Em seguida, selecione o **certificados** separador e, em seguida, efetue uma das seguintes ações:

- Selecione **importação** para abrir o **importar Microsoft Azure subscrições** caixa de diálogo. Selecione o **ficheiro de transferência de subscrição** associar e, no browser, guarde o ficheiro transferido para uma localização temporária. Novamente na caixa de diálogo, navegue até à localização de transferência e, em seguida, importá-lo para utilização na autenticação.
- Escolha uma subscrição ativa e selecione **editar**, que abre uma caixa de diálogo na qual pode edita uma subscrição existente para utilizar na autenticação.
- Selecione **novo** para abrir o **nova subscrição** diálogo caixa e forneça os detalhes necessários. Para carregar o certificado à sua nuvem serviço são assinaladas na caixa de diálogo, inicie sessão no portal do Azure, navegue até ao seu serviço de nuvem, selecione **definições > certificados de gestão**, selecione **carregar**, em seguida, Especifique o caminho para o `.cer` ficheiro.

Se pretender criar um certificado por si, pode consultar as instruções em [criar e carregar um gestão de certificados para o Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e, em seguida, carregar manualmente o certificado para o [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral geral das Web Apps](https://docs.microsoft.com/azure/app-service/)
- [Implementar a aplicação no App Service do Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
- [Implementar o WebJobs com o Visual Studio](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
- [Criar e implementar um serviço em nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)