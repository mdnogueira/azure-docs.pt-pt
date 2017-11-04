---
title: Medidas de utilizador reais para Traffic Manager do Azure com o Visual Studio Mobile Center | Microsoft Docs
description: "Configurar a sua aplicação móvel desenvolvida utilizando o Visual Studio Mobile Center enviar medidas de utilizador reais para o Gestor de tráfego"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 756496e5291d932ee9ac89265291e6892c4304fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Como enviar medidas de utilizador reais para o Gestor de tráfego com o Visual Studio Mobile Center

>[!NOTE]
>A funcionalidade de medidas de utilizador Real no Traffic Manager está em pré-visualização pública e não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. A funcionalidade não é suportada, pode ter restrita capacidades e poderão não estar disponível em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado desta funcionalidade, consulte o [Traffic Manager do Azure atualiza](https://azure.microsoft.com/updates/?product=traffic-manager) página.

Pode configurar a sua aplicação móvel desenvolvida utilizando o Visual Studio Mobile Center para enviar para o Gestor de tráfego de medidas de utilizador reais, seguindo os passos:

>[!NOTE]
> Atualmente, o envio de medidas de utilizador reais para o Gestor de tráfego só é suportada para Android.

Para configurar os valores reais do utilizador, terá de obter uma chave e instrumente a sua aplicação com o pacote RUM.

## <a name="step-1-obtain-a-key"></a>Passo 1: Obter uma chave
    
Os valores demorar e enviados para o Gestor de tráfego a partir da sua aplicação de cliente são identificados pelo serviço através de uma cadeia exclusiva, denominada a chave de medidas de utilizador reais (RUM). Pode obter uma chave RUM no portal do Azure, uma API REST ou utilizando o PowerShell / CLI das interfaces.

Para obter a chave de RUM através do portal do Azure utilizando o seguinte procedimento:
   1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se numa avaliação gratuita de um mês.
   2. Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego que pretende modificar e, em seguida, clique no perfil de Gestor de tráfego nos resultados de que o apresentados.
   3. Na página de perfil do Traffic Manager, clique em **medidas de utilizador reais** em **definições**.
   4. Clique em **gerar chave** para criar uma nova chave de RUM.
        
   ![Gerar a chave de medidas de utilizador reais](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração de chaves de medidas de utilizador reais**

   5.   A página apresenta a chave de RUM que é gerado e um fragmento de código JavaScript que tem de ser incorporado na sua página HTML.
 
   ![No código JavaScript para a chave de medidas de utilizador reais](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: Utilizador Real medidas chave e medida JavaScript**
 
   6. Clique em de **cópia** botão para copiar a chave de RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Passo 2: Instrumente a sua aplicação com o pacote do SDK do System Center Mobile RUM

Se estiver familiarizado com o Visual Studio Mobile Center, visite o [Web site](https://mobile.azure.com). Para obter instruções detalhadas sobre a integração do SDK, consulte [introdução ao Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Para utilizar medidas de utilizador reais, execute o seguinte procedimento:

1.  Adicionar o SDK ao projeto

    Durante a pré-visualização do SDK de RUM ATM, tem de referenciar explicitamente o repositório de pacote.

    No seu **app/build.gradle** ficheiro, adicione as seguintes linhas:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    No seu **app/build.gradle** ficheiro, adicione as seguintes linhas:

    ```groovy
    dependencies {   
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Iniciar o SDK

    Abra a classe de atividade principal da sua aplicação e adicione as seguintes declarações de importação:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Procure o `onCreate` chamada de retorno no mesmo ficheiro e adicione o seguinte código:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [Real medidas de utilizador](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Inscrever-se](https://mobile.azure.com) para Mobile Center
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil de Gestor de tráfego](traffic-manager-create-profile.md)

