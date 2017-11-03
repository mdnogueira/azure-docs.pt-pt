---
title: "Tamanhos de máquina virtual para serviços em nuvem do Azure | Microsoft Docs"
description: "Lista os tamanhos de outra máquina virtual (e os IDs) para funções de web e de trabalho do serviço de nuvem do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 1ba56eb9539a4295fdaaab523cfd2a7e1587ef54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sizes-for-cloud-services"></a>Tamanhos de serviços Cloud
Este tópico descreve os tamanhos disponíveis e as opções para instâncias de função do serviço em nuvem (funções da web e funções de trabalho). Também fornece considerações de implementação a ter em consideração quando planear utilizar estes recursos. Cada tamanho tem um ID que colocar no seu [ficheiro de definição de serviço](cloud-services-model-and-package.md#csdef). Os preços para cada tamanho estão disponíveis no [preços de serviços em nuvem](https://azure.microsoft.com/pricing/details/cloud-services/) página.

> [!NOTE]
> Para ver os limites do Azure relacionados, consulte [subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamanhos de instâncias de função da web e de trabalho
Existem vários tamanhos standard à escolha no Azure. As considerações para alguns destes tamanhos incluem:

* As VMs da série D foram concebidos para executar aplicações que exigem um maior desempenho de poder de computação e disco temporário. As VMs da série D fornecem processadores mais rápidos, um rácio de memória para núcleo superior e uma unidade de estado sólida (SSD) para o disco temporário. Para obter detalhes, consulte o anúncio no blogue do Azure, [Novos Tamanhos de Máquinas Virtuais da Série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* A série DV2, o seguimento da série D original, apresenta uma CPU mais poderosa. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se no processador de última geração de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell), podendo chegar aos 3,1 GHz com o Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.
* As VMs da série G oferecem a maior capacidade de memória e são executadas em anfitriões com processadores da família Intel Xeon E5 V3.
* As VMs de uma série podem ser implementadas em vários tipos de hardware e processadores. O tamanho é limitado, com base no hardware, para oferecer o desempenho do processador consistente para a instância em execução, independentemente do hardware que é implementado no. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual.
* O tamanho A0 está sobre-subscrito no hardware físico. Apenas para este tamanho específico, outras implementações de cliente podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é indicado abaixo como a linha de base esperada, sujeito a uma variabilidade aproximada de 15%.

O tamanho da máquina virtual afeta o preço. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados em separado com base nas páginas utilizadas na conta de armazenamento. Para obter mais informações, consulte [detalhes de preços de serviços em nuvem](https://azure.microsoft.com/pricing/details/cloud-services/) e [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/).

As considerações seguintes podem ajudar a decidir um tamanho:

* Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obter informações detalhadas e considerações sobre como utilizar estas tamanhos, consulte [tamanhos de VM de computação de elevado desempenho](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Série Dv2, série D G-série, são ideais para aplicações que exigem CPUs mais rápidas, local um melhor desempenho do disco ou ter exigências de memória superiores. Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.
* Alguns dos anfitriões físicos nos centros de dados do Azure podem não suportar tamanhos de máquina virtual grandes, tais como A5 – A11. Como resultado, poderá ver a mensagem de erro **não conseguiu configurar a máquina virtual {nome}** ou **Falha ao criar a máquina virtual {nome}** quando o redimensionamento de uma máquina virtual existente para um novo tamanho; criar uma nova máquina virtual numa rede virtual criada antes de 16 de Abril de 2013; ou adicionar uma nova máquina virtual para um serviço em nuvem existente. Consulte [erro: "Falha ao configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte para soluções para cada cenário de implementação.
* A sua subscrição também pode limitar o número de núcleos que pode implementar em determinadas famílias de tamanhos. Para aumentar uma quota, contacte o Suporte do Azure.

## <a name="performance-considerations"></a>Considerações de desempenho
Foi criado o conceito do Azure computação unidade (ACU) para fornecer uma forma de comparar o desempenho de computação (CPU) em todos os SKUs do Azure e tem de identificar que SKU é mais provável satisfazer o desempenho.  A ACU está atualmente normalizada numa VM Pequena (Standard_A1) de 100 e todos os outros SKUs representam aproximadamente a velocidade máxima a que esse SKU consegue executar um teste de desempenho padrão.

> [!IMPORTANT]
> A ACU é apenas uma orientação. Os resultados da sua carga de trabalho podem variar.
>
>

<br>

| Família de SKU | ACU/Núcleo |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Breve ExtraLarge](#a-series) |100 |
| [A5 7](#a-series) |100 |
| [Standard_A1-8v2](#av2-series) |100 |
| [Standard_A2m-8mv2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [G1-5](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

As ACUs marcadas com um * utilizam a tecnologia Intel® Turbo para aumentar a frequência de CPU e proporcionar um aumento do desempenho. A quantidade do aumento pode variar consoante o tamanho da VM, a carga de trabalho e outras cargas de trabalho em execução no mesmo anfitrião.

## <a name="size-tables"></a>Tabelas de tamanhos
As tabelas seguintes mostram os tamanhos e as capacidades que fornecem.

* A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3), não se esqueça de que os números de capacidade especificados em GiB podem aparecer mais pequenos. Por exemplo, 1023 GiB = 1098,4 GB
* O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
* Os discos de dados podem operar nos modos em cache ou não colocado em cache. Para uma operação do disco de dados em cache, o modo de cache do anfitrião está definido como **ReadOnly** ou **ReadWrite**. Para uma operação do disco de dados não colocada em cache, o modo de cache do anfitrião está definido como **None**.
* A largura de banda máxima da rede é a largura de banda máxima agregada atribuída por tipo de VM. A largura de banda máxima fornece orientações para selecionar o tipo de VM certo para assegurar que está disponível a capacidade de rede adequada. Mover entre baixa, moderado, alta e muito elevado, o débito aumenta em conformidade. O desempenho de rede real irá depender de vários fatores, incluindo cargas da rede e das aplicações, bem como das definições de rede das aplicações.

## <a name="a-series"></a>Série A
| Tamanho            | Núcleos de CPU | Memória: GiB  | HDD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0.768        | 20                   | 1/baixo |
| Pequeno           | 1         | 1.75         | 225                  | 1 / moderado |
| Médio          | 2         | 3,5 GB       | 490                  | 1 / moderado |
| Grande           | 4         | 7            | 1000                 | 2 / alto |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / alto |
| A5              | 2         | 14           | 490                  | 1 / moderado |
| A6              | 4         | 28           | 1000                 | 2 / alto |
| A7              | 8         | 56           | 2040                 | 4 / alto |

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva
Para informações e considerações sobre como utilizar estas tamanhos, consulte [tamanhos de VM de computação de elevado desempenho](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho            | Núcleos de CPU | Memória: GiB  | HDD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8 *             |8          | 56           | 1817                 | 2 / alto |
| A9 *             |16         | 112          | 1817                 | 4 / muito alto |
| A10             |8          | 56           | 1817                 | 2 / alto |
| A11             |16         | 112          | 1817                 | 4 / muito alto |

\*Com capacidade RDMA

## <a name="av2-series"></a>Série Av2

| Tamanho            | Núcleos de CPU | Memória: GiB  | SSD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / moderado                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/moderado                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / alto                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / alto                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/moderado                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / alto                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / alto                     |


## <a name="d-series"></a>Série D
| Tamanho            | Núcleos de CPU | Memória: GiB  | SSD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1 / moderado |
| Standard_D2     | 2         | 7            | 100                  | 2 / alto |
| Standard_D3     | 4         | 14           | 200                  | 4 / alto |
| Standard_D4     | 8         | 28           | 400                  | 8 / alto |
| Standard_D11    | 2         | 14           | 100                  | 2 / alto |
| Standard_D12    | 4         | 28           | 200                  | 4 / alto |
| Standard_D13    | 8         | 56           | 400                  | 8 / alto |
| Standard_D14    | 16        | 112          | 800                  | 8 / muito alto |

## <a name="dv2-series"></a>Série Dv2
| Tamanho            | Núcleos de CPU | Memória: GiB  | SSD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1 / moderado |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / alto |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / alto |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / alto |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / extremamente alto |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / alto |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / alto |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / alto |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / extremamente alto |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8 / extremamente alto |

## <a name="g-series"></a>Série G
| Tamanho            | Núcleos de CPU | Memória: GiB  | SSD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/alto |
| Standard_G2     | 4         | 56           | 768                  |2 / alto |
| Standard_G3     | 8         | 112          | 1,536                |4 / muito alto |
| Standard_G4     | 16        | 224          | 3,072                |8 / extremamente alto |
| Standard_G5     | 32        | 448          | 6,144                |8 / extremamente alto |

## <a name="h-series"></a>Série H
As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho destinadas a necessidades computacionais avançadas, como modelagem molecular e dinâmicas de fluídos computacionais. Estes 8 e 16 núcleos VMs são criadas na tecnologia de processador Intel Haswell E5-2667 V3 com DDR4 memória e armazenamento baseadas em SSD local.

Além do poder de CPU substancial, a série H oferece diversas opções para funcionamento em rede RDMA de baixa latência através de FDR InfiniBand e várias configurações de memória para suportar requisitos computacionais de memória intensiva.

| Tamanho            | Núcleos de CPU | Memória: GiB  | SSD Local: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / alto |
| Standard_H16    | 16        | 112          | 2000                 | 8 / muito alto |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / alto |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / muito alto |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / muito alto |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / muito alto |

\*Com capacidade RDMA

## <a name="configure-sizes-for-cloud-services"></a>Configurar os tamanhos de serviços Cloud
Pode especificar o tamanho de Máquina Virtual de uma instância de função como parte do modelo de serviço descrito através de [ficheiro de definição de serviço](cloud-services-model-and-package.md#csdef). O tamanho da função determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de ficheiros local que está atribuído a uma instância em execução. Escolha o tamanho da função com base nos requisitos de recursos da sua aplicação.

Eis um exemplo para definir o tamanho de função ser [Standard_D2](#general-purpose-d) para uma instância de função da Web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Alterar o tamanho de uma função existente

Como da natureza das suas alterações de carga de trabalho ou novos tamanhos VM fique disponíveis, poderá pretender alterar o tamanho da sua função. Para tal, tem de alterar o tamanho da VM no seu ficheiro de definição de serviço (conforme mostrado acima), Empacote novamente esta o serviço de nuvem e implementá-la. Não é possível alterar os tamanhos de VM diretamente a partir do portal ou PowerShell.

>[!TIP]
> Pode querer utilizar diferentes tamanhos de VM para a função em ambientes diferentes (ex. testar a produção vs). Uma forma para fazer isto é criar várias definições de serviço (. csdef) ficheiros no seu projeto, em seguida, criar nuvem diferentes pacotes de serviços por ambiente durante a compilação automatizada utilizando a ferramenta de CSPack. Para saber mais sobre os elementos de um pacote de serviços de nuvem e como criá-los, consulte [a nuvem de novidades dos serviços de modelo e como pacote-lo?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Obter uma lista de tamanhos
Pode utilizar o PowerShell ou a API REST para obter uma lista de tamanhos. A API de REST está documentada [aqui](https://msdn.microsoft.com/library/azure/dn469422.aspx). O código seguinte é um comando do PowerShell que irá listar todos os tamanhos atualmente disponíveis para o seu serviço de nuvem.

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md).
* Saiba mais [sobre elevado desempenho tamanhos de VM de computação](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para cargas de trabalho HPC.
