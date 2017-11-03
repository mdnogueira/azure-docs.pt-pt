---
title: "Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016 | Microsoft Docs"
description: "Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 51962c14a3c32bbfa9a613fa203cc48cfea50c0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016 

Este tópico aplica-se aos seguintes clientes:

-   Windows 10
-   Windows Server 2016

Para outros clientes do Windows, consulte [híbrida de resolução de problemas do Azure Active Directory associados a um nível baixo dispositivos](device-management-troubleshoot-hybrid-join-windows-legacy.md).

Este tópico pressupõe que tem [dispositivos associados ao configurado híbrida do Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) para suportar os cenários seguintes:

- Acesso condicional baseado no dispositivo

- [Enterprise roaming das definições](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello para empresas](active-directory-azureadjoin-passport-deployment.md)


Este documento fornece orientações de resolução de problemas sobre como resolver problemas potenciais. 


Para Windows 10 e Windows Server 2016, híbrida associação do Azure Active Directory suporta o Windows 10 de Novembro de 2015, atualizar e superior. Recomendamos que utilize a atualização Anniversary.

## <a name="step-1-retrieve-the-join-status"></a>Passo 1: Obter o estado de associação 

**Para obter o estado de associação:**

1. Abra a linha de comandos como administrador

2. Tipo **dsregcmd /status**



    +----------------------------------------------------------------------+
   | Estado do dispositivo |+----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Nenhum DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: TpmProtected de fornecedor do Microsoft Platform Crypto: Sim KeySignTest:: tem de executar elevados para testar.
                  IDP: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ = = JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: Sim DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
   | Estado do utilizador |+----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizações WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} AzureAdPrt (AzureAd): Sim



## <a name="step-2-evaluate-the-join-status"></a>Passo 2: Avaliar o estado de associação 

Reveja os seguintes campos e certifique-se de que têm os valores esperados:

### <a name="azureadjoined--yes"></a>AzureAdJoined: Sim  

Este campo indica se o dispositivo é associado com o Azure AD. Se o valor for **não**, a associação ao Azure AD ainda não foi concluída. 

**Causas possíveis:**

- Falha na autenticação do computador para uma associação.

- Há um proxy HTTP na organização que não pode ser detetada pelo computador

- O computador não consegue contactar o Azure AD para autenticar ou DRS do Azure para o registo

- O computador não pode estar na rede interna da organização ou VPN com linha direta de visão, para um local controlador de domínio do AD.

- Se o computador tiver um TPM, pode estar num Estado incorreto.

- Poderá haver uma configuração incorreta nos serviços de anotou no documento anteriormente que será necessário verificar novamente. Exemplos comuns são:

    - O servidor de Federação não tem pontos finais de WS-Trust ativados

    - O servidor de Federação não permite a autenticação de entrada de computadores na sua rede através da autenticação integrada do Windows.

    - Não há nenhum objeto de ponto de ligação de serviço que aponta para o nome de domínio verificado no Azure AD na floresta do AD em que o computador pertence ao

---

### <a name="domainjoined--yes"></a>DomainJoined: Sim  

Este campo indica se o dispositivo estiver associado a um Active Directory no local ou não. Se o valor for **não**, o dispositivo não é possível efetuar uma associação do Azure AD híbrido.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: não  

Este campo indica se o dispositivo está registado no Azure AD como um dispositivo pessoal (marcado como *associado à área de trabalho*). Este valor deve ser **não** para um computador associado a um domínio que seja também associados ao Azure AD híbrido. Se o valor for **Sim**, foi adicionada uma conta escolar ou profissional antes da conclusão da associação a um Azure AD híbrido. Neste caso, a conta é ignorada quando utilizar a versão de atualização de Anniversary do Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Sim e AzureADPrt: Sim
  
Estes campos indicam se o utilizador tem autenticadas com êxito para o Azure AD quando iniciar sessão no dispositivo. Se os valores forem **não**, poderia ser devida:

- Chave de armazenamento incorreto (STK) no TPM associado ao dispositivo no registo (verificação KeySignTest ao ser executado elevado).

- ID de início de sessão alternativo

- Proxy de HTTP não encontrado

## <a name="next-steps"></a>Passos seguintes

Para perguntas, consulte o [perguntas mais frequentes sobre a gestão de dispositivos](device-management-faq.md) 