---
title: "Introdução de PoC manual do Azure Active Directory | Microsoft Docs"
description: "Explorar e implementar rapidamente a cenários de identidade e gestão de acesso"
services: active-directory
keywords: do Azure Active Directory, manual, uma prova de conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: fb4767bae6a5435f5739162eaf52edec2f0cbf0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Azure Active Directory prova de conceito manual: introdução

Este artigo fornece diretrizes para explorar o Azure diferentes capacidades do AD numa prova de conceito (PoC). O público-alvo deste documento é integradores de sistemas, Arquitetos de identidade e profissionais de TI

## <a name="how-to-use-this-playbook"></a>Como utilizar este manual de comunicação social

1. Utilize o [tema](active-directory-playbook-ingredients.md#theme) secção e escolha area(s) de interesse com base nas suas necessidades.  
2. Definir o âmbito de PoC escolhendo os cenários que se alinham com os seus objetivos empresariais. O mais curto melhor. É recomendável fazer como curto e concisas quanto possível transmitir o valor para os intervenientes, para minimizar a complexidade regressam-lo.  
3. Utilize o [implementação](active-directory-playbook-implementation.md) secção para compreender os cenários e o que seriam eles significam para o seu ambiente. Cada cenário, iremos descrevem como configurá-lo (o que chamamos [blocos modulares](active-directory-playbook-building-blocks.md)) e como navegar os cenários. 
4. Cada bloco modular explica os pré-requisitos necessários, bem como uma hora aproximada em que a concluir. Isto pode ajudá-lo durante o processo de planeamento. 
5. Com base em 1-3 acima, definir o [ambiente](active-directory-playbook-ingredients.md#environment) no qual pretende executar. Aconselhamo-a esforçar-para um ambiente de produção obter um bom funcionamento da experiência para os seus utilizadores. 
6. Quando a ter requisitos em conflito, utilize esta matriz de compromisso útil 
   * Mostrar centrada em tema do valor  
   * Smoothness para preparar, configurar e executar os cenários 
   * Tempo mínimo para executar os cenários de destino 
   * Tal como próximo de produção como exequível dentro do seu restrições 

>[!NOTE]
> Ao longo deste artigo, irá ver alguns produtos mencionados como exemplos para sua comodidade e aplicações de terceiros específico. Azure AD suporta milhares de aplicações no nosso [Galeria de aplicações](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) que pode utilizar com base nas suas necessidades e ambiente. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]