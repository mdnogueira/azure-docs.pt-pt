---
title: Eventos de risco do Azure Active Directory | Microsoft Docs
description: "Este tópico fornece uma descrição detalhada dos eventos de risco."
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, a segurança, a risco, a nível de risco, a vulnerabilidade e a política de segurança"
author: MarkusVi
manager: femila
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a454199137f8ccc99ddbef66758fd1cabd8fd486
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-active-directory-risk-events"></a>Eventos de risco do Azure Active Directory

A grande maioria das falhas de segurança ocorrer quando os atacantes ganham acesso a um ambiente roubo de identidade do utilizador. Detetar identidades comprometidas não é nenhuma tarefa fácil. Azure Active Directory utiliza adaptável algoritmos de aprendizagem automática e heurística para detetar as ações suspeitas que estão relacionados com as contas de utilizador. Cada detetado ação suspeita está armazenada num registo chamado *eventos de risco*.

Atualmente, o Azure Active Directory Deteta seis tipos de eventos de risco:

- [Utilizadores com credenciais obtidas ilicitamente](#leaked-credentials) 
- [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) 
- [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) 
- [Inícios de sessão a partir de dispositivos infetados](#sign-ins-from-infected-devices) 
- [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inícios de sessão a partir de localizações desconhecidas](#sign-in-from-unfamiliar-locations) (exclusiva **do Azure Active Directory Premium P2** edições)

![Eventos de risco](./media/active-directory-reporting-risk-events/91.png)

Este tópico fornece-lhe uma descrição detalhada do que eventos de risco são e como pode utilizá-las para proteger as identidades do Azure AD.


## <a name="risk-event-types"></a>Tipos de eventos de risco

A propriedade de tipo de eventos de risco é que um identificador para a ação suspeita um registo de eventos de risco tiver sido criado para.  
Levar os investimentos de contínuos da Microsoft para o processo de deteção para:

- Melhoramentos para a precisão da deteção de eventos de risco existente 
- Novos tipos de eventos de risco que serão adicionados no futuro

### <a name="leaked-credentials"></a>Credenciais obtidas ilicitamente

Quando cybercriminals comprometimentos palavras-passe válidas de utilizadores legítimos, os criminals, muitas vezes, partilham essas credenciais. Normalmente, isto é feito ao publicá-los publicamente nos sites web ou de colar escuro ou por comerciais ou vender as credenciais no mercado negro. A Microsoft podem fugir credenciais serviço adquire o nome de utilizador / palavra-passe pares através da monitorização de web sites público e um escuro e trabalhar com:

- Investigadores
- Aplicação da lei
- Equipas de segurança na Microsoft
- Outras fontes fidedignas 

Quando o serviço adquire nome de utilizador / pares de palavra-passe, são verificados relativamente credenciais válidas de atual dos utilizadores do AAD. Quando é encontrada uma correspondência, significa que a palavra-passe de um utilizador tiver sido comprometido e um *fuga eventos de risco de credenciais* é criado.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

Este tipo de eventos de risco identifica os utilizadores que têm sessão com êxito de um endereço IP que foi identificado como um endereço IP de proxy anónimo. Estes proxies são utilizados pelas pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para intenções maliciosas.


### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Este tipo de eventos de risco identifica dois inícios de sessão com origem a partir de localizações geograficamente distantes, em que, pelo menos, uma das localizações pode também ser atípica para o utilizador, fornecido passado comportamento. Além disso, o tempo entre dois inícios de sessão é mais curto do que o tempo que demorariam viajam de primeira localização para o segundo, que indica que outro utilizador está a utilizar as mesmas credenciais ao utilizador. 

Este algoritmo do machine learning que ignora óbvios "*falsos positivos*" contribuir para a condição de deslocação impossível, tais como as VPNs e localizações regularmente utilizadas por outros utilizadores na organização.  O sistema tem um período de aprendizagem inicial de 14 dias durante os quais este aprende o comportamento de início de sessão de um novo utilizador.

### <a name="sign-in-from-unfamiliar-locations"></a>Início de sessão a partir de localizações desconhecidas

Este tipo de eventos de risco considera decorridos desde o início de sessão localizações (IP, Latitude / Longitude e ASN) para determinar as localizações de nova / familiarizadas. O sistema armazena informações sobre localizações anteriores utilizados por um utilizador e considera estas localizações "familiares". O evento de risco é acionado quando o início de sessão ocorre a partir de uma localização que já não se encontra na lista de localizações familiares. O sistema tem um período de aprendizagem inicial de 30 dias, durante os quais não sinalizador quaisquer novas localizações como localizações desconhecidas. O sistema ignora também inícios de sessão de dispositivos familiares e localizações geograficamente próximo de uma localização familiar. 

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão a partir de dispositivos infetados

Este tipo de eventos de risco identifica inícios de sessão de dispositivos infetados com software maligno, que são reconhecidos ativamente comunicar com um servidor bot. Isto é determinado através da correlação entre os endereços IP do dispositivo do utilizador em relação a endereços IP que contactaram um servidor bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita
Este tipo de eventos de risco identifica os endereços IP do que um número elevado de tentativas de início de sessão falhadas foram visto, através de várias contas de utilizador, num curto período de tempo. Isto corresponde a padrões de tráfego de endereços IP utilizados pelos atacantes e é um indicador forte que contas são já ou estão prestes a ficar comprometida. Este é um algoritmo do machine learning que ignora óbvios "*falso-positivos*", tais como endereços IP que são regularmente utilizadas por outros utilizadores na organização.  O sistema tem um período de aprendizagem inicial de 14 dias onde-aprende o comportamento de início de sessão de um novo utilizador e um novo inquilino.


## <a name="detection-type"></a>Tipo de deteção

A propriedade de tipo de deteção é um indicador (em tempo real ou Offline) para o período de tempo da deteção de um evento de risco.  
Atualmente, a maioria dos eventos de risco são detetados offline numa operação de pós-processamento após o evento de risco.

A tabela seguinte apresenta a quantidade de tempo que demora para um tipo de deteção apresentar um relatório relacionados:

| Tipo de deteção | Relatório de latência |
| --- | --- |
| Em tempo real | minutos de 5 a 10 |
| Offline | 2 a 4 horas |


Para os tipos de eventos de risco que do Azure Active Directory Deteta, os tipos de deteção são:

| Tipo de evento de risco | Tipo de deteção |
| :-- | --- | 
| [Utilizadores com credenciais obtidas ilicitamente](#leaked-credentials) | Offline |
| [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) | Em tempo real |
| [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) | Offline |
| [Inícios de sessão a partir de localizações desconhecidas](#sign-in-from-unfamiliar-locations) | Em tempo real |
| [Inícios de sessão a partir de dispositivos infetados](#sign-ins-from-infected-devices) | Offline |
| [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Nível de risco

A propriedade de nível de risco de um evento de risco é um indicador (alta, média ou baixa) para a gravidade e a confiança de um evento de risco. Esta propriedade ajuda-o a atribuir prioridades as ações que deve tomar. 

A gravidade do evento de risco representa a força do sinal, como uma previsão da receita de compromisso de identidade.  
A confiança é um indicador para a possibilidade de falsos positivos. 

Por exemplo, 

* **Elevada**: elevada confiança e eventos de risco de elevada gravidade. Estes eventos são indicadores fortes que a identidade do utilizador foi comprometida e as contas de utilizador afetadas devem ser resolvidas imediatamente.

* **Média**: gravidade elevada, mas os eventos de risco de confiança inferior, ou vice versa. Estes eventos são potencialmente duvidosos e as contas de utilizador afetadas devem ser resolvidas.

* **Baixa**: baixa confiança e eventos de risco de baixa gravidade. Este evento não pode necessitar de uma ação imediata, mas quando combinado com outros eventos de risco, pode fornecer uma forte indicação de que a identidade for comprometida.

![Nível de risco](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>Credenciais obtidas ilicitamente

Fuga credenciais eventos de risco estão classificados como um **elevada**, porque fornecem uma indicação clara que o nome de utilizador e palavra-passe estão disponíveis para um atacante.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

O nível de risco para este tipo de eventos de risco é **média** porque um endereço IP anónimo não é uma indicação de um compromisso de conta segura.  
Recomendamos que contacte o utilizador para verificar se estava a utilizar endereços IP anónimos imediatamente.


### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Impossível é normalmente um bom indicador de que um hacker conseguiu com êxito início de sessão. No entanto, FALSO-positivos podem ocorrer quando um utilizador viaja utilizando um novo dispositivo ou uma VPN que não é normalmente utilizada por outros utilizadores na organização. Outra origem de Falso-positivos é aplicações incorretamente passaram servidor IPs como cliente IPs, que pode dar o aspecto de inícios de sessão está alojado ocorridos a partir do Centro de dados em que essa aplicação do back-end (muitas vezes, estes são os datacenters da Microsoft, que Pode dar o aspecto de inícios de sessão a decorrer da Microsoft pertencentes à empresa endereços IP). Como resultado destas falso-positivos, o nível de risco para este evento de risco é **média**.

> [!TIP]
> Pode reduzir a quantidade de Falso-positves comunicado para este tipo de eventos de risco configurando [denominado localizações](active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Início de sessão a partir de localizações desconhecidas

Localizações desconhecidas podem fornecer uma forte indicação de que um atacante é capaz de utilizar uma identidade roubada. FALSO-positivos podem ocorrer quando um utilizador viaja, está a tentar terminar um novo dispositivo ou está a utilizar uma VPN de novo. Como resultado destas falsos positivos, o nível de risco para este tipo de evento é **média**.

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão a partir de dispositivos infetados

Este evento de risco identifica os endereços IP, não os dispositivos de utilizador. Se vários dispositivos estejam atrás de um único endereço IP e apenas alguns são controladas pela rede bot, inícios de sessão de outros dispositivos meu acionador este evento desnecessariamente, que é o motivo para classificar este evento de risco como **baixa**.  

Recomendamos que contacte o utilizador e analisar todos os dispositivos do utilizador. Também é possível que o dispositivo pessoal de um utilizador está infetado ou conforme mencionado anteriormente, que alguém utilizou um dispositivo infetado do mesmo endereço IP que o utilizador. Dispositivos infetados frequentemente infetados por software maligno que ainda não tenham sido identificadas pelo software de antivírus e também pode indicar como hábitos de utilizador incorreto, que poderão ter causado o dispositivo ficar infetado.

Para obter mais informações sobre como infeções de software maligno de endereço, consulte o [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita

Recomendamos que contacte o utilizador para verificar se são, na verdade, iniciou sessão de um endereço IP que foi marcado como suspeito. O nível de risco para este tipo de evento é "**média**" porque vários dispositivos podem estar protegido pelo mesmo endereço IP, enquanto que apenas algumas podem ser responsáveis pela atividade suspeita. 


 
## <a name="next-steps"></a>Passos seguintes

Eventos de risco são foundation para proteger as identidades do seu Azure AD. Do Azure AD consegue atualmente detetar eventos de risco seis: 


| Tipo de evento de risco | Nível de risco | Tipo de deteção |
| :-- | --- | --- |
| [Utilizadores com credenciais obtidas ilicitamente](#leaked-credentials) | Elevado | Offline |
| [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) | Médio | Em tempo real |
| [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) | Médio | Offline |
| [Inícios de sessão a partir de localizações desconhecidas](#sign-in-from-unfamiliar-locations) | Médio | Em tempo real |
| [Inícios de sessão a partir de dispositivos infetados](#sign-ins-from-infected-devices) | Baixo | Offline |
| [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Médio | Offline|

Onde podem encontrar os eventos de risco que foram detetados no seu ambiente?
Existem dois locais onde rever eventos de risco comunicado:

 - **Relatórios do Azure AD** -eventos de risco fazem parte de segurança do Azure AD de relatórios. Para obter mais detalhes, consulte o [os utilizadores no relatório de segurança de risco](active-directory-reporting-security-user-at-risk.md) e [relatório de segurança de inícios de sessão arriscados](active-directory-reporting-security-risky-sign-ins.md).

 - **Azure AD Identity Protection** -eventos de risco são também fazem parte da [do Azure Active Directory Identity Protection](active-directory-identityprotection.md) capacidades de relatórios.
    

Enquanto a deteção de eventos de risco já representa um aspeto importante de proteger as identidades, também tem a opção para resolvê-los o manualmente ou implementar mesmo respostas automáticas através da configuração de políticas de acesso condicional. Para obter mais detalhes, consulte o artigo de [do Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 
