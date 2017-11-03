---
title: "Configurar um nome de domínio personalizado para a instância de API Management do Azure | Microsoft Docs"
description: "Este tópico descreve como configurar um nome de domínio personalizado para a instância de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: a7abba87035016eee05b4ab663404f10127bba1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado 

Quando cria uma instância de gestão de API (APIM), Azure atribui-o para um subdomínio api.net do azure (por exemplo, `apim-service-name.azure-api.net`). No entanto, pode expor os seus pontos finais APIM utilizando o seu próprio nome de domínio, tal como **contoso.com**. Este tutorial mostra como mapear um nome DNS existente personalizado para pontos finais expostos por uma instância de API Management do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de API Management do Azure](get-started-create-service-instance.md).
+ Um nome de domínio personalizado que é detido por si. O nome de domínio personalizado que pretende utilizar, tem de ser fornecidas separadamente e alojada num servidor DNS. Este tópico não fornecer instruções sobre como alojar um nome de domínio personalizado.
+ Tem de ter um certificado válido com uma chave pública e privada (. PFX). Requerente ou nome alternativo do requerente (SAN) tem de corresponder ao nome de domínio (isto ativa APIM expor, de forma segura, URLs através de SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utilize o portal do Azure para definir um nome de domínio personalizado

1. Navegue até à sua instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **domínios personalizados e SSL**.
    
    Não há um número de pontos finais para o qual pode atribuir um nome de domínio personalizado. Atualmente, os seguintes pontos finais estão disponíveis: 
    + **Proxy** (predefinição é: `<apim-service-name>.azure-api.net`), 
    + **Portal** (predefinição é: `<apim-service-name>.portal.azure-api.net`),     
    + **Gestão** (predefinição é: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (predefinição é: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > Pode atualizar todos os pontos finais ou alguns dos mesmos. Normalmente, os clientes atualizar **Proxy** (este URL é utilizado para chamar a API exposta através da gestão de API) e **Portal** (portal do programador URL). **Gestão** e **SCM** pontos finais são utilizados internamente por clientes APIM e, por conseguinte, com menos frequência estão atribuídos um nome de domínio personalizado.
3. Selecione o ponto final que pretende atualizar. 
4. Na janela do lado direito, clique em **personalizada**.

    + No **nome de domínio personalizado**, especifique o nome que pretende utilizar. Por exemplo, `api.contoso.com`. <br/>Nomes de domínio de caráter universal (por exemplo, *. domain.com) também são suportados.
    + No **certificado**, especifique uma válida. Ficheiro PFX que pretende carregar. 
    + Se o certificado tiver uma palavra-passe, introduza-na **palavra-passe** campo.
1. Clique em aplicar.

    >[!NOTE]
    >O processo de atribuição do certificado pode demorar 15 minutos ou modo.

## <a name="next-steps"></a>Passos seguintes

[Atualizar e dimensionar o serviço](upgrade-and-scale.md)