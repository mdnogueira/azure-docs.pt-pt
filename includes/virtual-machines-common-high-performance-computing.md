As organizações têm necessidades de computação em grande escala. Estas cargas de trabalho de Macrocomputação incluem design engenharia e análise, cálculos de riscos financeiros, composição de imagem, modelação complexa, simulações Monte Carlo e muito mais. 

Utilize a nuvem do Azure para executar com eficiência intensivas de computação Linux e Windows cargas de trabalho, das tarefas de lote paralelas para simulações de HPC tradicionais. Execute a sua HPC e cargas de trabalho do batch na infraestrutura do Azure, com a sua escolha de cálculo serviços, gestores de grelha, soluções de Marketplace e alojado de fornecedor (SaaS) aplicações. O Azure oferece soluções flexíveis para distribuir trabalho e dimensionar a milhares de VMs ou núcleos e, em seguida, reduzir verticalmente quando necessitar de menos recursos. 



## <a name="solution-options"></a>Opções de solução



* **Soluções do-IT-yourself**
    * Configurar o seu ambiente de cluster em máquinas virtuais do Azure ou [conjuntos de dimensionamento de máquina virtual](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Comparação de precisão e deslocar um cluster no local ou implementar um cluster de novo no Azure para a capacidade adicional. 
    * Utilizar modelos Azure Resource Manager para implementar à esquerda [gestores de carga de trabalho](#workload-managers), infraestrutura, e [aplicações](#hpc-applications). 
    * Escolha [tamanhos HPC e GPU VM](#hpc-and-gpu-sizes) que incluem ligações de hardware e de rede especializadas para cargas de trabalho MPI ou GPU. 
    * Adicionar [armazenamento de elevado desempenho](#hpc-storage) para cargas de trabalho de I/O intensivo.
* **Soluções híbridas**
    * Expandir a sua solução no local para descarregamento de cargas de trabalho de pico de ("rajada") para a infraestrutura do Azure
    * Utilizar a cloud computação a pedido com existentes [Gestor da carga de trabalho](#workload-manager).
    * Tirar partido das [tamanhos HPC e GPU VM](#hpc-and-gpu-sizes) para cargas de trabalho MPI ou GPU.
* **Soluções de computação grandes como um serviço**
    * Desenvolver soluções de Macrocomputação personalizadas e fluxos de trabalho utilizando [do Azure Batch](#azure-batch) relacionados e [serviços do Azure](#related-azure-services).
    * Execute soluções de engenharia e simulação ativado o Azure a partir de fornecedores, incluindo [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/), e [computação](https://cyclecomputing.com/) (agora [associado Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
* **Soluções de Marketplace**
    * Utilizar a escala de [aplicações HPC](#hpc-applications) e [soluções](#marketplace-solutions) oferecido no [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
    


As secções seguintes fornecem mais informações sobre as tecnologias de suporte e ligações para documentação de orientação.



## <a name="marketplace-solutions"></a>Soluções de Marketplace

Visite o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) para imagens de Linux e VM do Windows e soluções concebidas para HPC. Os exemplos incluem:

* [RogueWave baseado em CentOS HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server para HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [Motor de servidor de grelha TIBCO](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Ciência de dados do Azure VM para o Windows e Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Edição de nuvem Intel para Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Aplicações de HPC

Execute personalizadas ou comerciais aplicações HPC no Azure. São vários os exemplos nesta secção são benchmarked dimensionamento de forma eficiente com VMs adicionais ou núcleos de computação. Visite o [Azure Marketplace](https://marketplace.azure.com) pronto para implementar soluções.

> [!NOTE]
> Consulte o fornecedor de qualquer aplicação comercial para restrições de licenciamento, ou outras para executar na nuvem. Nem todos os fornecedores oferecem licenciamento pay as you go. Poderá precisar de um servidor de licenciamento na nuvem para a sua solução ou ligar a um servidor de licenças no local.

### <a name="engineering-applications"></a>Aplicações de engenharia


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB distribuídas Server informática](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Gráficos e composição

* [Autodesk Maya, 3ds máx. e Arnold](../articles/batch/batch-rendering-service.md) no Azure Batch (pré-visualização)

### <a name="ai-and-deep-learning"></a>AI e aprendizagem profunda

* [Lote AI](../articles/batch-ai/overview.md) formação para modelos de aprendizagem profunda
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Ligação avançada Learning VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Receitas Shipyard para profunda aprendizagem do batch](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Tamanhos HPC e GPU VM
O Azure oferece uma variedade de tamanhos de [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VMs, incluindo os tamanhos concebidos para cargas de trabalho de computação intensivas. Por exemplo, H16r e H16mr VMs podem ligar a uma rede RDMA de back-end de débito elevado. Esta rede de nuvem pode melhorar o desempenho das aplicações paralelas fortemente conjugados em execução sob [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) ou Intel MPI. 

Série N VMs funcionalidade GPUs NVIDIA concebido para aplicações intensivas de computação ou intensivas de gráficos, incluindo a visualização e aprendizagem de artificial intelligence (AI). 

Saiba mais:

* Os tamanhos de computação de elevado desempenho [Linux](../articles/virtual-machines/linux/sizes-hpc.md) e [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VMs 
* Tamanhos GPU preparados para [Linux](../articles/virtual-machines/linux/sizes-gpu.md) e [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VMs 

Aprenda a:

* [Configurar um cluster de Linux RDMA para executar aplicações MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurar um cluster do Windows RDMA com o Microsoft HPC Pack para executar aplicações MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Utilize as VMs de computação intensivas em conjuntos do Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) é informática de uma plataforma de serviço para a execução de paralelo em grande escala e de elevado desempenho (HPC) as aplicações forma eficiente na nuvem. Agendas de Batch intensivas de computação do Azure funcionam com um conjunto gerido de máquinas virtuais, e pode dimensionar automaticamente de computação recursos para satisfazer as necessidades das suas tarefas. 

Fornecedores de SaaS ou os programadores podem utilizar os SDKs do Batch e ferramentas para integrar aplicações HPC ou cargas de trabalho de contentor com o Azure, estagiar os dados para o Azure e criar pipelines de execução da tarefa. 

Aprenda a:

* [Começar a desenvolver com Batch](../articles/batch/batch-dotnet-get-started.md)
* [Utilizar os exemplos de código do Azure Batch](https://github.com/Azure/azure-batch-samples)
* [VMs de prioridade baixa utilização com o Batch](../articles/batch/batch-low-pri-vms.md)
* [Executar de cargas de trabalho HPC com Shipyard do Batch](https://github.com/Azure/batch-shipyard)
* [Utilizar a linguagem de R com o Batch](https://github.com/Azure/doAzureParallel)

## <a name="workload-managers"></a>Gestores de carga de trabalho

Seguem-se exemplos de gestores de cluster e a carga de trabalho que podem ser executadas na infraestrutura do Azure. Crie clusters autónomos em VMs do Azure ou rajada para VMs do Azure de um cluster no local. 
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Gestor de clusters de Bright](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Espetro Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) -consulte as opções para ser executada no [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VMs 



## <a name="hpc-storage"></a>Armazenamento HPC

Cargas de trabalho em grande escala Batch e HPC têm exigências de armazenamento de dados e de acesso que excedem as capacidades de sistemas de ficheiros tradicional de nuvem. Implementar soluções de sistema de ficheiros paralela no Azure, tal como [Lustre](http://lustre.org/) e [BeeGFS](http://www.beegfs.com/content/).

Saiba mais:

* [Sistemas de ficheiros paralelas para armazenamento HPC no Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>Serviços relacionados do Azure

Máquinas virtuais do Azure, conjuntos de dimensionamento de máquina virtual, Batch e os serviços de computação relacionados são a base de maioria das soluções HPC do Azure. No entanto, a solução pode tirar partido de vários serviços do Azure relacionados. Eis uma lista parcial:

### <a name="storage"></a>Armazenamento

* [BLOB, tabela e armazenamento de filas](../articles/storage/storage-introduction.md)
* [Armazenamento de ficheiros](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dados e análise
* [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md) para clusters do Hadoop no Azure
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [Base de Dados SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>Redes
* [Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Contentores
* [Serviço de Contentor](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Histórias dos clientes

Seguem-se exemplos de clientes que tiverem resolvido problemas empresariais com soluções HPC do Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P & C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities internacional](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre soluções de Macrocomputação para [simulação de engenharia](https://simulation.azure.com/), [composição](https://simulation.azure.com/), [mercados banking e capital](https://finance.azure.com/), e [genomics](https://enterprise.microsoft.com/en-us/industries/health/genomics/) .
* Para os anúncios mais recentes, veja o [blogue da equipa do Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e o [blogue do Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Utilizar o Azure gerido e dimensionável [Batch](https://azure.microsoft.com/services/batch/) serviço para executar cargas de trabalho de computação intensivas sem gerir a infraestrutura subjacente [Saiba mais](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)



