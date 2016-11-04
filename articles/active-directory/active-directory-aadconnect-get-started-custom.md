---
title: 'Azure AD Connect: Instalação personalizada | Microsoft Docs'
description: Este documento fornece detalhes sobre as opções de instalação personalizada do Azure AD Connect. Utilize estas instruções para instalar o Active Directory através do Azure AD Connect.
services: active-directory
keywords: o que é o Azure AD Connect, instale o Active Directory, componentes precisos para o Azure AD
documentationcenter: ''
author: andkjell
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/13/2016
ms.author: billmath;andkjell

---
# Instalação personalizada do Azure AD Connect
As **Definições personalizadas** do Azure AD Connect são utilizadas quando pretende mais opções para a instalação. São utilizadas se tiver várias florestas ou se pretender configurar funcionalidades opcionais não abrangidas na instalação rápida. São utilizadas em todos os casos em que a opção [**instalação rápida**](active-directory-aadconnect-get-started-express.md) não satisfaz a sua implementação ou topologia.

Antes de começar a instalar o Azure AD Connect, certifique-se de que [transferiu o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) e concluiu os pré-requisitos indicados em [Azure AD Connect: Hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md). Certifique-se também de que tem disponíveis as contas necessárias, conforme descrito em [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md).

Se as definições personalizadas não coincidirem com a topologia, por exemplo, para atualizar o DirSync, consulte [documentação relacionada](#related-documentation) para obter outros cenários.

## Instalação de definições personalizadas do Azure AD Connect
### Definições Rápidas
Nesta página, clique em **Personalizar** para iniciar uma instalação de definições personalizadas.

### Instalar os componentes necessários
Quando instalar os serviços de sincronização, pode deixar a secção de configuração opcional desmarcada e o Azure AD Connect configura tudo automaticamente. Configura uma instância do SQL Server 2012 Express LocalDB, cria os grupos adequados e atribui permissões. Se pretender alterar as predefinições, pode utilizar a tabela seguinte para entender as opções de configuração opcionais que estão disponíveis.

![Componentes necessários](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

| Configuração opcional | Descrição |
| --- | --- |
| Utilizar um SQL Server existente |Permite-lhe especificar o nome do SQL Server e o nome da instância. Escolha esta opção se já tiver um servidor de base de dados que pretende utilizar. Introduza o nome da instância, seguido de uma vírgula e do número de porta em **Nome da Instância**, caso o SQL Server não tenha a navegação ativada. |
| Utilizar uma conta de serviço existente |Por predefinição, o Azure AD Connect cria uma conta de serviço local para ser utilizada pelos serviços de sincronização. A palavra-passe é gerada automaticamente e é desconhecida da pessoa que está a instalar o Azure AD Connect. Se utilizar um SQL Server remoto ou um proxy que exija a autenticação, tem de ter uma conta de serviço no domínio e conhecer a palavra-passe. Nesses casos, introduza a conta de serviço a utilizar. Certifique-se de que o utilizador que está a executar a instalação é um SA no SQL Server, para possa ser criado um início sessão para a conta de serviço. Consulte [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation) |
| Especificar grupos de sincronização personalizados |Por predefinição, o Azure AD Connect cria quatro grupos locais no servidor quando são instalados os serviços de sincronização. Estes grupos são: grupo Administradores, grupo Operadores, grupo Procura e grupo Reposição de Palavra-passe. Pode especificar aqui os seus próprios grupos. Os grupos têm de ser locais no servidor e não podem estar localizados no domínio. |

### Início de sessão do utilizador
Depois de instalar os componentes necessários, é-lhe pedido que selecione o método de início de sessão único dos utilizadores. A tabela seguinte fornece uma breve descrição das opções disponíveis. Para obter uma descrição completa dos métodos de início de sessão, consulte [Início de sessão do utilizador](active-directory-aadconnect-user-signin.md).

![Início de Sessão de Utilizador](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

| Opção Início de Sessão Único | Descrição |
| --- | --- |
| Sincronização de Palavra-passe |Os utilizadores podem iniciar sessão nos Cloud Services da Microsoft, como o Office 365, utilizando a mesma palavra-passe que utilizam na respetiva rede no local. As palavras-passe dos utilizadores são sincronizadas para o Azure AD como um hash de palavra-passe e a autenticação ocorre na nuvem. Consulte [Sincronização de palavras-passe](active-directory-aadconnectsync-implement-password-synchronization.md) para obter mais informações. |
| Federação com o AD FS |Os utilizadores podem iniciar sessão nos Cloud Services da Microsoft, como o Office 365, utilizando a mesma palavra-passe que utilizam na respetiva rede no local.  Os utilizadores serão redirecionados para a respetiva instância do AD FS no local para iniciarem sessão e a autenticação ocorre no local. |
| Não configurar |Nenhuma destas funcionalidades é instalada e configurada. Escolha esta opção se já tiver instalado um servidor de federação de terceiros ou outra solução existente. |

### Ligar ao Azure AD
No ecrã Ligar ao Azure AD, introduza uma conta de administrador global e a palavra-passe. Se tiver selecionado **Federação com o AD FS** na página anterior, não inicie sessão com uma conta num domínio que pretenda ativar para federação. Uma das recomendações é utilizar uma conta no domínio predefinido **onmicrosoft.com**, incluído com o diretório do Azure AD.

Esta conta é utilizada apenas para criar uma conta de serviço no Azure AD e deixa de ser utilizada uma vez concluído o assistente.  
![Início de Sessão de Utilizador](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Se a conta de administrador global tiver a MFA ativada, terá de fornecer novamente a palavra-passe no pop-up início de sessão e completar o desafio MFA. O desafio pode ser fornecer um código de verificação ou uma chamada telefónica.  
![Início de Sessão de Utilizador no MFA](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

A conta de administrador global também pode ter a opção [Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) ativada.

Se receber um erro e tiver problemas com a conectividade, veja [Resolver problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).

## Páginas na secção Sincronização
### Ligar os diretórios
Para se ligar ao Serviço de Domínio do Active Directory, o Azure AD Connect tem as credenciais de uma conta com permissões suficientes. Pode introduzir a parte do domínio no formato NetBios ou FQDN, ou seja, FABRIKAM\syncuser ou fabrikam.com\syncuser. Esta conta pode ser uma conta de utilizador normal, porque precisa apenas das permissões de leitura predefinidas. No entanto, dependendo do seu cenário, poderá precisar de mais permissões. Para obter mais informações, consulte [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)

![Ligar o Diretório](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### Configuração do início de sessão do Azure AD
Esta página permite-lhe rever os domínios UPN presentes no AD DS no local e que foram verificados no Azure AD. Esta página também lhe permite configurar o atributo a utilizar para userPrincipalName.

![Domínios não verificados](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Reveja todos os domínios marcados como **Não Adicionado** e **Não Verificado**. Certifique-se de que os domínios que utiliza foram verificados no Azure AD. Quando tiver verificado os domínios, clique no símbolo de atualização. Para obter mais informações, consulte [Adicionar e verificar o domínio](active-directory-add-domain.md)

**UserPrincipalName** -o atributo userPrincipalName é o atributo que os utilizadores utilizam quando iniciam sessão no Azure AD e no Office 365. O domínio utilizado, também conhecido como sufixo UPN, deve ser verificado no Azure AD para que os utilizadores possam ser sincronizados. A Microsoft recomenda que mantenha o atributo predefinido userPrincipalName. Se este atributo não for encaminhável e não puder ser verificado, é possível selecionar outro atributo. Pode, por exemplo, selecionar o correio eletrónico como o atributo que contém o ID de início de sessão. A utilização de um atributo diferente de userPrincipalName é conhecida como **ID Alternativo**. O valor do atributo ID Alternativo tem de seguir a norma RFC822. Um ID Alternativo pode ser utilizado na sincronização de palavras-passe e na federação.

> [!WARNING]
> A utilização de um ID Alternativo não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, veja [Configurar ID de Início de Sessão Alternativo](https://technet.microsoft.com/library/dn659436.aspx).
> 
> 

### Filtragem de domínios e de UOs
Por predefinição, todos os domínios e UOs são sincronizados. Se houver domínios ou UOs que não pretende sincronizar com o Azure AD, pode anular a seleção destes domínios e UOs.  
![Filtragem de DomainOU](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) Esta página no assistente está a configurar filtragem baseada no domínio. Para obter mais informações, consulte [Filtragem baseada no domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).

Também é possível que alguns domínios não estejam acessíveis devido a restrições de firewall. Estes domínios estão desmarcados por predefinição e têm um aviso.  
![Domínios inacessíveis](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Se vir este aviso, certifique-se de que estes domínios são de facto inacessível e que o aviso é esperado.

### Identificar os utilizadores de forma exclusiva
A funcionalidade Correspondência entre florestas permite-lhe definir o modo como são representados no Azure AD os utilizadores das florestas do AD DS. Um utilizador pode ser representado apenas uma vez em todas as florestas ou ter uma combinação de contas ativadas e desativadas. O utilizador também pode ser representado como um contacto em algumas florestas.

![Exclusivo](./media/active-directory-aadconnect-get-started-custom/unique.png)

| Definição | Descrição |
| --- | --- |
| [Os utilizadores são representados apenas uma vez em todas as florestas](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) |Todos os utilizadores são criados como objetos individuais no Azure AD. Os objetos não estão associados no metaverso. |
| [Atributo de correio](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) |Esta opção associa utilizadores e contactos se o atributo de correio tiver o mesmo valor em florestas diferentes. Utilize esta opção quando os contactos foram criados utilizando GALSync. |
| [ObjectSID e msExchangeMasterAccountSID / msRTCSIP-OriginatorSid](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) |Esta opção associa um utilizador ativado numa floresta conta a um utilizador desativado numa floresta de recursos. No Exchange, esta configuração é conhecida como uma caixa de correio ligada. Também pode ser utilizada esta opção se utilizar apenas o Lync e o Exchange não estiver presente na floresta de recursos. |
| sAMAccountName e MailNickName |Esta opção associa atributos em que se espera que o ID de início de sessão do utilizador possa ser encontrado. |
| Um atributo específico |Esta opção permite-lhe selecionar o seu próprio atributo. **Limitação:** certifique-se de que escolhe um atributo que já pode ser encontrado no metaverso. Se escolher um atributo personalizado (não no metaverso), não é possível concluir o assistente. |

**Âncora de origem** - sourceAnchor é um atributo que é imutável durante a duração de um objeto de utilizador. É a chave primária da ligação do utilizador no local com o utilizador no Azure AD. Uma vez que o atributo não pode ser alterado, terá de planear um bom atributo para utilizar. Um bom candidato é objectGUID. Este atributo não é alterado, a menos que a conta de utilizador seja movida entre florestas/domínios. Num ambiente de várias florestas em que se movem contas entre florestas, tem de ser utilizado outro atributo, como um atributo com o campo IDdeEmpregado. Evite atributos que se alteram quando uma pessoa se casa ou muda de atribuições. Não é possível utilizar atributos com um @, sendo assim o endereço de correio eletrónico e userPrincipalName não podem ser utilizados. O atributo é também sensível a maiúsculas e minúsculas, por isso, ao mover um objeto entre florestas, certifique-se de que preserva as maiúsculas/minúsculas. Os atributos binários são codificados em base64, mas outros tipos de atributo permanecem no seu estado não codificado. Em cenários de federação e em algumas interfaces do Azure AD, este atributo é também conhecido como immutableID. Poderá encontrar mais informações acerca da âncora de origem no artigo [Conceitos de design](active-directory-aadconnect-design-concepts.md#sourceAnchor).

### Filtragem de sincronização baseada em grupos
A filtragem na funcionalidade de grupos permite-lhe sincronizar apenas um pequeno subconjunto de objetos para uma implementação piloto. Para utilizar esta funcionalidade, crie um grupo para este efeito no Active Directory no local. Em seguida, adicione os utilizadores e grupos que devem ser sincronizados com o Azure AD como membros diretos. Pode adicionar e remover posteriormente utilizadores deste grupo para manter a lista de objetos que deve estar presente no Azure AD. Todos os objetos que pretende sincronizar têm de ser membros diretos do grupo. Os utilizadores, grupos, contactos e computadores/dispositivos têm de ser todos membros diretos. A associação a grupos aninhados não é resolvida. Ao adicionar um grupo como membro, só é adicionado o grupo em si e não os seus membros.

![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/filter2.png)

> [!WARNING]
> Esta funcionalidade destina-se apenas a suportar uma implementação piloto. Não a utilize numa implementação de produção autêntica.
> 
> 

Numa implementação de produção autêntica, será difícil manter um único grupo com todos os objetos a sincronizar. Deve utilizar, em vez disso, um dos métodos descritos em [Configurar filtragem](active-directory-aadconnectsync-configure-filtering.md).

### Funcionalidades Opcionais
Este ecrã permite-lhe selecionar as funcionalidades opcionais para os seus cenários específicos.

![Funcionalidades opcionais](./media/active-directory-aadconnect-get-started-custom/optional.png)

> [!WARNING]
> Se tiver atualmente o DirSync ou o Azure AD Sync ativo, não ative nenhuma das funcionalidades de repetição de escrita no Azure AD Connect.
> 
> 

| Funcionalidades Opcionais | Descrição |
| --- | --- |
| Implementação Híbrida do Exchange |A funcionalidade Implementação Híbrida do Exchange permite a coexistência de caixas de correio do Exchange no local e no Office 365. O Azure AD Connect está a sincronizar um conjunto específico de [atributos](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) do Azure AD para o diretório no local. |
| Aplicação Azure AD e filtragem de atributos |Ao ativar a aplicação Azure AD e a filtragem de atributos, o conjunto de atributos sincronizados pode ser personalizado. Esta opção adiciona mais duas páginas de configuração ao assistente. Para obter mais informações, consulte [Aplicação Azure AD e filtragem de atributos](#azure-ad-app-and-attribute-filtering). |
| Sincronização de palavras-passe |Se tiver selecionado a federação como solução de início de sessão, poderá ativar esta opção. A sincronização de palavras-passe pode ser utilizada como uma opção de cópia de segurança. Para obter mais informações, consulte [Sincronização de palavras-passe](active-directory-aadconnectsync-implement-password-synchronization.md). |
| Repetição de escrita de palavras-passe |Ao ativar a repetição de escrita de palavras-passe, as alterações de palavras-passe com origem no Azure AD são reescritas no diretório no local. Para mais informações, consulte [Introdução à gestão de palavras-passe](active-directory-passwords-getting-started.md) |
| Repetição de escrita do grupo |Se utilizar a funcionalidade **Grupos do Office 365**, pode ter estes grupos representados no Active Directory no local. Esta opção só está disponível se tiver o Exchange presente no Active Directory no local. Para obter mais informações, consulte [Repetição de escrita do grupo](active-directory-aadconnect-feature-preview.md#group-writeback). |
| Repetição de escrita do dispositivo |Permite-lhe a repetição de escrita de objetos de dispositivo no Azure AD para o Active Directory no local para cenários de acesso condicional. Para mais informações, consulte [Ativar a repetição de escrita do dispositivo no Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md). |
| Sincronização de atributos de extensões de diretórios |Ao ativar a sincronização de atributos de extensões de diretórios, os atributos especificados são sincronizados com o Azure AD. Para obter mais informações, consulte [Extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md). |

### Aplicação Azure AD e filtragem de atributos
Se pretender limitar os atributos a sincronizar com o Azure AD, comece por selecionar os serviços que está a utilizar. Se efetuar alterações de configuração nesta página, um novo serviço terá de ser explicitamente selecionado executando novamente o assistente de instalação.

![Aplicações de funcionalidades opcionais](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Com base nos serviços selecionados no passo anterior, esta página mostra todos os atributos que são sincronizados. Esta lista é uma combinação de todos os tipos de objetos que estão a ser sincronizados. Se existirem alguns atributos específicos que não precisa de sincronizar, pode anular a sua seleção.

![Atributos de funcionalidades opcionais](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

> [!WARNING]
> A remoção de atributos pode afetar a funcionalidade. Para obter as melhores práticas e recomendações, consulte [tributos sincronizados](active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).
> 
> 

### Sincronização de atributos de Extensões de Diretórios
Pode expandir o esquema no Azure AD com atributos personalizados adicionados através da organização ou outros atributos no Active Directory. Para utilizar esta funcionalidade, selecione **Sincronização de atributos de extensão de diretórios** na página **Funcionalidades Opcionais**. Pode selecionar nesta página mais atributos para sincronizar.

![Extensões de diretórios](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Para obter mais informações, consulte [Extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md).

## Configurar a federação com o AD FS
Configurar o AD FS com o Azure AD Connect é simples, bastam apenas alguns cliques. É necessário o seguinte antes da configuração.

* Um servidor Windows Server 2012 R2 para o servidor de federação com a gestão remota ativada
* Um servidor Windows Server 2012 R2 para o servidor Proxy de Web Apps com a gestão remota ativada
* Um certificado SSL para o nome do serviço de federação que pretende utilizar (por exemplo, sts.contoso.com)

### Pré-requisitos de configuração do AD FS
Para configurar o farm do AD FS com o Azure AD Connect, certifique-se de que o WinRM está ativado nos servidores remotos. Além disso, verifique também os requisitos de portas listados na [Tabela 3 – Servidores do Azure AD Connect e de Federação/WAP](active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap).

### Criar um novo farm do AD FS ou utilizar um existente
Pode utilizar um farm do AD FS existente ou pode optar por criar um novo. Se optar por criar um novo, é necessário fornecer o certificado SSL. Se o certificado SSL estiver protegido por uma palavra-passe, é-lhe pedida a palavra-passe.

![Farm do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Se optar por utilizar um farm do AD FS existente, é direcionado diretamente para a configuração da relação de confiança entre o AD FS e o Azure AD.

### Especificar os servidores do AD FS
Introduza os servidores em que pretende instalar o AD FS. Pode adicionar um ou mais servidores com base nas suas necessidades de planeamento da capacidade. Associe todos os servidores ao Active Directory antes de efetuar esta configuração. A Microsoft recomenda a instalação de um único servidor do AD FS para implementações de teste e piloto. Em seguida, adicione e implemente mais servidores para satisfazer as suas necessidades de dimensionamento, executando novamente o Azure AD Connect após a configuração inicial.

> [!NOTE]
> Certifique-se de que todos os servidores estão associados a um domínio do AD antes de efetuar esta configuração.
> 
> 

![Servidores do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### Especificar os servidores Proxy de Web Apps
Introduza os servidores que pretende ter como servidores proxy de Web Apps. O servidor proxy de Web Apps está implementado na rede de perímetro (com acesso à extranet) e suporta pedidos de autenticação da extranet.
 Pode adicionar um ou mais servidores com base nas suas necessidades de planeamento da capacidade. A Microsoft recomenda a instalação de um único servidor proxy de aplicação Web para implementações de teste e piloto. Em seguida, adicione e implemente mais servidores para satisfazer as suas necessidades de dimensionamento, executando novamente o Azure AD Connect após a configuração inicial. Recomenda-se ter um número equivalente de servidores proxy para satisfazer a autenticação da intranet.

> [!NOTE]
> <li> Se a conta utilizada não for um administrador local nos servidores AD FS, ser-lhe-ão pedidas as credenciais de administrador.</li>
> <li> Antes de executar este passo, certifique-se de que existe conectividade HTTP/HTTPS entre o servidor do Azure AD Connect e o servidor Proxy de Web Apps.</li>
> <li> Certifique-se de que existe conectividade HTTP/HTTPS entre o Servidor de Web Apps e o servidor do AD FS, para permitir a passagem de pedidos de autenticação.</li>
> 
> 

![Aplicação Web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

É-lhe pedido que introduza as credenciais para que o servidor de Web Apps possa estabelecer uma ligação segura ao servidor do AD FS. Estas credenciais têm de pertencer a um administrador local no servidor do AD FS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### Especifique a conta de serviço para o serviço do AD FS
O serviço do AD FS requer uma conta de serviço de domínio para autenticar utilizadores e pesquisar informações sobre utilizadores no Active Directory. Pode suportar dois tipos de contas de serviço:

* **Conta de Serviço Gerida de Grupo** – introduzida nos Serviços de Domínio do Active Directory com o Windows Server 2012. Este tipo de conta fornece serviços, como o AD FS, uma conta única sem necessidade de atualizar regularmente a palavra-passe de conta. Utilize esta opção se já tiver controladores de domínio do Windows Server 2012 no domínio a que pertencem os servidores do AD FS.
* **Conta de Utilizador de Domínio** – este tipo de conta requer uma palavra-passe e a atualização regular da palavra-passe, quando a palavra-passe é alterada ou expira. Utilize esta opção apenas se não tiver controladores de domínio do Windows Server 2012 no domínio a que pertencem os servidores do AD FS.

Se tiver selecionado a Conta de Serviço Gerida de Grupo e esta funcionalidade nunca tiver sido utilizada no Active Directory, ser-lhe-ão pedidas as credenciais de Administrador de Empresa. Estas credenciais são utilizadas para iniciar o armazenamento de chaves e ativar a funcionalidade no Active Directory.

![Conta de Serviço do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### Selecionar o domínio do Azure AD que pretende federar
Esta configuração é utilizada para configurar a relação de federação entre o AD FS e o Azure AD. Configura o AD FS para emitir tokens de segurança para o Azure AD e configura o Azure AD para confiar nos tokens desta instância específica do AD FS. Esta página permite-lhe apenas configurar um domínio único na instalação inicial. Pode configurar mais domínios posteriormente, executando novamente o Azure AD Connect.

![Domínio do Azure AD](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### Verificar o domínio do Azure AD selecionado para federação
Quando seleciona o domínio a ser federado, o Azure AD Connect fornece-lhe as informações necessárias para verificar um domínio não verificado. Consulte [Adicionar e verificar o domínio](active-directory-add-domain.md) para saber como utilizar estas informações.

![Domínio do Azure AD](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

> [!NOTE]
> O AD Connect tenta verificar o domínio durante a fase de configuração. Se continuar a configurar sem adicionar os registos DNS necessários, o assistente não conseguirá concluir a configuração.
> 
> 

## Páginas de configuração e verificação
A configuração ocorre nesta página.

> [!NOTE]
> Antes de prosseguir com a instalação e se tiver configurado a federação, certifique-se de que configurou a [Resolução de nomes dos servidores de federação](active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers).
> 
> 

![Preparado para configurar](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### Modo de teste
É possível configurar um novo servidor de sincronização em paralelo, com modo de teste. É apenas suportado para ter um servidor de sincronização a exportar para um diretório na nuvem. Mas se pretender mover de outro servidor, por exemplo um a executar o DirSync, pode ativar o Azure AD Connect no modo de teste. Uma vez ativado, o motor de sincronização importa e sincroniza os dados normalmente, mas não exporta nada para o Azure AD ou o AD. A sincronização de palavras-passe de funcionalidades e a repetição de escrita de palavras-passe estão desativadas no modo de teste.

![Modo de teste](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

No modo de teste, é possível efetuar as alterações necessárias no motor de sincronização e rever o que está prestes a ser exportado. Quando lhe parecer que a configuração está boa, execute novamente o assistente de instalação e desative o modo de teste. Os dados são exportados para o Azure AD deste servidor. Não se esqueça de desativar ao mesmo tempo o outro servidor para que haja apenas um servidor a exportar ativamente.

Para obter mais informações, consulte [Modo de teste](active-directory-aadconnectsync-operations.md#staging-mode).

### Verificar a configuração de federação
O Azure AD Connect verifica as definições de DNS quando clica no botão Verificar.

![Concluir](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Verificar](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

Adicionalmente, execute os seguintes passos de verificação:

* Confirme que pode iniciar sessão a partir de um browser num computador associado ao domínio na intranet: ligue-se a https://myapps.microsoft.com e verifique o início de sessão com a sua conta com sessão iniciada. A conta de administrador do AD DS incorporada não está sincronizada e não pode ser utilizada para a verificação.
* Confirme que pode iniciar sessão a partir de um dispositivo da extranet. Numa máquina de uso doméstico ou num dispositivo móvel, ligue-se a https://myapps.microsoft.com e forneça as credenciais.
* Valide o início de sessão de cliente avançado. Ligue-se a https://testconnectivity.microsoft.com, escolha o separador **Office 365** e escolha o **Teste de Início de Sessão Único do Office 365**.

## Passos seguintes
Uma vez concluída a instalação, termine e inicie novamente sessão no Windows antes de utilizar o Synchronization Service Manager ou Editor de Regras de Sincronização.

Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](active-directory-aadconnectsync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

## Documentação relacionada
| Tópico |
| --- | --- |
| Visão geral do Azure AD Connect |
| Instalar utilizando as definições rápidas |
| Atualização do DirSync |
| Contas utilizadas para a instalação |

<!--HONumber=Sep16_HO3-->


