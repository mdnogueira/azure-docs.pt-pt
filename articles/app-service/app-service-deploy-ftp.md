---
title: "Implementar a aplicação no serviço de aplicações do Azure através de FTP/S | Microsoft Docs"
description: "Saiba como implementar a aplicação no serviço de aplicações do Azure através de FTP ou FTPS."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.openlocfilehash: e3ac2f2156719ad975049b0c2b4cbca81d88e779
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implementar a aplicação no serviço de aplicações do Azure através de FTP/S

Este artigo mostra como utilizar o FTP ou FTPS para implementar a sua aplicação web, o back-end da aplicação móvel ou a aplicação API para [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

O ponto final FTP/S para a sua aplicação já está ativo. É necessária ativar a implementação de FTP/S nenhuma configuração.

> [!IMPORTANT]
> Iremos estão continuamente a demorar passos para melhorar a segurança da plataforma Microsoft Azure. Como parte deste esforço em curso uma atualização de aplicações Web é planeada de regiões de Datacenters Central e nordeste da Alemanha. Durante este a aplicações Web irão desativar a utilização do protocolo FTP em texto simples para as implementações. A nossa recomendação para os nossos clientes está a mudar para FTPS para implementações. Esperamos que não seja qualquer interrupção ao seu serviço durante esta atualização que está a ser planeada 9/5. Valorizamo-lo suportar deste esforço.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Passo 1: Definir credenciais de implementação

Para aceder ao servidor FTP para a sua aplicação, primeiro precisa de credenciais de implementação. 

Para definir ou repor as credenciais de implementação, consulte o artigo [credenciais de implementação de serviço de aplicações do Azure](app-service-deployment-credentials.md). Este tutorial demonstra a utilização de credenciais de nível de utilizador.

## <a name="step-2-get-ftp-connection-information"></a>Passo 2: Obter informações de ligação de FTP

1. No [portal do Azure](https://portal.azure.com), abra a aplicação [painel de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Selecione **descrição geral** no menu à esquerda, em seguida, anote os valores para **utilizador FTP/implementação**, **nome de anfitrião do FTP**, e **nome de anfitrião FTPS**. 

    ![Informações de ligação de FTP](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > O **utilizador FTP/implementação** valor de utilizador, tal como apresentado pelo Portal do Azure, incluindo o nome da aplicação para fornecer contexto adequado para o servidor FTP.
    > Pode encontrar as informações do mesmas quando selecionar **propriedades** no menu esquerdo. 
    >
    > Além disso, a palavra-passe de implementação nunca é apresentada. Se se esquecer da sua palavra-passe de implementação, volte atrás para [passo 1](#step1) e repor a palavra-passe de implementação.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Passo 3: Implementação de ficheiros para o Azure

1. A partir do seu cliente FTP ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client), etc), utilize as informações de ligação que recolheu para ligar à sua aplicação.
3. Copiar os ficheiros e a respetiva estrutura de diretórios correspondentes para o [ **/site/wwwroot** diretório](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o **/site/wwwroot/App_Data/tarefas/** diretório para WebJobs).
4. Navegue até ao URL da sua aplicação para verificar que a aplicação está a funcionar corretamente. 

> [!NOTE] 
> Ao contrário [implementações baseadas em Git](app-service-deploy-local-git.md), implementação de FTP não suporta as automatizações de implementação seguintes: 
>
> - restauro de dependência (por exemplo, automatizações NuGet, NPM, PIP e compositor)
> - compilação de binários de .NET
> - a geração da Web. config (aqui está uma [exemplo de Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Deve restaurar, criar e gerar estes ficheiros necessários manualmente no seu computador local e implementá-las em conjunto com a sua aplicação.
>
>

## <a name="next-steps"></a>Passos seguintes

Para cenários de implementação mais avançados, tente [implementar no Azure com o Git](app-service-deploy-local-git.md). Implementação baseada em Git para o Azure permite o controlo de versão, o restauro de pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais Recursos

* [Credenciais de implementação do App Service do Azure](app-service-deploy-ftp.md)
