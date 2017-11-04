---
title: "Perguntas mais frequentes - serviços de domínio do Active Directory do Azure | Microsoft Docs"
description: "Perguntas mais frequentes sobre serviços de domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 2705e97bd5b259ef68090e5688df57e0d3478f30
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Serviços de domínio do Azure Active Directory: Perguntas mais frequentes (FAQ)
Esta página respostas a perguntas mais frequentes sobre o Azure Active Directory Domain Services. Manter a verificação de volta para atualizações.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Consulte a nossa [guia de resolução de problemas](active-directory-ds-troubleshooting.md) para soluções para problemas comuns encontrados quando configurar ou administração dos serviços de domínio do Azure AD.

### <a name="configuration"></a>Configuração
#### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios geridos para um único diretório do Azure AD?
Não. Só pode criar um único domínio gerido servido por serviços de domínio do Azure AD para um único diretório do Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Pode ativar os serviços de domínio do Azure AD numa rede virtual do Azure Resource Manager?
Sim. Serviços de domínio do Azure AD pode ser ativados com uma rede virtual do Azure Resource Manager. Esta funcionalidade está atualmente em pré-visualização.

#### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar o meu domínio gerido existente de uma rede virtual clássica a uma rede virtual do Gestor de recursos?
Atualmente não. Iremos irá fornecer um mecanismo para migrar o seu domínio gerido existente de uma rede virtual clássica para uma rede virtual do Gestor de recursos no futuro.

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Pode ativar os serviços de domínio do Azure AD uma subscrição do Azure CSP (fornecedor de solução em nuvem)?
Não. Estamos a trabalhar para adicionar suporte para subscrições de CSP.

#### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-use-adfs-to-authenticate-users-for-access-to-office-365-and-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Posso ativar os serviços de domínio do Azure AD no Azure federado diretório AD? Posso utilizar o AD FS para autenticar os utilizadores para acesso ao Office 365 e não a sincronizar os hashes de palavra-passe para o Azure AD. Pode ativar os serviços de domínio do Azure AD para este diretório?
Não. Serviços de domínio do Azure AD tem acesso para os hashes de palavra-passe de contas de utilizador, para autenticar os utilizadores através de NTLM ou Kerberos. Num diretório federado, os hashes de palavra-passe não são armazenados no diretório do Azure AD. Por conseguinte, os serviços de domínio do Azure AD não funciona com essas diretórios do Azure AD.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Pode posso disponibilizar os serviços de domínio do Azure AD em várias redes virtuais na minha subscrição?
O próprio serviço não suporta diretamente neste cenário. Seu domínio gerido está disponível na rede virtual apenas uma de cada vez. No entanto, pode configurar a conectividade entre várias redes virtuais para expor os serviços de domínio do Azure AD para outras redes virtuais. Veja como pode [ligar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Pode ativar os serviços de domínio do Azure AD através do PowerShell?
Implementação automatizada/PowerShell dos serviços de domínio do Azure AD não está disponível atualmente.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Serviços de domínio do Azure AD está disponível no portal do Azure?
Sim. Serviços de domínio do Azure AD podem ser configurados utilizando a [portal do Azure](https://portal.azure.com). O [portal clássico do Azure](https://manage.windowsazure.com) já não é suportada.

#### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Pode ativar os serviços de domínio do Azure AD com um modelo do Resource Manager?
Não. Algumas tarefas têm de ser executada como parte da ativação do serviços de domínio do Azure AD. Estas tarefas não são possíveis através de um modelo do Resource Manager. Utilize o novo portal do Azure para ativar os serviços de domínio do Azure AD para o seu diretório.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Pode adicionar controladores de domínio a um domínio gerido dos serviços de domínio do Azure AD?
Não. O domínio fornecido pelos serviços de domínio do Azure AD é um domínio gerido. Não é necessário aprovisionar, configurar ou gerir os controladores de domínio para este domínio - estas atividades de gestão são fornecidas como um serviço pela Microsoft. Por conseguinte, não é possível adicionar controladores de domínio adicional (leitura / escrita ou só de leitura) para o domínio gerido.

### <a name="administration-and-operations"></a>Administração e operações
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso ligar ao controlador de domínio para o meu domínio gerido utilizando o ambiente de trabalho remoto?
Não. Não tem permissões para ligar aos controladores de domínio para o domínio gerido através do ambiente de trabalho remoto. Os membros do grupo 'AAD DC administradores' podem administrar domínio gerido utilizando as ferramentas de administração do AD, como o Centro de administração do Active Directory (ADAC) ou do AD do PowerShell. Estas ferramentas são instaladas utilizando a funcionalidade 'Ferramentas de administração de servidor remoto' num servidor Windows associados a um domínio gerido.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Posso tiver ativado os serviços de domínio do Azure AD. Conta de utilizador que utilizar para as máquinas de associação de domínio para este domínio?
Os membros do grupo administrativo 'AAD DC administradores' podem máquinas de associação de domínio. Além disso, os membros deste grupo recebem acesso de ambiente de trabalho remoto a máquinas que foi associado ao domínio.

#### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Alguns têm privilégios de administrador de domínio para o domínio gerido fornecida pelos serviços de domínio do Azure AD?
Não. Não são concedidos privilégios administrativos no domínio gerido. Privilégios de administrador de domínio e o administrador de empresa não estão disponíveis para utilização dentro do domínio. Administrador de domínio existente ou grupos de administrador de empresa no seu diretório do Azure AD são também não concedidos privilégios de administrador empresarial/domínio no domínio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Pode modificar as associações de grupo utilizando outras ferramentas de administração do AD ou LDAP em domínios geridos?
Não. As associações de grupo não podem ser modificadas em domínios de manutenção pelos serviços de domínio do Azure AD. O mesmo se aplica a atributos de utilizador. No entanto pode alterar as associações de grupo ou os atributos de utilizador no Azure AD ou no seu domínio no local. Essas alterações são automaticamente sincronizadas nos serviços de domínio do Azure AD.

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora para que as alterações posso efetuar ao meu diretório do Azure AD para estar visível no meu domínio gerido?
Serão sincronizadas as alterações efetuadas no diretório do Azure AD utilizando a IU do Azure AD ou o PowerShell para o seu domínio gerido. Este processo de sincronização é executado em segundo plano. Depois de concluída a sincronização inicial uso do seu diretório, normalmente demora cerca de 20 minutos para as alterações efetuadas no Azure AD para serem refletidas no seu domínio gerido.

#### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Pode expandir o esquema do domínio gerido fornecido pelos serviços de domínio do Azure AD?
Não. O esquema é administrado pela Microsoft para o domínio gerido. Extensões de esquema não são suportadas pelos serviços de domínio do Azure AD.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Pode modificar ou adicionar registos DNS no meu domínio gerido?
Sim. Os membros do grupo 'AAD DC administradores' são concedidos privilégios de administrador de DNS, a modificar registos DNS no domínio gerido. Pode utilizarem a consola do Gestor de DNS num computador com o Windows Server associado ao domínio gerido, gerir o DNS. Para utilizar a consola do Gestor de DNS, instale 'Ferramentas do servidor DNS', que faz parte da funcionalidade 'Ferramentas de administração de servidor remoto' opcional no servidor. Obter mais informações sobre [utilitários para administrar, monitorização e resolução de problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) está disponível no TechNet.

### <a name="billing-and-availability"></a>Disponibilidade e de faturação
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>É que um serviço pago dos serviços de domínio do Azure AD?
Sim. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Existe uma versão de avaliação gratuita para o serviço?
Este serviço está incluído na versão de avaliação gratuita do Azure. Pode inscrever-se para obter um [avaliação de um mês do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Pode interromper um domínio gerido dos serviços de domínio do Azure AD? 
Não. Assim que tiver ativado um domínio gerido dos serviços de domínio do Azure AD, o serviço está disponível na sua rede virtual selecionada até que desativar/eliminar o domínio gerido. Não é possível colocar em pausa o serviço. Faturação continua numa base horária, até que elimine o domínio gerido.

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Pode obter os serviços de domínio do Azure AD como parte do Enterprise Mobility Suite (EMS)? É necessário do Azure AD Premium para utilizar os serviços de domínio do Azure AD?
Não. Serviços de domínio do AD do Azure é uma serviço do Azure pay as you go e não faz parte do EMS. Serviços de domínio do Azure AD pode ser utilizados com todas as edições do Azure AD (gratuita, básico e, Premium). São faturadas numa base horária, dependendo de utilização.

#### <a name="what-azure-regions-is-the-service-available-in"></a>As regiões do Azure está disponível no serviço?
Consulte o [serviços do Azure por região](https://azure.microsoft.com/regions/#services/) página para ver uma lista de regiões do Azure onde os serviços de domínio do Azure AD está disponível.
