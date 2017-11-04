# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plataforma suportada a migração de recursos IaaS do clássico para o Azure Resource Manager
Neste artigo, vamos descrever como podemos está a ativar a migração da infraestrutura como um recursos de serviço (IaaS) do clássico para modelos de implementação do Resource Manager. Pode ler mais sobre [funcionalidades do Azure Resource Manager e os benefícios](../articles/azure-resource-manager/resource-group-overview.md). Iremos detalhe como ligar recursos a partir de modelos de implementação de dois coexistem na sua subscrição através de gateways de site para site de rede virtual.

## <a name="goal-for-migration"></a>Objetivo para migração
Gestor de recursos permite implementar aplicações complexas através de modelos, configura a máquinas virtuais utilizando extensões VM e incorpora a gestão de acesso e a etiquetagem. O Azure Resource Manager inclui implementação paralela, dimensionável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implementação também fornece gestão de ciclo de vida de computação, rede e armazenamento separadamente. Por fim, há um foco sobre como ativar a segurança por predefinição com a imposição de máquinas virtuais numa rede virtual.

Quase todas as funcionalidades do modelo de implementação clássica são suportadas para computação, rede e armazenamento no Gestor de recursos do Azure. Para tirar partido de novas funcionalidades no Gestor de recursos do Azure, pode migrar as implementações existentes do modelo de implementação clássica.

## <a name="supported-resources-for-migration"></a>Recursos suportados para migração
Estes recursos IaaS clássicos são suportados durante a migração

* Virtual Machines
* Conjuntos de Disponibilidade
* Serviços Cloud
* Contas de Armazenamento
* Redes Virtuais
* Gateways de VPN
* Express Route Gateways _(na mesma subscrição que a rede Virtual apenas)_
* Grupos de Segurança de Rede 
* Tabelas de Rota 
* IPs Reservados 

## <a name="supported-scopes-of-migration"></a>Suportado âmbitos de migração
Existem 4 diferentes formas para concluir a migração de recursos de computação, rede e armazenamento. Estes são 

* Migração de máquinas virtuais (não numa rede virtual)
* Migração de máquinas virtuais (numa rede virtual)
* Migração de contas de armazenamento
* Recursos desanexados (grupos de segurança de rede, as tabelas de rotas & IPs reservados)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (não numa rede virtual)
No modelo de implementação Resource Manager, a segurança é imposta para as suas aplicações por predefinição. Todas as VMs têm de ser uma rede virtual no modelo do Resource Manager. Os reinícios de plataforma do Azure (`Stop`, `Deallocate`, e `Start`) as VMs como parte da migração. Tem duas opções para as redes virtuais que as máquinas virtuais serão migradas para:

* Pode pedir a plataforma para criar uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
* É possível migrar a máquina virtual numa rede virtual existente no Gestor de recursos.

> [!NOTE]
> Neste âmbito da migração, as operações de gestão plane e as operações de dados-plane podem não ser permitidas para um período de tempo durante a migração.
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (numa rede virtual)
Para a maioria das configurações de VM, apenas os metadados está a migrar entre os modelos de implementação clássica e Resource Manager. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações de gestão plane poderão não ser permitidas para um determinado período de tempo durante a migração. No entanto, o plane de dados continua a trabalhar. Ou seja, as aplicações em execução em VMs (clássica) não cobrado um período de indisponibilidade durante a migração.

As seguintes configurações não são atualmente suportadas. Se for adicionado suporte no futuro, algumas VMs nesta configuração pode implicar o período de inatividade (aceda através de parar, Desalocação e reiniciar as operações de VM).

* Tiver mais do que um conjunto de disponibilidade num serviço em nuvem único.
* Tem um ou mais conjuntos de disponibilidade e VMs que não estão a ser um conjunto de disponibilidade num serviço em nuvem único.

> [!NOTE]
> Este âmbito da migração, o plane de gestão pode não ser permitido durante um período de tempo durante a migração. Para determinados configurações, tal como descrito anteriormente, dados plane período de indisponibilidade ocorre.
>
>

### <a name="storage-accounts-migration"></a>Migração de contas de armazenamento
Para permitir a migração totalmente integrada, pode implementar as VMs do Gestor de recursos numa conta de armazenamento clássico. Com esta capacidade, recursos de rede e computação podem e devem ser migrados independentemente das contas de armazenamento. Depois de migrar sobre as máquinas virtuais e a rede Virtual, terá de migrar sobre as contas do storage para concluir o processo de migração.

> [!NOTE]
> O modelo de implementação Resource Manager não tem o conceito de clássico imagens e os discos. Quando a conta de armazenamento é migradas, clássicas imagens e discos não estão visíveis na pilha do Resource Manager, mas a cópia de segurança VHDs permanecem na conta de armazenamento.
>
>

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>Recursos desanexados (grupos de segurança de rede, as tabelas de rotas & IPs reservados)
Grupos de segurança de rede, as tabelas de rotas & IPs reservados que não estão ligados a qualquer máquinas virtuais e redes virtuais podem ser migradas separadamente.

<br>

## <a name="unsupported-features-and-configurations"></a>Configurações e funcionalidades não suportadas
Iremos suporta atualmente algumas funcionalidades e configurações. As secções seguintes descrevem os nossas recomendações em torno deles.

### <a name="unsupported-features"></a>Funcionalidades não suportadas
As seguintes funcionalidades não são atualmente suportadas. Pode, opcionalmente, remova estas definições, migrar as máquinas virtuais e, em seguida, volte a ativar as definições no modelo de implementação Resource Manager.

| Fornecedor de recursos | Funcionalidade | Recomendação |
| --- | --- | --- |
| Computação | Discos não associadas máquina virtual. | Os blobs VHD atrás destes discos serão obter migrados quando é migrada a conta de armazenamento |
| Computação | Imagens da máquina virtual. | Os blobs VHD atrás destes discos serão obter migrados quando é migrada a conta de armazenamento |
| Rede | ACLs de ponto final. | Remova as ACLs de ponto final e repita a migração. |
| Rede | Gateway de Aplicação | Remova o Gateway de aplicação antes de iniciar a migração e, em seguida, recriar o Gateway de aplicação depois de concluída a migração. |
| Rede | Redes virtuais utilizando o VNet Peering. | Migrar para o Gestor de recursos de rede Virtual e, em seguida, ponto. Saiba mais sobre [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). | 

### <a name="unsupported-configurations"></a>Configurações não suportadas
As seguintes configurações não são atualmente suportadas.

| Serviço | Configuração | Recomendação |
| --- | --- | --- |
| Resource Manager |Função de acesso baseado em controlo (RBAC) para os recursos clássicos |Porque o URI dos recursos é modificado após a migração, é recomendado que planeie as atualizações de política do RBAC que precisam para ocorrer após a migração. |
| Computação |Associado uma VM de várias sub-redes |Atualize a configuração de sub-rede para fazer referência apenas a sub-redes. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual, mas não tem uma sub-rede explícita atribuída |Pode, opcionalmente, elimine a VM. |
| Computação |Máquinas virtuais que possuem alertas, políticas de dimensionamento automático |A migração atravessa e estas definições são ignoradas. Recomenda-se vivamente que, de avaliar o seu ambiente antes de efetuar a migração. Em alternativa, pode reconfigurar as definições de alerta depois de concluída a migração. |
| Computação |Extensões de VM de XML (BGInfo 1.*, depurador do Visual Studio, Web Deploy e depuração remota) |Não é suportada. Recomenda-se que remova estas extensões da máquina virtual para continuar a migração ou serão removidos automaticamente durante o processo de migração. |
| Computação |Diagnóstico de arranque com o armazenamento Premium |Desative a funcionalidade de diagnóstico de arranque para as VMs antes de continuar com a migração. Pode reativar o diagnóstico de arranque na pilha do Resource Manager após a migração estar concluída. Além disso, os blobs que estão a ser utilizados para captura de ecrã e registos de série devem ser eliminados pelo que já não são cobradas para os blobs. |
| Computação | Serviços em nuvem que contêm funções da web/trabalho | Isto não é atualmente suportado. |
| Computação | Serviços em nuvem que contêm mais do que uma disponibilidade definiram ou conjuntos de disponibilidade vários. |Isto não é atualmente suportado. Mova as máquinas virtuais ao mesmo conjunto antes de migrar de disponibilidade. |
| Computação | VM com a extensão do Centro de segurança do Azure | Centro de segurança do Azure instala automaticamente as extensões na suas máquinas virtuais para monitorizar a segurança e gerar alertas. Estas extensões, normalmente, obterem instaladas automaticamente se a política do Centro de segurança do Azure está ativada na subscrição. Para migrar as máquinas virtuais, desative a política do Centro de segurança na subscrição que removerá o Centro de segurança monitorização extensão das máquinas virtuais. |
| Computação | VM com a extensão de cópia de segurança ou de instantâneo | Estas extensões são instaladas numa máquina Virtual configurada com o serviço de cópia de segurança do Azure. Para migrar estas máquinas virtuais, siga as orientações [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault).  |
| Rede |Redes virtuais que contêm máquinas virtuais e funções da web/trabalho |Isto não é atualmente suportado. Mova as funções da Web/trabalho a sua própria rede Virtual antes de migrar. Assim que a rede Virtual clássica é migrada, a rede Virtual do Gestor de recursos do migrados do Azure pode estar em modo de peering com a rede Virtual clássica para alcançar configuração semelhante como anteriormente.|
| Rede | Circuitos Expressroute clássicos |Isto não é atualmente suportado. Estes circuitos tem de ser migrados para o Azure Resource Manager antes de iniciar a migração de IaaS. Para obter mais informações sobre esta consulte [circuitos do ExpressRoute mover do clássico para o modelo de implementação Resource Manager](../articles/expressroute/expressroute-move.md).|
| Serviço de Aplicações do Azure |Redes virtuais que contêm os ambientes do App Service |Isto não é atualmente suportado. |
| O Azure HDInsight |Redes virtuais que contêm serviços do HDInsight |Isto não é atualmente suportado. |
| Serviços de ciclo de vida do Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais que são geridas pelos serviços de ciclo de vida de Dynamics |Isto não é atualmente suportado. |
| Azure AD Domain Services |Redes virtuais que contêm os serviços de domínio do Azure AD |Isto não é atualmente suportado. |
| Azure RemoteApp |Redes virtuais que contenham implementações de Azure RemoteApp |Isto não é atualmente suportado. |
| API Management do Azure |Redes virtuais que contenham implementações de API Management do Azure |Isto não é atualmente suportado. Para migrar a VNET IaaS, altere a VNET da implementação da API de gestão que não é uma operação nenhum período de indisponibilidade. |
