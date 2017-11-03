---
title: 'Faz com que a unidade d: de uma VM de um disco de dados | Microsoft Docs'
description: 'Descreve como alterar as letras de unidade para uma VM do Windows para que possa utilizar a unidade d: como uma unidade de dados.'
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: 7667175c01be2421bfc3badd83b1d8aaeb29bfde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilizar a unidade d: como uma unidade de dados numa VM do Windows
Se a aplicação tem de utilizar a unidade D para armazenar dados, siga estas instruções para utilizar uma letra de unidade diferente para o disco temporário. Nunca utilize o disco temporário para armazenar dados que precisa para manter.

Se redimensionar ou **paragem (Deallocate)** uma máquina virtual, isto pode acionar a colocação da máquina virtual para um hipervisor de novo. Um evento de manutenção planeada ou também pode acionar Esta colocação. Neste cenário, o disco temporário irá ser reatribuído para a primeira letra de unidade disponível. Se tiver uma aplicação que requer que a unidade d: especificamente, tem de seguir estes passos para temporariamente mover o pagefile.sys, anexar um disco de dados nova e atribua-a letra D e, em seguida, regresse a pagefile.sys na unidade temporária. Depois de concluído, Azure irá não reponha o d: se a VM é movido para um hipervisor diferentes.

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [compreender unidade temporária no Microsoft Virtual Machines do Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Anexar o disco de dados
Em primeiro lugar, terá de anexar o disco de dados para a máquina virtual. Para fazê-lo através do portal, consulte o artigo [como anexar um disco de dados geridos no portal do Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporariamente pagefile.sys a unidade C
1. Ligar à máquina virtual. 
2. Clique com botão direito do **iniciar** menu e selecione **sistema**.
3. No menu da esquerda, selecione **definições de sistema avançadas**.
4. No **desempenho** secção, selecione **definições**.
5. Selecione o **avançadas** separador.
6. No **Memória Virtual** secção, selecione **alteração**.
7. Selecione o **C** unidade e, em seguida, clique em **sistema gerido tamanho** e, em seguida, clique em **definir**.
8. Selecione o **D** unidade e, em seguida, clique em **nenhum ficheiro de paginação** e, em seguida, clique em **definir**.
9. Clique em aplicar. Irá receber um aviso se o computador tem de ser reiniciado para que as alterações surtam efeito.
10. Reinicie a máquina virtual.

## <a name="change-the-drive-letters"></a>Alterar as letras de unidade
1. Assim que a VM for reiniciado, inicie sessão novamente para a VM.
2. Clique em de **iniciar** menu e tipo **diskmgmt.msc** e prima Enter. Gestão de discos será iniciado.
3. Clique com o botão direito no **D**, a unidade de armazenamento temporário e selecione **letra de unidade de alteração e caminhos**.
4. Em letra de unidade, selecione uma nova unidade, tais como **T** e, em seguida, clique em **OK**. 
5. Faça duplo clique no disco de dados e selecione **letra de unidade de alteração e caminhos**.
6. Em letra de unidade, selecione a unidade **D** e, em seguida, clique em **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Mover pagefile.sys de volta para a unidade de armazenamento temporário
1. Clique com botão direito do **iniciar** menu e selecione **sistema**
2. No menu da esquerda, selecione **definições de sistema avançadas**.
3. No **desempenho** secção, selecione **definições**.
4. Selecione o **avançadas** separador.
5. No **Memória Virtual** secção, selecione **alteração**.
6. Selecione a unidade de SO **C** e clique em **nenhum ficheiro de paginação** e, em seguida, clique em **definir**.
7. Selecione a unidade de armazenamento temporário **T** e, em seguida, clique em **sistema gerido tamanho** e, em seguida, clique em **definir**.
8. Clique em **Aplicar**. Irá receber um aviso se o computador tem de ser reiniciado para que as alterações surtam efeito.
9. Reinicie a máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Pode aumentar o armazenamento disponível para a máquina virtual por [anexar um disco de dados adicionais](attach-managed-disk-portal.md).

