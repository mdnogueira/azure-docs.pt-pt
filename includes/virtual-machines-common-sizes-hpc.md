
Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz.  Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como o desempenho de débito e da rede de armazenamento para cada tamanho neste agrupamento. 

As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho destinadas a necessidades computacionais avançadas, como modelagem molecular e dinâmicas de fluídos computacionais. Estas VMs de vCPU de 8 e 16 núcleos foram criadas com base na tecnologia de processador Intel Haswell E5-2667 V3 com memória DDR4 e armazenamento temporário baseado em SSD. 

Além do poder de CPU substancial, a série H oferece diversas opções para funcionamento em rede RDMA de baixa latência através de FDR InfiniBand e várias configurações de memória para suportar requisitos computacionais de memória intensiva.



## <a name="h-series"></a>Série H

ACU: 290-300

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do disco: IOPS | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r* |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr* |16 |224 |2000 |64 |64 x 500 |4 |

*Nas aplicações de MPI, a rede de back-end RDMA dedicada é ativada pela rede FDR InfiniBand, que oferece latência ultra baixa e elevada largura de banda.

<br>



## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva

ACU: 225

| Tamanho | vCPU | Memória: GiB | Armazenamento (HDD) temporário: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs máximos|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |32 |32x500 |2 |
| Standard_A9* |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

*Nas aplicações de MPI, a rede de back-end RDMA dedicada é ativada pela rede FDR InfiniBand, que oferece latência ultra baixa e elevada largura de banda.

<br>



