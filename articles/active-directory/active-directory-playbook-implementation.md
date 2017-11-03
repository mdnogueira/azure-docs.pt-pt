---
title: "Implementação de PoC manual do Azure Active Directory | Microsoft Docs"
description: "Explorar e implementar rapidamente a cenários de identidade e gestão de acesso"
services: active-directory
keywords: do Azure Active Directory, manual, uma prova de conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 10877ee33ec04cf0d350417af59d598eab249aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Azure Active Directory prova de conceito manual: implementação

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation - a sincronizar o AD para o Azure AD 

Uma identidade híbrida é o alicerce da maioria dos clientes empresariais que já tenham um diretório no local. O objetivo aqui é intencionalmente gaste como menos tempo quanto possível mostrar o valor dos cenários reais de identidades e acessos. 

| Cenário | Blocos Modulares| 
| --- | --- |  
| [Expandir a sua identidade no local para a nuvem](#extending-your-on-premises-identity-to-the-cloud) | [Sincronização de diretórios - sincronização de Hash de palavra-passe](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Tenha em atenção**: Se já tiver DirSync/ADSync ou versões anteriores do Azure AD Connect, este passo é opcional. Alguns cenários neste guia podem requerer a versão mais recente do Azure AD Connect.  <br/>[Imagem corporativa](active-directory-playbook-building-blocks.md#branding) | 
| [Atribuir licenças do Azure AD através de grupos](#assigning-azure-ad-licenses-using-groups) | [Baseado em grupo de licenciamento](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Expandir a sua identidade no local para a nuvem 

1. João é o administrador do Active Directory no Contoso. Ele obtém o requisito para ativar a identidade como um serviço para um conjunto de utilizadores. Após a execução do Assistente do Azure AD Connect, a identidade dos utilizadores destino disponíveis na nuvem. 
2. Bernardo pergunta Susie, um dos utilizadores de destino, para aceder ao painel de acesso do Azure Active Directory e confirme que ela pode ser autenticado. Susie verá uma página de início de sessão com marca corporativa e um painel de acesso vazia que está pronto para ativar o acesso de aplicação futuras.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Atribuir licenças do Azure AD através de grupos 

1. João é o Administrador Global do AD do Azure e pretende atribuir licenças do Azure AD para um conjunto específico de utilizadores como parte da implementação inicial do Azure AD.
2. João cria um grupo para os utilizadores piloto. 
3. Bernardo atribui as licenças ao grupo
4. Susie, um dos técnicos de informação, é adicionada ao grupo de segurança como parte do respetiva funções de tarefas
5. Após algum tempo, Susie tem acesso para a licença do Azure AD premium. Este procedimento activará mais os cenários POC mais tarde.

## <a name="theme---lots-of-apps-one-identity"></a>Tema - muitas aplicações, uma identidade

| Cenário | Blocos Modulares| 
| --- | --- |  
| [Integrar aplicações SaaS - SSO federado](#integrate-saas-applications---federated-sso) | [Configuração de SSO federado SaaS](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Grupos - propriedade delegado](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integrar aplicações SaaS - palavra-passe SSO](#integrate-saas-applications---password-sso) | [Configuração de SSO de palavra-passe de SaaS](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [SSO e eventos de ciclo de vida de identidade](#sso-and-identity-lifecycle-events) | [SaaS e o ciclo de vida de identidade](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Proteger o acesso a contas partilhados](#secure-access-to-shared-accounts) | [Configuração de contas de partilhada de SaaS](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Acesso remoto seguro a aplicações no local](#secure-remote-access-to-on-premises-applications) | [Configuração de Proxy de aplicações](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Sincronizar as identidades LDAP para o Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Configuração do conector LDAP genérico](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integrar aplicações SaaS - SSO federado 

1. João é o Administrador Global do AD do Azure e recebe um pedido do departamento de Marketing para ativar o acesso a respetiva instância do ServiceNow. Bernardo localiza o tutorial passo a passo na documentação do Azure AD e o seguinte e os delegados a atribuição de utilizadores para a aplicação a Kevin, o cabeçalho da equipa de Marketing. 
2. Kevin inicia sessão como proprietário do ServiceNow elegibilidade e atribui Susie para a aplicação. Kevin avisos também que o perfil de Susie foi criado no ServiceNow automaticamente
3. Susie é um técnico de informação no departamento de Marketing. A Joana inicia sessão no azure AD e localiza todas as aplicações de SaaS, que ela é atribuída a no portal de myapps. A partir daí, ela perfeitamente obtém acesso à ServiceNow.
4. O departamento de Marketing pretende auditar quem acedeu ServiceNow. Bernardo transfere um relatório de atividade e partilhe o mesmo com Kevin através de correio eletrónico.  

### <a name="sso-and-identity-lifecycle-events"></a>SSO e eventos de ciclo de vida de identidade

1. Susie demora uma ausência de deixe e pela política da empresa no local conta AD é desativado temporário. Susie agora não é possível iniciar sessão no Azure AD e, por conseguinte, não é possível aceder ao ServiceNow. 
2. Susie faz uma mudança lateral do departamento de Marketing para vendas. Kevin remove o respetivo acesso do ServiceNow. Susie iniciar sessão em myapps o azure ad e ela já não verá o mosaico do ServiceNow. Após 10 minutos, Kevin confirma que conta Susie foi desativada a partir da consola de gestão do ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Integrar aplicações SaaS - palavra-passe SSO

1. João configura o acesso ao Atlassian HipChat. HipChat tem integração de SSO de palavra-passe e conceder acesso aos Susie
2. Susie inicia sessão portal de myapps e verá uma hiperligação para transferir a extensão de browser do Azure AD IE, ela transfere
3. Após clicar em, ela obtém um pedido de credenciais de nome de utilizador e palavra-passe utras HipChat. Esta é uma operação única e, depois de concluir, ela tem acesso ao HipChat
4. Mais tarde, de alguns dias Susie abre myapps portal e clica em HipChat novamente. Este tempo aproximadamente, ela obtém acesso totalmente integrado
5. Pretende Kevin, o proprietário da aplicação HipChat auditar quem acedeu a aplicação. Bernardo transfere um relatório de auditoria e partilhe o mesmo com Kevin através de correio eletrónico. 

### <a name="secure-access-to-shared-accounts"></a>Proteger o acesso a contas partilhados 

1. Bernardo é a tarefa para proteger o identificador de Twitter partilhado para os membros da equipa de vendas. Adiciona o Twitter como uma aplicação de SSO e atribui-o para o grupo de segurança da equipa de vendas. Ele foi fornecido as credenciais à conta partilhada e ele fornece no sistema. 
2. Já não é fidedigno devido a várias pessoas saber Twitter credenciais de partilha. Bernardo permite rollover automático da palavra-passe do Twitter.
3. Susie, um membro da equipa de vendas, inicia sessão portal de myapps e verá uma hiperligação para transferir a extensão de browser do IE do Azure AD. A Joana instala-o.
4. Após clicar em ela aceder diretamente ao Twitter. A Joana não souber a palavra-passe.
5. Arnold também faz parte da equipa de vendas. Ele tem a mesma experiência como Susie nos passos 3 a 4
6. O departamento de vendas pretende auditar quem acedeu Twitter. Bernardo transfere um relatório de atividade e partilhe o mesmo com Kevin através de correio eletrónico. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Acesso remoto seguro a aplicações no local

1. Bernardo, o Administrador Global AD do Azure, tem de foi obtido vários pedidos para permitir que os funcionários aceder a várias útil recursos no local, como a aplicação de despesas, ao trabalhar remotamente. Ele segue o [documentação do Proxy de aplicações](active-directory-application-proxy-enable.md) para instalar um conector e publicar as despesas como uma aplicação de Proxy de aplicações. 
2. Bernardo partilha o URL externo de aplicação despesas com Susie, um dos empregados que necessitam de acesso remoto. Ela acede a ligação e, depois de autenticação relativamente do AAD, ela é capaz de aceder à aplicação despesas e continuar a serem produtivos ao remoto. 
3. Bernardo, em seguida, continua a publicação de aplicações adicionais no local utilizando o mesmo processo e conceder acesso a utilizadores, conforme necessário. Adiciona o acesso condicional e multi-factor authentication para as aplicações mais confidenciais que ele publica, para garantir a segurança adicional.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Sincronizar as identidades LDAP para o Azure AD

1. A empresa do de Bob tem infraestrutura de identidade complexas. A maioria dos utilizadores é mantida dentro de Windows Server Active Directory do domínio dos serviços (ADDS). Algumas das mesmas, são geridas pelo sistema de RH dentro de Active Directory Lightweight Directory serviços (ADLDS).
2. Bernardo é incumbido a permitir o acesso a aplicações SaaS para todos os utilizadores (também estes não estão presentes no ADDS).
3. João configura o conector de LDAP genérico para retirar dados de ADLDS no Azure AD Connect.
4. João cria as regras de sincronização para que os utilizadores LDAP são preenchidos para Metaverso e para o Azure AD
5. Susie LDAP utilizador acede a sua aplicação SaaS através de ser sincronizados identidade



> [!IMPORTANT] 
> Esta é uma configuração avançada que requerem familiarizado com o FIM/MIM. Se utilizada na produção, aconselhamos perguntas sobre esta configuração passar por [suporte Premier](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Tema - aumentar a segurança 

| Cenário | Blocos Modulares| 
| --- | --- |  
| [Acesso de conta de administrador segura](#secure-administrator-account-access) | [Azure MFA com as chamadas telefónicas](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Proteger o acesso a aplicações](#secure-access-to-applications) | [Acesso condicional para aplicações SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Ativar a administração de apenas no tempo](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Proteger identidades com base em risco](#protect-identities-based-on-risk) | [Detetar eventos de risco](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Implementar políticas de início de sessão risco](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Se autentiquem sem palavras-passe utilizando a autenticação baseada em certificado](#authenticate-without-passwords-using-certificate-based-authentication) | [Configurar a autenticação baseada em certificado](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Acesso de conta de administrador segura

1. João é o Administrador Global do AD do Azure. Ele identificou José como coadministrador do serviço. 
2. João configura a conta de José sempre exigir a MFA melhorar a postura de segurança
3. José inicia sessão no portal do Azure e avisos que ele tem de registar o número de telefone para continuar o início de sessão
4. Inícios de sessão subsequentes de José estão agora protegidos com o multi-factor Authentication e ele agora obtém uma chamada telefónica para verificar a identidade.

### <a name="secure-access-to-applications"></a>Acesso seguro a aplicações

1. Kevin é o proprietário da empresa do ServiceNow. A empresa pretende agora aos utilizadores iniciar sessão com a MFA quando aceder fora da rede empresarial.
2. Bernardo, nosso Administrador Global do Azure AD, adiciona uma política de acesso condicional para a aplicação de ServiceNow para ativar a MFA para acesso externo
3. Susie, nosso técnico de informação, inicia sessão no portal minhas aplicações e clica no mosaico do ServiceNow. A Joana é agora solicitada com a MFA.

### <a name="enable-just-in-time-jit-administration"></a>Ative apenas na administração time (JIT)

1. Bernardo e José são administradores globais de AD do Azure. Pretendem para ativar o acesso JIT para as funções de gestão e também para manter registos de utilização das funções com privilégios.
2. João ativa PIM no inquilino do Azure AD e torna-se o administrador de segurança. Ele é alterada ele próprio tanto o membro da função Administrador global de José de permanente para elegível.
3. Bernardo e José necessitam agora de ativar a respetiva função através do portal do Azure antes de efetuar quaisquer alterações à configuração do Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Proteger identidades com base em risco 

1. Susie, um técnico de informação tenta iniciar sessão num browser de tor. 
2. João verifica o dashboard do Azure AD identity protection e verá início de sessão de Susie de um endereço IP anónimo. A equipa de segurança pretende challenge esses utilizadores de acessos com a MFA
3. João ativa do Azure AD Identity Protection política desafio MFA para eventos de risco média ou superior
4. Tempo passa e Susie voltar a iniciar browser Tor. Neste momento, a Joana irá ver o desafio MFA

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Se autentiquem sem palavras-passe utilizando a autenticação baseada em certificado

1. Bernardo é administrador Global para instituição financeira que proíbe a utilização de palavras-passe como um fator de autenticação para as aplicações.
2. João ativa e impõe a autenticação de certificado no ADFS e o Azure AD
3. Susie ao aceder a aplicação é solicitada para autenticar a utilização de certificado

## <a name="theme---scale-with-self-service"></a>Tema - escala com Self-Service

| Cenário | Blocos Modulares| 
| --- | --- |  
| [Reposição de palavra-passe Self-Service](#self-service-password-reset) | [Reposição de palavra-passe Self-Service](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Acesso de Self-Service a aplicações](#self-service-access-to-applications) | [Acesso de Self-Service a aplicações](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Reposição de palavra-passe Self-Service 

1. João é o Administrador Global do Azure AD e permite a gestão de palavras-passe Self Service para um subconjunto de utilizadores, incluindo Susie. 
2. Os registos de Susie no myapps portal e ver uma mensagem para registar o respetiva informações de segurança futuras palavra-passe de reposição de eventos.
3. Rápido reencaminhar alguns dias, Susie se esquecer do respetivo palavra-passe e repõe-lo através do portal do Azure AD

### <a name="self-service-access-to-applications"></a>Acesso de Self-Service a aplicações 

1. Kevin é o proprietário da empresa do ServiceNow. John aos utilizadores "Inscreva-se"-la a pedido, em vez de adicioná-los ao mesmo tempo
2. Bernardo, nosso Administrador Global do Azure AD, modifica a aplicação de ServiceNow para permitir pedidos de recuperação self-service
3. Susie, nosso técnico de informação, inicia sessão no portal minhas aplicações e clica no botão "Adicionar mais aplicações" e consulte ServiceNow como uma das aplicações recomendadas. Em seguida, a Joana navega para trás para a minha portal de aplicações e ver a aplicação do ServiceNow.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]