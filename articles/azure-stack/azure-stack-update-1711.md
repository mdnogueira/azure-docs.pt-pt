---
title: "Atualização de pilha do Azure 1711 | Microsoft Docs"
description: "Saiba mais sobre as novidades na atualização 1711 para pilha do Azure integrado sistemas, problemas conhecidos e onde pode transferir a atualização."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: 4d98556f17fa834c497c2d1cd1854c9e6b02e021
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-stack-1711-update"></a>Atualização de pilha 1711 do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo descreve os melhoramentos e corrige deste pacote de atualização, problemas conhecidos para esta versão e onde pode transferir a atualização. Conhecido problemas são divididos em problemas conhecidos relacionados com o processo de atualização diretamente e os problemas conhecidos com a compilação (pós-instalação).

> [!IMPORTANT]
> Este pacote de atualização só é aplicável para sistemas de pilha do Azure integrado. Não aplicável este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de compilação

É o número de compilação de atualização do Azure pilha 1711 **20171122.1**.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Tem de instalar primeiro a pilha de Azure [1710 atualizar](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) antes de aplicar esta atualização.

### <a name="new-features-and-fixes"></a>Novas funcionalidades e correções

Esta atualização inclui as seguintes melhorias e correções para pilha do Azure.

#### <a name="new-features"></a>Novas funcionalidades

- Suporte para Syndicating modelos de solução
- Atualizações do registo de gráfico de pilha do Azure e o processamento de erros
- Capacidade para ligar os anfitriões e desativar
- Os utilizadores podem agora ativar VMs do Windows automaticamente
- Ponto final com privilégios adicionado cmdlet do PowerShell para obter chaves de recuperação do BitLocker para fins de retenção
- Suporte para a atualização de imagens offline ao atualizar a infraestrutura

#### <a name="fixes"></a>Correções

- Condição provável de antecipação fixo no DNS durante o registo de cluster também atualizada e unidade substituível em campo (FRU)
- Correção para a capacidade de reinício do anfitrião de Desativação na unidade substituível em campo (FRU)
- Vários outros desempenho, segurança e correções de estabilidade

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 novas funcionalidades e correções

- [14 de Novembro de 2017 — KB4048953 (compilação do SO 14393.1884)](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos relacionados com o processo de atualização

Esta secção contém as questões importantes que poderá encontrar durante a instalação da atualização de 1711.


1. **Sintoma:** operadores de pilha do Azure podem ver o seguinte erro durante o processo de atualização: *"nome: instalação de atualização.", "Descrição": "Instalar atualizações em anfitriões e VMs Infra.", "errorMessage": "escreva LiveUpdate da função ' VirtualMachines gerada uma exceção: \n\nThere espaço insuficiente no disk.\n\nat <ScriptBlock>, <No file>: line22 ","Estado":"Error","startTimeUtc":" 2017-11-10T16:46:59.123Z ","endTimeUtc":" 2017-11-10T19:20:29.669Z ","passos": []"*
    2. **Causa:** este problema é causado por falta de espaço livre em disco num ou mais máquinas virtuais que fazem parte da infraestrutura de pilha do Azure
    3. **Resolução:** contacte o serviço de cliente da Microsoft e de suporte (CSS) para obter assistência.
<br><br>
2. **Sintoma:** operadores de pilha do Azure podem ver o seguinte erro durante o processo de atualização:*exceção ao chamar "ExtractToFile" com os argumentos "3": "o processo não é possível aceder ao ficheiro ' <\\<machineName>-ERCS01\C$ \ Programa Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll >'*
    1. **Causa:** este problema é causado quando retomar uma atualização do portal do que era anteriormente retomado utilizando um ponto final com privilégios (PEP).
    2. **Resolução:** contacte o serviço de cliente da Microsoft e de suporte (CSS) para obter assistência.
<br><br>
3. **Sintoma:**operadores de pilha do Azure podem ver o seguinte erro durante o processo de atualização:*"tipo de 'CheckHealth' da função 'VirtualMachines' desencadeado uma verificação de estado de funcionamento do computador de exceção: \n\nVirtual para <machineName>-ACS01 produzidos a os seguintes erros. \nThere era um erro ao obter as informações da VM a partir de anfitriões. Detalhes de exceção: \nGet-VM: A operação no computador 'Node03' falhou: serviço o WS-Management não é possível processar o pedido. O WMI \nservice ou o fornecedor WMI devolveu um erro desconhecido: HRESULT 0x8004106c ".*
    1. **Causa:** este problema é causado por um problema de Windows Server que se destina a ser resolvidos em atualizações subsequentes do servidor de janela.
    2. **Resolução:** contacte o serviço de cliente da Microsoft e de suporte (CSS) para obter assistência.
<br><br>
4. **Sintoma:**operadores de pilha do Azure podem ver o seguinte erro durante o processo de atualização:*"tipo de 'DefenderUpdate' da função 'URP' levantou uma exceção: Falha ao obter versão de \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{nome de ficheiro} .exe após 60 tentativas em cópia-AzSDefenderFiles, c:\Programas\Microsoft Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: linha 262"*
    1. **Causa:** este problema é causado por uma transferência de fundo falhou ou está incompleto de atualizações de definições do Windows Defender.
    2. **Resolução:** volte tentar retomar a atualização depois de 8 horas passaram, uma vez que a primeira atualização tente.

### <a name="known-issues-post-installation"></a>Problemas conhecidos (pós-instalação)

Esta secção contém pós-instalação problemas conhecidos com compilação **20171122.1**.

#### <a name="portal"></a>Portal

- Poderá não ser possível ver os recursos de computação ou o armazenamento no portal do administrador. Isto indica que ocorreu um erro durante a instalação da atualização e que a atualização foi incorretamente comunicada conclusão com êxito. Se este problema ocorrer, contacte o Microsoft CSS para obter assistência.
- Poderá ver um dashboard em branco no portal. Para recuperar o dashboard, selecione o ícone de equipamento no canto superior direito do portal e, em seguida, selecione **restaurar predefinições**.
- Ao visualizar as propriedades de um grupo de recursos, o **mover** botão está desativado. Este comportamento é esperado. Mover grupos de recursos entre subscrições não é atualmente suportado.
- Para qualquer fluxo de trabalho onde selecionou uma subscrição, o grupo de recursos ou a localização numa lista pendente, pode deparar-se um ou mais dos seguintes problemas:

   - Poderá ver uma linha em branco no topo da lista. Deve ainda poderá selecionar um item conforme esperado.
   - Se a lista de itens na lista pendente é pequeno, poderá não conseguir ver algum dos nomes de item.
   - Se tiver várias subscrições do utilizador, a lista de lista pendente do grupo de recursos pode estar vazia. 

        > [!NOTE]
        > Para contornar os dois últimos problemas, pode escrever o nome da subscrição ou grupo de recursos (se sabe que este) ou pode utilizar em vez disso, o PowerShell.

- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos completo e, em seguida, eliminar subscrições de utilizador.
- Não é possível ver permissões à sua subscrição utilizando os portais de pilha do Azure. Como solução, pode verificar as permissões com o PowerShell.

#### <a name="health-and-monitoring"></a>Estado de funcionamento e a monitorização

- Se reiniciar o computador de uma instância de função de infraestrutura, poderá receber uma mensagem a indicar que o reinício falhou. No entanto, o reinício, na verdade, com êxito.

#### <a name="marketplace"></a>Marketplace
- Quando tentar adicionar itens para o mercado de pilha do Azure utilizando o **adicionar a partir do Azure** opção, nem todos os itens podem ser visíveis para transferência.
- Os utilizadores podem procurar o mercado completo sem uma subscrição e podem ver itens administrativos como planos e ofertas. Estes itens estão não funcional para os utilizadores.

#### <a name="compute"></a>Computação
- Os utilizadores recebem a opção para criar uma máquina virtual com armazenamento georredundante. Esta configuração provoca a falha da criação máquina virtual.
- Pode configurar uma conjunto apenas com um domínio de falhas de um e um domínio de atualização de um de disponibilidade de máquina virtual.
- Não há nenhum experiência marketplace para criar conjuntos de dimensionamento de máquina virtual. Pode criar um conjunto, utilizando um modelo de dimensionamento.
- Definições de dimensionamento para conjuntos de dimensionamento de máquina virtual não estão disponíveis no portal. Como solução, pode utilizar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, tem de utilizar o `-Name` parâmetro em vez de `-VMScaleSetName`.
 
#### <a name="networking"></a>Redes
- Não é possível criar um balanceador de carga com um endereço IP público utilizando o portal. Como solução, pode utilizar o PowerShell para criar o Balanceador de carga.
- Tem de criar uma regra de tradução (NAT) de endereço de rede quando cria um balanceador de carga de rede. Caso contrário, receberá um erro ao tentar adicionar uma regra NAT após a criação do Balanceador de carga.
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM) depois da VM foi criada e associada a esse endereço IP. Desassociação irá aparecer funcionar, mas o endereço IP público anteriormente atribuído permanece associado a VM original. Este comportamento ocorre mesmo reatribuir o endereço IP para uma nova VM (normalmente denominado como um *alternância de VIP*). Todas as futuras tenta estabelecer ligação através deste resultado de endereço IP numa ligação para a VM originalmente associada e não para a nova. Atualmente, tem de utilizar os novos endereços IP públicos apenas para a criação de nova VM.
- Os operadores do Azure da pilha poderão não ser possível implementar, eliminar, modificar VNETs ou grupos de segurança de rede. Este problema é principalmente utilizado em tentativas de atualização subsequentes do mesmo pacote. Isto é causado por um problema de empacotamento com uma atualização que está atualmente a ser investigação.
 
#### <a name="sqlmysql"></a>SQL Server/MySQL
- Pode demorar até uma hora até os inquilinos podem criar bases de dados num novo SQL Server ou MySQL SKU. 
- Criação de itens diretamente no SQL Server e o MySQL servidores que não são executadas pelo fornecedor de recursos de alojamento não é suportada e pode resultar num Estado não correspondentes.
 
#### <a name="app-service"></a>Serviço de Aplicações
- Um utilizador tem de registar o fornecedor de recursos de armazenamento antes de poderem criarem a sua primeira função do Azure na subscrição.

#### <a name="identity"></a>Identidade

No Azure Active Directory serviços de Federação (ADFS) implementar ambientes, o **azurestack\azurestackadmin** conta já não é o proprietário da subscrição de fornecedor predefinido. Em vez de iniciar sessão no **portal de administração / adminmanagement endpoint** com o **azurestack\azurestackadmin**, pode utilizar o **azurestack\cloudadmin** conta, por isso que pode gerir e utilizar a subscrição do fornecedor predefinido.

> [!IMPORTANT]
> Apesar do **azurestack\cloudadmin** conta é o proprietário da subscrição de fornecedor predefinido em ambientes de ADFS implementada, não tem permissões para RDP num anfitrião. Continuar a utilizar o **azurestack\azurestackadmin** conta ou a conta de administrador local para iniciar sessão, aceder e gerir o anfitrião, conforme necessário.

## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização de 1711 de pilha do Azure do [aqui](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Mais informações

Microsoft forneceu uma forma para monitorizar e retomar a atualizações com o Privileged ponto final (PEP) instalados com 1711 de atualização.

- Consulte o [monitorizar atualizações na pilha do Azure, consultando a documentação do ponto final com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Consultar também

- Consulte [gerir atualizações na descrição geral do Azure pilha](azure-stack-updates.md) para uma descrição geral da gestão de atualização na pilha do Azure.
- Consulte [aplicar atualizações na pilha de Azure](azure-stack-apply-updates.md) para obter mais informações sobre como aplicar atualizações com a pilha do Azure.
