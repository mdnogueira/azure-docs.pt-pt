---
title: "Série N controlador a configuração do Azure para Windows | Microsoft Docs"
description: "Como configurar os controladores de NVIDIA GPU para VMs N série com o Windows no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/07/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b480d10df777a2757c073ff77e1845d33d63163a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configurar controladores GPU para VMs N série com o Windows Server
Para tirar partido das capacidades de GPU de VMs de série N do Azure com o Windows Server 2016 ou o Windows Server 2012 R2, instale controladores de gráficos NVIDIA suportados. Este artigo fornece os passos de configuração de controlador depois de implementar uma VM de série N. As informações de configuração do controlador também estão disponíveis para [VMs com Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para especificações básicas, as capacidades de armazenamento e detalhes do disco, consulte [tamanhos de VM do Windows para a GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]



## <a name="driver-installation"></a>Instalação de controlador

1. Estabelecer ligação ao ambiente de trabalho remoto para cada VM de série N.

2. Transferir, extraia e instalar o controlador suportado para o sistema operativo Windows.

Em VMs do Azure NV, é necessário um reinício após a instalação de controlador. Em VMs do NC, não é necessário um reinício.

## <a name="verify-driver-installation"></a>Certifique-se a instalação de controlador

Pode verificar a instalação de controladores no Gestor de dispositivos. O exemplo seguinte mostra a configuração com êxito do cartão Tesla K80 numa VM de NC do Azure.

![Propriedades do controlador GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo para a GPU, execute o [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o controlador o utilitário da linha de comandos.

1. Abra uma linha de comandos e mude para o **c:\Programas\Microsoft Files\NVIDIA Corporation\NVSMI** diretório.

2. Executar **nvidia smi**. Se o controlador estiver instalado, verá o resultado semelhante abaixo. Tenha em atenção que **GPU Util** mostra **0%** , a menos que estiver a executar uma carga de trabalho para a GPU na VM.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Rede RDMA para NC24r VMs

Conectividade de rede RDMA, pode ser ativada em VMs NC24r implementadas no mesmo conjunto de disponibilidade. Tem de adicionar a extensão de HpcVmDrivers para instalar controladores de dispositivos de rede do Windows que ativar a conetividade RDMA. Para adicionar a extensão VM para uma VM NC24r, utilize [Azure PowerShell](/powershell/azure/overview) cmdlets do Azure Resource Manager.

> [!NOTE]
> Atualmente, apenas o Windows Server 2012 R2 suporta a rede RDMA em NC24r VMs.
> 

Para instalar a versão mais recente 1.1 HpcVMDrivers extensão numa VM com capacidade RDMA existente com o nome myVM na região EUA oeste:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [extensões de Máquina Virtual e funcionalidades para o Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A rede RDMA suporta tráfego da Interface de passagem de mensagens (MPI) para aplicações em execução com [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) ou Intel MPI 5. x. 


## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o GPUs NVIDIA nas VMs N série, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs do Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs do Azure NV)

* Os programadores criar aplicações para a GPU-acelerados para GPUs de Tesla NVIDIA também podem transferir e instalar a 8 do Toolkit de CUDA para [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) ou [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Para obter mais informações, consulte o [guia de instalação CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


