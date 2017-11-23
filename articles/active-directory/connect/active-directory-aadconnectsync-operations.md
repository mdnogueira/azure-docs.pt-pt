---
title: "Sincronização do Azure AD Connect: considerações e tarefas operacionais | Microsoft Docs"
description: "Este tópico descreve as tarefas operacionais para a sincronização do Azure AD Connect e como preparar para o funcionamento deste componente."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 89bfedd282d04569bcf873fd7a9082791a94376b
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronização do Azure AD Connect: considerações e tarefas operacionais
O objetivo deste tópico é descrever tarefas operacionais para a sincronização do Azure AD Connect.

## <a name="staging-mode"></a>Modo de teste
O modo de teste pode ser utilizado para vários cenários, incluindo:

* Elevada disponibilidade.
* Testar e implementar novas alterações de configuração.
* Introduzir um novo servidor e desativar o antigo.

Com um servidor no modo de teste, pode efetuar alterações à configuração e pré-visualizar as alterações antes de efetuar o servidor do Active Directory. Também permite-lhe executar importação completa e uma sincronização completa para confirmar que todas as alterações esperadas antes de efetuar estas alterações para o seu ambiente de produção.

Durante a instalação, pode selecionar o servidor ser **modo de teste**. Esta ação faz com que o servidor do Active Directory para importação e sincronização, mas não é executado qualquer exportações. Um servidor no modo de teste não está em execução sincronização de palavra-passe ou de repetição de escrita de palavras-passe, mesmo se tiver selecionado estas funcionalidades durante a instalação. Quando desativar o modo de teste, o servidor inicia a exportar, permite a sincronização de palavra-passe e permite a repetição de escrita de palavras-passe.

> [!NOTE]
> Suponha que tem do Azure AD Connect com funcionalidade de sincronização de Hash de palavra-passe ativada. Quando ativar o modo de teste, a paragem do servidor sincronização da palavra-passe é alterado de AD no local. Quando desativar o modo de teste, o servidor de retoma de sincronizar as alterações de palavra-passe de onde pela última vez foi deixada. Se o servidor for deixado em modo de teste por um longo período de tempo, pode demorar algum tempo para o servidor para sincronizar todas as alterações de palavra-passe que tinham ocorreu durante o período de tempo.
>
>

Ainda pode forçar uma exportação utilizando o Gestor do serviço de sincronização.

Um servidor no modo de teste continua a receber as alterações do Active Directory e o Azure AD. Tem sempre uma cópia das alterações mais recentes e demore muito rápido podem sobre as responsabilidades de outro servidor. Se efetuar alterações de configuração para o servidor primário, é da responsabilidade do cliente para efetuar as mesmas alterações ao servidor no modo de teste.

Os dos com dados de conhecimento de tecnologias de sincronização mais antigos, o modo de transição é diferente, uma vez que o servidor tem a sua própria base de dados do SQL Server. Esta arquitetura permite ao servidor de modo de teste estar localizados no Centro de dados diferentes.

### <a name="verify-the-configuration-of-a-server"></a>Verificar a configuração de um servidor
Para aplicar este método, siga estes passos:

1. [Preparar](#prepare)
2. [Configuração](#configuration)
3. [Importar e sincronizar](#import-and-synchronize)
4. [Certifique-se](#verify)
5. [Servidor ativo do comutador](#switch-active-server)

#### <a name="prepare"></a>Preparação
1. Instalar o Azure AD Connect, selecione **modo de teste**e anule a seleção **iniciar a sincronização** na última página do Assistente de instalação. Este modo permite-lhe executar manualmente o motor de sincronização.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Início de sessão desativado/início de sessão no e no menu Iniciar, selecione **serviço de sincronização**.

#### <a name="configuration"></a>Configuração
Se efetuou alterações personalizadas para o servidor primário e pretende comparar a configuração com o servidor de teste, em seguida, utilize [documenter de configuração do Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importar e sincronizar
1. Selecione **conectores**e selecione o primeiro conector com o tipo **serviços de domínio do Active Directory**. Clique em **executar**, selecione **completa importação**, e **OK**. Execute estes passos para todas as conexões deste tipo.
2. Seleccione o conector com o tipo **Azure Active Directory (Microsoft)**. Clique em **executar**, selecione **completa importação**, e **OK**.
3. Certifique-se que o separador conectores ainda estiver selecionado. Para cada conector com o tipo **serviços de domínio do Active Directory**, clique em **executar**, selecione **sincronização Delta**, e **OK**.
4. Seleccione o conector com o tipo **Azure Active Directory (Microsoft)**. Clique em **executar**, selecione **sincronização Delta**, e **OK**.

Pode agora ter testado exportação alterações para o Azure AD e AD no local (se estiver a utilizar a implementação híbrida do Exchange). Os passos seguintes permitem-lhe verificar que está prestes a alterar antes de começar, na verdade, a exportação para os diretórios.

#### <a name="verify"></a>Verificar
1. Inicie uma linha de comandos cmd e aceda a`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Execute: `csexport "Name of Connector" %temp%\export.xml /f:x` o nome do conector pode ser encontrado no serviço de sincronização. Tem um nome semelhante a "contoso.com – AAD" para o Azure AD.
3. Execute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` tiver um ficheiro no % temp % denominado export.csv que pode ser reduzida no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportado.
4. Efetue as alterações necessárias para os dados ou a configuração e executar estes passos novamente (importar e sincronizar e verifique) até que as alterações que estão prestes a ser exportados são esperadas.

**Noções sobre o ficheiro export.csv** maioria do ficheiro é facilmente compreensíveis. Alguns abreviaturas para compreender os conteúdos:
* OMODT – o tipo de objeto de modificação. Indica se a operação ao nível do objeto é um adicionar, atualização ou eliminação.
* AMODT – o tipo de modificação do atributo. Indica se a operação de um nível de atributo é um adicionar, atualização ou eliminação.

**Obter os identificadores comuns** o ficheiro de export.csv contém todas as alterações que estão prestes a ser exportado. Cada linha corresponde a uma alteração de um objeto no espaço de conector e o objeto é identificado pelo atributo DN. O atributo de DN é um identificador exclusivo atribuído a um objeto no espaço de conector. Quando tem demasiadas linhas/alterações no export.csv para analisar, poderá ser difícil descobrir que objetos que as alterações são para com base no atributo DN individualmente. Para simplificar o processo de analisar as alterações, utilize o csanalyzer.ps1 script do PowerShell. O script obtém identificadores comuns (por exemplo, displayName, userPrincipalName) dos objetos. Para utilizar o script:
1. Copie o script do PowerShell da secção [CSAnalyzer](#appendix-csanalyzer) num ficheiro denominado `csanalyzer.ps1`.
2. Abra uma janela do PowerShell e navegue para a pasta onde criou o script do PowerShell.
3. Execute: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Tem agora um ficheiro denominado **processedusers1.csv** que pode ser reduzida no Microsoft Excel. Tenha em atenção que o ficheiro fornece um mapeamento do atributo DN de identificadores comuns (por exemplo, displayName e userPrincipalName). Atualmente não inclui as alterações de atributos real que estão prestes a ser exportado.

#### <a name="switch-active-server"></a>Servidor ativo do comutador
1. No servidor atualmente ativo, desative o servidor (FIM/DirSync/Azure AD Sync) pelo que não está a ser exportar para o Azure AD ou defini-lo no modo (Azure AD Connect) de teste.
2. Execute o Assistente de instalação no servidor no **modo de teste** e desativar **modo de teste**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação após desastre
Parte da estrutura de implementação é planear o que fazer, caso exista um desastre onde perder o servidor de sincronização. Existem modelos diferentes a utilizar e qual delas utilizar depende de vários fatores, incluindo:

* O que é a sua tolerância para não ser capaz de disponibilizar alterações a objetos no Azure AD durante o período de indisponibilidade?
* Se utilizar a sincronização de palavra-passe, os utilizadores aceitar que têm de utilizar a palavra-passe antiga no Azure AD, no caso de serem alteração-la no local?
* Tem uma dependência em operações em tempo real, tais como a repetição de escrita de palavras-passe?

Consoante as respostas a estas questões e a política da sua organização, uma das seguintes estratégias pode ser implementada:

* Reconstrua quando necessário.
* Existe um servidor de reserva dinâmica dos componentes de reserva, conhecido como **modo de teste**.
* Utilize máquinas virtuais.

Se não utilizar a base de dados SQL Express incorporada, em seguida, também deve rever o [elevada disponibilidade do SQL Server](#sql-high-availability) secção.

### <a name="rebuild-when-needed"></a>Reconstruir quando necessário
Uma estratégia de viável está a planear uma reconstrução do servidor quando necessário. Normalmente, instalar o motor de sincronização e não que a importação inicial e a sincronização podem ser concluídas entre algumas horas. Se não é um servidor de reserva disponível, é possível utilizar temporariamente um controlador de domínio para alojar o motor de sincronização.

O servidor do motor de sincronização não armazena qualquer estado sobre os objetos para a base de dados pode ser reconstruído a partir dos dados no Active Directory e do Azure AD. O **sourceAnchor** atributo é utilizado para associar os objetos da nuvem e no local. Se reconstruir o servidor com objetos no local existentes e a nuvem, em seguida, o motor de sincronização corresponde a esses objetos em conjunto novamente no reinstalação. As coisas que precisa para documentos e guardar são as alterações de configuração efetuadas no servidor, tais como regras de filtragem e sincronização. Estas configurações personalizadas têm de ser reaplicadas antes de iniciá-la.

### <a name="have-a-spare-standby-server---staging-mode"></a>Existe um servidor de reserva dinâmica reserva - modo de teste
Se tiver um ambiente mais complexo, em seguida, ter um ou mais servidores de reserva dinâmica é recomendado. Durante a instalação, pode ativar um servidor de ser **modo de teste**.

Para obter mais informações, consulte [modo de teste](#staging-mode).

### <a name="use-virtual-machines"></a>Utilize máquinas virtuais
É um método comum e suportado executar o motor de sincronização numa máquina virtual. No caso do anfitrião tem um problema, a imagem com o servidor do motor de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>Elevada disponibilidade do SQL Server
Se não estiver a utilizar o SQL Server Express que vem com o Azure AD Connect, em seguida, elevada disponibilidade para o SQL Server também deve ser considerada. As soluções de elevada disponibilidade suportadas incluem clustering do SQL Server e AOA (grupos de disponibilidade Always). Soluções não suportadas incluem espelhamento.

Foi adicionado suporte para AOA do SQL Server para o Azure AD Connect, versão 1.1.524.0. Tem de ativar o SQL Server AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect Deteta se a instância do SQL Server fornecida está ativada para SQL Server AOA ou não. Se AOA do SQL Server estiver ativada, o Azure AD Connect mais figuras se AOA do SQL Server está configurado para utilizar replicação síncrona ou assíncrona. Quando configurar o serviço de escuta do grupo de disponibilidade, é recomendável que defina a propriedade RegisterAllProvidersIP para 0. Isto acontece porque o Azure AD Connect utiliza atualmente SQL Native Client para ligar ao SQL Server e SQL Server Native Client não suporta a utilização da propriedade MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Apêndice CSAnalyzer
Consulte a secção [verificar](#verify) sobre como utilizar este script.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**  

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)  
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)  
