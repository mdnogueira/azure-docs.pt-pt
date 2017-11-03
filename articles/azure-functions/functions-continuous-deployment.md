---
title: "A implementação contínua para as funções do Azure | Microsoft Docs"
description: "Utilize as instalações de uma implementação contínua do serviço de aplicações do Azure para publicar as suas funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 35a0b0faa61cf4b42ba1d8696c85f5724ff73f23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure
As funções do Azure torna mais fácil de implementar a aplicação de função utilizando a integração contínua do serviço de aplicações. As funções integra BitBucket, Dropbox, GitHub e serviços de equipa do Visual Studio (VSTS). Isto permite que o fluxo de trabalho onde o código de função atualizações feito através da utilização de uma implementação de Acionador estes serviços integrados no Azure. Se estiver familiarizado com as funções do Azure, comece por [descrição geral das funções do Azure](functions-overview.md).

A implementação contínua é uma excelente opção para projetos em que várias e frequentes contribuições estão a ser integradas. Também permite-lhe manter o controlo de origem no seu código de funções. Atualmente são suportadas as seguintes origens de implementação:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repositório externo (Git ou Mercurial)
* [Repositório de Git local](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

As implementações são configuradas numa base de aplicações por função. Depois de ativar a implementação contínua, o acesso ao código de função no portal estiver definido como *só de leitura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implementação contínua

Tem de ter a origem de implementação configurada e o código de funções na origem de implementação antes de configurar a implementação contínua. Numa implementação de aplicação de função especificada, a cada função se encontra num subdiretório com nome, onde o nome do diretório é o nome da função.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Definir implementação contínua
Utilize este procedimento para configurar a implementação contínua para uma aplicação de função existente. Estes passos demonstram a integração com um repositório do GitHub, mas aplicam passos semelhantes para Visual Studio Team Services ou outros serviços de implementação.

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **funcionalidades da plataforma** e **opções de implementação**. 
   
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Em seguida, no **implementações** painel clique **configuração**.
 
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. No **origem de implementação** painel, clique em **Escolher origem**, em seguida, preencha as informações para a origem de implementação que escolheu e clique em **OK**.
   
    ![Selecione a origem de implementação](./media/functions-continuous-deployment/choose-deployment-source.png)

Após a configuração da implementação contínua, todos os ficheiros na sua origem de implementação é copiada para a aplicação de função e uma implementação de site completo é acionada. O site é implementada novamente quando são atualizados ficheiros de origem.

## <a name="deployment-options"></a>Opções de Implementação

Seguem-se alguns cenários de implementação típica:

- [Criar uma implementação de teste](#staging)
- [Mover as funções existentes para a implementação contínua](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Criar uma implementação de teste

A função aplicações ainda não suporta as ranhuras de implementação. No entanto, ainda pode gerir implementações de teste e produção separadas utilizando a integração contínua.

O processo para configurar e trabalhar com uma implementação de teste, geralmente, este aspeto:

1. Crie duas aplicações de função na sua subscrição, uma para o código de produção e outra para testes. 

2. Crie uma origem de implementação, se ainda não tiver um. Este exemplo utiliza [GitHub].

3. Para a sua aplicação de função de produção, concluir os passos anteriores **implementação contínua configurada** e defina o ramo de implementação para o ramo principal do repositório do GitHub.
   
    ![Selecione o ramo de implementação](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Repita este passo para a aplicação de função de teste, mas optar o ramo transição em vez disso, no seu repositório do GitHub. Se a origem de implementação não suporta a ramificação, utilize uma pasta diferente.
    
5. Efetuar atualizações para o código no ramo ou pasta de transição, em seguida, certifique-se de que essas alterações são refletidas na implementação de teste.

6. Depois de testar, intercalação alterações do ramo a transição para o ramo principal. Este intercalação aciona uma implementação de aplicação de função de produção. Se a origem de implementação não suporta ramos, substitua os ficheiros na pasta de produção com os ficheiros da pasta de transição.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para a implementação contínua
Quando tiver funções existentes que tiver criado e mantido no portal, tem de transferir os ficheiros de código de função existente a utilizar FTP ou o repositório de Git local antes de pode configurar a implementação contínua, tal como descrito acima. Pode fazê-lo nas definições do serviço de aplicações, para a sua aplicação de função. Depois de transferir os ficheiros, pode carregá-los para a origem de implementação contínua escolhido.

> [!NOTE]
> Depois de configurar a integração contínua, já não será possível editar os ficheiros de origem no portal de funções.

- [Como: configurar as credenciais de implementação](#credentials)
- [Como: transferir ficheiros utilizando o FTP](#downftp)
- [Como: transferir ficheiros utilizando o repositório de Git local](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Como: configurar as credenciais de implementação
Pode transferir os ficheiros da sua aplicação de função com o FTP ou o repositório de Git local, tem de configurar as suas credenciais para aceder ao site. As credenciais são definidas ao nível da aplicação de função. Utilize os seguintes passos para definir as credenciais de implementação no portal do Azure:

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **funcionalidades da plataforma** e **as credenciais de implementação**.
   
    ![Defina credenciais de implementação de local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Escreva um nome de utilizador e palavra-passe, em seguida, clique em **guardar**. Agora, pode utilizar estas credenciais para aceder à sua aplicação de função de FTP ou o repositório de Git incorporado.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Como: transferir ficheiros utilizando o FTP

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **funcionalidades da plataforma** e **propriedades**, em seguida, copie os valores para **utilizador FTP/implementação**, **nome de anfitrião do FTP**, e **nome de anfitrião FTPS**.  

    **Utilizador FTP/implementação** têm de ser introduzidos tal como apresentado no portal, incluindo o nome da aplicação, para fornecer contexto adequado para o servidor FTP.
   
    ![Obter as informações de implementação](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. A partir do seu cliente FTP, utilize as informações de ligação que recolheu para ligar a sua aplicação e transferir os ficheiros de origem para as suas funções.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Como: transferir ficheiros utilizando um repositório de Git local

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **funcionalidades da plataforma** e **opções de implementação**. 
   
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Em seguida, no **implementações** painel clique **configuração**.
 
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. No **origem de implementação** painel, clique em **repositório de Local Git** e, em seguida, clique em **OK**.

3. No **funcionalidades da plataforma**, clique em **propriedades** e anote o valor do URL do Git. 
   
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clone o repositório no seu computador local utilizando uma linha de comandos com suporte para o Git ou a ferramenta de Git favorita. O comando de clone de Git tem o seguinte aspeto:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Obter ficheiros a partir da sua aplicação de função para o clone no seu computador local, como no exemplo seguinte:
   
        git pull origin master
   
    Se solicitado, forneça o [configuradas as credenciais de implementação](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
