---
title: "Application Insights para resolver problemas de políticas personalizadas - Azure AD B2C | Microsoft Docs"
description: "como configurar o Application Insights para rastrear a execução de políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: saeda
ms.openlocfilehash: 8c79df33cd5f04f490e2cc6372f7e8ac1c4d9bbe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>B2C do Azure Active Directory: Recolher registos

Este artigo fornece os passos para recolher registos do Azure AD B2C, de modo a que pode diagnosticar problemas com as políticas personalizadas.

>[!NOTE]
>Atualmente, os registos de atividade de detalhado descritos aqui concebidos **apenas** para ajudar no desenvolvimento de políticas personalizadas. Utilize o modo de desenvolvimento em produção.  Os registos de recolhem todas as afirmações enviadas de e para os fornecedores de identidade durante o desenvolvimento.  Se utilizar na produção, o programador assume a responsabilidade para PII (privada informações de identificação) recolhida no registo de aplicação Insights que possuem.  Estes registos detalhados apenas são recolhidos quando a política é colocada no **modo de desenvolvimento**.


## <a name="use-application-insights"></a>Utilizar o Application Insights

O Azure AD B2C suporta uma funcionalidade para enviar dados para o Application Insights.  Application Insights fornece uma forma para diagnosticar exceções e visualizar os problemas de desempenho de aplicações.

### <a name="setup-application-insights"></a>Configurar o Application Insights

1. Aceda ao [Portal do Azure](https://portal.azure.com). Certifique-se de que está no inquilino com a sua subscrição do Azure (não o inquilino do Azure AD B2C).
1. Clique em **+ novo** no menu de navegação esquerdo.
1. Procure e selecione **Application Insights**, em seguida, clique em **criar**.
1. Preencha o formulário e clique em **criar**. Selecione **geral** para o **tipo de aplicação**.
1. Quando o recurso tiver sido criado, abra o recurso do Application Insights.
1. Localizar **propriedades** no menu à esquerda e clique no mesmo.
1. Copiar o **chave de instrumentação** e guardá-lo para a secção seguinte.

### <a name="set-up-the-custom-policy"></a>Configurar a política personalizada

1. Abra o ficheiro RP (por exemplo, SignUpOrSignin.xml).
1. Adicionar os seguintes atributos para o `<TrustFrameworkPolicy>` elemento:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Se não existir já, adicionar um nó subordinado `<UserJourneyBehaviors>` para o `<RelyingParty>` nós. Tem de estar localizado imediatamente após o`<DefaultUserJourney ReferenceId="YourPolicyName" />`
2. Adicionar o nó seguinte como um elemento subordinado do `<UserJourneyBehaviors>` elemento. Certifique-se de que substitui `{Your Application Insights Key}` com o **chave de instrumentação** que obteve do Application Insights na secção anterior.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"`indica ApplicationInsights a fim de acelerar a telemetria através do pipeline de processamento, aconselhável para o desenvolvimento, mas restrita em volumes elevadas.
  * `ClientEnabled="true"`envia o script do lado do cliente ApplicationInsights para o registo de erros de vista e do lado do cliente de página (e não necessários).
  * `ServerEnabled="true"`envia o JSON de UserJourneyRecorder existente como um evento personalizado para o Application Insights.
Exemplo:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Carregue a política.

### <a name="see-the-logs-in-application-insights"></a>Consulte os registos no Application Insights

>[!NOTE]
> Não há um curto atraso (menos de cinco minutos) antes de poder ver novos registos no Application Insights.

1. Abra o recurso do Application Insights que criou no [portal do Azure](https://portal.azure.com).
1. No **descrição geral** menu, clique em **análise**.
1. Abra um novo separador no Application Insights.
1. Aqui está uma lista de consultas que pode utilizar para ver os registos

| Consulta | Descrição |
|---------------------|--------------------|
Rastreios | Ver todos os registos gerados pelo Azure AD B2C |
os rastreios \| onde timestamp > ago(1d) | Ver todos os registos gerados pelo Azure AD B2C para o último dia

As entradas poderão ser longas.  Exportar para CSV para observá.

Pode saber mais sobre a ferramenta de análise [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>A Comunidade desenvolveu um Visualizador de journey de utilizador para ajudar os programadores de identidade.  Não é suportada pela Microsoft e disponibilizado estritamente como-é.  Este lê a partir da sua instância do Application Insights e fornece uma vista de estrutura de bem do utilizador eventos journey.  Obter o código de origem e implementá-la na sua própria solução.

>[!NOTE]
>Atualmente, os registos de atividade de detalhado descritos aqui concebidos **apenas** para ajudar no desenvolvimento de políticas personalizadas. Utilize o modo de desenvolvimento em produção.  Os registos de recolhem todas as afirmações enviadas de e para os fornecedores de identidade durante o desenvolvimento.  Se utilizar na produção, o programador assume a responsabilidade para PII (privada informações de identificação) recolhida no registo de aplicação Insights que possuem.  Estes registos detalhados apenas são recolhidos quando a política é colocada no **modo de desenvolvimento**.

[Repositório do Github para exemplos de política personalizada não suportado e ferramentas relacionadas](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Passos Seguintes

Explore os dados no Application Insights para o ajudar a compreender como o Framework de experiência de identidade B2C subjacente funciona para fornecer as suas próprias identidades experiências com.
