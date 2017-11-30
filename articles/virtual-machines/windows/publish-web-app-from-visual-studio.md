---
title: "Publicar uma aplicação Web para uma VM do Azure a partir do Visual Studio | Microsoft Docs"
description: "Publicar uma aplicação ASP.NET Web para uma Máquina Virtual do Azure a partir do Visual Studio"
services: virtual-machines-windows
documentationcenter: 
author:
- kraigb
- justcla
manager: ghogen
editor: 
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2017
ms.author:
- kraigb
- justcla
ms.openlocfilehash: 5a0dd3d123cb0d580ea753cebc36ebcdb7084db9
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicar uma aplicação ASP.NET Web para uma VM do Azure a partir do Visual Studio

Este documento descreve como publicar uma aplicação web do ASP.NET para uma máquina virtual do Azure (VM) utilizando o **máquinas virtuais do Microsoft Azure** funcionalidade de publicação no Visual Studio 2017.  

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o Visual Studio para publicar um projeto ASP.NET para uma VM do Azure, a VM deve ser corretamente configurada.

- Máquina tem de ser configurada para executar uma aplicação web ASP.NET e tiver instalado o WebDeploy.

- A VM tem de ter um nome DNS configurado. Para obter mais informações, consulte [criar um nome de domínio completamente qualificado no portal do Azure para uma VM do Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicar a aplicação web do ASP.NET à VM do Azure com o Visual Studio
A seguinte secção descreve como publicar uma aplicação web ASP.NET existente para uma máquina virtual do Azure.

1. Abra a sua solução de aplicação web no Visual Studio 2017.
2. Clique com o botão direito no projeto no Explorador de soluções e escolha **publicar...**
3. Utilize a seta à direita da página para deslocar as opções de publicação até encontrar **máquinas virtuais do Microsoft Azure**.  

   ![Publicar página - seta para a direita]

4. Selecione o **máquinas virtuais do Microsoft Azure** ícone e selecione **publicar**.

   ![Publicar página - ícone de máquinas de virtuais do Microsoft Azure]

5. Selecione a conta adequada (com a subscrição do Azure ligada à máquina virtual).  
   - Se tem sessão iniciada Visual Studio, a lista de conta é preenchida com todas as suas contas autenticadas.  
   - Se não tem sessão iniciada, ou se a conta tem de não estiver listada, escolha "Adicionar uma conta..." e siga as instruções para iniciar sessão.  
   ![Seletor de conta do Azure]  

6. Selecione a VM adequada na lista de máquinas de virtuais existentes.

   > [!Note]
   > Povoar esta lista pode demorar algum tempo.

   ![Seletor VM do Azure]

7. Clique em OK para iniciar a publicação.

8. Quando lhe forem pedidas as credenciais, forneça o nome de utilizador e palavra-passe de uma conta de utilizador no destino VM que está configurado com a publicação de direitos (normalmente, o nome de utilizador administrador e palavra-passe utilizada durante a criação da VM).  

   ![O WebDeploy início de sessão]

9. Aceite o certificado de segurança.

   ![Erro de certificado]

10. Assista a janela de resultados para verificar o progresso da operação publicar.

    ![Janela de saída]

11. Se publicação for bem sucedida, inicia um browser para abrir o URL do site publicado recentemente.

**Êxito!**

Agora publicou com êxito a aplicação web a uma máquina virtual do Azure.

## <a name="publish-page-options"></a>Página Opções de publicação

Depois de concluir o Assistente de publicação, a página de publicar é aberta no documento bem com o novo perfil de publicação selecionado.

### <a name="re-publish"></a>Publicar novamente

Para publicar as atualizações à sua aplicação web, selecione o **publicar** botão na página de publicar.  
- Se lhe for solicitado, introduza o nome de utilizador e palavra-passe.  
- Publicação começa de imediato.

![Publicar página - botão publicar]

### <a name="modify-publish-profile-settings"></a>Modificar definições de perfil de publicação

Para ver e modificar as definições de perfil de publicação, selecione **definições...** .  

![Publicar página - botão de definições]

As definições deverá ter um aspeto semelhante ao seguinte:  

![Publicar a definições - página de ligação]

#### <a name="save-user-name-and-password"></a>Guarde o nome de utilizador e palavra-passe
- Para evitar a fornecer as informações de autenticação sempre que publicar, pode preencher o **nome de utilizador** e **palavra-passe** campos e selecione o **palavra-passe de guardar** caixa.
- Utilize o **validar ligação** botão para confirmar se introduziu as informações corretas.

#### <a name="deploy-to-clean-web-server"></a>Implementar a limpeza de servidor web

- Se pretender certificar-se de que o servidor web tem uma cópia limpa da aplicação web depois de cada carregamento (e que não existem outros ficheiros restantes hanging em torno de uma implementação anterior), pode verificar o **remover ficheiros adicionais no destino** na caixa de verificação a **definições** separador.

- Aviso: A publicação com esta definição elimina todos os ficheiros que existem no servidor web (diretório wwwroot). Lembre-se de que sabe o estado da máquina antes de publicar com esta opção ativada. 

![Definições - página de definições de publicação]

## <a name="next-steps"></a>Passos seguintes

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configurar CI/CD para implementação automatizada para a VM do Azure

Para configurar um pipeline de entrega contínua com o serviço de equipa do Visual Studio, consulte [implementar uma Máquina Virtual para Windows](https://docs.microsoft.com/en-us/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publicar página - seta para a direita]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publicar página - ícone de máquinas de virtuais do Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Seletor de conta do Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Seletor VM do Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[O WebDeploy início de sessão]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Erro de certificado]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Janela de saída]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publicar página - botão publicar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publicar página - botão de definições]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publicar a definições - página de ligação]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Definições - página de definições de publicação]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
