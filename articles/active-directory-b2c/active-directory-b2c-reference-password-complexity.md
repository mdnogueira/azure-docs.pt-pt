---
title: Complexidade de palavra-passe - Azure AD B2C | Microsoft Docs
description: Como configurar os requisitos de complexidade de palavras-passe fornecidas pelos consumidores no Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 800199b3d1d91d700d26f988a4d49713028de1ce
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>O Azure AD B2C: Configurar os requisitos de complexidade de palavras-passe

> [!NOTE]
> **Esta funcionalidade está em pré-visualização.**  Contacte [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) ter o seu inquilino de teste ativado com esta funcionalidade.

O Azure Active Directory B2C (Azure AD B2C) suporta alterar os requisitos de complexidade de palavras-passe fornecidas por um utilizador final ao criar uma conta.  Por predefinição, o Azure AD B2C utiliza `Strong` palavras-passe.  Azure AD B2C suporta também para controlar a complexidade de palavras-passe que os clientes podem utilizar as opções de configuração.

## <a name="when-password-rules-are-enforced"></a>Quando são impostas quais regras de palavra-passe

Durante a inscrição ou de reposição de palavra-passe, um utilizador final tem de fornecer uma palavra-passe que cumpra as regras de complexidade.  Regras de complexidade de palavra-passe são impostas por políticas.  É possível ter uma política para exigir um pin com quatro dígitos durante a inscrição tempo outra política requer uma cadeia de oito carateres durante a inscrição.  Por exemplo, pode utilizar uma política com a complexidade de palavra-passe diferente para os adultos que para elementos subordinados.

Complexidade de palavra-passe nunca é aplicada durante o início de sessão.  Os utilizadores recebem nunca durante o início de sessão para alterar a palavra-passe porque esta não cumpre o requisito de complexidade atual.

Eis os tipos de políticas, onde a complexidade de palavra-passe pode ser configurada:

* Política de inscrição ou início de sessão
* Política de reposição de palavra-passe
* Política personalizada ([configurar a complexidade da palavra-passe numa política personalizada do](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Como configurar a complexidade de palavra-passe

1. Siga estes passos para [navegue para o Azure AD B2C definições](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Abra **políticas de inscrição ou início de sessão**.
1. Selecione uma política e clique em **editar**.
1. Abra **complexidade de palavra-passe**.
1. Alterar a complexidade de palavra-passe para esta política para **simples**, **forte**, ou **personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Descrição |
| --- | --- |
| Simples | Palavra-passe com, pelo menos, 8 a 64 carateres. |
| Forte | Palavra-passe com, pelo menos, 8 a 64 carateres. Requer 3 fora de 4 em minúsculas, maiúsculos, números ou símbolos. |
| Personalizado | Esta opção fornece o maior parte dos controlo sobre as regras de complexidade de palavra-passe.  Permite configurar um comprimento personalizado.  Também permite aceitar apenas de número de palavras-passe (pins). |

## <a name="options-available-under-custom"></a>As opções disponíveis em personalizada

### <a name="character-set"></a>Conjunto de carateres

Permite-lhe aceitar dígitos apenas (pins) ou o completo conjunto de carateres.

* **Apenas números** permite dígitos apenas (0-9) ao introduzir uma palavra-passe.
* **Todos os** permite que qualquer letra, número ou símbolo.

### <a name="length"></a>comprimento

Permite-lhe controlar os requisitos de comprimento da palavra-passe.

* **Comprimento mínimo** tem de ser, pelo menos, 4.
* **Comprimento máximo** tem de ser maior ou igual ao comprimento mínimo e pode ter no máximo de 64 carateres.

### <a name="character-classes"></a>Classes de carateres

Permite-lhe controlar os tipos de carateres diferentes utilizados a palavra-passe.

* **2 de 4: caráter em minúsculas, caráter em maiúsculas, número (0-9), símbolo** garante a palavra-passe contém, pelo menos, dois tipos de carateres. Por exemplo, um número e um caráter em minúsculas.
* **3 de 4: caráter em minúsculas, caráter em maiúsculas, número (0-9), símbolo** garante a palavra-passe contém, pelo menos, dois tipos de carateres. Por exemplo, um número, um caráter em minúsculas e um caráter em maiúsculas.
* **4 de 4: caráter em minúsculas, caráter em maiúsculas, número (0-9), símbolo** garante a palavra-passe contém todos os para tipos de carateres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustration do utilizador final. Alguns estudos demonstraram que este requisito não melhorar a entropia de palavra-passe. Consulte [NIST diretrizes de palavra-passe](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
