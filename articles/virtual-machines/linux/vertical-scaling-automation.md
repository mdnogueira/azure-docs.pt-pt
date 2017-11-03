---
title: "Aumentar verticalmente a máquina virtual do Azure com a automatização do Azure | Microsoft Docs"
description: "Como aumentar verticalmente uma Máquina Virtual Linux em resposta a alertas com a automatização do Azure de monitorização"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: singhkay
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ffcecf1e61fc0cd9ee668514fbb913dafe39bd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Aumentar verticalmente a máquina virtual de Linux do Azure com a automatização do Azure
Dimensionamento vertical é o processo de aumentar ou diminuir os recursos de uma máquina em resposta à carga de trabalho. No Azure Isto pode ser conseguido através da alteração do tamanho da Máquina Virtual. Isto pode ajudar nos seguintes cenários

* Se a Máquina Virtual não está a ser utilizada com frequência, pode redimensioná-la para um tamanho mais pequeno para reduzir os custos mensais
* Se a Máquina Virtual está a ter um pico de carga, pode ser redimensionada para um tamanho maior para aumentar a capacidade

O contorno para obter os passos realizar esta tarefa é como abaixo

1. Configurar a automatização do Azure para aceder às suas máquinas virtuais
2. Importar os runbooks de escala Vertical de automatização do Azure para a sua subscrição
3. Adicionar um webhook ao runbook
4. Adicionar um alerta para a Máquina Virtual

> [!NOTE]
> Devido ao tamanho da Máquina Virtual primeiro, os tamanhos pode ser ampliada, poderá ser limitado devido a disponibilidade dos outros tamanhos do cluster implementado na máquina Virtual atual. Os runbooks de automatização publicados utilizados neste artigo asseguramos neste caso e dimensionar apenas dentro do abaixo pares de tamanho VM. Isto significa que uma Máquina Virtual de Standard_D1v2 subitamente não irá ser escalado para cima para Standard_G5 ou ampliada para baixo para Basic_A0.
> 
> | Dimensionamento par de tamanhos de VM |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1v2 |Standard_D5v2 |
> | Standard_D11v2 |Standard_D14v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar a automatização do Azure para aceder às suas máquinas virtuais
A primeira coisa que precisa de fazer é criar uma conta de automatização do Azure que irá alojar os runbooks utilizados para dimensionar as instâncias do conjunto de dimensionamento de VM. Recentemente, o serviço de automatização introduziu a funcionalidade de "Conta Run As" que faz com que a definição de segurança de Principal de serviço para executar automaticamente os runbooks em nome do utilizador muito fácil. Pode ler mais sobre este artigo abaixo:

* [Autenticar Runbooks com a conta Run As do Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar os runbooks de escala Vertical de automatização do Azure para a sua subscrição
Os runbooks que são necessários para aumentar verticalmente a sua máquina Virtual já estão publicados na Galeria de Runbook da automatização do Azure. Terá de importá-los na sua subscrição. Pode saber como importar runbooks ao ler o artigo seguinte.

* [Galleries módulos e Runbooks de automatização do Azure](../../automation/automation-runbook-gallery.md)

Os runbooks que precisam de ser importados são apresentados na imagem abaixo

![Importar runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao runbook
Assim que tiver de importar os runbooks que terá de adicionar um webhook ao runbook para que pode ser acionada por um alerta de uma Máquina Virtual. Os detalhes de criar um webhook de Runbook podem ser lidos aqui

* [Webhooks de automatização do Azure](../../automation/automation-webhooks.md)

Certifique-se de que copia o webhook antes de fechar a caixa de diálogo de webhook como irá precisar na secção seguinte.

## <a name="add-an-alert-to-your-virtual-machine"></a>Adicionar um alerta para a Máquina Virtual
1. Selecione as definições da Máquina Virtual
2. Selecione "Regras de alerta"
3. Selecione "Adicionar alerta"
4. Selecione uma métrica a acionar o alerta no
5. Selecione uma condição que quando cumprido irá fazer com que o alerta accionar
6. Selecione um limiar para a condição no passo 5. para ser cumprido
7. Selecione um período durante o qual irá verificar o serviço de monitorização para a condição e o limiar em 5 passos & 6
8. Colar o webhook que copiou da secção anterior.

![Adicionar o alerta a Máquina Virtual 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar o alerta a Máquina Virtual 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

