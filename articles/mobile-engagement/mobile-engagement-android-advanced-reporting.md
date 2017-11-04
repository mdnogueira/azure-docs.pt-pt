---
title: "Opções de relatórios para o Azure Mobile Engagement Android SDK avançadas"
description: "Descreve como fazê-lo de relatórios avançadas para capturar a análise para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Avançadas de relatórios com o Engagement no Android
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Este tópico descreve os cenários de relatórios adicionais na sua aplicação Android. Pode aplicar estas opções para a aplicação que criou no [introdução](mobile-engagement-android-get-started.md) tutorial.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

O tutorial concluiu foi deliberadamente direta e simples, mas não existe são as opções avançadas de pode escolher.

## <a name="modifying-your-activity-classes"></a>Modificar o `Activity` classes
No [tutorial de introdução](mobile-engagement-android-get-started.md), todos tinha fazer foi para tornar o `*Activity` subclasses herdarem correspondente `Engagement*Activity` classes. Por exemplo, se a sua atividade legado expandido `ListActivity`, iria tornar expandir `EngagementListActivity`.

> [!IMPORTANT]
> Quando utilizar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se nenhuma chamada `requestWindowFeature(...);` é efetuada antes da chamada para `super.onCreate(...);`, caso contrário ocorre uma falha.
> 
> 

Pode encontrar estas classes no `src` pasta e pode copiá-los no seu projeto. As classes também estejam no **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: chamar `startActivity()` e `endActivity()` manualmente
Se não é possível ou não pretender sobrecarregar o `Activity` classes, em vez disso, pode iniciar e terminar as atividades ao chamar o `EngagementAgent`do métodos diretamente.

> [!IMPORTANT]
> O SDK Android nunca chama o `endActivity()` método, mesmo quando a aplicação está fechada (no Android, nunca estão fechadas aplicações). Assim, é *altamente* recomendado para chamar o `startActivity()` método no `onResume` chamada de retorno de *todos os* as atividades e o `endActivity()` método o `onPause()` chamada de retorno de *todos os* as atividades. Esta é a única forma de se certificar de que existem sessões não podem fugir. Se Ocorreu uma fuga uma sessão, o serviço o Engagement nunca de ser desliga do back-end Engagement (uma vez que o serviço permanece ligado, desde que uma sessão está pendente).
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

Neste exemplo é semelhante para o `EngagementActivity` classe e o respetivos variantes, cujo código de origem é fornecido no `src` pasta.

## <a name="using-applicationoncreate"></a>Utilizar Application.onCreate()
Qualquer código é colocar na `Application.onCreate()` e na outra aplicação de chamadas de retorno é executada para todos os seus processos da aplicação, incluindo o Engagement serviço. Pode ter efeitos secundários indesejáveis, tal como as alocações de memória desnecessários e threads no processo do Engagement, ou recetores difusão duplicados ou serviços.

Se substituir `Application.onCreate()`, recomendamos adicionar o seguinte fragmento de código no início da sua `Application.onCreate()` função:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Pode fazê-lo a mesma coisa `Application.onTerminate()`, `Application.onLowMemory()`, e `Application.onConfigurationChanged(...)`.

Também pode expandir `EngagementApplication` em vez de expandir `Application`: a chamada de retorno `Application.onCreate()` faz a verificação de processo e as chamadas `Application.onApplicationProcessCreate()` apenas se o processo atual não é o que aloja o serviço de envolvimento, as mesmas regras aplicam-se as outras chamadas de retorno.

## <a name="tags-in-the-androidmanifestxml-file"></a>Etiquetas no ficheiro AndroidManifest.xml
Na etiqueta de serviço no ficheiro AndroidManifest.xml, o `android:label` atributo permite-lhe escolher o nome do serviço Engagement conforme é apresentado aos utilizadores finais no ecrã "A executar os serviços" do seu telefone. Recomendamos que definir este atributo como `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especificar o `android:process` atributo assegura que o serviço de envolvimento é executado no seu próprio processo (a executar o Engagement no mesmo processo como a aplicação faz com que o thread principal/IU reativo potencialmente inferior).

## <a name="building-with-proguard"></a>Compilar com ProGuard
Se criar o pacote de aplicação com ProGuard, terá de manter algumas classes. Pode utilizar o seguinte fragmento de configuração:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
