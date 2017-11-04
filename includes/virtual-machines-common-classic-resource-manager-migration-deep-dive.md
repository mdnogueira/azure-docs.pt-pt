## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrar os recursos IaaS do modelo de implementação clássica para o Azure Resource Manager
Em primeiro lugar, é importante compreender a diferença entre operações plane de dados e gestão plane na infraestrutura como um recursos de serviço (IaaS).

* *Plane gestão/controlo* descreve as chamadas que entrem em plane o controlo de gestão/ou a API para modificar os recursos. Por exemplo, operações como criar uma VM, reiniciar uma VM e atualizar uma rede virtual com uma nova sub-rede gerem os recursos em execução. Não diretamente afetam a ligação às VMs.
* *Plane dados* (aplicação) descreve o tempo de execução da aplicação em si e envolve a interação com instâncias que não podem passar através da API do Azure. Por exemplo, ao aceder ao seu Web site ou extrair dados a partir de uma instância do SQL Server em execução ou de um servidor MongoDB, é dados interações plane ou da aplicação. Outros exemplos incluem a copiar um blob de uma conta de armazenamento e aceder a um endereço IP público a utilizar o protocolo RDP (Remote Desktop Protocol) ou de Secure Shell (SSH) para a máquina virtual. Estas operações mantêm a aplicação em execução em processos, redes e armazenamento.

O plane de dados é o mesmo entre o modelo de implementação clássica e Resource Manager pilhas. A diferença é que, durante o processo de migração, Microsoft traduz a representação de recursos do modelo de implementação clássica para que a pilha do Resource Manager. Como resultado, terá de utilizar novas ferramentas, APIs e SDKs para gerir os recursos na pilha do Resource Manager.

![Diagrama que mostra a diferença entre plane/controlo de gestão e plane de dados](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Em alguns cenários de migração, a plataforma Azure para, desaloca e reinicia as suas máquinas virtuais. Isto faz com que um breve tempo de inatividade plane de dados.
>

## <a name="the-migration-experience"></a>A experiência de migração
Antes de começar a migração:

* Confirme que os recursos que quer migrar não utilizam funcionalidades ou configurações não suportadas. Normalmente, a plataforma deteta estes problemas e gera um erro.
* Se tiver VMs que não estão numa rede virtual, estão parados e desalocadas como parte da operação de preparação. Se não quiser perder o endereço IP público, considere reservar o endereço IP antes de acionar a operação de preparação. Se as VMs numa rede virtual, não são parados e desalocadas.
* Planeie a migração para ocorrer fora do horário de expediente, de modo a poder dar resposta a falhas inesperadas que possam surgir.
* Transfira a configuração atual das suas VMs com o PowerShell, os comandos da interface de linha de comandos (CLI) ou as APIs REST, de modo a facilitar a validação após a fase de preparação estar concluída.
* Atualize os scripts de automatização e operationalization para processar o modelo de implementação do Resource Manager, antes de começar a migração. Opcionalmente, pode efetuar operações GET quando os recursos estão no estado pronto.
* Avalie as políticas de controlo de acesso baseado em funções (RBAC) que são configuradas nos recursos IaaS no modelo de implementação clássica e planear após a migração estar concluída.

O fluxo de trabalho de migração é o seguinte:

![Diagrama que mostra o fluxo de trabalho de migração](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> As operações descritas nas secções seguintes são idempotent todos os. Se tiver um problema que não seja uma funcionalidade não suportada ou um erro de configuração, tente novamente a preparar abortar ou consolidar a operação. Azure tenta novamente a ação.
>
>

### <a name="validate"></a>Validação
A operação de validação é o primeiro passo do processo de migração. O objetivo deste passo é analisar o estado dos recursos que pretende migrar no modelo de implementação clássica. A operação avalia se os recursos são capazes de migração (êxito ou falha).

Selecione a rede virtual ou um serviço em nuvem (se não estiver numa rede virtual) que pretende validar para migração. Se o recurso não for capaz de migração, Azure apresenta uma lista de motivos por que motivo.

#### <a name="checks-not-done-in-the-validate-operation"></a>Não é feitas na operação de validar as verificações

A operação de validar analisa apenas o estado dos recursos no modelo de implementação clássica. Pode verificar a para todas as falhas e cenários não suportados devido a várias configurações no modelo de implementação clássica. Não é possível verificar a existência de todos os problemas que a pilha do Azure Resource Manager pode impõe nos recursos durante a migração. Estes problemas são verificados apenas quando os recursos são submetidos a transformação no próximo passo de migração (operação preparar). A tabela seguinte apresenta uma lista de todos os problemas não verificados na operação de validar:


|Verificações de redes não na operação de validar|
|-|
|Uma rede virtual com gateways de ER e VPN.|
|Uma ligação de gateway de rede virtual no estado desligado.|
|Todos os circuitos de ER previamente são migrados para a pilha do Azure Resource Manager.|
|Quota de Gestor de recursos do Azure verifica a existência de recursos de rede. Por exemplo: IP público estático, os IPs públicos dinâmicos, carregar balanceador, grupos de segurança de rede, as tabelas de rotas e interfaces de rede. |
| Todas as regras de Balanceador de carga são válidas em implementação e a rede virtual. |
| IPs privados em conflito entre stop-desalocada VMs na mesma rede virtual. |

### <a name="prepare"></a>Preparação
A operação de preparação é o segundo passo do processo de migração. O objetivo deste passo é simular a transformação dos recursos do IaaS do modelo de implementação clássica para recursos do Resource Manager. Além disso, a operação de preparação apresenta esta lado lado a lado para que possa visualizar.

> [!NOTE] 
> Os recursos no modelo de implementação clássica não são modificados durante este passo. É um passo seguro para executar se estiver a tentar terminar a migração. 

Selecionar a rede virtual ou o serviço em nuvem (se não se trata de uma rede virtual) que pretende preparar a migração.

* Se o recurso não for capaz de migração, o Azure interrompe o processo de migração e lista o motivo por que falhou a operação de preparação.
* Se o recurso é capaz de migração, o Azure bloqueia baixo as operações de gestão plane para os recursos em migração. Por exemplo, não pode adicionar um disco de dados a uma VM em migração.

Azure, em seguida, inicia a migração dos metadados do modelo de implementação clássica para o Gestor de recursos para a migração de recursos.

Depois de concluída a operação de preparação, tem a opção de visualizar os recursos no modelo de implementação clássica e Resource Manager. A plataforma Azure cria um nome de grupo de recursos, com o padrão `cloud-service-name>-Migrated`, para cada serviço cloud no modelo de implementação clássica.

> [!NOTE]
> Não é possível selecionar o nome de um grupo de recursos criado para os recursos migrados (ou seja, "-migrados"). No entanto, após a conclusão da migração, pode utilizar a funcionalidade de movimentação do Gestor de recursos do Azure para mover recursos para qualquer grupo de recursos que pretende. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../articles/resource-group-move-resources.md).

As capturas de ecrã de dois seguintes mostram o resultado depois de preparar um efetuado com êxito a operação. Primeiro mostra um grupo de recursos que contém o serviço em nuvem original. Segunda mostra o novo "-migrados" grupo de recursos que contém os recursos do Azure Resource Manager equivalente.

![Captura de ecrã que mostra o serviço em nuvem original](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Captura de ecrã que mostra os recursos do Azure Resource Manager na operação de preparação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Segue-se em segundo plano ver os recursos após a conclusão da fase de preparação. Tenha em atenção que o recurso a plane de dados é o mesmo. Este é representado na plane a gestão (modelo de implementação clássica) e o plane de controlo (Resource Manager).

![Diagrama da fase de preparação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> As VMs que não estão numa rede virtual no modelo de implementação clássica são paradas e desalocadas nesta fase da migração.
>

### <a name="check-manual-or-scripted"></a>Verificação (manual ou com script)
O passo de verificação, tem a opção para utilizar a configuração do que transferiu anteriormente para validar que a migração procura correta. Em alternativa, pode iniciar sessão portal e verificação lugar para cima as propriedades e recursos para validar que a migração de metadados parece bom.

Se estiver a migrar uma rede virtual, a maioria das configurações de máquinas virtuais não são reiniciadas. Para aplicações nessas VMs, pode validar que a aplicação ainda está em execução.

Pode testar os scripts de monitorização e operacionais para ver se as VMs estão a funcionar conforme esperado e se os scripts atualizados funcionem corretamente. Quando os recursos estão no estado pronto, só são suportadas operações OBTER.

Não há nenhuma janela de conjunto de tempo antes de que tem de consolidar a migração. Pode permanecer neste estado o tempo que quiser. Contudo, o plano de gestão é bloqueado para estes recursos enquanto não abortar ou consolidar.

Se vir erros, pode sempre abortar a migração e regressar ao modelo de implementação clássica. Depois de voltar atrás, Azure abre-se as operações de gestão plane nos recursos, para que possa retomar as operações normais nessas VMs no modelo de implementação clássica.

### <a name="abort"></a>Abortar
Este passo é opcional se pretender reverter as alterações ao modelo de implementação clássica e parar a migração. Esta operação elimina os metadados do Gestor de recursos (criada no passo de preparação) para os seus recursos. 

![Diagrama do passo de abortar](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Não é possível efetuar esta operação depois de ter ativada a operação de consolidação.     
>

### <a name="commit"></a>Consolidação
Depois de concluída a validação, pode consolidar a migração. Recursos não este aparecer no modelo de implementação clássica e só estão disponíveis no modelo de implementação Resource Manager. Os recursos migrados só podem ser geridos no portal novo.

> [!NOTE]
> Esta é uma operação idempotent. Se falhar, repita a operação. Se continuar a falhar, crie um pedido de suporte ou criar uma mensagem num fórum com um "ClassicIaaSMigration" tag nosso [fórum VM](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagrama do passo de consolidação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Fluxograma de migração

Eis uma fluxograma que mostra como pode continuar com a migração:

![Captura de ecrã que mostra os passos da migração](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Tradução do modelo de implementação clássica para recursos do Resource Manager
Pode encontrar o modelo de implementação clássica e Resource Manager representações dos recursos na seguinte tabela. Atualmente, não são suportadas outras funcionalidades e recursos.

| Representação clássica | Representação do Resource Manager | Notas |
| --- | --- | --- |
| Nome do serviço cloud |Nome DNS |Durante a migração, é criado um grupo de recursos novo para cada serviço cloud, com o padrão de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contém todos os seus recursos. O nome do serviço cloud converte-se um nome DNS que está associado ao endereço IP público. |
| Máquina virtual |Máquina virtual |As propriedades específicas de cada VM são migradas sem alterações. Determinadas informações de osProfile, como o nome do computador, não são armazenadas no modelo de implementação clássica e permanecem vazias após a migração. |
| Recursos de disco ligados à VM |Discos implícitos ligados à VM |Os discos não são modelados como recursos de nível superior no modelo de implementação Resource Manager. São migrados como discos implícitos na VM. Atualmente, apenas os discos que estão ligados a uma VM são suportados. VMs do Gestor de recursos podem agora utilizar as contas do storage no modelo de implementação clássica, o que permite que os discos facilmente ser migrados sem quaisquer atualizações. |
| Extensões de VM |Extensões de VM |Todas as extensões de recursos, exceto as extensões XML, são migradas do modelo de implementação clássica. |
| Certificados de máquinas virtuais |Certificados no Azure Key Vault |Se um serviço em nuvem contém os certificados de serviço, a migração cria um novo cofre de chaves do Azure por serviço em nuvem e move os certificados para o Cofre de chaves. As VMs são atualizadas, de modo a fazerem referência aos certificados no cofre de chaves. <br><br> Não elimine o Cofre de chaves. Isto pode fazer com que a VM entrar em estado de falha. |
| Configuração WinRM |Configuração WinRM em osProfile |A configuração Gestão Remota do Windows é movida sem quaisquer alterações como parte da migração. |
| Propriedade Availability-set |Recurso Availability-set | Especificação de conjunto de disponibilidade é uma propriedade da VM no modelo de implementação clássica. Como parte da migração, os conjuntos de disponibilidade convertem-se num recurso de nível superior. As configurações seguintes não são suportadas: múltiplos conjuntos de disponibilidade por serviço cloud, um ou mais conjuntos de disponibilidade juntamente com VMs que não estão em nenhum conjunto de disponibilidade num serviço cloud. |
| Configuração de rede numa VM |Interface de rede primária |A configuração de rede numa VM é representada como o recurso de interface de rede primária após a migração. Nas VMs que não estão numa rede virtual, o endereço IP é alterado durante a migração. |
| Várias interfaces de rede numa VM |Interfaces de rede |Se uma VM tem várias interfaces de rede associadas, cada interface de rede torna-se um recurso de nível superior como parte da migração, juntamente com todas as propriedades. |
| Conjunto de pontos finais com balanceamento de carga |Load balancer |No modelo de implementação clássica, a plataforma atribuía um balanceador de carga implícito a cada serviço cloud. Durante a migração, é criado um recurso de balanceador de carga novo e o conjunto de pontos finais com balanceamento de carga converte-se em regras de balanceador de carga. |
| Regras NAT de entrada |Regras NAT de entrada |Os pontos finais de entrada definidos na VM são convertidos em regras de tradução de endereços de rede de entrada no balanceador de carga durante a migração. |
| Endereço VIP |Endereço IP público com o nome DNS |O endereço IP virtual torna-se de um endereço IP público e está associado ao balanceador de carga. Os IPs virtuais só podem ser migrados se lhes tiverem sido atribuídos pontos finais de entrada. |
| Rede virtual |Rede virtual |A rede virtual é migrada, com todas as propriedades, para o modelo de implementação Resource Manager. É criado um grupo de recursos novo com o nome `-migrated`. |
| IPs Reservados |Endereço IP público com o método de alocação estática |Os IPs Reservados associados ao balanceador de carga são migrados, juntamente com a migração do serviço cloud ou da máquina virtual. Atualmente, não é suportada a migração de IPs reservados. |
| Endereço IP público por VM |Endereço IP público com o método de alocação dinâmica |O endereço IP público associado à VM é convertido num recurso de endereço IP público, com o método de alocação definido como estático. |
| NSGs |NSGs |Os grupos de segurança de rede associados a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O NSG no modelo de implementação clássica não é removido durante a migração. No entanto, as operações do plano de gestão para o NSG são bloqueadas enquanto a migração está em curso. |
| Servidores DNS |Servidores DNS |Os servidores DNS associados a uma rede virtual ou à VM são migrados como parte da migração de recursos correspondente, juntamente com todas as propriedades. |
| UDRs |UDRs |As rotas definidas pelo utilizador associadas a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O URD no modelo de implementação clássica não é removido durante a migração. As operações do plano de gestão para o UDR são bloqueadas enquanto a migração está em curso. |
| Propriedade de reencaminhamento de IPs na configuração de rede de uma VM |Propriedade de reencaminhamento de IPs na NIC |A propriedade de reencaminhamento de IPs numa VM é convertida numa propriedade na interface de rede durante a migração. |
| Balanceador de carga com vários IPs |Balanceador de carga com vários recursos de IP público |Cada IP público associado com o Balanceador de carga é convertido para um recurso IP público e associado com o Balanceador de carga após a migração. |
| Nomes DNS internos na VM |Nomes DNS internos na NIC |Durante a migração, os sufixos de DNS internos das VMs são migrados para uma propriedade só de leitura com o nome “InternalDomainNameSuffix” na NIC. O sufixo permanece inalterado após a migração e resolução VM deve continuar a funcionar como anteriormente. |
| Gateway de rede virtual |Gateway de rede virtual |Propriedades do gateway de rede virtual são migradas inalterado. O VIP associado ao gateway também não muda. |
| Site de rede local |Gateway de rede local |Propriedades do site de rede local são migradas sem alterações para um novo recurso designado por gateway de rede local. Representa os prefixos de endereço no local e o IP do gateway remoto. |
| Referências de ligações |Ligação |Referências de conectividade entre o gateway e o site de rede local numa configuração de rede é representada por um novo recurso denominado ligação. Todas as propriedades de referência de conectividade nos ficheiros de configuração de rede são copiadas inalterada para o recurso de ligação. Conectividade entre redes virtuais no modelo de implementação clássica é conseguida através da criação de dois túneis IPsec para os sites de rede local que representa as redes virtuais. Isto é transformado para o tipo de ligação de-rede-para-virtual-rede virtual no modelo do Resource Manager, sem necessidade de gateways de rede local. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações à automatização e às ferramentas após a migração
Como parte da migração os recursos do modelo de implementação clássica para o modelo de implementação Resource Manager, tem de atualizar o seu automatização existente ou ferramentas para assegurar que continua a funcionar após a migração.
