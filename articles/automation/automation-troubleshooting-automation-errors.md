---
title: "Resolução de problemas comuns de automatização do Azure | Microsoft Docs"
description: "Este artigo fornece informações para ajudar a resolver problemas e corrija erros comuns de automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: "Erro de automatização, resolução de problemas, problema"
ms.assetid: 5f3cfe61-70b0-4e9c-b892-d02daaeee07d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: sngun; v-reagie
ms.openlocfilehash: f9ad68abef47cde7472e413ee82510f7df9121cd
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Resolução de problemas comuns na automatização do Azure 
Este artigo fornece erros comuns que pode ocorrer na automatização do Azure e sugere possíveis soluções para resolvê-los de resolução de problemas.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erros de autenticação quando trabalhar com runbooks de automatização do Azure
### <a name="scenario-sign-in-to-azure-account-failed"></a>Cenário: Início de sessão na conta do Azure falhou
**Erro:** recebe o erro "Unknown_user_type: utilizador tipo desconhecido" quando trabalhar com os cmdlets Add-AzureAccount ou Login-AzureRmAccount.

**Razão para o erro:** este erro ocorre se o nome de recurso de credencial não é válido ou se o nome de utilizador e palavra-passe que utilizou para configurar o recurso de credencial de automatização não são válidos.

**Sugestões de resolução de problemas:** para determinar o que é o problema, siga os passos seguintes:  

1. Certifique-se de que não tem quaisquer carateres especiais, incluindo o  **@**  caráter no nome do recurso de credencial de automatização que está a utilizar para ligar ao Azure.  
2. Certifique-se de que pode utilizar o nome de utilizador e palavra-passe que são armazenadas na credencial de automatização do Azure no seu editor local do ISE do PowerShell. Pode fazê-lo executando os seguintes cmdlets no ISE do PowerShell:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. Se a autenticação falhar localmente, isto significa que ainda não configurou as credenciais do Azure Active Directory corretamente. Consulte [autenticação no Azure utilizando o Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogue para obter a conta do Azure Active Directory corretamente configurado.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>Cenário: Não foi possível encontrar a subscrição do Azure
**Erro:** recebe o erro "a subscrição com o nome ``<subscription name>`` não encontrada" quando trabalhar com os cmdlets Select-AzureSubscription ou Select-AzureRmSubscription.

**Razão para o erro:** este erro ocorre se o nome da subscrição não é válido ou se o utilizador do Azure Active Directory que está a tentar obter os detalhes da subscrição não está configurado como um administrador da subscrição.

**Sugestões de resolução de problemas:** para determinar se corretamente ter autenticado para o Azure e tem acesso à subscrição que está a tentar a selecionar, siga os passos seguintes:  

1. Certifique-se de que executa o **Add-AzureAccount** antes de executar o **Select-AzureSubscription** cmdlet.  
2. Se continuar a ver esta mensagem de erro, modificar o seu código ao adicionar o **Get-AzureSubscription** seguinte cmdlet a **Add-AzureAccount** cmdlet e, em seguida, execute o código.  Agora, verifique se a saída de Get-AzureSubscription contém os detalhes da sua subscrição.  

   * Se não vir quaisquer detalhes da subscrição na saída, isto significa que a subscrição não está inicializada.  
   * Se vir os detalhes da subscrição na saída, confirme que está a utilizar o nome de subscrição correta ou o ID com a **Select-AzureSubscription** cmdlet.   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Cenário: Autenticação no Azure falhou porque a autenticação multifator é ativada
**Erro:** recebe o erro "Add-AzureAccount: AADSTS50079: inscrição de autenticação forte (prova cópia de segurança) é necessária" quando a autenticação no Azure com o nome de utilizador do Azure e a palavra-passe.

**Razão para o erro:** se tiver a autenticação multifator na sua conta do Azure, não é possível utilizar um utilizador do Azure Active Directory para autenticar para o Azure.  Em vez disso, terá de utilizar um certificado ou um principal de serviço para autenticar para o Azure.

**Sugestões de resolução de problemas:** para utilizar um certificado com os cmdlets de gestão de serviço do Azure, consulte [criação e adição de um certificado para gerir os serviços do Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para utilizar um principal de serviço com os cmdlets do Azure Resource Manager, consulte [criar serviço principal através do portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md) e [autenticar um principal de serviço com o Azure Resource Manager.](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Erros comuns ao trabalhar com runbooks
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>Cenário: O início da tarefa de runbook foi tentado três vezes, mas não foi possível a cada hora de início
**Erro:** o runbook falha com o erro "" a tarefa foi tentou três vezes mas não foi possível."

**Razão para o erro:** este erro pode dever-se pelos seguintes motivos:  

1. Limite de memória.  Podemos ter documentados limites na quantidade de memória atribuída a uma Sandbox [limites de serviço de automatização](../azure-subscription-service-limits.md#automation-limits) para uma tarefa o poderá falhar se estiver a utilizar mais de 400 MB de memória. 

2. Módulo incompatível.  Isto pode ocorrer se as dependências de módulo não estão corretas e se não forem, o runbook, normalmente, irá devolver um "comando não foi encontrado" ou "Não é possível vincular o parâmetro" mensagem. 

**Sugestões de resolução de problemas:** qualquer uma das seguintes soluções irá corrigir o problema:  

* Métodos sugeridos para trabalhar dentro do limite de memória são dividir a carga de trabalho entre vários runbooks, não processar como a quantidade de dados na memória, não ao escrever a saída desnecessária a partir dos seus runbooks ou considere quantos pontos de verificação escrever para o seu fluxo de trabalho do PowerShell runbooks.  

* Tem de atualizar os módulos do Azure, seguindo os passos [como atualizar os módulos do Azure PowerShell na automatização do Azure](automation-update-azure-modules.md).  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Cenário: O Runbook falhar devido ao objeto de serialização anulado
**Erro:** o runbook falha com o erro "não é possível vincular o parâmetro ``<ParameterName>``. Não é possível converter o ``<ParameterType>`` valor do tipo Deserialized ``<ParameterType>`` para o tipo ``<ParameterType>``".

**Razão para o erro:** se o runbook for um fluxo de trabalho do PowerShell, armazena objetos complexos num formato de serialização anulado para manter o estado de runbook se o fluxo de trabalho é suspenso.  

**Sugestões de resolução de problemas:**  
Qualquer uma das seguintes três soluções irá corrigir este problema:

1. Se estiver a encaminhando os objetos complexos de um cmdlet para outro, molde estes cmdlets num InlineScript.  
2. Transmita o nome ou valor que precisa do objeto complexo em vez de transmitir todo o objeto.  
3. Utilize um runbook do PowerShell em vez de um runbook de fluxo de trabalho do PowerShell.  

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Cenário: A tarefa de Runbook falhou porque a quota alocada excedido
**Erro:** a tarefa de runbook falha com o erro "a quota para a tarefa total mensal de tempo de execução foi atingida para esta subscrição".

**Razão para o erro:** este erro ocorre quando a execução da tarefa excede a quota livre 500 minuto para a sua conta. Esta quota é aplicada a todos os tipos de tarefas de execução da tarefa como uma tarefa de teste, inicie uma tarefa a partir do portal, executar uma tarefa utilizando webhooks e agendar uma tarefa para executar utilizando o portal do Azure ou no seu centro de dados. Para obter mais informações sobre preços para ver de automatização [automatização preços](https://azure.microsoft.com/pricing/details/automation/).

**Sugestões de resolução de problemas:** se pretender utilizar mais do que 500 minutos de processamento por mês, terá de alterar a sua subscrição do gratuito camada para o escalão básico. Pode atualizar para o escalão básico, efetuando os seguintes passos:  

1. Inicie sessão na sua subscrição do Azure  
2. Selecione a conta de Automatização que quer atualizar  
3. Clique em **definições** > **preços**.
4. Clique em **ativar** na parte inferior da página para atualizar a sua conta para o **básico** camada.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Cenário: Cmdlet não reconhecido ao executar um runbook
**Erro:** falha a tarefa de runbook com o erro "``<cmdlet name>``: O termo ``<cmdlet name>`` não é reconhecida como o nome de um cmdlet, a função, o ficheiro de script ou programa operável."

**Razão para o erro:** este erro é causado quando o motor do PowerShell não é possível encontrar o cmdlet que está a utilizar no runbook.  Isto pode dever-se ao facto do módulo que contém o cmdlet está em falta da conta do, existe um conflito de nomes com um nome de runbook, ou o cmdlet também existe no outro módulo e automatização não é possível resolver o nome.

**Sugestões de resolução de problemas:** qualquer uma das seguintes soluções irá corrigir o problema:  

* Verifique se introduziu o nome do cmdlet corretamente.  
* Certificar-se de que o cmdlet existe na sua conta de automatização e de que existem não está em conflito. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e procure o cmdlet que pretende localizar na biblioteca ou executar **Get-Command ``<CommandName>``** .  Depois de validar que o cmdlet está disponível para a conta e de que não existem nenhum nome entra em conflito com outros cmdlets ou runbooks, adicioná-lo para a tela e certifique-se de que está a utilizar um parâmetro válido definido no runbook.  
* Se tiver um conflito de nomes e o cmdlet está disponível em dois módulos diferentes, pode resolver isto, utilizando o nome completamente qualificado para o cmdlet. Por exemplo, pode utilizar **ModuleName\CmdletName**.  
* Se estiver a executar o runbook no local num grupo de trabalho híbrida, em seguida, certifique-se de que o módulo/cmdlet está instalado no computador que aloja o worker híbrido.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Cenário: Um runbook de execução longa consistentemente falha com a exceção: "a tarefa não é possível continuar a ser executada porque este foi expulso repetidamente do ponto de verificação mesmo".
**Razão para o erro:** este comportamento é propositado devido a monitorização de "Quota justa" dos processos dentro da automatização do Azure, que suspenda automaticamente um runbook se a ser executada mais do que três horas. No entanto, a mensagem de erro devolvida não fornece "que seguinte" opções. Um runbook pode ser suspensa para uma série de razões. Suspende acontecer principalmente devido a erros. Por exemplo, uma exceção não identificada no runbook, uma falha de rede ou uma falha na função de trabalho de Runbook executar o runbook, todos os fará com que o runbook seja suspenso e iniciar a partir do último ponto de verificação quando retomado.

**Sugestões de resolução de problemas:** a solução para evitar este problema documentada consiste em utilizar pontos de verificação num fluxo de trabalho.  Para obter mais informações, consulte [Learning fluxos de trabalho do PowerShell](automation-powershell-workflow.md#checkpoints).  Pode encontrar uma explicação mais detalhada de "Quota justa" e o ponto de verificação neste artigo do blogue [utilizando pontos de verificação em Runbooks](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Erros comuns ao importar os módulos
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Cenário: Ocorre uma falha ao importar o módulo ou os cmdlets não pode ser executados após a importação
**Erro:** um módulo falha importar ou importa com êxito, mas não os cmdlets são extraídos.

**Razão para o erro:** algumas razões comuns que um módulo não pode importar com êxito para a automatização do Azure são:  

* A estrutura não corresponde a estrutura de automatização necessita que seja na.  
* O módulo está depende de outro módulo que não tenha sido implementado a sua conta de automatização.  
* O módulo está a faltar as respetivas dependências na pasta.  
* O **New-AzureRmAutomationModule** cmdlet está a ser utilizado para carregar o módulo, e não atribuiu o caminho de armazenamento completo ou não carregou o módulo utilizando um URL acessível publicamente.  

**Sugestões de resolução de problemas:**  
Qualquer uma das seguintes soluções corrigir o problema:  

* Certifique-se de que o módulo de acordo com o seguinte formato:  
  ModuleName.Zip  **->**  ModuleName ou um número de versão  **->**  (ModuleName.psm1, ModuleName.psd1)
* Abra o ficheiro. psd1 e se o módulo tem quaisquer dependências.  Se existir, carregar estes módulos para a conta de automatização.  
* Certifique-se de que qualquer .dlls referenciado estão presentes na pasta do módulo.  

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erros comuns ao trabalhar com configuração de estado pretendido (DSC)
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Cenário: Nó está num Estado de falha com um erro "Não encontrada"
**Erro:** o nó tem um relatório com **falha** que contém o erro e o estado "ao tentar obter a ação do servidor https://``<url>``//accounts/ ``<account-id>`` /Nodes(AgentId=``<agent-id>``) / GetDscAction falhou porque uma configuração válida ``<guid>`` não é possível localizar. "

**Razão para o erro:** este erro normalmente ocorre quando o nó está atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração do nó (por exemplo, ABC. Servidor Web).  

**Sugestões de resolução de problemas:**  

* Certifique-se de que está a atribuir o nó com o "nome do nó de configuração" e não o "nome de configuração".  
* Pode atribuir uma configuração de nó a um nó através do portal do Azure ou com um cmdlet do PowerShell.

  * Para atribuir uma configuração de nó a um nó através do portal do Azure, abra o **nós de DSC** página, em seguida, selecione um nó e clique em **atribuir a configuração do nó** botão.  
  * Para atribuir uma configuração de nó a um nó através do cmdlet do PowerShell, utilize **conjunto AzureRmAutomationDscNode** cmdlet

### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Cenário: Não existem configurações de nó (ficheiros MOF) foram produzidas quando uma configuração é compilada
**Erro:** suspende a tarefa de compilação de DSC com o erro: "compilação foi concluída com êxito, mas não .mofs de configuração de nó foram geradas".

**Razão para o erro:** quando seguintes expressão o **nó** palavra-chave na configuração de DSC avalia como $null e não configurações de nó são produzidas.    

**Sugestões de resolução de problemas:**  
Qualquer uma das seguintes soluções corrigir o problema:  

* Certifique-se de que a expressão junto ao **nó** palavra-chave na definição de configuração não está a avaliar para $null.  
* Se está a transmitir ConfigurationData ao compilar a configuração, certifique-se de que está a transmitir os valores esperados, que requer a configuração do [ConfigurationData](automation-dsc-compile.md#configurationdata).

### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Cenário: O relatório de nó DSC fica presa "em curso" Estado
**Erro:** DSC agente produz "Não existe uma instância encontrada com fornecida valores de propriedade."

**Razão para o erro:** atualizou a sua versão do WMF e ter danificado WMI.  

**Sugestões de resolução de problemas:** siga as instruções no [DSC problemas e limitações conhecidos](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) para corrigir o problema.

### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Cenário: Não é possível utilizar uma credencial numa configuração de DSC
**Erro:** foi suspenso a tarefa de compilação de DSC com o erro: "Propriedades"credencial' do tipo de processamento de erro de System.InvalidOperationException ``<some resource name>``: converter e armazenar uma palavra-passe encriptada como texto simples só é permitida se PSDscAllowPlainTextPassword está definido como verdadeiro ".

**Razão para o erro:** utilizou uma credencial numa configuração mas não fornecer adequada **ConfigurationData** definir **PSDscAllowPlainTextPassword** como true para cada nó configuração.  

**Sugestões de resolução de problemas:**  

* Certifique-se passar o adequado **ConfigurationData** definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [ativos no Automation DSC do Azure](automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Passos seguintes
Se seguiu os passos de resolução de problemas acima e não é possível encontrar a resposta, pode rever as opções de suporte adicionais abaixo.

* Obter a ajuda de especialistas do Azure. Submeter o seu problema para o [fóruns do MSDN Azure ou Stack Overflow.](https://azure.microsoft.com/support/forums/).
* Ficheiro de um incidente de suporte do Azure. Vá para o [suportam o Azure site](https://azure.microsoft.com/support/options/) e clique em **obter suporte** em **técnica e faturação suporte**.
* Publique um pedido de Script no [Centro de scripts](https://azure.microsoft.com/documentation/scripts/) se estiver à procura de uma solução de runbook da automatização do Azure ou um módulo de integração.
* Publique comentários ou pedidos funcionalidades para a automatização do Azure no [voz do utilizador](https://feedback.azure.com/forums/34192--general-feedback).
