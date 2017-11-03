---
title: "Do Azure AD Connect: Histórico de lançamento de versões | Microsoft Docs"
description: "Este artigo apresenta uma lista de todas as versões do Azure AD Connect e o Azure AD Sync"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2017
ms.author: billmath
ms.openlocfilehash: 51cdb60d1967f2a4a4ebadbd2717fd580a79da6b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-connect-version-release-history"></a>Do Azure AD Connect: Histórico de lançamento de versões
A equipa do Azure Active Directory (Azure AD) atualiza regularmente o Azure AD Connect com novas funcionalidades e funções. Nem todas as adições são aplicáveis a todos os público.

Este artigo foi concebido para ajudar a manter um registo das versões que tenham sido publicadas e para compreender se tem de atualizar para a versão mais recente ou não.

Esta é uma lista de tópicos relacionados:


Tópico |  Detalhes
--------- | --------- |
Passos para atualizar a partir do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](active-directory-aadconnect-upgrade-previous-version.md) versão do Azure AD Connect.
Permissões necessárias | Para as permissões necessárias para aplicar uma atualização, consulte [contas e permissões](./active-directory-aadconnect-accounts-permissions.md#upgrade).
Transferência| [Transferir o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).


## <a name="116490"></a>1.1.649.0
Estado: de 2017 27 de Outubro

>[!NOTE]
>Este compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problema fixo
* Corrigido um problema de compatibilidade de versão entre o Azure AD Connect e o agente do Azure AD Connect Health (para sincronização). Este problema afeta os clientes que estiver a efetuar a atualização no local do Azure AD Connect versão 1.1.647.0, mas atualmente tem o agente de estado de funcionamento da versão 3.0.127.0. Após a atualização, o agente de estado de funcionamento já não pode enviar dados de estado de funcionamento sobre o serviço de sincronização ligar do Azure AD para o serviço de estado de funcionamento do Azure AD. Com esta correção está instalado o agente de estado de funcionamento da versão 3.0.129.0 durante a atualização do Azure AD Connect no local. Agente de estado de funcionamento versão 3.0.129.0 não tem o problema de compatibilidade com o Azure AD Connect versão 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Estado: 19 de Outubro de 2017

> [!IMPORTANT]
> Não há um problema de compatibilidade conhecidos entre a versão do Azure AD Connect 1.1.647.0 e versão do agente do Azure AD Connect Health (para sincronização) 3.0.127.0. Este problema impede que o agente de estado de funcionamento enviar dados de estado de funcionamento sobre o serviço do Azure AD Connect sincronização (incluindo erros de sincronização de objetos e dados de histórico de execução) para o serviço de estado de funcionamento do Azure AD. Antes de atualizar manualmente a implementação do Azure AD Connect para a versão 1.1.647.0, verifique se que a versão atual do agente do Azure AD Connect Health instalado no seu servidor do Azure AD Connect. Pode fazê-lo acedendo a *painel de controlo → Adicionar/remover programas* e procure a aplicação *Microsoft Azure agente AD Connect Health para sincronização*. Se a respetiva versão seja a 3.0.127.0, recomenda-se que aguardar a próxima versão do Azure AD Connect fique disponível antes da atualização. Se a versão do agente de estado de funcionamento não 3.0.127.0, é adequado continuar com a atualização manual e no local. Tenha em atenção que este problema não afeta a atualização de swing ou os clientes que estão a efetuar uma instalação nova do Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas fixos
* Foi corrigido um problema com o *alterar utilizador início de sessão* tarefas no Assistente do Azure AD Connect:

  * O problema ocorre quando tiver uma implementação existente do Azure AD Connect com sincronização de palavra-passe **ativada**, e está a tentar definir o método de início de sessão do utilizador como *autenticação pass-through*. Antes da alteração é aplicada, o assistente apresenta incorretamente o "*desativar a sincronização de palavras-passe*" linha de comandos. No entanto, a sincronização de palavra-passe permanece ativada após a alteração é aplicada. Com esta correção, o assistente já não apresenta a linha de comandos.

  * Por predefinição, o assistente não desativar a sincronização de palavra-passe quando atualizar o método de início de sessão de utilizador utilizando o *alterar utilizador início de sessão* tarefas. Isto é evitar a interrupção para clientes que pretendem manter a sincronização de palavras-passe, mesmo ativar a autenticação pass-through ou de federação como o método de início de sessão primário do utilizador.
  
  * Se pretender desativar a sincronização de palavra-passe depois de atualizar o método de início de sessão do utilizador, tem de executar o *personalizar a configuração da sincronização* tarefas no assistente. Quando navegar para o *funcionalidades opcionais* página, desmarque a *sincronização de palavra-passe* opção.
  
  * Tenha em atenção que o mesmo problema ocorre também se tentar ativar/desativar totalmente integrada Single Sign-On. Especificamente, tem uma implementação existente do Azure AD Connect com sincronização de palavras-passe ativada e o método de início de sessão do utilizador já está configurado como *autenticação pass-through*. Utilizar o *alterar utilizador início de sessão* tarefa, tentar verificação/desmarque o *ativar totalmente integrada Single Sign-On* opção enquanto o método de início de sessão do utilizador permanece configurado como "Autenticação pass-through". Antes da alteração é aplicada, o assistente apresenta incorretamente o "*desativar a sincronização de palavras-passe*" linha de comandos. No entanto, a sincronização de palavra-passe permanece ativada após a alteração é aplicada. Com esta correção, o assistente já não apresenta a linha de comandos.

* Foi corrigido um problema com o *alterar utilizador início de sessão* tarefas no Assistente do Azure AD Connect:

   * O problema ocorre quando tiver uma implementação existente do Azure AD Connect com sincronização de palavra-passe **desativada**, e está a tentar definir o método de início de sessão do utilizador como *autenticação pass-through*. Quando a alteração é aplicada, o assistente permite-autenticação pass-through e sincronização de palavra-passe. Com esta correção, o assistente permite já não é a sincronização de palavra-passe.

  * Anteriormente, a sincronização de palavra-passe foi um pré-requisito para ativar a autenticação pass-through. Quando configurar o método de início de sessão do utilizador como *autenticação pass-through*, o assistente deverá ativar a autenticação pass-through e sincronização de palavra-passe. Recentemente, a sincronização de palavra-passe foi removida como um pré-requisito. Como parte do Azure AD Connect versão 1.1.557.0, foi efetuada uma alteração ao Azure AD Connect para ativar a sincronização de palavra-passe quando definir o método de início de sessão do utilizador como *autenticação pass-through*. No entanto, a alteração só foi aplicada a instalação do Azure AD Connect. Com esta correção, a alteração do mesma é também aplicada para a *alterar utilizador início de sessão* tarefas.
  
  * Tenha em atenção que o mesmo problema ocorre também se tentar ativar/desativar totalmente integrada Single Sign-On. Especificamente, tem uma implementação existente do Azure AD Connect com sincronização de palavras-passe desativada e o método de início de sessão do utilizador já está configurado como *autenticação pass-through*. Utilizar o *alterar utilizador início de sessão* tarefa, tentar verificação/desmarque o *ativar totalmente integrada Single Sign-On* opção enquanto o método de início de sessão do utilizador permanece configurado como "Autenticação pass-through". Quando a alteração é aplicada, o assistente permite a sincronização de palavra-passe. Com esta correção, o assistente permite já não é a sincronização de palavra-passe. 

* Foi corrigido um problema que causou a atualização do Azure AD Connect falhar com o erro "*não é possível atualizar o serviço de sincronização*". Além disso, o serviço de sincronização já não pode começar com o erro de evento "*o serviço não conseguiu iniciar porque a versão da base de dados é mais recente do que a versão dos binários do instalado*". O problema ocorre quando o administrador efetuar a atualização não tem o privilégio de administrador do sistema para o SQL server que está a ser utilizado pelo Azure AD Connect. Com esta correção, o Azure AD Connect requer apenas o administrador para ter o privilégio de db_owner na base de dados ADSync durante a atualização.

* Foi corrigido um problema do Azure AD Connect atualizar que os clientes que ativou afetados [totalmente integrada Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Depois de atualizar o Azure AD Connect, totalmente integrada Single Sign-On incorretamente é apresentado como desativado no Assistente do Azure AD Connect, apesar da funcionalidade permaneça ativada e totalmente funcionais. Com esta correção, a funcionalidade de agora, é apresentado corretamente como ativada no assistente.

* Foi corrigido um problema que causou a geração do Assistente do Azure AD Connect para sempre mostrar o "*configurar âncora de origem*" linha de comandos no *pronto para configurar* página, mesmo que não relacionadas com a âncora de origem foram efetuadas alterações.

* Quando efetuar a atualização no local manual do Azure AD Connect, o cliente é necessário fornecer as credenciais de Administrador Global do inquilino do Azure AD correspondente. Anteriormente, foi possível continuar a atualização, apesar das credenciais de Administrador Global fornecidas pertence a um Azure diferentes inquilino do AD. Enquanto a atualização aparece concluída com êxito, determinadas configurações não são mantidas corretamente com a atualização. Com esta alteração, o assistente não permitirá atualizar para continuar se as credenciais fornecidas não correspondem ao inquilino do Azure AD.

* Remover redundante lógica que desnecessariamente reinício do serviço do Azure AD Connect Health no início de uma atualização manual.


#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* Foi adicionada lógica para simplificar os passos necessários para configurar o Azure AD Connect com a nuvem de Datacenters da Microsoft. Anteriormente, são necessários para atualizar as chaves de registo específicos no servidor do Azure AD Connect para que esta funcione corretamente com a nuvem de Datacenters da Microsoft, conforme descrito neste artigo. Agora, o Azure AD Connect pode detetar automaticamente se o inquilino na nuvem de Datacenters da Microsoft baseia-se nas credenciais de administrador global fornecidas durante a configuração.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
>[!NOTE]
> Nota: O serviço de sincronização tem uma interface WMI que permite-lhe desenvolver o suas próprias programador personalizado. Esta interface despromovido e será removida do futuras versões do Azure AD Connect vem incluído após 30 de Junho de 2018. Os clientes que pretendem personalizar agenda de sincronização devem utilizar o [incorporado programador (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Problemas fixos
* Quando o Assistente do Azure AD Connect cria a conta do conector do AD necessária para sincronizar as alterações do Active Directory no local, este não corretamente atribuir a conta a permissão necessária para ler PublicFolder objetos. Este problema afeta instalação rápida e instalação personalizada. Esta alteração corrige o problema.

* Foi corrigido um problema que causou a página de resolução de problemas do Azure AD Connect assistente não compor corretamente para os administradores de do Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* Quando a sincronização de palavras-passe utilizando o Assistente do Azure AD Connect página de resolução de problemas de resolução de problemas, a página de resolução de problemas devolve agora estado específicas do domínio.

* Anteriormente, caso tentem ativar a sincronização de Hash de palavra-passe, o Azure AD Connect não verifica se a conta do conector do AD tem permissões necessárias para sincronizar os hashes de palavra-passe do AD no local. Agora, o Assistente do Azure AD Connect irá verificar e avisá-lo se a conta do conector do AD não tem permissões suficientes.

### <a name="ad-fs-management"></a>Gestão do AD FS
#### <a name="fixed-issue"></a>Problema fixo
* Corrigido um problema relacionado com a utilização de [msDS-ConsistencyGuid como a âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funcionalidade. Este problema afeta os clientes de que configuraram *federação com o AD FS* como o método de início de sessão do utilizador. Quando executar *configurar âncora de origem* muda de tarefas no assistente, Azure AD Connect para utilizar * ms-DS-ConsistencyGuid como atributo de origem para immutableId. Como parte desta alteração, o Azure AD Connect tenta atualizar as regras de afirmação para ImmutableId no AD FS. No entanto, este passo falhou porque o Azure AD Connect não tinha as credenciais de administrador necessárias para configurar o AD FS. Com esta correção, o Azure AD Connect agora pede-lhe para introduzir as credenciais de administrador do AD FS ao executar o *configurar âncora de origem* tarefas.



## <a name="116140"></a>1.1.614.0
Estado: 05 de Setembro de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Há um problema conhecido que está a causar a atualização do Azure AD Connect falhar com o erro "*não é possível atualizar o serviço de sincronização*". Além disso, o serviço de sincronização já não pode começar com o erro de evento "*o serviço não conseguiu iniciar porque a versão da base de dados é mais recente do que a versão dos binários do instalado*". O problema ocorre quando o administrador efetuar a atualização não tem o privilégio de administrador do sistema para o SQL server que está a ser utilizado pelo Azure AD Connect. Permissões de dbo não são suficientes.

* Há um problema conhecido com o Azure AD Connect atualização que está a afetar os clientes que ativou [totalmente integrada Single Sign-On](active-directory-aadconnect-sso.md). Depois de atualizar o Azure AD Connect, a funcionalidade é apresentado como desativado no assistente, apesar da funcionalidade permaneça ativada. Uma correção para este problema será fornecido no futuro da versão. Os clientes que estão preocupados com este problema de apresentação podem manualmente corrigir Ativando totalmente integrada Single Sign-On no assistente.

#### <a name="fixed-issues"></a>Problemas fixos
* Foi corrigido um problema que impediu que o Azure AD Connect atualizar as regras de afirmações no ADFS no local ao ativar o [msDS-ConsistencyGuid como a âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funcionalidade. O problema ocorrer se tentar ativar a funcionalidade para uma implementação existente do Azure AD Connect que tenha configurado como método de início de sessão de ADFS. O problema ocorre porque o assistente não solicitar credenciais ADFS antes de tentar atualizar as regras de afirmações no AD FS.
* Foi corrigido um problema que causou o Azure AD Connect efetuar a instalação se no local tem de floresta do AD NTLM desativada. O problema está devido ao Assistente do Azure AD Connect não fornecer credenciais completamente qualificadas ao criar os contextos de segurança necessários para a autenticação Kerberos. Isto faz com que a autenticação Kerberos falhar e Assistente do Azure AD Connect para reverter a utilização de NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Problemas fixos
* Foi corrigido um problema onde não é possível criar uma nova regra de sincronização se o atributo de Tag não se encontra preenchido.
* Foi corrigido um problema que causou o Azure AD Connect ligar ao AD no local para a sincronização de palavra-passe utilizando o NTLM, apesar de Kerberos está disponível. Este problema ocorre se o local topologia do AD tem um ou mais controladores de domínio que foi restaurada a partir de uma cópia de segurança.
* Foi corrigido um problema que causou a geração de passos de sincronização completa para desnecessariamente ocorrer após a atualização. Em geral, executar passos de sincronização completa é necessária após a atualização se existirem alterações às regras de sincronização de out-of-box. O problema foi devido a um erro na lógica de deteção de alteração incorretamente detetou uma alteração ao identificar a expressão da regra de sincronização com carateres de nova linha. Carateres de nova linha são inseridos na expressão da regra de sincronização para melhorar a legibilidade.
* Foi corrigido um problema que pode fazer com que o servidor do Azure AD Connect não funcione corretamente após a atualização automática. Este problema afeta os servidores do Azure AD Connect com a versão 1.1.443.0 (ou anterior). Para obter detalhes sobre o problema, consulte o artigo [do Azure AD Connect não está a funcionar corretamente após a atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Foi corrigido um problema que pode fazer com que a atualização automática para repetida a cada 5 minutos quando forem encontrados erros. Com a correção, atualização automática de tentativas com término exponencial quando são encontrados erros.
* Corrigido um problema onde os eventos de sincronização de palavra-passe 611 incorretamente é apresentado nos registos de eventos de aplicações do Windows como **informativa** em vez de **erro**. Evento 611 é gerado sempre que a sincronização de palavra-passe encontra um problema. 
* Foi corrigido um problema no Assistente do Azure AD Connect que permite que a funcionalidade de repetição de escrita do grupo ativado sem selecionar uma UO necessária para a repetição de escrita do grupo.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* Adicionar uma tarefa de resolução de problemas ao Assistente do Azure AD Connect em tarefas adicionais. Os clientes podem tirar partido esta tarefa para resolver problemas relacionados com a sincronização de palavra-passe e recolher diagnóstico geral. No futuro, a tarefa de resolução de problemas irá ser expandida para incluir outros problemas relacionados com a sincronização de diretórios.
* Suporta agora do Azure AD Connect, um novo modo de instalação chamado **base de dados de utilização existente**. Este modo de instalação permite aos clientes instalar o Azure AD Connect que especifica uma base de dados ADSync existente. Para obter mais informações sobre esta funcionalidade, consulte o artigo [utilizar uma base de dados existente](active-directory-aadconnect-existing-database.md).
* Para melhor segurança, o Azure AD Connect agora por predefinição, utiliza TLS1.2 para ligar ao Azure AD para a sincronização de diretórios. Anteriormente, a predefinição foi TLS1.0.
* Quando o agente do Azure AD Connect palavra-passe sincronização é iniciado, este tenta ligar ao ponto final de bem conhecido do Azure AD para a sincronização de palavra-passe. Após a ligação com êxito, é redirecionado para um ponto final específico da região. Anteriormente, o agente de sincronização de palavra-passe coloca na cache o ponto final específico da região até ser reiniciado. Agora, o agente limpa a cache e tentativas com o ponto final bem conhecido, se encontrar um problema de ligação com o ponto final específico da região. Esta alteração garante que a sincronização de palavra-passe pode ativação pós-falha para outro ponto final específico da região quando o ponto final específico da região de cache já não está disponível.
* Para sincronizar alterações no local, floresta do AD, uma conta do AD DS é necessária. Pode (i) criar o AD DS conta por si e forneça a credencial para o Azure AD Connect ou (ii) fornecem um Admin da empresa de credenciais e permita que o Azure AD Connect, criar a conta do AD DS para si. Anteriormente, (i) é a opção predefinida no Assistente do Azure AD Connect. Agora, (ii) é a opção predefinida.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* Adicionado suporte para a nuvem do Microsoft Azure Government e Datacenters da Microsoft Cloud.

### <a name="ad-fs-management"></a>Gestão do AD FS
#### <a name="fixed-issues"></a>Problemas fixos
* O cmdlet ADSyncNGCKeysWriteBack inicialização no módulo do powershell de preparação do AD foi incorretamente ACL'ing o contentor de registo do dispositivo e, por conseguinte, poderá apenas herdar permissões existentes.  Isto foi atualizado para que a conta de serviço de sincronização tem as permissões corretas.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* A tarefa do AAD Connect verificar ADFS início de sessão foi atualizada para que verifica inícios de sessão Online da Microsoft e não apenas token obtenção do ADFS.
* Quando configurar um novo farm do AD FS com o AAD Connect, a página solicitar credenciais ADFS foi movida para que ocorra agora antes do utilizador é-lhe pedido para fornecer os servidores AD FS e WAP.  Isto permite que o AAD Connect verificar se a conta especificada tem as permissões corretas.
* Durante a atualização do AAD Connect, iremos já não falhará uma atualização se confiança do AAD do AD FS não consegue atualizar.  Se isto acontecer, o utilizador será apresentado uma mensagem de aviso adequado e deve avançar para repor a fidedignidade através da tarefa adicional AAD Connect.

### <a name="seamless-single-sign-on"></a>Totalmente integrado Single Sign-On
#### <a name="fixed-issues"></a>Problemas fixos
* Foi corrigido um problema que causou o Assistente do Azure AD Connect devolver um erro se tentar ativar [totalmente integrada Single Sign-On](active-directory-aadconnect-sso.md). A mensagem de erro é *"Configuração do Microsoft Azure AD Connect autenticação agente falhou".* Este problema afeta os clientes existentes que tinham atualizados manualmente a versão de pré-visualização dos agentes de autenticação para [autenticação pass-through](active-directory-aadconnect-sso.md) com base nos passos descritos neste [artigo](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Estado: 23 de Julho de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema fixo

* Foi corrigido um problema que causou a regra de sincronização de out-of-box "Out AD - utilizador ImmutableId" a remover:

  * O problema ocorre quando o Azure AD Connect é atualizado ou quando a opção de tarefa *atualizar configuração da sincronização* no Azure AD Connect assistente é utilizado para atualizar a configuração da sincronização do Azure AD Connect.
  
  * Esta regra de sincronização se aplica aos clientes que tem ativado o [msDS-ConsistencyGuid como a funcionalidade de âncora de origem](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Esta funcionalidade foi introduzida na versão 1.1.524.0 e depois. Quando a regra de sincronização é removida, o Azure AD Connect já não pode preencher local no atributo de ms-DS-ConsistencyGuid AD com o valor do atributo de ObjectGuid. -Não impede que novos utilizadores que está a ser aprovisionados com o Azure AD.
  
  * A correção garante que a regra de sincronização já não será removida durante a atualização, ou durante a alteração da configuração, desde que a funcionalidade está ativada. Para os clientes existentes que foram afetados por este problema, a correção também garante que a regra de sincronização é adicionada novamente depois de atualizar para esta versão do Azure AD Connect.

* Foi corrigido um problema que faz com que as regras de sincronização de out-of-box ter precedência um valor inferior a 100:

  * Em geral, os valores de prioridade 0 - 99 estão reservados para as regras de sincronização personalizados. Durante a atualização, os valores de prioridade para as regras de sincronização de out-of-box são atualizados para acomodar as alterações de regra de sincronização. Devido a este problema, as regras de sincronização de out-of-box podem ser atribuídas precedência um valor inferior a 100.
  
  * A correção impede que o problema ocorrer durante a atualização. No entanto, o sistema não restaurar os valores de precedência para clientes existentes que foram afetados pelo problema. Uma correção separada irá ser fornecida no futuro para o ajudar com o restauro.

* Foi corrigido um problema onde o [ecrã domínio filtragem e de UOS](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no Azure AD Connect assistente está a ser mostrada *sincronizar todos os domínios e UOs* opção selecionada, como, apesar de filtragem baseada na UO está ativada.

*   Foi corrigido um problema que causou o [ecrã Configurar partições de diretório](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager para devolver um erro se o *atualizar* botão é clicado. A mensagem de erro é *"foi encontrado um erro ao atualizar domínios: não é possível converter o objeto do tipo 'System.Collections.ArrayList' no tipo ' Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* O erro ocorre quando o novo domínio do AD foi adicionado a uma floresta existente do AD e está a tentar atualizar o Azure AD Connect com o botão de atualização.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas

* [Funcionalidade de atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) foi expandido para suportar clientes com as seguintes configurações:
  * Ativou a funcionalidade de repetição de escrita do dispositivo.
  * Ativou a funcionalidade de repetição de escrita do grupo.
  * A instalação não é um definições Express ou uma atualização do DirSync.
  * Tiver mais de 100 000 objetos no metaverso.
  * Está a ligar mais do que uma floresta. Configuração rápida liga-se apenas a uma floresta.
  * A conta do conector do AD já não é a conta MSOL_ predefinida.
  * O servidor está configurado para ser no modo de teste.
  * Ativou a funcionalidade de repetição de escrita do utilizador.
  
  >[!NOTE]
  >A expansão de âmbito da funcionalidade de atualização automática de afecta os clientes com o Azure AD Connect compilação 1.1.105.0 e depois. Se não pretender que a ser atualizado automaticamente no servidor do Azure AD Connect, terá de executar os seguintes cmdlet no seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre a ativação/desativação da atualização automática, consulte o artigo [do Azure AD Connect: atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Estado: Não serão lançadas. As alterações neste compilação estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema fixo

* Foi corrigido um problema que causou a regra de sincronização de out-of-box "Out AD - utilizador ImmutableId" a serem removidos quando é atualizada a configuração de filtragem baseada na UO. Esta regra de sincronização é necessária para o [msDS-ConsistencyGuid como a funcionalidade de âncora de origem](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Foi corrigido um problema onde o [ecrã domínio filtragem e de UOS](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no Azure AD Connect assistente está a ser mostrada *sincronizar todos os domínios e UOs* opção selecionada, como, apesar de filtragem baseada na UO está ativada.

*   Foi corrigido um problema que causou o [ecrã Configurar partições de diretório](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager para devolver um erro se o *atualizar* botão é clicado. A mensagem de erro é *"foi encontrado um erro ao atualizar domínios: não é possível converter o objeto do tipo 'System.Collections.ArrayList' no tipo ' Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* O erro ocorre quando o novo domínio do AD foi adicionado a uma floresta existente do AD e está a tentar atualizar o Azure AD Connect com o botão de atualização.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas

* [Funcionalidade de atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) foi expandido para suportar clientes com as seguintes configurações:
  * Ativou a funcionalidade de repetição de escrita do dispositivo.
  * Ativou a funcionalidade de repetição de escrita do grupo.
  * A instalação não é um definições Express ou uma atualização do DirSync.
  * Tiver mais de 100 000 objetos no metaverso.
  * Está a ligar mais do que uma floresta. Configuração rápida liga-se apenas a uma floresta.
  * A conta do conector do AD já não é a conta MSOL_ predefinida.
  * O servidor está configurado para ser no modo de teste.
  * Ativou a funcionalidade de repetição de escrita do utilizador.
  
  >[!NOTE]
  >A expansão de âmbito da funcionalidade de atualização automática de afecta os clientes com o Azure AD Connect compilação 1.1.105.0 e depois. Se não pretender que a ser atualizado automaticamente no servidor do Azure AD Connect, terá de executar os seguintes cmdlet no seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre a ativação/desativação da atualização automática, consulte o artigo [do Azure AD Connect: atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Estado: de 2017 de Julho

>[!NOTE]
>Este compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema fixo
* Foi corrigido um problema com o cmdlet de inicializar-ADSyncDomainJoinedComputerSync que causou o domínio verificado configurado no objeto de ponto de ligação de serviço existente para ser alterada, mesmo se ainda se trata de um domínio válido. Este problema ocorre quando o inquilino do Azure AD tem mais do que um domínios verificados que podem ser utilizadas para configurar o ponto de ligação de serviço.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* Repetição de escrita de palavras-passe está agora disponível para pré-visualização com o Microsoft Azure Government nuvem e de Datacenters da Microsoft Cloud. Para obter mais informações sobre o suporte do Azure AD Connect para as instâncias de serviço diferente, consulte o artigo [do Azure AD Connect: Considerações especiais para instâncias](active-directory-aadconnect-instances.md).

* O cmdlet de inicializar-ADSyncDomainJoinedComputerSync tem agora um novo parâmetro opcional denominado AzureADDomain. Este parâmetro permite especificar que verificadas domínio a ser utilizado para configurar o ponto de ligação de serviço.

### <a name="pass-through-authentication"></a>Autenticação pass-through

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* O nome do agente necessário para a autenticação pass-through foi alterada de *conector do Proxy de aplicações do Microsoft Azure AD* para *agente do Microsoft Azure AD Connect autenticação*.

* Ativar a autenticação pass-through já não permite a sincronização de Hash de palavra-passe por predefinição.


## <a name="115530"></a>1.1.553.0
Estado: de 2017 de Junho

> [!IMPORTANT]
> Existem alterações de regra de esquema e sincronização introduzidas neste compilação. Serviço de sincronização ligar do Azure AD irá acionar passos importação completa e a sincronização completa após a atualização. Detalhes de alterações são descritos abaixo. Diferir temporariamente a importação completa e a sincronização completa passos após a atualização, consulte o artigo [como diferir sincronização completa após a atualização](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Problema conhecido
* Há um problema que afecta os clientes que utilizam [filtragem baseada na UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) com a sincronização do Azure AD Connect. Quando navegar para o [página domínio filtragem e de UOS](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no Assistente do Azure AD Connect, é esperado o seguinte comportamento:
  * Se a filtragem baseada na UO estiver ativada, o **sincronizar selecionados domínios e UOs** opção está selecionada.
  * Caso contrário, o **sincronizar todos os domínios e UOs** opção está selecionada.

O problema que seja é que o **sincronizar todos os domínios e UOs opção** está sempre selecionado quando executar o assistente.  Isto ocorre mesmo se a filtragem baseada na UO configurado anteriormente. Antes de guardar quaisquer alterações de configuração AAD Connect, certifique-se de que o **sincronizar selecionados domínios e UOs opção está selecionada** e confirme que todas as OUs tem de sincronizar são ativadas de novo. Caso contrário, filtragem baseada na UO será desativada.

#### <a name="fixed-issues"></a>Problemas fixos

* Foi corrigido um problema com a repetição de escrita de palavras-passe que permite aos administradores do Azure AD para repor a palavra-passe no local AD privilegiado a conta de utilizador. O problema ocorre quando o Azure AD Connect é concedido a permissão de reposição de palavra-passe através da conta com privilégios. O problema é resolvido nesta versão do Azure AD Connect, não permitindo que um administrador do AD do Azure para repor a palavra-passe arbitrários no local AD privilegiado a conta de utilizador, a menos que o administrador é o proprietário dessa conta. Para obter mais informações, consulte [4033453 Consultivo de alterações de segurança](https://technet.microsoft.com/library/security/4033453).

* Foi corrigido um problema relacionado com o [msDS-ConsistencyGuid como a âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funcionalidade onde o Azure AD Connect não repetição de escrita para no local atributo msDS-ConsistencyGuid de AD. O problema ocorre quando existem vários no local ao Azure AD Connect de florestas do AD e o *existem identidades de utilizador em vários opção de diretórios* está selecionada. Quando é utilizado este tipo de configuração, as regras de sincronização resultante não preencher o atributo de sourceAnchorBinary no Metaverso. O atributo sourceAnchorBinary é utilizado como o atributo de origem para o atributo msDS-ConsistencyGuid. Como resultado, a repetição de escrita para o atributo ms-DSConsistencyGuid não ocorrer. Para corrigir o problema, foram atualizadas as regras de sincronização seguinte para se certificar de que o atributo sourceAnchorBinary no Metaverso sempre é preenchido:
  * Do AD - InetOrgPerson AccountEnabled.xml
  * Do AD - InetOrgPerson Common.xml
  * Do AD - AccountEnabled.xml de utilizador
  * Do AD - Common.xml de utilizador
  * Do AD - utilizador associar SOAInAAD.xml

* Anteriormente, mesmo que o [msDS-ConsistencyGuid como a âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funcionalidade não está ativada, a regra de sincronização "Out AD – utilizador ImmutableId" é continuam a ser adicionada para o Azure AD Connect. O efeito é benignas e não causar a repetição de escrita do atributo msDS-ConsistencyGuid para ocorrer. Para evitar confusões, foi adicionada lógica para se certificar de que a regra de sincronização só é adicionada quando a funcionalidade está ativada.

* Foi corrigido um problema que causou a sincronização de hash de palavra-passe falhar com o evento de erro 611. Este problema ocorre depois de um ou mais domínio os controladores foram removidos do AD no local. No final de cada ciclo de sincronização de palavra-passe, o cookie de sincronização emitido por no local AD contém os IDs de invocação dos controladores de domínio removidos com o valor de USN (Update Sequence Number) 0. O Gestor de sincronização de palavras-passe não consegue manter a sincronização cookie que contêm USN valor 0 e falha com o evento de erro 611. Durante o próximo ciclo de sincronização, o Gestor de sincronização de palavras-passe reutiliza o cookie de sincronização persistente último que não contém um valor de USN de 0. Isto faz com que as alterações de palavra-passe mesmo necessário ressincronizar. Com esta correção, o Gestor de sincronização de palavras-passe mantém o cookie de sincronização corretamente.

* Anteriormente, mesmo que a atualização automática foi desativado através do cmdlet Set-ADSyncAutoUpgrade, a atualização automática de processo continua a verificar a existência de atualizar periodicamente e baseia-se no que respeite disablement o instalador que transferiu. Com esta correção, o processo de atualização automática já não verifica a existência de atualização periodicamente. A correção é aplicada automaticamente quando o programa de instalação de atualização para esta versão do Azure AD Connect é executado uma vez.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas

* Anteriormente, o [msDS-ConsistencyGuid como a âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funcionalidade estava disponível para novas implementações apenas. Agora, está disponível para as implementações existentes. Mais especificamente:
  * Para aceder a funcionalidade, inicie o Assistente do Azure AD Connect e escolha o *âncora de origem de atualização* opção.
  * Esta opção só está visível para as implementações existentes que estão a utilizar objectGuid como atributo sourceAnchor.
  * Quando configurar a opção, o assistente valida o estado do atributo msDS-ConsistencyGuid no Active Directory no local. Se o atributo não está configurado qualquer objeto de utilizador no diretório, o assistente utiliza msDS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo está configurado numa ou mais objetos de utilizador no diretório, o assistente conclui o atributo está a ser utilizado por outras aplicações e não é adequado como atributo sourceAnchor e não permite a alteração de âncora de origem para continuar. Se tiver a certeza de que o atributo não é utilizado por aplicações existentes, terá de contactar o suporte para obter informações sobre como suprimir o erro.

* Específicas **userCertificate** atributo em objetos de dispositivo, o Azure AD Connect agora procura valores de certificados necessários para [ligar dispositivos associados a um domínio ao Azure AD para Windows 10 experiência](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) e filtros de saída rest antes da sincronização para o Azure AD. Para ativar este comportamento, a regra de sincronização de out-of-box "Enviados ao AAD - dispositivo associar SOAInAD" foi atualizada.

* Azure AD Connect agora suporta repetição de escrita de Exchange Online **cloudPublicDelegates** atributo para AD no local **publicDelegates** atributo. Isto permite que o cenário onde uma caixa de correio do Exchange Online pode ser direitos SendOnBehalfTo para os utilizadores com caixa de correio do Exchange no local. Para suportar esta funcionalidade, uma nova regra de sincronização de out-of-box "Out AD – repetição de escrita do utilizador Exchange híbrida PublicDelegates" foi adicionado. Esta regra de sincronização é adicionada apenas para o Azure AD Connect quando híbrida do Exchange funcionalidade está ativada.

*   Suporta agora do Azure AD Connect, sincronizar o **altRecipient** atributos do Azure AD. Para suportar esta alteração, as seguintes regras de sincronização de out-of-box tem sido atualizadas para incluir o fluxo de atributo necessário:
  * Do AD – Exchange do utilizador
  * Limite para o AAD – ExchangeOnline de utilizador
  
* O **cloudSOAExchMailbox** atributo no Metaverso indica se um utilizador especificado tem de caixa de correio do Exchange Online ou não. A definição foi atualizada para incluir adicionais Exchange Online RecipientDisplayTypes como estas caixas de correio do equipamento e sala de conferência. Para ativar esta alteração, a definição do atributo cloudSOAExchMailbox, que é possível localizar a regra de sincronização de out-of-box "do AAD – utilizador híbrida do Exchange", foi atualizada de:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... para o seguinte:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Adicionar o seguinte conjunto de funções de X509Certificate2 compatível para a criação de sincronização expressões de regra para lidar com valores de certificado no atributo userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Selecione|
    |CertKeyAlgorithmParams|CertHashString|onde|
    |||com o|

* Foram introduzidas seguintes alterações de esquema para permitir que os clientes podem criar regras de sincronização personalizados para o fluxo sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como distinguishedName para objetos de utilizador:

  * Foram adicionados ao esquema de MV seguintes atributos:
    * Grupo: AccountName
    * Grupo: domainNetBios
    * Grupo: domainFQDN
    * Pessoa: distinguishedName

  * Foram adicionados os seguintes atributos ao esquema do conector do Azure AD:
    * Grupo: OnPremisesSamAccountName
    * Grupo: NetBiosName
    * Grupo: DnsDomainName
    * Utilizador: OnPremisesDistinguishedName

* O script de cmdlet ADSyncDomainJoinedComputerSync tem agora um novo parâmetro opcional denominado AzureEnvironment. O parâmetro é utilizado para especificar que o inquilino do Azure Active Directory correspondente estiver alojado de região. Os valores válidos incluem:
  * AzureCloud (predefinição)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Atualizado Editor de regras de sincronização para utilizar a associação (em vez de aprovisionar) como o valor predefinido do tipo de ligação durante a criação de regra de sincronização.

### <a name="ad-fs-management"></a>Gestão do AD FS

#### <a name="issues-fixed"></a>Problemas fixo

* Os seguintes URLs são introduzidos pelo Azure AD para melhorar a resiliência contra a falha de autenticação pontos finais de WS-Federation novo e será adicionado ao local Configuração do AD FS para confiança de entidade parte das:
  * https://Ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://CCS.login.microsoftonline.com/login.srf
  * https://CCS-sdf.login.microsoftonline.com/login.srf
  
* Foi corrigido um problema que causou a geração do AD FS gerar o valor de afirmação incorreto para IssuerID. O problema ocorre se existem vários domínios verificados no inquilino do Azure AD e o sufixo de domínio do atributo userPrincipalName utilizado para gerar a afirmação IssuerID é, pelo menos, 3 níveis profunda (por exemplo, johndoe@us.contoso.com). O problema foi resolvido, atualizando o regex utilizada pelas regras de afirmação.

#### <a name="new-features-and-improvements"></a>Funcionalidades novas e melhoradas
* Anteriormente, a funcionalidade de gestão de certificados do AD FS fornecida pelo Azure AD Connect só pode ser utilizada com farms ADFS geridos através do Azure AD Connect. Agora, pode utilizar a funcionalidade com farms ADFS que não são geridos com o Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Data da versão: de 2017 de Maio

> [!IMPORTANT]
> Existem alterações de regra de esquema e sincronização introduzidas neste compilação. Serviço de sincronização ligar do Azure AD irá acionar passos importação completa e a sincronização completa após a atualização. Detalhes de alterações são descritos abaixo.
>
>

**Fixos problemas:**

Sincronização do Azure AD Connect

* Foi corrigido um problema que faz com que a atualização automática ocorrer no servidor do Azure AD Connect, mesmo que o cliente foi desativada a funcionalidade com o cmdlet Set-ADSyncAutoUpgrade. Com esta correção, o processo de atualização automática no servidor ainda verifica a existência de atualização periodicamente, mas o instalador que transferiu honra a configuração de atualização automática.
* Durante a atualização no local de DirSync, Azure AD Connect cria uma conta de serviço do Azure AD a ser utilizada pelo conector do Azure AD para sincronizar com o Azure AD. Depois da conta for criada, o Azure AD Connect autentica com o Azure AD com a conta. Por vezes, a autenticação falha devido a problemas temporários, que por sua vez faz com que a atualização no local de DirSync falhar com o erro *"erro executar tarefa de configurar AAD Sync: AADSTS50034: para iniciar sessão para esta aplicação, a conta tem de ser adicionar o diretório de xxx.onmicrosoft.com."* Para melhorar a resiliência da atualização do DirSync, Azure AD Connect agora repete o passo de autenticação.
* Ocorreu um problema com a compilação 443 que faz com que a atualização do DirSync no local com êxito, mas necessário para a sincronização de diretórios de perfis de execução não é criado. Autorrecuperação lógica está incluído neste compilação do Azure AD Connect. Quando o cliente atualizado para este compilação, o Azure AD Connect Deteta a falta de perfis de execução e cria-los.
* Foi corrigido um problema que faz com que o processo de sincronização de palavra-passe falhar ao iniciar com 6900 de ID de evento e o erro *"tem já foi adicionado um item com a mesma chave"*. Este problema ocorre se atualizar a UO configuração de filtragem para incluir a partição de configuração do AD. Para corrigir este problema, o processo de sincronização de palavra-passe sincroniza agora as alterações de palavra-passe de partições de domínio de AD apenas. As partições de domínio não como partição de configuração são ignoradas.
* Durante a instalação rápida, Azure AD Connect cria um local conta do AD DS para ser utilizada pelo conector do AD para comunicar com o AD no local. Anteriormente, a conta for criada com o sinalizador PASSWD_NOTREQD definido no atributo do controlo de conta de utilizador e uma palavra-passe aleatória está definida na conta. Agora, o Azure AD Connect explicitamente remove o sinalizador PASSWD_NOTREQD depois da palavra-passe está definida na conta.
* Foi corrigido um problema que faz com que a atualização do DirSync falhar com o erro *"Ocorreu um impasse no sql server que tentar adquirir um bloqueio de aplicação"* quando o atributo mailNickname se encontra no local de esquema AD, mas não tem o vínculo para a classe de objeto de utilizador do AD.
* Foi corrigido um problema que faz com que a funcionalidade de repetição de escrita do dispositivo para ser automaticamente desativado quando um administrador está a atualizar configuração da sincronização do Azure AD Connect utilizando o Assistente do Azure AD Connect. Isto é provocado pelo assistente efetuar verificação de pré-requisitos para a configuração de repetição de escrita do dispositivo existente no AD no local e a verificação de falha. A correção está a ignorar a verificação se a repetição de escrita do dispositivo já está ativada anteriormente.
* Para configurar a filtragem de UO, pode utilizar o Assistente do Azure AD Connect ou o Synchronization Service Manager. Anteriormente, se utilizar o Assistente do Azure AD Connect para configurar a filtragem de UO, UOs novo criados posteriormente estão incluídos para sincronização de diretórios. Se não pretender que o novo UOs para ser incluída, tem de configurar a filtragem de UO utilizando o Gestor do serviço de sincronização. Agora, pode conseguir o mesmo comportamento utilizando o Assistente do Azure AD Connect.
* Foi corrigido um problema que faz com que os procedimentos armazenados necessários pelo Azure AD Connect para ser criados sob o esquema do administrador instalar, em vez de no esquema dbo.
* Foi corrigido um problema que faz com que o atributo de TrackingId devolvido pelo Azure AD para ser omitido no AAD Connect servidor registos de eventos. O problema ocorre se o Azure AD Connect recebe uma mensagem de redirecionamento do Azure AD e do Azure AD Connect é não é possível ligar ao ponto final fornecido. O TrackingId é utilizada pelos engenheiros de suporte para correlacionar com os registos do lado do serviço durante a resolução de problemas.
* Quando o Azure AD Connect recebe LargeObject erro do Azure AD, o Azure AD Connect gera um evento com EventID 6941 e a mensagem *"o objeto aprovisionado é demasiado grande. Compacte o número de valores de atributo neste objeto."* Ao mesmo tempo, o Azure AD Connect também gera um evento enganosa com EventID 6900 e a mensagem *"Microsoft.Online.Coexistence.ProvisionRetryException: não foi possível comunicar com o Windows service do Azure Active Directory."* Para minimizar a confusão, o Azure AD Connect já não gera o evento última quando é recebido LargeObject erro.
* Foi corrigido um problema que faz com que o Gestor do serviço de sincronização para deixar de responder quando tentar atualizar a configuração do conector LDAP genérico.

**Funcionalidades novas/melhorias:**

Sincronização do Azure AD Connect
* Alterações de regra de sincronização – as seguintes alterações de regra de sincronização foi implementadas:
  * Conjunto de regras de sincronização de predefinição atualizado não exportar atributos **userCertificate** e **userSMIMECertificate** se os atributos têm mais de 15 valores.
  * Atributos de AD **campo IDdeEmpregado** e **msExchBypassModerationLink** estão agora incluídas no conjunto de regras de sincronização de predefinição.
  * Atributo de AD **fotografia** foi removido do conjunto de regras de sincronização de predefinição.
  * Adicionar **preferredDataLocation** para o esquema do Metaverso e esquema de conector AAD. Os clientes que pretendem atualizar qualquer um dos atributos no Azure AD, podem implementar regras de sincronização personalizados para o fazer. Para saber mais sobre o atributo, consulte a secção do artigo [sincronização do Azure AD Connect: como efetuar uma alteração para a configuração predefinida - permitir a sincronização de PreferredDataLocation](active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-preferreddatalocation).
  * Adicionar **userType** para o esquema do Metaverso e esquema de conector AAD. Os clientes que pretendem atualizar qualquer um dos atributos no Azure AD, podem implementar regras de sincronização personalizados para o fazer.

* O Azure AD Connect agora ativa automaticamente a utilização do atributo ConsistencyGuid como o atributo âncora de origem para on-premises objetos do AD. Adicional, Azure AD Connect preenche o atributo ConsistencyGuid com o valor do atributo de objectGuid se estiver vazia. Esta funcionalidade é aplicável a nova implementação apenas. Para obter mais informações sobre esta funcionalidade, consulte a secção do artigo [do Azure AD Connect: conceitos - utilizando msDS-ConsistencyGuid como sourceAnchor de Design](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Resolução de problemas novo cmdlet Invoke-ADSyncDiagnostics foi adicionado ajudar a diagnosticar a sincronização de Hash de palavra-passe problemas relacionados com o. Para obter informações sobre como utilizar o cmdlet, consulte o artigo [resolver problemas de sincronização de palavras-passe com a sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-synchronization).
* O Azure AD Connect, agora suporta a pasta de pública Mail-Enabled sincronizar objetos de AD no local ao Azure AD. Pode ativar a funcionalidade com o Assistente do Azure AD Connect em funcionalidades opcionais. Para obter mais informações sobre esta funcionalidade, consulte o artigo [Office 365 diretório baseado em Edge bloquear suporte para o local correio ativado pastas públicas](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* O Azure AD Connect requer um anúncio conta DS para sincronizar a partir do AD no local. Anteriormente, se tiver instalado o Azure AD Connect, utilizando o modo de rápida, pode fornecer que as credenciais de uma conta de administrador de empresa e do Azure AD Connect iriam criar a conta de AD DS necessária. No entanto, para uma instalação personalizada e adicionar florestas a uma implementação existente, era necessário para fornecer em vez disso, a conta do AD DS. Agora, tem também a opção para fornecer as credenciais de uma conta de administrador de empresa durante uma instalação personalizada e permitir que o Azure AD Connect, criar a conta do AD DS necessária.
* O Azure AD Connect suporta agora AOA do SQL Server. Tem de ativar o SQL Server AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect Deteta se a instância do SQL Server fornecida está ativada para SQL Server AOA ou não. Se AOA do SQL Server estiver ativada, o Azure AD Connect mais figuras se AOA do SQL Server está configurado para utilizar replicação síncrona ou assíncrona. Quando configurar o serviço de escuta do grupo de disponibilidade, é recomendável que defina a propriedade RegisterAllProvidersIP para 0. Isto acontece porque o Azure AD Connect utiliza atualmente SQL Native Client para ligar ao SQL Server e SQL Server Native Client não suporta a utilização da propriedade MultiSubNetFailover.
* Se estiver a utilizar o LocalDB como a base de dados para o servidor do Azure AD Connect e atingiu o limite de tamanho de 10 GB, o serviço de sincronização já não for iniciado. Anteriormente, terá de efetuar a operação de ShrinkDatabase num LocalDB para recuperar espaço suficiente DB para o serviço de sincronização para iniciar. Após o qual, pode utilizar o Synchronization Service Manager para eliminar o histórico de execução para recuperar o espaço de DB mais. Agora, pode utilizar cmdlet Start-ADSyncPurgeRunHistory para dados do histórico de remoção de execução da LocalDB para recuperar o espaço de base de dados. Além disso, este cmdlet suporta o modo offline (especificando o parâmetro - offline) que pode ser utilizado quando o serviço de sincronização não está em execução. Nota: O modo offline apenas pode ser utilizado se o serviço de sincronização não está em execução e a base de dados utilizado LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessário, do Azure AD Connect comprime agora detalhes do erro de sincronização antes de os armazenando num bases de dados LocalDB/SQL. Ao atualizar a partir de uma versão mais antiga do Azure AD Connect para esta versão, o Azure AD Connect efetua uma única compressão detalhes do erro de sincronização existentes.
* Anteriormente, depois de atualizar a configuração de filtragem de UO, manualmente tem de executar uma importação completa para garantir a objetos existentes são corretamente incluído/excluído da sincronização de diretórios. Agora, o Azure AD Connect aciona automaticamente a importação completa durante a sincronização seguinte ciclo. Importação completa, mais só é possível aplicar aos conectores de AD afetados pela atualização. Nota: Este melhoramento é aplicável a UO filtragem de atualizações efetuadas utilizando o Assistente do Azure AD Connect apenas. Não é aplicável a filtragem efetuadas utilizando o Gestor do serviço de sincronização de atualizações de UO.
* Anteriormente, suporta a filtragem baseada no grupo de utilizadores, grupos e contacte apenas os objetos. Agora, com base no grupo de filtragem também suporta objetos de computador.
* Anteriormente, pode eliminar dados de espaço de conector sem a desativação do agendador de sincronização do Azure AD Connect. Agora, o Synchronization Service Manager bloqueia a eliminação de dados de espaço de conector se detetar que o programador está ativado. Além disso, é devolvido um aviso a informar os clientes sobre potencial perda de dados se os dados de espaço de conector são eliminados.
* Anteriormente, tem de desativar transcription do PowerShell para o Assistente do Azure AD Connect ser executada corretamente. Este problema seja resolvido parcialmente. Pode ativar PowerShell transcription se estiver a utilizar o Assistente do Azure AD Connect para gerir a configuração da sincronização. Tem de desativar PowerShell transcription se estiver a utilizar o Assistente do Azure AD Connect para gerir a configuração do AD FS.



## <a name="114860"></a>1.1.486.0
Data da versão: Abril de 2017

**Fixos problemas:**
* Corrigir o problema em que o Azure AD Connect não instalará com êxito na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Data da versão: Abril de 2017

**Problemas conhecidos:**

* Esta versão do Azure AD Connect não instalará com êxito se as seguintes condições se verificarem todas as:
   1. Estiver a efetuar a instalação de raiz ou atualização no local de DirSync do Azure AD Connect.
   2. Estão a utilizar uma versão localizada do Windows Server em que o nome do grupo de administrador incorporado no servidor não é "Administradores".
   3. Estão a utilizar a predefinição do SQL Server 2012 Express LocalDB instalado com o Azure AD Connect, em vez de fornecer a suas próprias SQL completo.

**Fixos problemas:**

Sincronização do Azure AD Connect
* Foi corrigido um problema em que o agendador de sincronização ignore o passo de sincronização completo se um ou mais conectores estão em falta para esse passo de sincronização do perfil de execução. Por exemplo, adicionasse manualmente um conector a utilizar o Synchronization Service Manager sem criar um perfil de execução para a importação Delta. Esta correção assegura que o agendador de sincronização continua a executar a importação Delta para outros conectores.
* Corrigido um problema onde o serviço de sincronização imediatamente para o processamento um perfil de execução quando é detetar um problema com um dos passos de execução. Esta correção assegura que o serviço de sincronização ignora que executam o passo e continua a processar o resto. Por exemplo, ter uma importação Delta executar perfil para o conector do AD com vários passos de execução (um para cada local domínio AD). O serviço de sincronização irá executar a importação Delta com outros domínios de AD, mesmo se um deles tem problemas de conectividade de rede.
* Foi corrigido um problema que faz com que a atualização do conector do Azure AD a ser ignorada durante a atualização automática.
* Corrigir um problema que faz com que o Azure AD Connect para incorretamente determinar se o servidor for um controlador de domínio durante a configuração, que por sua vez em faz com que o DirSync atualização falhar.
* Foi corrigido um problema que faz com que a atualização no local de DirSync por não criar perfil de execução para o conector do Azure AD.
* Foi corrigido um problema em que a interface de utilizador do Gestor do serviço de sincronização deixar de responder ao tentar configurar o conector de LDAP genérico.

Gestão do AD FS
* Foi corrigido um problema em que o Assistente do Azure AD Connect falha se o nó principal do AD FS foi movido para outro servidor.

Ambiente de trabalho SSO
* Foi corrigido um problema no Assistente do Azure AD Connect onde o ecrã de início de sessão não permitem-lhe ativar a funcionalidade de ambiente de trabalho SSO, se tiver escolhido a sincronização de palavra-passe como a opção de início de sessão durante a instalação de novo.

**Funcionalidades novas/melhorias:**

Sincronização do Azure AD Connect
* Azure AD Connect sincronizar agora suporta a utilização da conta de serviço do Virtual, conta de serviço gerida e conta de serviço gerida de grupo à sua conta de serviço. Isto aplica-se a nova instalação do Azure AD Connect apenas. Quando instalar o Azure AD Connect:
    * Por predefinição, o Assistente do Azure AD Connect irá criar uma conta de serviço do Virtual e utiliza-o à sua conta de serviço.
    * Se estiver a instalar num controlador de domínio, o Azure AD Connect retrocede para o comportamento anterior onde irá criar uma conta de utilizador de domínio e utiliza-o em vez disso, como a conta de serviço.
    * Pode substituir o comportamento predefinido, fornecendo um dos seguintes:
      * Conta de serviço gerida de um grupo
      * Uma conta de serviço gerida
      * Uma conta de utilizador de domínio
      * Uma conta de utilizador local
* Anteriormente, se atualizar para uma nova compilação do Azure AD Connect contendo conectores atualizar ou alterações de regra de sincronização, o Azure AD Connect serão acionado um ciclo de sincronização completa. Agora, o Azure AD Connect seletivamente aciona uma importação completa passo apenas para os conectores com a atualização e passo de sincronização completa apenas para os conectores com alterações de regra de sincronização.
* Anteriormente, o limiar de eliminação exportar só se aplica exportações que são acionadas através o agendador de sincronização. Agora, a funcionalidade é expandida para incluir exportações acionadas manualmente ao cliente utilizar o Synchronization Service Manager.
* No seu inquilino do Azure AD, não há uma configuração de serviço que indica se a funcionalidade de sincronização de palavra-passe está ativada para o seu inquilino ou não. Anteriormente, é mais fácil para a configuração do serviço estar incorretamente configurado pelo Azure AD Connect, se tiver uma ativa e o servidor de transição. Agora, o Azure AD Connect irá tentar manter a configuração do serviço consistente com o Active Directory apenas o servidor do Azure AD Connect.
* O Azure AD Connect, o assistente agora Deteta e devolve um aviso se no local do AD não tem a Reciclagem do AD ativado.
* Anteriormente, a exportar do Azure AD tempos limite e falha se determinado limiar excede o tamanho combinado de objetos no batch. Agora, o serviço de sincronização serão questão reenviar os objetos em lotes separados, mais pequenos, se for detetado o problema.
* A aplicação de gestão de chaves do serviço de sincronização foi removida do Menu Iniciar do Windows. Gestão da chave de encriptação continuará a ser suportada através da interface de linha de comandos utilizando miiskmu.exe. Para obter informações sobre a gestão de chave de encriptação, consulte o artigo [abandonar a chave de encriptação do Azure AD Connect sincronização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Anteriormente, se alterar a palavra-passe da conta de serviço sincronização do Azure AD Connect, o serviço de sincronização não será possível iniciar corretamente até ter abandonado a chave de encriptação e a reinicialização da palavra-passe da conta de serviço sincronização do Azure AD Connect. Agora, já não é necessária.

Ambiente de trabalho SSO

* Assistente do Azure AD Connect não exige período porta 9090 abertas na rede, ao configurar a autenticação pass-through e SSO de ambiente de trabalho. Só é necessária a porta 443. 

## <a name="114430"></a>1.1.443.0
Data da versão: de 2017 de Março

**Fixos problemas:**

Sincronização do Azure AD Connect
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect falhar caso o nome a apresentar do conector do Azure AD não contém o domínio onmicrosoft.com inicial atribuído ao inquilino do Azure AD.
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect falhar ao efetuar a ligação à base de dados SQL quando a palavra-passe da conta de serviço de sincronização contém carateres especiais, como o apóstrofo, dois pontos e espaço.
* Foi corrigido um problema que faz com o erro "o dimage tem uma âncora de que é diferente do que a imagem" que seja efetuada num servidor do Azure AD Connect no modo de teste, depois de excluiu temporariamente no local AD de objeto de sincronização e, em seguida, incluído-a novamente para a sincronizar.
* Foi corrigido um problema que faz com o erro "o objeto localizado por DN é um fantasma" que seja efetuada num servidor do Azure AD Connect no modo de teste, depois de excluiu temporariamente no local AD de objeto de sincronização e, em seguida, incluído-a novamente para a sincronizar.

Gestão do AD FS
* Foi corrigido um problema onde o Assistente do Azure AD Connect não atualizar configuração do AD FS e definir as afirmações direita em que a confiança da entidade confiadora após a configuração do ID de início de sessão alternativo.
* Foi corrigido um problema em que o Assistente do Azure AD Connect não consegue processar corretamente servidores AD FS cujas contas de serviço são configuradas utilizando o formato de userPrincipalName em vez de formato de sAMAccountName.

Autenticação pass-through
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect falhar caso a passar através de autenticação está selecionada, mas falha de registo do seu conector.
* Foi corrigido um problema que faz com que o Assistente do Azure AD Connect para ignorar a validação verifica no método de início de sessão selecionado quando a funcionalidade de ambiente de trabalho SSO está ativada.

Reposição de palavra-passe
* Foi corrigido um problema que pode fazer com que o servidor do Azure AAD Connect tente ligar novamente se a ligação foi cancelada por uma firewall ou proxy.

**Funcionalidades novas/melhorias:**

Sincronização do Azure AD Connect
* Cmdlet Get-ADSyncScheduler devolve agora uma nova propriedade booleana denominada SyncCycleInProgress. Se o valor devolvido for VERDADEIRO, significa que há um ciclo de sincronização agendada em curso.
* Pasta de destino para armazenar os registos de configuração e instalação do Azure AD Connect tem sido movida %localappdata%\AADConnect para %programdata%\AADConnect para melhorar a acessibilidade para os ficheiros de registo.

Gestão do AD FS
* Suporte adicionado para atualizar o certificado de SSL de Farm do AD FS.
* Suporte adicionado para gerir o AD FS 2016.
* Pode agora especificar gMSA existente (grupo conta de serviço gerida) durante a instalação do AD FS.
* Agora, pode configurar o SHA-256 como o algoritmo de hash de assinatura do Azure AD confiança da entidade confiadora.

Reposição de palavra-passe
* Introduziu melhorias para permitir que o produto para a função em ambientes com regras de firewall mais rigorosos.
* Fiabilidade melhorada de ligação para o Service Bus do Azure.

## <a name="113800"></a>1.1.380.0
Data da versão: Dezembro de 2016

**Problema fixo:**

* Corrigir o problema em que a regra de afirmação issuerid para serviços de Federação do Active Directory (AD FS) está em falta neste compilação.

>[!NOTE]
>Este compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

## <a name="113710"></a>1.1.371.0
Data da versão: Dezembro de 2016

**Problema conhecido:**

* A regra de afirmação issuerid para o AD FS está em falta neste compilação. A regra de afirmação issuerid é necessária se a Federação de vários domínios com o Azure Active Directory (Azure AD). Se estiver a utilizar o Azure AD Connect para gerir no local implementação do AD FS, a atualização para esta compilação remove a regra de afirmação issuerid existentes da sua configuração do AD FS. Pode contornar o problema ao adicionar a regra de afirmação issuerid após a instalação/atualização. Para obter detalhes sobre o issuerid a adição de regras de afirmações, consulte este artigo em [suporte para vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problema fixo:**

* Se a porta 9090 não está aberto para a ligação de saída, a instalação do Azure AD Connect ou a atualização falhará.

>[!NOTE]
>Este compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

## <a name="113700"></a>1.1.370.0
Data da versão: Dezembro de 2016

**Problemas conhecidos:**

* A regra de afirmação issuerid para o AD FS está em falta neste compilação. A regra de afirmação issuerid é necessária se a Federação de vários domínios com o Azure AD. Se estiver a utilizar o Azure AD Connect para gerir no local implementação do AD FS, a atualização para esta compilação remove a regra de afirmação issuerid existentes da sua configuração do AD FS. Pode contornar o problema ao adicionar a regra de afirmação issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar issuerid de regras de afirmações, consulte este artigo em [suporte para vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).
* Porta 9090 tem de ser abrir saída para concluir a instalação.

**Novas funcionalidades:**

* Autenticação pass-through (pré-visualização).

>[!NOTE]
>Este compilação não está disponível para clientes através da funcionalidade do Azure AD Connect automática atualizar.

## <a name="113430"></a>1.1.343.0
Data da versão: Novembro de 2016

**Problema conhecido:**

* A regra de afirmação issuerid para o AD FS está em falta neste compilação. A regra de afirmação issuerid é necessária se a Federação de vários domínios com o Azure AD. Se estiver a utilizar o Azure AD Connect para gerir no local implementação do AD FS, a atualização para esta compilação remove a regra de afirmação issuerid existentes da sua configuração do AD FS. Pode contornar o problema ao adicionar a regra de afirmação issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar issuerid de regras de afirmações, consulte este artigo em [suporte para vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).

**Fixos problemas:**

* Por vezes, instalar o Azure AD Connect irá falhar porque não é possível criar uma conta de serviço local cuja palavras-passe cumpra o nível de complexidade especificado pela política de palavra-passe da organização.
* Corrigido um problema em que as regras de associação não são reavaliadas quando um objeto no espaço de conector em simultâneo fica fora do âmbito para uma regra de associação e tornar-se no âmbito para outro. Isto pode acontecer se tiver duas ou mais regras de associação cujas condições de associação são mutuamente exclusivas.
* Foi corrigido um problema onde regras de sincronização de entrada (a partir do Azure AD), que não contêm regras de associação, não são processadas se tiverem valores de prioridade inferiores que os contém regras de associação.

**Melhoramentos:**

* Suporte adicionado para instalar o Azure AD Connect, no Windows Server 2016 Standard ou superior.
* Suporte adicionado para SQL Server 2016 a utilizar como base de dados remota do Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Data da versão: Agosto de 2016

**Fixos problemas:**

* Alterações ao sincronizar o intervalo não ocorrer até após a conclusão do ciclo de sincronização seguinte.
* Assistente do Azure AD Connect não aceita uma conta do Azure AD cujo nome de utilizador começa com um caráter de sublinhado (\_).
* Assistente do Azure AD Connect não consegue autenticar a conta do Azure AD, se a palavra-passe de conta contém demasiados carateres especiais. Mensagem de erro "não é possível validar as credenciais. Tiver ocorrido um erro inesperado." é devolvido.
* Desinstalar o servidor de teste desativa a sincronização de palavras-passe no inquilino do Azure AD e faz com que a sincronização de palavras-passe falhar com o servidor de Active Directory.
* Sincronização de palavra-passe falhar invulgar casos quando não existe nenhum hash de palavra-passe armazenado no utilizador.
* Quando o servidor do Azure AD Connect está ativado para o modo de teste, repetição de escrita de palavras-passe não está temporariamente desativada.
* Assistente do Azure AD Connect não mostrar a sincronização de palavra-passe real e a configuração de repetição de escrita de palavras-passe quando o servidor está no modo de teste. Sempre mostra-los como desativados.
* Alterações de configuração para a sincronização de palavra-passe e a repetição de escrita de palavras-passe não são mantidas pelo Assistente do Azure AD Connect, quando o servidor está no modo de teste.

**Melhoramentos:**

* Atualizar o cmdlet Start-ADSyncSyncCycle para indicar se é possível iniciar um novo ciclo de sincronização ou não.
* Adicionar o cmdlet Stop-ADSyncSyncCycle para terminar o ciclo de sincronização e a operação, que estão atualmente em curso.
* Atualizar o cmdlet Stop-ADSyncScheduler para terminar o ciclo de sincronização e a operação, que estão atualmente em curso.
* Quando configurar [extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md) no Assistente do Azure AD Connect, agora é possível selecionar o atributo de AD do Azure do tipo "Cadeia Teletex".

## <a name="111890"></a>1.1.189.0
Data da versão: Junho de 2016

**Fixos problemas e melhorias:**

* Agora pode ser instalado o Azure AD Connect num servidor compatíveis com FIPS.
  * Para a sincronização de palavra-passe, consulte [sincronização de palavra-passe e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Foi corrigido um problema onde não foi possível resolver um nome NetBIOS para o FQDN no conector do Active Directory.

## <a name="111800"></a>1.1.180.0
Data da versão: Maio de 2016

**Novas funcionalidades:**

* Avisa e ajuda-o a verificar a domínios, se não tiver fazê-lo antes de executar o Azure AD Connect.
* Foi adicionado suporte para [Datacenters da Microsoft Cloud](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Foi adicionado suporte para a versão mais recente [cloud do Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infraestrutura com novos requisitos de URL.

**Fixos problemas e melhorias:**

* Adicionar a filtragem para o Editor de regra da sincronização para facilitar a localizar as regras de sincronização.
* Melhoria do desempenho aquando da eliminação de um espaço de conector.
* Quando o mesmo objeto foi eliminado e adicionar a mesma execução (chamado delete/adicionar) foi corrigido um problema.
* Uma sincronização desativada da regra já não volta a ativar incluíam objectos e atualizar atributos no esquema de atualização ou diretório.

## <a name="111300"></a>1.1.130.0
Data da versão: Abril de 2016

**Novas funcionalidades:**

* Foi adicionado suporte para atributos com múltiplos valores para [extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md).
* Foi adicionado suporte para obter mais variações de configuração para [a atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) equacionar elegível para atualização.
* Adicionar alguns cmdlets para [programador personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Data da versão: Março de 2016

**Fixos problemas:**

* Efetuada se a instalação Express não pode ser utilizada no Windows Server 2008 (pré-R2), porque a sincronização de palavra-passe não é suportado neste sistema operativo.
* Atualizar do DirSync com uma configuração de filtro personalizado não foi possível funcionar conforme esperado.
* Ao atualizar para uma versão mais recente e não foram efetuadas alterações à configuração, não devem ser agendada uma importação completa/sincronização.

## <a name="111100"></a>1.1.110.0
Data da versão: Fevereiro de 2016

**Fixos problemas:**

* Atualização a partir de versões anteriores não funciona se a instalação não está na pasta C:\Program Files predefinida.
* Se instalar e limpar **iniciar o processo de sincronização** no final do Assistente de instalação, executar o Assistente de instalação de uma segunda vez não permitirão o programador.
* O Programador de não funciona conforme esperado nos servidores onde o formato de data/hora US-en não é utilizado. Também irá bloquear o mesmo `Get-ADSyncScheduler` para devolver vezes corretos.
* Se tiver instalado uma versão anterior do Azure AD Connect com o AD FS como a opção de início de sessão e a atualização, não é possível executar o Assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Data da versão: Fevereiro de 2016

**Novas funcionalidades:**

* [A atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) funcionalidade para clientes de definições rápidas.
* Suporte para o administrador global utilizando o Azure multi-factor Authentication e Privileged Identity Management no Assistente de instalação.
  * Terá de permitir que o proxy permitir o tráfego para https://secure.aadcdn.microsoftonline-p.com também se utilizar o multi-factor Authentication.
  * Tem de adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites fidedignos para autenticação Multifator funcionar corretamente.
* Permita a alteração do método de início de sessão do utilizador após a instalação inicial.
* Permitir [domínio e a UO filtragem](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no Assistente de instalação. Isto também permite que a ligação para todas as florestas onde nem todos os domínios estão disponíveis.
* [Programador](active-directory-aadconnectsync-feature-scheduler.md) está incorporada para o motor de sincronização.

**Funcionalidades promovidas da pré-visualização para GA:**

* [Repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md).
* [Extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md).

**Novas funcionalidades de pré-visualização:**

* A nova predefinição sincronizar o intervalo é de 30 minutos de ciclo. Utilizado para ser três horas para todas as versões anteriores. Adiciona suporte para alterar o [programador](active-directory-aadconnectsync-feature-scheduler.md) comportamento.

**Fixos problemas:**

* A página de domínios DNS verifique sempre não reconhece os domínios.
* Pedidos de credenciais de administrador de domínio quando configurar o AD FS.
* O local contas AD não são reconhecidas pelo Assistente de instalação se encontra num domínio com uma árvore DNS diferente que o domínio de raiz.

## <a name="1091310"></a>1.0.9131.0
Data da versão: Dezembro de 2015

**Fixos problemas:**

* Sincronização de palavra-passe não poderá funcionar quando alterar as palavras-passe no Active Directory Domain Services (AD DS), mas funciona quando definiu uma palavra-passe.
* Quando tiver um servidor proxy, a autenticação para o Azure AD pode falhar durante a instalação, ou se uma atualização é cancelada na página de configuração.
* Atualizar a partir de uma versão anterior do Azure AD Connect com uma instância completa do SQL Server irá falhar se não for um administrador de sistema (SA) do SQL Server.
* Atualizar a partir de uma versão anterior do Azure AD Connect com o SQL Server remoto mostra o erro "Não é possível aceder a SQL do ADSync da base de dados".

## <a name="1091250"></a>1.0.9125.0
Data da versão: Novembro de 2015

**Novas funcionalidades:**

* Pode reconfigurar o AD FS para confiança do Azure AD.
* Pode atualizar o esquema do Active Directory e voltar a gerar regras de sincronização.
* Pode desativar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um literal novo numa regra de sincronização.

**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para sincronização](../connect-health/active-directory-aadconnect-health-sync.md).
* Suporte para [serviços de domínio do Azure AD](../active-directory-passwords-update-your-own-password.md) sincronização de palavra-passe.

**Novo cenário suportado:**

* Suporta várias organizações de Exchange no local. Para obter mais informações, consulte [implementações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Fixos problemas:**

* Problemas de sincronização de palavra-passe:
  * Um objeto movido fora do âmbito para no âmbito não terão a respetiva palavra-passe sincronizado. Isto inclui UO e filtragem de atributos.
  * Selecionar uma nova UO para incluir sincronizadas não requer uma sincronização de palavras-passe completa.
  * Quando um utilizador desativado está ativado a palavra-passe não sincronizar.
  * A fila de tentativas de palavra-passe é infinita e o limite de 5000 objetos serão descontinuados anterior foi removido.
* Não é possível ligar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo que é utilizado para filtragem de grupos após a instalação inicial.
* Já não cria um novo perfil de utilizador no servidor do Azure AD Connect para cada utilizador efetuar uma alteração de palavra-passe com repetição de escrita de palavras-passe ativada.
* Não é possível utilizar o número inteiro longo valores de âmbitos de regras de sincronização.
* A caixa de verificação "repetição de escrita do dispositivo" continua a ser desativada se existirem controladores de domínio inacessível.

## <a name="1086670"></a>1.0.8667.0
Data da versão: Agosto de 2015

**Novas funcionalidades:**

* O Assistente de instalação do Azure AD Connect é agora localizado em todos os idiomas do Windows Server.
* O desbloqueio de suporte adicionado para a conta ao utilizar a gestão de palavras-passe do Azure AD.

**Fixos problemas:**

* Assistente de instalação do Azure AD Connect falhas se outro utilizador continuar a instalação em vez da pessoa que iniciado pela primeira vez a instalação.
* Se uma desinstalação anterior do Azure AD Connect não conseguir desinstalar corretamente a sincronização do Azure AD Connect, não é possível reinstalar.
* Não é possível instalar o Azure AD Connect com instalação rápida, se o utilizador não está a ser o domínio de raiz da floresta ou se for utilizada uma versão não inglesa do Active Directory.
* Se não é possível resolver o FQDN da conta de utilizador do Active Directory, é apresentada uma mensagem de erro enganosa "Falha ao confirmar o esquema".
* Se a conta utilizada no conector do Active Directory foi alterada fora do assistente, o assistente falhará em execuções subsequentes.
* O Azure AD Connect, por vezes, Falha ao instalar num controlador de domínio.
* Não é possível ativar e desativar o "Modo de teste" se foram adicionados os atributos de extensão.
* Repetição de escrita de palavras-passe falhará em algumas configurações devido a uma palavra-passe incorreta no conector do Active Directory.
* Não é possível atualizar o DirSync, se um nome exclusivo (DN) é utilizado na filtragem de atributos.
* Utilização excessiva da CPU ao utilizar a reposição de palavra-passe.

**Funcionalidades de pré-visualização removido:**

* A funcionalidade de pré-visualização [repetição de escrita do utilizador](active-directory-aadconnect-feature-preview.md#user-writeback) temporariamente foi removida com base nos comentários dos nossos clientes de pré-visualização. Estas serão adicionadas mais tarde após podemos ter resolvido o feedback fornecido.

## <a name="1086410"></a>1.0.8641.0
Data da versão: Junho de 2015

**Versão inicial do Azure AD Connect.**

Nome do Azure AD Sync foi alterada para o Azure AD Connect.

**Novas funcionalidades:**

* [Definições rápidas](active-directory-aadconnect-get-started-express.md) instalação
* Pode [configurar o AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Pode [atualização do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Introduzida [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode)

**Novas funcionalidades de pré-visualização:**

* [Repetição de escrita do utilizador](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Repetição de escrita do grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md)
* [Extensões de diretórios](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Data da versão: Maio de 2015

**Novo requisito:**

* Azure AD Sync necessita agora a versão do .NET Framework 4.5.1 ser instalada.

**Fixos problemas:**

* Repetição de escrita de palavras-passe do Azure AD está a falhar com um erro de conectividade do Service Bus do Azure.

## <a name="104910413"></a>1.0.491.0413
Data da versão: Abril de 2015

**Fixos problemas e melhorias:**

* O conector do Active Directory não processa eliminações corretamente se estiver ativada a Reciclagem e existem vários domínios na floresta.
* O desempenho das operações de importação foi melhorado para o conector Azure Active Directory.
* Quando um grupo excedeu o limite de associação (por predefinição, o limite está definido como 50 000 objetos), o grupo foi eliminado no Azure Active Directory. Com o novo comportamento, não é possível eliminar o grupo, emitido um erro e novas alterações de associação não são exportadas.
* Não é possível aprovisionar um novo objeto se um eliminar testada com o mesmo DN já se encontra presente no espaço de conector.
* Alguns objetos estão marcados para sincronização durante a sincronização de diferenças, mesmo apesar de não existir nenhuma alteração testada no objeto.
* Forçar uma sincronização de palavra-passe ação também remove a lista de DC preferencial.
* CSExportAnalyzer tem problemas com os Estados de alguns objetos.

**Novas funcionalidades:**

* Uma associação pode agora ligar a "ANY" tipo de objeto de MV.

## <a name="104850222"></a>1.0.485.0222
Data da versão: Fevereiro de 2015

**Melhoramentos:**

* Desempenho melhorado de importação.

**Fixos problemas:**

* Sincronização de palavra-passe honra atributo cloudFiltered que é utilizado pelo filtragem de atributos. Objetos filtrados já não estão no âmbito da sincronização de palavra-passe.
* Em situações raras, onde a topologia tinha muitos controladores de domínio, a sincronização de palavra-passe não funciona.
* Foi ativado "Parou-server" ao importar a partir do conector do Azure AD depois de gestão de dispositivos no Azure AD/Intune.
* Associação externa principais de segurança (FSPs) a partir de vários domínios na mesma floresta, causa um erro de associação ambígua.

## <a name="104751202"></a>1.0.475.1202
Data da versão: Dezembro de 2014

**Novas funcionalidades:**

* Sincronização de palavras-passe com a filtragem baseada no atributo é agora suportada. Para obter mais informações, consulte [sincronização de palavras-passe com filtragem](active-directory-aadconnectsync-configure-filtering.md).
* O atributo msDS-ExternalDirectoryObjectID é gravado do Active Directory. Esta funcionalidade adiciona suporte para aplicações do Office 365. Utiliza o OAuth2 para aceder a uma implementação híbrida do Exchange de caixas de correio de Online e no local.

**Problemas de atualização fixos:**

* Está disponível no servidor de uma versão mais recente do Assistente de início de sessão.
* Foi utilizado um caminho de instalação personalizada para instalar o Azure AD Sync.
* Um critério de associação personalizado inválido bloqueia a atualização.

**Outras correções:**

* Corrigido os modelos de Office Pro Plus.
* Problemas de instalação fixo causados por nomes de utilizador que começam com um travessão.
* Corrigido perder a definição de sourceAnchor ao executar o Assistente de instalação de uma segunda vez.
* Rastreio ETW fixo para a sincronização de palavra-passe.

## <a name="104701023"></a>1.0.470.1023
Data da versão: Outubro de 2014

**Novas funcionalidades:**

* Sincronização de palavras-passe a partir de vários locais do Active Directory para o Azure AD.
* Instalação da IU localizada para todos os idiomas do Windows Server.

**Atualizar a partir do DG AADSync 1.0**

Se já tiver instalado o Azure AD Sync, é um passo adicional, que tem de efetuar no caso de tiver alterado qualquer uma das regras de sincronização de out-of-box. Depois de atualizar para o 1.0.470.1023 de versão, a sincronização regras modificar estão duplicadas. Para cada regra de sincronização modificados, efetue o seguinte:

1.  Localize a regra de sincronização modificou e tome nota das alterações.
* Elimine a regra de sincronização.
* Localize a nova regra de sincronização é criada pelo Azure AD Sync e, em seguida, volte a aplicar as alterações.

**Permissões da conta do Active Directory**

Conta do Active Directory tem de possuir permissões adicionais para poder ler os hashes de palavra-passe do Active Directory. As permissões a conceder são denominadas "Replicar as alterações do" e "Diretório de replicação altera tudo." São necessárias ambas as permissões para conseguir ler os hashes de palavra-passe.

## <a name="104190911"></a>1.0.419.0911
Data da versão: Setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
