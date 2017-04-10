---
title: Utilizar o Outlook no Azure RemoteApp | Microsoft Docs
description: Saiba como configurar e utilizar o Outlook no Azure RemoteApp | Microsoft Azure
services: remoteapp
documentationcenter: 
author: pavithir
manager: mbaldwin
ms.assetid: cb2a498f-9539-4522-a874-542114926a61
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a6d4fbdf0e552f50673092183e893841ec0c5aa4
ms.lasthandoff: 03/31/2017


---
# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Utilizar o Microsoft Outlook no Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp vai ser descontinuado a 31 de agosto de 2017. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

O Azure RemoteApp suporta o Microsoft Outlook O365. Leia mais sobre como o [Office funciona no Azure RemoteApp](remoteapp-officesubscription.md). Existem algumas definições recomendadas para o Outlook quando utilizado no Azure RemoteApp.

## <a name="cached-mode"></a>Modo de cache
O modo de cache é uma configuração recomendada na utilização do Outlook no Azure RemoteApp. Quando configura uma conta do Outlook 2013 para utilizar o Modo Exchange em Cache, o Outlook 2013 funciona a partir de uma cópia local da caixa de correio do Microsoft Exchange do utilizador que está armazenada num ficheiro de dados offline (ficheiro OST) no computador do utilizador, juntamente com o Livro de Endereços Offline (OAB). A caixa de correio em cache e o livro de endereços offline são atualizados periodicamente a partir do serviço do O365. Leia mais sobre [as diferenças entre o modo de cache e o modo online](https://technet.microsoft.com/library/jj683103.aspx).

O utilizador pode selecionar o **Modo Exchange em Cache** ou o **Modo Online** durante a configuração da conta ou alterando as definições de conta. Pode também implementar um ou outro modo através da Ferramenta de Personalização do Office (FPO) ou da Política de Grupo.  

Leia as [instruções passo-a-passo sobre a ativação do modo de cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Pesquisa
No Azure RemoteApp, a utilização da pesquisa no Outlook tem limitações. O Azure RemoteApp utiliza VMs agrupadas para acomodar sessões de utilizador. A indexação da pesquisa depende do ID da máquina, que é distinto para diferentes VMs. É possível que sempre que um utilizador inicia sessão no Azure RemoteApp, seja direcionado para uma nova VM. Tal significa que, se a pesquisa local for ativada, o indexador será executado sempre que o ID da máquina se alterar (quando o utilizador está numa VM diferente). Consoante o tamanho do ficheiro .OST, o indexador pode levar tempo a concluir e a utilizar os recursos necessários para outras aplicações. A pesquisa não só seria lenta, mas também poderia não produzir resultados. Utilizar um perfil de conta do Modo Online permite contornar esta situação, mas o desempenho global seria afetado negativamente devido à falta de uma cache local (veja a ligação acima para obter mais informações sobre a diferença entre o modo em cache e online). Infelizmente, não é possível desativar a pesquisa indexada/local e a pesquisa online não pode ser ativada por predefinição no Outlook 2013.


