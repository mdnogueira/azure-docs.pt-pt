


Existem vários tamanhos standard à escolha no Azure. As considerações para alguns destes tamanhos incluem:

* As VMs da série D foram concebidos para executar aplicações que exigem um maior desempenho de poder de computação e disco temporário. As VMs da série D fornecem processadores mais rápidos, um rácio de memória para núcleo superior e uma unidade de estado sólida (SSD) para o disco temporário. Para obter detalhes, consulte o anúncio no blogue do Azure, [Novos Tamanhos de Máquinas Virtuais da Série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* A série DV2, o seguimento da série D original, apresenta uma CPU mais poderosa. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se no processador de última geração de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell), podendo chegar aos 3,1 GHz com o Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.
* A série F tem por base o processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, o qual pode atingir velocidades de relógio de 3,1 GHz com o Intel Turbo Boost Technology 2.0. Este é o mesmo desempenho de CPU da série Dv2 de VMs.  A um preço de tabela por hora mais baixo, a série F é o melhor valor de desempenho por preço no portfólio do Azure com base na Unidade de Computação do Azure (ACU) por núcleo. 
  
    A série F também apresenta uma nova norma de nomenclatura de tamanho de VM para o Azure. Para esta série e tamanhos de VM lançados no futuro, o valor numérico depois da letra do nome de família irá corresponder ao número de núcleos de CPU. As capacidades adicionais, tais como otimizada para armazenamento premium, irão ser designadas pelo letras a seguir ao número de núcleos de CPU. Este formato de nomenclatura será utilizado para futuros tamanhos de VM lançados, mas não irá alterar retroativamente os nomes de quaisquer tamanhos de VM existentes que tenham sido lançados.
* As VMs da série G oferecem a maior capacidade de memória e são executadas em anfitriões com processadores da família Intel Xeon E5 V3.
* As VMs das séries DS, DSv2, Fs e GS podem utilizar o Armazenamento Premium, o qual fornece armazenamento de alto desempenho e de baixa latência para cargas de trabalho de E/S intensivas. Estas VMs utilizam unidades de estado sólido (SSDs) para alojar os discos de uma máquina virtual e também fornecem uma cache de disco SSD local. O Armazenamento Premium está disponível em determinadas regiões. Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).
*   As VMs das séries A e Av2 podem ser implementados em vários tipos de hardware e processadores. O tamanho é limitado com base no hardware de forma a proporcionar um desempenho de processador consistente para a instância em execução, independentemente do hardware no qual está implementado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual.
* O tamanho A0 está sobre-subscrito no hardware físico. Apenas para este tamanho específico, outras implementações de cliente podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é indicado abaixo como a linha de base esperada, sujeito a uma variabilidade aproximada de 15%.

O tamanho da máquina virtual afeta o preço. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados em separado com base nas páginas utilizadas na conta de armazenamento. Para obter detalhes, veja [Detalhes dos Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

As considerações seguintes podem ajudar a decidir um tamanho:

* Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obter informações detalhadas e considerações sobre como utilizar estes tamanhos, veja [Acerca de VMs da série H e da série A de computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* As séries Dv2, D, G e as variantes DS/GS são ideais para aplicações que exigem CPUs mais rápidas, melhor desempenho de disco local ou memórias com mais capacidade.  Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.
* As VMs da série F são uma excelente opção para cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de mais memória ou SSD local por núcleo de CPU.  As cargas de trabalho, como análise, servidores de jogos, servidores Web e processamento de lotes, beneficiarão do valor da série F.
* Alguns dos anfitriões físicos nos centros de dados do Azure podem não suportar tamanhos de máquina virtual grandes, tais como A5 – A11. Como resultado, poderá ver a mensagem de erro **Falha ao configurar a máquina virtual<machine name> ** ou **Falha ao criar a máquina virtual<machine name> ** quando redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual numa rede virtual criada antes de 16 de Abril de 2013 ou adicionar uma nova máquina virtual a um serviço na cloud existente. Veja [Erro: "Falha ao configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte para obter soluções para cada cenário de implementação.  
* A sua subscrição também pode limitar o número de núcleos que pode implementar em determinadas famílias de tamanhos. Para aumentar uma quota, contacte o Suporte do Azure.

## <a name="performance-considerations"></a>Considerações de desempenho
Criámos o conceito de Unidade de Computação do Azure (ACU) para fornecer uma forma de comparação do desempenho de computação (CPU) nos SKUs do Azure. Isto ajudará a identificar facilmente que SKU é mais provável de satisfazer as suas necessidades de desempenho.  A ACU está atualmente normalizada numa VM Pequena (Standard_A1) de 100 e todos os outros SKUs representam aproximadamente a velocidade máxima a que esse SKU consegue executar um teste de desempenho padrão. 

> [!IMPORTANT]
> A ACU é apenas uma orientação.  Os resultados da sua carga de trabalho podem variar. 
> 
> 

<br>

| Família de SKU | ACU/Núcleo |
| --- | --- |
| [A0](#a-series) |50 |
| [A1-A4](#a-series) |100 |
| [A5-A7](#a-series) |100 |
| [A1_v2-A8_v2](#av2-series) |100 |
| [A2m_v2-A8m_v2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-D14](#d-series) |160 |
| [D1_v2-D15_v2](#dv2-series) |210 - 250* |
| [DS1-DS14](#ds-series) |160 |
| [DS1_v2-DS15_v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1-G5](#g-series) |180 - 240* |
| [GS1-GS5](#gs-series) |180 - 240* |
| [H](#h-series) |290 - 300* |
| [L4s-L32s](#l-series) |180 - 240* |

As ACUs marcadas com um * utilizam a tecnologia Intel® Turbo para aumentar a frequência de CPU e proporcionar um aumento do desempenho.  A quantidade do aumento pode variar consoante o tamanho da VM, a carga de trabalho e outras cargas de trabalho em execução no mesmo anfitrião.

## <a name="size-tables"></a>Tabelas de tamanhos
As tabelas seguintes mostram os tamanhos e as capacidades que fornecem.

* A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3), não se esqueça de que os números de capacidade especificados em GiB podem aparecer mais pequenos. Por exemplo, 1023 GiB = 1098,4 GB
* O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
* Os discos de dados podem operar nos modos em cache ou não colocado em cache.  Para uma operação do disco de dados em cache, o modo de cache do anfitrião está definido como **ReadOnly** ou **ReadWrite**.  Para uma operação do disco de dados não colocada em cache, o modo de cache do anfitrião está definido como **None**.
* A largura de banda máxima da rede é a largura de banda máxima agregada atribuída por tipo de VM. A largura de banda máxima fornece orientações para selecionar o tipo de VM certo para assegurar que está disponível a capacidade de rede adequada. Ao mover entre Baixo, Moderado, Alto e Muito Alto, o débito irá aumentar em conformidade. O desempenho de rede real irá depender de vários fatores, incluindo cargas da rede e das aplicações, bem como das definições de rede das aplicações.

## <a name="a-series"></a>Série A
| Tamanho | Núcleos de CPU | Memória: GiB | HDD Local: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0.768 |20 |1 |1x500 |1/baixo |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |1 / moderado |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |1 / moderado |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / alto |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / alto |
| Standard_A5 |2 |14 |135 |4 |4x500 |1 / moderado |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / alto |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / alto |

<br>

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva
Para obter informações e considerações sobre como utilizar estes tamanhos, veja [Acerca de VMs da série H e da série A de computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho | Núcleos de CPU | Memória: GiB | HDD Local: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2 / alto |
| Standard_A9* |16 |112 |382 |16 |16x500 |4 / muito alto |
| Standard_A10 |8 |56 |382 |16 |16x500 |2 / alto |
| Standard_A11 |16 |112 |382 |16 |16x500 |4 / muito alto |

*Com capacidade RDMA

<br>

## <a name="av2-series"></a>Série Av2

| Tamanho        | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs/Largura de banda da rede máximos |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A1_v2 | 1         | 2            | 10                   | 2              | 2x500              | 1 / moderado              |
| Standard_A2_v2 | 2         | 4            | 20                   | 4              | 4x500              | 2/moderado              |
| Standard_A4_v2 | 4         | 8            | 40                   | 8              | 8x500              | 4 / alto                  |
| Standard_A8_v2 | 8         | 16           | 80                   | 16             | 16x500             | 8 / alto                  |
| Standard_A2m_v2 | 2        | 16           | 20                   | 4              | 4x500              | 2/moderado              |
| Standard_A4m_v2 | 4        | 32           | 40                   | 8              | 8x500              | 4 / alto                  |
| Standard_A8m_v2 | 8        | 64           | 80                   | 16             | 16x500             | 8 / alto                  |


## <a name="d-series"></a>Série D
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 |1 |3.5 |50 |2 |2x500 |1 / moderado |
| Standard_D2 |2 |7 |100 |4 |4x500 |2 / alto |
| Standard_D3 |4 |14 |200 |8 |8x500 |4 / alto |
| Standard_D4 |8 |28 |400 |16 |16x500 |8 / alto |
| Standard_D11 |2 |14 |100 |4 |4x500 |2 / alto |
| Standard_D12 |4 |28 |200 |8 |8x500 |4 / alto |
| Standard_D13 |8 |56 |400 |16 |16x500 |8 / alto |
| Standard_D14 |16 |112 |800 |32 |32x500 |8 / muito alto |

<br>

## <a name="dv2-series"></a>Série Dv2
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3.5 |50 |2 |2x500 |1 / moderado |
| Standard_D2_v2 |2 |7 |100 |4 |4x500 |2 / alto |
| Standard_D3_v2 |4 |14 |200 |8 |8x500 |4 / alto |
| Standard_D4_v2 |8 |28 |400 |16 |16x500 |8 / alto |
| Standard_D5_v2 |16 |56 |800 |32 |32x500 |8 / extremamente alto |
| Standard_D11_v2 |2 |14 |100 |4 |4x500 |2 / alto |
| Standard_D12_v2 |4 |28 |200 |8 |8x500 |4 / alto |
| Standard_D13_v2 |8 |56 |400 |16 |16x500 |8 / alto |
| Standard_D14_v2 |16 |112 |800 |32 |32x500 |8 / extremamente alto |
| Standard_D15_v2** |20 |140 |1,000 |40 |40x500 |8/extremamente alto* |

*Em algumas regiões, estão disponíveis redes aceleradas para o tamanho Standard_D15_v2. Para obter mais informações sobre a utilização e a disponibilidade, veja [Redes Aceleradas em Pré-visualização](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) e [Redes Aceleradas para uma máquina virtual](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

**A instância está isolada do hardware dedicado de um único cliente.

<br>

## <a name="ds-series"></a>Série DS*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 32 |1 / moderado |
| Standard_DS2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 64 |2 / alto |
| Standard_DS3 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 128 |4 / alto |
| Standard_DS4 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 256 |8 / alto |
| Standard_DS11 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 64 |2 / alto |
| Standard_DS12 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 128 |4 / alto |
| Standard_DS13 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 256 |8 / alto |
| Standard_DS14 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 512 |8 / muito alto |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série DS pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s).  Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="dsv2-series"></a>Série DSv2*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 48 |1/moderado |
| Standard_DS2_v2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 96 |2 alto |
| Standard_DS3_v2 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 192 |4 alto |
| Standard_DS4_v2 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 384 |8 alto |
| Standard_DS5_v2 |16 |56 |112 |32 |64,000 / 512 (688) |51,200 / 768 |8 extremamente alto |
| Standard_DS11_v2 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 96 |2 alto |
| Standard_DS12_v2 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 192 |4 alto |
| Standard_DS13_v2 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 384 |8 alto |
| Standard_DS14_v2 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 768 |8 extremamente alto |
| Standard_DS15_v2*** |20 |140 |280 |40 |80,000 / 640 (720) |64,000 / 960 |8/extremamente alto** |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série DSv2 pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s).  Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

**Em algumas regiões, estão disponíveis redes aceleradas para o tamanho Standard_DS15_v2. Para obter mais informações sobre a utilização e a disponibilidade, veja [Redes Aceleradas em Pré-visualização](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) e [Redes Aceleradas para uma máquina virtual](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

***A instância está isolada do hardware dedicado de um único cliente.
<br>

## <a name="f-series"></a>Série F
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |2 |16 |2 |2x500 |1 / moderado |
| Standard_F2 |2 |4 |32 |4 |4x500 |2 / alto |
| Standard_F4 |4 |8 |64 |8 |8x500 |4 / alto |
| Standard_F8 |8 |16 |128 |16 |16x500 |8 / alto |
| Standard_F16 |16 |32 |256 |32 |32x500 |8 / extremamente alto |

<br>

## <a name="fs-series"></a>Série Fs*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4,000 / 32 (12) |3,200 / 48 |1 / moderado |
| Standard_F2s |2 |4 |8 |4 |8,000 / 64 (24) |6,400 / 96 |2 / alto |
| Standard_F4s |4 |8 |16 |8 |16,000 / 128 (48) |12,800 / 192 |4 / alto |
| Standard_F8s |8 |16 |32 |16 |32,000 / 256 (96) |25,600 / 384 |8 / alto |
| Standard_F16s |16 |32 |64 |32 |64,000 / 512 (192) |51,200 / 768 |8 / extremamente alto |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série Fs pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s).  Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="g-series"></a>Série G
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4 x 500 |1/alto |
| Standard_G2 |4 |56 |768 |8 |8 x 500 |2 / alto |
| Standard_G3 |8 |112 |1,536 |16 |16 x 500 |4 / muito alto |
| Standard_G4 |16 |224 |3,072 |32 |32 x 500 |8 / extremamente alto |
| Standard_G5* |32 |448 |6,144 |64 |64 x 500 |8 / extremamente alto |

*A instância está isolada do hardware dedicado de um único cliente.
<br>

## <a name="gs-series"></a>Séries GS*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10,000 / 100 (264) |5,000 / 125 |1/alto |
| Standard_GS2 |4 |56 |112 |8 |20,000 / 200 (528) |10,000 / 250 |2/alto |
| Standard_GS3 |8 |112 |224 |16 |40,000 / 400 (1,056) |20,000 / 500 |4 / muito alto |
| Standard_GS4 |16 |224 |448 |32 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / extremamente alto |
| Standard_GS5** |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / extremamente alto |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série GS pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s). 

**A instância está isolada do hardware dedicado de um único cliente.
<br>

## <a name="h-series"></a>Série H
As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho destinadas a necessidades computacionais avançadas, como modelagem molecular e dinâmicas de fluídos computacionais. Estas VMs de 8 e 16 núcleos foram criadas com base na tecnologia de processador Intel Haswell E5-2667 V3 com memória DDR4 e armazenamento baseado em SSD local. 

Além do poder de CPU substancial, a série H oferece diversas opções para funcionamento em rede RDMA de baixa latência através de FDR InfiniBand e várias configurações de memória para suportar requisitos computacionais de memória intensiva.

Para obter informações e considerações sobre como utilizar estes tamanhos, veja [Acerca de VMs da série H e da série A de computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco: IOPS | NICs/Largura de banda da rede máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |2 / alto |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 / muito alto |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |2 / alto |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4 / muito alto |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4 / muito alto |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 / muito alto |

*Com capacidade RDMA

<br>


## <a name="ls-series"></a>Série Ls 

A série Ls é otimizada para cargas de trabalho que necessitam de armazenamento local de baixa latência, como bases de dados NoSQL (por exemplo, Cassandra, MongoDB, Cloudera e Redis). A série Ls oferece até 32 núcleos de CPU, com o [processador Intel® Xeon® E5 v3 família](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Este é o mesmo desempenho de CPU que o da série G/GS e dispõe de 8 GiB de memória por núcleo de CPU.  

 
| Tamanho          | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs/Largura de banda da rede máximos | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | ND / ND (0)          | 5,000 / 125                               | 2 / alto       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | ND / ND (0)          | 10,000 / 250                              | 4 / muito alto  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | ND / ND (0)          | 20,000 / 500                              | 8 / extremamente alto | 
| Standard_L32s | 32   | 256  | 5,630 | 64             | ND / ND (0)          | 40,000 / 1,000                            | 8 / extremamente alto | 
 
MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes. 



## <a name="n-series"></a>Série N
Os tamanhos NC e NV também são conhecidos como instâncias ativadas para GPU. São máquinas virtuais especializadas que incluem placas GPU da NVIDIA otimizadas para diferentes cenários e casos de utilização. Os tamanhos NV são otimizados e foram concebidos para visualização remota, transmissão em fluxo, jogos, codificação e cenários VDI que utilizem arquiteturas, como OpenGL e DirectX. Os tamanhos NC são mais otimizados para aplicações e algoritmos de computação e rede intensivas, incluindo simulações e aplicações baseadas em CUDA e OpenCL. 


### <a name="nv-instances"></a>Instâncias NV
As instâncias NV têm a tecnologia da placa GPU Tesla M60 da NVIDIA e NVIDIA GRID para aplicações aceleradas de ambiente de trabalho e ambientes de trabalho virtuais onde os clientes poderão visualizar os seus dados ou simulações. Os utilizadores poderão visualizar os seus fluxos de trabalho gráficos intensivos nas instâncias NV para obter capacidades gráficas superiores e executar adicionalmente cargas de trabalho de precisão única, como codificação e composição. O Tesla M60 fornece núcleos CUDA 4096 numa estrutura de GPU dupla com até 36 fluxos de 1080p H.264. 


| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = metade de uma placa M60.

**Sistemas operativos suportados**

* Windows Server 2016, Windows Server 2012 R2 – veja [Configuração de controladores da série N para Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)

### <a name="nc-instances"></a>Instâncias NC
As instâncias NC têm tecnologia da placa Tesla K80 da NVIDIA. Os utilizadores podem agora processar os dados muito mais rapidamente ao tirar partido da tecnologia CUDA para aplicações de exploração de energia, simulações de falhas, composição de raios, aprendizagem aprofundada e muito mais. O Tesla K80 fornece núcleos CUDA 4992 com um design de GPU dupla, até 2,91 Teraflops de precisão dupla e até 8,93 Teraflops de desempenho de precisão única.

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA

**Sistemas operativos suportados**

* Windows Server 2016, Windows Server 2012 R2 – veja [Configuração de controladores da série N para Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)
* Ubuntu 16.04 LTS – veja [Configuração de controladores da série N para Linux](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md)

<br>

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Notas: Standard A0 - A4 que utilizam a CLI e o PowerShell
No modelo de implementação clássica, alguns nomes de tamanhos de VMs são ligeiramente diferentes, como na CLI e no PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é Small
* Standard_A2 é Medium
* Standard_A3 é Large
* Standard_A4 é ExtraLarge

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [Subscrição do Azure e limites de serviço, quotas e restrições](../articles/azure-subscription-service-limits.md).
* Saiba mais [Sobre VMs de série H e de série A de computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para cargas de trabalho como a Computação de alto Desempenho (HPC).



<!--HONumber=Feb17_HO3-->


