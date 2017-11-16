---
title: "Azure Active Directory híbrida identidade considerações de design - definir a estratégia de proteção de dados | Microsoft Docs"
description: "É possível definir a estratégia de proteção de dados para a sua solução de identidade híbrida satisfazer as necessidades empresariais que definiu."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2017
ms.author: billmath
ms.openlocfilehash: f9eb994e2105f2eb7704c8729e5f372c8042673d
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir a estratégia de proteção de dados para a sua solução de identidade híbrida
Nesta tarefa, vai definir a estratégia de proteção de dados para a sua solução de identidade híbrida cumprir os requisitos de negócio que definiu no:

* [Determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinar os requisitos de gestão de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinar os requisitos de controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinar os requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Conforme explicado no [determinar os requisitos de sincronização do directory](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), o Microsoft Azure AD pode sincronizar com os serviços de domínio de diretório Active Directory no local (AD DS). Esta integração permite utilizar o organizações do Azure AD para verificar as credenciais dos utilizadores quando se estão a tentar aceder a recursos empresariais. Pode fazê-lo para ambos os cenários: dados rest no local e na nuvem. Acesso a dados no Azure AD requer autenticação do utilizador através de um serviço de token de segurança (STS).

Uma vez autenticado, o nome principal de utilizador (UPN) é lida o token de autenticação. Em seguida, o sistema de autorização determina o replicadas partição e o contentor correspondente para o domínio do utilizador. Em seguida, as informações sobre a existência do utilizador, estado ativado e função de ajudam do sistema de autorização determinar se o acesso ao inquilino de destino está autorizado para o utilizador nessa sessão. Determinadas ações autorizadas (especificamente, criar utilizador e palavra-passe da reposição) crie um registo de auditoria que um administrador inquilino, em seguida, utiliza para gerir os esforços de compatibilidade ou as investigações.

Mover dados do datacenter no local para o armazenamento do Azure através de uma ligação de Internet não podem sempre ser viáveis devido ao volume de dados, disponibilidade de largura de banda ou outras considerações. O [serviço de importação/exportação do Storage do Azure](../storage/common/storage-import-export-service.md) oferece uma opção baseada em hardware para a obter e colocar a grandes volumes de dados no blob storage. Permite-lhe enviar [encriptada com BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) unidades de disco rígido diretamente para um datacenter Azure, onde os operadores da nuvem carregar o conteúdo à sua conta de armazenamento ou pode transferir os dados do Azure para as unidades para devolver ao utilizador. Apenas discos encriptados são considerados aceites para este processo (utilizando uma chave do BitLocker gerada pelo serviço durante a configuração de tarefa). A chave do BitLocker é fornecida para o Azure separadamente, deste modo, fornecendo fora de banda de chaves partilha.

Uma vez que os dados em trânsito podem ocorrer em cenários diferentes, também é relevante para saber que utiliza o Microsoft Azure [redes virtuais](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego de inquilinos entre si, empregar medidas como anfitrião - e ao nível do convidado as firewalls, filtragem de pacotes IP, bloquear a porta e pontos finais HTTPS. No entanto, a maior parte das comunicações internas do Azure, incluindo a infraestrutura de infraestrutura e de infraestrutura de cliente (no local), também é encriptados. Outro cenário importante é as comunicações dentro dos centros de dados do Azure; Microsoft gere redes para garantir que nenhum VM pode representar ou eavesdrop no endereço IP de outro. O TLS/SSL é utilizado ao aceder ao armazenamento do Azure ou bases de dados do SQL Server ou ao ligar a serviços em nuvem. Neste caso, o administrador de cliente é responsável por obter um certificado TLS/SSL e implementar a sua infraestrutura de inquilino. Os dados do tráfego movidos entre máquinas virtuais na mesma implementação ou entre inquilinos numa única implementação através da rede Virtual do Microsoft Azure podem ser protegidos através de protocolos de comunicações encriptadas como HTTPS, SSL/TLS ou outras pessoas.

Dependendo de como respondeu às questões na [determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), deverá conseguir determinar como pretende proteger os seus dados e como solução de identidade híbrida pode ajudá-lo com esse processo. A tabela seguinte mostra as opções suportadas pelo Azure que estão disponíveis para cada cenário de proteção de dados.

| Opções de proteção de dados | Inativos na nuvem | Em rest no local | Em trânsito |
| --- | --- | --- | --- |
| Encriptação de unidade BitLocker |X |X | |
| SQL Server para encriptar as bases de dados |X |X | |
| Encriptação de VM para VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Leitura [conformidade pela funcionalidade](https://azure.microsoft.com/support/trust-center/services/) em [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações que cada serviço do Azure está em conformidade com.
> Uma vez que as opções de proteção de dados de utilizam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para esta tarefa. Certifique-se de que lhe é tirar partido de todas as opções disponíveis para cada Estado dos dados.
>
>

## <a name="define-content-management-options"></a>Definir as opções de gestão de conteúdo
Uma vantagem de utilizar o Azure AD para gerir uma infraestrutura de identidade híbrida é que o processo é completamente transparente de perspetiva do utilizador final. O utilizador tenta aceder a um recurso partilhado, o recurso requer autenticação, o utilizador tem de enviar um pedido de autenticação para o Azure AD para poder obter o token e aceder ao recurso. Este processo completo ocorre em segundo plano, sem interação do utilizador. Também é possível conceder a permissão para um [grupo](active-directory-manage-groups.md#getting-started-with-access-management) de utilizadores para permitir-lhes autorização efetuar determinadas ações comuns.

As organizações que são normalmente preocupação sobre privacidade de dados exigem a classificação de dados para a solução. Se a sua infraestrutura no local atual já está a utilizar a classificação de dados, é possível utilizar o Azure AD como repositório principal para a identidade do utilizador. Uma ferramenta comuns que é utilizado no local para classificação de dados é denominada [Toolkit de classificação de dados](https://msdn.microsoft.com/library/Hh204743.aspx) para o Windows Server 2012 R2. Esta ferramenta pode ajudar a identificar, classificar e proteger os dados nos servidores de ficheiros na sua nuvem privada. Também é possível utilizar o [classificação automática de ficheiros](https://technet.microsoft.com/library/hh831672.aspx) no Windows Server 2012 para realizar esta tarefa.

Se a sua organização não tiver a classificação de dados no local, mas tem proteger ficheiros confidenciais sem adicionar novo servidores no local, poderá utilizar Microsoft [serviço Azure Rights Management](https://technet.microsoft.com/library/JJ585026.aspx).  O Azure RMS utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os ficheiros e o e-mail e funciona em vários dispositivos — telemóveis, tablets e PCs. Porque o Azure RMS é um serviço em nuvem, não é necessário configurar explicitamente fidedignidades com outras organizações para poder partilhar conteúdo protegido com as mesmas. Se estas já possuírem um Office 365 ou um diretório do Azure AD, colaboração entre as organizações é suportada automaticamente. Também pode sincronizar apenas os atributos do diretório do Azure RMS tem de suportar uma identidade comum para as contas do Active Directory no local, utilizando os serviços de sincronização de diretório Active Directory do Azure (AAD Sync) ou do Azure AD Connect.

Uma parte vital do gestão de conteúdos é compreender quem está a aceder a quais recursos, por conseguinte, é importante para a solução de gestão de identidade uma capacidade do registo avançadas. O Azure AD fornece o registo mais de 30 dias, incluindo:

* Alterações à associação de função (ex: utilizador adicionado à função de Administrador Global)
* As atualizações de credencial (ex: alterações de palavra-passe)
* Gestão de domínios (ex: verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão de utilizadores (ex: adicionar, remover, atualizar um utilizador)
* Adição ou remoção de licenças

> [!NOTE]
> Leitura [segurança do Microsoft Azure e gestão do registo de auditoria](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre as capacidades de registo no Azure.
> Dependendo de como respondeu às questões na [determinar os requisitos de gestão de conteúdos](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), deverá conseguir determinar como pretende que o conteúdo sejam geridos na sua solução de identidade híbrida. Apesar de todas as opções de expostas na tabela 6 são capazes de integração com o Azure AD, é importante definir que é mais adequada às suas necessidades de negócio.
>
>

| Opções de gestão de conteúdo | Vantagens | Desvantagens |
| --- | --- | --- |
| Centralizado no local (servidor de gestão de direitos do Active Directory) |Controlo total sobre a infraestrutura de servidor responsável para classificar os dados <br> Capacidade incorporada no Windows Server, sem necessidade de licenciamento adicional ou de subscrição <br> Pode ser integrado com o Azure AD num cenário híbrido <br> Suporta as capacidades de gestão (IRM) direitos de informação no Microsoft Online services, tais como o Exchange Online e SharePoint Online, bem como do Office 365 <br> Suporta produtos de servidor da Microsoft no local, como o Exchange Server, SharePoint Server e servidores de ficheiros que executam o Windows Server e a infraestrutura de classificação de ficheiros (FCI). |Manutenção superior (manter cópias de segurança com atualizações, configuração e atualizações de potenciais), desde IT é proprietário do servidor <br> Requer uma infraestrutura de servidor no local<br> Não tirar partido das capacidades do Azure nativamente |
| Centralizado em nuvem (Azure RMS) |Mais fácil de gerir em comparação com a solução no local <br> Pode ser integrado com o AD DS num cenário híbrido <br>  Totalmente integrado com o Azure AD <br> Não necessita de um servidor no local para implementar o serviço <br> Suporta local produtos de servidor da Microsoft, tais como o Exchange Server, SharePoint, servidor e os servidores de ficheiros que executam o Windows Server e a classificação de ficheiros, infraestrutura (FCI) <br> Departamento de TI podem ter controlo total sobre a chave do respetivo inquilino com capacidade BYOK. |A organização tem de ter uma subscrição na nuvem que suporte RMS <br> A organização tem de ter um diretório do Azure AD para suportar a autenticação de utilizador para RMS |
| Híbrido (Azure RMS integrado, local no servidor do Active Directory Rights Management) |Este cenário acumula as vantagens de ambas as, centralizado no local e na nuvem. |A organização tem de ter uma subscrição na nuvem que suporte RMS <br> A organização tem de ter um diretório do Azure AD para suportar a autenticação de utilizador para RMS, <br> Requer uma ligação entre o serviço em nuvem do Azure e a infraestrutura no local |

## <a name="define-access-control-options"></a>Definir as opções de controlo de acesso
Ao tirar partido da autenticação, autorização e capacidades do controlo de acesso disponíveis no Azure AD, pode ativar a sua empresa a utilizar um repositório de identidade central ao permitir que os utilizadores e parceiros para utilizar um único início de sessão (SSO) conforme mostrado na figura seguinte:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gestão centralizada e totalmente a integração com outros diretórios

Azure Active Directory fornece início de sessão único a milhares de aplicações SaaS e no local as aplicações web. Consulte o [lista de compatibilidades de Federação do Azure Active Directory: fornecedores de identidade de terceiros que podem ser utilizados para implementar o início de sessão único](https://msdn.microsoft.com/library/azure/jj679342.aspx) artigo para obter mais detalhes sobre o SSO por terceiros que foram testados pela Microsoft. Esta capacidade permite que a organização implementar uma variedade de cenários B2B enquanto mantém o controlo da gestão de identidades e acessos. No entanto, durante o B2B estruturar o processo, é importante para compreender o método de autenticação que é utilizado pelo parceiro de e confirmar se este método é suportado pelo Azure. Atualmente, os métodos seguintes são suportados pelo Azure AD:

* Segurança Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokens
* Certificados

> [!NOTE]
> ler [protocolos de autenticação do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais detalhes sobre cada protocolo e das respetivas funções no Azure.
>
>

Utilizar o suporte do Azure AD, aplicações empresariais móveis podem utilizar a mesma experiência de autenticação de Mobile Services fácil para permitir que os funcionários iniciar sessão nas suas aplicações móveis com as respetivas credenciais empresariais do Active Directory. Com esta funcionalidade do Azure AD é suportado como um fornecedor de identidade nos Mobile Services em conjunto com os outros fornecedores de identidade já suportadas (que incluem Accounts da Microsoft, ID do Facebook, ID do Google e Twitter ID). Se as aplicações no local, utilizar a credencial do utilizador localizada em AD DS da empresa, o acesso a partir de parceiros e os utilizadores provenientes da nuvem deve ser transparente. Pode gerir o controlo de acesso condicional do utilizador para aplicações web (baseado na nuvem), web API, cloud services da Microsoft, aplicações de SaaS de terceiros e aplicações de cliente (móvel) nativo e tem os benefícios de segurança, auditoria, reporting todos de uma colocar. No entanto, é recomendado para validar a implementação num ambiente de não produção ou com um número limitado de utilizadores.

> [!TIP]
> é importante mencionar que do Azure AD não tem política de grupo que tenha de AD DS. Para impor a política de dispositivos, precisa de uma solução de gestão de dispositivos móveis, tais como [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Depois do utilizador é autenticado com o Azure AD, é importante avaliar o nível de acesso que o utilizador tem. O nível de acesso que o utilizador tem através de um recurso pode variar. Enquanto o Azure AD pode adicionar uma camada adicional de segurança, controlar o acesso a alguns recursos, tenha em atenção que o recurso próprio também pode ter sua própria lista de controlo de acesso em separado, como o controlo de acesso a ficheiros localizados num servidor de ficheiros. A figura seguinte resume os níveis de controlo de acesso que pode ter num cenário híbrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)

Cada interação no diagrama mostrado na figura X representa um cenário de controlo de acesso que pode ser abrangido pelo Azure AD. Abaixo, pode encontrar uma descrição de cada cenário:

1. O acesso condicional para aplicações que estão alojados no local: pode utilizar dispositivos registados com políticas de acesso para aplicações que estão configuradas para utilizar o AD FS com Windows Server 2012 R2. Para mais informações sobre como configurar o acesso condicional no local, consulte [Configurar o Acesso Condicional no local com o Registo de Dispositivos do Azure Active Directory](active-directory-conditional-access-azure-portal.md).

2. Controlo de acesso ao portal do Azure: Azure também lhe permite controlar o acesso ao portal através da utilização de controlo de acesso baseado em funções (RBAC)). Este método permite que a empresa restringir o número de operações que uma pessoa pode fazer no portal do Azure. Ao utilizar o RBAC para controlar o acesso ao portal, administradores de TI pode delegar o acesso utilizando as seguintes abordagens de gestão de acesso:

    * Atribuição de função com base no grupo: pode atribuir acesso a grupos do Azure AD que podem ser sincronizados do Active Directory local. Isto permite-lhe tirar partido de investimentos existentes que sua organização tem efetuadas nas ferramentas e processos para gerir grupos. Também pode utilizar a funcionalidade de gestão de grupo delegada do Azure AD Premium.
    * Utilize funções incorporadas no Azure: pode utilizar três funções — proprietário, Contribuidor, leitor e, para garantir que os utilizadores e grupos têm permissão para efetuar apenas as tarefas que precisam para as respetivas tarefas.
    * Granular acesso a recursos: pode atribuir funções de utilizadores e grupos para uma determinada subscrição, grupo de recursos ou um recurso do Azure individuais como um Web site ou a base de dados. Desta forma, pode certificar-se de que os utilizadores têm acesso a todos os recursos que precisam e sem acesso a recursos que não precisa de gerir.

> [!NOTE]
> Se está a criar aplicações e pretender personalizar o controlo de acesso para os mesmos, também é possível utilizar funções de aplicação do Azure AD para autorização. Reveja esta [WebApp-RoleClaims-DotNet exemplo](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) sobre como criar a sua aplicação para utilizar esta capacidade.
>
>

3. Acesso condicional para aplicações do Office 365 com o Microsoft Intune: administradores de TI podem aprovisionar políticas de dispositivos de acesso condicional para proteger os recursos empresariais, enquanto ao mesmo tempo, permitindo técnicos de informação em dispositivos compatíveis aos serviços. Para obter mais informações, veja [Conditional Access Device Policies for Office 365 services (Políticas de Dispositivos de Acesso Condicional para os serviços do Office 365)](active-directory-conditional-access-device-policies.md).

4. Acesso condicional para aplicações Saas: [esta funcionalidade](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) permite-lhe configurar regras de acesso por aplicação multi-factor authentication e a capacidade de bloquear o acesso para os utilizadores não numa rede fidedigna. Pode aplicar as regras de autenticação multifator em todos os utilizadores que estão atribuídos à aplicação ou apenas para os utilizadores nos grupos de segurança especificados. Os utilizadores podem ser excluídos do requisito de autenticação multifator se estão a aceder a aplicação a partir de um endereço IP que no dentro da organização de rede.

Uma vez que as opções de controlo de acesso de utilizam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para esta tarefa. Certifique-se de que está a tirar partido todas as opções disponíveis para cada cenário que requer que controlam o acesso aos seus recursos.

## <a name="define-incident-response-options"></a>Definir as opções de resposta a incidentes
Pode ajudá-lo do Azure AD que as TI identidade potenciais riscos de segurança no ambiente através da monitorização da atividade do utilizador. IT pode utilizar o acesso do Azure AD e relatórios de utilização para ganhar visibilidade a integridade e a segurança do diretório da sua organização. Com esta informação, um administrador de TI melhor determinar onde pode ficar os possíveis riscos de segurança para que estes podem planear adequadamente a mitigar os riscos.  [Subscrição do Azure AD Premium](active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que pode ativar administrador de TI para obter estas informações. [Relatórios do Azure AD](active-directory-view-access-usage-reports.md) são classificados da seguinte forma:

* **Relatórios de anomalias**: contém eventos de início de sessão que foram identificados como estando anómalas. O objetivo é fazer com que conhecimento essa atividade permitem-lhe tornar uma determinação sobre se um evento é suspeito.
* **Integrado relatório da aplicação**: fornece informações sobre a forma como as aplicações em nuvem estão a ser utilizadas na sua organização. Azure Active Directory oferece integração com milhares de aplicações em nuvem.
* **Relatórios de erros**: indicar erros que podem ocorrer quando o aprovisionamento de contas em aplicações externas.
* **Relatórios de utilizadores específicos**: apresentar dispositivos/início de sessão nos dados de atividade para um utilizador específico.
* **Registos de atividade**: conter um registo de eventos auditados todas as últimas 24 horas, últimos 7 dias, ou últimos 30 dias, bem como alterações ao grupo de atividade e atividade de registo e reposição de palavra-passe.

> [!TIP]
> Outro relatório que também pode ajudar a equipa de resposta a incidentes a trabalhar um caso é o [utilizador com as credenciais obtidas ilicitamente](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) relatório. Este relatório apresenta qualquer correspondências entre a lista de credenciais obtidas ilicitamente e o seu inquilino.
>
>

Outros relatórios incorporados importantes no Azure AD que podem ser utilizados durante uma investigação de resposta a incidentes e são:

* **Atividade de reposição de palavra-passe**: fornecer o administrador aprofundadas quão ativamente a reposição de palavra-passe está a ser utilizada na organização.
* **Atividade de registo de reposição de palavra-passe**: fornece informações para o qual os utilizadores registou os respetivos métodos para a reposição de palavra-passe, e os métodos selecionou.
* **Grupo de atividade**: disponibiliza um histórico de alterações para o grupo (ex: os utilizadores adicionados ou removidos) que foram iniciadas no painel de acesso.

Para além da capacidade de relatórios de núcleo do Azure AD Premium que pode utilizar durante um processo de investigação de resposta a incidentes, equipa de TI pode também tire partido do relatório de auditoria para obter informações tais como:

* Alterações à associação de função (por exemplo, utilizador adicionado à função de Administrador Global)
* Atualizações de credenciais (por exemplo, alterações de palavra-passe)
* Gestão de domínio (por exemplo, verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão de utilizadores (por exemplo, adicionar, remover, atualizar um utilizador)
* Adição ou remoção de licenças

Uma vez que as opções de resposta a incidentes utilizam uma abordagem multicamada, não é aplicável para esta tarefa a comparação entre essas opções. Certifique-se de que lhe é tirar partido de todas as opções disponíveis para cada cenário que requer a utilização de capacidade de relatórios do Azure AD como parte do processo de resposta a incidentes da sua empresa.

## <a name="next-steps"></a>Passos seguintes
[Determinar as tarefas de gestão de identidade híbrida](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
