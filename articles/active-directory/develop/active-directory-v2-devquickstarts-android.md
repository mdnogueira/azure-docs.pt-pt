---
title: "Aplicação Android de v 2.0 do Azure Active Directory | Microsoft Docs"
description: "Como criar uma aplicação Android que inicia sessão dos utilizadores com tanto pessoais conta Microsoft e trabalho ou escola contas e chamadas a Graph API através da utilização de bibliotecas de terceiros."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: c0a5a818c61f7af7ff04bf890b54e8364f3b21b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar início de sessão a uma aplicação Android utilizando uma biblioteca de terceiros com Graph API utilizando o ponto final v 2.0
A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Os programadores podem utilizar qualquer biblioteca que pretendem integrar aos nossos serviços. Para ajudar os programadores a utilizar a nossa plataforma com outras bibliotecas, escrevemos algumas instruções como esta para demonstrar como configurar bibliotecas de terceiros para ligar à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação de especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pode ligar à plataforma de identidade da Microsoft.

Com a aplicação que cria esta explicação passo a passo, os utilizadores podem iniciar sessão na sua organização e, em seguida, procure próprios na sua organização ao utilizar a Graph API.

Se estiver familiarizado com o OAuth2 ou o OpenID Connect, muito este exemplo de configuração poderá não fazer sentido para si. Recomendamos que leia [2.0 protocolos - fluxo de código do OAuth 2.0 autorização](active-directory-v2-protocols-oauth-code.md) para em segundo plano.

> [!NOTE]
> Algumas funcionalidades da nossa plataforma que têm uma expressão as normas de OAuth2 ou o OpenID Connect, como o acesso condicional e gestão de políticas do Intune, tem de utilizar nosso bibliotecas de identidade do Microsoft Azure de código aberto.
> 
> 

O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades.

> [!NOTE]
> Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Transferir o código a partir do GitHub
O código deste tutorial [é mantido no GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Para acompanhar, pode [transferir a estrutura da aplicação como um. zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Pode também simplesmente transferir o exemplo e começar imediatamente:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registar uma aplicação
Criar uma nova aplicação, o [portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga os passos detalhados em [como registar uma aplicação com o ponto final v 2.0](active-directory-v2-app-registration.md).  Certifique-se de que:

* Copiar o **Id da aplicação** que está atribuído à sua aplicação, porque irá precisar das mesmas em breve.
* Adicionar o **Mobile** plataforma para a sua aplicação.

> Nota: O portal de registo de aplicações fornece um **URI de redirecionamento** valor. No entanto, neste exemplo tem de utilizar o valor predefinido de `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Transferir a biblioteca de terceiros NXOAuth2 e criar uma área de trabalho
Nestas instruções, irá utilizar o OIDCAndroidLib a partir do GitHub, o que é uma biblioteca OAuth2 com base no código OpenID Connect do Google. Implementa o perfil de aplicação nativa e suporta o ponto final de autorização do utilizador. Estes são todos os aspetos que terá para integrar com a plataforma de identidade da Microsoft.

Clone o repositório de OIDCAndroidLib para o seu computador.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Configurar o ambiente do Android Studio
1. Criar um novo projeto do Android Studio e aceite as predefinições do assistente.
   
    ![Criar novo projeto no Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Dispositivos Android de destino](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Adicionar uma atividade ao mobile](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Para configurar os módulos de projeto, mova o repositório clonado para a localização de projeto. Pode também criar o projeto e, em seguida, clone-o diretamente para a localização do projeto.
   
    ![Módulos do projeto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Abra as definições de módulos do projeto, utilizando o menu de contexto ou utilizando o atalho Ctrl + Alt + Maj + S.
   
    ![Definições de módulos do projeto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Remova o módulo de aplicação predefinido porque apenas pretende as definições de contentor do projeto.
   
    ![O módulo de aplicação predefinido](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Importe os módulos do repositório clonado ao projeto atual.
   
    ![Importar projeto do gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![página de criação de novo módulo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Repita estes passos para o `oidlib-sample` módulo.
7. Verificar as dependências de oidclib o `oidlib-sample` módulo.
   
    ![dependências de oidclib no módulo oidlib exemplo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Clique em **OK** e a aguardar sincronização do gradle.
   
    O settings.gradle deve ter o seguinte aspeto:
   
    ![Captura de ecrã do settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Criar a aplicação de exemplo para se certificar de que o exemplo funcionar corretamente.
   
    Não poderá utilizar este ainda no Azure Active Directory. Vamos precisar de configurar alguns pontos finais primeiro. Isto é para garantir que não tem um problemas do Android Studio antes vamos iniciar personalizar a aplicação de exemplo.
10. Compilar e executar `oidlib-sample` como o destino no Android Studio.
    
    ![Progresso da compilação de exemplo oidlib](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Eliminar o `app ` diretório foi deixado quando remover o módulo do projeto, porque o Android Studio não elimine-o para segurança.
    
    ![Estrutura do ficheiro que inclui o diretório de aplicação](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Abra o **editar configurações** menu a remoção da configuração de execução que também foi deixada quando remover o módulo do projeto.
    
    ![Editar o menu de configurações](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![execute a configuração da aplicação](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Configurar os pontos finais de exemplo
Agora que tem o `oidlib-sample` com êxito, vamos editar alguns pontos finais para obter este trabalho com o Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Configurar o cliente editando o ficheiro oidc_clientconf.xml
1. Porque estão a utilizar o OAuth2 fluxos apenas para obter um token e chamar a API de gráfico, defina o cliente para efetuar o OAuth2 apenas. OIDC ficará-se um exemplo posterior.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Configure o seu ID de cliente que recebeu do portal de registo.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Configure o URI de redirecionamento com aquele abaixo.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Configure os âmbitos que precisa para aceder à API do gráfico.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

O `User.Read` valor `oidc_scopes` permite-lhe ler o perfil básico assinada no utilizador.
Pode saber mais sobre todos os âmbitos disponíveis em [âmbitos de permissões de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Se pretender que uma explicação sobre `openid` ou `offline_access` como âmbitos OpenID Connect, consulte [2.0 protocolos - fluxo de código do OAuth 2.0 autorização](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Configurar os pontos finais de cliente editando o ficheiro oidc_endpoints.xml
* Abra o `oidc_endpoints.xml` de ficheiros e efetuar as seguintes alterações:
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Estes pontos finais nunca devem ser alterado se estiver a utilizar o OAuth2 como o protocolo.

> [!NOTE]
> Os pontos finais para `userInfoEndpoint` e `revocationEndpoint` não são atualmente suportadas pelo Azure Active Directory. Se deixar estas com o valor de example.com predefinido, é será reminded que estes não estão disponíveis na amostra :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>Configurar uma chamada de API do gráfico
* Abra o `HomeActivity.java` de ficheiros e efetuar as seguintes alterações:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Aqui uma chamada de Graph API simple devolve nosso informações.

Trata-se de todas as alterações que precisa de fazer. Execute o `oidlib-sample` aplicação e clique em **sessão**.

Depois de ter autenticadas com êxito, selecione o **pedido recursos protegidos** botão para testar a sua chamada à API do gráfico.

## <a name="get-security-updates-for-our-product"></a>Obter as atualizações de segurança para o nosso produto
Aconselhamo-lo a obter notificações sobre incidentes de segurança, visitando o [TechCenter de segurança](https://technet.microsoft.com/security/dd252948) e subscrever SUBSCREVENDO os alertas de segurança.

