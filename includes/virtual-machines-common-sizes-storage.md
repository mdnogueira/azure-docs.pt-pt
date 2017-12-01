Armazenamento oferecem débito elevado de disco e e/s otimizados tamanhos de VM e são ideais para macrodados, NoSQL e SQL, bases de dados. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento débito e a rede de largura de banda para cada tamanho neste agrupamento. 

A série Ls oferece até 32 vCPUs, com o [processador Intel® Xeon® E5 v3 família](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls tem o mesmo desempenho de CPU que a série G/GS e dispõe de 8 GiB de memória por vCPU.  

## <a name="ls-series"></a>Série Ls

ACU: 180-240
 
| Tamanho          | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito de armazenamento temporário de máx.: IOPS / MBps | Débito máximo do disco não colocado em cache: IOPS/MBps | Os NICs de máximo / esperado largura de banda de rede (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 8    | 20,000 / 200   | 10,000 / 250        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 16   | 40,000 / 400   | 20,000 / 500       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 32   | 80,000 / 800   | 40,000 / 1,000       | 8 / 6,000 - 16,000 &#8224; | 
| Standard_L32s* | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 80,000 / 2,000     | 8 / 20,000 | 
 

O débito máximo do disco possível com VMs da série Ls pode estar limitado pelo número, tamanho e repartição de quaisquer discos anexados. Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md). 

*A instância está isolada do hardware dedicado de um único cliente.

