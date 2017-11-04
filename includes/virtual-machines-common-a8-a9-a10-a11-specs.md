

## <a name="deployment-considerations"></a>Considerações sobre implementação
* **Subscrição do Azure** – para implementar mais do que alguns instâncias de computação intensiva, considere uma subscrição pay as you go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

* **Preços e disponibilidade** -tamanhos de VM estas são oferecidos apenas num padrão de escalão de preço. Verifique [produtos disponíveis por região] (https://azure.microsoft.com/regions/services/) de disponibilidade em regiões do Azure. 
* **Quota de núcleos** – poderá ter de aumentar a quota de núcleos na sua subscrição do Azure do valor predefinido. A subscrição também poderá limitar o número de núcleos que pode implementar em determinados famílias de tamanho VM, incluindo a série de H. Para pedir um aumento de quota [abrir um pedido de suporte ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) , sem encargos. (Predefinição limites podem variar consoante a categoria de subscrição.)
  
  > [!NOTE]
  > Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure. Quotas do Azure são crédito limites, não as garantias de capacidade. Independentemente da sua quota, são-lhe apenas cobrados de núcleos que utiliza.
  > 
  > 
* **Rede virtual** – um Azure [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) não é necessário utilizar as instâncias intensivas de computação. No entanto, para muitas implementações precisa de, pelo menos, uma rede de virtual do Azure baseados na nuvem ou uma ligação site a site se precisar de aceder a recursos no local. Quando for necessário, crie uma nova rede virtual para implementar as instâncias. A adição de VMs de computação intensiva para uma rede virtual num grupo de afinidade não é suportada.
* **Redimensionamento** – devido ao seu hardware especializado, apenas pode redimensionar instâncias intensivas de computação dentro da mesma família de tamanho (série de H ou intensivas de computação série A). Por exemplo, apenas pode redimensionar uma VM de série de H a partir de um tamanho de série H para outro. Além disso, a redimensionar a partir de um tamanho de não-intensivas de computação para um tamanho de computação intensiva não é suportada.  
