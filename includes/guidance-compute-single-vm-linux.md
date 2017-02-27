Este artigo descreve um conjunto de práticas comprovadas para executar máquinas virtuais (VMs) do Linux no Azure, tendo em atenção a escalabilidade, a disponibilidade, a gestão e a segurança. O Azure suporta a execução de várias distribuições Linux populares, incluindo CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Para obter mais informações, veja [Azure and Linux (O Azure e o Linux)][azure-linux].

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes, o [Resource Manager][resource-manager-overview] e a implementação clássica. Este artigo utiliza o Resource Manager, que é recomendação da Microsoft para implementações novas.
> 
> 

Não recomendamos utilizar uma única VM para cargas de trabalho críticas de missões, porque só é criado um único ponto de falha. Para maior disponibilidade, implemente várias VMs num [conjunto de disponibilidade][availability-set]. Para obter mais informações, veja [Running multiple VMs on Azure (Executar várias VMs no Azure)][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama da arquitetura
O aprovisionamento de uma VM no Azure envolve mais elementos móveis do que apenas a VM propriamente dita. Tem também de considerar elementos de computação, de rede e de armazenamento.

> Está disponível para transferência no [Centro de Transferências da Microsoft][visio-download] um documento do Visio que inclui este diagrama de arquitetura. O diagrama está na página “Compute - single VM” (“Computação - VM única”).
> 
> 

![[0]][0]

* **Grupo de recursos.** Um [*grupo de recursos*][resource-manager-overview] é um contentor que retém recursos relacionados. Crie um grupo de recursos para conter os recursos desta VM.
* **VM**. Pode aprovisionar uma VM a partir de uma lista de imagens publicadas ou de um ficheiro de disco rígido virtual (VHD), que é carregado para o Armazenamento de Blobs do Azure.
* **Disco do SO.** O disco do SO é um VHD armazenado no [Armazenamento do Azure][azure-storage]. Isto significa que permanece disponível mesmo que a máquina anfitriã esteja em baixo. O disco do SO é `/dev/sda1`.
* **Disco temporário.** A VM é criada com um disco temporário. Este disco é armazenado numa unidade física na máquina anfitriã. *Não* é guardado no Armazenamento do Azure e pode ser eliminado durante os reinícios e outros eventos do ciclo de vida das VMs. Utilize este disco apenas para dados temporários, tais como ficheiros de paginação ou de troca. O disco temporário é `/dev/sdb1` e está montado em `/mnt/resource` ou em `/mnt`.
* **Discos de dados.** Um [disco de dados][data-disk] é um VHD persistente utilizado para dados de aplicações. Os discos de dados são armazenados no Armazenamento do Azure, como o disco do SO.
* **Rede virtual (VNet) e sub-rede.** Todas as VMs no Azure são implementadas numa VNet, a qual é dividida em sub-redes.
* **Endereço IP público.** É necessário um endereço IP público para comunicar com a VM &mdash; por exemplo, através de SSH.
* **Interface de rede (NIC).** A NIC permite à VM comunicar com a rede virtual.
* **Grupo de Segurança de Rede (NSG).** O [NSG][nsg] é utilizado para permitir/recusar tráfego de rede para a sub-rede. Pode associar um NSG a uma NIC individual ou a uma sub-rede. Se associar a uma sub-rede, as regras do NSG aplicam-se a todas as VMs nessa sub-rede.
* **Diagnósticos.** O registo de diagnósticos é fundamental para gerir e resolver problemas de VMs.

## <a name="recommendations"></a>Recomendações

As recomendações seguintes aplicam-se à maioria dos cenários. Siga-as, a não ser que tenha requisitos específicos que as anulem. 

### <a name="vm-recommendations"></a>Recomendações de VMs

O Azure oferece muitos tamanhos de máquinas virtuais diferentes, mas recomendamos as séries DS- e GS-, pois estes tamanhos suportam o [Armazenamento Premium][premium-storage]. Selecione um dos seguintes tamanhos de máquina, a menos que tenha uma carga de trabalho especializada, como computação de alto desempenho. Para obter mais detalhes, veja [virtual machine sizes (tamanhos de máquinas virtuais)][virtual-machine-sizes].

Se estiver a mover uma carga de trabalho existente para o Azure, comece com o tamanho de VM que melhor corresponda aos seus servidores no local. Em seguida, meça o desempenho da carga de trabalho real relativamente a CPU, memória e operações de entrada/saída de dados por segundo (IOPS) e, se necessário, ajuste o tamanho. Se a sua VM precisar de várias NICs, tenha em atenção que o número máximo de NICs é uma função do [tamanho da VM][vm-size-tables].

Quando aprovisiona a VM e outros recursos, tem de especificar uma região. Geralmente, deve escolher a região que está mais perto dos seus utilizadores internos ou clientes. No entanto, nem todos os tamanhos de VM poderão estar disponíveis em todas as regiões. Para obter detalhes, veja [Serviços por região][services-by-region]. Para listar os tamanhos de VM disponíveis numa determinada região, execute o seguinte comando da interface de linha de comandos (CLI) do Azure:

```
azure vm sizes --location <location>
```

Para obter informações sobre como escolher uma imagem de VM publicada, veja [Select Linux VM images with the Azure CLI (Selecionar imagens de VM do Linux com a CLI do Azure)][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendações de disco e armazenamento

Para um melhor desempenho de E/S, recomendamos o [Armazenamento Premium][premium-storage], que armazena os dados em unidades de estado sólido (SSDs). O custo tem por base o tamanho do disco aprovisionado. O IOPS e o débito (ou seja, a velocidade de transferência de dados) também dependem do tamanho do disco, pelo que, quando aprovisionar um disco, considere os três fatores (capacidade, IOPS e débito). 

Crie contas de armazenamento do Azure separadas para cada VM e conter os discos rígidos virtuais (VHDs), de modo a evitar atingir os limites de IOPS das contas de armazenamento. 

Adicione um ou mais discos de dados. Quando cria um VHD, os discos de dados não estão formatados. Inicie sessão na VM para formatá-los. Na shell do Linux, os discos de dados são apresentados como `/dev/sdc`, `/dev/sdd` e assim sucessivamente. Pode executar `lsblk` para listar os dispositivos de bloqueio, incluindo os discos. Para utilizar um disco de dados, crie uma partição e um sistema de ficheiros e monte o disco. Por exemplo:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Se tiver um grande número de discos de dados, tenha em consideração os limites de E/S totais da conta de armazenamento. Para obter mais informações, veja [Virtual machine disk limits (Limites dos discos de máquinas virtuais)][vm-disk-limits].

Quando adiciona um disco de dados, é-lhe atribuído um número de unidade lógica (LUN). Opcionalmente, pode especificar o ID do LUN &mdash; por exemplo, se estiver a substituir um disco e quiser manter o mesmo ID de LUN ou se tiver uma aplicação que procura um ID de LUN específico. Contudo, lembre-se de que os IDs de LUNs têm de ser exclusivos para cada disco.

Poderá ser útil alterar o agendador de E/S para otimizar o desempenho dos SSDs, pois os discos das VMs com contas de armazenamento premium são SSDs. Uma recomendação comum é utilizar o agendador NOOP para os SSDs, mas deve utilizar uma ferramenta como [iostat] para monitorizar o desempenho de E/S do disco da sua carga de trabalho específica.

Para um melhor desempenho, crie uma conta de armazenamento separada para conter os registos de diagnósticos. Para conter os registos de diagnósticos, é suficiente uma conta de armazenamento localmente redundante (LRS) standard.

### <a name="network-recommendations"></a>Recomendações de rede

O endereço IP público pode ser dinâmico ou estático. Por predefinição, é dinâmico.

* Se precisar de um endereço IP fixo que não se vai alterar, reserve um [endereço IP estático][static-ip] &mdash; por exemplo, se tiver de criar um registo A no DNS ou precisar de adicionar o endereço IP a uma lista de segurança.
* Também pode criar um nome de domínio completamente qualificado (FQDN) para o endereço IP. Depois, pode registar um [registo CNAME][cname-record] no DNS que aponte para o FQDN. Para obter mais informações, veja [Create a fully qualified domain name in the Azure portal (Criar um nome de domínio completamente qualificado no portal do Azure)][fqdn].

Todos os NSGs contêm um conjunto de [regras predefinidas][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de entrada da Internet. Não é possível eliminar as regras predefinidas, mas estas podem ser substituídas por outras. Para permitir o tráfego da Interne, crie regras que permitam o tráfego de entrada em portas específicas &mdash; por exemplo, a porta 80 para HTTP.  

Para ativar o SSH, adicione uma regra ao NSG que permita o tráfego de entrada para a porta TCP 22.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Para aumentar ou diminuir verticalmente, [altere o tamanho da VM][vm-resize]. 

Para aumentar horizontalmente, ponha duas ou mais VMs num conjunto de disponibilidade por trás de um balanceador de carga. Para obter mais detalhes, veja [Running multiple VMs on Azure (Executar várias VMs no Azure)][multi-vm].

## <a name="availability-considerations"></a>Considerações de disponibilidade

Para maior disponibilidade, implemente várias VMs num conjunto de disponibilidade. Isto também oferece um [contrato de nível de serviço][vm-sla] (SLA) maior. 

A sua VM pode ser afetada por [manutenções planeadas][planned-maintenance] ou por [manutenções não planeadas][manage-vm-availability]. Pode utilizar [registos de reinício de VM][reboot-logs] para determinar se o reinício de uma VM foi provocado por uma manutenção planeada.

Os VHDs são armazenados no [Armazenamento do Azure][azure-storage], o qual é replicado para fins de durabilidade e disponibilidade.

Para proteger de perdas de dados acidentais durante as operações normais (por exemplo, devido a erros dos utilizadores), deve também implementar cópias de segurança para um ponto anterior no tempo com [instantâneos de blob][blob-snapshot] ou com outra ferramenta.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

**Grupos de recursos.** Coloque recursos estreitamente ligados que partilhem o mesmo ciclo de vida no mesmo [grupo de recursos][resource-manager-overview]. Os grupos de recursos permitem-lhe implementar e monitorizar recursos como um grupo e agregar os custos de faturação por grupo de recursos. Também pode eliminar recursos como um conjunto, o que é muito útil para implementações de teste. Dê nomes significativos aos recursos. Desta forma, é mais fácil localizar recursos específicos e compreender a função dos mesmos. Veja [Recommended Naming Conventions for Azure Resources (Convenções de Nomenclatura Recomendadas para Recursos do Azure)][naming conventions].

**SSH**. Antes de criar uma VM do Linux, gere um par de chaves públicas-privadas de RSA 2048 bits. Utilize o ficheiro de chave pública quando criar a VM. Para obter mais informações, veja [Como utilizar SSG com Linux e Mac no Azure][ssh-linux].

**Diagnósticos de VMs.** Ative a monitorização e os diagnósticos, incluindo métricas básicas de estado de funcionamento, registos de infraestrutura de diagnósticos e [diagnósticos de arranque][boot-diagnostics]. Os diagnósticos de arranque podem ajudá-lo a diagnosticar falhas no arranque se a sua VM estiver no estado “não arrancável”. Para obter mais informações, veja [Enable monitoring and diagnostics (Ativar a monitorização e os diagnósticos)][enable-monitoring].  

O comando seguinte da CLI ativa os diagnósticos:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Parar uma VM.** O Azure faz uma distinção entre os estados “parada” e “desalocada”. Se o estado da VM for "parada", será cobrado, mas não se for "desalocada".

Para desalocar VMs, utilize o comando seguinte da CLI:

```
azure vm deallocate <resource-group> <vm-name>
```

No portal do Azure, o botão **Parar** desaloca a VM. No entanto, se encerrar com o SO enquanto tiver sessão iniciada, a VM é parada, mas *não* desalocada, pelo que continua a ser cobrado.

**Eliminar uma VM.** Se eliminar uma VM, os VHDs não são eliminados. Isto significa que pode eliminar em segurança a VM sem perder dados. No entanto, ainda lhe será cobrado o armazenamento. Para eliminar o VHD, elimine o ficheiro do [Armazenamento de blobs][blob-storage].

Para impedir a eliminação acidental, utilize um [bloqueio de recursos][resource-lock] para bloquear o grupo de recursos inteiro ou bloqueie recursos individuais, como a VM. 

## <a name="security-considerations"></a>Considerações de segurança

Utilize a extensão de VM [OSPatching] para automatizar as atualizações do SO. Instale esta extensão quando aprovisionar a VM. Pode especificar a frequência de instalação das correções e se a VM deve ser reiniciada após a aplicação das mesmas.

Utilize o [controlo de acesso baseado em funções][rbac] (RBAC) para controlar o acesso aos recursos do Azure que implementar. O RBAC permite-lhe atribuir funções de autorização a membros da sua equipa de DevOps. Por exemplo, a Função Leitor pode ver recursos do Azure, mas não criá-los, geri-los nem eliminá-los. Algumas funções são específicas de determinados tipos de recursos do Azure. Por exemplo, a função Contribuidor de Máquina Virtual pode reiniciar ou desalocar uma VM, repor a palavra-passe de administrador, criar uma VM e assim sucessivamente. Outras [funções RBAC incorporadas][rbac-roles] que podem ser úteis para esta arquitetura de referência incluem [Utilizador de DevTest Labs][rbac-devtest] e [Contribuidor de Rede][rbac-network]. 

Um utilizador pode ser atribuído a várias funções e pode criar funções personalizadas para obter permissões ainda mais detalhadas.

d> [!NOTE]
> O RBAC não limita as ações que os utilizadores que tenham sessão iniciada numa VM podem fazer. Estas permissões são determinadas pelo tipo de conta no SO convidado.   
> 
> 

Utilize [registos de auditoria][audit-logs] para ver ações de aprovisionamento e outros eventos da VM.

Se precisar de encriptar o SO e os discos de dados, considere a [Azure Disk Encryption][disk-encryption]. 

## <a name="solution-deployment"></a>Implementação de solução
Está disponível uma implementação para esta arquitetura de referência no [GitHub][github-folder]. Inclui uma VNet, um NSG e uma VM individual. Para implementar a arquitetura, siga estes passos: 

1. Clique com o botão direito do rato no botão abaixo e selecione “Abrir ligação num novo separador” ou “Abrir ligação numa nova janela”.
   [![Implementar no Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Quando a ligação estiver aberta no portal do Azure, tem de introduzir valores para algumas das definições: 
   
   * O nome do **Grupo de recursos** já está definido no ficheiro de parâmetros, por isso, selecione **Criar Novo** e introduza `ra-single-vm-rg` na caixa de texto.
   * Selecione a região na caixa pendente **Localização**.
   * Não edite as caixas de texto **Uri da Raiz de Modelo** nem **Uri da Raiz de Parâmetro**.
.   * Selecione **linux** na caixa pendente **Tipo de SO**.
   * Reveja os termos e condições e clique na caixa de seleção **Aceito os termos e condições acima apresentados**.
   * Clique no botão **Comprar**.
3. Aguarde pela conclusão da implementação.
4. Os ficheiros de parâmetros incluem um nome de utilizador e palavra-passe de administrador codificados e recomenda-se vivamente que os altere a ambos de imediato. Clique na VM com o nome `ra-single-vm0 ` no portal do Azure. Em seguida, clique em **Repor palavra-passe**, na secção **Suporte + resolução de problemas**. Selecione **Repor palavra-passe**, na caixa pendente **Modo**, e selecione um **nome de utilizador** e uma **palavra-passe** novos. Clique no botão **Atualizar** para manter o nome de utilizador e a palavra-passe novos.

## <a name="next-steps"></a>Passos seguintes
Para maior disponibilidade, implemente duas ou mais VMs por trás de um balanceador de carga. Para obter mais informações, veja [Running multiple VMs on Azure (Executar várias VMs no Azure)][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-size-tables]: ../articles/virtual-machines/virtual-machines-windows-sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Single Linux VM architecture in Azure (Arquitetura de VM do Linux individual no Azure)"



<!--HONumber=Feb17_HO3-->


