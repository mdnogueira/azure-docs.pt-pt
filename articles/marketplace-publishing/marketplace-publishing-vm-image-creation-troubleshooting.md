---
title: "Como resolver problemas comuns durante a criação do VHD | Microsoft Docs"
description: "Respostas a perguntas de resolução de problemas comuns e problemas durante a criação do VHD."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Como resolver problemas comuns encontrados durante a criação do VHD
Este artigo é fornecido para o ajudar um publicador do Azure Marketplace e/ou coadministrador que pode ocorrer um problema ou tiver questões comuns ao publicar ou gerir os respetivos solution(s) de máquina virtual.

1. Como posso alterar o nome do anfitrião?
   
    Depois de criada a VM, os utilizadores não é possível atualizar o nome do anfitrião.
2. Como repor o serviço de ambiente de trabalho remoto ou a respetiva palavra-passe de início de sessão?
   
   * [Referência para a VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referência para a VM com Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Como gerar novos ssh certificados?
   
   Consulte a ligação: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Como configurar um certificado VPN abra?
   
   Consulte a ligação: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. O que é a política de suporte para executar o software de servidor Microsoft no ambiente de máquina virtual do Microsoft Azure (infraestrutura-como-um-serviço)?
   
   Consulte a ligação: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Máquinas virtuais dispõem de qualquer identificador exclusivo?
   
   Azure codifica ID exclusivo de VM do Azure em cada VM. Consulte os detalhes neste blogue e a documentação.
7. Numa VM como posso gerir a extensão de script personalizado na tarefa de arranque?
   
   Consulte a ligação: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Como criar uma VM a partir do portal do Azure com o VHD que é carregado para o premium storage?
   
   Iremos ainda não suporta esta funcionalidade.
9. Uma aplicação de 32 bits é suportada no Azure Marketplace?
   
   Consulte a hiperligação para obter detalhes sobre a política de suporte: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Sempre que esteja a tentar criar uma imagem a partir do meu VHDs, receber o erro ". VHD já está registado no repositório de imagens como o recurso"no PowerShell. Não foi possível a criar o qualquer imagem antes nem foi possível localizar qualquer imagem com este nome no Azure. Como resolver isto?
    
    Normalmente, isto acontecer se o utilizador aprovisionado uma VM a partir deste VHD e não existe um bloqueio nessa VHD. Verifique que não há nenhuma VM alocado a partir deste VHD. Se o erro persistir ainda, em seguida, volte emitir um pedido de suporte utilizando a seguinte hiperligação ou da publicação portal sobre isto (detalhes são fornecidos na resposta da pergunta 11).