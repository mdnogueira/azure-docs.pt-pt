---
title: "Glossário de proteção de identidade do Azure Active Directory | Microsoft Docs"
description: "Glossário de proteção de identidade do Azure Active Directory"
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, o cloud app discovery, gestão de aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança, Glossário"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5b6d2db0a8df31fd159053cceab6b9b320ffd8cb
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário de proteção de identidade do Azure Active Directory
### <a name="at-risk-user"></a>Em risco (utilizador)
Um utilizador com um ou mais eventos de risco Active Directory. 

### <a name="atypical-sign-in-location"></a>Localização de início de sessão atípica
Um início de sessão a partir de uma localização geográfica, que não é típica para o utilizador específico, semelhantes utilizadores ou o inquilino.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Active Directory que fornece uma vista consolidada sobre eventos de risco e potenciais vulnerabilidades que afetam as identidades de uma organização.

### <a name="conditional-access"></a>Acesso condicional
Uma política para proteger o acesso aos recursos. Regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  Regras de exemplo incluem restringir o acesso com base na localização do utilizador, método de autenticação de utilizador ou de estado de funcionamento do dispositivo.

### <a name="credentials"></a>Credenciais
Informações que incluem uma prova de identificação que é utilizada para obter acesso ao local e de recursos de rede e de identificação. Exemplos de credenciais são os nomes de utilizador e palavras-passe, smart cards e certificados.

### <a name="event"></a>Evento
Um registo de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-event"></a>FALSO-positivos (evento de risco)
Um Estado de eventos de risco definir manualmente por um utilizador Identity Protection, que indica que o evento de risco foi investigado e foi incorretamente sinalizado como um evento de risco.

### <a name="identity"></a>Identidade
A pessoa ou entidade que tem de ser verificada através de autenticação, com base em critérios como palavra-passe ou um certificado.

### <a name="identity-risk-event"></a>Eventos de risco de identidade
Evento do AAD que foi sinalizado como anómala pelo Identity Protection e isso poderá indicar que uma identidade foi comprometida.

### <a name="ignored-risk-event"></a>Ignorado (evento de risco)
Um Estado de eventos de risco definir manualmente por um utilizador Identity Protection, que indica que o evento de risco é fechado sem colocar uma ação de remediação.

### <a name="impossible-travel-from-atypical-locations"></a>Impossível de localizações atípicas
Um evento de risco acionado quando são detetados dois inícios de sessão para o mesmo utilizador, em que pelo menos uma delas é a partir de uma localização de início de sessão atípica e, em que o tempo entre os inícios de sessão é mais pequeno que o tempo mínimo que demoraria a fisicamente viajam entre estas localizações.  

### <a name="investigation"></a>Investigação
O processo de rever as atividades, registos e outras informações relevantes relacionados com um evento de risco para decidir se os passos de remediação ou atenuação são necessários, compreender se e como a identidade foi comprometido e compreender a forma como a identidade comprometida foi utilizada.

### <a name="leaked-credentials"></a>Credenciais obtidas ilicitamente
Um evento de risco acionado quando as credenciais do utilizador atual (nome de utilizador e palavra-passe) encontram-se registado publicamente na web escuro pelos investigadores do nosso.

### <a name="mitigation"></a>Mitigação
Uma ação para limitar ou eliminar a capacidade de um atacante para explorar uma identidade comprometida ou dispositivo sem restaurar o dispositivo ou a identidade para um Estado de segurança. Uma mitigação não resolver eventos de risco anteriores associados o identidade ou o dispositivo.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Tem um método de autenticação que requer dois ou mais métodos de autenticação, que podem incluir um utilizador, um certificado; algo o utilizador sabe, tais como nomes de utilizador, palavras-passe ou expressões de passagem; atributos físicos, tal como um thumbprint; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Deteção offline
A deteção de anomalias e edição de avaliação do risco de um evento, tais como a tentativa de início de sessão após o facto, um evento que já tiver ocorrido.

### <a name="policy-condition"></a>Condição de política
Uma parte de uma política de segurança que define as entidades (grupos de utilizadores, aplicações, plataformas de dispositivos, Estados do dispositivo, intervalos de IP, tipos de clientes) incluída na política ou excluído do mesmo.

### <a name="policy-rule"></a>Regra de política
A parte de uma política de segurança que descreve as circunstâncias acionam a política e as ações executadas quando a política é activada.

### <a name="prevention"></a>Prevenção
Uma ação para evitar danos na organização através de abuso de um dispositivo ou uma identidade suspeito ou saber para ficar comprometida. Uma ação de prevenção não proteger o dispositivo ou a identidade e não resolve eventos de risco anteriores.

### <a name="privileged-user"></a>Privilégio (utilizador)
Um utilizador com o momento de um evento de risco, permissões de administrador permanentes ou temporárias para um ou mais recursos no Azure Active Directory, tais como um Administrador Global, administrador de faturação, administrador de serviço, o administrador de utilizador e palavra-passe de administrador. 

### <a name="real-time"></a>Em tempo real
Consulte a deteção em tempo real.

### <a name="real-time-detection"></a>Deteção em tempo real
A deteção de anomalias e avaliação de risco de um evento, tais como a tentativa de início de sessão antes do evento são autorizados a continuar.

### <a name="remediated-risk-event"></a>Corrigido (evento de risco)
Um Estado de eventos de risco definido automaticamente pelo Identity Protection, que indica que o evento de risco foi remediado utilizando a ação de remediação padrão para este tipo de eventos de risco. Por exemplo, quando a palavra-passe do utilizador é reposta, muitos eventos de risco que indicam que a palavra-passe anterior foi comprometida são remediados automaticamente.

### <a name="remediation"></a>Remediação
Uma ação para proteger uma identidade ou um dispositivo que anteriormente suspeito ou conhecido for comprometida. Uma ação de remediação restaura a identidade ou o dispositivo para um Estado de funcionamento seguro e resolve os eventos de risco anteriores associados o identidade ou o dispositivo.

### <a name="resolved-risk-event"></a>Resolvido (evento de risco)
Estado de eventos de risco definir manualmente por um utilizador Identity Protection, que indica que o utilizador demorou uma ação de remediação adequado fora da proteção de identidade e de que o evento de risco deve ser considerado fechado.

### <a name="risk-event-status"></a>Estado do evento de risco
Uma propriedade de um evento de risco, que indica se o evento está ativo e se fechados, o motivo para fechando-o.

### <a name="risk-event-type"></a>Tipo de evento de risco
Uma categoria para o evento de risco, que indica o tipo de anomalias que causou o evento em risco.

### <a name="risk-level-risk-event"></a>Nível de risco (evento de risco)
Uma indicação (alta, média ou baixa) da gravidade do evento de risco para ajudar os utilizadores Identity Protection atribuir prioridades as ações tomar para reduzir o risco para a respetiva organização. 

### <a name="risk-level-sign-in"></a>Nível de risco (início de sessão)
Uma indicação (alta, média ou baixa) da probabilidade de que para um específico início de sessão, alguém está a tentar utilizar a identidade do utilizador.

### <a name="risk-level-user-compromise"></a>Nível de risco (compromisso do utilizador)
Uma indicação (alta, média ou baixa) da probabilidade de uma identidade foi comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (alta, média ou baixa) de gravidade da vulnerabilidade para ajudar os utilizadores Identity Protection atribuir prioridades as ações tomar para reduzir o risco para a respetiva organização.

### <a name="secure-identity"></a>Proteger (identidade)
Execute as ações de remediação, como uma alteração de palavra-passe ou máquina reprocessamento de imagem para restaurar uma identidade potencialmente comprometida para um Estado de funcionamento não comprometido.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras de política e a condição. Uma política pode ser aplicada a entidades, tais como os utilizadores, grupos, aplicações, dispositivos, plataformas de dispositivos, Estados do dispositivo, intervalos de IP e tipos de clientes Auth2.0. Quando uma política é ativada, esta é avaliada sempre que uma entidade incluída na política é emitida um token para um recurso.

### <a name="sign-in-v"></a>Iniciar sessão (v)
Para se autenticar uma identidade no Azure Active Directory.

### <a name="sign-in-n"></a>Início de sessão (n)
O processo ou a ação de autenticação de uma identidade no Azure Active Directory e o evento que captura esta operação.

### <a name="sign-in-from-anonymous-ip-address"></a>Início de sessão do endereço IP anónimo
Acionado um evento de risco após um bem-sucedida início de sessão do endereço IP que foi identificado como um endereço IP de proxy anónimo.

### <a name="sign-in-from-infected-device"></a>Início de sessão a partir de dispositivos infetados
Um evento de risco acionado quando um início de sessão tem origem num endereço de IP que é conhecido para ser utilizado por um ou mais dispositivos comprometidos, que são ativamente a tentar comunicar com um servidor bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Início de sessão do endereço IP com atividade suspeita
Acionado um evento de risco após um bem-sucedida início de sessão de um IP endereços com um número elevado de tentativas de início de sessão em várias contas de utilizador num curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Início de sessão da localização familiarizada
Um evento de risco acionado quando um utilizador com êxito inicia sessão a partir de uma localização nova (IP, Latitude/Longitude e ASN).

### <a name="sign-in-risk"></a>Risco de início de sessão
Consulte o risco ao nível (início de sessão)

### <a name="sign-in-risk-policy"></a>Política de início de sessão risco
Uma política de acesso condicional que avalia o risco um específico início de sessão e aplica-se mitigações com base nas condições predefinidas e as regras.

### <a name="user-compromise-risk"></a>Risco de compromisso do utilizador
Consulte o risco ao nível (compromisso do utilizador)

### <a name="user-risk"></a>Risco de utilizador
Consulte o risco ao nível (compromisso do utilizador).

### <a name="user-risk-policy"></a>Política de risco do utilizador
Uma política de acesso condicional que considera o início de sessão e aplica-se mitigações com base nas condições predefinidas e as regras.

### <a name="users-flagged-for-risk"></a>Utilizadores sinalizados para risco
Utilizadores que têm de eventos de risco que são remediados ou Active Directory

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou a condição no Azure Active Directory que torna o diretório suscetível a exploits ou ameaças.

## <a name="see-also"></a>Consultar também
* [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)

