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
ms.date: 8/22/2017
ms.author: kraigb
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-named-authentication-credentials"></a>Configurar as credenciais de autenticação com o nome
Para publicar uma aplicação no Azure a partir do Visual Studio ou para monitorizar um serviço em nuvem existente, tem de fornecer credenciais que o Visual Studio podem utilizar para autenticar pedidos para o Azure. Existem vários locais no Visual Studio, onde pode iniciar sessão para fornecer estas credenciais. Por exemplo, no Explorador de servidores, pode abrir o menu de atalho para o **Azure** nó e selecione **ligar à subscrição do Microsoft Azure**. Quando iniciar sessão, as informações de subscrição associado à sua conta do Azure estão disponíveis no Visual Studio. Não há nada que mais tem de fazer.

Ferramentas do Azure também suporta uma forma de fornecer credenciais anterior: a utilizar o ficheiro de subscrição (ficheiro. publishsettings). Este artigo descreve este método, que ainda é suportado no Azure SDK 2.2.

Os itens seguintes são necessários para a autenticação do Azure:

* O ID de subscrição
* Um certificado válido de x. 509v3

> [!NOTE]
> O comprimento da chave do certificado de x. 509 v3 tem de ser, pelo menos, 2.048 bits. Azure rejeita qualquer certificado que não cumpram este requisito ou que não é válido.
>
>

Visual Studio utiliza o ID de subscrição, juntamente com os dados de certificado, como as credenciais. As credenciais adequadas são referenciadas no ficheiro de subscrição (ficheiro. publishsettings), que contém uma chave pública do certificado. O ficheiro de subscrição pode conter as credenciais para a mais do que uma subscrição.

Pode editar as informações de subscrição do **nova subscrição** ou **editar subscrição** caixa de diálogo, conforme explicado posteriormente neste artigo.

Se pretender criar um certificado por si, pode consultar as instruções em [criar e carregar um gestão de certificados para o Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e, em seguida, carregar manualmente o certificado para o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Estas credenciais que requer o Visual Studio para gerir os seus serviços em nuvem não são as mesmas credenciais que são necessárias para autenticar um pedido com os serviços de armazenamento do Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importar, definir ou editar credenciais de autenticação no Visual Studio

1. No Explorador de servidores, abra o menu de atalho para o **Azure** nó e selecione **gerir e subscrições de filtro**.
2. Selecione o **certificados** separador e, em seguida, utilizar qualquer um dos seguintes métodos:

    * Selecione **importação** para abrir o **importar Microsoft Azure subscrições** caixa de diálogo. Aqui, pode transferir o ficheiro de subscrições para a subscrição actualmente carregada, navegue até à localização de transferência e, em seguida, importá-lo para utilização na autenticação.
    * Selecione **novo** para abrir o **nova subscrição** caixa de diálogo. Aqui, pode configurar uma nova subscrição para utilização na autenticação.
    * Selecione **editar** (depois de escolher a sua subscrição do Active Directory) para abrir o **editar subscrição** caixa de diálogo. Aqui, pode editar uma subscrição existente para utilizar na autenticação. 

O procedimento seguinte parte do princípio de que o **nova subscrição** caixa de diálogo está aberta.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Para configurar as credenciais de autenticação no Visual Studio
1. No **selecionar um certificado existente para a autenticação** lista, escolha um certificado.
2. Selecione o **copiar o caminho completo** ligação. O caminho para o certificado (ficheiro. cer) é copiado para a área de transferência.

   > [!IMPORTANT]
   > Para publicar a aplicação do Azure a partir do Visual Studio, tem de carregar este certificado para o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Para carregar o certificado para o portal do Azure:

   a. Abra o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Se lhe for pedida, inicie sessão portal e, em seguida, navegue para **definições** > **certificados de gestão**.
   
   c. No **certificados de gestão** painel, selecione **carregar**.
   
   d. Selecione a sua subscrição do Azure, cole o caminho completo do ficheiro. cer que acabou de criar e selecione **carregar**.

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral geral das Web Apps](https://docs.microsoft.com/azure/app-service/)
* [Implementar a aplicação no App Service do Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [Implementar o WebJobs com o Visual Studio](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [Criar e implementar um serviço em nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)