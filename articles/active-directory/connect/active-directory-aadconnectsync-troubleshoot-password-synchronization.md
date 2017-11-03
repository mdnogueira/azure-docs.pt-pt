---
title: "Resolver problemas de sincronização de palavras-passe com a sincronização do Azure AD Connect | Microsoft Docs"
description: "Este artigo fornece informações sobre como resolver problemas de sincronização de palavra-passe."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: d3bb2883257896c72cc616ea7476f3d25ee6aa4b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Resolver problemas de sincronização de palavras-passe com a sincronização do Azure AD Connect
Este tópico fornece os passos para resolver problemas com a sincronização de palavra-passe. Se as palavras-passe não estiver a sincronizar conforme esperado, pode ser para um subconjunto de utilizadores ou para todos os utilizadores.

Para implementação de ligar do Azure Active Directory (Azure AD) com a versão 1.1.614.0 ou depois, utilize a tarefa de resolução de problemas no Assistente para resolver problemas de sincronização de palavra-passe:

* Se tiver um problema onde nenhuma palavra-passe é sincronizados, consulte o [nenhuma palavra-passe é sincronizados: resolver problemas utilizando a tarefa de resolução de problemas](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) secção.

* Se tiver um problema com objetos individuais, consulte o [um objeto não está a sincronizar as palavras-passe: resolver problemas utilizando a tarefa de resolução de problemas](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) secção.

Para a implementação com a versão 1.1.524.0 ou posterior, existe um cmdlet de diagnóstico que pode utilizar para resolver problemas de sincronização de palavra-passe:

* Se tiver um problema onde nenhuma palavra-passe é sincronizados, consulte o [nenhuma palavra-passe é sincronizados: resolver problemas utilizando o cmdlet diagnóstico](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) secção.

* Se tiver um problema com objetos individuais, consulte o [um objeto não está a sincronizar as palavras-passe: resolver problemas utilizando o cmdlet diagnóstico](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) secção.

Para versões antigas da implementação do Azure AD Connect:

* Se tiver um problema onde nenhuma palavra-passe é sincronizados, consulte o [nenhuma palavra-passe é sincronizados: manual passos de resolução de problemas](#no-passwords-are-synchronized-manual-troubleshooting-steps) secção.

* Se tiver um problema com objetos individuais, consulte o [um objeto não está a sincronizar as palavras-passe: manual passos de resolução de problemas](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) secção.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Nenhuma palavra-passe é sincronizados: resolver problemas utilizando a tarefa de resolução de problemas
Pode utilizar a tarefa de resolução de problemas para saber por que motivo nenhuma palavra-passe é sincronizados.

> [!NOTE]
> A tarefa de resolução de problemas está disponível apenas para o Azure AD Connect versão 1.1.614.0 ou posterior.

### <a name="run-the-troubleshooting-task"></a>Executar a tarefa de resolução de problemas
Para resolver problemas em que não é sincronizados nenhuma palavra-passe:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Inicie o Assistente do Azure AD Connect.

4. Navegue para o **tarefas adicionais** página, selecione **Troubleshoot**e clique em **seguinte**.

5. Na página de resolução de problemas, clique em **iniciar** para iniciar o menu de resolução de problemas no PowerShell.

6. No menu principal, selecione **resolver problemas de sincronização de palavras-passe**.

7. No sub menu, selecione **sincronização de palavra-passe não funciona em todas as**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa resolução de problemas
A tarefa de resolução de problemas efetua as seguintes verificações:

* Valida que a funcionalidade de sincronização de palavra-passe está ativada para o seu inquilino do Azure AD.

* Valida que o servidor do Azure AD Connect não está no modo de teste.

* Para cada existente conector no local do Active Directory (que corresponde a uma floresta do Active Directory existente):

   * Valida que a funcionalidade de sincronização de palavra-passe está ativada.
   
   * Pesquisa para eventos de heartbeat de sincronização de palavra-passe nos registos de eventos de aplicações do Windows.

   * Para cada domínio do Active Directory no conector do Active Directory no local:

      * Valida que o domínio é acessível a partir do servidor do Azure AD Connect.

      * Valida se as contas de serviços de domínio do Active Directory (AD DS) utilizadas pelo conector do Active Directory no local tem o nome de utilizador correto, palavra-passe e as permissões necessárias para a sincronização de palavra-passe.

O diagrama seguinte ilustra os resultados do cmdlet para uma topologia do domínio único, no local do Active Directory:

![Resultados de diagnóstico para a sincronização de palavra-passe](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalgeneral.png)

As restantes desta secção descreve os resultados específicos que são devolvidos pela tarefa e os problemas correspondentes.

#### <a name="password-synchronization-feature-isnt-enabled"></a>Funcionalidade de sincronização de palavra-passe não está ativada
Se ainda não ativou a sincronização de palavra-passe utilizando o Assistente do Azure AD Connect, é devolvido o erro seguinte:

![Sincronização de palavra-passe não está ativada](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Servidor do Azure AD Connect está no modo de teste
Se o servidor do Azure AD Connect está no modo de teste, sincronização de palavra-passe está temporariamente desativada e é devolvido o erro seguinte:

![Servidor do Azure AD Connect está no modo de teste](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalstaging.png)

#### <a name="no-password-synchronization-heartbeat-events"></a>Não há eventos de heartbeat de sincronização de palavra-passe
Cada conector do Active Directory no local tem a suas próprias canal de sincronização de palavra-passe. Quando o canal de sincronização de palavra-passe é estabelecido e não sabemos de quaisquer alterações de palavra-passe sejam sincronizadas, uma vez a cada 30 minutos sob o registo de eventos de aplicações do Windows é gerado um evento de heartbeat (EventId 654). Para cada conector do Active Directory no local, o cmdlet procura de eventos de heartbeat correspondentes nas últimas três horas. Não se for encontrado nenhum evento de heartbeat, é devolvido o erro seguinte:

![Não existem heart de sincronização de palavra-passe beat eventos](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Conta do AD DS não tem as permissões corretas
Se a conta do AD DS que é utilizada pelo conector do Active Directory no local para sincronizar os hashes de palavra-passe não tiver as permissões adequadas, é devolvido o erro seguinte:

![Credencial incorreto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nome de utilizador ou palavra-passe da conta incorreto do AD DS
Se a conta de AD DS utilizada pelo conector do Active Directory no local para sincronizar os hashes de palavra-passe tem um nome de utilizador incorreto ou a palavra-passe, é devolvido o erro seguinte:

![Credencial incorreto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Um objeto não está a sincronizar as palavras-passe: resolver problemas utilizando a tarefa de resolução de problemas

Pode utilizar a tarefa de resolução de problemas para determinar a razão pela qual um objeto não sincronizar as palavras-passe.

> [!NOTE]
> A tarefa de resolução de problemas está disponível apenas para o Azure AD Connect versão 1.1.614.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Execute o cmdlet de diagnóstico
Para resolver problemas de um objeto de utilizador específico:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Inicie o Assistente do Azure AD Connect.

4. Navegue para o **tarefas adicionais** página, selecione **Troubleshoot**e clique em **seguinte**.

5. Na página de resolução de problemas, clique em **iniciar** para iniciar o menu de resolução de problemas no PowerShell.

6. No menu principal, selecione **resolver problemas de sincronização de palavras-passe**.

7. No sub menu, selecione **palavra-passe não está sincronizado para uma conta de utilizador específica**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa resolução de problemas
A tarefa de resolução de problemas efetua as seguintes verificações:

* Examina o estado do objeto do Active Directory no espaço de conector do Active Directory, o Metaverso e o Azure espaço de conector do AD.

* Valida que existem regras de sincronização com a sincronização de palavra-passe ativada e aplicadas ao objeto do Active Directory.

* Tenta obter e apresentar os resultados da última tentativa para sincronizar a palavra-passe para o objeto.

O diagrama seguinte ilustra os resultados do cmdlet quando resolver problemas de sincronização de palavras-passe para um único objeto:

![Resultados de diagnóstico para a sincronização de palavra-passe - único objeto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectgeneral.png)

As restantes desta secção descreve os resultados específicos devolvidos pelo cmdlet e problemas correspondentes.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>O objeto do Active Directory não se encontra exportado para o Azure AD
Sincronização de palavra-passe para esta conta do Active Directory no local falhar porque não existe nenhum objecto correspondente no inquilino do Azure AD. É devolvido o erro seguinte:

![Objeto do AD do Azure está em falta](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Utilizador tem uma palavra-passe temporária
Atualmente, o Azure AD Connect não suporta a sincronização de palavras-passe temporárias com o Azure AD. Uma palavra-passe é considerada temporário se a **alterar palavra-passe no próximo início de sessão** opção é definida no utilizador do Active Directory no local. É devolvido o erro seguinte:

![Palavra-passe temporária não seja exportado](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultados da última tentativa para sincronizar a palavra-passe não estão disponíveis
Por predefinição, o Azure AD Connect armazena os resultados de tentativas de sincronização de palavra-passe durante sete dias. Se não existirem resultados disponíveis para o objeto selecionado do Active Directory, é devolvido o aviso seguinte:

![Resultados de diagnóstico para um único objeto - não existem históricos de sincronização de palavra-passe](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nenhuma palavra-passe é sincronizados: resolver problemas utilizando o cmdlet de diagnóstico
Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para saber por que motivo nenhuma palavra-passe é sincronizados.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet está disponível apenas para o Azure AD Connect versão 1.1.524.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Execute o cmdlet de diagnóstico
Para resolver problemas em que não é sincronizados nenhuma palavra-passe:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Um objeto não está a sincronizar as palavras-passe: resolver problemas utilizando o cmdlet de diagnóstico
Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para determinar o motivo pelo qual um objeto não sincronizar as palavras-passe.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet está disponível apenas para o Azure AD Connect versão 1.1.524.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Execute o cmdlet de diagnóstico
Para resolver problemas de onde nenhuma palavra-passe é sincronizados para um utilizador:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute o seguinte cmdlet:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Por exemplo:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nenhuma palavra-passe é sincronizados: manual passos de resolução de problemas
Siga estes passos para determinar por que razão não é sincronizados nenhuma palavra-passe:

1. É o servidor de ligação no [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode)? Um servidor no modo de teste não sincronizar as palavras-passe.

2. Execute o script no [obter o estado das definições de sincronização de palavra-passe](#get-the-status-of-password-sync-settings) secção. Proporciona uma descrição geral da configuração da sincronização de palavra-passe.  

    ![Saída do script do PowerShell a partir das definições de sincronização de palavra-passe](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  

3. Se a funcionalidade não está ativada no Azure AD ou se o estado de canal de sincronização não estiver ativado, execute o Assistente de instalação do Connect. Selecione **personalizar as opções de sincronização**e anule a seleção de sincronização de palavra-passe. Esta alteração temporariamente desativa a funcionalidade. Em seguida, execute novamente o assistente e volte a ativar a sincronização de palavra-passe. Execute o script novo para verificar que a configuração estiver correta.

4. Procure no registo de eventos de erros. Procure os seguintes eventos, teriam de indicar um problema:
    * Origem: "A sincronização de diretórios" ID: 0, 611, 652, 655 se vir estes eventos, há um problema de conectividade. A mensagem de registo de eventos contém informações de floresta em que tem um problema. Para obter mais informações, consulte [problema de conectividade](#connectivity problem).

5. Não se vir nenhum heartbeat ou se mais nada trabalhou, executar [acionar uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords). Execute o script apenas uma vez.

6. Consulte o [resolver problemas de um objeto que não está a sincronizar as palavras-passe](#one-object-is-not-synchronizing-passwords) secção.

### <a name="connectivity-problems"></a>Problemas de conectividade

Tem conectividade com o Azure AD?

A conta possui as permissões necessárias para ler os hashes de palavra-passe em todos os domínios? Se instalou Connect utilizando as definições rápidas, as permissões já devem estar corretas. 

Se utilizou uma instalação personalizada, defina manualmente as permissões da seguinte forma:
    
1. Para localizar a conta utilizada pelo conector do Active Directory, inicie **Synchronization Service Manager**. 
 
2. Aceda a **conectores**e, em seguida, procure a floresta do Active Directory no local que esteja a resolver. 
 
3. Seleccione o conector e, em seguida, clique em **propriedades**. 
 
4. Aceda a **ligar à floresta do Active Directory**.  
    
    ![Conta utilizada pelo conector do Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Tenha em atenção que o nome de utilizador e o domínio onde a conta está localizada.
    
5. Iniciar **computadores e utilizadores do Active Directory**e, em seguida, certifique-se de que a conta encontrado anteriormente tem as permissões de siga definida na raiz de todos os domínios na floresta:
    * Replicar as alterações de diretório
    * Alterações de diretório de replicar todos os

6. Os controladores de domínio está acessível pelo Azure AD Connect? Se o servidor de ligação não é possível ligar a todos os controladores de domínio, configure **utilizar apenas o controlador de domínio preferencial**.  
    
    ![Controlador de domínio utilizada pelo conector do Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    
7. Volte ao **Synchronization Service Manager** e **configurar partições de diretório**. 
 
8. Selecione o seu domínio no **selecionar partições de diretório**, selecione o **utilizam apenas os controladores de domínio preferencial** caixa de verificação e, em seguida, clique em **configurar**. 

9. Na lista, introduza os controladores de domínio que deve utilizar Connect para sincronização de palavra-passe. É utilizada a mesma lista de importação e exportação bem. Execute estes passos para todos os seus domínios.

10. Se o script mostra que não há nenhum heartbeat, execute o script no [acionar uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Um objeto não está a sincronizar as palavras-passe: manual passos de resolução de problemas
Pode facilmente resolver problemas de sincronização de palavra-passe, verificando o estado de um objeto.

1. No **computadores e utilizadores do Active Directory**, procure o utilizador e, em seguida, certifique-se de que o **o utilizador deve alterar a palavra-passe no próximo início de sessão** caixa de verificação está desmarcada.  

    ![Active Directory produtivas palavras-passe](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  

    Se a caixa de verificação está selecionada, pedir ao utilizador para iniciar sessão e alterar a palavra-passe. Palavras-passe temporárias não são sincronizadas com o Azure AD.

2. Se a palavra-passe procura correta no Active Directory, siga o utilizador no motor de sincronização. Seguindo o utilizador do Active Directory no local ao Azure AD, pode ver se encontra um erro de descritivo no objeto.

    a. Iniciar o [Gestor do serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md).

    b. Clique em **conectores**.

    c. Selecione o **conector do Active Directory** onde o utilizador está localizado.

    d. Selecione **procurar espaço de conector**.

    e. No **âmbito** caixa, selecione **DN ou âncora**e, em seguida, introduza o DN completo do utilizador que é de resolução de problemas.

    ![Pesquisa de utilizador no espaço de conector com DN](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  

    f. Localize o utilizador está a procurar e, em seguida, clique em **propriedades** para ver todos os atributos. Se o utilizador não está a ser o resultado da pesquisa, certifique-se a [regras de filtragem](active-directory-aadconnectsync-configure-filtering.md) e certifique-se de que executa [aplicar e verificar as alterações](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) para o utilizador a aparecer no Connect.

    g. Para ver os detalhes de sincronização de palavra-passe do objeto da última semana, clique em **registo**.  

    ![Detalhes de registo do objecto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  

    Se o registo de objeto está vazio, o Azure AD Connect foi não é possível ler o hash de palavra-passe do Active Directory. Continuar a resolução de problemas com [erros de conectividade](#connectivity-errors). Se vir qualquer outro valor que **êxito**, consulte a tabela [registo de sincronização de palavra-passe](#password-sync-log).

    h. Selecione o **linhagem** separador e certifique-se de que regra de sincronização, pelo menos, um no **PasswordSync** coluna é **verdadeiro**. A configuração predefinida, o nome da regra de sincronização é **do AD - utilizador AccountEnabled**.  

    ![Informações de linhagem sobre um utilizador](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  

    posso. Clique em **as propriedades do objeto de Metaverso** para apresentar uma lista de atributos de utilizador.  

    ![Informações de Metaverso](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  

    Certifique-se de que não existe nenhum **cloudFiltered** atributo presente. Certifique-se de que os atributos de domínio (domainFQDN e domainNetBios) têm os valores esperados.

    j. Clique em de **conectores** separador. Certifique-se de que vê conectores para o Active Directory no local e o Azure AD.

    ![Informações de Metaverso](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  

    k. Selecione a linha que representa do Azure AD, clique em **propriedades**e, em seguida, clique em de **linhagem** separador. O objeto de espaço de conector deve ter uma regra de saída a **PasswordSync** coluna definida como **verdadeiro**. A configuração predefinida, o nome da regra de sincronização é **Out para o AAD - associar utilizador**.  

    ![Caixa de diálogo de propriedades do objeto de espaço de conector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Registo de sincronização de palavra-passe
A coluna de estado pode ter os seguintes valores:

| Estado | Descrição |
| --- | --- |
| Êxito |Palavra-passe foi sincronizado com êxito. |
| FilteredByTarget |Palavra-passe está definido como **o utilizador deve alterar a palavra-passe no próximo início de sessão**. Palavra-passe não foi sincronizada. |
| NoTargetConnection |Nenhum objecto no metaverso ou no espaço de conector do Azure AD. |
| SourceConnectorNotPresent |Não foi encontrado no espaço de conector do Active Directory no local nenhum objeto. |
| TargetNotExportedToDirectory |O objeto no espaço de conector do Azure AD ainda não foram exportado. |
| MigratedCheckDetailsForMoreInfo |Entrada de registo foi criada antes de compilação 1.0.9125.0 e é apresentada no respetivo estado de legado. |
| Erro |Serviço devolveu um erro desconhecido. |
| Desconhecido |Ocorreu um erro ao tentar processar um lote de hashes de palavra-passe.  |
| MissingAttribute |Atributos específicos (por exemplo, o hash de Kerberos) necessários para os serviços de domínio do Azure AD não estão disponíveis. |
| RetryRequestedByTarget |Atributos específicos (por exemplo, o hash de Kerberos) necessários para os serviços de domínio do Azure AD anteriormente não estavam disponíveis. É efetuada uma tentativa para ressincronizar o hash de palavra-passe do utilizador. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para ajudar a resolução de problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obter o estado das definições de sincronização de palavra-passe
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Acionar uma sincronização completa de todas as palavras-passe
> [!NOTE]
> Execute este script apenas uma vez. Se precisar de mais do que uma vez a executá-la, autorize é o problema. Para resolver o problema, contacte o suporte da Microsoft.

Pode acionar uma sincronização completa de todas as palavras-passe utilizando o script seguinte:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Passos seguintes
* [Implementar a sincronização de palavras-passe com a sincronização do Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Sincronização do Azure AD Connect: Personalizar as opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
