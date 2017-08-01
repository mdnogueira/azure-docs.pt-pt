
* A série M oferece a contagem de CPU mais alta (até 128 vCPUs) e a maior capacidade de memória (até 2.0 TiB) de qualquer VM na cloud.  É ideal para bases de dados muito grandes ou outras aplicações que tiram partido de contagens altas de CPU e grandes quantidades de memória.

* As séries Dv2, D, G e as variantes DS/GS são ideais para aplicações que exigem CPUs mais rápidas, melhor desempenho de disco local ou memórias com mais capacidade.  Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.

* As VMs da série D foram concebidos para executar aplicações que exigem um maior desempenho de poder de computação e disco temporário. As VMs da série D fornecem processadores mais rápidos, um rácio de memória para núcleo superior e uma unidade de estado sólida (SSD) para o disco temporário. Para obter detalhes, consulte o anúncio no blogue do Azure, [Novos Tamanhos de Máquinas Virtuais da Série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* A série DV2, o seguimento da série D original, apresenta uma CPU mais poderosa. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se no processador de última geração de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell), podendo chegar aos 3,1 GHz com o Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.


## <a name="esv3-series"></a>Série ESv3

ACU: 160-190

As instâncias da série ESv3 baseiam-se no processador 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) e podem chegar aos 3.5GHz com o Intel Turbo Boost Technology 2.0 e utilizar o armazenamento premium. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

| Tamanho             | vCPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache e local: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|------------------|-----------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3  | 2         | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2/moderado                                   |
| Standard_E4s_v3  | 4         | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2/moderado                                   |
| Standard_E8s_v3  | 8         | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / alto                                       |
| Standard_E16s_v3 | 16        | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / alto                                       |
| Standard_E32s_v3 | 32        | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / extremamente alto                             |
| Standard_E64s_v3 | 64        | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / extremamente alto                             |



## <a name="ev3-series"></a>Série Ev3

ACU: 160 - 190 

As instâncias da série Ev3 são baseadas no processador de 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) e pode chegar aos 3.5 GHz com o Intel Turbo Boost Technology 2.0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3. 

| Tamanho             | vCPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache e local: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|------------------|-----------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2_v3  | 2         | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2/moderado                                   |
| Standard_E4_v3  | 4         | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2/moderado                                   |
| Standard_E8_v3  | 8         | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / alto                                       |
| Standard_E16_v3 | 16        | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / alto                                       |
| Standard_E32_v3 | 32        | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / extremamente alto                             |
| Standard_E64_v3 | 64        | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / extremamente alto                             |


## <a name="m-series"></a>Série M*

ACU: 160-180

| Tamanho            | vCPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache e local: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M64ms  | 64   | 1792        | 2048           | 32             | 80,000 / 800 (6348)       | 40,000 / 1,000                            | 8 / 16000          |
| Standard_M128s** | 128  | 2048        | 4096           | 64             | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 25000          |



*As VMs da série M contam com a Intel® Hyper-Threading Technology

**Mais de 64 vCPUs exigem um destes SO convidados suportados: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux ou CentOS 7.3 com LIS 4.2.1 

<br>

## <a name="gs-series"></a>Séries GS*

ACU: 180 - 240

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache e local: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / Desempenho de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |8 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |16 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4 |16 |224 |448 |32 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 6000 - 16000 &#8224; |
| Standard_GS5** |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série GS pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s). Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md). 

**A instância está isolada do hardware dedicado de um único cliente.


<br>

## <a name="g-series"></a>Série G

ACU: 180 - 240

| Tamanho         | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Débito do disco local máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4 / 4 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8 / 8 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 16 / 16 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 32 / 32 x 500                     | 8 / 6000 - 16000 &#8224;          |
| Standard_G5* | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

*A instância está isolada do hardware dedicado de um único cliente.
<br>


## <a name="dsv2-series"></a>Série DSv2*

ACU: 210 - 250

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache e local: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / Desempenho de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 96 |2 / 1500 |
| Standard_DS12_v2 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 192 |4 / 3000 |
| Standard_DS13_v2 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 384 |8 / 6000 |
| Standard_DS14_v2 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 768 |8 / 6000 - 12000 &#8224; |
| Standard_DS15_v2** |20 |140 |280 |40 |80,000 / 640 (720) |64,000 / 960 |8 / 20000***

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série DSv2 pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s).  Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

**Instância é um nó isolado que garante que a sua VM é a única no nosso nó Intel Haswell.

***25000 Mbps com Redes Aceleradas

<br>

## <a name="dv2-series"></a>Série Dv2

ACU: 210 - 250

| Tamanho              | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Débito do disco local máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / 6000 - 12000 &#8224;          |
| Standard_D15_v2* | 20        | 140         | 1,000          | 60000 / 937 / 468                                        | 40 / 40x500                       | 8 / 20000** |

*Instância é um nó isolado que garante que a sua VM é a única no nosso nó Intel Haswell.

**25000 Mbps com Redes Aceleradas.

<br>

## <a name="ds-series"></a>Série DS*

ACU: 160

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Discos de dados máximos | Débito máximo do disco em cache e local: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / Desempenho de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 512 |8 / 6000 - 8000 &#8224; |

*O débito máximo do disco (IOPS ou MBps) possível com uma VM da série DS pode estar limitado pelo número, tamanho e repartição do(s) disco(s) anexado(s).  Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).


## <a name="d-series"></a>Série D

ACU: 160

| Tamanho         | Núcleos de CPU | Memória: GiB | SSD Local: GiB | Débito do disco local máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / 6000 - 8000 &#8224;                |

<br>

