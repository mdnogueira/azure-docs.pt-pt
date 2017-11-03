## <a name="deployment-considerations"></a>Considerações sobre implementação

* Para disponibilidade de série N VMs, consulte [produtos disponíveis por região](https://azure.microsoft.com/en-us/regions/services/).

* Série N VMs só podem ser implementadas no modelo de implementação Resource Manager.

* Ao criar uma VM de série N no portal do Azure, no **Noções básicas** painel, selecione um **tipo de disco da VM** de **HDD**. Para escolher um tamanho de série N disponível, no **tamanho** painel, clique em **ver todos os**.

* Série N VMs não suportam discos VM que sejam copiados pelo armazenamento do Azure Premium.

* Se pretender implementar mais do que alguns série N VMs, considere uma subscrição pay as you go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

* Poderá ter de aumentar a quota de núcleos (por região) na sua subscrição do Azure e aumentar a quota de núcleos de NC ou NV separada. Para pedir um aumento de quota [abrir um pedido de suporte ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) , sem encargos. Limites de predefinição podem variar consoante a categoria de subscrição.

* Uma imagem de VM, pode implementar em série N VMs é o [máquina de Virtual de ciência de dados do Azure](../articles/machine-learning/data-science-virtual-machine/overview.md). As máquinas de ciência de dados preinstalls e configura muitos ciência de dados mais populares e avançada ferramentas de aprendizagem. É também preinstalls controladores NVIDIA Tesla GPU para instâncias de NC.





