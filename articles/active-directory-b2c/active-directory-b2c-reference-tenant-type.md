---
title: "Do Azure Active Directory B2C: Residency de disponibilidade & dados da região | Microsoft Docs"
description: "Um tópico sobre os tipos de inquilinos do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: gsacavdm
ms.openlocfilehash: facd66f0324e382ea7609a035de8129ba433846f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Do Azure Active Directory B2C: Residency de disponibilidade & dados da região
Disponibilidade de região e residency de dados são dois conceitos muito diferentes que aplicam de forma diferente para o Azure AD B2C do resto do Azure. Este artigo irá explicam as diferenças entre estes dois conceitos e comparar como se aplicam ao Azure versus do Azure AD B2C.

## <a name="summary"></a>Resumo
O Azure AD B2C é **geralmente disponível em todo o mundo** com a opção para **residency dados nos Estados Unidos ou em Europa**.

## <a name="concepts"></a>Conceitos
* **Disponibilidade de região** refere-se ao onde um serviço está disponível para utilização.
* **Residency dados** refere-se para armazenar dados de utilizador.

## <a name="region-availability"></a>Disponibilidade de região
O Azure AD B2C está disponível em todo o mundo através da nuvem pública do Azure. 

Isto é diferente do modelo maioria dos outros siga de serviços do Azure que alguns disponibilidade com residency de dados. Pode ver os exemplos deste em ambos os Azure [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) página e o [Calculadora de preços do Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência dos dados
O Azure AD B2C armazena os dados de utilizador nos Estados Unidos ou Europa.

Residency de dados é determinado com base na qual país/região é selecionado quando [criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).

![Captura de ecrã de um inquilino de pré-visualização](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Os dados residem nos Estados Unidos para países/regiões seguintes:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panama, Porto Rico e Trindade e Tobago

Os dados residem na Europa para países/regiões seguintes:

> Algeria, Austria, Azerbaijan, Bahrain, Belarus, Belgium, Bulgaria, Croatia, Cyprus, República Checa, Dinamarca, Egypt, Estonia, Finlândia, França, Datacenters, Grécia, Hungary, Iceland, Irlanda, Israel, (Itália), Jordan, Kazakhstan, Kenya, Kuwait, Lativa, Lebanon, Macedónia Liechtenstein, Lituania, Luxembourg, FYRO, Malta, Montenegro, Morocco, Países Baixos, Nigeria, Noruega, Oman, Paquistão, Polónia, Portugal, Qatar, Romania, Russia, Arábia Saudita, Serbia, Slovakia, Slovenia, África do Sul, Espanha, Suécia, Suíça, Tunisia, Turquia, Ukraine, Unidas Arab Emirates e Reino Unido.

As restantes países/regiões estão a ser adicionado à lista.  Por agora, pode continuar a utilizar Azure AD B2C por diretriz qualquer um dos países/regiões acima.

> Afeganistão, Argentina, Austrália, Brasil, subordinado, Colombia, Ecuador, RAE de Hong Kong, Índia, Indonesia, Iraq, Japão, Coreia, Malaysia, Nova Zelândia, Paraguay, Peru, Filipinas, Singapura, Sri Lanca, Taiwan, Thailand, Uruguay e Venezuela.

## <a name="preview-tenant"></a>Inquilino de pré-visualização
Se criou um inquilino do B2C durante o período de pré-visualização do Azure AD B2C, é provável que o **inquilino tipo** indica **inquilino de pré-visualização**. Se for este o caso, tem de utilizar o inquilino apenas para desenvolvimento e fins de teste e não para aplicações de produção.

> [!IMPORTANT]
> Não há nenhum caminho de migração de um inquilino do B2C de pré-visualização para um inquilino do B2C escala de produção. Tenha em atenção que se sabe problemas quando eliminar um inquilino de pré-visualização B2C e voltar a criar um inquilino do B2C escala de produção com o mesmo nome de domínio. Tem de criar um inquilino do B2C escala de produção com um nome de domínio diferente.


![Captura de ecrã de um inquilino de pré-visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
