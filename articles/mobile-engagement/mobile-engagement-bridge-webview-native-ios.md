---
title: Bridge iOS WebView com o SDK iOS do Mobile Engagement nativo
description: Descreve como criar uma ponte entre WebView que executam Javascript e o SDK do Mobile Engagement iOS nativo
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35f7bdbeb480122513ae2a0b04a6d8cfd426802a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Bridge iOS WebView com o SDK iOS do Mobile Engagement nativo
> [!div class="op_single_selector"]
> * [Bridge de Android](mobile-engagement-bridge-webview-native-android.md)
> * [Bridge de iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Algumas aplicações móveis são concebidas como uma aplicação híbrida onde a aplicação for desenvolvida utilizando a programação iOS nativo Objective-C, mas alguns ou todos os ecrãs mesmo são compostos dentro de um WebView do iOS. Ainda pode consumir iOS do Mobile Engagement SDK essas aplicações e este tutorial descreve como ir sobre como fazê-lo. 

Existem duas abordagens para atingir esse objetivo, embora ambos são não documentadas:

* Primeiro um descrita neste [ligação](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) que envolve a registar um `UIWebViewDelegate` na sua vista web e catch-e-imediatamente-cancelar uma alteração da localização efetuada em Javascript. 
* Segundo um baseia-se neste [WWDC 2013 sessão](https://developer.apple.com/videos/play/wwdc2013/615), uma abordagem que é de limpeza que o primeiro e que iremos segui deste guia. Tenha em atenção que esta abordagem só funciona no iOS7 e superior. 

Siga os passos abaixo para os iOS ponte exemplo:

1. Primeiro de tudo, tem de garantir que tenham passado por nossa [tutorial de introdução](mobile-engagement-ios-get-started.md) para integrar o SDK do iOS dos Mobile Engagement na sua aplicação híbrida. Opcionalmente, também pode ativar teste o registo da seguinte forma para que possa ver os métodos SDK como acionamos do webview. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Agora Certifique-se de que a aplicação híbrida tem um ecrã com um webview. Pode adicioná-la para o `Main.storyboard` da aplicação. 
3. Associar este webview com seu **ViewController** , clicando e arrastando webview da vista controlador cena ao `ViewController.h` editar ecrã, a sua colocação apenas abaixo o `@interface` linha. 
4. Depois de fazê-lo, uma caixa de diálogo aparecer solicitando um nome. Forneça o nome como **webView**. O `ViewController.h` ficheiro deve ter um aspeto semelhante ao seguinte:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Iremos atualizar o `ViewController.m` ficheiro mais tarde mas primeiro vamos criar o ficheiro de bridge que cria um wrapper ao longo de algumas frequentemente utilizadas iOS do Mobile Engagement métodos SDK. Crie um novo ficheiro de cabeçalho chamado **EngagementJsExports.h** que utiliza o `JSExport` mecanismo descrito a acima mencionados [sessão](https://developer.apple.com/videos/play/wwdc2013/615) para expor os métodos de iOS nativo. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Em seguida, iremos criar a segunda parte do ficheiro bridge. Crie um ficheiro chamado **EngagementJsExports.m** que irá conter a implementação de criar os wrappers reais chamando métodos SDK do iOS do Mobile Engagement. Também em atenção que iremos são ao analisar o `extras` que está a ser transmitidas do javascript webview e colocando que para uma `NSMutableDictionary` o objeto a ser transmitido com chamadas de método o Engagement SDK.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Agora vamos voltar para o **ViewController.m** e atualizá-lo com o seguinte código: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Tenha em atenção os seguintes pontos sobre o **ViewController.m** ficheiro:
   
   * No `loadWebView` método, iremos são ao carregar um ficheiro HTML local chamado **LocalPage.html** cujo código, irá rever seguinte. 
   * No `webViewDidFinishLoad` método, iremos são a obtenção de `JsContext` e associar a nossa classe de wrapper com o mesmo. Isto permitirá a nossa wrapper ao chamar os métodos SDK utilizando o identificador **EngagementJs** do webView. 
9. Crie um ficheiro chamado **LocalPage.html** com o seguinte código:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Tenha em atenção os seguintes pontos sobre o ficheiro HTML acima:
    
    * Contém um conjunto de caixas de entrada onde pode fornecer dados a ser utilizado como nomes para o evento, a tarefa, o erro, a AppInfo. Ao clicar no botão junto ao mesmo, é efetuada uma chamada para o Javascript que eventualmente chama os métodos do ficheiro bridge a passar esta chamada para o SDK do iOS do Mobile Engagement. 
    * Iremos são etiquetagem em algumas informações adicionais estáticas para os eventos, tarefas e até mesmo erros para demonstrar como este pode ser efetuada. Estas informações adicionais são enviadas como um JSON de cadeia que, se procurar no `EngagementJsExports.m` de ficheiros, que é analisada e transmitido juntamente com o envio de eventos, tarefas, erros. 
    * Uma tarefa do Mobile Engagement é arrancou com o nome que especificou na caixa de entrada, executar para 10 segundos e encerrar. 
    * Uma etiqueta ou Mobile Engagement appinfo seja passada com customer_name como a chave estática e o valor que introduziu na entrada como o valor da etiqueta. 
11. Executar a aplicação e irá ver o seguinte. Agora, fornecer algum nome para um evento de teste, como o seguinte e clique em **enviar** junto ao mesmo. 
    
     ![][1]
12. Agora se pode aceder a **Monitor** separador da sua aplicação e procure em **eventos-detalhes >**, irá ver este evento aparecer juntamente com o app-info estático está a enviar. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
