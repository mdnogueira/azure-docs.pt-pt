---
title: "Como ativar a SSO em várias aplicações no iOS com o ADAL | Microsoft Docs"
description: "Como utilizar as funcionalidades do SDK da ADAL para permitir início de sessão único em aplicações. "
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 73b8ed7e6a153a0790f7eae9bd51bb2e554ae72e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como ativar a SSO em várias aplicações no iOS utilizam a ADAL
Fornecer único Sign-On (SSO) para que os utilizadores só têm de introduzir as respetivas credenciais de uma vez e ter essas credenciais automaticamente funciona entre aplicações agora é esperado pelos clientes. Dificuldade em introduzindo o nome de utilizador e palavra-passe um ecrã pequeno, muitas vezes, vezes combinados com um fator adicional (2FA) como uma chamada telefónica ou um código diante, resulta em insatisfação rápida se um utilizador tem de fazer mais do que uma vez para o produto.

Além disso, se aplicar uma plataforma de identidade que poderão utilizar outras aplicações, tais como Accounts Microsoft ou uma conta de trabalho do Office 365, os clientes esperam que as credenciais estejam disponíveis para utilizar em todas as respetivas aplicações, independentemente do fornecedor.

A plataforma do Microsoft Identity, juntamente com os nossos SDKs de identidade da Microsoft, faz este disco rígido trabalho por si e dá-lhe a capacidade de delight os seus clientes com o SSO está dentro do seu próprio conjunto de aplicações ou, tal como com os nossos mediador capacidade e aplicações de autenticação, através de todo o dispositivo.

Estas instruções indicam como configurar o nosso SDK na sua aplicação para fornecer desta vantagem aos seus clientes.

Estas instruções aplicam-se a:

* Azure Active Directory
* Azure Active Directory B2C
* B2B do Azure Active Directory
* Acesso Condicional do Azure Active Directory

O documento anterior parte do princípio de que sabe como [aprovisionar aplicações no portal de legado do Azure Active Directory](active-directory-how-to-integrate.md) e integrados a sua aplicação com o [SDK iOS do Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceitos SSO na plataforma de identidade da Microsoft
### <a name="microsoft-identity-brokers"></a>Mediadores de identidade da Microsoft
A Microsoft fornece às aplicações para cada plataforma móvel que permitem que o protocolo de bridge de credenciais em todas as aplicações de fornecedores diferentes e permite funcionalidades avançadas especiais que necessitam de um único local seguro a partir de onde validar as credenciais. Chamamos a estes **mediadores**. Em dispositivos iOS e Android estes mediadores são fornecidos através de aplicações transferíveis que clientes instalar de forma independente ou podem ser enviados para o dispositivo por uma empresa que gere alguns ou todos os do dispositivo para as respetivas empregado. Estes mediadores suportam a gestão de segurança apenas para algumas aplicações ou todo o dispositivo em que os administradores de TI pretendidos ao nível. No Windows, esta funcionalidade é fornecida por um selecionador de conta incorporada para o sistema operativo, conhecido tecnicamente como o Mediador de autenticação Web.

Para obter mais informações sobre como Utilizamos estas mediadores e como os seus clientes poderão vê-los no seu fluxo de início de sessão para a plataforma do Microsoft Identity continue a ler.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões para iniciar sessão em dispositivos móveis
Acesso às credenciais em dispositivos seguir dois padrões básicos para a plataforma do Microsoft Identity:

* Inícios de sessão assistidos não-Mediador
* Mediador assistidos inícios de sessão

#### <a name="non-broker-assisted-logins"></a>Inícios de sessão assistidos não-Mediador
Inícios de sessão assistidos não-mediador são experiências de início de sessão que ocorrem de acordo com a aplicação e utilizam o armazenamento local no dispositivo para essa aplicação. Este armazenamento pode ser partilhado em todas as aplicações, mas as credenciais estão vinculadas totalmente para a aplicação ou o conjunto de aplicações com essa credencial. Tiver já provavelmente teve este em muitas aplicações móveis ao introduzir um nome de utilizador e palavra-passe dentro da própria aplicação.

Estes inícios de sessão tem as seguintes vantagens:

* Experiência de utilizador existe inteiramente a aplicação.
* As credenciais podem ser partilhadas entre aplicações que estão assinadas pelo mesmo certificado, fornecer uma experiência único início de sessão para o conjunto de aplicações.
* Controlo por volta a experiência de início de sessão é fornecido para a aplicação antes e depois do início de sessão.

Estes inícios de sessão tem as seguintes desvantagens:

* Início de sessão único não é possível experiência de utilizador em todas as aplicações que utilizam um Microsoft Identity, apenas entre essas Identities Microsoft que tenha configurado a sua aplicação.
* A aplicação não pode ser utilizado com funcionalidades empresariais mais avançadas, como o acesso condicional, ou utilizar o InTune de conjunto de produtos.
* A aplicação não suporta a autenticação baseada em certificado para os utilizadores empresariais.

Eis uma representação de como os SDKs de identidade da Microsoft trabalham com o armazenamento partilhado das suas aplicações para permitir SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Mediador assistidos inícios de sessão
Inícios de sessão assistida mediador são experiências de início de sessão que ocorrem dentro da aplicação de Mediador e utilizam o armazenamento e a segurança do Mediador para partilhar as credenciais em todas as aplicações no dispositivo que se aplicam a plataforma do Microsoft Identity. Isto significa que as aplicações que dependem broker para os utilizadores iniciar sessão. Em dispositivos iOS e Android estes mediadores são fornecidos através de aplicações transferíveis que clientes instalar de forma independente ou podem ser enviados para o dispositivo por uma empresa que gere o dispositivo para os seus utilizadores. Um exemplo deste tipo de aplicação é a aplicação Microsoft Authenticator em dispositivos iOS. No Windows, esta funcionalidade é fornecida por um selecionador de conta incorporada para o sistema operativo, conhecido tecnicamente como o Mediador de autenticação Web.
A experiência varia consoante a plataforma e, por vezes, pode ser incómoda aos utilizadores se não for gerida corretamente. Familiarizado provavelmente mais com este padrão se tiver instalada a aplicação do Facebook e utilizar o Facebook ligar a partir de outra aplicação. A plataforma do Microsoft Identity utiliza o mesmo padrão.

Para iOS, que isto leva a uma "transição" animação onde a aplicação é enviada para a imagem de fundo enquanto as aplicações do Microsoft Authenticator vem para primeiro plano para o utilizador selecionar a conta que pretende iniciar sessão.  

Para Android e Windows selecionador de conta é apresentada em cima da sua aplicação que é inferior acontece ao utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o mediador obtém invocado
Se um mediador compatível estiver instalado no dispositivo, como a aplicação Microsoft Authenticator, os SDKs de identidade do Microsoft executará automaticamente o trabalho de invocar o mediador para quando um utilizador indica que pretendem que inicie sessão com uma conta da plataforma Microsoft Identity. Esta conta pode ser uma Account Microsoft pessoal, escolar ou conta profissional, ou uma conta que está a fornecer e o anfitrião no Azure com os nossos produtos B2C e B2B. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Como podemos assegurar que a aplicação é válido
 
 A necessidade para garantir que a identidade de uma chamada de aplicação que de Mediador é fundamental para a segurança que fornecemos no Mediador assistido inícios de sessão. IOS e Android não impõe os identificadores exclusivos que só são válidos para uma determinada aplicação para que aplicações maliciosas podem "falsificar" o identificador da aplicação legítimo e receber os tokens destinam-se a aplicação legítima. Para garantir que estão a comunicar sempre com a aplicação correta no tempo de execução, vamos pedir o programador para fornecer um redirectURI personalizado quando registarem a respetiva aplicação com a Microsoft. **Como os programadores devem craft este URI de redirecionamento é abordada em detalhe abaixo.** Este redirectURI personalizado contém o ID do pacote da aplicação e está a certificar-se a ser exclusivo para a aplicação pela Apple App Store. Quando uma aplicação chama o mediador, o mediador pede-lhe o sistema operativo iOS fornecê-lo com o ID do pacote que o Mediador de chamada. O mediador fornece este ID de pacote para a Microsoft na chamada para o nosso sistema de identidade. Se o ID do pacote da aplicação não tem correspondência com o ID do pacote fornecido para nós pelo programador durante o registo, iremos irá negar o acesso para os tokens para o recurso a aplicação está a solicitar. Esta verificação garante que apenas a aplicação registada pelo programador recebe tokens.

**O programador tem a opção de se o SDK do Microsoft Identity chama o mediador ou utiliza o fluxo de assistido não-mediador.** No entanto se o programador optar por não utilizar o fluxo assistido do Mediador de perder a vantagem de utilizar as credenciais SSO que o utilizador pode já ter adicionado no dispositivo e impede a respetiva aplicação de que está a ser utilizado com funcionalidades empresariais que Microsoft disponibiliza aos clientes como acesso condicional, capacidades de gestão do Intune e a autenticação baseada em certificado.

Estes inícios de sessão tem as seguintes vantagens:

* Utilizador experiências SSO em todas as respetivas aplicações, independentemente do fornecedor.
* A aplicação pode utilizar funcionalidades mais avançadas de negócio, como o acesso condicional ou utilizar o InTune de conjunto de produtos.
* A aplicação pode suportar a autenticação baseada em certificado para os utilizadores empresariais.
* Experiência de início de sessão muito mais segura como a identidade da aplicação e o utilizador são verificados pela aplicação de Mediador com encriptação e algoritmos de segurança adicional.

Estes inícios de sessão tem as seguintes desvantagens:

* No iOS o utilizador é transitado para fora de experiência da sua aplicação enquanto as credenciais são escolhidas.
* Perda de capacidade para gerir a experiência de início de sessão para os seus clientes na sua aplicação.

Eis uma representação de como os SDKs de identidade da Microsoft trabalham com as aplicações de Mediador para permitir SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Armed com estas informações de fundo que deve ser capaz de para melhor compreender e implementar o SSO dentro da sua aplicação com a plataforma do Microsoft Identity e SDKs.

## <a name="enabling-cross-app-sso-using-adal"></a>Ativar a SSO em várias aplicações com ADAL
Aqui, utilizamos o SDK do iOS ADAL para:

* Ative não-mediador assistido SSO para o conjunto de aplicações
* Ativar o suporte para SSO assistida Mediador

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ativar o SSO para não-mediador assistido SSO
Para não-mediador SSO assistido em todas as aplicações os SDKs de identidade do Microsoft gerir muito a complexidade do SSO para si. Isto inclui ao localizar o utilizador à direita na cache e manter uma lista de utilizadores com sessão iniciada para a consulta.

Para ativar a SSO em aplicações que possui que precisa de fazer o seguinte:

1. Certifique-se todos os seus utilizadores de aplicações, o mesmo ID de cliente ou o ID da aplicação.
2. Certifique-se de que todas as aplicações partilham o mesmo certificado de assinatura da Apple para que possam partilhar keychains
3. Solicitar a mesma elegibilidade de keychain para cada uma das suas aplicações.
4. Conte os SDKs de identidade do Microsoft keychain partilhado que pretende que a utilização.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Com o mesmo ID de cliente / aplicação ID para todas as aplicações no seu conjunto de aplicações
Por ordem para a plataforma do Microsoft Identity saber que tem permissão para partilhar tokens entre as suas aplicações, cada uma das suas aplicações terão de partilhar o mesmo ID de cliente ou o ID da aplicação. Este é o identificador exclusivo que foi que lhe é fornecido quando se registou a sua primeira aplicação no portal.

-Pode estar a pensar como irá identificar diferentes aplicações para o serviço do Microsoft Identity se utiliza o mesmo ID de aplicação A resposta é com o **redirecionar URIs**. Cada aplicação pode ter vários URIs redirecionar registado no portal de integração. Cada aplicação no seu conjunto tem um URI de redirecionamento diferentes. Abaixo é um exemplo do aspeto:

URI de redirecionamento de App1:`x-msauth-mytestiosapp://com.myapp.mytestapp`

URI de redirecionamento App2:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI de redirecionamento App3:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estes estão aninhados sob o mesmo ID de cliente / ID da aplicação e pesquisado com base no regressar ao-na sua configuração de SDK do URI de redirecionamento.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Tenha em atenção que o formato destes URI de redirecionamento são explicados abaixo. Pode utilizar qualquer URI de redirecionamento, a menos que pretenda suportar o mediador, caso em que devem ter um aspeto semelhante acima*

#### <a name="create-keychain-sharing-between-applications"></a>Criar partilha entre aplicações de keychain
Ativar a partilha de keychain está fora do âmbito deste documento e abrangidas pela Apple no respetivo documento [adicionar capacidades](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). O que é importante é que decida o que pretende que a keychain ser chamado e adicionar essa capacidade em todas as suas aplicações.

Quando tiver elegibilidade configurada corretamente, deverá ver um ficheiro no diretório do seu projeto denominado `entitlements.plist` que contenha algo que se assemelha ao seguinte:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Assim que tiver a elegibilidade de keychain ativada em cada uma das suas aplicações e está pronto para utilizar o SSO, dizer o SDK de identidade da Microsoft sobre a keychain utilizando a seguinte definição na sua `ADAuthenticationSettings` com a definição seguinte:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando partilha uma keychain entre as aplicações de qualquer aplicação pode eliminar os utilizadores ou worse eliminar todos os tokens na aplicação. Isto é particularmente desastroso, se tiver aplicações que dependem dos tokens de em segundo plano de trabalho. Partilhar uma keychain significa que tem de ser muito cuidado em todos os remover operações através de SDKs de identidade da Microsoft.
> 
> 

Já está! O SDK de identidade do Microsoft irão partilhar agora credenciais em todas as suas aplicações. A lista de utilizador também será partilhada entre instâncias da aplicação.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO para broker assistido SSO
A capacidade para uma aplicação utilizar qualquer broker que está instalado no dispositivo é **desativado por predefinição**. Para utilizar a aplicação com o mediador tem de fazer alguma configuração adicional e adicionar algum código a sua aplicação.

Os passos a seguir são:

1. Ative o modo de Mediador na chamada de código da aplicação para o SDK MS.
2. Estabelecer um novo URI de redirecionamento e fornecer que a aplicação e o registo da aplicação.
3. Registar um esquema de URL.
4. Suporte de iOS9: adicionar uma permissão ao seu ficheiro info. plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de Mediador na sua aplicação
A capacidade para a sua aplicação utilizar o mediador está ativada quando cria o "contexto" ou a configuração inicial do seu objeto de autenticação. Fazê-lo ao definir o tipo de credenciais no código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
O `AD_CREDENTIALS_AUTO` definição permitirá que o SDK de identidade da Microsoft ao tentar a chamada para o mediador `AD_CREDENTIALS_EMBEDDED` irá impedir que o SDK do Microsoft Identity chamar para o mediador.

#### <a name="step-2-registering-a-url-scheme"></a>Passo 2: Registar um esquema de URL
A plataforma do Microsoft Identity utiliza os URLs para invocar o mediador e, em seguida, devolver controlo voltar à sua aplicação. Para concluir essa ida e volta tem um esquema de URL registado para a sua aplicação que irá conhecer a plataforma do Microsoft Identity. Isto pode dever-se para além de outros esquemas de aplicação que pode tiver registado anteriormente com a sua aplicação.

> [!WARNING]
> Recomendamos um esquema de URL bastante exclusivo para minimizar as hipóteses de outra aplicação com o mesmo esquema de URL. Apple não impõe a exclusividade de esquemas de URL que são registados na app store.
> 
> 

Abaixo está um exemplo de como este aparece na configuração do seu projeto. Pode também fazê-no XCode, bem como:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 3: Estabelecer um novo URI com o esquema de URL de redirecionamento
Para garantir que é sempre devolver os tokens de credencial para a aplicação correta, temos de certificar-se de que chamamos a voltar à sua aplicação de uma forma que pode verificar o sistema operativo iOS. O sistema operativo iOS relatórios para as aplicações de Mediador da Microsoft, o ID do pacote da aplicação ao chamá-lo. Isto não pode ser falsificado por uma aplicação rogue. Por conseguinte, iremos tirar partido isto juntamente com o URI da nossa aplicação de Mediador para se certificar de que os tokens são devolvidos para a aplicação correta. É necessário estabelecer este URI ambos de redirecionamento exclusivo na sua aplicação e definido como um URI de redirecionamento de mensagens em fila no nosso portal de programador.

O URI de redirecionamento tem de estar no formato correto:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este URI de redirecionamento tem de ser especificado no seu registo de aplicação utilizando o [portal do Azure](https://portal.azure.com/). Para obter mais informações sobre o registo de aplicações do Azure AD, consulte [integrar com o Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passo 3a: adicionar um URI de redirecionamento no portal da aplicação e dev para suportar a autenticação baseada em certificado
A autenticação de certificados com base em suporte um segundo "msauth" tem de estar registado na sua aplicação e o [portal do Azure](https://portal.azure.com/) para processar a autenticação de certificado se pretender adicionar que suportam na sua aplicação.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Passo 4: iOS9: adicionar um parâmetro de configuração para a sua aplicação
Utiliza a ADAL – canOpenURL: para verificar se o mediador está instalado no dispositivo. No iOS 9 Apple bloqueados quais podem consultar os esquemas de uma aplicação. Terá de adicionar "msauth" para a secção de LSApplicationQueriesSchemes do seu `info.plist file`.

<key>LSApplicationQueriesSchemes</key>

<array><string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Configurou o SSO!
Agora o SDK do Microsoft Identity será automaticamente o partilhar credenciais entre as suas aplicações e invocar o mediador se estiver presente nos respetivos dispositivos.

