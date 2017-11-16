---
title: "Como criar e implementar um serviço em nuvem | Microsoft Docs"
description: "Saiba como criar e implementar um serviço em nuvem no portal do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 020e2996f40ed4a48affd3776e44e382c40cb3c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço em nuvem
O portal do Azure fornece duas formas para criar e implementar um serviço em nuvem: *criação rápida* e *criação personalizada*.

Este artigo explica como utilizar o método de criação rápida para criar um novo serviço de nuvem e, em seguida, utilizar **carregar** para carregar e implementar um pacote de serviço em nuvem no Azure. Quando utilizar este método, o portal do Azure torna disponíveis ligações convenientes para concluir todos os requisitos à medida que avança. Se estiver pronto para implementar o seu serviço em nuvem quando a criar, pode efetuar ambas ao mesmo tempo utilizando criação personalizada.

> [!NOTE]
> Se planeia publicar o serviço de nuvem de serviços de equipa de Studio Visual (VSTS), utilize a criação rápida e, em seguida, configurar a publicação de VSTS de início rápido do Azure ou do dashboard. Para obter mais informações, consulte [contínua de entrega para o Azure ao utilizar o Visual Studio Team Services][TFSTutorialForCloudService], ou consulte a ajuda para o **início rápido** página.
>
>

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implementar uma aplicação como um serviço em nuvem no Azure:

* **Definição de serviço**  
  O ficheiro de definição de serviço em nuvem (. csdef) define o modelo de serviço, incluindo o número de funções.
* **Configuração do serviço**  
  O ficheiro de configuração do serviço de nuvem (. cscfg) fornece definições de configuração para a nuvem de funções de serviços e individuais, incluindo o número de instâncias de função.
* **Pacote de serviço**  
  O pacote de serviço (. cspkg) contém o código de aplicação e as configurações e o ficheiro de definição de serviço.

Pode saber mais sobre estas e como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparar a sua aplicação
Antes de poder implementar um serviço em nuvem, tem de criar o pacote de serviço de nuvem (. cspkg) do código da aplicação e um ficheiro de configuração do serviço de nuvem (. cscfg). O SDK do Azure fornece ferramentas para se preparar estes ficheiros de implementação necessária. Pode instalar o SDK do [Azure transfere](https://azure.microsoft.com/downloads/) página, no idioma que preferir desenvolver o seu código de aplicação.

Funcionalidades do serviço de nuvem três requerem configurações especiais antes de exportar um pacote de serviço:

* Se pretender implementar um serviço em nuvem que utiliza o Secure Sockets Layer (SSL) para a encriptação de dados, [configurar a sua aplicação](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.
* Se pretender configurar ligações de ambiente de trabalho remoto para instâncias de função, [configuram as funções](cloud-services-role-enable-remote-desktop-new-portal.md) para ambiente de trabalho remoto.
* Se pretender configurar verboso de monitorização do serviço em nuvem, ative o diagnóstico do Azure para o serviço em nuvem. *Monitorização mínima* (a predefinição do nível de monitorização) utiliza contadores de desempenho recolhidos de sistemas de operativos de anfitrião para instâncias de função (máquinas virtuais). *Monitorização verboso* recolhe métricas adicionais com base nos dados de desempenho dentro de instâncias de função para ativar a análise quanto mais próximo dos problemas que ocorrem durante o processamento da aplicação. Para saber como ativar o diagnóstico do Azure, consulte [ativar diagnósticos no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço em nuvem com as implementações de funções da web ou funções de trabalho, terá [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se ainda não instalou o SDK do Azure, clique em **instalar o Azure SDK** para abrir o [página de transferências do Azure](https://azure.microsoft.com/downloads/)e, em seguida, transferir o SDK para o idioma que preferir desenvolver o seu código. (Terá uma oportunidade para fazê-lo mais tarde.)
* Se quaisquer instâncias de função requerem um certificado, crie os certificados. Serviços cloud necessitam de um ficheiro. pfx com uma chave privada. Pode carregar os certificados para o Azure, como criar e implementar o serviço em nuvem.

## <a name="create-and-deploy"></a>Criar e implementar
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **novo > computação**e, em seguida, desloque para baixo para e clique em **serviço em nuvem**.

    ![Publicar o seu serviço em nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Na nova **serviço em nuvem** painel, introduza um valor para o **nome DNS**.
4. Crie um novo **grupo de recursos** ou selecione um existente.
5. Selecione uma **Localização**.
6. Clique em **pacote**. Esta ação irá abrir o **carregar um pacote** painel. Preencha os campos obrigatórios. Se qualquer uma das suas funções contenham uma única instância, certifique-se **implementar mesmo que uma ou mais funções contenham uma única instância** está selecionada.
7. Certifique-se de que **iniciar a implementação** está selecionada.
8. Clique em **OK** que irá fechar o **carregar um pacote** painel.
9. Se não tiver quaisquer certificados para adicionar, clique em **criar**.

    ![Publicar o seu serviço em nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Carregar um certificado
Se o pacote de implementação foi [configurado para utilizar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), pode carregar o certificado agora.

1. Selecione **certificados**e o **adicionar certificados** painel, selecione o ficheiro. pfx do certificado SSL e, em seguida, forneça o **palavra-passe** para o certificado
2. Clique em **anexar certificado**e, em seguida, clique em **OK** no **adicionar certificados** painel.
3. Clique em **criar** no **serviço em nuvem** painel. Quando a implementação foi atingido o **pronto** Estado, pode avançar para os passos seguintes.

    ![Publicar o seu serviço em nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Certifique-se a sua implementação concluída com êxito
1. Clique na instância do serviço em nuvem.

    O estado deve mostrar que o serviço **executar**.
2. Em **Essentials**, clique em de **URL do Site** para abrir o seu serviço em nuvem num web browser.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).
