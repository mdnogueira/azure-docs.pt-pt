---
title: Anexar um disco de dados geridos para uma VM do Windows - Azure | Microsoft Docs
description: "Como ligar o novo disco de dados geridos para uma VM do Windows no portal do Azure utilizando o modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.openlocfilehash: f0cf88a06c5470ef173b22e7213419a6c8760723
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Como anexar um disco de dados geridos para uma VM do Windows no portal do Azure

Este artigo mostra como anexar um novo disco de dados geridos para máquinas virtuais do Windows através do portal do Azure. Antes de fazer isto, consulte estas sugestões:

* O tamanho da máquina virtual controla quantos discos de dados, pode anexar. Para obter mais informações, consulte [tamanhos das virtual machines](sizes.md).
* Para um novo disco, não precisa de criar primeiro, porque o Azure cria-lo quando a ligá-lo.

Também pode [anexar um disco de dados com o Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Adicionar um disco de dados
1. No menu à esquerda, clique em **máquinas virtuais**.
2. Selecione a máquina virtual na lista.
3. No painel da máquina virtual, clique em **discos**.
   4. No **discos** painel, clique em **+ adicionar disco de dados**.
5. Na lista pendente para o novo disco, selecione **criar vazio**.
6. No **criar disco de gerido** painel, escreva um nome para o disco e ajustar as outras definições conforme necessário. Quando tiver terminado, clique em **criar**.
7. No **discos** painel, clique em Guardar para guardar a nova configuração de disco para a VM.
6. Depois do Azure cria o disco e anexa-lo para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Ligar à VM.
1. Clique no menu Iniciar no interior da VM e tipo **diskmgmt.msc** e acessos **Enter**. Esta ação iniciará o snap-in Gestão de discos.
2. Gestão de discos irá reconhece que tem um disco novo, não foi inicializado e irá aparecer a janela de inicializar o disco.
3. Certifique-se o novo disco está selecionado e clique em **OK** ao inicializá-lo.
4. O novo disco será agora apresentado como **não alocado**. Clique com botão direito em qualquer local no disco e selecione **novo volume simples**. O **Assistente de novo Volume simples** será iniciado.
5. Avance no assistente, ao manter todas as predefinições, quando tiver terminado selecione **concluir**.
6. Feche a gestão de discos.
7. Irá receber um pop-up que precisa para formatar o disco novo antes de poder utilizar. Clique em **disco formato**.
8. No **disco novo formato** caixa de diálogo, verifique as definições e, em seguida, clique em **iniciar**.
9. Receberá um aviso a informar que os discos de formatação serão apagar todos os dados, clique em **OK**.
10. Quando o formato estiver concluído, clique em **OK**.

## <a name="use-trim-with-standard-storage"></a>Utilizar operações de COMPACTAÇÃO com o armazenamento standard

Se utilizar o armazenamento standard (HDD), deverá ativar a limitação. Operações de COMPACTAÇÃO elimina os blocos no disco, é-lhe faturado apenas para o armazenamento que está a utilizar, na verdade, de modo. Isto permite poupar nos custos se criar ficheiros grandes e, em seguida, elimine-os. 

Pode executar este comando para verificar a definição de COMPACTAÇÃO. Abra uma linha de comandos na sua VM do Windows e o tipo:

```
fsutil behavior query DisableDeleteNotify
```

Se o comando devolve 0, cortar está ativada corretamente. Se devolve 1, execute o seguinte comando para ativar a limitação:
```
fsutil behavior set DisableDeleteNotify 0
```

Depois de eliminar dados do seu disco, pode certificar-se das operações de corte libertação corretamente executando desfragmentação com operações de COMPACTAÇÃO:

```
defrag.exe <volume:> -l
```

Pode também Certifique-se de que todo o volume é cortado por formatação do volume.

## <a name="next-steps"></a>Passos seguintes
Se a aplicação tem de utilizar o d: disco para armazenar dados, pode [alterar a letra de unidade de disco temporário Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
