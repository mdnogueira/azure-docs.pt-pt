---
title: "Resolver problemas de ativação pós-falha para falhas do Azure | Microsoft Docs"
description: "Este artigo descreve formas de resolver erros comuns no falha através do Azure"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: pratshar
ms.openlocfilehash: 5e1f9a0298c2abd542d7687778716f644a1d0a47
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Resolver erros ao efetuar a ativação pós-falha de uma máquina virtual para o Azure
Poderá receber um dos seguintes erros ao efetuar ativação pós-falha de uma máquina virtual para o Azure. Para resolver problemas, utilize os passos descritos para cada condição de erro.


## <a name="failover-failed-with-error-id-28031"></a>Falha na ativação pós-falha com o ID de erro 28031

Recuperação de site não conseguiu criar uma máquina virtual no Azure de ativação pós-falha. Pode ocorrer devido a um dos seguintes motivos:

* Não é uma quota suficiente disponível para criar a máquina virtual: pode verificar a quota disponível acedendo a subscrição -> utilização + quotas. Pode abrir um [novo pedido de suporte](http://aka.ms/getazuresupport) para aumentar a quota.
     
* Está a tentar máquinas de virtuais de ativação pós-falha das famílias de tamanho diferente no mesmo conjunto de disponibilidade. Certifique-se de que escolha a mesma família de tamanho para todas as máquinas virtuais no mesmo conjunto de disponibilidade. Alterar tamanho acedendo às definições de computação e rede da máquina virtual e, em seguida, repita a ativação pós-falha.
  
* Há uma política na subscrição que impede a criação de uma máquina virtual. Altere a política para permitir a criação de uma máquina virtual e, em seguida, repita a ativação pós-falha. 

## <a name="failover-failed-with-error-id-28092"></a>Falha na ativação pós-falha com o ID de erro 28092

Recuperação de site não conseguiu criar uma interface de rede para a ativação pós-falha da máquina virtual. Certifique-se de que tem quota suficiente disponível para criar as interfaces de rede na subscrição. Pode verificar a quota disponível acedendo a subscrição -> utilização + quotas. Pode abrir um [novo pedido de suporte](http://aka.ms/getazuresupport) para aumentar a quota. Se tiver uma quota suficiente, em seguida, esta situação pode ter um intermitente emitir, repita a operação. Se o problema persistir, mesmo após várias tentativas, em seguida, deixe um comentário no final deste documento.  

## <a name="failover-failed-with-error-id-70038"></a>Falha na ativação pós-falha com o ID de erro 70038

Recuperação de site não conseguiu criar uma máquina de virtual clássico no Azure de ativação pós-falha. Pode ocorrer porque:

* Um dos recursos, tais como uma rede virtual que é necessário para a máquina virtual a ser criado não existe. Criar a rede virtual como fornecido com as definições de computação e rede da máquina virtual ou modifique a definição a uma rede virtual que já existe e, em seguida, repita a ativação pós-falha. 


## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, em seguida, publique a consulta no [fórum de recuperação de Site](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou deixe um comentário no final deste documento. Temos uma Comunidade de Active Directory que deve ser capaz de ajudá-lo.