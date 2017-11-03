---
title: "Do Azure AD Connect: Contas e permissões | Microsoft Docs"
description: "Este tópico descreve as contas utilizadas e criado e as permissões necessárias."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: billmath
ms.openlocfilehash: b45e4096cb68c4b88d2d782427d66a11d1b86b33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Do Azure AD Connect: Contas e permissões
O Assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

* Definições rápidas, o assistente necessita de mais privilégios.  Isto é, de modo a que, pode configurar a configuração facilmente, sem necessidade de criar os utilizadores ou configurar permissões.
* Nas definições personalizadas, o assistente oferece mais opções e escolhas. No entanto, existem algumas situações em que tem de garantir que tem as permissões corretas por si.

## <a name="related-documentation"></a>Documentação relacionada
Se não leu a documentação em [integrar as identidades no local ao Azure Active Directory](../active-directory-aadconnect.md), a tabela seguinte fornece ligações para tópicos relacionados.

|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Definições de instalação rápida
As definições rápidas, o Assistente de instalação pede-lhe as credenciais de administrador de empresa do AD DS.  Isto é, para que o Active Directory no local pode ser configurado com as permissões necessárias para o Azure AD Connect. Se estiver a atualizar do DirSync, as credenciais de Admins de empresa do AD DS são utilizadas para repor a palavra-passe para a conta utilizada pelo DirSync. Também precisa de credenciais de Administrador Global do AD do Azure.

| Página do Assistente | Credenciais recolhidas | Permissões necessárias | Utilizado para |
| --- | --- | --- | --- |
| N/D |Utilizador que executa o Assistente de instalação |Administrador do servidor local |<li>Cria a conta local que é utilizada como o [conta de serviço do motor de sincronização](#azure-ad-connect-sync-service-account). |
| Ligar ao Azure AD |Credenciais de diretório do Azure AD |Função de administrador global no Azure AD |<li>Ativar a sincronização no diretório do Azure AD.</li>  <li>A criação do [conta do Azure AD](#azure-ad-service-account) que é utilizada para operações de sincronização em curso no Azure AD.</li> |
| Ligar ao AD DS |Credenciais de Active Directory no local |Membro do grupo Admins de empresa (EA) no Active Directory |<li>Cria um [conta](#active-directory-account) no Active Directory e atribui permissões para a mesma. Esta conta foi criada é utilizada para ler e escrever informações de diretório durante a sincronização.</li> |

### <a name="enterprise-admin-credentials"></a>Credenciais de administrador de empresa
Estas credenciais são utilizadas apenas durante a instalação e não são utilizadas uma vez concluída a instalação. Pelo administrador de empresa, não é o administrador de domínio deve certificar-se de que as permissões no Active Directory podem ser definidas em todos os domínios.

### <a name="global-admin-credentials"></a>Credenciais de administrador global
Estas credenciais são utilizadas apenas durante a instalação e não são utilizadas uma vez concluída a instalação. É utilizado para criar o [conta do Azure AD](#azure-ad-service-account) utilizados para sincronizar as alterações para o Azure AD. A conta também permite sincronização como uma funcionalidade no Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Uma conta de permissões para o AD DS criada para as definições rápidas
O [conta](#active-directory-account) criados para leitura e escrita para o AD DS tem as seguintes permissões ao criados pelas definições rápidas:

| Permissão | Utilizado para |
| --- | --- |
| <li>Replicar as alterações de diretório</li><li>Alterações de diretório de replicar todos os |Sincronização de palavra-passe |
| Leitura/escrita todas as propriedades de utilizador |Importar e o Exchange híbrido |
| Leitura/escrita todas as propriedades iNetOrgPerson |Importar e o Exchange híbrido |
| Grupo de todas as propriedades de leitura/escrita |Importar e o Exchange híbrido |
| Contacte de todas as propriedades de leitura/escrita |Importar e o Exchange híbrido |
| Repor palavra-passe |Preparação para ativar a repetição de escrita de palavras-passe |

## <a name="custom-settings-installation"></a>Instalação de definições personalizadas
O Azure AD Connect versão 1.1.524.0 e posterior, tem a opção para permitir que o Assistente do Azure AD Connect para criar a conta utilizada para ligar ao Active Directory.  Versões anteriores requerem que a conta for criada antes da instalação. As permissões tem de conceder esta conta podem ser encontradas na [criar a conta do AD DS](#create-the-ad-ds-account). 

| Página do Assistente | Credenciais recolhidas | Permissões necessárias | Utilizado para |
| --- | --- | --- | --- |
| N/D |Utilizador que executa o Assistente de instalação |<li>Administrador do servidor local</li><li>Se utilizar um servidor SQL completo, o utilizador tem de ser administrador de sistema (SA) no SQL Server</li> |Por predefinição, cria a conta local que é utilizada como o [conta de serviço do motor de sincronização](#azure-ad-connect-sync-service-account). A conta é criada apenas quando o administrador não especificar uma conta específica. |
| Instalar serviços de sincronização, opção de conta de serviço |AD ou as credenciais da conta de utilizador local |Utilizador, as permissões são concedidas pelo Assistente de instalação |Se o administrador especifica uma conta, esta conta é utilizada como a conta de serviço para o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório do Azure AD |Função de administrador global no Azure AD |<li>Ativar a sincronização no diretório do Azure AD.</li>  <li>A criação do [conta do Azure AD](#azure-ad-service-account) que é utilizada para operações de sincronização em curso no Azure AD.</li> |
| Ligar os diretórios |Credenciais do Active Directory no local para cada floresta que está ligado ao Azure AD |As permissões dependem as funcionalidades que pode ativa e pode ser encontrado na [criar a conta do AD DS](#create-the-ad-ds-account) |Esta conta é utilizada para ler e escrever informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor na lista, o assistente recolhe as credenciais quando as credenciais de início de sessão do utilizador a executar o assistente não são suficientes estabelecer a ligação |Administrador de domínio |Instalação e configuração da função de servidor do AD FS. |
| Servidores de proxy de aplicações Web |Para cada servidor na lista, o assistente recolhe as credenciais quando as credenciais de início de sessão do utilizador a executar o assistente não são suficientes estabelecer a ligação |Administrador local no computador de destino |Instalação e configuração da função de servidor do WAP. |
| Credenciais de fidedignidade do proxy |As credenciais (as credenciais de proxy utiliza para inscrever um certificado de confiança do FS de confiança do serviço de Federação |Conta de domínio que seja um administrador local do servidor do AD FS |Inscrição inicial do certificado de confiança FS WAP. |
| Página de conta de serviço FS AD, "Utilizar uma opção de conta de utilizador de domínio" |Credenciais de conta de utilizador do AD |Utilizador de domínio |A conta de utilizador do AD cujas credenciais são fornecidas é utilizada como a conta de início de sessão do serviço do AD FS. |

### <a name="create-the-ad-ds-account"></a>Criar a conta do AD DS
A conta que especificar no **ligar os diretórios** página tem de estar presente no Active Directory antes da instalação.  Também tem de ter as permissões necessárias concedidas. O Assistente de instalação não verificar que as permissões e a quaisquer problemas apenas encontram-se durante a sincronização.

As permissões necessárias depende de funcionalidades opcionais, ativar. Se tiver vários domínios, é possível conceder as permissões para todos os domínios na floresta. Se não ativar qualquer uma destas funcionalidades, a predefinição **utilizador de domínio** permissões são suficientes.

| Funcionalidade | Permissões |
| --- | --- |
| funcionalidade de msDS-ConsistencyGuid |Permissões de escrita para o atributo msDS-ConsistencyGuid documentado [conceitos de Design - utilizando msDS-ConsistencyGuid como sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Sincronização de palavra-passe |<li>Replicar as alterações de diretório</li>  <li>Alterações de diretório de replicar todos os |
| Implementação híbrida do Exchange |Permissões de escrita para os atributos documentados [repetição de escrita do Exchange híbrida](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Pasta de público de correio do Exchange |Permissões de leitura para os atributos documentados [pasta pública de correio eletrónico Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Permissões de escrita para os atributos documentados [introdução à gestão de palavra-passe](../active-directory-passwords-writeback.md) para os utilizadores. |
| Repetição de escrita do dispositivo |Permissões concedidas com um script do PowerShell, conforme descrito em [repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md). |
| Repetição de escrita do grupo |Ler, criar, atualizar e eliminar o grupo de objetos para sincronizados **grupos do Office 365**.  Para obter mais informações consulte [repetição de escrita do grupo](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, terá as seguintes permissões:

>[!IMPORTANT]
>A partir da compilação 1.1.484, do Azure AD Connect apresentou um erro de regressão que necessita de permissões de administrador do sistema para atualizar a base de dados do SQL Server.  Este erro ainda está presente na compilação mais recente 1.1.614.  Se estiver a atualizar para esta compilação, precisa de permissões de administrador do sistema.  Permissões de dbo não são suficientes.  Se tentar atualizar o Azure AD Connect sem ter permissões de administrador do sistema, a atualização falhará e o Azure AD Connect deixarão de funcionar corretamente posteriormente.  A Microsoft está ciente disto e está a funcionar para corrigir isto.


| Principal | Permissões necessárias | Utilizado para |
| --- | --- | --- |
| Utilizador que executa o Assistente de instalação |Administrador do servidor local |Os binários de atualização. |
| Utilizador que executa o Assistente de instalação |Membro de ADSyncAdmins |Efetue alterações em regras de sincronização e outra configuração. |
| Utilizador que executa o Assistente de instalação |Se utilizar um servidor SQL completo: DBO (ou semelhante) da base de dados de motor de sincronização |Efetue alterações ao nível da base de dados, tais como a atualização tabelas com colunas novas. |

## <a name="more-about-the-created-accounts"></a>Mais informações sobre as contas criadas
### <a name="active-directory-account"></a>Conta do Active Directory
Se utilizar as definições rápidas, é criada uma conta no Active Directory que é utilizada para sincronização. A conta criada encontra-se no domínio de raiz de floresta no contentor de utilizadores e tem o nome o prefixo **MSOL_**. A conta for criada com uma palavra-passe complexa desde que não expire. Se tiver uma política de palavra-passe no seu domínio, certifique-se muito e palavras-passe complexas deverá ser permitidas para esta conta.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Se utilizar definições personalizadas, em seguida, é responsável pela criação da conta antes de iniciar a instalação.

### <a name="azure-ad-connect-sync-service-account"></a>Conta de serviço de sincronização do Azure AD Connect
O serviço de sincronização pode ser executado contas diferentes. Pode ser executado sob um **conta de serviço do Virtual** (VSA), um **conta de serviço gerida de grupo** (gMSA/sMSA) ou uma conta de utilizador normais. As opções suportadas foram alteradas com o 2017 versão de Abril de ligar quando fizer uma instalação de raiz. Se atualizar a partir de uma versão anterior do Azure AD Connect, não estão disponíveis as opções adicionais.

| Tipo de conta | Opção de instalação | Descrição |
| --- | --- | --- |
| [Conta de serviço virtual](#virtual-service-account) | Abril de 2017 rápida e personalizadas e posterior | Esta é a opção utilizada para todas as instalações rápidas, exceto as instalações num controlador de domínio. Para personalizar, é a opção predefinida, a menos que outra opção é utilizada. |
| [Conta de serviço geridas de grupo](#group-managed-service-account) | Abril de 2017 personalizado e posterior | Se utilizar um SQL server remoto, em seguida, recomendamos que utilize uma conta de serviço geridas de grupo. |
| [Conta de utilizador](#user-account) | Abril de 2017 rápida e personalizadas e posterior | Uma conta de utilizador, o prefixo AAD_ apenas é criada durante a instalação quando instalado no Windows Server 2008 e quando instalado num controlador de domínio. |
| [Conta de utilizador](#user-account) | Março de 2017 rápida e personalizadas e versões anteriores | É criada uma conta local, o prefixo AAD_ durante a instalação. Quando utilizar instalação personalizada, é possível especificar outra conta. |

Se utilizar ligar com uma compilação de 2017 Março ou anterior, em seguida, deve não repor a palavra-passe da conta de serviço, uma vez que o Windows destroys as chaves de encriptação por motivos de segurança. Não é possível alterar a conta para qualquer outra conta sem reinstalar o Azure AD Connect. Se atualizar para uma compilação de 2017 Abril ou posterior, em seguida, é suportada alterar a palavra-passe da conta de serviço, mas não é possível alterar a conta utilizada.

> [!Important]
> A conta de serviço só pode ser definida na primeira instalação. Não é suportada para alterar a conta de serviço, uma vez concluída a instalação.

Esta é uma tabela de predefinição, as opções de recomendada e suportadas para a conta de serviço de sincronização.

Legenda:

- **Negrito** indica a opção predefinida e na maior parte dos casos a opção recomendada.
- *Italic* indica a opção recomendada quando não é a opção predefinida.
- 2008 - opção de predefinida quando instalado no Windows Server 2008
- Não-bold - opção suportado
- Conta local - da conta de utilizador Local no servidor
- Conta de domínio - conta de utilizador de domínio
- sMSA - [conta de serviço gerida autónoma](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [conta de serviço geridas de grupo](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personalizado | SQL Server remoto</br>Personalizado |
| --- | --- | --- | --- |
| **máquina autónomo/grupo de trabalho** | Não suportado | **VSA**</br>Conta local (2008)</br>Conta local |  Não suportado |
| **máquina associada ao domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta de domínio</br>sMSA, gMSA | **gMSA**</br>Conta de domínio |
| **Controlador de domínio** | **Conta de domínio** | *gMSA*</br>**Conta de domínio**</br>sMSA| *gMSA*</br>**Conta de domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem uma palavra-passe e for gerido pelo Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser utilizada com cenários em que o motor de sincronização e o SQL Server estão no mesmo servidor. Se utilizar o SQL Server remoto, em seguida, recomendamos que utilize um [conta de serviço gerida de grupo](#managed-service-account) em vez disso.

Esta funcionalidade requer o Windows Server 2008 R2 ou posterior. Se instalar o Azure AD Connect no Windows Server 2008, em seguida, a instalação utilizará um [conta de utilizador](#user-account) em vez disso.

#### <a name="group-managed-service-account"></a>Conta de serviço geridas de grupo
Se utilizar um SQL server remoto, em seguida, recomendamos a utilização de um **conta de serviço gerida de grupo**. Para obter mais informações sobre como preparar o Active Directory para a conta de serviço geridas de grupo, consulte [descrição geral a contas de serviço do grupo gerido](https://technet.microsoft.com/library/hh831782.aspx).

Para utilizar esta opção, o [instalar os componentes necessários](active-directory-aadconnect-get-started-custom.md#install-required-components) página, selecione **utilizar uma conta de serviço existente**e selecione **conta de serviço gerida**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Também é suportado para utilizar um [autónomo conta de serviço gerida](https://technet.microsoft.com/library/dd548356.aspx). No entanto, estes apenas podem ser utilizados no computador local e não existe nenhuma vantagem utilizá-los através da conta de serviço virtuais predefinida.

Esta funcionalidade requer o Windows Server 2012 ou posterior. Se pretender utilizar um sistema operativo anterior e utilizar o SQL Server remoto, em seguida, tem de utilizar um [conta de utilizador](#user-account).

#### <a name="user-account"></a>Conta de utilizador
Uma conta de serviço local é criada pelo Assistente de instalação (a menos que especifique a conta a utilizar nas definições personalizadas). A conta é adicionado como prefixo **AAD_** e utilizado para o serviço de sincronização real para executar como. Se instalar o Azure AD Connect num controlador de domínio, é criada a conta no domínio. O **AAD_** conta de serviço tem de estar localizada no domínio se:
   - utilizar um servidor remoto com o SQL server
   - utilizar um proxy que requer autenticação

![Conta de serviço de sincronização](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

A conta for criada com uma palavra-passe complexa desde que não expire.

Esta conta é utilizada para armazenar as palavras-passe para as outras contas de forma segura. Estas outras contas palavras-passe é armazenados de forma encriptada na base de dados. As chaves privadas para as chaves de encriptação são protegidas com a encriptação de chave secreta de serviços criptográficos utilizando a API do Windows Data Protection (DPAPI).

Se utilizar um SQL Server total, em seguida, a conta de serviço é ao DBO da base de dados criada para o motor de sincronização. O serviço não irão funcionar conforme pretendido com quaisquer outras permissões. Também é criado um início de sessão do SQL Server.

A conta também tem permissões para os ficheiros, chaves de registo e outros objetos relacionados com o motor de sincronização.

### <a name="azure-ad-service-account"></a>Conta de serviço do AD FS
É criada uma conta no Azure AD para utilizar o serviço de sincronização. Esta conta pode ser identificada pelo respetivo nome de apresentação.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

O nome do servidor que é utilizada a conta no pode ser identificado na segunda parte do nome de utilizador. Na imagem, o nome do servidor é FABRIKAMCON. Se tiver servidores de teste, cada servidor tem a sua própria conta.

A conta de serviço é criada com uma palavra-passe complexa desde que não expire. É concedido uma função especial **contas de sincronização de diretórios** que tenha apenas as permissões para executar tarefas de sincronização de diretórios. Não é possível conceder esta função incorporada especial fora do Assistente do Azure AD Connect. O portal do Azure mostra esta conta com a função **utilizador**.

Não há um limite de 20 contas de serviço de sincronização no Azure AD. Para obter a lista de contas de serviço do Azure AD existentes no seu Azure AD, execute o seguinte cmdlet do PowerShell do Azure AD:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover AD do Azure não utilizado contas de serviço, execute o seguinte cmdlet do PowerShell do Azure AD:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](../active-directory-aadconnect.md).
