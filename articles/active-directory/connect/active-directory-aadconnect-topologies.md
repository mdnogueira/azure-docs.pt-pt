---
title: 'O Azure AD Connect: Topologias suportadas | Microsoft Docs'
description: "Este tópico descreve as topologias suportadas e não suportadas para o Azure AD Connect"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: d7af3a531bc1bcced1a3c48403a6f60afea0f82f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias do Azure AD Connect
Este artigo descreve as várias topologias do Azure Active Directory (Azure AD) que utilizam a sincronização do Azure AD Connect como solução de integração de chave e no local. Este artigo inclui as configurações suportadas e não suportadas.

Segue-se a legenda para imagens no artigo:

| Descrição | Símbolo |
| --- | --- |
| Floresta do Active Directory no local |![Floresta do Active Directory no local](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| No local do Active Directory com a importação filtrado |![Active Directory com a importação filtrada](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Servidor de sincronização do Azure AD Connect |![Servidor de sincronização do Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Servidor sincronização do Azure AD Connect, "modo de teste" |![Servidor sincronização do Azure AD Connect, "modo de teste"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync com do Forefront Identity Manager (FIM) 2010 ou do Microsoft Identity Manager (MIM) 2016 |![GALSync com o FIM 2010 ou o MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Servidor de sincronização do Azure AD Connect, detalhada |![Servidor de sincronização do Azure AD Connect, detalhada](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Cenário não suportado |![Cenário não suportado](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, único inquilino do Azure AD
![Topologia para uma floresta única e um único inquilino](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é um único local floresta, com um ou vários domínios e um único do Azure AD de inquilino. Para a autenticação do Azure AD, é utilizada a sincronização de palavra-passe. A instalação rápida do Azure AD Connect suporta apenas esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Floresta única, vários servidores de sincronização para um inquilino do Azure AD
![Topologia não suportada, filtrada para uma única floresta](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Não é suportado ter múltiplos servidores de sincronização do Azure AD Connect ligados ao mesmo inquilino do Azure AD, exceto para um [transição servidor](#staging-server). Tem não suportado, mesmo se estes servidores estiverem configurados para sincronizar com um conjunto de objetos mutuamente exclusivo. Poderá ter considerado esta topologia se não é possível contactar todos os domínios na floresta de um único servidor ou se quiser distribuam carga por vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Várias florestas, único inquilino do Azure AD
![Topologia de várias florestas e de um único inquilino](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com várias florestas de Active Directory no local. Existem várias razões para ter mais do que uma floresta de Active Directory no local. Exemplos típicos são estruturas com florestas de recursos de conta e o resultado de uma fusão ou aquisição.

Se tiver várias florestas, todas as florestas tem de estar acessível por um único servidor de sincronização do Azure AD Connect. Não tem de associar o servidor a um domínio. Se for necessário para aceder a todas as florestas, pode colocar o servidor numa rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede filtrada).

O Assistente de instalação do Azure AD Connect oferece várias opções para consolidar os utilizadores são representados em várias florestas. O objetivo é representado apenas uma vez que um utilizador no Azure AD. Existem algumas topologias comuns que pode configurar no caminho de instalação personalizada no Assistente de instalação. No **identificar os utilizadores de forma exclusiva** página, selecione a opção correspondente que representa a topologia. A consolidação está configurada apenas para os utilizadores. Grupos de duplicados não são consolidados com a configuração predefinida.

Topologias comuns são abordadas nas secções sobre [separar topologias](#multiple-forests-separate-topologies), [completa mesh](#multiple-forests-full-mesh-with-optional-galsync), e [a topologia de recurso de conta](#multiple-forests-account-resource-forest).

Pressupõe que a configuração predefinida na sincronização do Azure AD Connect:

* Cada utilizador tem apenas uma conta ativada e a floresta onde está localizada a esta conta é utilizada para autenticar o utilizador. Este pressuposto destina-se a sincronização de palavra-passe e a Federação. UserPrincipalName e sourceAnchor/immutableID provenientes nesta floresta.
* Cada utilizador tem apenas uma caixa de correio.
* A floresta que aloja a caixa de correio para um utilizador tem a melhor qualidade dos dados de atributos visíveis na lista de endereços Global Exchange (GAL). Se não existir nenhuma caixa de correio para o utilizador, qualquer floresta pode ser utilizada para contribuir com estes valores de atributo.
* Se tiver uma caixa de correio ligada, há também uma conta numa floresta diferente utilizada para início de sessão.

Se o seu ambiente não corresponde nesses pressupostos, ocorrem os seguintes procedimentos:

* Se tiver mais do que uma conta Active Directory ou mais do que uma caixa de correio, o motor de sincronização escolhe um e ignora o outro.
* Uma caixa de correio ligada com nenhuma conta de Active Directory não é exportada para o Azure AD. A conta de utilizador não é representada como um membro em qualquer grupo. Uma caixa de correio ligada no DirSync sempre é representada como uma caixa de correio normal. Esta alteração intencionalmente é um comportamento diferente para suportar melhor os cenários de floresta múltiplos.

Pode encontrar mais detalhes em [entender a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um inquilino do Azure AD
![Topologia não suportada para várias florestas e de múltiplos servidores de sincronização](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Ter mais do que um servidor de sincronização do Azure AD Connect ligado a um único inquilino do Azure AD não é suportado. A exceção é a utilização de um [transição servidor](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Várias florestas, topologias separadas
![Opção para representar os utilizadores apenas uma vez em todos os diretórios](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Depiction de várias florestas e topologias separadas](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Neste ambiente, todas as florestas no local são tratadas como entidades separadas. Nenhum utilizador está presente em qualquer outra floresta. Cada floresta tem a sua própria organização do Exchange e não há nenhum GALSync entre as florestas. Esta topologia poderá estar a situação após uma fusão/aquisição ou numa organização onde cada unidade de negócio funciona independentemente. Estas florestas estão na mesma organização no Azure AD e aparecem com uma GAL unificada. Na imagem anterior, cada objeto em cada floresta é representado uma vez no metaverso e agregado no inquilino do destino do Azure AD.

### <a name="multiple-forests-match-users"></a>Várias florestas: corresponder a utilizadores
Comuns a todos estes cenários é a distribuição e grupos de segurança podem conter uma mistura de utilizadores, contactos e as principais de segurança externa (FSPs). FSPs são utilizadas em serviços de domínio do Active Directory (AD DS) para representar os membros de outras florestas num grupo de segurança. Todos os FSPs são resolvidos para o objecto real no Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Várias florestas: completa mesh com GALSync opcional
![Opção para utilizar o atributo de correio para a correspondência quando existem identidades de utilizadores em vários diretórios](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Topologia de malha completa para várias florestas](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que os utilizadores e os recursos localizados em qualquer floresta. Normalmente, existem confianças bidirecionais entre as florestas.

Se estiver presente na floresta de mais do que um Exchange, poderão existir (opcionalmente) uma solução de GALSync no local. Todos os utilizadores, em seguida, é representado como um contacto em todas as outras florestas. GALSync normalmente é implementado através do FIM 2010 ou o MIM 2016. O Azure AD Connect não pode ser utilizado para GALSync no local.

Neste cenário, os objetos de identidade são associados através do atributo de correio. Um utilizador que tem uma caixa de correio numa floresta é associado contactos nas outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Várias florestas: floresta de recursos de conta
![Opção para utilizar os atributos Sidobjeto e msExchMasterAccountSID para efetuar a correspondência quando existem identidades em vários diretórios](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topologia de floresta de recursos de conta para várias florestas](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Uma topologia de floresta de recursos de conta, tem um ou mais *conta* florestas com contas de utilizador do Active Directory. Também tem um ou mais *recursos* florestas com contas desativadas.

Neste cenário, um (ou mais) recursos confianças de floresta todas as florestas de contas. A floresta de recursos, normalmente, tem um esquema do Active Directory expandido com o Exchange e o Lync. Serviços de todos os Exchange e o Lync, juntamente com outros serviços partilhados, encontram-se nesta floresta. Os utilizadores têm uma conta de utilizador desativado nesta floresta e a caixa de correio está ligada à floresta de contas.

## <a name="office-365-and-topology-considerations"></a>Office 365 e considerações sobre a topologia
Algumas cargas de trabalho do Office 365 têm determinadas restrições em topologias suportadas:

| Carga de trabalho | Restrições |
--------- | ---------
| Exchange Online | Se existir mais do que uma organização de Exchange no local (ou seja, Exchange tiver sido implementado a mais do que uma floresta), tem de utilizar o Exchange 2013 SP1 ou posterior. Para obter mais informações, consulte [implementações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype para empresas | Quando estiver a utilizar várias florestas no local, apenas a topologia de floresta de recursos de conta é suportada. Para obter mais informações, consulte [requisitos ambientais para o Skype para empresas servidor 2015](https://technet.microsoft.com/library/dn933910.aspx). |


## <a name="staging-server"></a>Servidor de teste
![Servidor de teste numa topologia](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect suporta a instalação de um segundo servidor no *modo de teste*. Um servidor no modo lê dados de todos os diretórios ligados mas não escrever nada diretórios ligados. Utiliza o ciclo de sincronização normal e, por conseguinte, tem uma cópia atualizada os dados de identidade.

Um desastre em que o servidor principal falhar, pode ativação pós-falha para o servidor de teste. Fazê-lo no Assistente do Azure AD Connect. Este segundo servidor pode estar localizado num centro de dados diferentes porque nenhuma infraestrutura está partilhada com o servidor primário. Tem de copiar manualmente qualquer alteração de configuração efetuada no servidor primário para o segundo servidor.

Pode utilizar um servidor de teste para testar uma nova configuração personalizada e o efeito que tem nos seus dados. Pode pré-visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, pode tornar o servidor de teste do servidor ativo e definir o antigo servidor ativo para o modo de teste.

Também pode utilizar este método para substituir o servidor de sincronização do Active Directory. Prepare o novo servidor e configurá-lo para o modo de teste. Certifique-se de que está em bom estado, desativar (tornando-o Active Directory), de modo de teste e encerrar o servidor atualmente ativo.

É possível ter mais de um servidor de teste, quando pretender ter várias cópias de segurança em datacenters diferentes.

## <a name="multiple-azure-ad-tenants"></a>Vários inquilinos do Azure AD
Recomenda-se ter um único inquilino no Azure AD para uma organização.
Antes de que pretende utilizar vários inquilinos do Azure AD, consulte o artigo [gestão unidades administrativas no Azure AD](../active-directory-administrative-units-management.md). Abrange os cenários comuns em que pode utilizar um único inquilino.

![Topologia de várias florestas e de vários inquilinos](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Há uma relação de 1:1 entre um servidor de sincronização do Azure AD Connect e um inquilino do Azure AD. Para cada inquilino do Azure AD, precisa de uma instalação de servidor de sincronização do Azure AD Connect. As instâncias de inquilino do Azure AD são isoladas por predefinição. Ou seja, os utilizadores no um inquilino não é possível ver os utilizadores no outro inquilino. Se pretender que esta separação, esta é uma configuração suportada. Caso contrário, deve utilizar o único modelo de inquilino do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez num inquilino do Azure AD
![Topologia filtrada para uma única floresta](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Nesta topologia, um servidor de sincronização do Azure AD Connect está ligado a cada inquilino do Azure AD. Os servidores de sincronização do Azure AD Connect tem de ser configurados para efeitos de filtragem para que cada um tem um conjunto de objetos a funcionar mutuamente exclusivo. Pode, por exemplo, definir o âmbito cada servidor para um determinado domínio ou unidade organizacional.

Um domínio DNS pode ser registado em apenas um único inquilino do Azure AD. Os UPNs dos utilizadores na instância do Active Directory no local também tem de utilizar espaços de nomes separados. Por exemplo, na imagem anterior, três sufixos UPN separados estão registados na instância do Active Directory no local: contoso.com, fabrikam.com e wingtiptoys.com. Os utilizadores em cada domínio de Active Directory no local, utilizar um espaço de nomes diferentes.

Não há nenhum GALSync entre as instâncias de inquilino do Azure AD. O endereço do livro no Exchange Online e Skype para empresas mostra apenas os utilizadores no mesmo inquilino.

Esta topologia tem as seguintes restrições no caso contrário, suportada cenários:

* Apenas um dos inquilinos do Azure AD pode ativar uma híbrida do Exchange com a instância do Active Directory no local.
* Dispositivos Windows 10 pode ser associada com apenas um inquilino do Azure AD.
* A início de sessão (SSO) opção única para autenticação de pass-through e de sincronização de palavra-passe pode ser utilizada com apenas um inquilino do Azure AD.

O requisito de um conjunto de objetos mutuamente também se aplica a repetição de escrita. Algumas funcionalidades de repetição de escrita não são suportadas com esta topologia porque estes partem do princípio de uma única configuração no local. Estas funcionalidades incluem:

* Repetição de escrita grupo com a configuração predefinida.
* Repetição de escrita do dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes num inquilino do Azure AD
![Topologia não suportada para uma única floresta e de vários inquilinos](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologia não suportada para uma floresta única e vários conectores](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Estas tarefas não são suportadas:

* Sincronizar o mesmo utilizador para vários inquilinos do Azure AD.
* Fazer uma configuração alterar para que os utilizadores um inquilino do Azure AD são apresentados como contactos no outro inquilino do Azure AD.
* Modificar a sincronização do Azure AD Connect para ligar a vários inquilinos do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync através da utilização de repetição de escrita
![Topologia não suportada para várias florestas e de vários diretórios, com GALSync concentrar-se no Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologia não suportada para várias florestas e de vários diretórios, com GALSync concentrar-se no local do Active Directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Inquilinos do Azure AD são isolados por predefinição. Estas tarefas não são suportadas:

* Altere a configuração da sincronização do Azure AD Connect para ler dados a partir do outro inquilino do Azure AD.
* Exportação de utilizadores como contactos noutra instância do Active Directory no local através da utilização de sincronização do Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com o servidor de sincronização no local
![GALSync numa topologia de várias florestas e de vários diretórios](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Pode utilizar o FIM 2010 ou o MIM 2016 no local para sincronizar utilizadores (através de GALSync) entre duas organizações do Exchange. Os utilizadores de uma organização são apresentados como externos utilizadores por contactos na outra organização. Estes diferentes no local do Active Directory instâncias, em seguida, podem ser sincronizadas com os seus próprios inquilinos do Azure AD.

## <a name="next-steps"></a>Passos seguintes
Para saber como instalar o Azure AD Connect nestes cenários, consulte o artigo [instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
