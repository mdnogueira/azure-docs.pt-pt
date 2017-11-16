---
title: Azure multi-factor Authentication - como funciona
description: "A Multi-Factor Authentication do Azure ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Isto fornece segurança adicional, exigindo que uma segunda forma de autenticação e fornece autenticação forte através de uma gama de opções de verificação fácil."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 1f3495b038171edd713678aef49be9401ab458dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="how-azure-multi-factor-authentication-works"></a>Como funciona o Azure multi-factor Authentication
A segurança de verificação de dois passos reside na respetiva abordagem em camadas. Compromisso de vários fatores de autenticação apresenta um significativo desafio para os atacantes. Mesmo se um atacante conseguir saber a palavra-passe do utilizador, é inútil sem também ter posse do dispositivo fidedigno. 

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

A Multi-Factor Authentication do Azure ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples.  Isto fornece segurança adicional, exigindo que uma segunda forma de autenticação e fornece autenticação forte através de uma gama de opções de verificação fácil.


## <a name="methods-available-for-two-step-verification"></a>Métodos disponíveis para a verificação de dois passos
Quando um utilizador inicia sessão, uma verificação adicional é enviada ao utilizador.  Seguem-se uma lista de métodos que pode ser utilizado para esta verificação de segundo.

| Método de verificação | Descrição |
| --- | --- |
| Chamada telefónica |É efetuada uma chamada para o telemóvel registado um utilizador. O utilizador introduz um PIN, se necessário, em seguida, premir a tecla #. |
| Mensagem de texto |É enviada uma mensagem de texto para o telemóvel do utilizador com um código de seis dígitos. O utilizador introduz este código na página de início de sessão. |
| Notificação de aplicação móvel |É enviado um pedido de verificação para Smartphone de um utilizador. O utilizador deve introduzir um PIN, se necessário, em seguida, seleciona **verifique** da aplicação móvel. |
| Código de verificação de aplicação móvel |A aplicação móvel, o que está em execução no Smartphone de um utilizador, apresenta um código de verificação que altera a cada 30 segundos. O utilizador localiza o código mais recente e introduz-la na página de início de sessão. |
| tokens OATH de terceiros | Servidor de autenticação multi-factor do Azure pode ser configurado para aceitar os métodos de verificação por terceiros. |

Multi-factor Authentication do Azure fornece métodos de verificação selecionável para a nuvem e de servidor. Pode escolher que métodos estão disponíveis para os seus utilizadores: chamada telefónica, texto, a notificação de aplicação ou códigos de aplicações. Para obter mais informações, consulte [métodos de verificação selecionável](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os diferentes [versões e métodos de consumo de multi-factor Authentication do Azure](multi-factor-authentication-versions-plans.md)

- Escolha se pretende implementar o MFA do Azure [na nuvem ou no local](multi-factor-authentication-get-started.md)

- Leitura respostas [perguntas mais frequentes](multi-factor-authentication-faq.md)