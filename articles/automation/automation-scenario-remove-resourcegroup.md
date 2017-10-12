---
title: "Automatizar a remoção de grupos de recursos | Microsoft Docs"
description: "Versão do Fluxo de Trabalho do PowerShell de um cenário da Automatização do Azure, incluindo runbooks, para remover todos os grupos de recursos da sua subscrição."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: magoedte
ms.openlocfilehash: 8b23e55a597f293b17183e80eea6c2763aabe9ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Cenário da Automatização do Azure – remoção automática de grupos de recursos
Muitos clientes criam mais do que um grupo de recursos. Alguns podem ser utilizados para gerir aplicações de produção, ao passo que outros podem ser utilizados como ambientes de desenvolvimento e de teste. Automatizar a implementação destes recursos é uma coisa, mas conseguir desativar um grupo de recursos com o clique de um botão é outra. Pode simplificar esta tarefa de gestão comum através da Automatização do Azure. Isto é útil se estiver a trabalhar com uma subscrição do Azure que tenha um limite de gastos através de uma oferta de membro, como o MSDN ou o programa Microsoft Partner Network Cloud Essentials.

Este cenário baseia-se num runbook do PowerShell e foi concebido para remover um ou mais grupos de recursos que especificar da sua subscrição. A predefinição do runbook é testar antes de continuar. Isto garante que não eliminará o grupo de recursos acidentalmente antes de estar pronto para concluir este procedimento.   

## <a name="getting-the-scenario"></a>Obter o cenário
Este cenário é constituído por um runbook do PowerShell que pode transferir a partir da [Galeria do PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). Também pode importá-lo diretamente a partir da [Galeria de Runbooks](automation-runbook-gallery.md) no portal do Azure.<br><br>

| Runbook | Descrição |
| --- | --- |
| Remove-ResourceGroup |Remove um ou mais grupos de recursos do Azure e os recursos associados da subscrição. |

<br>
São definidos para este runbook os seguintes parâmetros de entrada:

| Parâmetro | Descrição |
| --- | --- |
| NameFilter (obrigatório) |Especifica um filtro de nomes para limitar os grupos de recursos que planeia eliminar. Pode utilizar uma lista separada por vírgulas para transmitir vários valores.<br>O filtro não é sensível a maiúsculas e minúsculas e corresponderá a qualquer grupo de recursos que contenha a cadeia. |
| PreviewMode (opcional) |Executa o runbook para ver que grupos de recursos seriam eliminados, mas sem realizar ações.<br>A predefinição é **verdadeiro**, para ajudar a evitar a eliminação acidental de um ou mais grupos de recursos transmitidos para o runbook. |

## <a name="install-and-configure-this-scenario"></a>Instalar e configurar este cenário
### <a name="prerequisites"></a>Pré-requisitos
Este runbook utiliza a [conta Run As do Azure](automation-sec-configure-azure-runas-account.md) para se autenticar.    

### <a name="install-and-publish-the-runbooks"></a>Instalar e publicar os runbooks
Depois de transferir o runbook, pode importá-lo ao seguir o procedimento em [Procedimentos para importar runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Publique o runbook após ter sido importado com êxito para a sua conta da Automatização.

## <a name="using-the-runbook"></a>Utilizar o runbook
Os passos seguintes orientam-no ao longo da execução deste runbook e ajudam-no a familiarizar-se com o funcionamento do mesmo. Neste exemplo, só irá testar o runbook, mas não eliminará efetivamente o grupo de recursos.  

1. No portal do Azure, abra a sua conta de Automatização e clique em **Runbooks**.
2. Selecione o runbook **Remove-ResourceGroup** e clique em **Iniciar**.
3. Quando iniciar o runbook, é aberto o painel **Iniciar Runbook** e pode configurar os parâmetros. Introduza os nomes dos grupos de recursos da sua subscrição que irá utilizar para fins de teste e cuja eliminação acidental não causará problemas.<br> ![Parâmetros Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)

   > [!NOTE]
   > Certifique-se de que a opção **Previewmode** está definida como **verdadeiro** para evitar eliminar os grupos de recursos selecionados.  **Tenha em atenção** que este runbook não irá remover o grupo de recursos que contém a conta de Automatização que está a executar este runbook.  
   >
   >
4. Depois de configurar todos os valores do parâmetro, clique em **OK** e o runbook será adicionado à fila para ser executado.  

Para ver os detalhes da tarefa do runbook **Remove-ResourceGroup** no portal do Azure, selecione **Tarefas** no runbook. O resumo da tarefa apresenta os parâmetros de entrada e o fluxo de saída, para além de informações gerais sobre a tarefa e eventuais exceções que possam ter ocorrido.<br> ![Estado da tarefa do runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

O **Resumo da Tarefa** inclui mensagens dos fluxos de saída, aviso e erro. Selecione **Saída** para ver resultados detalhados da execução do runbook.<br> ![Resultados da saída do runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Passos seguintes
* Para começar a criar o seu próprio runbook, veja [Criar ou importar um runbook na Automatização do Azure](automation-creating-importing-runbook.md).
* Para começar a utilizar runbooks do Fluxo de Trabalho do PowerShell, veja [O meu primeiro runbook do Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md).
