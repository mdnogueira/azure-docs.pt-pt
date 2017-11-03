---
title: "Azure Active Directory B2C: Introdução as políticas personalizadas | Microsoft Docs"
description: "Como começar com as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 4f14dbf4b66f10290cd4f98d56a005f97cc6a207
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: Introdução as políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir os passos neste artigo, a política personalizada irá suportar "conta local" se inscrever ou iniciar sessão através de um endereço de correio eletrónico e palavra-passe. Também irá preparar o ambiente para o adicionar fornecedores de identidade (como o Facebook ou do Azure Active Directory). Aconselhamo-lo para concluir estes passos antes de ler sobre outras utilizações da estrutura de experiência de identidade do Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que tem um inquilino do Azure AD B2C, que é um contentor para todos os seus utilizadores, aplicações, políticas e muito mais. Se ainda não tiver uma, terá de [criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Aconselhamo vivamente que todos os programadores para concluir as instruções de política incorporadas do Azure AD B2C e configurar as aplicações com políticas incorporadas antes de continuar. As aplicações funcionarão com ambos os tipos de políticas depois de efetuar uma alteração secundária para o nome da política para invocar a política personalizada.

>[!NOTE]
>Para aceder a edição de política personalizada, precisa de uma subscrição do Azure válida ligada ao seu inquilino. Se ainda não o [ligado o seu inquilino do Azure AD B2C para uma subscrição do Azure](active-directory-b2c-how-to-enable-billing.md) ou a sua subscrição do Azure está desativada, o botão de estrutura de experiência de identidade não estarão disponível.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Adicione chaves de assinatura e encriptação para o seu inquilino do B2C para utilização por políticas personalizadas

1. Abra o **identidade experiência Framework** painel nas definições de inquilino do Azure AD B2C.
2. Selecione **política chaves** para ver as chaves disponíveis no seu inquilino.
3. Crie B2C_1A_TokenSigningKeyContainer se não existir:<br>
    a. Selecione **Adicionar**. <br>
    b. Selecione **gerar**.<br>
    c. Para **nome**, utilize `TokenSigningKeyContainer`. <br> 
    O prefixo `B2C_1A_` podem ser adicionados automaticamente.<br>
    d. Para **tipo de chave**, utilize **RSA**.<br>
    e. Para **datas**, utilize as predefinições. <br>
    f. Para **utilização da chave**, utilize **assinatura**.<br>
    g. Selecione **Criar**.<br>
4. Crie B2C_1A_TokenEncryptionKeyContainer se não existir:<br>
 a. Selecione **Adicionar**.<br>
 b. Selecione **gerar**.<br>
 c. Para **nome**, utilize `TokenEncryptionKeyContainer`. <br>
   O prefixo `B2C_1A`_ podem ser adicionados automaticamente.<br>
 d. Para **tipo de chave**, utilize **RSA**.<br>
 e. Para **datas**, utilize as predefinições.<br>
 f. Para **utilização da chave**, utilize **encriptação**.<br>
 g. Selecione **Criar**.<br>
5. Crie B2C_1A_FacebookSecret. <br>
Se já tiver um segredo de aplicação do Facebook, adicione-o como uma chave de política para o seu inquilino. Caso contrário, terá de criar a chave com um valor do marcador de posição para que as políticas de passar pela validação.<br>
 a. Selecione **Adicionar**.<br>
 b. Para **opções**, utilize **Manual**.<br>
 c. Para **nome**, utilize `FacebookSecret`. <br>
 O prefixo `B2C_1A_` podem ser adicionados automaticamente.<br>
 d. No **segredo** box, introduza o seu FacebookSecret de developers.facebook.com ou `0` como um marcador de posição. *Não é o ID de aplicação do Facebook.* <br>
 e. Para **utilização da chave**, utilize **assinatura**. <br>
 f. Selecione **criar** e confirmar a criação.

## <a name="register-identity-experience-framework-applications"></a>Registar aplicações Framework de experiência de identidade

O Azure AD B2C requer a registar duas aplicações adicionais que são utilizadas pelo motor de inscrever-se e iniciar sessão de utilizadores.

>[!NOTE]
>Tem de criar duas aplicações que ativar início de sessão utilizando as contas locais: IdentityExperienceFramework (uma aplicação web) e ProxyIdentityExperienceFramework (uma aplicação nativa) com o delegado permissão da aplicação IdentityExperienceFramework. Contas locais existem apenas no seu inquilino. Os utilizadores inscrever-se com uma combinação de endereço/palavra-passe de e-mail exclusivo a aceder às suas aplicações registado por inquilino.

### <a name="create-the-identityexperienceframework-application"></a>Criar a aplicação de IdentityExperienceFramework

1. No [portal do Azure](https://portal.azure.com), mudar para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Abra o **do Azure Active Directory** painel (não o **do Azure AD B2C** painel). Poderá ter de selecionar **mais serviços** a encontrá-lo.
3. Selecione **registos de aplicação**.
4. Selecione **novo registo de aplicação**.
   * Para **nome**, utilize `IdentityExperienceFramework`.
   * Para **tipo de aplicação**, utilize **aplicação/API Web**.
   * Para **URL de início de sessão**, utilize `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, onde `yourtenant` é o nome de domínio de inquilino do Azure AD B2C.
5. Selecione **Criar**.
6. Depois de criado, selecione a aplicação criada recentemente **IdentityExperienceFramework**.<br>
   * Selecione **propriedades**.<br>
   * Copie o ID da aplicação e guardá-lo para utilizar mais tarde.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Criar a aplicação de ProxyIdentityExperienceFramework

1. Selecione **registos de aplicação**.
1. Selecione **novo registo de aplicação**.
   * Para **nome**, utilize `ProxyIdentityExperienceFramework`.
   * Para **tipo de aplicação**, utilize **nativo**.
   * Para **URI de redirecionamento**, utilize `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, onde `yourtenant` é o seu inquilino do Azure AD B2C.
1. Selecione **Criar**.
1. Quando tiver sido criada, selecione a aplicação **ProxyIdentityExperienceFramework**.<br>
   * Selecione **propriedades**. <br>
   * Copie o ID da aplicação e guardá-lo para utilizar mais tarde.
1. Selecione **as permissões necessárias**.
1. Selecione **Adicionar**.
1. Selecione **selecionar um API**.
1. Procure o nome IdentityExperienceFramework. Selecione **IdentityExperienceFramework** na resultados e, em seguida, clique em **selecione**.
1. Selecione a caixa de verificação junto a **acesso IdentityExperienceFramework**e, em seguida, clique em **selecione**.
1. Selecione **feito**.
1. Selecione **conceder permissões**e, em seguida, confirme selecionando **Sim**.

## <a name="download-starter-pack-and-modify-policies"></a>Transferir o pacote de arranque e modificar as políticas

As políticas personalizadas são um conjunto de ficheiros XML, que têm de ser carregado para o inquilino do Azure AD B2C. Podemos fornecer os pacotes de arranque para ajudá-lo a aceder rapidamente. Cada pacote de arranque na lista seguinte contém o número mais pequeno de perfis de técnicas e percursos de utilizador necessários para atingir os cenários descritos:
 * LocalAccounts. Permite a utilização de apenas para contas locais.
 * SocialAccounts. Permite a utilização de contas sociais (ou federadas).
 * **SocialAndLocalAccounts**. Podemos utilizar este ficheiro para as instruções.
 * SocialAndLocalAccountsWithMFA. São incluídas opções social, local e multi-factor Authentication.

Cada pacote de arranque contém:

* O [ficheiro base](active-directory-b2c-overview-custom.md#policy-files) da política. Algumas modificações são necessárias para a base.
* O [ficheiro de extensão](active-directory-b2c-overview-custom.md#policy-files) da política.  Este ficheiro é onde a maioria das alterações de configuração são efetuadas.
* [Ficheiros de terceiros entidade confiadora](active-directory-b2c-overview-custom.md#policy-files) são ficheiros específicas de tarefas denominados pela sua aplicação.

>[!NOTE]
>Se o seu editor de XML suporta a validação, Valide os ficheiros contra o esquema de TrustFrameworkPolicy_0.3.0.0.xsd XML que está localizado no diretório de raiz do pacote de arranque. Validação de esquema XML identifica os erros antes de carregar.

 Vamos começar:

1. Transferir active-directory-b2c-custom-policy-starterpack a partir do GitHub. [Transferir o ficheiro. zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou executar

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Abra a pasta de SocialAndLocalAccounts.  O ficheiro de base (TrustFrameworkBase.xml) nesta pasta contém conteúdo necessário para contas locais e social empresarial. O conteúdo de redes social não interfere com os passos para obter contas locais de cópia de segurança e em execução.
3. Abra TrustFrameworkBase.xml. Se precisar de um editor de XML, [tente Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma simples.
4. Na raiz `TrustFrameworkPolicy` elemento, atualizar o `TenantId` e `PublicPolicyUri` atributos, substituindo `yourtenant.onmicrosoft.com` com o nome de domínio do seu inquilino do Azure AD B2C:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId`é o nome da política que vir no portal e o nome pelo qual este ficheiro de política é referenciado por outros ficheiros de política.

5. Guarde o ficheiro.
6. Abra TrustFrameworkExtensions.xml. Efetue as alterações de dois mesmas ao substituir `yourtenant.onmicrosoft.com` com o seu inquilino do Azure AD B2C. Efetuar a substituição do mesma no `<TenantId>` elemento para um total de três alterações. Guarde o ficheiro.
7. Abra SignUpOrSignIn.xml. Efetue as alterações mesmas ao substituir `yourtenant.onmicrosoft.com` com o seu inquilino do Azure AD B2C em três locais. Guarde o ficheiro.
8. Abra a reposição de palavra-passe e editar ficheiros de perfil. Efetue as alterações mesmas ao substituir `yourtenant.onmicrosoft.com` com o seu inquilino do Azure AD B2C em três locais em cada ficheiro. Guarde os ficheiros.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar os IDs de aplicações na sua política personalizada
Adicionar os IDs de aplicação para o ficheiro de extensões (`TrustFrameworkExtensions.xml`):

1. O ficheiro de extensões (TrustFrameworkExtensions.xml), localize o elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Substitua as instâncias de `IdentityExperienceFrameworkAppId` com o ID da aplicação a estrutura de experiência de identidade que criou anteriormente. Segue-se um exemplo:

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Substitua as instâncias de `ProxyIdentityExperienceFrameworkAppId` com o ID da aplicação a estrutura de experiência de identidade de Proxy que criou anteriormente.
4. Guarde o ficheiro de extensões.

## <a name="upload-the-policies-to-your-tenant"></a>Carregue as políticas no seu inquilino

1. No [portal do Azure](https://portal.azure.com), mudar para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e abra o **do Azure AD B2C** painel.
2. Selecione **identidade experiência Framework**.
3. Selecione **carregar política**.

    >[!WARNING]
    >Os ficheiros de política personalizada tem de ser carregados pela seguinte ordem:

1. Carregar TrustFrameworkBase.xml.
2. Carregar TrustFrameworkExtensions.xml.
3. Carregar SignUpOrSignin.xml.
4. Carregar os ficheiros de política.

Quando um ficheiro é carregado, o nome do ficheiro de política é prepended com `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada utilizando o executar agora

1. Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.

   >[!NOTE]
   >**Executar agora** requer, pelo menos, uma aplicação para preregistered no inquilino. As aplicações têm de estar registadas no inquilino do B2C, através da utilização de **aplicações** selecção de menu no Azure AD B2C ou utilizando a estrutura de experiência de identidade para invocar políticas incorporadas e personalizadas. É necessário apenas um registo por aplicação.<br><br>
   Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.  

2. Abra B2C_1A_signup_signin, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.

3. Poderá inscrever-se utilizando um endereço de correio eletrónico.

4. Inicie sessão com a mesma conta para confirmar que tem a configuração correta.

>[!NOTE]
>Uma causa comum de falha de início de sessão é uma aplicação de IdentityExperienceFramework configurada incorretamente.


## <a name="next-steps"></a>Passos seguintes

### <a name="add-facebook-as-an-identity-provider"></a>Adicionar Facebook como um fornecedor de identidade
Para configurar o Facebook:
1. [Configurar uma aplicação do Facebook no developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Adicione o segredo de aplicação do Facebook no seu inquilino do Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. No ficheiro de política TrustFrameworkExtensions, substitua o valor de `client_id` com o ID de aplicação do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Carregue o ficheiro de política de TrustFrameworkExtensions.xml no seu inquilino.
5. Teste utilizando **executar agora** ou ao invocar a política diretamente a partir da sua aplicação registada.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adicionar o Azure Active Directory como um fornecedor de identidade
O ficheiro de base utilizado neste guia de introdução ao obter já contém algum do conteúdo que necessita para adicionar outros fornecedores de identidade. Para obter informações sobre como configurar inícios de sessão, consulte o [Azure Active Directory B2C: Inicie sessão com contas do Azure AD](active-directory-b2c-setup-aad-custom.md) artigo.

Para obter uma descrição geral das políticas personalizadas no Azure AD B2C, que utilizam o Framework de experiência de identidade, consulte o [Azure Active Directory B2C: as políticas personalizadas](active-directory-b2c-overview-custom.md) artigo. 
