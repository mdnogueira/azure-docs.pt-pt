---
title: "Configurar a extensão de NPS de MFA do Azure | Microsoft Docs"
description: "Depois de instalar a extensão NPS, utilize estes passos de configuração avançada, como adicionar à lista branca IP e de substituição de UPN."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: ef922668f080b8f02f07c2f9724f5a98171fb754
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançadas para a extensão NPS para o multi-factor Authentication

A extensão de servidor de políticas de rede (NPS) expande as funcionalidades de multi-factor Authentication do Azure baseados na nuvem para a sua infraestrutura no local. Este artigo pressupõe que já tenha a extensão instalada e agora pretende saber como personalizar a extensão para si necessidades. 

## <a name="alternate-login-id"></a>ID de início de sessão alternativo

Uma vez que a extensão NPS liga ao seu no local e a nuvem diretórios, que poderá encontrar um problema em que o seu local principal os nomes de utilizador (UPNs) não correspondem os nomes na nuvem. Para resolver este problema, utilize os IDs de início de sessão alternativo. 

Dentro da extensão NPS, pode designar um atributo do Active Directory a utilizar em vez do UPN para Azure multi-factor Authentication. Isto permite-lhe proteger os seus recursos no local com a verificação sem modificar os UPNs no local. 

Para configurar os IDs de início de sessão alternativo, aceda a `HKLM\SOFTWARE\Microsoft\AzureMfa` e edite os valores de registo seguinte:

| Nome | Tipo | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | Cadeia | vazio | Designe o nome de atributo do Active Directory que pretende utilizar em vez do UPN. Este atributo é utilizado como o atributo AlternateLoginId. Se este valor de registo está definido como um [atributo do Active Directory válido](https://msdn.microsoft.com/library/ms675090.aspx) (por exemplo, correio ou displayName), em seguida, valor o atributo é utilizado em vez de UPN do utilizador para autenticação. Se este valor de registo está vazio ou não configurado, em seguida, AlternateLoginId está desativada e UPN do utilizador é utilizado para autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | Valor booleano | Falso | Utilize este sinalizador para forçar a utilização de Catálogo Global para pesquisas LDAP ao procurar o AlternateLoginId. Configurar um controlador de domínio como um Catálogo Global, adicione o atributo de AlternateLoginId para o Catálogo Global e, em seguida, ative este sinalizador. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurada (não vazia), **este sinalizador é imposta como verdadeiro**, independentemente do valor da definição de registo. Neste caso, a extensão NPS requer o Catálogo Global estar configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | Cadeia | vazio | Forneça uma lista de ponto e vírgula separada de florestas para procurar. Por exemplo, *contoso.com;foobar.com*. Se este valor de registo estiver configurada, a extensão NPS procura iteratively todas as florestas pela ordem que foram apresentados e devolve o primeiro valor AlternateLoginId com êxito. Se este valor de registo não estiver configurado, está limitada a pesquisa de AlternateLoginId para o domínio atual.|

Para resolver problemas relacionados com os IDs de início de sessão alternativo, utilize os passos recomendados para [alternativa erros de ID de início de sessão](multi-factor-authentication-nps-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções de IP

Se precisar de monitorizar a disponibilidade de servidor, como se balanceadores de carga Certifique-se de que os servidores estão a executar antes de enviar cargas de trabalho, não pretender que estas verificações até ser bloqueado por pedidos de verificação. Em vez disso, crie uma lista de endereços IP que sabe que são utilizados pelas contas de serviço e desative os requisitos de multi-factor Authentication para essa lista. 

Para configurar uma lista branca de IP, aceda a `HKLM\SOFTWARE\Microsoft\AzureMfa` e configure o seguinte valor de registo: 

| Nome | Tipo | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | Cadeia | vazio | Forneça uma lista de ponto e vírgula separada de endereços IP. Inclua os endereços IP de máquinas onde os pedidos de serviço têm origem, como o servidor NAS/VPN. Os intervalos IP são sub-redes não são suportadas. <br><br> Por exemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

Quando um pedido é apresentada de um endereço IP que existe na lista branca, a verificação é ignorada. A lista branca de IP é comparado com o endereço IP que é fornecido no *ratNASIPAddress* atributos do pedido RADIUS. Se um pedido RADIUS é apresentada sem o atributo ratNASIPAddress, é registado o seguinte aviso: "Está a ser ignorada lista branca de P_WHITE_LIST_WARNING::IP como IP de origem está em falta no pedido RADIUS no atributo NasIpAddress."

## <a name="next-steps"></a>Passos seguintes

[Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](multi-factor-authentication-nps-errors.md)