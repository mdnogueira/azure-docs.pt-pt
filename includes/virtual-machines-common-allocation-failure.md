
Se o problema do Azure não esteja endereçado neste artigo, visite o [fóruns do Azure no MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema estes fóruns ou a @AzureSupport no Twitter. Além disso, pode ficheiro um pedido de suporte do Azure ao selecionar **obter suporte** no [suporte do Azure](https://azure.microsoft.com/support/options/) site.

## <a name="general-troubleshooting-steps"></a>Passos de resolução de problemas gerais
### <a name="troubleshoot-common-allocation-failures-in-the-classic-deployment-model"></a>Resolver problemas de falhas de alocação comuns no modelo de implementação clássica
Estes passos podem ajudar a resolver várias falhas de atribuição de máquinas virtuais:

* Redimensione a VM para um tamanho VM diferente.<br>
    Clique em **Procurar tudo** > **máquinas virtuais (clássicas)** > sua máquina virtual > **definições** > **tamanho**. Para obter passos detalhados, consulte [redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).
* Eliminar todas as VMs do serviço em nuvem e voltar a criar as VMs.<br>
    Clique em **Procurar tudo** > **máquinas virtuais (clássicas)** > sua máquina virtual > **eliminar**. Em seguida, clique em **novo** > **computação** > [imagem de máquina virtual].

### <a name="troubleshoot-common-allocation-failures-in-the-azure-resource-manager-deployment-model"></a>Resolver problemas de falhas de alocação comuns no modelo de implementação Azure Resource Manager
Estes passos podem ajudar a resolver várias falhas de atribuição de máquinas virtuais:

* Parar (desalocar) todas as VMs no mesmo disponibilidade definido, em seguida, reinicie cada um deles.<br>
    Para parar o: clique em **grupos de recursos** > o grupo de recursos > **recursos** > o conjunto de disponibilidade > **máquinas virtuais** > sua máquina virtual >  **Parar**.
  
    Depois de todas as VMs parar, selecione a primeira VM e clique em **iniciar**.

## <a name="background-information"></a>Informações gerais
### <a name="how-allocation-works"></a>Como funciona a alocação
Os servidores nos centros de dados do Azure são divididos em partições em clusters. Normalmente, um pedido de atribuição é tentado em vários clusters, mas é possível que determinadas restrições do pedido de alocação forçar a plataforma do Azure, tente o pedido em apenas um cluster. Neste artigo, iremos irá consultar este como "afixado para um cluster." Diagrama 1 abaixo ilustra o caso de uma alocação normal, que é tentada em vários clusters. Diagrama 2 ilustra o caso de uma alocação que tenha afixado ao Cluster 2 uma vez que é onde está alojado o conjunto de CS_1 do serviço de nuvem ou de disponibilidade existente.
![Diagrama de atribuição](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Por que motivo ocorrem falhas de atribuição
Quando um pedido de alocação está afixado para um cluster, não há uma maior possibilidade de conseguir encontrar recursos livres, uma vez que o agrupamento de recursos disponíveis é menor. Além disso, se o pedido de alocação está afixado para um cluster, mas o tipo de recurso pedido não é suportado nesse cluster, o pedido irá falhar mesmo se o cluster tem de libertar recursos. Diagrama 3 abaixo ilustra o caso em que uma alocação afixada falha porque o cluster de candidatos só tem de libertar recursos. Diagrama 4 ilustra as maiúsculas e minúsculas onde uma alocação afixada falha porque o cluster de candidatos apenas não suporta o tamanho da VM pedido, apesar do cluster tem de libertar recursos.

![Falha na alocação afixado](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## <a name="detailed-troubleshoot-steps-specific-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Detalhadas resolver problemas de cenários de falha de alocação específico de passos no modelo de implementação clássica
Seguem-se cenários comuns de alocação que fazer com que um pedido de alocação para ser afixado. Iremos irá aprofundar cada cenário neste artigo.

* Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço em nuvem existente
* Reinício parcialmente parada (desalocadas) VMs
* Reinício totalmente parada (desalocadas) VMs
* Implementações de teste/produção (plataforma como serviço apenas)
* Grupo de afinidade (proximidade VM/serviço)
* Baseado em grupo de afinidade de rede virtual

Quando receber um erro de alocação, consulte o artigo se qualquer um dos cenários descritos aplicam-se o erro. Utilize o erro de alocação devolvido pela plataforma do Azure para identificar o cenário correspondente. Se o pedido está afixado, remova algumas das restrições afixação para abrir o seu pedido para clusters mais, deste modo, aumentar a probabilidade de sucesso de alocação.

Em geral, desde que o erro indica "não é suportado o tamanho da VM pedido", pode sempre voltar a tentar num momento posterior, como recursos suficientes podem ter sido libertados do cluster para acomodar o seu pedido. Se o problema é que o tamanho da VM pedido não é suportado, experimente um tamanho VM diferente. Caso contrário, a única opção é remover a restrição de afixação.

Dois cenários comuns de falha estão relacionados com grupos de afinidades. No passado, um grupo de afinidades foi utilizado para fornecer próximos instâncias de VMs/serviço ou foi utilizada para permitir a criação de uma rede virtual. Com a introdução das redes virtuais regionais, grupos de afinidades já não são necessários para criar uma rede virtual. Com a redução de latência de rede numa infraestrutura do Azure, a recomendação para utilizar grupos de afinidade de proximidade VM/serviço foi alterada.

Diagrama 5 abaixo apresenta taxonomia dos cenários de atribuição (afixado).
![Alocação afixado taxonomia](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [!NOTE]
> O erro listado em cada cenário de atribuição é um formulário curto. Consulte o [pesquisa de cadeia de erro](#Error string lookup) para cadeias de erro detalhadas.
> 
> 

## <a name="allocation-scenario-resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Cenário de alocação: Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço em nuvem existente
**Erro**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa desta afixação de cluster**

Um pedido para redimensionar uma VM ou adicionar uma VM ou uma instância de função a um serviço em nuvem existente tem de ser tentada no cluster original que aloja o serviço em nuvem existente. Criar um novo serviço em nuvem permite que a plataforma do Azure localizar outro cluster que tem de libertar recursos ou suporta o tamanho da VM que pediu.

**Solução**

Se o erro é Upgrade_VMSizeNotSupported *, experimente um tamanho VM diferente. Se utilizar um tamanho VM diferente não é uma opção, mas é aceitável para utilizar um endereço IP virtual (VIP) diferentes, crie um novo serviço em nuvem para alojar a nova VM e adicionar o novo serviço em nuvem para a rede virtual regional onde estiver a executar as VMs existentes. Se o serviço em nuvem existente não utilizar uma rede virtual regional, pode ainda criar uma nova rede virtual para o novo serviço de nuvem e, em seguida, ligar os [rede virtual existente para a nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Ver mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se o erro GeneralError *, é provável que o tipo de recurso (por exemplo, um determinado tamanho da VM) é suportado pelo cluster, mas o cluster não tem recursos gratuitos neste momento. É semelhante ao cenário acima, adicione o recurso de computação pretendido através da criação de um novo serviço de nuvem (tenha em atenção que o novo serviço de nuvem tem de utilizar um VIP diferente) e utilizar uma rede virtual regional para ligar os seus serviços em nuvem.

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Cenário de alocação: reinício parcialmente parada (desalocadas) VMs
**Erro**

GeneralError *

**Causa desta afixação de cluster**

Desalocação parcial significa que parada (desalocadas) uma ou mais, mas não todos, VMs num serviço em nuvem. Quando parar (desalocar) uma VM, os recursos associados são lançados. Reiniciar essa VM parada (desalocada), por conseguinte, é um novo pedido de alocação. Reinício de VMs num serviço em nuvem parcialmente desalocada é equivalente a adição de VMs para um serviço em nuvem existente. O pedido de atribuição tem de ser tentada no cluster original que aloja o serviço em nuvem existente. Criação de um serviço de nuvem diferente, permite que a plataforma do Azure localizar outro cluster que possui o recurso gratuito ou suporta o tamanho da VM que pediu.

**Solução**

Se é aceitável para utilizar um VIP diferente, eliminar as VMs paradas (desalocadas) (mas manter os discos associados) e adicione as VMs através de um serviço em nuvem diferente. Utilize uma rede virtual regional para ligar os serviços de cloud:

* Se o serviço em nuvem existente utiliza uma rede virtual regional, adicione simplesmente o novo serviço em nuvem para a mesma rede virtual.
* Se o serviço em nuvem existente não utiliza uma rede virtual regional, crie uma nova rede virtual para o novo serviço de nuvem e, em seguida, [ligar a sua rede virtual existente para a nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Ver mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="allocation-scenario-restart-fully-stopped-deallocated-vms"></a>Cenário de alocação: reinício totalmente parada (desalocadas) VMs
**Erro**

GeneralError *

**Causa desta afixação de cluster**

Meios de Desalocação completo que deixaram (desalocado) todas as VMs de um serviço em nuvem. Os pedidos de alocação para reiniciar estes VMs tem de ser tentada no cluster original que aloja o serviço em nuvem. Criar um novo serviço em nuvem permite que a plataforma do Azure localizar outro cluster que tem de libertar recursos ou suporta o tamanho da VM que pediu.

**Solução**

Se é aceitável para utilizar um VIP diferente, elimine as originais paradas (desalocadas) as VMs (, mas manter os discos associados) e eliminar o serviço de nuvem correspondente (os recursos de computação associado já foram lançados quando é parada (desalocada) as VMs). Crie um novo serviço em nuvem para adicionar as VMs novamente.

## <a name="allocation-scenario-stagingproduction-deployments-platform-as-a-service-only"></a>Cenário de alocação: implementações de teste/produção (plataforma como serviço apenas)
**Erro**

New_General * ou New_VMSizeNotSupported *

**Causa desta afixação de cluster**

A implementação de teste e a implementação de produção de um serviço em nuvem estão alojados no mesmo cluster. Ao adicionar a segunda implementação, o pedido de atribuição correspondente será tentado no mesmo cluster que aloja a primeira implementação.

**Solução**

Elimine a primeira implementação e o serviço em nuvem original e reimplemente o serviço de nuvem. Esta ação pode encaminhado para a primeira implementação de um cluster tem recursos livres suficientes para se ajustar ambas as implementações ou num cluster que suporta os tamanhos VM que pediu.

## <a name="allocation-scenario-affinity-group-vmservice-proximity"></a>Cenário de alocação: grupo de afinidade (proximidade VM/serviço)
**Erro**

New_General * ou New_VMSizeNotSupported *

**Causa desta afixação de cluster**

Nenhuma computação recursos atribuídos a um grupo de afinidade está associado a um cluster. Os pedidos de novos recursos de computação em que o grupo de afinidade estão tentadas no mesmo cluster onde estão alojados os recursos existentes. Isto acontece se os novos recursos são criados através de um novo serviço em nuvem ou através de um serviço em nuvem existente.

**Solução**

Se um grupo de afinidade não é necessário, não utilize um grupo de afinidade ou agrupar os recursos de computação em vários grupos de afinidade.

## <a name="allocation-scenario-affinity-group-based-virtual-network"></a>Cenário de alocação: baseado em grupo de afinidade de rede virtual
**Erro**

New_General * ou New_VMSizeNotSupported *

**Causa desta afixação de cluster**

Antes de redes virtuais regionais foram introduzidas, era necessário para associar uma rede virtual um grupo de afinidade. Como resultado, computação recursos colocados num grupo de afinidades estão vinculados pelas mesmas restrições conforme descrito no "cenário de alocação: grupo de afinidade (proximidade VM/serviço)" secção acima. Os recursos de computação estão associados a um cluster.

**Solução**

Se não precisar de um grupo de afinidade, crie uma nova rede virtual regional para os novos recursos que está a adicionar, e, em seguida, [ligar a sua rede virtual existente para a nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Ver mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Em alternativa, pode [migrar a rede virtual baseado em grupo de afinidade para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, adicione novamente os recursos pretendidos.

## <a name="detailed-troubleshooting-steps-specific-allocation-failure-scenarios-in-the-azure-resource-manager-deployment-model"></a>Cenários de falha de alocação específico de passos no modelo de implementação Azure Resource Manager de resolução de problemas detalhada
Seguem-se cenários comuns de alocação que fazer com que um pedido de alocação para ser afixado. Iremos irá aprofundar cada cenário neste artigo.

* Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço em nuvem existente
* Reinício parcialmente parada (desalocadas) VMs
* Reinício totalmente parada (desalocadas) VMs

Quando receber um erro de alocação, consulte o artigo se qualquer um dos cenários descritos aplicam-se o erro. Utilize o erro de alocação devolvido pela plataforma do Azure para identificar o cenário correspondente. Se o pedido está afixado num cluster existente, remova algumas das restrições afixação para abrir o seu pedido para clusters mais, deste modo, aumentar a probabilidade de sucesso de alocação.

Em geral, desde que o erro indica "não é suportado o tamanho da VM pedido", pode sempre voltar a tentar num momento posterior, como recursos suficientes podem ter sido libertados do cluster para acomodar o seu pedido. Se o problema que o tamanho da VM pedido não é suportado, veja a seguir para soluções.

## <a name="allocation-scenario-resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Cenário de alocação: Redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade existente
**Erro**

Upgrade_VMSizeNotSupported * ou GeneralError *

**Causa desta afixação de cluster**

Um pedido para redimensionar uma VM ou adicionar uma VM a um conjunto de disponibilidade existente tem de ser tentada no cluster original que aloja o conjunto de disponibilidade existente. Criar um novo conjunto de disponibilidade permite que a plataforma do Azure localizar outro cluster que tem de libertar recursos ou suporta o tamanho da VM que pediu.

**Solução**

Se o erro é Upgrade_VMSizeNotSupported *, experimente um tamanho VM diferente. Se utilizar um tamanho VM diferente não é uma opção, pare todas as VMs no conjunto de disponibilidade. Em seguida, pode alterar o tamanho da máquina virtual que irá alocar a VM para um cluster que suporta o tamanho da VM pretendido.

Se o erro GeneralError *, é provável que o tipo de recurso (por exemplo, um determinado tamanho da VM) é suportado pelo cluster, mas o cluster não tem recursos gratuitos neste momento. Se a VM pode fazer parte de um conjunto de disponibilidade diferente, crie uma nova VM numa conjunto (na mesma região) de disponibilidade diferente. Esta nova VM, em seguida, pode ser adicionada à mesma rede virtual.  

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Cenário de alocação: reinício parcialmente parada (desalocadas) VMs
**Erro**

GeneralError *

**Causa desta afixação de cluster**

Desalocação parcial significa que parada (desalocada) uma ou mais, mas não todos, VMs na disponibilidade de um conjunto. Quando parar (desalocar) uma VM, os recursos associados são lançados. Reiniciar essa VM parada (desalocada), por conseguinte, é um novo pedido de alocação. Reinício de VMs de um conjunto de disponibilidade parcialmente desalocada é equivalente ao adicionar VMs a um conjunto de disponibilidade existente. O pedido de atribuição tem de ser tentada no cluster original que aloja o conjunto de disponibilidade existente.

**Solução**

Pare todas as VMs no conjunto antes de reiniciar o primeiro de disponibilidade. Isto irá garantir que é executada uma nova tentativa de atribuição e que um novo cluster pode ser selecionado que tenha capacidade disponível.

## <a name="allocation-scenario-restart-fully-stopped-deallocated"></a>Cenário de alocação: reinício totalmente parada (desalocada)
**Erro**

GeneralError *

**Causa desta afixação de cluster**

Meios de Desalocação completo que deixaram (desalocado) todas as VMs num conjunto de disponibilidade. O pedido de alocação para reiniciar estes VMs destina-se a todos os clusters que suportam o tamanho pretendido.

**Solução**

Selecione um novo tamanho VM para atribuir. Se isto não resultar, tente novamente mais tarde.

<a name="Error string lookup"></a>

## <a name="error-string-lookup"></a>Pesquisa de cadeia de erro
**New_VMSizeNotSupported***

"O tamanho VM (ou combinação de tamanhos de VM) necessária para esta implementação não pode ser aprovisionada devido às restrições do pedido de implementação. Se for possível, tente simplificar as restrições, tais como enlaces de rede virtual, implementar um serviço alojado sem implementações e noutro grupo de afinidade ou com nenhum grupo de afinidade ou experimente implementar noutra região."

**New_General***

"Falha na alocação; Não é possível satisfazer as restrições no pedido. A nova implementação do serviço pedida está vinculada a um grupo de afinidade ou tenha como destino uma rede virtual ou há uma implementação existente neste serviço alojado. Qualquer uma destas condições restringe a nova implementação de recursos do Azure específicos. Volte a tentar mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. Em alternativa, se possível, remova as restrições acima mencionadas ou experimente implementar noutra região."

**Upgrade_VMSizeNotSupported***

"Não é possível atualizar a implementação. O tamanho da VM pedido XXX poderão não estar disponível em recursos que suportam a implementação existente. Tente novamente mais tarde, tente com um tamanho VM diferente ou menor número de instâncias de função ou crie uma implementação num serviço alojado vazio com um grupo de afinidade novo ou sem vinculação ao grupo de afinidade."

**GeneralError***

"O servidor encontrou um erro interno. Repita o pedido." Ou "Falha ao produzir uma alocação para o serviço".

