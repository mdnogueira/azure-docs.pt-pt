---
title: Compreender a identidade do Azure | Microsoft Docs
description: "Obter uma compreensão básica das recomendações para que possa tomar a decisão de governação de identidade melhor para a sua organização, conceitos e termos de solução de identidade do Microsoft Azure."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: e368f14638c480a632afa7c17023aa8ae4c8833f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Compreender as soluções de identidade do Azure
Microsoft Azure Active Directory (Azure AD) é um identidades e acessos na nuvem solução de gestão que fornece serviços de diretório, governação de identidade e gestão de acesso de aplicação. Azure AD rapidamente [permite início de sessão único (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) em 1 000 das aplicações previamente integradas, comerciais e personalizadas no [Galeria de aplicações do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Muitas destas aplicações, provavelmente já utilizar como o Office 365, em Salesforce.com, caixa, ServiceNow e Workday.

Um único diretório do Azure AD é automaticamente associado uma subscrição do Azure quando é criado. Como o serviço de identidade do Azure, o Azure AD, em seguida, fornece todos os gestão de identidades e funções de controlo de acesso para recursos baseados na nuvem. Estes recursos podem incluir os utilizadores, grupos e aplicações para um inquilino individual (organização) conforme mostrado no diagrama seguinte:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure oferece várias formas para tirar partido de identidade como um serviço (IDaaS) com diferentes níveis de complexidade para satisfazer as necessidades da sua organização individuais. O resto deste artigo ajuda-o a compreender conceitos e terminologia básica de identidade do Azure, bem como as recomendações para fazer a melhor escolha entre as opções disponíveis.

## <a name="terms-to-know"></a>Termos de saber

Antes de pode decidir sobre uma solução de identidade do Azure para a sua organização, precisa de uma compreensão básica dos termos utilizados normalmente quando se fala sobre os serviços de identidade do Azure.

|Termo saber| Descrição|
|-----|-----|
|Subscrição do Azure |As subscrições são utilizadas para pagar para serviços em nuvem do Azure e que normalmente estão ligadas a um cartão de crédito. Pode ter várias subscrições, mas pode ser difícil de partilhar recursos entre subscrições.|
|Inquilino do Azure | Um inquilino do Azure AD é representativo de uma única organização. É uma instância dedicada, fidedigna do Azure AD, que é criada automaticamente quando uma organização se inscreve para uma subscrição do serviço de nuvem Microsoft, tais como o Azure, o Intune ou o Office 365. Os inquilinos podem obter acesso aos serviços ou num ambiente dedicado (único inquilino) ou num ambiente partilhado com outras organizações (multi-inquilino).|
|Diretório do Azure AD | Cada inquilino do Azure tem um Azure dedicado, fidedigno diretório AD que contém utilizadores, grupos e aplicações do inquilino. É utilizado para efetuar a identidade e acesso de funções de gestão de recursos de inquilino. Porque um único diretório do Azure AD é automaticamente aprovisionado para representar a sua organização quando se inscreve num serviço de nuvem da Microsoft, como o Azure, o Microsoft Intune ou o Office 365, por vezes, verá os termos de licenciamento *inquilino*, *do Azure AD*, e *diretório do Azure AD* -no alternadamente. |
|Domínio personalizado | Quando se inscreve pela primeira vez para uma subscrição do serviço em nuvem Microsoft, o seu inquilino (organização) utiliza um *. onmicrosoft.com* nome de domínio. No entanto, a maioria das organizações tem uma ou mais nomes de domínio que são utilizados para efetuar negócio e de que os utilizadores finais utilizam para aceder a recursos da empresa. Pode adicionar o nome de domínio personalizado ao Azure AD para que o nome de domínio seja familiar aos utilizadores, tais como  *alice@contoso.com*  em vez de  *alice@contoso.onmicrosoft.com* . |
|Conta do Azure AD | Estas são as identidades, que são criadas através do Azure AD ou outro serviço em nuvem da Microsoft como o Office 365. São armazenados no Azure AD e acessíveis para qualquer uma das subscrições de serviços em nuvem da organização. |
|Administrador de subscrição do Azure| O administrador da conta é a pessoa que inscreveu no ou comprou a subscrição do Azure. Podem utilizar o [Centro de contas](https://account.azure.com/Subscriptions) para efetuar várias tarefas de gestão, como criar subscrições, cancelar subscrições, altere a faturação de uma subscrição ou alterar o administrador de serviço. |
|Administrador Global do Azure AD | Administradores globais do Azure AD têm acesso total a todas as funcionalidades administrativas do Azure AD. A pessoa que se inscreve para uma subscrição do serviço em nuvem Microsoft automaticamente torna-se um administrador global por predefinição. Pode ter mais do que um administrador global, mas apenas os administradores globais podem atribuir qualquer um dos [as outras funções de administrador](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) aos utilizadores. |
|Conta Microsoft | Contas Microsoft (criadas por si para utilização pessoal) fornecem acesso ao consumidor e orientado para produtos da Microsoft e serviços em nuvem, como o Outlook (Hotmail), OneDrive, Xbox LIVE ou do Office 365. Estas identidades são criadas e armazenadas no Microsoft consumidor identidade conta sistema executado pela Microsoft.|
|Contas profissionais ou escolares | Contas profissionais ou escolares (emitidas por um administrador para utilização de negócio/académicas) fornecem acesso para a empresa nível empresarial cloud services da Microsoft, tais como o Azure, o Intune ou o Office 365.|


## <a name="concepts-to-understand"></a>Conceitos para compreender

Agora que sabe os termos de identidade do Azure básica, deve obter mais informações sobre estes conceitos de identidade do Azure que o ajudar a tomar uma decisão de serviço de identidade do Azure informadas.

|Conceito de compreender |Descrição|
|-----|-----|
|[Como as subscrições do Azure estão associadas ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Cada subscrição do Azure tem uma relação de confiança com um diretório do Azure AD para autenticar utilizadores, serviços e dispositivos. *Várias subscrições podem confiar mesmo diretório do Azure AD, mas uma subscrição confiarão apenas um único diretório do Azure AD*. Esta relação de confiança é diferente da relação de uma subscrição com outros recursos do Azure (sites, bases de dados e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Se uma subscrição expirar, em seguida, acesso aos recursos associados a subscrição que não sejam do Azure AD também é interrompida. No entanto, o diretório do Azure AD permanece no Azure, para que possa associar outra subscrição esse diretório e continuar a gerir os recursos de inquilino.|
|[O Azure AD licenciamento funciona](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Ao utilizador o compra ou ativar o Enterprise Mobility Suite, Azure AD Premium ou Basic do Azure AD, o diretório é atualizado com a subscrição, incluindo o respetivo período de validade e licenças pré-pago. Depois da subscrição está ativa, o serviço pode ser gerido por administradores globais do Azure AD e utilizado pelos utilizadores licenciados. Informações da sua subscrição, incluindo o número de licenças atribuídas ou disponíveis, estão disponíveis no portal do Azure a partir de **do Azure Active Directory** > **licenças** painel. Também é o melhor local para gerir a suas atribuições de licenças.|
|[Controlo de acesso baseado em funções no portal do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Azure baseada em funções controlo de acesso (RBAC) ajuda a fornecer gestão de acesso detalhada para os recursos do Azure. Demasiados permissões podem expor e conta aos atacantes. Permissões insuficientes significa que os funcionários não é possível obter o trabalho feito de forma eficiente. Utilizando o RBAC, pode dar aos funcionários as permissões exatas que precisam de com base nas três funções básicas que se aplicam a todos os grupos de recursos: proprietário, Contribuidor, leitor. Também pode criar até 2000 dos seus [personalizadas funções do RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) para satisfazer as suas necessidades específicas. |
|[Identidade híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Identidade híbrida é conseguida ao integrar o seu local no Windows Server Active Directory (AD DS) com o Azure AD com [do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Isto permite-lhe fornecer uma identidade comum para os seus utilizadores para o Office 365, Azure e aplicações no local ou as aplicações de SaaS integradas com o Azure AD. Com a identidade híbridas, expandir eficazmente ambiente no local para a nuvem para a identidade e acesso.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>A diferença entre o Windows Server AD DS e AD do Azure
Azure Active Directory (Azure AD) e no local do Active Directory (Serviços de domínio do Active Directory ou o AD DS) são os sistemas que armazenam dados de diretório e gerir a comunicação entre os utilizadores e recursos, incluindo processos de início de sessão do utilizador, a autenticação e das procuras de diretório.

Se já estiver familiarizado com o local no Windows Server Active Directory Domain Services (AD DS), introduzido com o Windows 2000 Server, em seguida, provavelmente, a compreender o conceito básico de um serviço de identidade. No entanto, também é importante compreender que o Azure AD não é apenas um controlador de domínio na nuvem. É uma forma de fornecer a identidade como um serviço (IDaaS) no Azure que necessita de uma forma completamente nova de pensar totalmente adotar capacidades baseado na nuvem e proteger a sua organização contra ameaças modernas completamente nova. 

O AD DS é uma função de servidor no Windows Server, o que significa que pode ser implementado em máquinas físicas ou virtuais. Tem uma estrutura hierárquica, com base em 500. Utiliza o DNS para localizar objetos, podem ser Interagiu com através de LDAP e principalmente utiliza Kerberos para autenticação. Do Active Directory permite a unidades organizacionais (UOs) e objetos de política de grupo (GPOs) para além de associar máquinas ao domínio e as confianças de entidades são criadas entre domínios.

IT protegeu os respetivos perímetro de segurança para anos utilização dos AD DS, mas as empresas modernas, perímetro sem suporte as necessidades de identidade para os empregados, clientes e parceiros necessitam de um novo plane de controlo. Azure AD é essa plane de controlo de identidade. Segurança foi movido para além da firewall da empresa para a nuvem quais do Azure AD protege os recursos da empresa e o acesso ao fornecer uma identidade comum para os utilizadores (no local ou na nuvem). Isto proporciona aos utilizadores a flexibilidade para acederem as aplicações que precisam para fazer o seu trabalho a partir de praticamente qualquer dispositivo. Os controlos de proteção de dados baseados em risco totalmente integrada, por capacidades de aprendizagem automática e relatórios aprofundada, são também desde que TI precisam de manter os dados da empresa proteger.

Azure AD é um serviço de diretório público cliente multi, o que significa que, dentro do Azure AD pode criar um inquilino para os seus servidores em nuvem e aplicações como o Office 365. Utilizadores e grupos são criados numa estrutura simples sem UOs ou GPOs. A autenticação é efetuada através de protocolos, tais como SAML, WS-Federation e OAuth. É possível consultar o Azure AD, mas em vez de utilizar LDAP tem de utilizar uma API de REST chamado AD Graph API. Estes tudo funcionar através de HTTP e HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Expandir as funcionalidades de gestão e segurança do Office 365
Já a utilizar o Office 365? Pode acelerar a transformação digital ao alargar as capacidades incorporadas do Office 365 com o Azure AD para proteger os seus recursos, ativar a produtividade segura para a sua força de trabalho completa. Quando utilizar o Azure AD, para além das capacidades do Office 365, pode proteger o portefólio de aplicação completo com uma identidade que permite início de sessão em todas as aplicações. Pode expandir as capacidades de acesso condicional com base não apenas na localização de estado, mas o utilizador, dispositivo, aplicação e também o risco. As capacidades de autenticação multifator (MFA) fornecem ainda mais proteção quando precisar dele. Irá obter adicional supervisão de privilégios de utilizador e fornecer acesso administrativo a pedido, just-in-time. Os utilizadores serão mais produtivo e criar menos pedidos de suporte técnico, graças às capacidades de self-service do Azure AD fornece como repor palavras-passe esquecidas, pedidos de acesso de aplicação e criar e gerir grupos.

> [!TIP]
> Pretende obter mais informações sobre como utilizar a gestão de identidades do Azure AD com o Office 365? [Obter e-book transferível](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Soluções de identidade do Microsoft Azure

Microsoft Azure oferece várias formas de gerir as identidades dos utilizadores se estes são mantidas completamente no local, apenas na nuvem ou mesmo algures entre. Estas opções incluem: do-it-yourself (DIY) AD DS no Azure, Azure Active Directory (Azure AD), identidade híbrida e dos serviços de domínio do Azure AD.

### <a name="do-it-yourself-diy-ad-ds"></a>DS do-IT-yourself AD (DIY)
Para as empresas que precisam de apenas um pequeno os requisitos de espaço na nuvem, **do-it-yourself (DIY) do AD DS** no Azure podem ser utilizados. Esta opção suporta vários cenários de Windows Server AD DS são ideais para a implementação de máquinas virtuais (VMs) no Azure. Por exemplo, pode criar uma VM do Azure como um controlador de domínio em execução num centro de dados faraway que está ligado à rede remota. A partir daí, a VM seria capaz de suportar pedidos de autenticação de utilizadores remotos e melhorar o desempenho de autenticação. Esta opção é também adequada como substitui a relativamente baixo custo para locais de recuperação de desastres; caso contrário dispendiosas que aloja um pequeno número de controladores de domínio e uma única rede virtual no Azure. Por fim, poderá ter de implementar uma aplicação no Azure, como o SharePoint, que requer o Windows Server AD DS, mas não tem nenhum dependência em rede no local ou o Windows Server Active Directory empresarial. Neste caso, pode implementar uma floresta isolada no Azure para satisfazer os requisitos do farm do SharePoint server. Também é suportada para implementar aplicações de rede que necessitam de conectividade à rede no local e o Active Directory no local.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
**Azure AD autónomo** é um totalmente baseado na nuvem identidade e acesso de gestão como uma solução de serviço (IDaaS). Azure AD fornece um conjunto robusto de funcionalidades para gerir utilizadores e grupos. É um serviço que ajuda a proteger o acesso a aplicações no local e na cloud, incluindo serviços Web da Microsoft como o Office 365, e a várias aplicações SaaS (software como um serviço) que não são da Microsoft. Azure AD é apresentada no três edições: gratuita, Basic e Premium. Azure AD boosts eficácia organizacional e expande a segurança para além de firewall de perímetro para um novo plane de controlo protegida pela aprendizagem do Azure e outra funcionalidades de segurança avançadas.

### <a name="hybrid-identity"></a>Identidade híbrida
Em vez disso que escolher entre no local ou de soluções de identidade baseada na nuvem, muitos direta pensar CIOs e as empresas que tenham sido iniciada prevendo a direção de longo prazo da empresa, estiver a expandir os seus diretórios no local para a nuvem através de **identidade híbrida** soluções. Com a identidade híbrida, obtém uma identidade verdadeiramente global e solução de gestão de acesso que fornece acesso seguro e produtivos e permite aos utilizadores aplicações precisa de fazer as respetivas tarefas.

> [!TIP]
> Para obter mais informações sobre como CIOs efetuou do Azure Active Directory parte central da respetiva as estratégias, transfira o [Guia do CIO ao Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
**Serviços de domínio do Azure AD** oferece uma opção baseado na nuvem para utilizar o AD DS para o controlo de configuração de VM do Azure simples e uma forma para satisfazer os requisitos de identidade no local para o desenvolvimento de aplicações de rede e o teste. Serviços de domínio do Azure AD não se destina a comparação de precisão e deslocar no local infraestrutura de AD DS para VMs do Azure gerida pelos serviços de domínio do Azure AD. Em vez disso, as VMs do Azure nos domínios geridos deve ser utilizadas para suportar o desenvolvimento, teste e movimento de aplicações no local que necessitem de métodos de autenticação do AD DS para a nuvem.

## <a name="common-scenarios-and-recommendations"></a>Recomendações e cenários comuns

Seguem-se alguns cenários comuns de identidades e acessos com base em recomendações relativamente a qual poderá mais adequada para cada opção de identidade.

|Cenário de identidade| Recomendação|
|-----|-----|
|A minha organização tem feita os investimentos em grandes no local no Windows Server Active Directory, mas pretendem expandir a identidade para a nuvem.| A solução de identidade do Azure mais amplamente utilizadas é [identidade híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Se já fez investimentos no local do AD DS, pode expandir facilmente a identidade para a nuvem com o Azure AD Connect.|
|Meu negócio nasceu na nuvem e temos não investimentos em soluções de identidade no local.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é a melhor opção para empresas apenas na nuvem sem qualquer local investimentos.|
|Preciso de configuração de VM do Azure simples e controlo para satisfazer os requisitos no local identidade para o desenvolvimento de aplicações e o teste.|[Serviços de domínio do Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) é uma boa opção se precisar de utilizar o AD DS para controlo de configuração de VM do Azure simples ou procura para desenvolver ou migrar aplicações legadas, com suporte para o diretório no local para a nuvem.|  
|Necessário suportar algumas máquinas virtuais no Azure, mas a minha empresa ainda descontos elevados é investida no local do Active Directory (AD DS).|Utilizar [DIY do AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) utilizar as VMs do Azure quando tiver de suportar algumas máquinas virtuais e tenham investido grande de AD DS no local. |

## <a name="where-can-i-learn-more"></a>Onde posso obter mais informações?
Temos um ton excelente recursos online para o ajudar a saber tudo sobre o Azure AD. Aqui está uma lista dos artigos excelente para começar:

* [Ativar o seu diretório para a gestão híbrida com o Azure AD Connect](active-directory-aadconnect.md)
* [Segurança adicional para um mundo de alguma vez ligado](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Introdução aos relatórios do Azure AD](active-directory-reporting-getting-started.md)
* [Gerir as palavras-passe a partir de qualquer lugar](active-directory-passwords.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md)
* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [O que é o licenciamento de Microsoft Azure Active Directory?](active-directory-licensing-what-is.md)
* [Como pode detetar aplicações na nuvem não sancionadas utilizadas dentro da minha organização](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Passos seguintes

Agora que compreende os conceitos de identidade do Azure e as opções disponíveis para si, pode utilizar os seguintes recursos para começar a implementar a opção que escolheu:

[Saiba mais sobre as soluções de identidade híbrida do Azure](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Saiba mais num ambiente do Azure prova de conceito](https://aka.ms/aad-poc)

[Implementar o Azure AD na produção](https://aka.ms/aad-onboard)
