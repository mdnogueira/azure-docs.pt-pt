---
title: "Resolver um problema VM do Azure através da Virtualização aninhada no Azure | Microsoft Docs"
description: "Como resolver um problema de VM do Azure através da Virtualização aninhada no Azure"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/06/2017
ms.author: genli
ms.openlocfilehash: 35f52af5fbf0c945a766f5e5431c885d91df546a
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Resolver um problema VM do Azure através da Virtualização aninhada no Azure

Este artigo mostra como criar um ambiente de Virtualização aninhada no Microsoft Azure, pelo que pode montar o disco do problema VM no anfitrião Hyper-V (VM de recuperação) para fins de resolução de problemas.

## <a name="prerequisite"></a>Pré-requisito

Para montar o problema VM, a VM de recuperação tem de cumprir os pré-requisitos seguintes:

-   A VM de recuperação tem de ser a mesma localização que o problema VM.

-   A VM de recuperação tem de estar no mesmo grupo de recursos como o problema VM.

-   A VM de recuperação tem de utilizar o mesmo tipo de conta de armazenamento (Standard ou Premium) como o problema VM.

## <a name="step-1-create-a-recovery-vm-and-install-hyper-v-role"></a>Passo 1: Criar uma VM de recuperação e instalar a função Hyper-V

1.  Crie uma nova VM de recuperação:

    -  Sistema operativo: Windows Server 2016 Datacenter

    -  Tamanho: Quaisquer série V3 com, pelo menos, dois núcleos virtualização que suporte aninhado. Para obter mais informações, consulte [introduzir novos tamanhos de Dv3 e Ev3 VM](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesma localização, conta de armazenamento e grupo de recursos como o problema VM.

    -  Selecione o mesmo tipo de armazenamento como o problema VM (Standard ou Premium).

2.  Depois da VM de recuperação é criado, ambiente de trabalho remoto para a VM de recuperação.

3.  No Gestor de servidor, selecione **gerir** > **para adicionar funções e funcionalidades**.

4.  No **tipo de instalação** secção, selecione **instalação baseada em funções ou baseada em funcionalidade**.

5.  No **servidor de destino selecione** secção, certifique-se de que a VM de recuperação está selecionada.

6.  Selecione o **função Hyper-V** > **adicionar funcionalidades**.

7.  Selecione **seguinte** no **funcionalidades** secção.

8.  Se um comutador virtual não estiver disponível, selecione-a. Caso contrário, selecione **seguinte**.

9.  No **migração** secção, selecione **seguinte**

10. No **arquivos predefinidos** secção, selecione **seguinte**.

11. Selecione a caixa reiniciar automaticamente o servidor, se necessário.

12. Selecione **instalar**.

13. Permitir que o servidor instalar a função Hyper-V. Esta ação demora alguns minutos e o servidor será reiniciado automaticamente.

## <a name="step-2-create-the-problem-vm-on-the-recovery-vms-hyper-v-server"></a>Passo 2: Criar o problema VM no servidor de Hyper-V a VM de recuperação

1.  Registe o nome do disco no problema VM e, em seguida, elimine o problema VM. Certifique-se de que mantém anexados todos os discos. 

2.  Anexe o disco de SO do seu problema VM como um disco de dados da VM de recuperação.

    1.  Depois do problema que VM é eliminada, vá para a VM de recuperação.

    2.  Selecione **discos**e, em seguida, **adicionar disco de dados**.

    3.  Selecione o disco da VM de problema e, em seguida, selecione **guardar**.

3.  Depois do disco tem com êxito ligado ao ambiente de trabalho remoto a VM de recuperação.

4.  Abra a gestão de discos (diskmgmt.msc). Certifique-se de que o disco do problema VM está definida como **Offline**.

5.  Abra o Gestor de Hyper-V: No **Gestor de servidor**, selecione o **função Hyper-V**. O servidor com o botão direito e, em seguida, selecione o **Gestor de Hyper-V**.

6.  No Gestor de Hyper-V, clique com o botão direito a VM de recuperação e, em seguida, selecione **novo** > **Máquina Virtual** > **seguinte**.

7.  Escreva um nome para a VM e, em seguida, selecione **seguinte**.

8.  Selecione **geração 1**.

9.  Defina a memória de arranque ao 1024 MB ou mais.

10. Se aplicável, selecione o comutador de rede do Hyper-V que foi criada. Caso contrário mova para a página seguinte.

11. Selecione **anexar um disco rígido virtual mais tarde**.

    ![a imagem sobre a anexação de uma opção posterior disco rígido Virtual](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Selecione **concluir** quando é criada a VM.

13. Clique com o botão direito a VM que criou e, em seguida, selecione **definições**.

14. Selecione **controlador IDE 0**, selecione **rígido**e, em seguida, clique em **adicionar**.

    ![a imagem sobre adiciona o novo disco rígido](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. No **físicas de disco rígido**, selecione o disco do problema VM ligado à VM do Azure. Se não vir quaisquer discos listados, verifique se o disco estiver definido como offline utilizando a gestão de discos.

    ![a imagem sobre monta o disco](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Selecione **aplicar**e, em seguida, selecione **OK**.

18. Faça duplo clique na VM e, em seguida, inicie-o.

19. Agora pode trabalhar na VM da VM no local. Pode seguir quaisquer passos de resolução de problemas que precisar.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Passo 3: Recrie a VM do Azure no Azure

1.  Depois de obter a VM novamente online, encerre a VM com o Gestor de Hyper-V.

2.  Vá para o [portal do Azure](https://portal.azure.com) e selecione a VM de recuperação > discos, copie o nome do disco. Irá utilizar o nome no próximo passo. Desligar o disco fixo de VM de recuperação.

3.  Aceda a **todos os recursos**, procure o nome do disco e, em seguida, selecione o disco.

     ![a imagem sobre procura o disco](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Clique em **criar VM**.

     ![a imagem sobre cria vm a partir do disco](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Também pode utilizar o Azure PowerShell para criar a VM a partir do disco. Para obter mais informações, consulte [criar a nova VM a partir de um disco existente utilizando o PowerShell](create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Passos seguintes

Se estiver a ter problemas em ligar à VM, consulte [nas ligações RDP de resolver para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com a aceder a aplicações em execução numa VM, consulte [resolver problemas de conectividade de aplicação numa Windows VM](troubleshoot-app-connection.md).
