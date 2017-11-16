---
title: "Como manter um endereço IP virtual constante para um serviço em nuvem do Azure | Microsoft Docs"
description: "Saiba como Certifique-se de que não altere o endereço IP virtual (VIP) do seu serviço em nuvem do Azure."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 96c4ed88db5e24f439825aee5afe457a1e8c81d7
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Manter um endereço IP virtual constante para um serviço em nuvem do Azure
Quando atualizar um serviço em nuvem que está alojado no Azure, poderá ter de garantir que não altera o endereço IP virtual (VIP) do serviço. Muitos serviços de gestão do domínio utilizam o sistema de nomes de domínio (DNS) para registar nomes de domínio. DNS só funciona se o VIP permanece igual. Pode utilizar o **publicar assistente** nas ferramentas do Azure para se certificar de que o VIP do seu serviço em nuvem não altera quando efetua a atualização. Para obter mais informações sobre como utilizar a gestão de domínios DNS para serviços em nuvem, consulte [configurar um nome de domínio personalizado para um serviço em nuvem do Azure](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publicar um serviço em nuvem sem alterar o VIP
O VIP de um serviço em nuvem é atribuído ao primeiro implementá-lo no Azure num ambiente específico, como o ambiente de produção. O VIP altera-se apenas se eliminar a implementação explicitamente ou a implementação implicitamente é eliminada pelo processo de atualização de implementação. Para manter o VIP, não terá de eliminar a implementação e tem de se certificar de que Visual Studio não elimina a implementação automaticamente. 

Pode especificar definições de implementação no **publicar assistente**, que suporta várias opções de implementação. Pode especificar uma nova implementação ou uma implementação de atualização, que pode ser incremental ou em simultâneo. Ambos os tipos de implementação de atualização de mantém o VIP. Definições de um destes tipos diferentes de implementação, consulte [publicar Assistente da aplicação Azure](vs-azure-tools-publish-azure-application-wizard.md). Além disso, pode controlar se a implementação anterior de um serviço em nuvem é eliminada se ocorrer um erro. Se não definir essa opção corretamente, o VIP poderá alterar inesperadamente.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Atualizar um serviço em nuvem sem alterar o VIP
1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio. 

2. No **Explorador de soluções**, clique com o botão direito no projeto. No menu de atalho, selecione **publicar**.

    ![Menu Publicar](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. No **publicar aplicação Azure** diálogo caixa, selecione a subscrição do Azure para o qual pretende implementar. Iniciar sessão, se necessário e selecione **seguinte**.

    ![Publicar Azure aplicação página sessão](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. No **definições comuns** separador, certifique-se de que o nome da nuvem de serviço para que estiver a implementar, a **ambiente**, a **configuração de compilação**e o **a configuração do serviço** estão corretos.

    ![Publicar o separador de definições comuns de aplicação do Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. No **definições avançadas** separador, certifique-se de que o **etiqueta de implementação** e **conta de armazenamento** estão corretos. Certifique-se de que o **eliminar a implementação em caso de falha** caixa de verificação está desmarcada e certifique-se de que o **atualização da implementação** caixa de verificação está selecionada. Ao desmarcar a **eliminar a implementação em caso de falha** caixa de verificação, certifique-se de que o VIP não se encontra perdida se ocorrer um erro durante a implementação. Ao selecionar o **atualização da implementação** caixa de verificação, certifique-se de que a implementação não é eliminada e o VIP não é perdida quando voltar a publicar a aplicação. 

    ![Publicar o separador de definições avançadas da aplicação do Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Para obter mais especificar como pretende que as funções a ser atualizado, selecione **definições** junto a **atualização da implementação**. Selecione **atualização Incremental** ou **atualização em simultâneo**e selecione **OK**. Escolha **atualização Incremental** ao atualizar a cada instância da sua aplicação, umas a seguir, para que a aplicação está sempre disponível. Escolha **atualização em simultâneo** para atualizar todas as instâncias da aplicação ao mesmo tempo. A atualização em simultâneo é mais rápida, mas o serviço poderá não estar disponível durante o processo de atualização. Quando tiver terminado, selecione **seguinte**.

    ![Publicar a página de definições de implementação de aplicação do Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. No **publicar aplicação Azure** caixa de diálogo, selecione **seguinte** até o **resumo** é apresentada a página. Verificar as suas definições e, em seguida, selecione **publicar**.
   
    ![Publicar a página de resumo de aplicações do Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Passos seguintes
- [Com o Visual Studio publicar Assistente da aplicação do Azure](vs-azure-tools-publish-azure-application-wizard.md)

