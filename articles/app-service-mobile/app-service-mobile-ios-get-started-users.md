---
title: "Adicionar autenticação em dispositivos iOS com Mobile Apps do Azure"
description: "Saiba como utilizar Mobile Apps do Azure para autenticar os utilizadores da sua aplicação iOS através de vários fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: glenga
ms.openlocfilehash: 21a2cc6c1eaf4b34cbe8c2d7c4dbb69c8730cf32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-ios-app"></a>Adicionar autenticação à sua aplicação iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Neste tutorial, adicionar autenticação para o [iOS rápidos iniciar] utilizando um fornecedor de identidade suportados do projeto. Este tutorial baseia-se no [iOS rápidos iniciar] tutorial, que tem de concluir primeiro.

## <a name="register"></a>Registar a aplicação para autenticação e configurar o serviço de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitido

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação.  Isto permite que o sistema de autenticação redirecionar para a sua aplicação, depois do processo de autenticação está concluído.  Neste tutorial, utilizamos o esquema de URL _appname_ ao longo.  No entanto, pode utilizar qualquer esquema de URL que escolher.  Deve ser exclusivo para a sua aplicação móvel.  Para ativar o redirecionamento do lado do servidor de ésimo:

1. No [portal do Azure], selecione o serviço de aplicações.

2. Clique em de **autenticação / autorização** opção do menu.

3. Clique em **do Azure Active Directory** sob o **fornecedores de autenticação** secção.

4. Definir o **modo de gestão** para **avançadas**.

5. No **permitidos URLs de redirecionamento externos**, introduza `appname://easyauth.callback`.  O _appname_ esta cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir a especificação de URL normal para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia que escolher, terá de ajustar o código de aplicações móveis com o esquema de URL em vários locais.

6. Clique em **OK**.

7. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, prima **executar** para iniciar a aplicação. É gerada uma exceção porque a aplicação tenta aceder ao back-end como um utilizador não autorizado, mas o *TodoItem* tabela agora requer autenticação.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
**Objective-C**:

1. No Mac, abra *QSTodoListViewController.m* no Xcode e adicione o método seguinte:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Alteração *google* para *microsoftaccount*, *twitter*, *facebook*, ou *windowsazureactivedirectory* se não estiver a utilizar o Google como o fornecedor de identidade. Se utilizar o Facebook, tem de [lista branca Facebook domínios] [ 1] na sua aplicação.

    Substitua o **urlScheme** com um nome exclusivo para a sua aplicação.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL que especificou no **permitidos URLs de redirecionamento externos** campo no portal do Azure. O urlScheme é utilizado pela chamada de retorno de autenticação para mudar para a aplicação após a conclusão do pedido de autenticação.

2. Substitua `[self refresh]` no `viewDidLoad` no *QSTodoListViewController.m* com o seguinte código:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra o `QSAppDelegate.h` de ficheiros e adicione o seguinte código:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra o `QSAppDelegate.m` de ficheiros e adicione o seguinte código:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Adicione este código imediatamente antes de ler a linha `#pragma mark - Core Data stack`.  Substitua o _appname_ wih o valor de urlScheme que utilizou no passo 1.

5. Abra o `AppName-Info.plist` ficheiro (substituindo AppName com o nome da sua aplicação) e adicione o seguinte código:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Este código deve ser colocado dentro de `<dict>` elemento.  Substitua o _appname_ cadeia (de matriz para **CFBundleURLSchemes**) com o nome da aplicação que selecionou no passo 1.  Pode também efetuar estas alterações no plist editor - clique em de `AppName-Info.plist` ficheiro no XCode para abrir o editor de plist.

    Substitua o `com.microsoft.azure.zumo` cadeia para **CFBundleURLName** com o Apple identificador do pacote.

6. Prima *executar* para iniciar a aplicação e, em seguida, inicie sessão. Quando tiver iniciado a sessão, deverá conseguir ver a lista de tarefas e fazer atualizações.

**SWIFT**:

1. No Mac, abra *ToDoTableViewController.swift* no Xcode e adicione o método seguinte:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Alteração *google* para *microsoftaccount*, *twitter*, *facebook*, ou *windowsazureactivedirectory* se não estiver a utilizar o Google como o fornecedor de identidade. Se utilizar o Facebook, tem de [lista branca Facebook domínios] [ 1] na sua aplicação.

    Substitua o **urlScheme** com um nome exclusivo para a sua aplicação.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL que especificou no **permitidos URLs de redirecionamento externos** campo no portal do Azure. O urlScheme é utilizado pela chamada de retorno de autenticação para mudar para a aplicação após a conclusão do pedido de autenticação.

2. Remover as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` no fim do `viewDidLoad()` no *ToDoTableViewController.swift*. Adicione uma chamada para `loginAndGetData()` no respetivo local:

    ```swift
    loginAndGetData()
    ```

3. Abra o `AppDelegate.swift` de ficheiros e adicione a seguinte linha para o `AppDelegate` classe:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Substitua o _appname_ wih o valor de urlScheme que utilizou no passo 1.

4. Abra o `AppName-Info.plist` ficheiro (substituindo AppName com o nome da sua aplicação) e adicione o seguinte código:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Este código deve ser colocado dentro de `<dict>` elemento.  Substitua o _appname_ cadeia (de matriz para **CFBundleURLSchemes**) com o nome da aplicação que selecionou no passo 1.  Pode também efetuar estas alterações no plist editor - clique em de `AppName-Info.plist` ficheiro no XCode para abrir o editor de plist.

    Substitua o `com.microsoft.azure.zumo` cadeia para **CFBundleURLName** com o Apple identificador do pacote.

5. Prima *executar* para iniciar a aplicação e, em seguida, inicie sessão. Quando tiver iniciado a sessão, deverá conseguir ver a lista de tarefas e fazer atualizações.

Autenticação do serviço de aplicações utiliza maçãs Inter aplicação comunicação.  Para obter mais detalhes sobre este assunto, consulte o [documentação da Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[portal do Azure]: https://portal.azure.com

[iOS rápidos iniciar]: app-service-mobile-ios-get-started.md

