---
title: "Definir uma página inicial personalizada para as aplicações publicadas através do Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Abrange as noções básicas sobre conectores de Proxy de aplicações do Azure AD"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 811adc81424b8e53a740ec34f77a7610fc2a72a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma página inicial personalizada para as aplicações publicadas através do Proxy de aplicações do Azure AD

Este artigo explica como configurar aplicações para direcionar os utilizadores para uma página inicial personalizado. Quando publica uma aplicação com o Proxy de aplicações, defina um URL interno de uma só, mas por vezes, o que não é a página que os seus utilizadores devem ver primeiro. Defina uma página inicial personalizada que os utilizadores aceder à página de direita quando acedem as aplicações. Os seus utilizadores verão a home page personalizada definida, se acederem a aplicação do Azure Active Directory do painel de acesso ou o iniciador de aplicações do Office 365.

Quando os utilizadores iniciarem a aplicação, este estão direcionado por predefinição para o URL do domínio de raiz para a aplicação publicada. Normalmente, a página de destino está definida como o URL da página inicial. Utilize o módulo Azure AD PowerShell para definir os URLs de página inicial personalizada quando pretender que os utilizadores da aplicação encaminhado para uma página específica na aplicação. 

Eis um exemplo de razão pela qual uma empresa iria definir uma página inicial personalizada:
- Dentro da sua rede empresarial, os utilizadores aceder a *https://ExpenseApp/login/login.aspx* para iniciar sessão e aceder à sua aplicação.
- Como tem outros recursos, como imagens que o Proxy da aplicação precisar de aceder no nível superior da estrutura de pasta, publicar a aplicação com *https://ExpenseApp* como o URL interno.
- O URL externo predefinido é *https://ExpenseApp-contoso.msappproxy.net*, que não tem os seus utilizadores para a página de início de sessão.  
- Definir *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* como o URL da página inicial. 

>[!NOTE]
>Quando lhe conceder aos utilizadores acesso a aplicações publicadas, as aplicações são apresentadas no [painel de acesso do Azure AD](active-directory-saas-access-panel-introduction.md) e [iniciador da aplicação do Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Antes de começar

Antes de definir o URL da página inicial, tenha em consideração os seguintes requisitos:

* Certifique-se de que o caminho especificado é um caminho de subdomínios do URL do domínio de raiz.

  Se o URL do domínio de raiz, por exemplo, https://apps.contoso.com/app1/, o URL da página inicial que configurar tem de começar com https://apps.contoso.com/app1/.

* Se fizer uma alteração para a aplicação publicada, a alteração poderá repor o valor do URL da página inicial. Quando atualizar a aplicação no futuro, deve Reverificar e, se necessário, atualize o URL da página inicial.

## <a name="change-the-home-page-in-the-azure-portal"></a>Alterar a home page do portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **registos de aplicação** e escolha a aplicação a partir da lista. 
3. Selecione **propriedades** das definições.
4. Atualização do **URL da página inicial** campo com o novo caminho. 

   ![Forneça o novo URL de página inicial](./media/application-proxy-office365-app-launcher/homepage.png)

5. Selecione **guardar**

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo PowerShell do Azure AD

Antes de definir um URL de página inicial personalizada utilizando o PowerShell, instale o módulo Azure AD PowerShell. Pode transferir o pacote de [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), que utiliza o ponto final da Graph API. 

Para instalar o pacote, siga estes passos:

1. Abra uma janela do PowerShell padrão e, em seguida, execute o seguinte comando:

    ```
     Install-Module -Name AzureAD
    ```
    Se estiver a executar o comando como um não administrador, utilize o `-scope currentuser` opção.
2. Durante a instalação, selecione **Y** instalar dois pacotes a partir de Nuget.org. Ambos os pacotes são necessários. 

### <a name="find-the-objectid-of-the-app"></a>Localizar o ObjectID da aplicação

Obtenha o ObjectID da aplicação e, em seguida, procure a aplicação pela sua home page do.

1. Na janela do PowerShell do mesma, importe o módulo do Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Inicie sessão no módulo do Azure AD como administrador de inquilinos.

    ```
    Connect-AzureAD
    ```
3. Localize a aplicação com base no respetivo URL de página inicial. Pode encontrar o URL no portal, acedendo a **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações**. Este exemplo utiliza *sharepoint iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Deve obter um resultado semelhante ao apresentado aqui. Copie o GUID de ObjectID para utilizar na secção seguinte.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Atualizar o URL da página inicial

Crie o URL da página inicial e atualizar a sua aplicação com esse valor. Continue a utilizar a mesma janela do PowerShell para executar estes comandos. Ou, se estiver a utilizar uma nova janela do PowerShell, inicie sessão no módulo do Azure AD novamente utilizando `Connect-AzureAD`. 

1. Confirme que a aplicação correta e substituir *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* com o ObjectID que copiou na secção anterior.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Agora que já confirmada a aplicação, está pronto para atualizar a home page, da seguinte forma.

2. Crie um objeto de aplicação em branco para manter as alterações que pretende efetuar. Esta variável contém os valores que pretende atualizar. Nada é criado neste passo.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Defina o URL da página inicial para o valor que pretende. O valor tem de ser um caminho de subdomínio da aplicação publicada. Por exemplo, se alterar o URL da página inicial do *https://sharepoint-iddemo.msappproxy.net/* para *https://sharepoint-iddemo.msappproxy.net/hybrid/*, os utilizadores da aplicação aceda diretamente à home page personalizada .

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Efetue a atualização utilizando o GUID (ObjectID) que copiou no "passo 1: localizar o ObjectID da aplicação."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Para confirmar que a alteração foi efetuada com êxito, reinicie a aplicação.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Quaisquer alterações que efetuar para a aplicação podem repor o URL da página inicial. Se repõe o seu URL de página inicial, repita os passos nesta secção para voltar a defini-lo.

## <a name="next-steps"></a>Passos seguintes

- [Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Ativar o Proxy da aplicação no portal do Azure](active-directory-application-proxy-enable.md)
