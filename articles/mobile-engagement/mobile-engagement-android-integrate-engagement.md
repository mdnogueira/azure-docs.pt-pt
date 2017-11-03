---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Mais recentes atualizações e procedimentos para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35bd92e52b7a02f58620a03156902f9f91be57ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-on-android"></a>Como integrar o Engagement no Android
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Este procedimento descreve a forma mais simples para ativar o Engagement e de análise funções na sua aplicação Android de monitorização.

> [!IMPORTANT]
> O nível de API do SDK Android mínimo tem de ser 10 ou superior (Android 2.3.3 ou superior).
> 
> 

Os seguintes passos são suficientes para ativa o relatório de registos necessários para todas as estatísticas relativas a utilizadores, sessões, atividades, as falhas e Technicals de computação. O relatório de registos necessários para outras estatísticas como eventos, erros e tarefas de computação tem de ser feito manualmente utilizando a API do Engagement (consulte [como utilizar a marcação de API no Android de Mobile Engagement avançadas](mobile-engagement-android-use-engagement-api.md) uma vez que estas estatísticas da aplicação dependente.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Incorporar o Engagement SDK e o serviço no seu projeto Android
Transferir o SDK Android do [aqui](https://aka.ms/vq9mfn) obter `mobile-engagement-VERSION.jar` e colocá-los para o `libs` pasta do projeto Android (criar pasta bibliotecas se não existir ainda).

> [!IMPORTANT]
> Se criar o pacote de aplicação com ProGuard, terá de manter algumas classes. Pode utilizar o seguinte fragmento de configuração:
> 
> -manter classe pública * expande android.os.IInterface-manter classe com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

Especifique a cadeia de ligação do Engagement ao chamar o método seguinte na atividade de iniciador:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de ligação para a sua aplicação é apresentada no Portal do Azure.

* Se estiverem em falta, adicione as seguintes permissões Android (antes do `<application>` tag):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Adicione a secção seguinte (entre o `<application>` e `</application>` etiquetas):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Alteração `<Your application name>` com o nome da sua aplicação.

> [!TIP]
> O `android:label` atributo permite-lhe escolher o nome do serviço Engagement conforme irá aparecer aos utilizadores finais no ecrã "A executar os serviços" do seu telefone. Recomenda-se definir este atributo como `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).
> 
> 

Especificar o `android:process` atributo assegura que o serviço de envolvimento será executado no seu próprio processo (a executar o Engagement no mesmo processo como a aplicação irá tornar o thread principal/IU potencialmente menos dinâmico).

> [!NOTE]
> Qualquer código é colocar na `Application.onCreate()` e outras chamadas de retorno da aplicação serão executadas para todos os seus processos da aplicação, incluindo o Engagement serviço. Pode ter efeitos secundários indesejáveis (como as alocações de memória desnecessários e threads no processo do Engagement, recetores difusão duplicados ou serviços).
> 
> 

Se substituir `Application.onCreate()`, é recomendado que adicione o seguinte fragmento de código no início da sua `Application.onCreate()` função:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Pode fazê-lo a mesma coisa `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Também pode expandir `EngagementApplication` em vez de expandir `Application`: a chamada de retorno `Application.onCreate()` faz a verificação de processo e as chamadas `Application.onApplicationProcessCreate()` apenas se o processo atual não é o que aloja o serviço de envolvimento, as mesmas regras aplicam-se as outras chamadas de retorno.

## <a name="basic-reporting"></a>Relatório básico
### <a name="recommended-method-overload-your-activity-classes"></a>Recomendado método: Sobrecarga sua `Activity` classes
Para ativar o relatório de todos os registos de que o Engagement para os utilizadores, sessões, atividades, as falhas e técnicas estatísticas de computação, apenas tem de efetuar todas as suas `*Activity` classes secundárias herdarem correspondente `Engagement*Activity` classes (por exemplo, se expande a sua atividade legado `ListActivity`, disponibilizar que expande `EngagementListActivity`).

**Sem o Engagement:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Com o Engagement:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> Quando utilizar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se nenhuma chamada `requestWindowFeature(...);` é efetuada antes da chamada para `super.onCreate(...);`, caso contrário, ocorrerá uma falha.
> 
> 

Pode encontrar estas classes no `src` pasta e pode copiá-los no seu projeto. As classes também estejam no **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: chamar `startActivity()` e `endActivity()` manualmente
Se não é possível ou não pretender sobrecarregar o `Activity` classes, em vez disso, pode iniciar e terminar as atividades chamando `EngagementAgent`do métodos diretamente.

> [!IMPORTANT]
> O SDK Android nunca chama o `endActivity()` método, mesmo quando a aplicação está fechada (no Android, na verdade, nunca estão fechadas aplicações). Assim, é *altamente* recomendado para chamar o `startActivity()` método no `onResume` chamada de retorno de *todos os* as atividades e o `endActivity()` método o `onPause()` chamada de retorno de *todos os* as atividades. Esta é a única forma de se certificar de que não irão ser transmitidas sessões. Se Ocorreu uma fuga uma sessão, o serviço de envolvimento nunca irá desligar do back-end Engagement (uma vez que o serviço permanece ligado, desde que uma sessão está pendente).
> 
> 

Segue-se um exemplo:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

Neste exemplo muito semelhante de `EngagementActivity` classe e o respetivos variantes, cujo código de origem é fornecido no `src` pasta.

## <a name="test"></a>Teste
Agora, certifique-se a integração ao executar a sua aplicação móvel num dispositivo ou emulador e verificar que regista uma sessão no separador Monitor.

As secções seguintes são opcionais.

## <a name="location-reporting"></a>Relatórios de localização
Se pretender que as localizações sejam comunicados, tem de adicionar algumas linhas de configuração (entre o `<application>` e `</application>` etiquetas).

### <a name="lazy-area-location-reporting"></a>Relatórios de localização de área em diferido
Relatórios de localização de área em diferido permite comunicar o país, região e Localidade associados aos dispositivos. Este tipo de relatórios de localização apenas utiliza localizações de rede (com base no ID de célula ou Wi-Fi). A área de dispositivos é reportada no máximo uma vez por sessão. O GPS nunca é utilizada e, consequentemente, este tipo de relatório de localização tem muito poucos (não deve indicar não) impacto da bateria.

Áreas comunicadas são utilizadas para calcular geográficas estatísticas sobre utilizadores, sessões, eventos e erros. Também pode ser utilizados como critério de campanhas de alcance.

Para ativar os relatórios de localização de área em diferido, pode fazê-lo utilizando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de adicionar a permissão seguinte se estiverem em falta:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar a utilizar ``ACCESS_FINE_LOCATION`` se já utilizá-lo na sua aplicação.

### <a name="real-time-location-reporting"></a>Os relatórios de localização em tempo real
Permite que os relatórios de localização em tempo real para reportar a latitude e longitude associados aos dispositivos. Por predefinição, este tipo de relatórios de localização apenas utiliza localizações de rede (com base no ID de célula ou Wi-Fi) e o Reporting Services só está ativa quando a aplicação é executada em primeiro plano (ou seja, durante uma sessão).

Em tempo real de localizações são *não* utilizado para calcular estatísticas. Os seus única finalidade é permitir a utilização de geométrico em tempo real \<alcance-público-alvo-barreira geográfica\> critério no campanhas de alcance.

Para ativar a localização em tempo real, relatórios, pode fazê-lo utilizando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de adicionar a permissão seguinte se estiverem em falta:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar a utilizar ``ACCESS_FINE_LOCATION`` se já utilizá-lo na sua aplicação.

#### <a name="gps-based-reporting"></a>GPS com base em relatórios
Por predefinição, apenas os relatórios de localização em tempo real utilizam localizações de rede com base. Para ativar a utilização das localizações de GPS com base em (que são muito mais precisas), utilize o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de adicionar a permissão seguinte se estiverem em falta:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatórios de em segundo plano
Por predefinição, os relatórios de localização em tempo real só estão ativo quando a aplicação é executada em primeiro plano (ou seja, durante uma sessão). Para ativar os relatórios também em segundo plano, utilize o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Quando a aplicação é executada em segundo plano, só de rede com base em localizações são comunicadas, mesmo se tiver ativado o GPS.
> 
> 

O relatório de localização de fundo será parado, se o utilizador reinicia o respetivo dispositivo, pode adicionar esta opção para torná-lo reinicie automaticamente no momento do arranque:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Também terá de adicionar a permissão seguinte se estiverem em falta:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M permissões
A partir do Android M, alguns permissões são geridas no tempo de execução e necessita de aprovação do utilizador.

As permissões de tempo de execução serão desligadas por predefinição para novas instalações da aplicação se destinar a nível de API do Android 23. Caso contrário, ficará ativado por predefinição.

O utilizador pode ativar/desativar essas permissões no menu de definições do dispositivo. Desativar permissões a partir do menu de sistema inutilizam os processos em segundo plano da aplicação, este é um comportamento do sistema e não tem impacto na capacidade de receber push em segundo plano.

No contexto do Mobile Engagement, as permissões que necessitem de aprovação no tempo de execução são:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE`(apenas quando a filtrar o nível de API do Android 23 para que este)

O armazenamento externo é utilizado apenas para a funcionalidade de visão geral de alcance. Se encontrar pedir aos utilizadores esta permissão para ser incómodas, pode removê-la se tiver utilizado apenas para Mobile Engagement mas, ao custo de desativar a funcionalidade de visão.

Para as funcionalidades de localização, o utilizador deve pedir permissões ao utilizador a utilizar uma caixa de diálogo de sistema padrão. Se o utilizador aprova, tem de facultar ao ``EngagementAgent`` efetuar essa alteração em consideração em tempo real (caso contrário, a alteração será processada da próxima vez que o utilizador inicia a aplicação).

Eis um exemplo de código para utilizar numa atividade da sua aplicação para solicitar permissões e reencaminhar o resultado se positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, se pretender comunicar eventos específicos de aplicação, erros e tarefas, terá de utilizar a API do Engagement através de métodos do `EngagementAgent` classe. Um objeto desta classe pode ser retreived ao chamar o `EngagementAgent.getInstance()` método estático.

O Engagement API permite utilizar todas as capacidades avançadas do Engagement e está detalhada na forma como utilizar a API do Engagement no Android (bem como na documentação técnica do `EngagementAgent` classe).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Configuração avançada (em AndroidManifest.xml)
### <a name="wake-locks"></a>Bloqueios de reativação
Se pretender Certifique-se de que as estatísticas são enviadas em tempo real, quando utilizar Wi-Fi ou quando o ecrã está desativada, adicione a permissão opcional seguinte:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Relatório de falhas
Se pretender desativar relatórios de falhas, adicione (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Impulsar limiar
Por predefinição, os relatórios do serviço o Engagement os registos em tempo real. Se a aplicação comunica muito frequentemente os registos, é melhor para a memória intermédia os registos e para comunicá-los ao mesmo tempo em vez regular base (denominado "modo de rajada"). Para tal, adicione (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo de rajada ligeiramente aumentar a vida bateria, mas tem um impacto no Monitor Engagement: duração de sessões e as tarefas de todos os será arredondado o limiar de rajada (assim, as sessões e as tarefas mais curtos do que o limiar de rajada poderá não estar visível). Recomenda-se para utilizar um limiar de rajada que 30000 (30s).

### <a name="session-timeout"></a>Tempo limite da sessão
Por predefinição, uma sessão é terminada 10s após o fim do respetivo última atividade (que normalmente ocorre premindo a tecla de casa ou anterior, definindo o telefone inativo ou por jumping para outra aplicação). Isto serve para evitar uma divisão de sessão sempre que a saída de utilizador e regressar à aplicação muito rapidamente (que pode acontecer quando ele processará uma imagem de verificação de notificação, etc.). Pretende modificar este parâmetro. Para tal, adicione (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Desativar o relatório de registo
### <a name="using-a-method-call"></a>Utilizar uma chamada de método
Se pretender que o Engagement para parar de enviar os registos, pode chamar:

            EngagementAgent.getInstance(context).setEnabled(false);

Esta chamada é persistente: utiliza um ficheiro de preferências partilhadas.

Se o Engagement está ativa quando chamar esta função, poderá demorar 1 minuto parar o serviço. No entanto, não inicie o serviço em todos os da próxima vez que iniciar a aplicação.

Pode ativar novamente reporting chamando a mesma função com registo `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração na sua própria`PreferenceActivity`
Em vez de chamar esta função, também pode integrar esta definição diretamente no existentes `PreferenceActivity`.

Pode configurar o Engagement para utilizar o ficheiro de preferências (com o modo pretendido) no `AndroidManifest.xml` ficheiros com `application meta-data`:

* O `engagement:agent:settings:name` chave é utilizada para definir o nome do ficheiro de preferências partilhadas.
* O `engagement:agent:settings:mode` chave é utilizada para definir o modo do ficheiro de preferências partilhadas, deve utilizar o mesmo modo que no seu `PreferenceActivity`. O modo tem de ser transmitido como um número: Se estiver a utilizar uma combinação de sinalizadores constantes no seu código, verifique o valor total.

Utilizar o engagement sempre o `engagement:key` booleano chave do ficheiro de preferências para gerir esta definição.

O seguinte exemplo de `AndroidManifest.xml` mostra os valores predefinidos:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Em seguida, pode adicionar um `CheckBoxPreference` no seu esquema de preferência como o seguinte:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
