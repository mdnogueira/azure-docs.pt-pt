---
title: "Azure Active Directory híbrida identidade considerações de design - determine os requisitos de proteção de dados | Microsoft Docs"
description: "Quando planear a sua solução de identidade híbrida, identificar os requisitos de proteção de dados para a sua empresa e que opções estão disponíveis para melhor satisfazer estes requisitos."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 96bf9d4c26a22f718c29804c11681199e775f589
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Planear a segurança dos dados através da solução de identidade segura de otimização
O primeiro passo para proteger os dados é identificar quem pode aceder a dados e como parte deste processo, que tem de ter uma identidade solução que pode se integra com o sistema para fornecer capacidades de autenticação e autorização. Autenticação e autorização, muitas vezes, são confundidos entre si e as respetivas funções misunderstood. Na realidade, são bastante diferentes, conforme mostrado na imagem abaixo:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**Fases do ciclo de vida de gestão de dispositivos móveis**

Quando planear a sua solução de identidade híbrida deve compreender os requisitos de proteção de dados da sua empresa e que opções estão disponíveis para melhor satisfazer estes requisitos.

> [!NOTE]
> Depois de concluir a planear a segurança dos dados, reveja [determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para se certificar de que as suas seleções sobre requisitos de autenticação multifator não foram afetados pelas decisões efetuadas nesta secção.
> 
> 

## <a name="determine-data-protection-requirements"></a>Determinar os requisitos de proteção de dados
Na duração de mobilidade, a maioria das empresas tem um objetivo comum: permitir que os utilizadores sejam produtivos nos respetivos dispositivos móveis no local ou remotamente a partir de qualquer lugar para aumentar a produtividade. Enquanto pode ser um objetivo comuns, as empresas que tenham essa requisito também será preocupação relativamente a quantidade de ameaças que têm de ser mitigados para manter os dados da empresa seguros e manter a privacidade do utilizador. Cada empresa pode ter requisitos diferentes neste aspeto, regras de conformidade diferente que irão variar, de acordo com o setor a empresa está a funcionar serão levar a decisões de conceção diferentes. 

No entanto, existem alguns aspetos de segurança que devem ser explorou e validados, independentemente da indústria, que serão explicados de forma na secção seguinte.

## <a name="data-protection-paths"></a>Caminhos de proteção de dados
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**Caminhos de proteção de dados**

No diagrama acima, o componente de identidade vai ser o primeiro ser verificado antes dos dados são acedidos. No entanto, estes dados podem estar em diferentes Estados durante o período de tempo que foi acedido. Cada número neste diagrama representa um caminho no qual dados podem ser localizados num ponto no tempo. Estes números são explicados abaixo:

1. Proteção de dados ao nível do dispositivo.
2. Proteção de dados em trânsito.
3. Proteção de dados, rest no local.
4. Proteção de dados Inativos na nuvem.

Embora a técnica controla que irá permitir que as TI a proteger os dados em cada um dessas fases não são diretamente disponibilizadas pela solução de identidade híbrida, é necessário que a solução de identidade híbrida é capaz de tirar partido da nuvem e no local recursos de gestão de identidade para identificar o utilizador antes de conceder acesso aos dados. Quando planear a sua solução de identidade híbrida Certifique-se de que as perguntas seguintes são respondidas, de acordo com requisitos da sua organização:

## <a name="data-protection-at-rest"></a>Proteção de dados Inativos
Independentemente de onde os dados estão inativos (dispositivo, nuvem ou no local), é importante fazer uma avaliação neste aspeto, a compreender as necessidades da organização. Para esta área, certifique-se de que são mais frequentes seguintes perguntas:

* A sua empresa precisa de proteger os dados Inativos?
  * Se Sim, é a solução de identidade híbrida consegue integrar com a sua infraestrutura no local atual?
  * Se Sim, é a solução de identidade híbrida consegue integrar com as cargas de trabalho localizadas na nuvem?
* A gestão de identidades de nuvem é capaz de proteger as credenciais do utilizador e outros dados armazenados na nuvem?

## <a name="data-protection-in-transit"></a>Proteção de dados em trânsito
Dados em trânsito entre o dispositivo e o Centro de dados ou entre o dispositivo e na nuvem devem ser protegidos. No entanto, a ser em trânsito não significa necessariamente um processo de comunicações com um componente fora do seu serviço em nuvem; são transmitidos internamente, além disso, essas impedindo duas redes virtuais. Para esta área, certifique-se de que são mais frequentes seguintes perguntas:

* A sua empresa precisa de proteger dados em trânsito?
  * Se Sim, é a solução de identidade híbrida consegue integrar com controlos seguros, tais como o SSL/TLS?
* A gestão de identidades de nuvem manter os o tráfego de e para dentro do arquivo de diretório (dentro e entre centros de dados) assinado?

## <a name="compliance"></a>Conformidade
Requisitos de conformidade de regulamentação, leis e regulamentos irão variar de acordo com a indústria que pertence a sua empresa. As empresas na elevada indústrias reguladas devem ser abordadas preocupações de gestão de identidades relacionado com problemas de conformidade. Normas como Sarbanes-Oxley (SOX), o Health Insurance Portability e Accountability Act (HIPAA), o ato de Gramm-Leach-Bliley (GLBA) e a e Payment Card Industry Data segurança (PCI DSS) são muito restritas sobre identidade e acesso. A solução de identidade híbrida que a empresa irá adotar tem de ter as capacidades centrais irão cumprir os requisitos de uma ou mais estes regulamentos. Para esta área, certifique-se de que são mais frequentes seguintes perguntas:

* A solução de identidade híbrida é compatível com os requisitos de regulamentação para a sua empresa?
* Tem incorporada a solução de identidade híbrida capacidades que permitem a sua empresa para serem requisitos regulamentares de conformidade? 

> [!NOTE]
> Certifique-se de que toma nota de cada resposta e que compreende os fundamentos. [Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Ao responder a estas questões, vai selecionar que opções melhor se adapta às sua empresa precisa.
> 
> 

## <a name="next-steps"></a>Passos seguintes
 [Determinar os requisitos de gestão de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

