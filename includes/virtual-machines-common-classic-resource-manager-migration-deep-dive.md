## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>O que significa a migração de recursos de IaaS da implementação clássica para Resource Manager
Antes de aprofundar os detalhes, vamos ver a diferença entre as operações de planos de dados e de planos de gestão nos recursos de IaaS.

* O *plano de Gestão/Controlo* descreve as chamadas que entram no plano de gestão/controlo ou a API para modificar recursos. Por exemplo, operações como criar uma VM, reiniciar uma VM e atualizar uma rede virtual com uma nova sub-rede gerem os recursos em execução. Não afetam diretamente a ligação às instâncias.
* O *plano de dados* (aplicação) descreve o tempo de execução da aplicação propriamente dita e envolve a interação com instâncias que não passam pela API do Azure. Por exemplo, o acesso ao seu Web site ou a extração de dados de uma instância do SQL Server ou um servidor MongoDB em execução são consideradas interações de planos de dados ou aplicação. As cópias de blobs de uma conta de armazenamento e o acesso a endereços IP públicos para aceder por RDP ou SSH à máquina virtual também são considerados planos de dados. Estas operações mantêm a aplicação em execução em processos, redes e armazenamento.

![Captura de ecrã que ilustra a diferença entre o plano de gestão/controlo e o plano de dados](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> Em alguns cenários de migração, a plataforma Azure para, desaloca e reinicia as suas máquinas virtuais. Isto implica um curto período de indisponibilidade do plano de dados.
>
>

## <a name="the-migration-experience"></a>A experiência de migração
Antes de começar a experiência de migração, recomendamos o seguinte:

* Confirme que os recursos que quer migrar não utilizam funcionalidades ou configurações não suportadas. Normalmente, a plataforma deteta estes problemas e gera um erro.
* Se tiver VMs que não estejam numa rede virtual, estas vão ser interrompidas e desalocadas como parte da operação de preparação. Se não quiser perder o endereço IP público, considere reservar o endereço IP antes de acionar a operação de preparação. No entanto, se as VMs estiverem numa rede virtual, não são paradas nem desalocadas.
* Planeie a migração para ocorrer fora do horário de expediente, de modo a poder dar resposta a falhas inesperadas que possam surgir.
* Transfira a configuração atual das suas VMs com o PowerShell, os comandos da interface de linha de comandos (CLI) ou as APIs REST, de modo a facilitar a validação após a fase de preparação estar concluída.
* Atualize os scripts de automatização/operacionalização para processar o modelo de implementação Resource Manager antes de começar a migração. Opcionalmente, pode efetuar operações GET quando os recursos estão no estado pronto.
* Avalie as políticas de RBAC que estão configuradas nos recursos de IaaS clássicos e tenha um plano para quando a migração estiver terminada.

O fluxo de trabalho de migração é o seguinte

![Captura de ecrã que mostra o fluxo de trabalho de migração](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Todas as operações descritas nas secções seguintes são idempotent. Se tiver um erro que não esteja relacionado com uma funcionalidade ou configuração não suportada, recomendamos que repita a operação de preparação, aborto ou consolidação. A plataforma Azure repete a ação.
>
>

### <a name="validate"></a>Validação
A operação de validação é o primeiro passo do processo de migração. O objetivo deste passo é analisar os dados em segundo plano relativos aos recursos de migração e devolver êxito/falha se os mesmos puderem ser migrados.

Selecione a rede virtual ou o serviço alojado (se não for uma rede virtual) que quer validar para migração.

* Se o recurso não puder ser migrado, a plataforma Azure mostra todos os motivos pelos quais o mesmo não é suportado para migração.

Na validação de serviços de armazenamento, vai encontrar a conta migrada num grupo de recursos com o mesmo nome da sua conta de armazenamento, com “"-Migrated" acrescentado.  Por exemplo, se o nome da sua conta de armazenamento for "mystorage", o recurso ativado para o Azure Resource Manager vai estar num grupo de recursos chamado “mystorage-Migrated" e incluirá uma conta de armazenamento com o nome "mystorage".

### <a name="prepare"></a>Preparação
A operação de preparação é o segundo passo do processo de migração. O objetivo deste passo é simular a transformação dos recursos de IaaS de clássicos para recursos do Resource Manager e apresentá-los lado a lado, para que os visualize.

Selecione a rede virtual ou o serviço alojado (se não for uma rede virtual) que quer preparar para migração.

* Se o recurso não puder ser migrado, a plataforma Azure para o processo de migração e apresenta os motivos devido aos quais a operação de preparação falhou.
* Se o recurso puder ser migrado, a plataforma Azure bloqueia primeiro as operações de plano de gestão relativas aos recursos que estão a ser migrados. Por exemplo, não pode adicionar um disco de dados a uma VM em migração.

Depois, a plataforma Azure inicia a migração dos metadados do modelo clássico para Resource Manager dos recursos que estão a ser migrados.

Depois de concluída a operação de preparação, tem a opção de visualizar os recursos nos modelos clássico e Resource Manager. A plataforma Azure cria um nome de grupo de recursos, com o padrão `cloud-service-name>-Migrated`, para cada serviço cloud no modelo de implementação clássica.

> [!NOTE]
> Não é possível selecionar o nome do Grupo de Recursos criado para os recursos migrados (ou seja, "-Migrated"). Contudo, após a conclusão da migração, pode utilizar a funcionalidade de movimentação do Azure Resource Manager para mover os recursos para qualquer Grupo de Recursos que pretenda. Para obter mais informações, veja [Move resources to new resource group or subscription](../articles/resource-group-move-resources.md) (Mover recursos para um grupo de recursos novo u uma subscrição nova).

Seguem-se dois ecrãs que mostram o resultado após um operação de preparação bem-sucedida. O primeiro ecrã mostra um Grupo de Recursos que contém o serviço cloud original. O segundo mostra o grupo de recursos “-Migrated" novo que contém os recursos do Azure Resource Manager equivalentes.

![Captura de ecrã que mostra o serviço cloud clássico do portal](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Captura de ecrã que mostra recursos do Azure Resource Manager em Preparação no Portal](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

> [!NOTE]
> As Máquinas Virtuais que não estejam numa Rede Virtual clássica são paradas e desalocadas nesta fase da migração.
>
>

### <a name="check-manual-or-scripted"></a>Verificação (manual ou com script)
No passo de verificação, pode, opcionalmente, utilizar a configuração que transferiu anteriormente para verificar se a migração está correta. Em alternativa, pode iniciar sessão no portal e verificar as propriedades e os recursos, de modo a confirmar que a migração dos dados está certa.

Se estiver a migrar uma rede virtual, a maioria das configurações de máquinas virtuais não são reiniciadas. Para as aplicações nessas VMs, pode verificar se as mesmas continuam a funcionar.

Pode testar os scripts de monitorização/automatização e os scripts operacionais para ver se as VMs e os scrips atualizados estão a funcionar conforme esperado. Quando os recursos estão no estado pronto, só são suportadas operações OBTER.

Não há nenhum período de tempo definido antes do qual tenha de consolidar a migração. Pode permanecer neste estado o tempo que quiser. Contudo, o plano de gestão é bloqueado para estes recursos enquanto não abortar ou consolidar.

Se vir erros, pode sempre abortar a migração e regressar ao modelo de implementação clássica. Quando regressar a este modelo, a plataforma Azure desbloqueia as operações de plano de gestão nos recursos, para que possa retomar as operações normais nessas VMs no modelo de implementação clássica.

### <a name="abort"></a>Abortar
Abortar é um passo opcional que pode ser utilizado para reverter as alterações ao modelo de implementação clássica e parar a migração.

> [!NOTE]
> Se tiver acionado a operação de consolidação, esta operação não pode ser executada.     
>
>

### <a name="commit"></a>Consolidação
Depois de concluída a validação, pode consolidar a migração. Os recursos já não aparecem no modelo de implementação clássica e só estão disponíveis no modelo de implementação Resource Manager. Os recursos migrados só podem ser geridos no portal novo.

> [!NOTE]
> Esta é uma operação idempotent. Se falhar, recomenda-se que a repita. Se continuar a falhar, crie um pedido de suporte ou publique uma mensagem no nosso [fórum de VMs](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows), com a etiqueta ClassicIaaSMigration.
>
>
<br>
Eis uma fluxograma dos passos que ocorrem durante o processo de migração

![Captura de ecrã que mostra os passos da migração](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>Tradução de recursos da implementação clássica para Azure Resource Manager
Pode encontrar as representações clássica e do Resource Manager dos recursos na tabela abaixo: Atualmente, não são suportadas outras funcionalidades e recursos.

| Representação clássica | Representação do Resource Manager | Notas detalhadas |
| --- | --- | --- |
| Nome do serviço cloud |Nome DNS |Durante a migração, é criado um grupo de recursos novo para cada serviço cloud, com o padrão de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contém todos os seus recursos. O nome do serviço cloud converte-se um nome DNS que está associado ao endereço IP público. |
| Máquina virtual |Máquina virtual |As propriedades específicas de cada VM são migradas sem alterações. Determinadas informações de osProfile, como o nome do computador, não são armazenadas no modelo de implementação clássica e permanecem vazias após a migração. |
| Recursos de disco ligados à VM |Discos implícitos ligados à VM |Os discos não são modelados como recursos de nível superior no modelo de implementação Resource Manager. São migrados como discos implícitos na VM. Atualmente, apenas os discos que estão ligados a uma VM são suportados. As VMs do Resource Manager podem agora utilizar contas de armazenamento clássicas, o que permite migrar mais facilmente os discos sem necessidade de fazer atualizações. |
| Extensões de VM |Extensões de VM |Todas as extensões de recursos, exceto as extensões XML, são migradas do modelo de implementação clássica. |
| Certificados de máquinas virtuais |Certificados no Azure Key Vault |Se um serviço cloud contiver certificados de serviço, a plataforma cria um cofre de chaves do Azure novo por cada serviço cloud e move os certificados para esse cofre. As VMs são atualizadas, de modo a fazerem referência aos certificados no cofre de chaves. <br><br> **NOTA:** não elimine o cofre de chaves, pois poderá fazer com que a VM entre no estado de falha. Estamos a trabalhar para melhorar as coisas no back-end, para que os cofres de chaves possam ser eliminados em segurança ou movidos juntamente com a VM para uma subscrição nova. |
| Configuração WinRM |Configuração WinRM em osProfile |A configuração Gestão Remota do Windows é movida sem quaisquer alterações como parte da migração. |
| Propriedade Availability-set |Recurso Availability-set | A especificação availability-set era uma propriedade das VMs no modelo de implementação clássica. Como parte da migração, os conjuntos de disponibilidade convertem-se num recurso de nível superior. As configurações seguintes não são suportadas: múltiplos conjuntos de disponibilidade por serviço cloud, um ou mais conjuntos de disponibilidade juntamente com VMs que não estão em nenhum conjunto de disponibilidade num serviço cloud. |
| Configuração de rede numa VM |Interface de rede primária |A configuração de rede numa VM é representada como o recurso de interface de rede primária após a migração. Nas VMs que não estão numa rede virtual, o endereço IP é alterado durante a migração. |
| Várias interfaces de rede numa VM |Interfaces de rede |Se uma VM tiver várias interfaces de rede associadas, cada interface é convertida num recurso de nível superior como parte da migração no modelo de implementação Resource Manager, juntamente com todas as propriedades. |
| Conjunto de pontos finais com balanceamento de carga |Load balancer |No modelo de implementação clássica, a plataforma atribuía um balanceador de carga implícito a cada serviço cloud. Durante a migração, é criado um recurso de balanceador de carga novo e o conjunto de pontos finais com balanceamento de carga converte-se em regras de balanceador de carga. |
| Regras NAT de entrada |Regras NAT de entrada |Os pontos finais de entrada definidos na VM são convertidos em regras de tradução de endereços de rede de entrada no balanceador de carga durante a migração. |
| Endereço VIP |Endereço IP público com o nome DNS |O endereço IP virtual converte-se num endereço IP público e é associado ao balanceador de carga. Os IPs virtuais só podem ser migrados se lhes tiverem sido atribuídos pontos finais de entrada. |
| Rede virtual |Rede virtual |A rede virtual é migrada, com todas as propriedades, para o modelo de implementação Resource Manager. É criado um grupo de recursos novo com o nome `-migrated`. |
| IPs Reservados |Endereço IP público com o método de alocação estática |Os IPs Reservados associados ao balanceador de carga são migrados, juntamente com a migração do serviço cloud ou da máquina virtual. Atualmente, não é suportada a migração de IPs reservados. |
| Endereço IP público por VM |Endereço IP público com o método de alocação dinâmica |O endereço IP público associado à VM é convertido num recurso de endereço IP público, com o método de alocação definido como estático. |
| NSGs |NSGs |Os grupos de segurança de rede associados a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O NSG no modelo de implementação clássica não é removido durante a migração. No entanto, as operações do plano de gestão para o NSG são bloqueadas enquanto a migração está em curso. |
| Servidores DNS |Servidores DNS |Os servidores DNS associados a uma rede virtual ou à VM são migrados como parte da migração de recursos correspondente, juntamente com todas as propriedades. |
| UDRs |UDRs |As rotas definidas pelo utilizador associadas a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O URD no modelo de implementação clássica não é removido durante a migração. As operações do plano de gestão para o UDR são bloqueadas enquanto a migração está em curso. |
| Propriedade de reencaminhamento de IPs na configuração de rede de uma VM |Propriedade de reencaminhamento de IPs na NIC |A propriedade de reencaminhamento de IPs numa VM é convertida numa propriedade na interface de rede durante a migração. |
| Balanceador de carga com vários IPs |Balanceador de carga com vários recursos de IP público |Cada IP público associado ao balanceador de carga é convertido num recurso de IP público e associado ao balanceador de carga após a migração. |
| Nomes DNS internos na VM |Nomes DNS internos na NIC |Durante a migração, os sufixos de DNS internos das VMs são migrados para uma propriedade só de leitura com o nome “InternalDomainNameSuffix” na NIC. Os sufixos permanecem inalterados após a migração e a resolução das VMs deverá continuar a funcionar como antes. |
| Gateway de Rede Virtual |Gateway de Rede Virtual |As propriedades do Gateway de Rede Virtual são migradas sem alterações. O VIP associado ao gateway também não muda. |
| Site de rede local |Gateway de Rede Local |As propriedades de site de rede local são migradas sem alterações para um recurso novo com o nome Gateway de Rede Local. Este representa os prefixos de endereços no local e o IP do gateway remoto. |
| Referências de ligações |Ligação |As referências de conectividade entre o gateway e o site de rede local na configuração de rede são representadas por um recurso criado recentemente, com o nome ligação, no Resource Manager após a migração. Todas as propriedades da referência de conectividade nos ficheiros de configuração de rede são copiadas sem alterações para o recurso Ligação criado recentemente. A conectividade VNet a VNet no modelo de implementação clássica é obtida mediante a criação de dois túneis IPsec para os sites de rede local que representam as VMs. Isto é transformado no tipo de ligação Vnet2Vnet no modelo Resource Manager sem que sejam necessários gateways de rede locais. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações à automatização e às ferramentas após a migração
Como parte da migração dos seus recursos a partir do modelo de implementação Clássica para o Resource Manager, tem de atualizar a automatização ou as ferramentas atuais, para garantir que continuam a funcionar depois da migração.
