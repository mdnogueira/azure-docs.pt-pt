---
title: "Resolução de problemas de pilha do Microsoft Azure | Microsoft Docs"
description: "Resolução de pilha do Azure."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: helaw
ms.openlocfilehash: 0a8e871a3a44cb14503832d2f3a096712f8112a7
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Resolução de problemas de pilha do Microsoft Azure

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

Este documento fornece informações de resolução de problemas comuns de pilha do Azure. 

Porque o Kit de desenvolvimento de técnica de pilha do Azure é fornecido como um ambiente de avaliação, não são suportadas oficial do suporte técnico da Microsoft.  Se estiver a ter um problema não documentado, certifique-se verificar o [fórum MSDN do Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obter assistência adicional e informações.  

As recomendações de resolução de problemas que são descritos nesta secção são derivadas de várias origens e podem ou não poderão resolver o problema específico. Exemplos de código são fornecidos tal como está e não podem ser garantidos resultados esperados. Nesta secção está sujeita a edições frequentes e atualizações, melhoramentos ao produto são implementados.

## <a name="deployment"></a>Implementação
### <a name="deployment-failure"></a>Falha de implementação
Se ocorrer uma falha durante a instalação, pode utilizar utilize a opção para executar novamente o script de implementação para reiniciar a implementação do passo falhado.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>No final da implementação, a sessão do PowerShell é aberta e não mostra resultados
Este comportamento é, provavelmente, apenas o resultado do comportamento predefinido de uma janela de comando do PowerShell, quando foi selecionada. A implementação do kit de desenvolvimento, na verdade, é concluída com êxito, mas o script foi colocada em pausa quando selecionar a janela. Pode verificar que for este o caso, procurando a palavra "selecionar" no titlebar da janela de comando.  Prima a tecla ESC para anular-a e a mensagem de conclusão deve ser apresentada depois.

## <a name="virtual-machines"></a>Máquinas virtuais
### <a name="default-image-and-gallery-item"></a>Item de imagem e galeria predefinido
Tem de adicionar um item de imagem e da galeria do Windows Server antes de implementar VMs na pilha do Azure.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Depois de reiniciar o meu anfitrião de pilha do Azure, algumas VMs poderão não iniciar automaticamente.
Depois de reiniciar o seu anfitrião, poderá reparar serviços do Azure pilha não estão imediatamente disponíveis.  Isto acontece porque a pilha de Azure [infraestrutura VMs](azure-stack-architecture.md#virtual-machine-roles) e RPs demorar um pouco bit para verificar a consistência, mas, eventualmente, será automaticamente iniciado.

Também poderá reparar que inquilino que VMS não iniciar automaticamente após um reinício do anfitrião de kit de desenvolvimento de pilha do Azure.  Este é um problema conhecido e requer apenas alguns passos manuais para colocá-los online:

1.  No anfitrião de kit de desenvolvimento de pilha do Azure, iniciar **Gestor de clusters de ativação pós-falha** no menu Iniciar.
2.  Selecione o cluster **S Cluster.azurestack.local**.
3.  Selecione **funções**.
4.  VMs de inquilino serão apresentados num *guardado* estado.  Assim que estiver a executar todas as VMs de infraestrutura, as VMs de inquilino com o botão direito e selecione **iniciar** para retomar a VM.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Tem de eliminar algumas máquinas virtuais, mas ainda ver os ficheiros VHD no disco. Este comportamento é esperado?
Sim, isto é comportamento esperado. Foi concebido desta forma porque:

* Quando elimina uma VM, VHDs não são eliminados. Os discos estão separados recursos no grupo de recursos.
* Quando uma conta de armazenamento é eliminada, a eliminação está visível imediatamente através do Azure Resource Manager (portal, PowerShell), mas os discos que podem conter ainda são mantidos no armazenamento até que a recolha de lixo é executado.

Se vir "orphan" VHDs, é importante saber se o que fazem parte da pasta para a conta de armazenamento que foi eliminada. Se a conta de armazenamento não foi eliminada, é normal são ainda não existe.

Pode ler mais sobre como configurar a recuperação de limiar e a pedido de retenção no [gerir contas de armazenamento](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Armazenamento
### <a name="storage-reclamation"></a>Recuperação de armazenamento
Pode demorar até fourteen horas para a capacidade de reclamada a aparecer no portal. Recuperação de espaço depende de vários fatores, incluindo a percentagem de utilização dos ficheiros de contentor interno no arquivo de blob de bloco. Por conseguinte, dependendo da quantidade de dados é eliminado, não há nenhuma garantia, a quantidade de espaço que foi recuperado quando executa o recoletor de lixo.

## <a name="windows-azure-pack-connector"></a>Conector do Windows Azure Pack
* Se alterar a palavra-passe da conta azurestackadmin depois de implementar o kit de desenvolvimento de pilha do Azure, já não pode configurar o modo de cloud multi. Por conseguinte, não é possível estabelecer ligação ao ambiente do Windows Azure Pack de destino.
* Depois de configurar o modo de vários cloud:
    * Um utilizador pode ver o dashboard apenas depois de serem repor as definições do portal. (No portal de utilizador, clique no ícone de definições do portal (engrenagem ícone no canto superior direito). Em **restaurar predefinições**, clique em **aplicar**.)
    * Os títulos de dashboard podem não aparecer. Se este problema ocorrer, tem de adicionar manualmente-las novamente.
    * Alguns mosaicos poderão não mostrar corretamente quando os adicionar primeiro ao dashboard. Para corrigir este problema, atualize o browser.



