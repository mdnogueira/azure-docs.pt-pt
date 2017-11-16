---
title: "Saiba mais sobre a verificação de dois passos na MFA do Azure | Microsoft Docs"
description: "O que é o Azure multi-factor Authentication, porquê utilizar o MFA, obter mais informações sobre o cliente de multi-factor Authentication e os diferentes métodos e as versões disponíveis. "
keywords: "Introdução à MFA, descrição geral de mfa, o que é o mfa"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
ms.openlocfilehash: a928344dfc41687c92e7845bc304a05122ab8a92
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-multi-factor-authentication"></a>O que é a Multi-Factor Authentication do Azure?
Verificação de dois passos é um método de autenticação que requer mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. Funciona exigindo quaisquer dois ou mais dos seguintes métodos de verificação:

* Algo sabe (normalmente uma palavra-passe)
* Algo que tem (um dispositivo fidedigno não for facilmente duplicado, como um telefone)
* Algo que são (biometria)

<center>![Nome de utilizador e palavra-passe](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![certificados](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Smart Phone](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![de Smart Card](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![nome de utilizador e palavra-passe](./media/multi-factor-authentication/cert.png)</center>

A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Oferece autenticação forte através de vários métodos de verificação, incluindo chamada telefónica, mensagem de texto ou verificação de aplicação móvel.

## <a name="why-use-azure-multi-factor-authentication"></a>Porquê utilizar o multi-factor Authentication do Azure?
Hoje, mais do que nunca, pessoas cada vez mais estão ligadas. Com inteligentes telemóveis, tablets, portáteis e PCs, o que as pessoas têm várias opções para aceder as respetivas contas e as aplicações a partir de qualquer lugar e permaneça ligado a qualquer momento.

Multi-factor Authentication do Azure é uma solução fiável, fácil de utilizar e escalável que fornece um segundo método de autenticação para proteger os seus utilizadores.

| ![Fácil de Utilizar](./media/multi-factor-authentication/simple.png) | ![Escalável](./media/multi-factor-authentication/scalable.png) | ![Sempre protegido](./media/multi-factor-authentication/protected.png) | ![Fiável](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Fácil de utilizar** |**Dimensionável** |**Sempre protegido** |**Fiável** |

* **Fácil de utilizar** -Azure multi-factor Authentication é simple de configurar e utilizar. A proteção adicional que vem com o Azure multi-factor Authentication permite aos utilizadores gerir os seus próprios dispositivos. Melhor de tudo, em muitos casos, pode ser configurado com apenas alguns cliques.
* **Dimensionável** -Azure multi-factor Authentication utiliza a capacidade da nuvem e integra-se com o ambiente AD e aplicações personalizadas. Esta proteção é expandida, mesmo para os cenários de volume elevado, fundamentais.
* **Sempre protegido** -Azure multi-factor Authentication fornece autenticação forte, utilizando as normas da indústria mais elevadas.
* **Fiável** -Microsoft garante a disponibilidade de 99,9% do Azure multi-factor Authentication. O serviço é considerado indisponível quando não o consegue receber ou processar pedidos de verificação para a verificação de dois passos.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [como funciona o Azure multi-factor Authentication](multi-factor-authentication-how-it-works.md)

- Leia sobre os diferentes [versões e métodos de consumo de multi-factor Authentication do Azure](multi-factor-authentication-versions-plans.md)
