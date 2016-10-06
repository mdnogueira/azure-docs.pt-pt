<properties
    pageTitle="Automatizar a remoção de Grupos de Recursos | Microsoft Azure"
    description="Versão do Fluxo de Trabalho do PowerShell de um cenário da Automatização do Azure, incluindo runbooks, para remover todos os Grupos de Recursos da sua subscrição."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>


# Cenário da Automatização do Azure – remoção automática de grupos de recursos

Muitos clientes criam mais de um grupo de recursos, em que alguns se dedicam a gerir aplicações de produção e outros poderão ser ambientes de desenvolvimento, teste e transição. Automatizar a implementação destes recursos é uma coisa, mas conseguir desativar um grupo de recursos com o clique de um botão é outra.  Utilizar a Automatização para o fazer é um caso de utilização perfeito e uma oportunidade para uniformizar esta tarefa de gestão tão comum. A remoção automática também é útil se estiver a trabalhar com uma subscrição do Azure que tenha um limite de gastos através de uma oferta de membro, como o MSDN ou o programa Microsoft Partner Network Cloud Essentials, por exemplo. 

Este cenário baseia-se num runbook do PowerShell e foi concebido para remover um ou mais grupos de recursos que especificar da sua subscrição.  O runbook permite testar primeiro antes de avançar, que é o valor predefinido.  Desta forma, não elimina acidentalmente os grupos sem ter a certeza absoluta de que está pronto para concluir o procedimento.   

## Obter o cenário

Este cenário consiste num runbook do PowerShell que pode transferir a partir da [Galeria do PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) ou importar diretamente da [Galeria de Runbooks](automation-runbook-gallery.md), no portal do Azure.<br><br> 

Runbook | Descrição|
----------|------------|
Remove-ResourceGroup | Remove um ou mais grupos de recursos do Azure e os respetivos recursos da subscrição.  
<br>
São definidos para este runbook os seguintes parâmetros de entrada:

Parâmetro | Descrição|
----------|------------|
NameFilter (obrigatório) | Permite-lhe especificar um filtro de nomes para limitar os grupos de recursos que planeia eliminar. Pode transmitir vários valores mediante a utilização de uma lista separada por vírgulas.<br>O filtro não é sensível a maiúsculas e minúsculas e corresponderá a qualquer grupo de recursos que contenha a cadeia.|
PreviewMode (opcional) | Execute o runbook para ver que grupos de recursos seriam eliminados, mas sem tomar ações.<br>A predefinição é **verdadeiro**, para ajudar a evitar a eliminação acidental de um ou mais grupos de recursos transmitidos para o runbook.  

## Instalar e configurar este cenário

### Pré-requisitos

Este runbook utiliza a [conta Run As do Azure](automation-sec-configure-azure-runas-account.md) para se autenticar.    

### Instalar e publicar os runbooks

Depois de transferir o runbook, pode importá-lo ao seguir o procedimento em [Procedimentos para importar runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation).  Publique o runbook após ter sido importado com êxito para a sua conta da Automatização.


## Utilizar o runbook

Os passos seguintes orientam-no ao longo da execução deste runbook e ajudam-no a familiarizar-se com o funcionamento do mesmo.  Neste exemplo, só vamos testar o runbook, não vamos eliminar efetivamente o grupo de recursos.  

1. A partir do Portal do Azure, abra a sua conta da Automatização e clique no mosaico **Runbooks**.
2. Selecione o runbook **Remove-ResourceGroup** e clique em **Iniciar**.
3. Quando iniciar o runbook, é aberto o painel **Iniciar Runbook** e pode configurar os valores seguintes dos parâmetros.  Introduza o nome de um ou mais grupos de recursos da sua subscrição com os quais pretende fazer o teste e cuja eliminação acidental não cause problemas.<br> ![Parâmetros de Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] Confirme que a opção **Previewmode** está definida como **verdadeiro**, para evitar eliminar o grupo ou grupos selecionados.  **Tenha em atenção** que este runbook não irá remover o grupo de recursos que contém a conta de Automatização que o está a executar.  

4. Depois de configurar todos os valores do parâmetro, clique em **OK** e o runbook será adicionado à fila para ser executado.  

Para ver os detalhes da tarefa do runbook **Remove-ResourceGroup** no portal do Azure, selecione o mosaico **Tarefas** do runbook. O resumo da tarefa apresenta os parâmetros de entrada e o fluxo de saída, para além de informações gerais sobre a tarefa e eventuais exceções, caso tenham ocorrido.<br> ![Estado da Tarefa do Runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

O **Resumo da Tarefa** inclui mensagens dos fluxos de saída, aviso e erro. Selecione o mosaico **Saída** para ver resultados detalhados da execução do runbook.<br> ![Resultados da Saída do Runbook Remove-ResourceGroup Runbook](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png) 

## Passos seguintes

- Para começar a criar o seu próprio runbook, veja [Creating or importing a runbook in Azure Automation (Criar ou importar um runbook na Automatização do Azure)](automation-creating-importing-runbook.md)
- Para começar a utilizar runbooks do Fluxo de Trabalho do PowerShell, veja [O meu primeiro runbook do Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)


<!--HONumber=Sep16_HO4-->


