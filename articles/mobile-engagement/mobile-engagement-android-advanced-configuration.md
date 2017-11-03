---
title: "Configuração avançada para Android SDK do Azure Mobile Engagement"
description: "Descreve as opções de configuração avançadas, incluindo o manifesto do Android com o Azure Mobile Engagement Android SDK"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuração avançada para Android SDK do Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Este procedimento descreve como configurar várias opções de configuração para aplicações do Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisitos de permissão
Algumas opções requer permissões específicas, todos os que estão listados aqui para referência e, na linha na funcionalidade específica. Adicione estas permissões ao AndroidManifest.xml do projeto imediatamente antes ou depois do `<application>` etiquetas.

O código de permissão tem de ter um aspeto semelhante ao seguinte, onde preencha das permissões adequadas da tabela que se segue.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Permissão | Quando utilizado |
| --- | --- |
| INTERNET |Necessário. Para o relatório básico |
| ACCESS_NETWORK_STATE |Necessário. Para o relatório básico |
| RECEIVE_BOOT_COMPLETED |Necessário. Para apresentar o Centro de notificações após o reinício do dispositivo |
| WAKE_LOCK |Recomendado. Permite a recolha de dados quando utilizar Wi-Fi ou quando o ecrã está desativada |
| VIBRE |Opcional. Permite vibration quando as notificações são recebidas |
| DOWNLOAD_WITHOUT_NOTIFICATION |Opcional. Permite que a notificação de visão Android |
| WRITE_EXTERNAL_STORAGE |Opcional. Permite que a notificação de visão Android |
| ACCESS_COARSE_LOCATION |Opcional. Permite que os relatórios de localização em tempo real |
| ACCESS_FINE_LOCATION |Opcional. Permite que os relatórios de localização com base em GPS |

Começando com Android M, [algumas permissões são geridas em tempo de execução](mobile-engagement-android-location-reporting.md#android-m-permissions).

Se já estiver a utilizar ``ACCESS_FINE_LOCATION``, em seguida, não precisa de utilizar também ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opções de configuração do manifesto Android
### <a name="crash-report"></a>Relatório de falhas
Para desativar os relatórios de falhas, adicione este código entre o `<application>` e `</application>` etiquetas:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Impulsar limiar
Por predefinição, os relatórios do serviço o Engagement os registos em tempo real. Se os registos de relatório da aplicação podem variar com frequência, é melhor para a memória intermédia os registos e para comunicá-los, uma vez num base de tempo regulares (denominados "impulsar modo"). Para tal, adicione este código entre o `<application>` e `</application>` etiquetas:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Modo de rajada ligeiramente aumenta a vida bateria, mas tem um impacto no Monitor Engagement: todos os duração de sessões e as tarefas são arredondados o limiar de rajada (assim, as sessões e as tarefas mais curtos do que o limiar de rajada poderá não estar visível). O limiar de rajada deve ser não ultrapassa os 30000 (30s).

### <a name="session-timeout"></a>Tempo limite da sessão
 Pode terminar uma atividade, premindo o **home page** ou **novamente** chave, definindo o telefone inativo ou por jumping para outra aplicação. Por predefinição, uma sessão é terminada dez segundos após o fim do respetivo última atividade. Isto evita uma divisão de sessão sempre que o utilizador sai e devolve rapidamente, para a aplicação que pode acontecer quando o utilizador seleciona uma imagem, verifica uma notificação, etc. Pretende modificar este parâmetro. Para tal, adicione este código entre o `<application>` e `</application>` etiquetas:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Desativar o relatório de registo
### <a name="using-a-method-call"></a>Utilizar uma chamada de método
Se pretender que o Engagement para parar de enviar os registos, pode chamar:

    EngagementAgent.getInstance(context).setEnabled(false);

Esta chamada é persistente: utiliza um ficheiro de preferências partilhadas.

Se o Engagement está ativa quando chamar esta função, poderá demorar um minuto parar o serviço. No entanto, não inicie o serviço em todos os da próxima vez que iniciar a aplicação.

Pode ativar novamente reporting chamando a mesma função com registo `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração na sua própria`PreferenceActivity`
Em vez de chamar esta função, também pode integrar esta definição diretamente no existentes `PreferenceActivity`.

Pode configurar o Engagement para utilizar o ficheiro de preferências (com o modo pretendido) no `AndroidManifest.xml` ficheiros com `application meta-data`:

* O `engagement:agent:settings:name` chave é utilizada para definir o nome do ficheiro de preferências partilhadas.
* O `engagement:agent:settings:mode` chave é utilizada para definir o modo do ficheiro de preferências partilhadas. Utilizar o mesmo modo que no seu `PreferenceActivity`. O modo tem de ser transmitido como um número: Se estiver a utilizar uma combinação de sinalizadores constantes no seu código, verifique o valor total.

Utiliza o engagement sempre o `engagement:key` booleano chave do ficheiro de preferências para gerir esta definição.

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
