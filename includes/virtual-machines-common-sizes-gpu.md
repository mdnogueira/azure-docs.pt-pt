Os tamanhos NC e NV também são conhecidos como instâncias ativadas para GPU. São máquinas virtuais especializadas que incluem placas GPU da NVIDIA otimizadas para diferentes cenários e casos de utilização. Os tamanhos NV são otimizados e foram concebidos para visualização remota, transmissão em fluxo, jogos, codificação e cenários VDI que utilizem arquiteturas, como OpenGL e DirectX. Os tamanhos NC são mais otimizados para aplicações e algoritmos de computação e rede intensivas, incluindo simulações e aplicações baseadas em CUDA e OpenCL. 


As instâncias NV têm a tecnologia da placa GPU Tesla M60 da NVIDIA e NVIDIA GRID para aplicações aceleradas de ambiente de trabalho e ambientes de trabalho virtuais onde os clientes poderão visualizar os seus dados ou simulações. Os utilizadores poderão visualizar os seus fluxos de trabalho gráficos intensivos nas instâncias NV para obter capacidades gráficas superiores e executar adicionalmente cargas de trabalho de precisão única, como codificação e composição. O Tesla M60 fornece núcleos CUDA 4096 numa estrutura de GPU dupla com até 36 fluxos de 1080p H.264. 

As instâncias NC têm tecnologia da placa Tesla K80 da NVIDIA. Os utilizadores podem agora processar os dados muito mais rapidamente ao tirar partido da tecnologia CUDA para aplicações de exploração de energia, simulações de falhas, composição de raios, aprendizagem aprofundada e muito mais. O Tesla K80 fornece núcleos CUDA 4992 com um design de GPU dupla, até 2,91 Teraflops de precisão dupla e até 8,93 Teraflops de desempenho de precisão única.

## <a name="nv-instances"></a>Instâncias NV

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = metade de uma placa M60.

**Sistemas operativos suportados**

* Windows Server 2016, Windows Server 2012 R2 – veja [Configuração de controladores da série N para Windows](../articles/virtual-machines/windows/n-series-driver-setup.md)

## <a name="nc-instances"></a>Instâncias NC

| Tamanho | Núcleos de CPU | Memória: GiB | SSD Local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA

**Sistemas operativos suportados**

* Windows Server 2016, Windows Server 2012 R2 – veja [Configuração de controladores da série N para Windows](../articles/virtual-machines/windows/n-series-driver-setup.md)
* Ubuntu 16.04 LTS – veja [Configuração de controladores da série N para Linux](../articles/virtual-machines/linux/n-series-driver-setup.md)

<br>

