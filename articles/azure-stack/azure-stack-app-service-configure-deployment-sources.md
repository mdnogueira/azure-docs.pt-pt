---
title: "Configurar origens de implementação para os serviços de aplicação na pilha do Azure | Microsoft Docs"
description: "Como um administrador de serviço podem configurar origens de implementação (Git, GitHub, BitBucket, DropBox e OneDrive) para serviço de aplicações na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 82b6002557431f87de8fd206b4d7f4a07dec08b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-deployment-sources"></a>Configurar origens de implementação

Serviço de aplicações na pilha do Azure suporta a pedido implementação de vários fornecedores de controlo de origem. Esta funcionalidade permite aos programadores de aplicações implementar direta a partir do respetivos repositórios de controlo de origem. Se quiser configurar o serviço de aplicações para ligar ao respetivos repositórios de utilizadores, um operador da nuvem tem primeiro de configurar a integração entre o serviço de aplicações na pilha do Azure e o fornecedor do controlo de origem.  

Os fornecedores de controlo de origem suportada, além de local Git, são:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Origens de implementação de vista de administração do serviço de aplicações

1. Inicie sessão no Portal de administração de pilha do Azure (https://adminportal.local.azurestack.external) como o administrador de serviço.
2. Navegue até à **fornecedores de recursos** e selecione o **Admin da aplicação do serviço de recurso fornecedor**.  ![Administração de fornecedor de recursos do serviço de aplicações][1]
3. Clique em **configuração de controlo de origem**.  Aqui pode ver a lista de todas as origens de implementação configurado.
    ![Configuração de controlo de origem de administração de fornecedor recursos do serviço de aplicações][2]

## <a name="configure-github"></a>Configurar o GitHub

Tem de ter uma conta GitHub para concluir esta tarefa. Pode querer utilizar uma conta para a sua organização em vez de uma conta pessoal.

1. Inicie sessão no GitHub, navegue até à https://www.github.com/settings/developers e clique em **registar uma nova aplicação**.
    ![GitHub - registar uma nova aplicação][3]
2. Introduza um **nome da aplicação** por exemplo - o serviço de aplicações na pilha do Azure.
3. Introduza o **home page URL**. O URL de home page tem de ser o endereço de Portal de pilha do Azure. Por exemplo, https://portal.local.azurestack.external.
4. Introduza um **descrição da aplicação**.
5. Introduza o **URL de chamada de retorno de autorização**.  Numa implementação do Azure pilha predefinida, o Url está a ser https://portal.local.azurestack.external/tokenauthorize formulário, se estiver a executar em substitui a um domínio diferente do domínio para azurestack.local.
    ![GitHub - registar uma nova aplicação com valores preenchida][4]
6. Clique em **registar a aplicação**.  Agora será apresentada com uma listagem da página o **ID de cliente** e **segredo do cliente** para a aplicação.
    ![GitHub - registo de aplicação concluída][5]
7.  Num novo separador do browser ou janela inicie sessão no Portal do Azure pilha Admin (https://adminportal.local.azurestack.external) como o administrador de serviço.
8.  Navegue até à **fornecedores de recursos** e selecione o **Admin da aplicação do serviço de recurso fornecedor**.
9. Clique em **configuração de controlo de origem**.
10. Copie e cole o **Id de cliente** e **segredo do cliente** caixas de para a entrada correspondente para o GitHub.
11. Clique em **Guardar**.

## <a name="configure-bitbucket"></a>Configurar BitBucket

Tem de ter uma conta de BitBucket para concluir esta tarefa. Pode querer utilizar uma conta para a sua organização em vez de uma conta pessoal.

1. Inicie sessão no BitBucket e navegue até à **integrações** na sua conta.
    ![Dashboard de BitBucket - integrações][7]
2. Clique em **OAuth** em gestão de acesso e **adicionar consumidor**.
    ![BitBucket adicionar consumidor do OAuth][8]
3. Introduza um **nome** para o consumidor, por exemplo o serviço de aplicações na pilha do Azure.
4. Introduza um **Descrição** para a aplicação.
5. Introduza o **URL de chamada de retorno**.  Numa implementação do Azure pilha predefinida, o Url de chamada de retorno consta https://portal.local.azurestack.external/TokenAuthorize formulário, se estiver a executar em substitui a um domínio diferente do domínio para azurestack.local.  O Url tem de seguir a maiúsculas/minúsculas tal como indicado aqui BitBucket integração com êxito.
6. Introduza o **URL** -este Url deve ser o URL do Portal de pilha do Azure, por exemplo https://portal.local.azurestack.external.
7. Selecione o **permissões** necessário:
    - **Repositórios**: *leitura*
    - **Webhooks**: *leitura e escrita*
8. Clique em **Guardar**.  Agora verá esta nova aplicação, juntamente com o **chave** e **segredo** em **OAuth consumidores**.
    ![Lista de aplicações BitBucket][9]
9.  Num novo separador do browser ou janela inicie sessão no Portal do Azure pilha Admin (https://adminportal.local.azurestack.external) como o administrador de serviço.
10.  Navegue até à **fornecedores de recursos** e selecione o **Admin da aplicação do serviço de recurso fornecedor**.
11. Clique em **configuração de controlo de origem**.
12. Copie e cole o **chave** para o **Id de cliente** caixa de entrada e **segredo** para o **segredo do cliente** caixa de entrada para o BitBucket.
13. Clique em **Guardar**.


## <a name="configure-onedrive"></a>Configurar o OneDrive

Tem de ter uma Account Microsoft ligado a uma conta do OneDrive para concluir esta tarefa.  Pode querer utilizar uma conta para a sua organização em vez de uma conta pessoal.

> [!NOTE]
> OneDrive para empresas contas não são atualmente suportadas.

1. Procurar para https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm e inicie sessão com a sua Account Microsoft.
2. Em **as minhas aplicações**, clique em **adicionar uma aplicação**.
![Aplicações do OneDrive][10]
3. Introduza um **nome** para o novo registo de aplicação, introduza **do serviço de aplicações na pilha de Azure**e clique em **Criar aplicação**
4. O ecrã seguinte lista as propriedades da nova aplicação. Registo de **Id da aplicação**. ![Propriedades da aplicação OneDrive][11]
5. Em **aplicação segredos**, clique em **gerar a nova palavra-passe**. Tome nota do **nova palavra-passe gerada**. Este é o seu segredo de aplicação e não é recuperável depois de clicar em **OK** nesta fase.
6. Em **plataformas** clique **adicionar plataforma** e selecione **Web**.
7. Introduza o **URI de redirecionamento**.  Numa implementação do Azure pilha predefinida, o URI de redirecionamento está a ser https://portal.local.azurestack.external/tokenauthorize formulário, se estiver a executar em substitui a um domínio diferente do domínio para azurestack.local ![aplicação OneDrive - adicionar Plataforma Web][12]
8. Adicionar o **Microsoft Graph permissões** - **permissões delegadas**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplicação OneDrive - permissões do gráfico][13]
9. Clique em **Guardar**.
10.  Num novo separador do browser ou janela inicie sessão no Portal do Azure pilha Admin (https://adminportal.local.azurestack.external) como o administrador de serviço.
11.  Navegue até à **fornecedores de recursos** e selecione o **Admin da aplicação do serviço de recurso fornecedor**.
12. Clique em **configuração de controlo de origem**.
13. Copie e cole o **Id da aplicação** para o **Id de cliente** caixa de entrada e **palavra-passe** para o **segredo do cliente** caixa de entrada para o OneDrive.
14. Clique em **Guardar**.

## <a name="configure-dropbox"></a>Configurar o DropBox

> [!NOTE]
> Tem de ter uma conta DropBox para concluir esta tarefa.  Pode pretender utilizar uma conta para a sua organização em vez de uma conta pessoal.

1. Procurar para https://www.dropbox.com/developers/apps e inicie sessão com a sua conta DropBox.
2. Clique em **Criar aplicação**.

    ![Dropbox aplicações][14]

3. Selecione **DropBox API**.
4. Defina o acesso de nível para **pasta da aplicação**.
5. Introduza um **nome** para a sua aplicação.
![Registo de aplicação Dropbox][15]
6. Clique em **Criar aplicação**.  Agora será apresentada com uma página lista as definições para a aplicação, incluindo **chave da aplicação** e **segredo da aplicação**.
7. Verifique o **nome de pasta da aplicação** está definido como **do serviço de aplicações na pilha de Azure**.
8. Definir o **URI de redirecionamento OAuth 2** e clique em **adicionar**.  Numa implementação do Azure pilha predefinida, o URI de redirecionamento está em https://portal.local.azurestack.external/tokenauthorize formulário, se estiver a executar em substitui a um domínio diferente do domínio para azurestack.local.
![Configuração da aplicação Dropbox][16]
9.  Num novo separador do browser ou janela inicie sessão no Portal do Azure pilha Admin (https://adminportal.local.azurestack.external) como o administrador de serviço.
10.  Navegue até à **fornecedores de recursos** e selecione o **Admin da aplicação do serviço de recurso fornecedor**.
11. Clique em **configuração de controlo de origem**.
12. Copie e cole o **chave da aplicação** para o **Id de cliente** caixa de entrada e **segredo da aplicação** para o **segredo do cliente** caixa de entrada para a DropBox.
13. Clique em **Guardar**.


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>Passos seguintes

Os utilizadores podem agora utilizar as origens de implementação para coisas como [a implementação contínua](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-continuous-deployment), [implementação de Git local](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-deploy-local-git), e [pasta sincronização de nuvem](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-deploy-content-sync).
