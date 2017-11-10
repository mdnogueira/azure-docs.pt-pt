
GPU com otimização de VM são para as máquinas virtuais especializadas, como destina de composição de gráfico pesada e edição de vídeo. Disponível com GPUs único ou vários. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como o desempenho de débito e da rede de armazenamento para cada tamanho neste agrupamento. 


Os tamanhos NC e NV também são conhecidos como instâncias ativadas para GPU. Estes são os tamanhos de máquinas de virtuais especializadas que incluem preparado para cartões GPU do NVIDIA, otimizados para diferentes cenários e casos de utilização. Os tamanhos NV são otimizados e foram concebidos para visualização remota, transmissão em fluxo, jogos, codificação e cenários VDI que utilizem arquiteturas, como OpenGL e DirectX. Os tamanhos NC são mais otimizados para aplicações e algoritmos de computação e rede intensivas, incluindo simulações e aplicações baseadas em CUDA e OpenCL. 


As instâncias NV têm a tecnologia da placa GPU Tesla M60 da NVIDIA e NVIDIA GRID para aplicações aceleradas de ambiente de trabalho e ambientes de trabalho virtuais onde os clientes podem visualizar os seus dados ou simulações. Os utilizadores têm capacidade para visualizar os seus fluxos de trabalho intensivas de gráficos nas instâncias do NV para obter a capacidade de gráficos superior e adicionalmente executar única precisão cargas de trabalho, tais como a codificação e composição. O Tesla M60 fornece núcleos CUDA 4096 numa estrutura de GPU dupla com até 36 fluxos de 1080p H.264. 

As instâncias NC têm tecnologia da placa Tesla K80 da NVIDIA. Os utilizadores podem agora processar os dados muito mais rapidamente ao tirar partido da tecnologia CUDA para aplicações de exploração de energia, simulações de falhas, composição de raios, aprendizagem aprofundada e muito mais. O Tesla K80 fornece núcleos CUDA 4992 com um design de GPU dupla, até 2,91 Teraflops de precisão dupla e até 8,93 Teraflops de desempenho de precisão única.

## <a name="nv-instances"></a>Instâncias NV

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Discos de dados máximos |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = metade de uma placa M60.

## <a name="nc-instances"></a>Instâncias NC

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Discos de dados máximos |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA


