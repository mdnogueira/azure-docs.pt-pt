---
title: "Como criar e implementar um serviço em nuvem | Microsoft Docs"
description: "Saiba como criar e implementar um serviço em nuvem utilizando o método de criação rápida no Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 2a2172a78bfd3ac923edbc9de366b035629dd27b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço em nuvem
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-create-deploy-portal.md)
> * [Portal Clássico do Azure](cloud-services-how-to-create-deploy.md)
> 
> 

Portal clássico do Azure fornece duas formas para criar e implementar um serviço em nuvem: **criação rápida** e **criação personalizada**.

Este tópico explica como utilizar o método de criação rápida para criar um novo serviço de nuvem e, em seguida, utilizar **carregar** para carregar e implementar um pacote de serviço em nuvem no Azure. Quando utiliza este método, o portal clássico do Azure torna disponíveis ligações convenientes para concluir todos os requisitos à medida que avança. Se estiver pronto para implementar o seu serviço em nuvem quando a criar, pode utilizar ambas ao mesmo tempo utilizando **criação personalizada**.

> [!NOTE]
> Se planeia publicar o serviço de nuvem de serviços de equipa de Studio Visual (VSTS), utilize **criação rápida**e, em seguida, configurar a publicação de VSTS de **início rápido** ou do dashboard.
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

* Se pretender implementar um serviço em nuvem que utiliza o Secure Sockets Layer (SSL) para a encriptação de dados, [configurar a sua aplicação](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) para SSL.
* Se pretender configurar ligações de ambiente de trabalho remoto para instâncias de função, [configuram as funções](cloud-services-role-enable-remote-desktop.md) para ambiente de trabalho remoto.
* Se pretender configurar verboso de monitorização do serviço em nuvem, ative o diagnóstico do Azure para o serviço em nuvem. *Monitorização mínima* (a predefinição do nível de monitorização) utiliza contadores de desempenho recolhidos de sistemas de operativos de anfitrião para instâncias de função (máquinas virtuais). "Monitorização verboso * recolhe métricas adicionais com base nos dados de desempenho dentro de instâncias de função para ativar a análise quanto mais próximo dos problemas que ocorrem durante o processamento da aplicação. Para saber como ativar o diagnóstico do Azure, consulte [ativar diagnósticos no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço em nuvem com as implementações de funções da web ou funções de trabalho, terá [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se ainda não instalou o SDK do Azure, clique em **instalar o Azure SDK** para abrir o [página de transferências do Azure](https://azure.microsoft.com/downloads/)e, em seguida, transferir o SDK para o idioma que preferir desenvolver o seu código. (Terá uma oportunidade para fazê-lo mais tarde.)
* Se quaisquer instâncias de função requerem um certificado, crie os certificados. Serviços cloud necessitam de um ficheiro. pfx com uma chave privada. Pode [carregar os certificados para o Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) como criar e implementar o serviço em nuvem.
* Se planeia implementar o serviço em nuvem para um grupo de afinidade, crie o grupo de afinidade. Pode utilizar um grupo de afinidade para implementar o serviço de nuvem e de outros serviços do Azure para a mesma localização numa região. Pode criar o grupo de afinidade no **redes** área da clássica do Azure portal, no **grupos de afinidades** página.

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Como: criar um serviço em nuvem utilizando criação rápida
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **novo**>**computação**>**serviço em nuvem** > **Criação rápida**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. No **URL**, introduza um nome de subdomínio para utilizar no URL público para aceder ao seu serviço em nuvem em implementações de produção. O formato de URL para implementações de produção é: http://*myURL*. cloudapp.net.
3. No **região ou grupo de afinidade**, selecione a região geográfica ou grupo de afinidade para implementar o serviço de nuvem. Selecione um grupo de afinidade, se pretender implementar o seu serviço em nuvem para a mesma localização que outros serviços do Azure numa região.
4. Clique em **Criar Serviço em Nuvem**.
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    Pode monitorizar o estado do processo na área de mensagens na parte inferior da janela.
   
    O **serviços em nuvem** área abre-se, com o novo serviço em nuvem apresentado. Quando o estado muda para criado, a criação do serviço em nuvem foi concluída com êxito.
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Como: carregar um certificado para um serviço em nuvem
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **serviços em nuvem**, clique no nome do serviço de nuvem e, em seguida, clique em **certificados**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. Clique em **carregar um certificado** ou **carregar**.
3. No **ficheiro**, utilize **procurar** para selecionar o certificado (. pfx).
4. No **palavra-passe**, introduza a chave privada do certificado.
5. Clique em **OK** (marca de verificação).
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    Pode ver o progresso do carregamento, na área de mensagens, mostrado abaixo. Quando o carregamento estiver concluído, o certificado foi adicionado à tabela. Na área de mensagens, clique em OK para fechar a mensagem.
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Como: implementar um serviço em nuvem
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **serviços em nuvem**, clique no nome do serviço de nuvem e, em seguida, clique em **Dashboard**.
2. Clique em **carregue uma nova implementação de produção** ou **carregar**.
3. No **etiqueta de implementação**, introduza um nome para a nova implementação - por exemplo, MyCloudServicev4.
4. No **pacote**, utilize **procurar** para selecionar o ficheiro de pacote de serviço (. cspkg) a utilizar.
5. No **configuração**, utilize **procurar** para selecionar o ficheiro de configuração de serviço (. cscfg) a utilizar.
6. Se o serviço em nuvem irá incluir quaisquer funções com apenas uma instância, selecione o **implementar mesmo que uma ou mais funções contenham uma única instância** caixa de verificação para ativar a implementação continuar.
   
    Azure apenas pode garantir o acesso de percentagem de 99,95 para o serviço em nuvem durante as atualizações de manutenção e o serviço se cada função tem, pelo menos, duas instâncias. Se for necessário, pode adicionar instâncias de função adicionais no **escala** página depois de implementar o serviço em nuvem. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).
7. Clique em **OK** (marca de verificação) para iniciar a implementação do serviço em nuvem.
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    Pode monitorizar o estado da implementação na área de mensagens. Clique em OK para ocultar a mensagem.
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Certifique-se a sua implementação concluída com êxito
1. Clique em **Dashboard**.
   
    O estado deve mostrar que o serviço **executar**.
2. Em **leitura rápida**, clique no URL do site para abrir o seu serviço em nuvem num web browser.
   
    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerir o serviço de nuvem](cloud-services-how-to-manage.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).

