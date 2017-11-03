---
title: "Gerir o Cofre de chaves do Azure através da automatização do Azure | Microsoft Docs"
description: "Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir o Cofre de chaves do Azure."
services: Key-Vault, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
ms.openlocfilehash: dee39662472fe54776b591977f2b1ecb39d15b00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Gerir o Cofre de chaves do Azure através da automatização do Azure
Este guia apresenta-lhe o serviço de automatização do Azure e como podem ser utilizada para simplificar a gestão das suas chaves e segredos no Cofre de chaves do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A automatização do Azure](../automation/automation-intro.md) é um serviço do Azure para simplificar a gestão de nuvem através de automatização de processos e a configuração de estado pretendido. Utilizar a automatização do Azure, manual, repetido, tarefas demoradas e propensas ao erro podem ser automatizadas para aumentar a fiabilidade, a eficiência e a hora para o valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente fiável, de elevada disponibilidade preparada para satisfazer as suas necessidades. Na automatização do Azure, os processos podem ser arrancou manualmente, por sistemas de terceiros 3rd ou em intervalos agendados para que as tarefas acontecer exatamente quando necessário.

Reduzir a sobrecarga operacional e libertar IT e a equipa de DevOps focar-se no trabalho que adiciona o valor de negócio, movendo as tarefas de gestão de nuvem a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Como pode que o automatização do Azure ajuda a gerir o Cofre de chaves do Azure?
O Cofre de chaves que podem ser gerido na automatização do Azure utilizando o [cmdlets do Cofre de chaves AzureRM](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e [cmdlets do Cofre de chaves do Azure clássico](https://msdn.microsoft.com/library/azure/dn868052.aspx). O módulo do Azure para gerir o Cofre de chaves clássico está disponível automaticamente na automatização do Azure e pode importar o [AzureRM KeyVault módulo](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) na automatização do Azure, para que possam executar muitas das suas tarefas de gestão do Cofre de chaves no âmbito do serviço. Também pode ser emparelhado estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em todos os serviços do Azure e 3rd sistemas de terceiros.

Com os cmdlets do Cofre de chaves do Azure pode efetuar estas tarefas das restantes: 

* Criar e configurar um cofre de chaves
* Criar ou importar uma chave
* Criar ou atualizar um segredo
* Atualizar os atributos de uma chave
* Obter uma chave ou segredo
* Eliminar uma chave ou segredo

Seguem-se alguns exemplos de utilização do PowerShell para gerir o Cofre de chaves:  

* [Cofre de chaves do Azure - passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Definir e configurar um cofre de chaves do Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas de automatização do Azure e como podem ser utilizada para gerir o Cofre de chaves do Azure, siga estas ligações para saber mais sobre a automatização do Azure.

* Consulte a automatização do Azure [Tutorial de introdução](../automation/automation-first-runbook-graphical.md).
* Consulte o [scripts do PowerShell do Cofre de chaves do Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

