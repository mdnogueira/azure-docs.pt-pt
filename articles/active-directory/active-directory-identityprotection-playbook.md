---
title: "Azure Active Directory Identity Protection manual de comunicação social | Microsoft Docs"
description: Saiba como o Azure AD Identity Protection lhe permite limitar a capacidade de um atacante para explora um dispositivo ou identidade comprometida e para proteger uma identidade ou um dispositivo que anteriormente era suspeito ou conhecido for comprometida.
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, o cloud app discovery, gestão de aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5479981ed4c017e6953ef616588ca0e4060ce3b2
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection manual de comunicação social
Este manual de comunicação social ajuda-o a:

* Preencher os dados no ambiente de proteção de identidade, simulando eventos de risco e vulnerabilidades
* Configurar políticas de acesso condicional baseado em risco e testar o impacto destas políticas

## <a name="simulating-risk-events"></a>Eventos de risco simulando
Esta secção fornece os passos para simulando os seguintes tipos de eventos de risco:

* Inícios de sessão de endereços IP anónimos (simples)
* Inícios de sessão a partir de localizações desconhecidas (Moderada)
* Deslocação impossível para localizações atípicas (difícil)

Não não possível simulated outros eventos de risco de uma forma segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos
Este tipo de eventos de risco identifica os utilizadores que têm sessão com êxito de um endereço IP que foi identificado como um endereço IP de proxy anónimo. Estes proxies são utilizados pelas pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para intenções maliciosas.

**Para simular um início de sessão de um IP anónimo, execute os seguintes passos**:

1. Transferir o [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en).
2. Utilizando o Tor Browser, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Introduza as credenciais da conta que pretende ver apresentado no **inícios de sessão de endereços IP anónimos** relatório.

O início de sessão irá aparecer no dashboard do Identity Protection dentro de 5 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inícios de sessão a partir de localizações desconhecidas
O risco de localizações desconhecidas é um mecanismo de avaliação de início de sessão em tempo real que considera decorridos desde o início de sessão localizações (IP, Latitude / Longitude e ASN) para determinar as localizações de nova / familiarizadas. O sistema armazena IPs anterior, Latitude / Longitude e ASNs de um utilizador e considera que estas sejam familiares localizações. Uma localização de início de sessão é considerada familiarizada se a localização de início de sessão não corresponde a qualquer das localizações familiares existentes.

Proteção de identidade do Azure Active Directory:  

* tem um período de aprendizagem inicial de 14 dias durante os quais não sinalizador quaisquer novas localizações como localizações desconhecidas.
* ignora os inícios de sessão de dispositivos familiares e localizações geograficamente próximo de uma localização familiar existente.

Para simular localizações desconhecidas, tem de iniciar sessão a partir de uma localização e dispositivo que a conta não tiver assinado do antes. 

**Para simular um início de sessão a partir de uma localização familiarizado, execute os seguintes passos**:

1. Escolha uma conta que tenha, pelo menos, um histórico de início de sessão 14 dias. 
2. Efetue um:
   
   a. Ao utilizar uma VPN, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com) e introduza as credenciais da conta que pretende simular o evento de risco para.
   
   b. Peça um associar numa localização diferente para iniciar sessão com as credenciais da conta (não recomendadas).

O início de sessão irá aparecer no dashboard do Identity Protection dentro de 5 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Impossível atípica localização
Simulando a condição de deslocação impossível é difícil, porque o algoritmo utiliza o machine learning para weed saída falso-positivos como impossível dos dispositivos familiares ou inícios de sessão de VPNs que são utilizados por outros utilizadores no diretório. Além disso, o algoritmo necessita de um histórico de início de sessão de 3 para 14 dias para o utilizador antes de iniciar gerar eventos de risco.

**Para simular um deslocação impossível para localização atípica, execute os seguintes passos**:

1. Utilizando o seu browser padrão, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Introduza as credenciais da conta que pretende gerar um evento de risco deslocação impossível para.
3. Altere o agente de utilizador. Pode alterar agente de utilizador no Internet Explorer a partir de ferramentas de programador ou alterar o agente de utilizador no Firefox ou Chrome utilizando um suplemento de alternador do agente de utilizador.
4. Altere o seu endereço IP. Pode alterar o seu endereço IP utilizando uma VPN, um suplemento de Tor ou girar configurar uma nova máquina no Azure num centro de dados diferentes.
5. Início de sessão para [https://myapps.microsoft.com](https://myapps.microsoft.com) com as mesmas credenciais como antes e dentro de alguns minutos após o anterior início de sessão.

O início de sessão irá aparecer no dashboard do Identity Protection dentro de 2 a 4 horas.<br>
Devido a complexos de machine learning modelos envolvidas, há a possibilidade de que não irá obter selecionado cópias de segurança.<br> Pode querer replicar estes passos para várias contas do Azure AD.

## <a name="simulating-vulnerabilities"></a>Simulando vulnerabilidades
Vulnerabilidades são fragilidades num ambiente do Azure AD que podem ser forem exploradas por um ator incorretos. Atualmente 3 tipos de vulnerabilidades estão anexados no Azure AD Identity Protection que tiram partido de outras funcionalidades do Azure AD. Estas vulnerabilidades serão apresentadas no dashboard do Identity Protection automaticamente depois destas funcionalidades estão configuradas.

* Azure AD [multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>Risco de compromisso do utilizador
**Para testar o risco de compromisso do utilizador, execute os seguintes passos**:

1. Início de sessão para [https://portal.azure.com](https://portal.azure.com) com credenciais de administrador global para o seu inquilino.
2. Navegue para **identidade proteção**. 
3. No principal **do Azure AD Identity Protection** painel, clique em **definições**. 
4. No **as definições do Portal** painel, em **regras de segurança**, clique em **risco de compromisso do utilizador**. 
5. No **inicie sessão em risco** painel, ative **ativar regra** desativado e, em seguida, clique em **guardar** definições.
6. Para uma determinada conta de utilizador, simular um familiarizado localizações ou eventos de risco IP anónimo. Isto irá elevar o nível de risco de utilizador para esse utilizador **média**.
7. Aguarde alguns minutos e, em seguida, certifique-se de que nível de utilizador para o utilizador é **média**.
8. Vá para o **as definições do Portal** painel.
9. No **risco de compromisso do utilizador** painel, em **ativar regra**, selecione **no** . 
10. Selecione uma das seguintes opções:
    
    a. Para o bloco, selecione **média** em **bloco sessão**.
    
    b. Para impor a alteração de palavra-passe segura, selecione **média** em **requer autenticação multifator**.
11. Clique em **Guardar**.
12. Pode agora testar o acesso condicional baseado em risco inscrevendo-se na utilização de um utilizador com um nível de risco elevada. Se o risco de utilizador é médio, dependendo da configuração da sua política, o início de sessão está a ser bloqueado ou é forçadas para alterar a palavra-passe. 
    <br><br>
    ![Manual de comunicação social](./media/active-directory-identityprotection-playbook/201.png "manual de comunicação social")
    <br>

## <a name="sign-in-risk"></a>Risco de início de sessão
**Para testar um início de sessão em risco, execute os seguintes passos:**

1. Início de sessão para [https://portal.azure.com ](https://portal.azure.com) com credenciais de administrador global para o seu inquilino.
2. Navegue para **identidade proteção**.
3. No principal **do Azure AD Identity Protection** painel, clique em **definições**. 
4. No **as definições do Portal** painel, em **regras de segurança**, clique em **inicie sessão em risco**.
5. No * * a iniciar sessão risco * * painel, selecione **no** em **ativar regra**. 
6. Selecione uma das seguintes opções:
   
   a. Para bloquear, selecione **média** em **bloco iniciar sessão**
   
   b. Para impor a alteração de palavra-passe segura, selecione **média** em **requer autenticação multifator**.
7. Para bloquear, selecione média em blocos de início de sessão.
8. Para impor a autenticação multifator, selecione **média** em **requer autenticação multifator**.
9. Clique em **Guardar**.
10. Pode agora testar o acesso condicional baseado em risco simulando de localizações desconhecidas ou eventos de risco IP anónimo, porque são ambos **média** eventos de risco.


![Manual de comunicação social](./media/active-directory-identityprotection-playbook/200.png "manual de comunicação social")


## <a name="see-also"></a>Consultar também
* [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)

