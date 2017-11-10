---
title: "Atualização de pilha do Azure 1710 (compilação 20171020.1) | Microsoft Docs"
description: "Saiba mais sobre as novidades na atualização 1710 para pilha do Azure integrado sistemas, problemas conhecidos e onde pode transferir a atualização."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: twooley
ms.openlocfilehash: 32ca25998046caa6cf29410644f3cd86868b679d
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Atualização de pilha do Azure 1710 (compilação 20171020.1)

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo descreve os melhoramentos e corrige deste pacote de atualização, problemas conhecidos para esta versão e onde pode transferir a atualização. Conhecido problemas são divididos em problemas conhecidos relacionados com o processo de atualização diretamente e os problemas conhecidos com a compilação (pós-instalação).

> [!IMPORTANT]
> Este pacote de atualização é apenas para sistemas de pilha do Azure integrado. Não é aplicável este pacote de atualização a Kit de desenvolvimento de pilha do Azure.

## <a name="improvements-and-fixes"></a>Melhoramentos e correções

Esta atualização inclui as seguintes melhorias de qualidade e correções para pilha do Azure.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Melhoramentos do Windows Server 2016 e correções

- Atualizações para o Windows Server 2016: 10 de Outubro de 2017 — KB4041691 (compilação do SO 14393.1770. Consulte [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691) para obter mais informações.

### <a name="additional-quality-improvements-and-fixes"></a>Melhoramentos adicionais qualidade e correções

- Adicionar os cmdlets do PowerShell de ponto final com privilégios para ajudar a resolver problemas e atualizar o servidor de protocolo NTP (Network Time).
- Suporte adicionado para atualizar os módulos de ponto final do ponto final com privilégios apenas suficiente administração (JEA) e a lista branca de cmdlet. 
- Erros de idioma local fixo no ponto final com privilégios.
- Foi adicionada a capacidade para rodar as credenciais do gateway.
- Remover a conta de administrador local CBLocalAdmin. 
- Corrigido conteúdo do modelo de alerta de heartbeat para Certifique-se de que heartbeat alertas trabalho corretamente após uma atualização.
- Corrigir o fornecedor de recursos de infraestrutura para lidar com tempos limite durante as operações de FRU. 
- Foi adicionada a capacidade para programadores de nuvem utilizar perfis de API do Azure Resource Manager na pilha do Azure.
- Desativar o serviço Windows Update a máquina virtual de implementação (DVM). 
- Remover o nó desligar em/ações de interface de utilizador.
- Vários outros desempenho e a estabilidade correções. 
 
## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos relacionados com o processo de atualização

Esta secção contém as questões importantes que poderá encontrar durante a instalação da atualização 1710.

> [!IMPORTANT]
> Se a atualização falhar, quando mais tarde tentar retomar a atualização, tem de utilizar o `Resume-AzureStackUpdate` cmdlet do ponto final com privilégios. Não retome a atualização através do portal de administrador. (Este é um problema conhecido nesta versão.) Para obter mais informações, consulte [monitorizar atualizações na pilha do Azure utilizando o ponto final com privilégios](azure-stack-monitor-update.md).

| Sintoma  | Causa  | Resolução |
|---------|---------|---------|
|Quando efetuar uma atualização, um erro semelhante ao seguinte<br>poderão ocorrer durante o passo "Armazenamento anfitriões reiniciar o nó de armazenamento"<br> do plano de ação de atualização.<br><br>**{"name": "Reiniciar armazenamento anfitriões", "Descrição": "Reiniciar<br> anfitriões de armazenamento. errorMessage","": "escreva 'Reiniciar' da função<br> 'BareMetal' gerado um computador de exceção: \n\n<br> HostName-05 é ignorada. Falha ao obter o respetivo LastBootUpTime<br> através do serviço WMI com a seguinte mensagem de erro:<br> RPC o servidor não está disponível.<br> (Excepção de HRESULT: 0x800706BA). \nat reinício-anfitrião** | Este problema é causado por um controlador defeituoso potencial presente em algumas configurações. | 1. Iniciar sessão para a interface de web de controlador (BMC) de gestão de placas base e reinicie o anfitrião que é identificado na mensagem de erro.<br><br>2. Retome a atualização utilizando o ponto final com privilégios. |
| Quando efetuar uma atualização, o processo de atualização é apresentado ao compartimento de<br> e não efetuar progresso após o passo "passo: executar o passo 2.4 - instalação<br> Atualizar"do plano de ação de atualização.<br><br>Este passo é depois seguido por uma série de processos de cópia de .nupkg<br> ficheiros para as partilhas de ficheiros de infra-estrutura interna. Por exemplo:<br><br>**Ao copiar ficheiros 1 do content\PerfCollector\VirtualMachines para <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**  | O problema é causado por ficheiros de registo a ser preenchida os discos de uma máquina virtual de infraestrutura e um problema no Windows Server escalável ficheiros servidor (SOFS) que irá ser entregue numa atualização subsequente. | Contacte o suporte (CSS) e de serviço de cliente da Microsoft para obter assistência. | 
| Quando efetuar uma atualização, um erro semelhante ao seguinte<br> poderão ocorrer durante o passo "passo: executar o passo 2.13.2 - atualização<br> *VM_Name*"do plano de ação de atualização. (A máquina virtual<br> nome pode variar devido às.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: invocação de 'LiveUpdate' da interface de<br> função 'Cloud\Fabric\WAS' falhou: tipo de 'LiveUpdate' da função<br> '' Ocorreu uma exceção: Ocorreu um erro durante armazenamento<br> inicialização: Ocorreu um erro ao tentar efetuar uma API<br> a chamada para o serviço do Storage do Microsoft: {"Mensagem": "limite de tempo<br> ocorreu durante a comunicação com o Service Fabric.<br> Tipo de exceção: TimeoutException.<br> Mensagem de exceção: operação excedeu o tempo limite. "}**  | O problema é causado por um limite de tempo de e/s no Windows Server que será resolvido numa atualização subsequente. | Contacte o Microsoft CSS para obter assistência.
| Quando efetuar uma atualização, um erro semelhante ao seguinte<br> poderão ocorrer durante o passo "passo 21 VMs de reinício do SQL server".<br><br>**Tipo de 'LiveUpdateRestart' da função 'VirtualMachines' gerado um<br> exceção: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> consultar VM MachineName-Sql01. - 13/10/2017 5:23:50: 00 VerboseMessage: [VirtualMachines: LiveUpdateRestart]<br> VM está marcada como HighlyAvailable. - 13/10/2017 5:23:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] em<br>MS. Internal.ServerClusters.ExceptionHelp.Build em<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(force booleano) em Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() em <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() em Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 13/10/2017 5:23:50: 00 aviso<br>mensagem: tarefa: invocação de 'LiveUpdateRestart' da interface de<br> função 'Cloud\Fabric\VirtualMachines' falhou:** | Este problema pode ocorrer se não foi possível reiniciar a máquina virtual. | Contacte o Microsoft CSS para obter assistência.
| Quando efetuar uma atualização, poderá ocorrer um erro semelhante ao seguinte:<br><br>**2017-10-22T01:37:37.5369944Z 'Encerramento' de tipo de função 'SQL'<br> levantou uma exceção: erro ao colocar em pausa nó<br> 's45r1004 Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType &#95; App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 em<br> encerramento, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br> Aplicações \EnterpriseCloudEngineApplicationType &#95; App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br>Deployment\Classes\SQL\SQL.psm1: 50 no &#60; de linha O ScriptBlock &#62;<br> &#60; Nenhum ficheiro >: linha 18 em &#60; O ScriptBlock &#62; &#60; Nenhum ficheiro &#62;: 16 de linha** | Este problema pode ocorrer se a máquina virtual não pode ser colocada num estado suspenso para drenar funções. | Contacte o Microsoft CSS para obter assistência.
| Quando efetuar uma atualização, podem ocorrer qualquer um dos seguintes erros:<br><br>**O tipo 'Validar' da função 'ADFS' levantou uma exceção: validação<br> para a função ADFS/gráfico falhou com o erro: erro ao verificar o ADFS<br> sonda endpoint *endpoint_URI*: chamar exceção<br> " GetResponse"com"0"argumentos:" o servidor remoto<br> devolveu um erro: (503) do servidor não disponível. "em Invoke -<br>ADFSGraphValidation**<br><br>**O tipo 'Validar' da função 'ADFS' levantou uma exceção: validação<br> para a função ADFS/gráfico falhou com o erro: erro ao obter<br> propriedades ADFS: não foi possível ligar ao <br>NET.TCP: 1500/política. A tentativa de ligação teve<br> para um intervalo de tempo de 00:00:02.0498923. Código de erro TCP<br> 10061: foi possível estabelecer uma ligação porque o destino<br> máquina ativamente recusou-127.0.0.1:1500.<br> em Invoke-ADFSGraphValidation** | O plano de ação de atualização não é possível validar o estado de funcionamento de serviços de Federação do Active Directory (AD FS). | Contacte o Microsoft CSS para obter assistência.

## <a name="known-issues-post-installation"></a>Problemas conhecidos (pós-instalação)

Esta secção contém os problemas conhecidos com criar 20171020.1 de pós-instalação.

### <a name="portal"></a>Portal

- Poderá não ser possível ver os recursos de computação ou o armazenamento no portal do administrador. Isto indica que ocorreu um erro durante a instalação da atualização e que a atualização foi incorretamente comunicada conclusão com êxito. Se este problema ocorrer, contacte o Microsoft CSS para obter assistência.
- Poderá ver um dashboard em branco no portal. Para recuperar o dashboard, selecione o ícone de equipamento no canto superior direito do portal e, em seguida, selecione **restaurar predefinições**.
- Ao visualizar as propriedades de um grupo de recursos, o **mover** botão está desativado. Este comportamento é esperado. Mover grupos de recursos entre subscrições não é atualmente suportado.
- Para qualquer fluxo de trabalho onde selecionou uma subscrição, o grupo de recursos ou a localização numa lista pendente, pode deparar-se um ou mais dos seguintes problemas:

   - Poderá ver uma linha em branco no topo da lista. Deve ainda poderá selecionar um item conforme esperado.
   - Se a lista de itens na lista pendente é pequeno, poderá não conseguir ver algum dos nomes de item.
   - Se tiver várias subscrições do utilizador, a lista de lista pendente do grupo de recursos pode estar vazia. 

   Para contornar os dois últimos problemas, pode escrever o nome da subscrição ou grupo de recursos (se sabe que este) ou pode utilizar em vez disso, o PowerShell.
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos completo e, em seguida, eliminar subscrições de utilizador.
- Não é possível ver permissões à sua subscrição utilizando os portais de pilha do Azure. Como solução, pode verificar as permissões com o PowerShell.
  
### <a name="backup"></a>Cópia de segurança

- Não ative a cópia de segurança de infraestrutura no **cópia de segurança da infraestrutura** painel.

### <a name="health-and-monitoring"></a>Estado de funcionamento e a monitorização

- Se reiniciar o computador de uma instância de função de infraestrutura, poderá receber uma mensagem a indicar que o reinício falhou. No entanto, o reinício, na verdade, com êxito.

### <a name="marketplace"></a>Marketplace
- Quando tentar adicionar itens para o mercado de pilha do Azure utilizando o **adicionar a partir do Azure** opção, nem todos os itens podem ser visíveis para transferência.
- Os utilizadores podem procurar o mercado completo sem uma subscrição e podem ver itens administrativos como planos e ofertas. Estes itens estão não funcional para os utilizadores.

### <a name="compute"></a>Computação
- Os utilizadores recebem a opção para criar uma máquina virtual com armazenamento georredundante. Esta configuração provoca a falha da criação máquina virtual.
- Pode configurar uma conjunto apenas com um domínio de falhas de um e um domínio de atualização de um de disponibilidade de máquina virtual.
- Não há nenhum experiência marketplace para criar conjuntos de dimensionamento de máquina virtual. Pode criar um conjunto, utilizando um modelo de dimensionamento.
- Definições de dimensionamento para conjuntos de dimensionamento de máquina virtual não estão disponíveis no portal. Como solução, pode utilizar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, tem de utilizar o `-Name` parâmetro em vez de `-VMScaleSetName`.
 
### <a name="networking"></a>Redes
- Não é possível criar um balanceador de carga com um endereço IP público utilizando o portal. Como solução, pode utilizar o PowerShell para criar o Balanceador de carga.
- Tem de criar uma regra de tradução (NAT) de endereço de rede quando cria um balanceador de carga de rede. Caso contrário, receberá um erro ao tentar adicionar uma regra NAT após a criação do Balanceador de carga.
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM) depois da VM foi criada e associada a esse endereço IP. Desassociação irá aparecer funcionar, mas o endereço IP público anteriormente atribuído permanece associado a VM original. Este comportamento ocorre mesmo reatribuir o endereço IP para uma nova VM (normalmente denominado como um *alternância de VIP*). Todas as futuras tenta estabelecer ligação através deste resultado de endereço IP numa ligação para a VM originalmente associada e não para a nova. Atualmente, tem de utilizar os novos endereços IP públicos apenas para a criação de nova VM.
 
### <a name="sqlmysql"></a>SQL Server/MySQL
- Pode demorar até uma hora até os inquilinos podem criar bases de dados num novo SQL Server ou MySQL SKU. 
- Criação de itens diretamente no SQL Server e o MySQL servidores que não são executadas pelo fornecedor de recursos de alojamento não é suportada e pode resultar num Estado não correspondentes.
 
### <a name="app-service"></a>Serviço de Aplicações
- Um utilizador tem de registar o fornecedor de recursos de armazenamento antes de poderem criarem a sua primeira função do Azure na subscrição.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procedimentos de (FRU) de unidade substituível em campo

- Durante a execução da atualização, imagens offline não estão corrigidas. Se precisar de substituir um nó de unidade de escala, trabalhar com o fornecedor de hardware do OEM para se certificar de que o nó substituído tem o nível mais recente do patch.

## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização de 1710 [aqui](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral da gestão de atualizações na pilha do Azure, consulte [gerir atualizações na descrição geral do Azure pilha](azure-stack-updates.md).
- Para obter informações sobre como aplicar atualizações, consulte [aplicar atualizações na pilha de Azure](azure-stack-apply-updates.md).