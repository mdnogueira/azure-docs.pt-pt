---
title: Ligar o Active Directory ao Azure Active Directory. | Microsoft Docs
description: "O Azure AD Connect irá integrar os diretórios no local ao Azure Active Directory. Isto permite-lhe fornecer uma identidade comum para as aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD."
keywords: "introdução ao Azure AD Connect, descrição geral do Azure AD Connect, o que é o Azure AD Connect, instalar o Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 209f8869e9ed681285865154bdd4d2d7a0f22456
ms.lasthandoff: 04/12/2017


---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>Integre os diretórios no local ao Azure Active Directory
O Azure AD Connect irá integrar os diretórios no local ao Azure Active Directory. Isto permite-lhe fornecer uma identidade comum para o utilizadores das aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD. Este tópico descreve o planeamento, a implementação e os passos de operação. É uma coleção de ligações para os tópicos relacionados com esta área.

> [!IMPORTANT]
> [O Azure AD Connect é a melhor forma de ligar o seu diretório no local ao Azure AD e ao Office 365. É uma excelente altura para atualizar o Azure AD Connect do Windows Azure Active Directory Sync (DirSync) ou Azure AD Sync, uma vez que estas ferramentas foram preteridas e o seu suporte terminará a 13 de abril de 2017.](active-directory-aadconnect-dirsync-deprecated.md)
> 
> 

![O que é o Azure AD Connect](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Porquê utilizar o Azure AD Connect
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Os utilizadores e a organizações podem tirar partido do seguinte:

* Os utilizadores podem utilizar uma identidade única para acederem às aplicações no local e aos serviços na nuvem, como o Office 365.
* Uma ferramenta única para proporcionar uma experiência fácil de implementação para sincronização e início de sessão.
* Fornece as capacidades mais recentes para os seus cenários. O Azure AD Connect substitui as versões anteriores das ferramentas de integração de identidade, como o DirSync e o Azure AD Sync. Para obter mais informações, consulte [Comparação das ferramentas de integração de diretórios de identidade híbrida](../active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Como funciona o Azure AD Connect
O Azure Active Directory Connect é constituído por três componentes principais: os serviços de sincronização, o componente opcional dos Serviços de Federação do Active Directory e o componente de monitorização chamado [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

<center>![Pilha do Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Sincronização – este componente é responsável pela criação de utilizadores, grupos e outros objetos. Também é responsável por verificar se as informações de identidade dos seus utilizadores e grupos no local têm correspondência na nuvem.
* AD FS – a federação é uma parte opcional do Azure AD Connect e pode ser utilizada para configurar um ambiente híbrido utilizando uma infraestrutura do AD FS no local. Pode ser utilizada por organizações para lidarem com implementações complexas, tal como SSO para associação a um domínio, imposição de uma política de início de sessão do AD e MFA de smart card ou de terceiros.
* Monitorização do Estado de Funcionamento - o Azure AD Connect Health pode proporcionar uma monitorização robusta e uma localização central no Portal do Azure para visualizar esta atividade. Para obter informações adicionais, consulte [Azure Active Directory Connect Health](../connect-health/active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Instalar o Azure AD Connect
Pode encontrar a transferência do Azure AD Connect no [Centro de Transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).

| Solução | Cenário |
| --- | --- |
| Antes de iniciar – [Hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md) |<li>Passos a completar antes de iniciar a instalação do Azure AD Connect.</li> |
| [Definições rápidas](active-directory-aadconnect-get-started-express.md) |<li>Se tiver uma única floresta do AD, é esta a opção que se recomenda utilizar.</li> <li>Início de sessão do utilizador com a mesma palavra-passe, utilizando a sincronização de palavra-passe.</li> |
| [Definições personalizadas](active-directory-aadconnect-get-started-custom.md) |<li>Utilizadas se tiver várias florestas. Suporta muitas [topologias](active-directory-aadconnect-topologies.md) no local.</li> <li>Personalize a sua opção de início de sessão, como o ADFS para federação ou utilize um fornecedor de identidade de terceiros.</li> <li>Personalize as funcionalidades de sincronização, como a filtragem e repetição de escrita.</li> |
| [Atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Utilizada se tiver um servidor existente do DirSync já em execução.</li> |
| [Atualizar do Azure AD Sync ou do Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Há vários métodos diferentes, consoante a sua preferência.</li> |

[Após a instalação](active-directory-aadconnect-whats-next.md), deve verificar se está a funcionar de acordo com o esperado e atribuir licenças aos utilizadores.

### <a name="next-steps-to-install-azure-ad-connect"></a>Passos seguintes para Instalar o Azure AD Connect
|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças ](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Saber mais sobre como instalar o Azure AD Connect
Terá também de se preparar para questões [operacionais](active-directory-aadconnectsync-operations.md). Pode pretender ter um servidor de reserva a que possa recorrer facilmente caso aconteça um [desastre](active-directory-aadconnectsync-operations.md#disaster-recovery). Se planear efetuar alterações frequentes na configuração, deverá planear um servidor no [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode).

|Tópico |Ligação|  
| --- | --- |
|Topologias suportadas | [Topologias do Azure AD Connect](active-directory-aadconnect-topologies.md)|
|Conceitos de design | [Conceitos de design do Azure AD Connect](active-directory-aadconnect-design-concepts.md)|
|Contas utilizadas para a instalação | [Mais informações acerca das credenciais e permissões do Azure AD Connect](./active-directory-aadconnect-accounts-permissions.md)|
|Planeamento operacional | [Sincronização do Azure AD Connect: considerações e tarefas operacionais](active-directory-aadconnectsync-operations.md)|
|Opções de início de sessão do utilizador | [Opções de início de sessão de Utilizador do Azure AD Connect](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>Configurar funcionalidades de sincronização
O Azure AD Connect inclui várias funcionalidades que pode ativar como opção ou que estão ativadas por predefinição. Algumas das funcionalidades poderão requerer, às vezes, mais configuração em determinados cenários e topologias.

A [filtragem](active-directory-aadconnectsync-configure-filtering.md) é utilizada quando pretende limitar os objetos que são sincronizados para o Azure AD. Por predefinição, são sincronizados todos os utilizadores, contactos, grupos e computadores com Windows 10. Pode alterar a filtragem com base em domínios, UOs ou atributos.

A [sincronização de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) sincroniza o hash de palavra-passe no Active Directory para o Azure AD. O utilizador final pode utilizar a mesma palavra-passe no local e na nuvem, mas geri-la apenas numa única localização. Uma vez que utiliza o Active Directory no local como autoridade,pode também utilizar a sua própria política de palavras-passe.

A [repetição de escrita de palavras-passe](../active-directory-passwords-getting-started.md) permitirá que os utilizadores alterem e reponham as respetivas palavras-passe na nuvem e a aplicação da sua política de palavras-passe no local.

A [repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md) permitirá que um dispositivo registado no Azure AD seja rescrito no Active Directory no local, para que possa ser utilizado para acesso condicional.

A funcionalidade [impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) está ativada por predefinição e protege o diretório na nuvem de sofrer várias eliminações ao mesmo tempo. Por predefinição, permite 500 eliminações por execução. Pode alterar esta definição consoante o tamanho da sua organização.

A [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) está ativada por predefinição para instalações com definições rápidas e garante que o Azure AD Connect é sempre atualizado para a versão mais recente.

### <a name="next-steps-to-configure-sync-features"></a>Passos seguintes para configurar as funcionalidades de sincronização
|Tópico |Ligação|  
| --- | --- |
|Configurar a filtragem | [Sincronização do Azure AD Connect: configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md)|
|Sincronização de palavras-passe | [Sincronização do Azure AD Connect: implementar a sincronização de palavras-passe](active-directory-aadconnectsync-implement-password-synchronization.md)|
|Repetição de escrita de palavras-passe | [Introdução à gestão de palavras-passe](../active-directory-passwords-getting-started.md)|
|Repetição de escrita do dispositivo | [Ativar a repetição de escrita do dispositivo no Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)|
|Impedir eliminações acidentais | [Sincronização do Azure AD Connect: impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|Atualização automática | [Azure AD Connect: atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Personalizar a sincronização do Azure AD Connect
A sincronização do Azure AD Connect vem com uma configuração predefinida, concebida para funcionar com a maior parte dos clientes e topologias. Mas há sempre situações em que a configuração predefinida não funciona e deve ser ajustada. É suportada para efetuar alterações, como documentado nesta secção e tópicos ligados.

Se nunca trabalhou com uma topologia de sincronização, será boa ideia começar por entender as noções básicas e os termos utilizados, descritos nos [conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md). O Azure AD Connect é a evolução do MIIS2003, ILM2007 e FIM2010. Mesmo se alguns elementos são idênticos, muitos foram também alterados.

A [configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md) presume que pode existir mais do que uma floresta na configuração. Nessas topologias, um objeto de utilizador pode ser representado como um contacto noutra floresta. O utilizador pode também ter uma caixa de correio ligada noutra floresta de recursos. O comportamento da configuração predefinida está descrito em [utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

O modelo de configuração em sincronização é designado por [aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Os fluxos de atributos avançadas utilizam [funções](active-directory-aadconnectsync-functions-reference.md) para expressar transformações de atributos. Pode ver e examinar a configuração toda utilizando as ferramentas fornecidas com o Azure AD Connect. Se precisar de efetuar alterações na configuração, certifique-se de que segue as [melhores práticas](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), para que seja mais fácil adotar novas versões.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Passos seguintes para personalizar a sincronização do Azure AD Connect
|Tópico |Ligação|  
| --- | --- |
|Todos os artigos acerca da sincronização do Azure AD Connect | [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)|
|Conceitos técnicos | [Sincronização do Azure AD Connect: Conceitos Técnicos](active-directory-aadconnectsync-technical-concepts.md)|
|Entender a configuração predefinida | [Sincronização do Azure AD Connect: entender a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md)|
|Entender utilizadores e contactos | [Sincronização do Azure AD Connect: entender Utilizadores e Contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|Aprovisionamento declarativo | [Sincronização do Azure AD Connect: entender as Expressões do Aprovisionamento Declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|Alterar a configuração predefinida | [Melhores práticas para alterar a configuração predefinida](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Configurar as funcionalidades de federação
O ADFS pode ser configurado para suportar [vários domínios](active-directory-aadconnect-multiple-domains.md). Pode, por exemplo, ter vários domínios superiores que precisa de utilizar para a federação.

Se o servidor do ADFS não tiver sido configurado para atualizar automaticamente os certificados a partir do Azure AD ou se utilizar uma solução não ADFS, será notificado quando tiver de [atualizar os certificados](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Passos seguintes para configurar as funcionalidades de federação
|Tópico |Ligação|  
| --- | --- |
|Todos os artigos do AD FS | [Azure AD Connect e a federação](active-directory-aadconnectfed-whatis.md)|
|Configuração do ADFS com subdomínios | [Suporte para Vários Domínios para Federação com o Azure AD](active-directory-aadconnect-multiple-domains.md)|
|Gerir o farm do AD FS | [Gestão e personalização do AD FS com o Azure AD Connect](active-directory-aadconnect-federation-management.md)|
|Atualizar manualmente certificados de federação | [Renovar Certificados de Federação do Office 365 e do Azure AD](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>Mais informações e referências
|Tópico |Ligação|  
| --- | --- |
|Histórico de versões | [Histórico de versões](active-directory-aadconnect-version-history.md)|
|Comparar o DirSync, o Azure ADSync e o Azure AD Connect | [Comparação das ferramentas de integração de diretórios](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Lista de compatibilidades não ADFS do Azure AD | [Lista de compatibilidades de federação do Azure AD](active-directory-aadconnect-federation-compatibility.md)|
|Atributos sincronizados | [Atributos sincronizados](active-directory-aadconnectsync-attributes-synchronized.md)|
|Monitorizar utilizando o Azure AD Connect Health | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|Perguntas Mais Frequentes | [FAQ do Azure AD Connect](active-directory-aadconnect-faq.md)|

**Recursos Adicionais**

Apresentação do Ignite 2015 acerca de como expandir os diretórios no local para a nuvem.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 


