---
title: "Introdução ao Microsoft Power BI Embedded | Microsoft Docs"
description: "Power BI Embedded na sua aplicação de business intelligence"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: b32b06e9d6cbaacdfbdfe92e2c72cb6763c9eb52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introdução ao Microsoft Power BI Embedded

O **Power BI Embedded** oferece aos fabricantes independentes de software (ISV) e aos programadores uma forma de adicionar rapidamente fantásticos elementos visuais, relatórios e dashboards nas suas aplicações através de um modelo baseado em capacidades, cobrado à hora.

![Diagrama de fluxo de incorporação](media/get-started/introduction.png)

O Power BI Embedded oferece vantagens a um ISV, aos seus programadores e aos clientes. Por exemplo, um ISV pode começar a criar elementos visuais gratuitamente com o Power BI Desktop. Os ISVs podem conseguir um tempo de comercialização mais rápido ao minimizarem os esforços de desenvolvimento de análise visual e destacarem-se entre a concorrência com experiências de dados diferenciadas. Os ISVs também podem optar por cobrar um suplemento pelo valor adicional criado com a análise incorporada.

Os programadores podem passar mais tempo focados na criação da competência central da sua aplicação em vez de gastarem tempo a desenvolver elementos visuais e análise. Os programadores podem satisfazer rapidamente as exigências do cliente em termos de relatórios e dashboards e podem incorporá-los facilmente em APIs e SDKs totalmente documentados. Por último, ao ativarem a exploração de dados de fácil navegação nas suas aplicações, os ISVs permitem que os seus clientes tomem decisões rápidas, com base em dados, no contexto e com confiança a partir de qualquer dispositivo.

## <a name="register-an-application-within-azure-active-directory"></a>Registar uma aplicação no Azure Active Directory

É necessária uma aplicação registada no Azure Active Directory (AAD) para incorporar numa aplicação personalizada. A aplicação registada requer que o inquilino seja um inquilino do Power BI. Um inquilino do Power BI significa que, pelo menos, um utilizador na organização se inscreveu no Power BI. Ter um utilizador inscrito no Power BI irá permitir que as APIs do Power BI apareçam na aplicação registada.

Para obter mais informações sobre como registar uma aplicação no AAD, veja [Registar uma aplicação do Azure AD para incorporar conteúdo do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

## <a name="embed-content-in-your-application"></a>Incorporar conteúdo na sua aplicação

Depois de ter a aplicação registada no AAD, incorpore o conteúdo do Power BI na sua aplicação. Para incorporar conteúdo, utilize a API REST, juntamente com as APIs de JavaScript.

Temos exemplos para ajudá-lo a começar. Para obter as instruções do exemplo, veja [Integrar um dashboard, mosaico ou relatório na aplicação](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

## <a name="get-capacity-and-move-to-production"></a>Obter capacidade e mover para produção

Crie a capacidade do Power BI Embedded no Microsoft Azure para mover a sua aplicação para produção. Para obter informações sobre como criar capacidade, veja [Criar capacidade do Power BI Embedded no portal do Azure](create-capacity.md).

Faça a gestão da capacidade no portal de administração do Power BI. Atribua um cedente de área de trabalho para o ajudar nas áreas de trabalho da aplicação. Para obter mais informações, veja [Gerir capacidades no Power BI Premium e no Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>Passos seguintes

Se estiver pronto para criar capacidade do Power BI Embedded, veja [Criar capacidade do Power BI Embedded no portal do Azure](create-capacity.md).

Se está à procura de instruções de exemplo, veja [Integrar um dashboard, mosaico ou relatório na aplicação](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)