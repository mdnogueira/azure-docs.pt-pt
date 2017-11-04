---
title: Bridge WebView Android com nativo Mobile Engagement SDK Android
description: Descreve como criar uma ponte entre WebView que executam Javascript e o nativo Android SDK do Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Bridge WebView Android com nativo Mobile Engagement SDK Android
> [!div class="op_single_selector"]
> * [Bridge de Android](mobile-engagement-bridge-webview-native-android.md)
> * [Bridge de iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Algumas aplicações móveis são concebidas como uma aplicação híbrida onde a aplicação for desenvolvida utilizando a desenvolvimento do Android nativo, mas alguns ou todos os ecrãs mesmo são compostos dentro de um WebView Android. Ainda pode consumir SDK Android do Mobile Engagement essas aplicações e este tutorial descreve como ir sobre como fazê-lo. O código de exemplo abaixo baseia-se na documentação do Android [aqui](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Descreve como esta abordagem documentada poderia ser utilizada para implementar o mesmo para Android SDK do Mobile Engagement normalmente utilizado métodos de forma a que um Webview a partir de uma aplicação híbrida também pode iniciar pedidos para controlar eventos, tarefas, erros, app-info ao encaminhando-los através do nosso Android SDK. 

1. Primeiro de tudo, tem de garantir que tenham passado por nossa [tutorial de introdução](mobile-engagement-android-get-started.md) para integrar o Android SDK do Mobile Engagement na sua aplicação híbrida. Depois, fazê-lo, o `OnCreate` método terá um aspeto semelhante ao seguinte.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Agora Certifique-se de que a aplicação híbrida tem um ecrã com um WebView. O código para a mesma será semelhante ao seguinte onde iremos são carregar um ficheiro HTML local **Sample.html** no Webview no `onCreate` método do ecrã. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Agora crie um ficheiro de bridge chamado **WebAppInterface** que cria um wrapper através de alguns normalmente utilizadas através de métodos de SDK Android do Mobile Engagement a `@JavascriptInterface` abordagem descrita no [documentação Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Assim que tiver criado o ficheiro de bridge acima, precisamos de garantir que está associado a nossa Webview. Para tal acontecer, terá de editar o `SetWebview` método, para que o seguinte aspeto:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. No fragmento acima, podemos chamar `addJavascriptInterface` a associar a nossa classe de bridge nosso Webview e também criou um identificador chamado **EngagementJs** para chamar os métodos do ficheiro bridge. 
6. Agora crie o ficheiro seguinte chamado **Sample.html** no seu projeto numa pasta denominada **ativos** que é carregado para o Webview e onde que iremos chamar os métodos do ficheiro bridge.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Tenha em atenção os seguintes pontos sobre o ficheiro HTML acima:
   
   * Contém um conjunto de caixas de entrada onde pode fornecer dados a ser utilizado como nomes para o evento, a tarefa, o erro, a AppInfo. Ao clicar no botão junto ao mesmo, é efetuada uma chamada para o Javascript que eventualmente chama os métodos do ficheiro bridge a passar esta chamada para o SDK Android do Mobile Engagement. 
   * Iremos são etiquetagem em algumas informações adicionais estáticas para os eventos, tarefas e até mesmo erros para demonstrar como este pode ser efetuada. Estas informações adicionais são enviadas como um JSON de cadeia que, se procurar no `WebAppInterface` de ficheiros, que é analisada e colocar um Android `Bundle` e é transmitido juntamente com o envio de eventos, tarefas, erros. 
   * Uma tarefa do Mobile Engagement é arrancou com o nome que especificou na caixa de entrada, executar para 10 segundos e encerrar. 
   * Uma etiqueta ou Mobile Engagement appinfo seja passada com customer_name como a chave estática e o valor que introduziu na entrada como o valor da etiqueta. 
8. Executar a aplicação e irá ver o seguinte. Agora, fornecer algum nome para um evento de teste, como o seguinte e clique em **enviar** abaixo do mesmo. 
   
    ![][1]
9. Agora se pode aceder a **Monitor** separador da sua aplicação e procure em **eventos-detalhes >**, irá ver este evento aparecer juntamente com o app-info estático está a enviar. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
