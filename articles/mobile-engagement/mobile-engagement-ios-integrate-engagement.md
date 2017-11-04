---
title: "IOS do Mobile Engagement integração do SDK do Azure | Microsoft Docs"
description: "Mais recentes atualizações e procedimentos para o SDK do iOS do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 01fdbb43c21ac6932e8462f4a6507fc63e50542d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-on-ios"></a>Como integrar o Engagement no iOS
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

Este procedimento descreve a forma mais simples para ativar o Engagement análise e monitorização de funções na sua aplicação iOS.

O SDK do Engagement requer iOS7 + e Xcode 8 +: o destino da implementação da aplicação tem de ser, pelo menos, o iOS 7.

> [!NOTE]
> Se é realmente depende do XCode 7, em seguida, pode utilizar o [iOS o Engagement SDK v3.2.4](https://aka.ms/r6oouh). Não há um erro conhecido no módulo de alcance nesta versão anterior ao executar o iOS 10 dispositivos, consulte [a integração do módulo de alcance](mobile-engagement-ios-integrate-engagement-reach.md) para obter mais detalhes. Se optar por utilizar o SDK v3.2.4, em seguida, basta ignorar o `UserNotifications.framework` importar no próximo passo.
>
>

Os seguintes passos são suficientes para ativar o relatório de registos necessários para todas as estatísticas relativas a utilizadores, sessões, atividades, as falhas e Technicals de computação. O relatório de registos necessários para outras estatísticas como eventos, erros e tarefas de computação tem de ser feito manualmente utilizando a API do Engagement (consulte [como utilizar o Mobile Engagement avançadas etiquetagem API na sua aplicação iOS](mobile-engagement-ios-use-engagement-api.md) uma vez que estas estatísticas da aplicação dependente.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporar o Engagement SDK para o projeto iOS
* Transferir o SDK iOS do [aqui](http://aka.ms/qk2rnj).
* Adicione o Engagement SDK ao projeto iOS: no Xcode, clique no seu projeto e selecione **"Adicionar ficheiros a..."** e escolha o `EngagementSDK` pasta.
* Requer o engagement estruturas adicionais para trabalhar: no Explorador de projeto, abra o painel do projeto e selecione o destino correto. Em seguida, abra o **"Fases de compilação"** separador e no **"Binário com bibliotecas de ligação"** menu, adicionar estas estruturas:

  * `UserNotifications.framework`-definir a ligação como`Optional`
  * `AdSupport.framework`-definir a ligação como`Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> A estrutura de AdSupport pode ser removida. Engagement tem esta estrutura para recolher o IDFA. No entanto, a recolha de IDFA pode ser desativada \<ios-sdk-engagement-idfa\> para estar em conformidade com a nova política de Apple relativamente a este ID.
>
>

## <a name="initialize-the-engagement-sdk"></a>Inicializar o Engagement SDK
Terá de modificar o seu delegado de aplicação:

* Na parte superior do seu ficheiro de implementação, importe o agente do Engagement:

      [...]
      #import "EngagementAgent.h"
* Inicializar o Engagement dentro do método '**applicationDidFinishLaunching:**'ou'**aplicação: didFinishLaunchingWithOptions:**':

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Relatório básico
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Recomendado método: Sobrecarga sua `UIViewController` classes
Para ativar o relatório de todos os registos de que o Engagement para os utilizadores, sessões, atividades, as falhas e técnicas estatísticas de computação, basta pode efetuar todas as sua `UIViewController` classes secundárias herdam a `EngagementViewController` classes (mesmo regra para `UITableViewController`  - \> `EngagementTableViewController`).

**Sem o Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Com o Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `startActivity()` manualmente
Se não é possível ou não pretender sobrecarregar o `UIViewController` classes, em vez disso, pode iniciar as atividades chamando `EngagementAgent`do métodos diretamente.

> [!IMPORTANT]
> IOS SDK automaticamente chama o `endActivity()` método quando a aplicação está fechada. Assim, é *altamente* recomendado para chamar o `startActivity` método sempre que alterar a atividade do utilizador e a *nunca* chamar o `endActivity` método, uma vez que chamar este método força a sessão atual para terminado.
>
>

## <a name="location-reporting"></a>Relatórios de localização
Termos de Apple de serviço não permitir que as aplicações utilizar a localização de controlo para apenas a fins de estatísticas. Assim, recomenda-se que ative relatórios de localização apenas se a aplicação utilizar também a localização de registo por outro motivo.

A partir do iOS 8, tem de fornecer uma descrição de como a aplicação utiliza os serviços de localização, definindo uma cadeia para a chave [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] no ficheiro info. plist da sua aplicação. Se pretender que a localização do relatório em segundo plano com o Engagement, adicione a chave NSLocationAlwaysUsageDescription. Noutros casos, adicione a chave NSLocationWhenInUseUsageDescription. Tenha em atenção de que precisa de NSLocationAlwaysAndWhenInUseUsageDescription e NSLocationWhenInUseUsageDescription localização de fundo do relatório no iOS 11.

### <a name="lazy-area-location-reporting"></a>Relatórios de localização de área em diferido
Relatórios de localização de área em diferido permite comunicar o país, região e Localidade associados aos dispositivos. Este tipo de relatórios de localização apenas utiliza localizações de rede (com base no ID de célula ou Wi-Fi). A área de dispositivos é reportada no máximo uma vez por sessão. O GPS nunca é utilizada e, consequentemente, este tipo de relatório de localização tem muito poucos (não deve indicar não) impacto da bateria.

Áreas comunicadas são utilizadas para calcular geográficas estatísticas sobre utilizadores, sessões, eventos e erros. Também pode ser utilizados como critério de campanhas de alcance. A última área conhecida comunicados para um dispositivo pode ser obtido thanks para o [Device API].

Para ativar os relatórios de localização de área em diferido, adicione a seguinte linha depois de a inicializar o agente do Engagement:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Os relatórios de localização em tempo real
Permite que os relatórios de localização em tempo real para reportar a latitude e longitude associados aos dispositivos. Por predefinição, este tipo de relatórios de localização apenas utiliza localizações de rede (com base no ID de célula ou Wi-Fi) e o Reporting Services só está ativa quando a aplicação é executada em primeiro plano (ou seja, durante uma sessão).

Em tempo real de localizações são *não* utilizado para calcular estatísticas. Os seus única finalidade é permitir a utilização de geométrico em tempo real \<alcance-público-alvo-barreira geográfica\> critério no campanhas de alcance.

Para ativar os relatórios de localização em tempo real, adicione a seguinte linha depois de a inicializar o agente do Engagement:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS com base em relatórios
Por predefinição, apenas os relatórios de localização em tempo real utilizam localizações de rede com base. Para ativar a utilização das localizações de GPS com base em (que são muito mais precisas), adicione:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Relatórios de em segundo plano
Por predefinição, os relatórios de localização em tempo real só estão ativo quando a aplicação é executada em primeiro plano (ou seja, durante uma sessão). Para ativar os relatórios também em segundo plano, adicione:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Quando a aplicação é executada em segundo plano, só de rede com base em localizações são comunicadas, mesmo se tiver ativado o GPS.
>
>

Implementação desta função irá chamar [startMonitoringSignificantLocationChanges] quando a aplicação entra em segundo plano. Lembre-se de automaticamente irá reiniciar a aplicação em segundo plano se chegar um novo evento de localização.

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, se pretender comunicar eventos específicos de aplicação, erros e tarefas, terá de utilizar a API do Engagement através de métodos do `EngagementAgent` classe. Um objeto desta classe pode ser obtido ao chamar o `[EngagementAgent shared]` método estático.

O Engagement API permite utilizar todas as capacidades avançadas do Engagement e está detalhada na forma como utilizar a API do Engagement no iOS (bem como na documentação técnica do `EngagementAgent` classe).

## <a name="disable-idfa-collection"></a>Desativar a recolha de IDFA
Por predefinição, o Engagement utilizará o [IDFA] para identificar exclusivamente um utilizador. Mas se não estiver a utilizar a publicidade noutro local na aplicação, poderá ser rejeitados pelo processo de revisão da App Store. Recolha de IDFA pode ser desativada através da adição de pré-processador macro `ENGAGEMENT_DISABLE_IDFA` no seu ficheiro pch (ou no `Build Settings` da sua aplicação). Isto irá garantir que não há nenhum referências a `ASIdentifierManager`, `advertisingIdentifier` ou `isAdvertisingTrackingEnabled` na compilação da aplicação.

Integração no **prefix.pch** ficheiro:

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Pode verificar que a recolha de IDFA corretamente está desativada na sua aplicação ao verificar os registos de teste do Engagement. Consulte o teste de integração\<ios-sdk-engagement-teste-idfa\> documentação para obter mais informações.

## <a name="disable-log-reporting"></a>Desativar o relatório de registo
### <a name="using-a-method-call"></a>Utilizar uma chamada de método
Se pretender que o Engagement para parar de enviar os registos, pode chamar:

    [[EngagementAgent shared] setEnabled:NO];

Esta chamada é persistente: utiliza `NSUserDefaults` para armazenar as informações.

Pode ativar novamente reporting chamando a mesma função com registo `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integração do seu grupo de definições
Em vez de chamar esta função, também pode integrar esta definição diretamente no existentes `Settings.bundle` ficheiro. A cadeia `engagement_agent_enabled` tem de ser utilizado como um identificador de preferência e tem de ser associados a um comutador de alternar (`PSToggleSwitchSpecifier`).

O seguinte exemplo de `Settings.bundle` mostra como implementá-las:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
