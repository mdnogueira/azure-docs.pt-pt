---
title: "Recolher a métrica de recursos do Azure PaaS com Log Analytics | Microsoft Docs"
description: "Saiba como ativar a recolha de métricas de recurso de PaaS do Azure através do PowerShell para retenção e a análise na análise de registos."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 7bae18e151fbdccf95f3fe5f569041d6dd9c42eb
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Configurar a recolha de métricas de recurso do Azure PaaS com a análise de registos

Plataforma do Azure como um recursos de serviço (PaaS), como SQL do Azure e Web Sites (aplicações Web), pode emitir dados de métricas de desempenho nativamente à análise de registos. Este script permite aos utilizadores ativar as métricas de registo de recursos de PaaS já implementados num grupo de recursos específico ou através de uma subscrição completa. 

Atualmente, não é possível ativar métricas de registo para o PaaS recursos através do portal do Azure. Por conseguinte, tem de utilizar um script do PowerShell. Esta capacidade de registo de métricas nativo juntamente com a monitorização de análise de registos, permitem-lhe monitorizar os recursos do Azure à escala. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que tem os seguintes módulos do Azure Resource Manager instalados no seu computador antes de continuar:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Recomendamos que todos os módulos do Azure Resource Manager são a mesma versão para garantir a compatibilidade quando executar comandos do Azure Resource Manager a partir do PowerShell.
>
Para instalar a versão mais recente dos módulos do Azure Resource Manager no seu computador, consulte [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Ativar o diagnóstico do Azure  
Configurar diagnósticos do Azure para recursos de PaaS é conseguido ao executar o script, **ativar AzureRMDiagnostics.ps1**, que está disponível a partir de [galeria do PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  O script suporta os seguintes cenários:
  
* Especificação de um recurso relacionado com um ou mais grupos de recursos de uma subscrição  
* Especificação de um recurso relacionado com um grupo de recursos específico numa subscrição  
* Reconfigurar um recurso para reencaminhar para uma área de trabalho diferente

Apenas os recursos que suportam as métricas de recolher com diagnóstico do Azure e enviam diretamente ao Log Analytics são suportados.  Para obter uma lista detalhada, consulte [métricas para utilização na análise de registos e registos do serviço de recolha do Azure](log-analytics-azure-storage.md) 

Execute os seguintes passos para transferir e executar o script.

1.  A partir do ecrã de início de Windows, escreva **PowerShell** e com o botão direito do PowerShell de resultados da pesquisa.  Selecione o menu **executar como administrador**.   
2. Guardar o **ativar AzureRMDiagnostics.ps1** ficheiro de script localmente, executando o seguinte comando e fornecer um caminho para armazenar o script.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Executar `Login-AzureRmAccount` para criar uma ligação com o Azure.   
4. Execute o seguinte script `.\Enable-AzureRmDiagnostics.ps1` sem quaisquer parâmetros para ativar a recolha de dados de um recurso específico na sua subscrição ou com o parâmetro `-ResourceGroup <myResourceGroup>` para especificar um recurso num grupo de recursos específico.   
5. Selecione a subscrição adequada na lista se tiver mais do que uma, introduzindo o valor correto.<br><br> ![Selecione a subscrição devolvida pelo script](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Caso contrário, seleciona automaticamente a subscrição único disponível.
6. Em seguida, o script devolve uma lista de áreas de trabalho de análise de registos registado na subscrição.  Selecione a adequado na lista.<br><br> ![Selecione a área de trabalho devolvida pelo script](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Selecione o recurso do Azure que gostaria de ativar a recolha do. Por exemplo, se escrever 5, ativar a recolha de dados para bases de dados do SQL do Azure.<br><br> ![Tipo de recurso selecione devolvido pelo script](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Pode selecionar apenas os recursos que suportam as métricas de recolher com o diagnóstico do Azure e o envio de diretamente para uma análise de registos.  O script irá mostrar um valor de **verdadeiro** sob o **métricas** coluna para a lista de recursos que Deteta na sua subscrição ou grupo de recursos especificado.    
8. Lhe for pedido para confirmar a sua seleção.  Introduza **Y** ativar o registo de métricas para todos os selecionados recursos para o âmbito definido, que, no nosso exemplo, todas as bases de dados do SQL Server na subscrição.  

O script irá executar todas as recursos que correspondam aos critérios selecionados e ativar a recolha de métricas para os mesmos. Depois de concluído, verá uma mensagem a indicar a configuração está concluída.  

Em breve após a conclusão, será iniciado ver os dados de recursos Azure PaaS no seu repositório de análise de registos.  Um registo com o tipo `AzureMetrics` é criado e analisar estes registos são suportadas pelo [análise do Azure SQL](log-analytics-azure-sql.md) e [análise de aplicações Web do Azure](log-analytics-azure-web-apps-analytics.md) soluções de gestão.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Atualizar um recurso para enviar dados para outra área de trabalho
Se tiver um recurso que já está a enviar dados para uma área de trabalho de análise de registos e, posteriormente, decidir reconfigurá-lo para fazer referência a outra área de trabalho, pode executar o script com o `-Update` parâmetro.  

**Exemplo:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Será solicitado para atender as mesmas informações que quando executou o script para executar a configuração inicial.  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 

* Utilize [campos personalizados](log-analytics-custom-fields.md)(para analisar os registos de eventos em campos individuais.

* Reveja [criar um dashboard personalizado para ser utilizado na análise de registos](log-analytics-dashboards.md) compreender como visualizar o início de sessão procura uma forma lógica para a organização.