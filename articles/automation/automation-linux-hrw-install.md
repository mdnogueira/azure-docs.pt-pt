---
title: "Trabalho de Runbook híbrida da automatização do Azure Linux | Microsoft Docs"
description: "Este artigo fornece informações sobre como instalar um Azure Automation Runbook Worker híbrido que permite a execução de runbooks em computadores baseados em Linux no seu local datacenter ou o ambiente de nuvem."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 5e6687e4e868ae998c77bba231437d52fdbe719c
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Como implementar um Runbook Worker híbrido do Linux

Os Runbooks na automatização do Azure não é possível aceder a recursos em outras nuvens ou no seu ambiente no local, uma vez que são executados na nuvem do Azure. A funcionalidade de trabalho de Runbook híbrida da automatização do Azure permite-lhe executar runbooks diretamente no computador que aloja a função e relativamente aos recursos no ambiente para gerir os recursos locais. Os Runbooks são armazenados e geridos na automatização do Azure e, em seguida, entregar uma ou mais computadores designados.

Esta funcionalidade é ilustrada na imagem seguinte:<br>  

![Descrição geral de trabalho de Runbook híbrida](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para uma descrição geral técnica da função do Runbook Worker híbrido, consulte [descrição geral da arquitetura de automatização](automation-offering-get-started.md#automation-architecture-overview). Reveja as seguintes informações sobre o [requisitos de hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e [informações para preparar a sua rede](automation-offering-get-started.md#network-planning) antes de começar a implementar um Runbook Worker híbrido.  Depois de ter implementado com êxito um runbook worker, reveja [executar runbooks num Runbook Worker híbrido](automation-hrw-run-runbooks.md) para saber como configurar os runbooks para automatizar processos no seu centro de dados no local ou outro ambiente de nuvem.     

## <a name="hybrid-runbook-worker-groups"></a>Grupos de trabalho de Runbook híbrida
Cada Runbook Worker híbrido é um membro de um grupo de trabalho de Runbook híbrida que especificou quando instalar o agente.  Um grupo pode incluir um único agente, mas pode instalar múltiplos agentes num grupo de disponibilidade elevada.

Quando inicia um runbook num Runbook Worker híbrido, especifique o grupo que é executada no.  Os membros do grupo de determinam qual trabalho processa o pedido.  Não é possível especificar uma função de trabalho específica.

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalar o Runbook Worker híbrido Linux
Para instalar e configurar um Runbook Worker híbrido no computador Linux, pode seguir um processo muito diretamente reencaminhar para instalar manualmente e configurar a função.   É necessário ativar o **automatização de trabalho híbrida** solução na sua área de trabalho do OMS e, em seguida, executar um conjunto de comandos para registar o computador como uma função de trabalho e adicioná-lo a um grupo novo ou existente. 

Antes de continuar, terá de ter em atenção a área de trabalho de análise de registos que está ligada a conta de automatização bem como a chave primária para a sua conta de automatização.  Pode encontrar no portal, selecione a sua conta de automatização e selecionar **área de trabalho** para o ID da área de trabalho e selecionar **chaves** para a chave primária.  

1.  Ative a solução "Worker híbrido de automatização" no OMS. Isto pode ser feito ao:

   1. Na galeria do soluções no [portal do OMS](https://mms.microsoft.com) ativar o **automatização de trabalho híbrida** solução
   2. Execute o seguinte cmdlet:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Execute o seguinte comando, alterando os valores para parâmetros *-w*, *-k*, *-g*, e *-i*. Para o *-g* parâmetro substitua o valor com o nome do grupo de trabalho de Runbook híbrida que o Runbook Worker novo do Linux híbrido deve ser associado. Se o nome não existir já na sua conta de automatização, um novo grupo de trabalho de Runbook híbrida será efetuado com esse nome.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Depois do comando for concluído, o painel de grupos de trabalho híbrida no portal do Azure irá mostrar o novo grupo e o número de membros ou se um grupo existente, o número de membros é incrementado.  Pode selecionar o grupo da lista no **grupos de trabalho híbrida** painel e selecione o **híbridos** mosaico.  No **híbridos** painel, verá cada membro do grupo listado.  


## <a name="turning-off-signature-validation"></a>Desativar a validação da assinatura 
Por predefinição, os Runbook Workers híbridos Linux exigir validação da assinatura. Se executar um runbook não atribuído em relação a uma função de trabalho, verá um erro que contêm "Validação da assinatura falhou". Para desativar a validação da assinatura, execute o seguinte comando, substituindo o segundo parâmetro com o seu ID de área de trabalho do OMS:

    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <OMSworkspaceId>
    ```
   
## <a name="next-steps"></a>Passos Seguintes

* Reveja [executar runbooks num Runbook Worker híbrido](automation-hrw-run-runbooks.md) para saber como configurar os runbooks para automatizar processos no seu centro de dados no local ou outro ambiente de nuvem.
* Para obter instruções sobre como remover os Runbook Workers híbridos, consulte [remover Azure automatização de Runbook híbridos](automation-remove-hrw.md)
