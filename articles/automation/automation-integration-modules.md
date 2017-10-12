---
title: "Criar um Módulo de Integração da Automatização do Azure | Microsoft Docs"
description: "Tutorial que o orienta através da criação, teste e exemplo de utilização dos módulos de integração na Automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: 27798efb-08b9-45d9-9b41-5ad91a3df41e
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/13/2017
ms.author: magoedte
ms.openlocfilehash: 061a9856ef4a9bc4f569b812a061fad98b044f04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-integration-modules"></a>Módulos de Integração da Automatização do Azure
O PowerShell é a tecnologia fundamental por trás da Automatização do Azure. Uma vez que a Automatização do Azure é baseada no PowerShell, os módulos do PowerShell são essenciais para a extensibilidade da Automatização do Azure. Neste artigo, descrevemos as especificações da utilização da Automatização do Azure de módulos do PowerShell, referidos como "Módulos de Integração", e as melhores práticas para criar os seus módulos do PowerShell para se certificar de que funcionam como Módulos de Integração na Automatização do Azure. 

## <a name="what-is-a-powershell-module"></a>O que é um Módulo do PowerShell?
Um módulo do PowerShell é um grupo de cmdlets do PowerShell como **Get-Data** ou **Copy-Item**, que pode ser utilizado a partir da consola do PowerShell, scripts, fluxos de trabalho, runbooks e recursos do PowerShell DSC como WindowsFeature ou um Ficheiro, que podem ser utilizados a partir de configurações do PowerShell DSC. Todas as funcionalidades do PowerShell estão expostas através de cmdlets e recursos do DSC e todos os recursos do DSC/cmdlets estão protegido por um módulo do PowerShell, muitos dos quais são enviados juntamente com o PowerShell. Por exemplo, o cmdlet **Get-Date** faz parte do módulo do PowerShell Microsoft.PowerShell.Utility, o cmdlet **Copy-Item** faz parte do módulo do PowerShell Microsoft.PowerShell.Management e o recurso do Pacote DSC faz parte do módulo do PowerShell PSDesiredStateConfiguration. Ambos estes módulos são enviados com o PowerShell. Porém, muitos módulos do PowerShell não são enviados como parte do PowerShell e, em vez disso, são distribuídos com produtos proprietários ou de terceiros como o System Center 2012 Configuration Manager ou pela grande comunidade do PowerShell em locais como a Galeria do PowerShell.  Os módulos são úteis porque tornam as tarefas complexas mais simples através da funcionalidade encapsulada.  Pode saber mais sobre os [módulos do PowerShell no MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>O que é um Módulo de Integração da Automatização do Azure?
Um Módulo de Integração não é muito diferente de um módulo do PowerShell. É apenas um módulo do PowerShell que, opcionalmente, contém um ficheiro adicional - um ficheiro de metadados que especifica um tipo de ligação da Automatização do Azure que será utilizado com os cmdlets do módulo em runbooks. Um ficheiro opcional ou não, estes módulos do PowerShell podem ser importados para a Automatização do Azure para tornar os seus cmdlets disponíveis para utilização nos runbooks e os respetivos recursos de DSC disponíveis para utilização nas configurações de DSC. Nos bastidores, a Automatização do Azure armazena estes módulos e, no tempo de execução da tarefa de compilação de DSC e da tarefa de runbook, carrega-os para as sandboxes da Automatização do Azure, onde os runbooks são executados e as configurações de DSC são compiladas.  Quaisquer recursos de DSC nos módulos também são automaticamente colocados no servidor de solicitação do Automation DSC para que possam ser solicitados pelas máquinas que tentam aplicar configurações de DSC.  

Enviamos um número de módulos do Azure PowerShell na Automatização do Azure para que possa utilizar e começar imediatamente a automatizar a gestão do Azure, mas pode importar módulos do PowerShell para qualquer sistema, serviço ou ferramenta com o qual pretende integrar. 

> [!NOTE]
> Determinados módulos são fornecidos como "módulos globais" no serviço de Automatização. Estes módulos globais estão à sua disposição quando cria uma conta de automatização e, às vezes, atualizamo-los, o que faz com que sejam enviados automaticamente para a sua conta de automatização. Se não pretende que sejam atualizados automaticamente, pode sempre importar o módulo e que terá precedência em relação à versão do módulo global desse módulo que incluímos no serviço. 

O formato no qual importa um pacote do Módulo de Integração é um ficheiro comprimido com o mesmo nome que o módulo e uma extensão .zip. Contém o módulo do Windows PowerShell e quaisquer ficheiros de suporte, incluindo um ficheiro de manifesto (. psd1) se o módulo tiver um.

Se o módulo tiver de ter um tipo de ligação da Automatização do Azure, também tem de conter um ficheiro com o nome `<ModuleName>-Automation.json` que especifique as propriedades do tipo de ligação. Este é um ficheiro json colocado dentro da pasta do módulo do seu ficheiro .zip comprimido e contém os campos de uma "ligação" necessários para estabelecer ligação ao sistema ou serviço que o módulo representa. Isto irá criar um tipo de ligação na Automatização do Azure. Ao utilizar este ficheiro, pode definir os nomes de campo, os tipos e se os campos devem ser encriptados e/ou opcionais para o tipo de ligação do módulo. Segue-se um modelo no formato de ficheiro json:

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Se tiver implementado o Service Management Automation e criado pacotes de Módulos de Integração para os runbooks de automatização, isto deve ter um aspeto familiar. 

## <a name="authoring-best-practices"></a>Melhores Práticas de Criação
Embora os Módulos de Integração sejam, essencialmente, módulos do PowerShell, recomendamos, ainda assim, que considere uma série de coisas quando criar um módulo do PowerShell, para torná-lo mais útil para a Automatização do Azure. Alguns deles são específicos da Automatização do Azure e alguns deles são úteis apenas para que os módulos funcionem bem no Fluxo de Trabalho do PowerShell, quer esteja ou não a utilizar a Automatização. 

1. Inclua um resumo, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, pode definir determinadas informações de ajuda para os cmdlets para permitir ao utilizador receber ajuda sobre a utilização dos mesmos com o cmdlet **Get-Help**. Por exemplo, eis como pode definir um resumo e um URI de ajuda para um módulo do PowerShell escrito num ficheiro .psm1.<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> Disponibilizar estas informações não só apresentará esta ajuda relativa à utilização do cmdlet **Get-Help** na consola do PowerShell, como também expõe esta funcionalidade de ajuda dentro da Automatização do Azure.  Por exemplo, quando inserir atividades durante a criação de um runbook. Ao clicar em "Ver ajuda detalhada", abre o URI de ajuda noutro separador do browser que estiver a utilizar para aceder à Automatização do Azure.<br>![Ajuda do Módulo de Integração](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Se o módulo for executado relativamente a um sistema remoto,

    a. Deve conter um ficheiro de metadados do Módulo de Integração que defina as informações necessárias para estabelecer ligação a esse sistema remoto, ou seja, o tipo de ligação.  
    b. Cada cmdlet no módulo deve ser capaz de considerar um objeto de ligação (uma instância desse tipo de ligação) como um parâmetro.  

    Os cmdlets no módulo tornam-se mais fáceis de utilizar na Automatização do Azure se permitir a passagem de um objeto com os campos do tipo de ligação como um parâmetro para o cmdlet. Deste modo, os utilizadores não têm de mapear os parâmetros do recurso de ligação para os parâmetros correspondentes do cmdlet sempre que chamarem um cmdlet. Com base no exemplo de runbook acima, utiliza um recurso de ligação do Twilio denominado CorpTwilio para aceder ao Twilio e devolver todos os números de telefone na conta.  Repara como se mapeia os campos da ligação para os parâmetros do cmdlet?<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Uma abordagem melhor e mais fácil consiste na passagem direta do objeto de ligação para o cmdlet -
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    Pode ativar o comportamento como este para os cmdlets ao permitir que aceitem um objeto de ligação diretamente como um parâmetro, em vez de apenas os campos de ligação para os parâmetros. Normalmente, é aconselhável ter um parâmetro definido para cada um para que um utilizador que não esteja a utilizar a Automatização do Azure possa chamar os seus cmdlets sem construir uma tabela hash para atuar como o objeto de ligação. O conjunto de parâmetros **SpecifyConnectionFields** abaixo é utilizado para a passagem das propriedades do campo de ligação individualmente. **UseConnectionObject** permite a passagem direta da ligação. Como pode ver, o cmdlet Send-TwilioSMS no [módulo Twilio do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) permite a passagem de qualquer uma das formas: 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
3. Defina o tipo de saída para todos os cmdlets no módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense, no momento da conceção, o ajude a determinar as propriedades de saída do cmdlet para uma utilização durante a criação. É especialmente útil durante a criação gráfica de runbooks na Automatização, onde o conhecimento no momento da conceção é fundamental para uma experiência de utilizador fácil com o módulo.<br><br> ![Tipo de Saída Gráfica do Runbook](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Esta situação é semelhante a funcionalidade de "escrita antecipada" da saída de um cmdlet no ISE do PowerShell sem ter de o executar.<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. Os cmdlets no módulo não devem considerar tipos de objetos complexos como parâmetros. O Fluxo de Trabalho do PowerShell é diferente do PowerShell no sentido em que armazena tipos complexos no formato de serialização anulada. Os tipos primitivos permanecerão como primitivos, mas os tipos complexos são convertidos para as respetivas versões de serialização anulada, que são, essencialmente, matrizes de propriedades. Por exemplo, se tiver utilizado o cmdlet **Get-Process** num runbook (ou um Fluxo de Trabalho do PowerShell), iria devolver um objeto do tipo [Deserialized.System.Diagnostic.Process] e não o tipo esperado [System.Diagnostic.Process]. Este tipo tem as mesmas propriedades que o tipo sem serialização anulada, mas nenhum dos métodos. Se tentar passar este valor como um parâmetro para um cmdlet, onde o cmdlet espera um valor [System.Diagnostic.Process] para este parâmetro, receberá o seguinte erro: *Não é possível processar a transformação do argumento no parâmetro “processo”. Erro: "Não é possível converter o valor "System.Diagnostics.Process (CcmExec)" do tipo "Deserialized.System.Diagnostics.Process" no tipo "System.Diagnostics.Process".*   Isto acontece porque existe um erro de correspondência entre o tipo esperado [System.Diagnostic.Process] e o tipo [Deserialized.System.Diagnostic.Process] especificado. Para contornar este problema, certifique-se de que os cmdlets do seu módulo não consideram tipos complexos como parâmetros. Eis a forma errada de o fazer.
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    E eis a forma correta, considerando um primitivo que pode ser utilizado internamente pelo cmdlet para captar o objeto complexo e utilizá-lo. Uma vez que os cmdlets são executados no contexto do PowerShell, e não no Fluxo de Trabalho do PowerShell, dentro do cmdlet $process torna-se o tipo [System.Diagnostic.Process] correto.  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Os recursos de ligação nos runbooks são tabelas hash, que são um tipo complexo, e no entanto, estas tabelas hash parecem conseguir passar para os cmdlets para o respetivo parâmetro –Connection perfeitamente, sem exceção de transmissão. Tecnicamente, alguns tipos de PowerShell conseguem transmitir corretamente a partir do respetivo formato serializado para o formato de serialização anulada e, por conseguinte, podem passar para os cmdlets para os parâmetros que aceitam o tipo sem serialização anulada. A tabela hash também está incluída. É possível que os tipos definidos de um autor de módulos sejam implementados de forma a poderem anular a serialização corretamente, mas existem algumas desvantagens que devem ser consideradas. O tipo tem de ter um construtor predefinido, ter todas as suas propriedades públicas e ter um PSTypeConverter. No entanto, para os tipos já definidos que o autor do módulo não possui, não existe qualquer forma de os "corrigir", daí a recomendação para evitar tipos complexos para todos os parâmetros. Sugestão para a Criação de Runbooks: se, por algum motivo, os seus cmdlets precisarem de considerar um tipo de parâmetro complexo ou estiver a utilizar o módulo de outra pessoa que requeira um tipo de parâmetro complexo, a solução nos runbooks do Fluxo de Trabalho do PowerShell e Fluxos de Trabalho do PowerShell no PowerShell local é moldar o cmdlet que gera o tipo complexo e o cmdlet que consome o tipo complexo na mesma atividade do InlineScript. Uma vez que o InlineScript executa o respetivo conteúdo como o PowerShell e não como o Fluxo de Trabalho do PowerShell, o cmdlet que gera o tipo complexo produziria esse tipo correto e não o tipo complexo no formato de serialização anulada.
5. Crie todos os cmdlets no módulo sem monitorização de estado. O Fluxo de Trabalho do PowerShell executa cada cmdlet chamado no fluxo de trabalho numa sessão diferente. Isto significa que qualquer cmdlet que dependa do estado da sessão criado/modificado por outros cmdlets no mesmo módulo não funcionará nos runbooks do Fluxo de Trabalho do PowerShell.  Eis um exemplo de o que não deve fazer.
   
    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```
   <br>
6. O módulo deve estar totalmente incluído num pacote compatível com Xcopy. Uma vez que os módulos da Automatização do Azure estão distribuídos nas sandboxes da Automatização quando os runbooks precisam de executar, têm de funcionar independentemente do anfitrião no qual estão a ser executados. Isto significa que deve ser capaz de zipar o pacote do módulo, movê-lo para qualquer outro anfitrião com uma versão do PowerShell igual ou mais recente e pô-lo a funcionar normalmente aquando da importação para o ambiente de PowerShell desse anfitrião. Para que isso aconteça, o módulo não deve depender de quaisquer ficheiros fora da pasta do módulo (a pasta que é zipada ao importar para a Automatização do Azure) ou de qualquer definição de registo única num anfitrião, como as definidas pela instalação de um produto. Se esta melhor prática não for seguida, o módulo não será utilizável na Automatização do Azure.  

## <a name="next-steps"></a>Passos seguintes

* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre a criação de Módulos do PowerShell, veja o artigo [Escrever um Módulo do Windows PowerShell](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

