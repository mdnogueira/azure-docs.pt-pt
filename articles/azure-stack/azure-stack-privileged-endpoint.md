---
title: "Utilizar o ponto final com privilégios na pilha do Azure | Microsoft Docs"
description: "Mostra como utilizar o ponto final com privilégios na pilha do Azure (para um operador de pilha do Azure)."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 9769b12064216680bb1b2db8c1fd7449927c7771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Utilizar o ponto final com privilégios na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como um operador de pilha do Azure, deve utilizar o portal de administrador, PowerShell ou APIs do Azure Resource Manager para as tarefas de gestão mais diárias. No entanto, para algumas menos operações comuns, tem de utilizar o *ponto final com privilégios*. Este ponto final é uma consola de PowerShell remota pré-configurada que fornece-lhe apenas suficiente capacidades para o ajudar a efetuar uma tarefa necessária. O ponto final tira partido do PowerShell JEA (apenas suficiente administração) para expor apenas um conjunto de cmdlets restrito. Para aceder ao ponto final com privilégios e invocar o conjunto de cmdlets restrito, é utilizada uma conta de baixo privilégio. Não existem contas de administrador são necessárias. Para segurança adicional, processamento de scripts não é permitido.

Pode utilizar o ponto final com privilégios para efetuar tarefas, tais como o seguinte:

- Para efetuar tarefas de baixo nível, tais como [recolher registos de diagnóstico](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Para executar muitas tarefas de integração do Centro de dados de pós-implementação para sistemas integrados, como adicionar reencaminhadores do sistema de nomes de domínio (DNS) após a implementação, como configurar a integração de gráfico, integração de serviços de Federação do Active Directory (AD FS), certificado rotação, etc.
- Trabalhar com suporte para obter acesso temporário alto nível para resolução de problemas detalhada de um sistema integrado. 

O ponto final com privilégios regista cada ação (e o resultado correspondente) que efetua na sessão do PowerShell. Isto fornece a transparência completa e auditoria completa de operações. Pode manter estes ficheiros de registo para fins de auditorias futuras.

> [!NOTE]
> No Azure pilha Development Kit (ASDK), pode executar alguns dos comandos disponíveis no ponto final com privilégios diretamente a partir de uma sessão do PowerShell no anfitrião do kit de desenvolvimento. No entanto, pode pretender testar algumas operações, utilizando o ponto final com privilégios, tais como recolha de registos, porque este é o único método disponível para executar determinadas operações num ambiente sistemas integrada.

## <a name="access-the-privileged-endpoint"></a>Aceder ao ponto final com privilégios

O ponto final com privilégios que aceder através de uma sessão remota do PowerShell na máquina virtual que aloja o ponto final com privilégios. No ASDK, esta máquina virtual é denominada AzS ERCS01. Se estiver a utilizar um sistema integrado, existem três instâncias do ponto final com privilégios, cada um em execução numa máquina virtual (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03) em diferentes anfitriões para resiliência. 

Antes de iniciar este procedimento para um sistema integrado, certifique-se de que pode aceder um ponto final com privilégios pelo endereço IP ou através de DNS. Após a implementação inicial da pilha do Azure, pode aceder o ponto final com privilégios apenas por endereço IP porque a integração do DNS não estiver ainda definida cópias de segurança. O fornecedor do hardware OEM irá fornecer um ficheiro JSON com o nome "AzureStackStampDeploymentInfo" que contém os endereços IP do ponto final com privilégios.

Recomendamos que se ligar ao ponto final com privilégios apenas a partir do anfitrião de ciclo de vida de hardware ou de um computador dedicado, bloqueado para baixo, como um [estação de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Efetue um dos seguintes procedimentos consoante o seu ambiente:

    - Num sistema integrado, execute o seguinte comando para adicionar o ponto final com privilégios como um anfitrião fidedigno no seu anfitrião de ciclo de vida de hardware ou estação de trabalho de acesso privilegiado.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se estiver a executar o ADSK, inicie sessão para o anfitrião do kit de desenvolvimento.

2. No seu anfitrião de ciclo de vida de hardware ou estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell elevada. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que aloja o ponto final com privilégios:
 
    - Num sistema integrado:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que aloja um ponto final com privilégios. 
    - Se estiver a executar o ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando lhe for pedido, utilize as seguintes credenciais:

      - **Nome de utilizador**: Especifique a conta de CloudAdmin, no formato  **&lt;* domínio Azure pilha*&gt;\cloudadmin**. (Para ASDK, o nome de utilizador é **azurestack\cloudadmin**.)
      - **Palavra-passe**: introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.
    
3.  Depois de ligar, a linha de comandos será alterado para  **[*nome do endereço IP ou ERCS VM*]: PS > * * ou **[azs ercs01]: PS >**, consoante o ambiente. Aqui, execute `Get-Command` para ver a lista dos cmdlets disponíveis.

    ![Lista de apresentação do cmdlet Get-Command saída de comandos disponíveis](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Muitos destes cmdlets destinam-se apenas para ambientes de sistema integrada (por exemplo, os cmdlets relacionados com a integração do Centro de dados). No ASDK, ter foi validados os seguintes cmdlets:

    - Desmarque-anfitrião
    - Feche PrivilegedEndpoint
    - Sair-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Objeto de medida
    - Novo CloudAdminUser
    - Out-predefinido
    - Remover CloudAdminUser
    - Select-Object
    - Conjunto CloudAdminUserPassword
    - Stop-AzureStack
    - Get-ClusterLog

4.  Porque não é permitida a criação de scripts, não é possível utilizar a conclusão de separador para os valores de parâmetros. Para obter a lista de parâmetros para um cmdlet específico, execute o seguinte comando:

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    Se tentar qualquer tipo de operação de script, a operação falhar com o erro **ScriptsNotAllowed**. Este comportamento está previsto.

## <a name="close-the-privileged-endpoint-session"></a>Feche a sessão de ponto final com privilégios

 Conforme mencionado anteriormente, o ponto final com privilégios regista cada ação (e o resultado correspondente) que efetua na sessão do PowerShell. Deve fechar a sessão utilizando o `Close-PrivilegedEndpoint` cmdlet. Este cmdlet corretamente fecha o ponto final e transfere os ficheiros de registo para uma partilha de ficheiros externos de retenção.

Para fechar a sessão de ponto final:

1. Crie uma partilha de ficheiro externo que seja acessível pelo ponto final com privilégios. Num ambiente de kit de desenvolvimento, apenas pode criar uma partilha de ficheiros no anfitrião do kit de desenvolvimento.
2. Execute o `Close-PrivilegedEndpoint` cmdlet. 
3. Lhe for pedida para um caminho no qual pretende armazenar o ficheiro de registo transcript. Especifique a partilha de ficheiros que criou anteriormente, no formato &#92; &#92; *servername*&#92; *ShareName*. Se não especificar um caminho, o cmdlet falhar e a sessão permanece aberta. 

    ![Resultado do cmdlet fechar PrivilegedEndpoint que mostra onde pode Especifica o caminho de destino transcript](media/azure-stack-privileged-endpoint/closeendpoint.png)

Depois dos ficheiros de registo transcript são transferidos com êxito para a partilha de ficheiros, está a automaticamente eliminados do ponto final com privilégios. Se fechar a sessão de ponto final com privilégios utilizando os cmdlets `Exit-PSSession` ou `Exit`, ou apenas a fechar a consola do PowerShell, os registos de transcript não transferir para uma partilha de ficheiros. Permanecem no ponto final com privilégios. Da próxima vez que executar `Close-PrivilegedEndpoint` e incluir uma partilha de ficheiros, os registos de transcript do anterior sessões também irão transferir.

## <a name="next-steps"></a>Passos seguintes
[Ferramentas de diagnóstico de pilha do Azure](azure-stack-diagnostics.md)







