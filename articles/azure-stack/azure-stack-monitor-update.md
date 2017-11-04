---
title: "Monitorizar as atualizações na pilha do Azure utilizando o ponto final com privilégios | Microsoft Docs"
description: "Saiba como utilizar o ponto final com privilégios para monitorizar o estado de atualização para sistemas de pilha do Azure integrado."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: twooley
ms.openlocfilehash: af7387268d60dc639a39da23e040097fd0695a22
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorizar as atualizações na pilha do Azure utilizando o ponto final com privilégios

*Aplica-se a: Azure pilha integrado sistemas*

Pode utilizar o ponto final com privilégios para monitorizar o progresso de uma execução de atualização de pilha do Azure e retomar uma atualização falhada executar no último passo com êxito. 

Os seguintes novos cmdlets de PowerShell para gestão de atualizações são incluídos na atualização 1710 para sistemas de pilha do Azure integrado.

| Cmdlet  | Descrição  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Devolve o estado da atualização atualmente em execução, concluída ou falhada. Fornece o estado de alto nível da operação de atualização e um documento XML que descreve o passo atual e o estado correspondente. |
| `Get-AzureStackUpdateVerboseLog` | Devolve os registos verbosos que são gerados pela atualização. |
| `Resume-AzureStackUpdate` | Retoma uma atualização falhada o momento em que falhou. Em certos cenários, poderá ter de concluir os passos de mitigação antes de retomar a atualização.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Certifique-se de que os cmdlets estão disponíveis
Porque os cmdlets são novos no pacote de atualização de 1710 para pilha do Azure, o processo de atualização de 1710 precisa de obter até um certo ponto antes da capacidade de monitorização está disponível. Normalmente, os cmdlets estão disponíveis se o estado no portal do administrador indica que a atualização 1710 está no **reiniciar anfitriões de armazenamento** passo. Especificamente, a atualização de cmdlet ocorre durante **passo: executar o passo 2.6 - lista branca de atualizar PrivilegedEndpoint**.

Também pode determinar se os cmdlets estão disponíveis através de programação ao consultar a lista de comandos do ponto final com privilégios. Para tal, execute os seguintes comandos do anfitrião de ciclo de vida de hardware ou a partir de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto final com privilégios é um anfitrião fidedigno. Para obter mais informações, consulte o passo 1 de [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS no seu ambiente de pilha do Azure (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03). Substitua *prefixo* com a cadeia de prefixo de máquina virtual específica ao seu ambiente.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Quando lhe forem pedidas as credenciais, utilize o &lt; *domínio Azure pilha*&gt;\cloudadmin conta ou uma conta que seja membro do grupo CloudAdmins. Para a conta de CloudAdmin, introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.

2. Obter a lista completa dos comandos que estão disponíveis no ponto final com privilégios. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Determine se o ponto final com privilégios foi atualizado.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Liste os comandos específicos para o módulo de Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Por exemplo:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Utilizar os cmdlets de gestão de atualização

> [!NOTE]
> Execute os seguintes comandos do anfitrião de ciclo de vida de hardware ou a partir de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto final com privilégios é um anfitrião fidedigno. Para obter mais informações, consulte o passo 1 de [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Ligar para o ponto final com privilégios e atribuir a variável de sessão

Execute os seguintes comandos para criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS no seu ambiente de pilha do Azure (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03) e atribuir uma variável de sessão.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Quando lhe forem pedidas as credenciais, utilize o &lt; *domínio Azure pilha*&gt;\cloudadmin conta ou uma conta que seja membro do grupo CloudAdmins. Para a conta de CloudAdmin, introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Obter o estado de alto nível a execução de atualização atual 

Para obter um Estado de alto nível a execução de atualização atual, execute os seguintes comandos: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Os valores possíveis incluem:

- A Executar
- Foi concluída
- Falha 
- Cancelado

Pode executar estes comandos repetidamente para ver o estado mais atualizado. Não tem de voltar a estabelecer uma ligação para verificar novamente.

### <a name="get-the-full-update-run-status-with-details"></a>Obter a execução estado com detalhes de atualização completa 

Pode obter a atualização completa executar resumo como uma cadeia XML. Pode escrever a cadeia para um ficheiro para serem examinados, ou convertê-lo para um documento XML e utilizar o PowerShell para analisá-lo. O comando seguinte analisa o XML para obter uma lista hierárquica dos passos atualmente em execução.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

No exemplo seguinte, o passo de nível superior (nuvem Update) tem um plano de subordinados para atualizar e reinicie os anfitriões de armazenamento. Mostra que o plano de reiniciar anfitriões de armazenamento está a atualizar o serviço de armazenamento de BLOBs em um dos anfitriões.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>Obter o registo verboso progresso

Pode escrever o registo para um ficheiro para serem examinados. Isto pode ajudar a diagnosticar uma falha de atualização.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Ver ativamente o registo verboso

Ativamente para ver o registo verboso durante uma atualização executar e ir para as entradas mais recentes, execute os seguintes comandos para introduzir a sessão no modo interativo e para mostrar o registo:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
O registo de atualizações a cada 60 segundos e novo conteúdo (se disponível) é escrito na consola. 

Durante os processos em segundo plano de longa execução, o resultado da consola pode não ter sido escrito para a consola para algum tempo. Para cancelar a saída interativa, prima Ctrl + C. 

### <a name="resume-a-failed-update-operation"></a>Retomar uma operação de atualização falhou

Se a atualização falhar, pode retomar a execução de onde parou da atualização.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Resolução de problemas

O ponto final com privilégios está disponível em todas as máquinas de virtuais ERCS no ambiente de pilha do Azure. Uma vez que a ligação não é efetuada para um ponto final de elevada disponibilidade, pode deparar-se ocasionais interrupções, aviso ou mensagens de erro. Estas mensagens poderão indicar que a sessão foi desligada ou Ocorreu um erro ao comunicar com o serviço de ECE. Este comportamento é esperado. Pode repetir a operação dentro de alguns minutos ou criar uma nova sessão de ponto final com privilégios das outras máquinas virtuais ERCS. 

## <a name="next-steps"></a>Passos seguintes

- [Gerir atualizações na pilha do Azure](azure-stack-updates.md) 


