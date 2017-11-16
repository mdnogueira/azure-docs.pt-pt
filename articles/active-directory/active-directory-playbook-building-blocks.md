---
title: "Azure Active Directory prova de conceito manual de comunicação social edifício blocos | Microsoft Docs"
description: "Explorar e implementar rapidamente a cenários de identidade e gestão de acesso"
services: active-directory
keywords: do Azure Active Directory, manual, uma prova de conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: 7e4af248a1aafbd34a62c75e792746514456577b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory prova do manual de comunicação social conceito: blocos modulares

## <a name="catalog-of-roles"></a>Catálogo de funções

| Função | Descrição | Prova de responsabilidade de conceito (PoC) |
| --- | --- | --- |
| **Arquitetura de identidade / equipa de desenvolvimento** | Este agrupamento é, normalmente, o estruturar a solução, implementa prototypes, unidades aprovações e, finalmente, entrega operações | Fornecem os ambientes e são aqueles avaliar os cenários diferentes da perspetiva de capacidade de gestão |
| **Equipa de operações de identidade no local** | Gere a identidade de diferentes origens no local: florestas do Active Directory, diretórios LDAP, sistemas de RH e fornecedores de identidade de Federação. | Fornecer acesso a locais recursos necessários para os cenários de PoC.<br/>Deve estar envolvidas ligeiramente possível|
| **Proprietários técnica da aplicação** | Proprietários técnicos das aplicações em nuvem diferente e serviços que integram com o Azure AD | Fornecem detalhes sobre aplicações SaaS (potencialmente instâncias de teste) |
| **Administrador Global do Azure AD** | Gere a configuração do Azure AD | Forneça credenciais para configurar o serviço de sincronização. Normalmente, o mesmo agrupamento como identidade arquitetura durante a PoC mas separe durante a fase de operações|
| **Equipa de base de dados** | Proprietários da infraestrutura de base de dados | Fornece acesso ao ambiente do SQL Server (AD FS ou do Azure AD Connect) para os preparativos cenário específico.<br/>Deve estar envolvidas ligeiramente possível |
| **Equipa de rede** | Proprietários da infraestrutura de rede | Fornecer o acesso necessário ao nível da rede para os servidores de sincronização para corretamente acesso as origens de dados e na cloud services (regras de firewall, portas abertas, regras de IPSec etc.) |
| **Equipa de segurança** | Define a estratégia de segurança, analisa os relatórios de segurança de várias origens e segue em findings. | Fornecer segurança destino cenários de avaliação |

## <a name="common-prerequisites-for-all-building-blocks"></a>Pré-requisitos comuns para todos os blocos modulares

Seguem-se alguns pré-requisitos necessários para qualquer POC com o Azure AD Premium.

| Pré-requisito | Recursos |
| --- | --- |
| Inquilino do Azure do AD definido com uma subscrição do Azure válida | [Como obter um inquilino do Azure Active Directory](active-directory-howto-tenant.md)<br/>**Nota:** se já tiver um ambiente com licenças do Azure AD Premium, pode obter uma subscrição de extremidade zero, navegando até https://aka.ms/accessaad <br/>Saiba mais em: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ e https://technet.microsoft.com/library/dn832618.aspx |
| Domínios definido e verificado | [Adicionar um nome de domínio personalizado ao Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Nota:** algumas cargas de trabalho, tais como o Power BI foi aprovisionou um inquilino do azure do AD nos bastidores. Para verificar se um determinado domínio está associado a um inquilino, navegue até à https://login.microsoftonline.com/ {domain}/v2.0/.well-known/openid-configuration. Poderá ser necessário se obter uma resposta com êxito, em seguida, o domínio já está atribuído a um inquilino e assumir o controlo. Se assim for, contacte a Microsoft para obter orientações adicionais. Saiba mais sobre as opções alimentar em: [que é a inscrição Self-Service do Azure?](active-directory-self-service-signup.md) |
| Azure AD Premium ou EMS ativado de avaliação | [Azure Active Directory Premium livre durante um mês](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Atribuiu o Azure AD Premium licenças ou EMS aos utilizadores de PoC | [Licença de si e aos utilizadores no Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Credenciais de Administrador Global do AD do Azure | [Atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Opcional mas vivamente recomendado: ambiente de laboratório paralelas como contingência | [Pré-requisitos do Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Diretório sincronização - sincronização de Hash de palavra-passe (PHS) - nova instalação

Hora para concluído aproximada: uma hora para menos de 1000 utilizadores de PoC

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| O servidor para executar o Azure AD Connect | [Pré-requisitos do Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Segmente utilizadores POC, no mesmo domínio e parte de um grupo de segurança e a UO | [Instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Funcionalidades do Azure AD Connect necessários para a POC são identificadas | [Ligar o Active Directory com o Azure Active Directory - configurar a sincronização de funcionalidades](./connect/active-directory-aadconnect.md#configure-sync-features) |
| É necessário ter credenciais no local e ambientes de nuvem  | [Azure AD Connect: contas e permissões](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Transferir a versão mais recente do Azure AD Connect | [Transferir o Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Instalar o Azure AD Connect com o caminho mais simples: Express <br/>1. Filtrar para o destino UO para minimizar o tempo de ciclo de sincronização<br/>2. Escolha o conjunto de destino de utilizadores no grupo local.<br/>3. Implementar as funcionalidades necessárias para os outros temas POC | [Azure AD Connect: Instalação personalizada: domínio e a UO filtragem](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Instalação personalizada: baseado em grupo filtragem](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrar as identidades no local com o Azure Active Directory: configurar funcionalidades de sincronização](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Abra do Azure AD Connect da IU e veja a execução perfis concluída (importar, sincronização e exportação) | [Sincronização do Azure AD Connect: Scheduler](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Abra o [portal de gestão do Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), aceda ao painel "Todos os utilizadores", adicione a coluna de "Origem da autoridade" e ver que os utilizadores são apresentados, corretamente marcado como feitos "Windows Server AD" | [Portal de gestão do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Considerações

1. Observe as considerações de segurança da sincronização de hash de palavra-passe [aqui](./connect/active-directory-aadconnectsync-implement-password-synchronization.md).  Se a sincronização de hash de palavra-passe para os utilizadores de produção piloto não definitively é uma opção, em seguida, considere as seguintes alternativas:
   * Crie utilizadores de teste no domínio de produção. Certifique-se de que não sincronizar a qualquer outra conta
   * Mover para um ambiente UAT
2.  Se pretender pursue Federação, é valer a pena para compreender que os custos associados uma solução federada com o fornecedor de identidade no local para além de POC e medida contra as vantagens que procura:
    * Está a ser o caminho de crítico para que tenha de design para elevada disponibilidade
    * É um serviço no local, que terá de plano de capacidade
    * É um serviço no local, que terá de monitor/manter/patch

Saiba mais: [identidade de compreender o Office 365 e o Azure Active Directory - identidade federada](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Imagem corporativa

Hora para concluído aproximada: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ativos (imagens, logótipos, etc.); Para melhor visualização confirme os recursos de tem os tamanhos recomendados. | [Adicionar a imagem corporativa à sua página de início de sessão no Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Opcional: Se o ambiente tiver um servidor do ADFS, o acesso ao servidor para personalizar o tema web | [AD FS utilizador início de sessão personalização](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Computador cliente para executar a experiência de início de sessão do utilizador final |  |
| Opcional: Experiência de dispositivos móveis para validar |  |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Aceda ao Portal de gestão do Azure AD | [Portal de gestão do Azure AD - imagem corporativa da empresa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Carregar os recursos para a página de início de sessão (logótipo heroína logótipo pequeno, etiquetas, etc.). Opcionalmente, se tiver o AD FS, alinha os elementos mesmos com páginas de início de sessão do AD FS | [Adicionar a imagem corporativa para o início de sessão e painel de acesso páginas: elementos personalizáveis](customize-branding.md) |
| Aguarde alguns minutos para que a alteração totalmente em vigor |  |
| Iniciar sessão com as credenciais de utilizador POC a https://myapps.microsoft.com |  |
| Confirme o aspeto e funcionalidade no browser | [Adicionar a imagem corporativa para o início de sessão e painel de acesso páginas](customize-branding.md) |
| Opcionalmente, confirme o aspeto e funcionalidade nos outros dispositivos |  |

### <a name="considerations"></a>Considerações

Se o aspeto e funcionalidade antigo permanecem após a personalização esvaziar a cache do cliente do browser e repita a operação.

## <a name="group-based-licensing"></a>Baseado em grupo de licenciamento

Hora para concluído aproximada: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Todos os utilizadores POC fazem parte de um grupo de segurança (na nuvem ou no local) | [Crie um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Aceda ao painel de licenças no Portal de gestão do Azure AD | [Portal de gestão do Azure AD: licenciamento](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Atribua licenças para o grupo de segurança com utilizadores POC. | [Atribuir licenças para um grupo de utilizadores no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Considerações

Em caso de problemas, aceda a [cenários, limitações e problemas conhecidos com utilizar grupos para gerir o licenciamento no Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Configuração de SSO federado SaaS

Hora para concluído aproximada: 60 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ambiente de teste da aplicação SaaS disponível. Neste guia, utilizamos ServiceNow como exemplo.<br/>Recomendamos vivamente que utilize uma instância de teste para minimizar friction no navegar qualidade de dados existente e mapeamentos. | Aceda a https://developer.servicenow.com/app.do#! / raiz para iniciar o processo de obtenção de uma instância de teste |
| Acesso de administrador para a consola de gestão do ServiceNow | [Tutorial: Integração do Azure Active Directory com o ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Destino definido de utilizadores para atribuir a aplicação. Recomenda-se um grupo de segurança que contenha os utilizadores de PoC. <br/>Se criar o grupo não for viável, em seguida, atribuir os utilizadores diretamente para a aplicação para a PoC | [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Partilhar o tutorial para todos os atores do Documentation Microsoft  | [Tutorial: Integração do Azure Active Directory com o ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Defina uma reunião de trabalho e siga os passos do tutorial com cada ator. | [Tutorial: Integração do Azure Active Directory com o ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Atribua a aplicação para o grupo identificado nos pré-requisitos. Se a POC tem o acesso condicional no âmbito, pode revê que mais tarde e adicionar MFA e semelhantes. <br/>Tenha em atenção de que isto irá iniciar o processo de aprovisionamento (se configurada) |  [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Crie um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Utilize a gestão de AD do Azure Portal para adicionar a aplicação de ServiceNow da Galeria| [Gestão do AD do Azure Portal: aplicações da empresa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Novidades na gestão de aplicações da empresa no Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| No painel "De sessão único-" da aplicação do ServiceNow ativar "baseados em SAML início de sessão" |  |
| Preencha os campos "Início de sessão no URL" e "Identificador" com o seu URL de ServiceNow<br/>Selecione a caixa para "Tornar novo certificado ativa"<br/>e guardar definições |  |
| Abra o painel de "Configurar ServiceNow" na parte inferior do painel para ver instruções personalizadas para configurar o ServiceNow |  |
| Siga as instruções para configurar o ServiceNow |  |
| No painel de "A aprovisionar" da aplicação do ServiceNow ativar o aprovisionamento "Automático" | [Gerir a conta de utilizador de aprovisionamento de aplicações da empresa no portal do Azure](active-directory-enterprise-apps-manage-provisioning.md) |
| Aguarde alguns minutos enquanto ter concluído o aprovisionamento.  Entretanto, pode verificar os relatórios de aprovisionamento |  |
| Inicie sessão no https://myapps.microsoft.com/ como um utilizador de teste que tem acesso | [O que é o painel de acesso?](active-directory-saas-access-panel-introduction.md) |
| Clique no mosaico para a aplicação que acabou de criar. Confirmar o acesso |  |
| Opcionalmente, pode verificar os relatórios de utilização da aplicação. Tenha em atenção que é alguma latência, por isso terá de aguardar algum tempo para ver o tráfego nos relatórios. | [Relatórios de atividade de início de sessão no portal do Azure Active Directory: a utilização das aplicações geridas](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Políticas de retenção de relatórios do Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considerações

1. Acima [Tutorial](active-directory-saas-servicenow-tutorial.md) refere-se ao Azure antigo experiência de gestão do AD. Mas PoC baseia-se no [início rápido](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away) experiência.
2. Se a aplicação de destino não está presente na galeria, em seguida, pode utilizar "Traga a sua própria aplicação". Saiba mais: [são as novidades na gestão de aplicações da empresa no Azure Active Directory: adicionar aplicações personalizadas a partir de um local](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Configuração de SSO de palavra-passe de SaaS

Hora para concluído aproximada: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ambiente de teste para aplicações SaaS. Um exemplo de SSO de palavra-passe é HipChat e o Twitter. Para qualquer outra aplicação, é necessário o URL exato da página com o formulário de início de sessão de html. | [Twitter no Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat no Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Contas para as aplicações de teste. | [Inscrever-se no Twitter](https://twitter.com/signup?lang=en)<br/>[Inscrever-se gratuitamente: HipChat](https://www.hipchat.com/sign_up) |
| Destino definido de utilizadores para atribuir a aplicação. Um grupo de segurança contidos os utilizadores é recomendada. | [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Acesso de administrador local para um computador para implementar a extensão do painel de acesso para o Internet Explorer, Chrome ou Firefox | [Extensão do painel de acesso de i/e](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão de painel de acesso para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão de painel de acesso para o Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Instale a extensão de browser | [Extensão do painel de acesso de i/e](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão de painel de acesso para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão de painel de acesso para o Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurar a aplicação a partir da Galeria | [Novidades na gestão de aplicações da empresa no Azure Active Directory: À Galeria de aplicações novas e melhoradas](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurar a palavra-passe SSO | [Gerir o início de sessão para aplicações da empresa no portal do Azure: início de sessão baseado em palavra-passe](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Atribuir a aplicação para o grupo identificado nos pré-requisitos | [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Inicie sessão no https://myapps.microsoft.com/ como um utilizador de teste que tem acesso |  |
| Clique no mosaico para a aplicação que acabou de criar. | [O que é o painel de acesso?: SSO baseada em palavra-passe sem aprovisionamento de identidade](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Forneça a credencial de aplicação | [O que é o painel de acesso?: SSO baseada em palavra-passe sem aprovisionamento de identidade](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Feche o browser e repita o início de sessão. Este tempo cerca o utilizador deve ver acesso totalmente integrado à aplicação. |  |
| Opcionalmente, pode verificar os relatórios de utilização da aplicação. Tenha em atenção que é alguma latência, por isso terá de aguardar algum tempo para ver o tráfego nos relatórios. | [Relatórios de atividade de início de sessão no portal do Azure Active Directory: a utilização das aplicações geridas](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Políticas de retenção de relatórios do Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considerações

Se a aplicação de destino não está presente na galeria, em seguida, pode utilizar "Traga a sua própria aplicação". Saiba mais: [são as novidades na gestão de aplicações da empresa no Azure Active Directory: adicionar aplicações personalizadas a partir de um local](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenha em consideração os seguintes requisitos:
   * Aplicação deve ter um URL de início de sessão conhecidos
   * A página de início de sessão deve conter um formulário HTML com um mais campos de texto que as extensões de browser poderá auto-preencher. No mínimo, esta deve conter o nome de utilizador e palavra-passe.

## <a name="saas-shared-accounts-configuration"></a>Configuração de contas de partilhada de SaaS

Hora para concluído aproximada: 30 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| A lista de aplicações de destino e os URLS exatos início de sessão antes de tempo. Por exemplo, pode utilizar o Twitter. | [Twitter no Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Inscrever-se no Twitter](https://twitter.com/signup?lang=en) |
| Partilhado credenciais para esta aplicação SaaS. | [Partilha de contas de utilizar o Azure AD](active-directory-sharing-accounts.md)<br/>[Azure AD automatizada palavra-passe roll a ativação pós-falha para o Facebook, Twitter e LinkedIn agora em pré-visualização! -Blogue Enterprise Mobility and Security] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Credenciais para, pelo menos, dois membros de equipa que serão a mesma conta de acesso. Têm de ser parte de um grupo de segurança. | [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Acesso de administrador local para um computador para implementar a extensão do painel de acesso para o Internet Explorer, Chrome ou Firefox | [Extensão do painel de acesso de i/e](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão de painel de acesso para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão de painel de acesso para o Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Instale a extensão de browser | [Extensão do painel de acesso de i/e](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão de painel de acesso para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão de painel de acesso para o Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurar a aplicação a partir da Galeria | [Novidades na gestão de aplicações da empresa no Azure Active Directory: À Galeria de aplicações novas e melhoradas](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurar a palavra-passe SSO | [Gerir o início de sessão para aplicações da empresa no portal do Azure: início de sessão baseado em palavra-passe](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Atribuir a aplicação para o grupo identificado nos pré-requisitos ao atribuir-lhes credenciais | [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Inicie sessão como utilizadores de diferentes nessa aplicação acesso como o **mesmo partilhado conta.**  |  |
| Opcionalmente, pode verificar os relatórios de utilização da aplicação. Tenha em atenção que é alguma latência, por isso terá de aguardar algum tempo para ver o tráfego nos relatórios. | [Relatórios de atividade de início de sessão no portal do Azure Active Directory: a utilização das aplicações geridas](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Políticas de retenção de relatórios do Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Considerações

Se a aplicação de destino não está presente na galeria, em seguida, pode utilizar "Traga a sua própria aplicação". Saiba mais: [são as novidades na gestão de aplicações da empresa no Azure Active Directory: adicionar aplicações personalizadas a partir de um local](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenha em consideração os seguintes requisitos:
   * Aplicação deve ter um URL de início de sessão conhecidos
   * A página de início de sessão deve conter um formulário HTML com um mais campos de texto que as extensões de browser poderá auto-preencher. No mínimo, esta deve conter o nome de utilizador e palavra-passe.

## <a name="app-proxy-configuration"></a>Configuração de Proxy de aplicações

Hora para concluído aproximada: 20 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Do Microsoft Azure AD basic ou subscrição premium e um diretório do Azure AD para o qual é um administrador global | [Edições do Azure Active Directory](active-directory-editions.md) |
| Uma aplicação web alojadas no local que pretende configurar para acesso remoto |  |
| Um servidor a executar o Windows Server 2012 R2 ou Windows 8.1 ou superior, em que pode instalar o conector do Proxy da aplicação | [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md) |
| Se existir uma firewall no caminho, certifique-se de que está aberta, para que o conector possa fazer pedidos HTTPS (TCP) para o Proxy de aplicações | [Ativar o Proxy da aplicação no portal do Azure: pré-requisitos do Proxy de aplicações](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Se a organização utilizar servidores proxy para estabelecer ligação à internet, faça uma vista de olhos a blogue após a trabalhar com servidores de proxy no local existentes para obter detalhes sobre como configurá-las | [Trabalhar com servidores de proxy no local existentes](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Instalar um conector no servidor | [Ativar o Proxy da aplicação no portal do Azure: instalar e registar o conector](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Publicar a aplicação no local no Azure AD como uma aplicação de Proxy de aplicações | [Publicar aplicações através do Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md) |
| Atribuir utilizadores de teste | [Publicar aplicações através do Proxy de aplicações do Azure AD: adicionar um utilizador de teste](application-proxy-publish-azure-portal.md#add-a-test-user) |
| Opcionalmente, configure uma experiência único início de sessão para os seus utilizadores | [Forneça o início de sessão único com o Proxy de aplicações do Azure AD](application-proxy-sso-azure-portal.md) |
| Testar a aplicação através do início de sessão portal de MyApps como utilizador atribuído | https://MyApps.microsoft.com |

### <a name="considerations"></a>Considerações

1. Embora sugerimos colocar o conector na sua rede empresarial, há casos quando o utilizador verá um melhor desempenho colocando-lo na nuvem. Saiba mais: [considerações sobre a topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory](application-proxy-network-topology-considerations.md)
2. Para obter mais detalhes de segurança e como esta opção fornece um acesso remoto seguro particularmente Consulte solução, mantendo apenas ligações de saída: [considerações de segurança para aceder remotamente a aplicações ao utilizar o Proxy de aplicações do Azure AD](application-proxy-security-considerations.md)

## <a name="generic-ldap-connector-configuration"></a>Configuração do conector LDAP genérico

Hora para concluído aproximada: 60 minutos

> [!IMPORTANT]
> Esta é uma configuração avançada que requerem familiarizado com o FIM/MIM. Se utilizada na produção, aconselhamos perguntas sobre esta configuração passar por [suporte Premier](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| O Azure AD Connect instalado e configurado | Bloco modular: [sincronização de diretórios - sincronização de Hash de palavra-passe](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Requisitos de reunião de instância ADLDS | [Referência técnica de conector LDAP genérica: Descrição geral do conector LDAP genérico](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Lista de cargas de trabalho, o que os utilizadores estão a utilizar e atributos associados estas cargas de trabalho | [Sincronização do Azure AD Connect: atributos sincronizados com o Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Adicione o conector LDAP genérico | [Referência técnica de conector LDAP genérica: criar um novo conector](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Criar perfis de execução para o conector criado (importação completa, a importação delta, sincronização completa, as sincronizações de diferenças, exportação) | [Criar um perfil de execução de agente de gestão](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [A utilização de conectores com o Azure AD Connect sincronização do Service Manager](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Importação completa perfil de execução e certifique-se de que existem objetos no espaço de conector | [Procurar um objecto de espaço de conector](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[A utilização de conectores com o Azure AD Connect sincronização do Service Manager: espaço de conector de pesquisa](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Criar regras de sincronização, de modo a que os objetos no Metaverso têm atributos necessários para cargas de trabalho | [Sincronização do Azure AD Connect: melhores práticas para alterar a configuração predefinida: alterações às regras de sincronização](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Sincronização do Azure AD Connect: Noções sobre o aprovisionamento declarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Sincronização do Azure AD Connect: Noções sobre expressões de aprovisionamento declarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Iniciar o ciclo de sincronização completa | [Sincronização do Azure AD Connect: programador: iniciar o programador](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| Em caso de problemas de fazer a resolução de problemas | [Resolver problemas relacionados com um objeto que não está a sincronizar com o Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Verificar, se LDAP utilizador pode iniciar sessão e aceder à aplicação | https://MyApps.microsoft.com |

### <a name="considerations"></a>Considerações

> [!IMPORTANT]
> Esta é uma configuração avançada que requerem familiarizado com o FIM/MIM. Se utilizada na produção, aconselhamos perguntas sobre esta configuração passar por [suporte Premier](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupos - propriedade delegado

Hora para concluído aproximada: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Aplicação SaaS (SSO federado ou palavra-passe SSO) já configurada | Bloco modular: [SaaS configuração de SSO federado](#saas-federated-sso-configuration) |
| Grupo de nuvem que é atribuído acesso para a aplicação no #1 é identificado | Bloco modular: [SaaS configuração de SSO federado](#saas-federated-sso-configuration) <br/>[Crie um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| As credenciais para o proprietário do grupo estão disponíveis | [Manage access to resources with Azure Active Directory groups](active-directory-manage-groups.md) (Gerir o acesso aos recursos com grupos do Azure Active Directory) |
| Identificou credenciais para o técnico de informação aceder as aplicações | [O que é o painel de acesso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Identificar o grupo que tenha sido concedido acesso à aplicação e configure o proprietário de determinado grupo| [Gerir as definições para um grupo no Azure Active Directory](active-directory-groups-settings-azure-portal.md) |
| Inicie sessão como o proprietário do grupo, consulte a associação a grupos no separador de grupos do painel de acesso | [Página de gestão de grupos do Active Directory do Azure](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Adicionar o técnico de informação que pretende testar |  |
| Inicie sessão como o técnico de informação, confirme que o mosaico está disponível | [O que é o painel de acesso?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Considerações

Se a aplicação tiver ativado o aprovisionamento, poderá ter de aguardar alguns minutos para que seja concluída antes de aceder à aplicação a como o técnico de informação de aprovisionamento.

## <a name="saas-and-identity-lifecycle"></a>SaaS e o ciclo de vida de identidade

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Aplicação SaaS (SSO federado ou palavra-passe SSO) já configurada | Bloco modular: [SaaS configuração de SSO federado](#saas-federated-sso-configuration) |
| Grupo de nuvem que é atribuído acesso para a aplicação no #1 é identificado | Bloco modular: [SaaS configuração de SSO federado](#saas-federated-sso-configuration) <br/>[Crie um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Identificou credenciais para o técnico de informação aceder as aplicações | [O que é o painel de acesso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Remova o utilizador do grupo da que aplicação é atribuída a | [Gerir a associação de grupo para os utilizadores no seu inquilino do Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Aguarde alguns minutos para aprovisionamento automatizados. | [Automatizar o aprovisionamento de utilizadores de aplicações de SaaS no Azure AD: como funciona o trabalho de aprovisionamento automatizado?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Numa sessão separada do browser, inicie sessão como o técnico de informação portal minhas aplicações e confirme que mosaico está em falta | http://MyApps.microsoft.com |


### <a name="considerations"></a>Considerações

Utilize para tirar conclusões o cenário de PoC para leavers e/ou deixe de ausência cenários. Se o utilizador obtém desativado no AD no local ou removida, há já não é uma forma de iniciar sessão na aplicação SaaS.

## <a name="self-service-access-to-application-management"></a>Self-Service de acesso à gestão de aplicações

Hora para concluído aproximada: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar os utilizadores POC que irão solicitar acesso a aplicações, como parte do grupo de segurança | Bloco modular: [SaaS configuração de SSO federado](#saas-federated-sso-configuration) |
| Aplicação de destino implementada | Bloco modular: [SaaS configuração de SSO federado](#saas-federated-sso-configuration) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Aceda ao painel de aplicações da empresa no Portal de gestão do Azure AD | [Portal de gestão do Azure AD: As aplicações da empresa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Configurar a aplicação de pré-requisitos com self-service | [Novidades na gestão de aplicações da empresa no Azure Active Directory: configurar o acesso de aplicação personalizada](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Inicie sessão como o técnico de informação para a minha portal de aplicações | http://MyApps.microsoft.com |
| Tenha em atenção "+ Adicionar aplicação" botão op da página. Utilize-o para obter acesso à aplicação |  |

### <a name="considerations"></a>Considerações

As aplicações escolhidas poderão ter requisitos de aprovisionamento, pelo que vai imediatamente para a aplicação pode causar alguns erros. Se a aplicação escolhida suporta o aprovisionamento com o azure ad e está configurado, poderá utilizar isto como uma oportunidade para mostrar o fluxo de todo a funcionar de ponto a ponto. Consulte o bloco modular para [SaaS configuração de SSO federado](#saas-federated-sso-configuration) para obter mais recomendações

## <a name="self-service-password-reset"></a>Reposição de palavra-passe Self-Service

Hora para concluído aproximada: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ative a gestão de palavra-passe self-service no seu inquilino. | [Azure Active Directory reposição palavra-passe para os administradores de TI](active-directory-passwords-update-your-own-password.md) |
| Ative a palavra-passe repetição de escrita gerir palavras-passe no local. Note que isto requer específico do Azure AD Connect versões | [Pré-requisitos da Repetição de Escrita de Palavras-passe](active-directory-passwords-writeback.md) |
| Identifique os utilizadores de PoC que irão utilizar esta funcionalidade e certifique-se de que são membros de um grupo de segurança. Os utilizadores têm de ser não-administradores demonstramos completamente a capacidade | [Personalizar: Gestão de palavras-passe do Azure ao AD: restringir o acesso a reposição de palavra-passe](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Navegue até ao Portal de gestão do Azure AD: reposição de palavra-passe | [Portal de gestão do Azure AD: Reposição de palavra-passe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Determine a que política de reposição de palavra-passe. Para efeitos POC, pode utilizar chamada telefónica e Q & A. Recomenda-se para ativar o registo de ser necessário no início de sessão para o painel de acesso |  |
| Terminar sessão e inicie sessão como um técnico de informação |  |
| Fornecer os dados de reposição de palavra-passe Self-Service, como configurado por passo 2 | http://aka.MS/ssprsetup |
| Feche o browser |  |
| Começar o processo de início de sessão como o técnico de informação que utilizou no passo 4 |  |
| Repor a palavra-passe | [Atualizar a sua própria palavra-passe: repor a minha palavra-passe](active-directory-passwords-update-your-own-password.md) |
| Tente iniciar sessão com a sua nova palavra-passe para o Azure AD, bem como a recursos no local |  |

### <a name="considerations"></a>Considerações

1. Se atualizar o Azure AD Connect vai fazer com que friction, em seguida, considere utilizá-lo contra contas na nuvem ou torná-lo uma demonstração em relação a um ambiente separada
2. Os administradores têm uma política diferente e utilizar a conta de administrador para repor a palavra-passe poderá taint PoC e causar confusões. Certifique-se de que utilizar uma conta de utilizador normal para as operações de reposição de teste


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure multi-factor Authentication com as chamadas telefónicas

Hora para concluído aproximada: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar os utilizadores POC que irão utilizar o MFA  |  |
| Telefone com boa receção de desafio MFA  | [O que é o Multi-Factor Authentication do Azure?](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Navegue para o painel "Utilizadores e grupos" no Portal de gestão do Azure AD | [Portal de gestão do Azure AD: Utilizadores e grupos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Escolha o painel de "Todos os utilizadores" |  |
| Na parte superior da barra no botão "Multi-factor Authentication" de escolha | Direcionar o URL para o portal do Azure MFA: https://aka.ms/mfaportal |
| Nas definições do "Utilizador" selecionar os utilizadores de PoC e ativá-los para a MFA | [Estados do Utilizador no Multi-Factor Authentication do Azure](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| Inicie sessão como o utilizador de PoC e o percurso durante o processo de prova  |  |

### <a name="considerations"></a>Considerações

1. Os passos de PoC este bloco modular explicitamente a definição de MFA um utilizador em todos os inícios de sessão. Existem outras ferramentas, como o acesso condicional e a proteção de identidade que interagir com a MFA em mais direcionado cenários. Este será algo a considerar quando mover de POC para produção.
2. Os passos de PoC este bloco modular explicitamente estiver a utilizar as chamadas telefónicas como o método MFA para expedience. Como efetuar a transição de POC produção, recomendamos a utilização aplicações como o [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) como o segundo fator sempre que possível.
Saiba mais: [rascunho publicação especial do NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Acesso condicional do MFA para aplicações SaaS

Hora para concluído aproximada: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identifique os utilizadores de PoC para a política de destino. Estes utilizadores devem estar num grupo de segurança para definir o âmbito da política de acesso condicional | [Configuração de SSO federado SaaS](#saas-federated-sso-configuration) |
| Aplicação SaaS já configurada |  |
| Utilizadores de PoC já estão atribuídos à aplicação |  |
| As credenciais para o utilizador POC estão disponíveis |  |
| O utilizador POC está registado para MFA. Utilizar um telefone com boa receção | http://aka.MS/ssprsetup |
| Dispositivo na rede interna. Endereço IP configurado no intervalo de endereços interno | Localizar o endereço ip: https://www.bing.com/search?q=what%27s+my+ip |
| Dispositivo na rede externa (pode ser um telefone com a rede móvel da operadora) |  |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Aceda ao Portal de gestão do Azure AD: painel de acesso condicional | [Portal de gestão do Azure AD: Acesso condicional](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Crie política de acesso condicional:<br/>-Os utilizadores de PoC destino em "Utilizadores e grupos"<br/>-Aplicação de PoC destino sob "Aplicações em nuvem"<br/>-Destino todas as localizações exceto aqueles fidedignas em "Condições" -> "Localizações" **Nota:** IPs fidedignos são configuradas no [MFA Portal](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Exigir autenticação multifator em "Conceder" | [Introdução ao acesso condicional no Azure Active Directory: passos de configuração de política](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Acesso aplicação a partir de dentro da rede empresarial | [Introdução ao acesso condicional no Azure Active Directory: testar a política](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Aplicação de acesso de rede pública | [Introdução ao acesso condicional no Azure Active Directory: testar a política](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Considerações

Se estiver a utilizar Federação, pode utilizar o fornecedor de identidade no local (IdP) para comunicar o estado de rede da empresa dentro/externa com afirmações. Pode utilizar esta técnica sem ter de gerir a lista de endereços IP que podem ser complexas para avaliar e gerir grandes organizações. A configuração, tem de conta para o cenário "roaming de rede" (um utilizador registo a partir da rede interna e ao comutadores com sessão iniciada localizações como num café) e certifique-se de que compreende as implicações. Saiba mais: [proteger recursos da nuvem com o Azure multi-factor Authentication e o AD FS: IPs fidedignos para utilizadores federados](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Hora para concluído aproximada: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar o administrador global que irá fazer parte de POC PIM | [Começar a utilizar o Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identificar o administrador global que vai ser o administrador de segurança | [Começar a utilizar o Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Diferentes funções administrativas no Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md) |
| Opcional: Confirme se os administradores globais têm acesso ao e-mail para notificações de correio eletrónico no PIM | [O que é o Azure AD Privileged Identity Management?: configurar as definições de ativação de função](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Início de sessão para https://portal.azure.com como um administrador global (GA) e o painel PIM de arranque. O Administrador Global que executa este passo é pré-propagadas como o administrador de segurança.  Vamos chamar esta ator GA1 | [Utilizar o Assistente de segurança no Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identificar o administrador global e movê-los a partir permanente para elegível. Deve ser um administrador separado da utilizada no passo 1 para efeitos de clareza. Vamos chamar esta ator GA2 | [Azure AD Privileged Identity Management: Como adicionar ou remover uma função de utilizador](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[O que é o Azure AD Privileged Identity Management?: configurar as definições de ativação de função](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Agora, inicie sessão no como GA2 https://portal.azure.com e tente alterar a "Definições de utilizador". Tenha em atenção algumas opções são desativadas. | |
| Num novo separador na mesma sessão como passo 3, navegue até agora para https://portal.azure.com e adicionar o painel do PIM ao dashboard. | [Como ativar ou desativar as funções no Azure AD Privileged Identity Management: adicionar a aplicação de Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Pedido de ativação para a função de Administrador Global | [Como ativar ou desativar as funções no Azure AD Privileged Identity Management: ativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Tenha em atenção que se GA2 nunca inscreveu MFA, o registo para o MFA do Azure será necessário |  |
| Volte ao separador original no passo 3 e clique no botão Atualizar no browser. Tenha em atenção que tem agora acesso para alterar a "Definições de utilizador" | |
| Opcionalmente, se os administradores globais tiverem o e-mail ativado, pode verificar GA1 e da GA2 pasta a receber e ver a notificação da função a ser ativada |  |
| 8 Verifique o histórico de auditoria e observe o relatório para confirmar a elevação de GA2 é apresentado. | [O que é o Azure AD Privileged Identity Management?: função actividade de revisão](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Considerações

Esta capacidade é parte do Azure AD Premium P2 e/ou EMS E5

## <a name="discovering-risk-events"></a>Detetar eventos de risco

Hora para concluído aproximada: 20 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Dispositivo com browser Tor transferido e instalado | [Transferir Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Acesso ao utilizador POC o início de sessão | [Azure Active Directory Identity Protection manual de comunicação social](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Browser abrir tor | [Transferir Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Inicie sessão no https://myapps.microsoft.com com a conta de utilizador POC | [Azure Active Directory Identity Protection manual de comunicação social: simulando eventos de risco](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Aguarde 5 a 7 minutos |  |
| Inicie sessão como um administrador global para https://portal.azure.com e abrir o painel Identity Protection | https://aka.MS/aadipgetstarted |
| Abra o painel de eventos de risco. Deverá ver uma entrada em "Inícios de sessão de endereços IP anónimos"  | [Azure Active Directory Identity Protection manual de comunicação social: simulando eventos de risco](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Considerações

Esta capacidade é parte do Azure AD Premium P2 e/ou EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Implementar políticas de início de sessão risco

Hora para concluído aproximada: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Dispositivo com browser Tor transferido e instalado | [Transferir Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Acesso como um utilizador POC para efetuar o registo no teste |  |
| O utilizador POC está registado com a MFA. Certifique-se utilizar um telefone com boa receção | Bloco modular: [Azure multi-factor Authentication com as chamadas telefónicas](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Inicie sessão como um administrador global para https://portal.azure.com e abrir o painel de Identity Protection | https://aka.MS/aadipgetstarted |
| Ative uma política de início de sessão do risco da seguinte forma:<br/>-Atribuído a: utilizador POC<br/>-Condições: Início de sessão risco médio ou superior (início de sessão anónimo localização é considerado como um nível de risco média)<br/>-Controlos: Exigir a MFA | [Azure Active Directory Identity Protection manual de comunicação social: início de sessão risco](active-directory-identityprotection-playbook.md#sign-in-risk) |
| Browser abrir tor | [Transferir Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Inicie sessão no https://myapps.microsoft.com com a conta de utilizador de PoC |  |
| Tenha em atenção o desafio MFA | [Início de sessão experiências com o Azure AD Identity Protection: recuperação de risco início de sessão](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Considerações

Esta capacidade é parte do Azure AD Premium P2 e/ou E5 do EMS. Para saber mais sobre eventos de risco visitam: [eventos de risco do Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Configurar a autenticação baseada em certificado

Aproximada do tempo para concluir: 20 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Dispositivos com o certificado de utilizador aprovisionado (Windows, iOS ou Android) da PKI de empresa | [Implementar certificados de utilizador](https://msdn.microsoft.com/library/cc770857.aspx) |
| Domínio do Azure AD federadas com o AD FS | [Azure AD Connect e a federação](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Descrição geral de serviços de certificados do Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Para dispositivos iOS ter a aplicação Microsoft Authenticator instalada | [Começar a utilizar a aplicação Authenticator da Microsoft](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Passos

| Passo | Recursos |
| --- | --- |
| Ativar "Autenticação de certificados" no ADFS | [Configurar políticas de autenticação: Para configurar a autenticação principal global no Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Opcional: Ative a autenticação de certificado no Azure AD para clientes do Exchange Active Sync | [Introdução à autenticação baseada em certificado no Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Navegue para o painel de acesso e autenticar com o certificado de utilizador | https://MyApps.microsoft.com |

### <a name="considerations"></a>Considerações

Para saber mais sobre as limitações desta implementação visitam: [ADFS: autenticação de certificado com o Azure AD & do Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Posse do certificado de utilizador deve ser protegido. A pela gestão de dispositivos ou com o PIN em caso de smart cards.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
