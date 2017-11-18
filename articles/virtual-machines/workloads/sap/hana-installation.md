---
title: "Instalar o SAP HANA SAP HANA no Azure (instâncias de grande) | Microsoft Docs"
description: "Como instalar o SAP HANA num SAP HANA no Azure (grande instância)."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef85c098058c97e5ec6d758fcf1dab5b1a87786
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar o SAP HANA (instâncias de grande) no Azure

Seguem-se algumas definições importantes saber antes de ler este guia. No [descrição geral de SAP HANA (instâncias de grandes dimensões) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) introduzimos duas classes diferentes de unidades de instância grande HANA com:

- S72, S72m, S144, S144m, S192 e S192m, que denominamos 'Type posso de classe' de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que denominamos 'class II de tipo' de SKUs.

O especificador de classe vai ser utilizado em toda a documentação de instância grande HANA para, eventualmente, consulte funcionalidades diferentes e requisitos com base nas HANA grande SKUs de instância.

Outras definições utilizadas frequentemente são:
- **Carimbo de instância grande:** uma pilha de infraestrutura de hardware que é o SAP HANA TDI certificada e dedicados para executar instâncias de SAP HANA no Azure.
- **SAP HANA no Azure (instâncias de grande):** oficial nome para a oferta no Azure executar HANA instâncias no SAP HANA TDI certificadas hardware que é implementada em carimbos de data / instância grande em diferentes regiões do Azure. O termo relacionado **instância grande HANA** é curto para SAP HANA no Azure (instâncias de grandes dimensões) e é amplamente utilizado neste guia de implementação técnica.


A instalação de SAP HANA é da responsabilidade do cliente e pode começar a atividade após handoff de um novo SAP HANA no servidor do Azure (instâncias de grande). E, depois da conectividade entre o VNet(s) do Azure e as ou as unidades de instância grande HANA foi estabelecida. 

> [!Note]
> Por política SAP, a instalação de SAP HANA tem de ser efetuada por uma pessoa certificada para efetuar instalações de SAP HANA. Uma pessoa, o que foi efectuada com êxito o certificado associar de tecnologia de SAP exam, exam de certificação de SAP HANA instalação, ou por um integrador de sistema de certificados de SAP (TAMA).

Verifique novamente, especialmente quando estiver a planear instalar HANA 2.0, [2235581 de n. º de nota de suporte de SAP - SAP HANA: sistemas operativos suportados pelo](https://launchpad.support.sap.com/#/notes/2235581/E) para certificar-se de que o SO é suportado com o SAP HANA versão lhe decidir instalar. Tenha em atenção que o SO suportado para HANA 2.0 é mais restrito do que o SO suportado para HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Primeiro passos após receber o HANA grande de instância de unidades

**Primeiro passo** depois de receber a instância de grande HANA e ter acesso estabelecido e conectividade para as instâncias, está a registar o SO da instância do seu fornecedor de SO. Este passo inclui a registar com o SO Linux SUSE numa instância do SUSE SMT que precisa de ter implementado numa VM no Azure. A unidade de instância grande HANA pode ligar a esta instância SMT (ver mais tarde nesta documentação). Ou, tem de ser registado com o Red Hat subscrição gestor tem de ligar ao seu SO de VM de RedHat. Observações também de Consulte deste [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este passo também é necessário para poder aplicar o patch do SO. Uma tarefa que está a ser da responsabilidade do cliente. Para SUSE, encontrar documentação para instalar e configurar SMT [aqui](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Segundo passo** é verificar a existência de novas patches e correções de versão/versão de SO específica. Verifique se o nível de patch da instância grande HANA está sobre o estado mais recente. Com base na temporização na patch de SO/versões e as alterações à imagem do que Microsoft pode implementar, poderão existir casos onde as correções mais recentes não podem ser incluídas. Por conseguinte, é um passo obrigatório após tendo mais de uma unidade de instância grande HANA, para verificar se patches relevantes de segurança, a funcionalidade, disponibilidade e desempenho foram lançadas entretanto pelo fabricante do Linux específica e tem de ser aplicadas.

**Passo terceira** é consulte as notas de SAP relevantes para instalar e configurar o SAP HANA na versão/versão de SO específica. Devido à alteração ou alterações de recomendações para SAP notas ou as configurações que são dependentes de cenários de instalação individuais, Microsoft não será sempre capaz de ter uma unidade de instância grande HANA perfeitamente configurada. Por conseguinte, é obrigatório para si como um cliente, leia as notas de SAP relacionados com o SAP HANA na sua versão exata do Linux. Também verificar as configurações da versão/versão do SO necessária e aplicar as definições de configuração em que já não o fez.

No específicos, verifique os parâmetros seguintes e, eventualmente, tendo em conta:

- NET.Core.rmem_max = 16777216
- NET.Core.wmem_max = 16777216
- NET.Core.rmem_default = 16777216
- NET.Core.wmem_default = 16777216
- NET.Core.optmem_max = 16777216
- NET.IPv4.tcp_rmem = 65536 16777216 16777216
- NET.IPv4.tcp_wmem = 65536 16777216 16777216

A partir de com SLES12 SP1 e RHEL 7.2, estes parâmetros tem de ser definidos num ficheiro de configuração no diretório /etc/sysctl.d. Por exemplo, um ficheiro de configuração com o nome 91-NetApp-HANA.conf tem de ser criado. Para versões mais antigas SLES e RHEL estes parâmetros têm de ser in/etc/sysctl.conf conjunto.

Para RHEL todas as versões e que comece com SLES12, o 
- SUNRPC.tcp_slot_table_entries = 128

parâmetro tem de ser definido in/etc/modprobe.d/sunrpc-local.conf. Se o ficheiro não existir, tem primeiro criada ao adicionar a entrada seguinte: 
- Opções sunrpc tcp_max_slot_table_entries = 128

**Passo Fourth** é para verificar a hora do sistema da sua unidade de instância grande HANA. As instâncias são implementadas com um sistema fuso horário que representam a localização da região do Azure, que o carimbo de instância grande HANA está localizado na. Está livre para alterar o fuso horário das instâncias que possui ou a hora do sistema. Se o fizer e ordenação mais instâncias no seu inquilino, esteja preparado que precisa de adaptar o fuso horário das instâncias recentemente entregues. Operações do Microsoft não ter nenhum aprofundadas o fuso horário do sistema que configurou com as instâncias após o handover. Por conseguinte, instâncias recentemente implementadas não podem ser definidas no mesmo fuso horário que alterou para. Como resultado, é da responsabilidade do cliente como o cliente para verificar e se for necessário adaptar o fuso horário de instâncias passadas. 

**Quinto passo** consiste em verificar anfitriões/etc. Como painéis de introdução passados, têm diferentes endereços IP atribuídos para diferentes fins (consulte a secção seguinte). Verifique o ficheiro etc/hosts. Nos casos em que as unidades são adicionadas para um inquilino existente, não espere etc/anfitriões dos sistemas recentemente implementados realizados corretamente com os endereços IP dos sistemas de fornecido anteriormente. Por conseguinte, é como cliente para verificar as definições corretas deste modo, que uma instância recentemente implementada pode interagir e resolver os nomes dos anteriormente implementados unidades no seu inquilino. 

## <a name="networking"></a>Redes
Iremos partem do princípio de que seguiu as recomendações estruturar as VNets do Azure e ligar esses VNets para as instâncias de grande HANA conforme descrito nestes documentos:

- [Descrição geral de SAP HANA (instância grande) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura de SAP HANA (instâncias de grandes dimensões) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Existem alguns detalhes vale para mencionar sobre o funcionamento em rede das unidades único. Cada unidade de instância grande HANA inclui duas ou três endereços IP que estão atribuídos a dois ou três portas NIC da unidade. Três endereços IP são utilizados nas configurações de escalamento horizontal de HANA e o cenário de replicação do sistema HANA. Um dos endereços IP atribuídos a NIC da unidade está fora do conjunto IP do servidor que foi descrito no [arquitetura no Azure e descrição geral de SAP HANA (instância grande)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

A distribuição de unidades com dois endereços IP atribuídos deve ter o seguinte aspeto:

- eth0.XX deve ter um endereço IP atribuído que está fora do intervalo de endereços do conjunto de IP do servidor que submetido para a Microsoft. Este endereço IP deverá ser utilizado para manter de /etc/hosts do SO.
- eth1.XX deve ter atribuído um endereço IP é utilizado para comunicação com o NFS. Por conseguinte, estes endereços fazer **não** tem de ser mantidos na etc/anfitriões para permitir tráfego de instância para a instância do inquilino.

Para cenários de implementação de replicação do sistema HANA ou HANA Escalamento horizontal, uma configuração de painel com dois endereços IP atribuídos não é adequada. Se ter dois endereços IP atribuídos apenas e que pretenda implementar este tipo de configuração, contacte o SAP HANA na gestão de serviço do Azure para obter um terceiro endereço IP de uma terceira VLAN atribuído. Para unidades de instância grande HANA ter três endereços IP atribuídos em três portas NIC, aplicam as seguintes regras de utilização:

- eth0.XX deve ter um endereço IP atribuído que está fora do intervalo de endereços do conjunto de IP do servidor que submetido para a Microsoft. Por conseguinte, este endereço IP não deverá ser utilizado para manter de /etc/hosts do SO.
- eth1.XX deve ter atribuído um endereço IP é utilizado para comunicação com o armazenamento NFS. Por conseguinte, este tipo de endereços não deve ser mantido nos anfitriões/etc.
- eth2.XX deve ser utilizado exclusivamente para ser mantidos na etc/anfitriões para comunicação entre as diferentes instâncias. Estes endereços seria também os endereços IP que precisam de ser mantidos em configurações de HANA de escalamento horizontal como endereços IP que HANA utiliza para a configuração do nó entre.



## <a name="storage"></a>Armazenamento

O esquema de armazenamento para SAP HANA no Azure (instâncias de grande) está configurado por SAP HANA na gestão de serviço do Azure através do SAP recomendado guia linhas conforme documentado no [os requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) documento técnico. Os tamanhos dos volumes diferentes com as diferentes instâncias SKUs HANA grande aproximados obteve documentados [arquitetura no Azure e descrição geral de SAP HANA (instância grande)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As convenções de nomenclatura dos volumes de armazenamento estão listadas na seguinte tabela:

| Utilização do armazenamento | Nome de montagem | Nome do volume | 
| --- | --- | ---|
| Dados HANA | /Hana/data/SID/mnt0000<m> | Armazenamento de IP: / hana_data_SID_mnt00001_tenant_vol |
| Registo HANA | /Hana/log/SID/mnt0000<m> | Armazenamento de IP: / hana_log_SID_mnt00001_tenant_vol |
| Cópia de segurança de registo HANA | /Hana/log/backups | Armazenamento de IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA partilhado | /Hana/Shared/SID | Armazenamento de IP: hana_shared_SID_mnt00001_tenant_vol/partilhado |
| usr/sap | /usr/SAP/SID | Armazenamento de IP: / hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Onde SID = a instância HANA ID de sistema 

E de inquilino = uma enumeração interna de operações quando implementar um inquilino.

Como pode ver, HANA partilhados e usr/sap estão a partilhar o mesmo volume. Nomenclature do mountpoints inclui o ID de sistema das instâncias HANA, bem como o número de montagem. Nas implementações de escala de segurança apenas há uma montagem, como mnt00001. Enquanto que a implementação de escalamento horizontal que poderá ver monta tantos, como, terá de nós de trabalho e o mestre. Para o ambiente de escalamento horizontal, dados, registo, os volumes de cópia de segurança de registo são partilhados e ligados a cada nó na configuração de escalamento horizontal. Para configurações com várias instâncias SAP, um conjunto diferente de volumes é criado e ligado à unidade de instância grande HAN.

Como ler o documento e procure uma unidade de instância grande HANA, tenha em atenção que as unidades vêm com o volume de disco em vez disso vantajoso para HANA/dados e que temos um volume HANA / / cópia de segurança. O motivo por que motivo é tamanho HANA/dados grandes, por isso, é que os instantâneos de armazenamento que oferecemos para si, como um cliente estiver a utilizar o mesmo volume de disco. Isto significa que o armazenamento mais instantâneos efetuar, mais espaço é consumido pelo instantâneos nos volumes de armazenamento atribuída. O volume HANA / / cópia de segurança não considerar-se para ser o volume de colocar as cópias de segurança da base de dados. É dimensionados de forma a ser utilizado como volume de cópia de segurança para as cópias de segurança do registo de transações HANA. Nas futuras versões do armazenamento de instantâneos de self-service, que iremos destina-se a este volume específico com instantâneos mais frequentes. E com que mais induzirem frequentes replicações para o site de recuperação de desastres se pretendidos ao nível a opção-para a funcionalidade de recuperação após desastre fornecida pela infraestrutura de instância grande HANA. Consulte os detalhes na [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Para além do armazenamento fornecido, pode comprar a capacidade de armazenamento adicional em incrementos de 1 TB. Este tipo de armazenamento adicional pode ser adicionado como novos volumes para um instâncias de grande HANA.

Durante a integração com o SAP HANA na gestão de serviço do Azure, o cliente especifica um ID de utilizador (UID) e o ID de grupo (GID) para o grupo de utilizadores e sapsys sidadm (ex: 1000,500) é necessário que durante a instalação do sistema de SAP HANA, estes valores mesmos são utilizados. Como pretende implementar várias instâncias HANA numa unidade, receberá vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento da implementação tem de definir:

- O SID das diferentes instâncias HANA (sidadm é derivado do mesmo).
- Tamanhos de memória das diferentes instâncias HANA. Uma vez que o tamanho de memória por instâncias define o tamanho dos volumes em cada conjunto de volume individuais.

Com base nas recomendações do fornecedor de armazenamento estão configuradas as seguintes opções de montagem para todos os volumes montados (exclui arranque LUN):

- NFS rw, vers = 4, do disco rígido, timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, bloquear 0 0

Estes montagem pontos são configurados no etc/fstab, como mostrado nos gráficos seguintes:

![fstab de volumes montados numa unidade de instância grande HANA](./media/hana-installation/image1_fstab.PNG)

O resultado do comando df -h numa unidade S72m HANA grande instância deverá ter o seguinte aspeto:

![fstab de volumes montados numa unidade de instância grande HANA](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós no carimbos de data / instância grandes são sincronizados com os servidores NTP. Com a sincronização de SAP HANA nas unidades do Azure (instâncias de grandes dimensões) e as VMs do Azure num servidor NTP, deve ser não acontecer de que se desviam significativos de tempo entre a infraestrutura e as unidades de computação no Azure ou instância grande carimbos.

Para otimizar o SAP HANA para o armazenamento utilizado por baixo, também deve definir os parâmetros de configuração de SAP HANA seguintes:

- max_parallel_io_requests 128
- async_read_submit no
- async_write_submit_active no
- todos os async_write_submit_blocks
 
Para versões de SAP HANA 1.0 até SPS12, estes parâmetros podem ser definidos durante a instalação da base de dados SAP HANA, conforme descrito em [2267798 de n. º de nota SAP - configuração da base de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Também pode configurar os parâmetros após a instalação de base de dados SAP HANA utilizando o framework hdbparam. 

Com o SAP HANA 2.0, a arquitetura de hdbparam foi preterida. Como resultado de parâmetros tem de ser definidos utilizando comandos do SQL Server. Para obter mais informações, consulte [SAP nota #2399079: eliminação de hdbparam no HANA 2](https://launchpad.support.sap.com/#/notes/2399079).


## <a name="operating-system"></a>Sistema operativo

Espaço de comutação de entregue da imagem do SO estiver definido como 2 GB de acordo com o [1999997 de n. º de nota de suporte de SAP - FAQ: SAP HANA memória](https://launchpad.support.sap.com/#/notes/1999997/E). Qualquer definição diferentes pretendido tem de ser definida por si, como um cliente.

[SUSE Linux Enterprise Server 12 SP1 para aplicações SAP](https://www.suse.com/products/sles-for-sap/hana) é a distribuição de Linux instalado para SAP HANA no Azure (instâncias de grande). Esta distribuição determinada fornece as capacidades específicas do SAP &quot;a Box&quot; (incluindo os parâmetros predefinidos para em execução SAP SLES eficazmente).

Consulte [biblioteca/técnicos de recurso](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no Web site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na rede de Comunidade do SAP (SCN) para vários recursos úteis relacionadas com a implementação de SAP HANA no SLES (incluindo a configuração de alta Disponibilidade, proteção de segurança específica para as operações de SAP e mais).

SAP adicional e útil em ligações de SUSE:

- [SAP HANA no Site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Melhor prática para SAP: colocar em fila replicação – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – SLES a proteção contra vírus para SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo o SLES 12 para aplicações SAP).

Notas de suporte SAP aplicável para implementar o SAP HANA SLES 12:

- [Nota de suporte SAP #1944799 – SAP HANA diretrizes para a instalação do sistema operativo SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Nota de suporte SAP #2205917 – SAP HANA DB de definições de SO para o SLES 12 para aplicações SAP recomendadas](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Nota de suporte do SAP #1984787 – SUSE Linux Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787).
- [Nota de suporte SAP #171356 – Software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787).
- [Nota de suporte SAP #1391070 – UUID de Linux soluções](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para a execução de SAP HANA em instâncias de grande HANA. Versões do RHEL 6.7 e 7.2 estão disponíveis. . Anote na opposite to nativo as VMs do Azure onde são suportadas apenas RHEL 7.2 e versões mais recentes, instâncias de grande HANA suportam RHEL 6.7 bem. No entanto, recomendamos que utilize uma versão do RHEL 7. x.

SAP útil e adicional no Red Hat relacionados com ligações:
- [SAP HANA no Red Hat Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Notas de suporte SAP aplica-se a implementação de SAP HANA no Red Hat:

- [Nota de suporte SAP #2009879 - SAP HANA diretrizes para o sistema operativo do Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Nota de suporte do SAP #2292690 - SAP HANA DB: Definições de SO recomendada para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Nota de suporte do SAP #2247020 - SAP HANA DB: Definições de SO recomendada para RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Nota de suporte SAP #1391070 – UUID de Linux soluções](https://launchpad.support.sap.com/#/notes/1391070).
- [SAP suporte Nota #2228351 - Linux: Revisão de SAP HANA da base de dados SPS 11 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Nota de suporte do SAP #2397039 - FAQ: SAP em RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Nota de suporte SAP #1496410 - Red Hat Enterprise Linux 6. x: instalação e a atualização](https://launchpad.support.sap.com/#/notes/1496410).
- [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7. x: instalação e a atualização](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Sincronização de hora

Aplicações SAP incorporadas numa arquitetura do SAP NetWeaver são sensíveis em diferenças de tempo para os vários componentes que compõem o sistema SAP. Informações de SAP ABAP curto com o título do erro da ZDATE\_grande\_tempo\_DIFF são provável familiar, como estas informações curtas aparecem quando a hora do sistema de diferentes servidores ou VMs é demasiado até que ponto apart drifting.

Para SAP HANA no Azure (instâncias de grande), a sincronização de hora feito no Azure &#39; t aplicam-se para as unidades de computação de carimbos de data / instância grandes. Esta sincronização não é aplicável para executar aplicações SAP em VMs do Azure nativa, como o Azure assegura um sistema de &#39; tempo s corretamente está sincronizado. Como resultado, uma hora separada servidor tem de ser configurado, que pode ser utilizada por SAP servidores de aplicações em execução em VMs do Azure e o SAP HANA da base de dados instâncias em execução em instâncias de grande HANA. A infraestrutura de armazenamento carimbos de data / instância grande é a hora sincronizada com os servidores NTP.

## <a name="setting-up-smt-server-for-suse-linux"></a>Configurar o servidor SMT para SUSE Linux
SAP HANA grande instâncias não tem uma ligação direta à Internet. Por conseguinte, não é um processo simples para registar esse uma unidade com o fornecedor de SO e para transferir e aplicar patches. No caso do SUSE Linux, pode ser uma solução configurar um servidor SMT numa VM do Azure. Enquanto que a VM do Azure tem de ser alojado numa VNet do Azure, que está ligado à instância de grande HANA. Com essa um servidor SMT, a unidade de instância grande HANA foi possível registar e transferir patches. 

SUSE fornece um guia de maior no respetivo [ferramenta de gestão de subscrição para o SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Como pré-condição para a instalação de um servidor SMT que cumpre a tarefa para a instância de grande HANA, terá de:

- Uma VNet do Azure que está ligada para o circuito de ER de instância grande HANA.
- Uma conta SUSE que está associada uma organização. Enquanto que a organização seria necessário ter alguns uma subscrição válida do SUSE.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalação do servidor SMT na VM do Azure

Neste passo, instalar o servidor SMT numa VM do Azure. A primeira medida é para iniciar sessão para o [SUSE cliente Center](https://scc.suse.com/)

Como são registadas no, aceda a organização--> credenciais de organização. Na secção deve encontrar as credenciais que são necessárias para configurar o servidor SMT.

O terceiro passo é instalar uma VM com Linux do SUSE na VNet do Azure. Para implementar a VM, ter uma imagem de galeria SLES 12 SP2 do Azure. No processo de implementação, não defina um nome DNS e não utilize endereços IP estáticos visto esta captura de ecrã

![implementação da VM para o servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A VM implementada foi uma VM mais pequena e obteve do endereço IP na VNet do Azure de 10.34.1.4. Nome da VM foi smtserver. Após a instalação, a conectividade com as ou as unidades de instância HANA grande foi verificada. Depende de como organizados de resolução do nome poderá ter de configurar a resolução das unidades de instância grande HANA no etc/anfitriões de VM do Azure. Adicione um disco adicional à VM que vai ser utilizado para reter os patches. O próprio disco de arranque pode ser demasiado pequeno. No caso demonstrado, o disco foi montado para /srv/www/htdocs conforme mostrado na captura de ecrã seguinte. Um disco de 100 GB deve suffice.

![implementação da VM para o servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Inicie sessão para as instância de grande HANA ou as unidades, manter /etc/hosts e verifique se pode aceder a VM do Azure que deve para executar o servidor SMT através da rede.

Depois desta verificação é efetuada com êxito, tem de iniciar sessão VM do Azure que deve executar o servidor SMT. Se estiver a utilizar o putty para iniciar sessão para a VM, terá de executar esta sequência de comandos na janela do bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Depois de executar estes comandos, reinicie o bash para ativar as definições. Em seguida, inicie YAST.

Na YAST, vá para a manutenção de Software e procure smt. Selecione smt, o que muda automaticamente para yast2 smt, conforme mostrado abaixo

![SMT no yast](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para instalação a smtserver. Depois de instalado, vá para a configuração do servidor SMT e introduza as credenciais organizacionais do Centro de cliente SUSE que obteve anteriormente. Também introduza o nome de anfitrião de VM do Azure como o URL do servidor SMT. Nesta demonstração, vamos foi https://smtserver tal como apresentado no gráfico seguinte.

![Configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Como passo seguinte, terá de testar se a ligação para o Centro de cliente SUSE funciona. Como ver nos gráficos seguintes, no caso de demonstração, funcionar.

![Teste ligar-se ao centro de cliente SUSE](./media/hana-installation/image7_test_connect.png)

Uma vez iniciado o programa de configuração a SMT, tem de fornecer uma palavra-passe de base de dados. Uma vez que é uma nova instalação, tem de definir essa palavra-passe, conforme mostrado no gráfico seguinte.

![Definir a palavra-passe para a base de dados](./media/hana-installation/image8_define_db_passwd.PNG)

A interação seguinte que tiver é quando um certificado é criado. Percorrer a caixa de diálogo, conforme mostrado seguinte e o passo deve continuar.

![Criar certificado do servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

Poderão existir alguns minutos despendidos no passo de 'Executar verificação de sincronização' no final da configuração. Após a instalação e configuração do servidor SMT, deverá considerar o repositório de diretório sob a montagem www/ponto /srv/htdocs/juntamente com alguns diretórios secundárias no repositório. 

Reinicie o servidor SMT e os respetivos serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Transferência de pacotes no servidor de SMT

Depois de todos os serviços são reiniciados, selecione os pacotes adequados na gestão de SMT Yast a utilizar. A seleção do pacote depende a imagem do SO do servidor instância grande HANA e não o SLES versão ou a versão da VM com o servidor SMT. Um exemplo do ecrã de seleção é mostrado abaixo.

![Selecione os pacotes](./media/hana-installation/image10_select_packages.PNG)

Quando tiver terminado com a seleção do pacote, terá de iniciar a cópia inicial dos pacotes selecionadas para o servidor SMT que configurou. Esta cópia é acionada na shell utilizando o comando smt-espelho conforme mostrado abaixo


![Transferir pacotes para o servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Como ver acima, os pacotes devem obter copiados para os diretórios criados no montagem ponto /srv/www/htdocs. Este processo pode demorar algum tempo. Depende de como número de pacotes que selecionar, pode demorar até uma hora ou mais.
À medida que conclui este processo, terá de mover para o programa de configuração de cliente SMT. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de instância grande HANA

Neste caso, os clientes são as unidades de instância grande HANA. A configuração do servidor SMT copiados clientSetup4SMT.sh o script para a VM do Azure. Cópia de script através da unidade de instância grande HANA pretende ligar ao seu servidor SMT. Inicie o script com a opção -h e conceda-lhe como parâmetro de nome do seu servidor SMT. No smtserver exemplo.

![Configurar cliente SMT](./media/hana-installation/image12_configure_client.PNG)

Pode existir um cenário em que a carga do certificado do servidor, o cliente foi concluída com êxito, mas o registo falha conforme mostrado abaixo.

![Falha de registo do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registo falhou, leia este [SUSE suporta documentos](https://www.suse.com/de-de/support/kb/doc/?id=7006024) e execute os passos descritos não existe.

> [!IMPORTANT] 
> Como o nome do servidor tem de fornecer o nome da VM, neste smtserver maiúsculas, sem o nome de domínio completamente qualificado. Apenas funciona de nome de VM. 

Depois de tem sido executados estes passos, terá de executar o seguinte comando na unidade instância grande HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> No nossos testes tivemos sempre de aguardar alguns minutos após esse passo. ClientSetup4SMT.sh execução imediata, depois das medidas corretivas descritas no artigo SUSE, terminou com mensagens que o certificado não será válido ainda. A aguardar 5-10 minutos, normalmente e executar clientSetup4SMT.sh terminou numa configuração de cliente com êxito.

Se Ocorreu o problema que é necessário corrigir com base nos passos do artigo SUSE, terá de reiniciar clientSetup4SMT.sh na unidade instância grande HANA novamente. Agora, deve ser concluída com êxito, conforme mostrado abaixo.

![Registo de cliente foi concluída com êxito](./media/hana-installation/image14_finish_client_config.PNG)

Neste passo, se configurou o cliente SMT da unidade instância grande HANA para ligar o servidor de SMT instalado na VM do Azure. Agora pode efetuar 'zypper cópias de segurança' ou 'zypper no' para a instalação de patches de SO instâncias grande HANA ou instalar pacotes adicionais. É compreendida que só pode obter patches que transferiu antes do servidor de SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exemplo de uma instalação de SAP HANA instâncias grande HANA
Esta secção ilustra como instalar o SAP HANA numa unidade instância grande HANA. O estado de início temos ter o seguinte aspeto:

- Fornecido Microsoft todos os dados para implementar a uma instância de grandes dimensões do SAP HANA.
- A instância de grandes dimensões do SAP HANA que recebeu da Microsoft.
- Criar uma VNet do Azure que está ligada à sua rede no local.
- Ligado o circuito ExpressRotue para HANA instâncias grandes para a mesma VNet do Azure.
- Instalou uma VM do Azure a utilizar como uma caixa de ir para instâncias de grande HANA.
- A certificou-se de que consegue ligar a partir da caixa de ir à sua unidade de instância grande HANA e vice-versa.
- Tiver selecionado se todos os patches e pacotes necessários estão instalados.
- Leia as notas de SAP e documentations relativamente à instalação HANA no sistema operativo que estiver a utilizar e certifique-se de que a versão HANA escolhidas é suportada no sistema operativo versão.

O que é apresentado nas sequências de seguintes é a transferência de pacotes de instalação HANA à caixa ir VM, neste caso, em execução num SO de Windows, a cópia dos pacotes para a unidade de instância grande HANA e a sequência de configuração.

### <a name="download-of-the-sap-hana-installation-bits"></a>Transferência de bits de instalação SAP HANA
Uma vez que as unidades de instância grande HANA não tem uma ligação direta à internet, diretamente não é possível transferir os pacotes de instalação do SAP para a instância de VM de grande HANA. Para ultrapassar a conectividade internet direta em falta, terá da caixa de ir. Transferir os pacotes para a caixa de ir VM.

Para poder transferir os pacotes de instalação HANA, precisa de um utilizador S SAP ou outro utilizador, o que lhe permite aceder às SAP Marketplace. Passar por esta sequência de ecrãs após iniciar sessão:

Aceda a [SAP serviço Marketplace](https://support.sap.com/en/index.html) > clique em Transferir Software > instalações e de atualização > pelo índice alfabética > em H – edição de plataforma do SAP HANA > SAP HANA plataforma edição 2.0 > instalação > transferir os seguintes ficheiros

![Transferir a instalação de HANA](./media/hana-installation/image16_download_hana.PNG)

No caso de demonstração, iremos transferir pacotes de instalação de SAP HANA 2.0. Na caixa de ir Azure VM, expanda os arquivos de extração para o diretório conforme mostrado abaixo.

![Extrair instalação HANA](./media/hana-installation/image17_extract_hana.PNG)

Como são extraídos de arquivos, copie o diretório criado pela extração, no caso de acima 51052030, até à unidade de instância HANA grande para o volume de /hana/shared para um diretório que criou.

> [!Important]
> Não copie os pacotes de instalação para a raiz ou o LUN de arranque, uma vez que o espaço é limitado e tem de ser utilizada por outros processos bem.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar o SAP HANA na unidade instância grande HANA
Para instalar o SAP HANA, terá de iniciar sessão como raiz do utilizador. Raiz apenas tem permissões suficientes para instalar o SAP HANA.
A primeira coisa que precisa de fazer consiste em definir permissões no diretório copiadas em hana/partilhado. As permissões têm de definir como

```
chmod –R 744 <Installation bits folder>
```

Se pretender instalar SAP HANA utilizando a configuração gráfica, o pacote de gtk2 tem de ser instalada nas instâncias grande HANA. Verifique se está instalada com o comando

```
rpm –qa | grep gtk2
```

Mais passos, iremos são demonstrar a configuração de SAP HANA com a interface de utilizador gráfica. Como passo seguinte, vá para o diretório de instalação e navegue para o diretório de sub HDB_LCM_LINUX_X86_64. Iniciar

```
./hdblcmgui 
```
fora do diretório. Agora é obter guiado através de uma sequência de ecrãs onde tem de fornecer os dados para a instalação. No caso demonstrado, iremos estiver a instalar o servidor de base de dados SAP HANA e os componentes de cliente de SAP HANA. Por conseguinte nosso seleção é 'Base de dados do SAP HANA' conforme mostrado abaixo

![Selecione HANA na instalação](./media/hana-installation/image18_hana_selection.PNG)

No ecrã seguinte, escolher a opção 'Instalar o novo sistema'

![Selecionar a instalação do novo HANA](./media/hana-installation/image19_select_new.PNG)

Após este passo, tem de selecionar entre vários componentes adicionais que podem ser instalados além para o servidor de base de dados SAP HANA.

![Selecione os componentes HANA adicionais](./media/hana-installation/image20_select_components.PNG)

Para efeitos desta documentação, vamos escolher o cliente do SAP HANA e SAP HANA Studio. É também instalada uma instância de dimensionamento. Por conseguinte, no ecrã seguinte, tem de escolher 'Anfitrião único System' 

![Selecionar a instalação de dimensionamento](./media/hana-installation/image21_single_host.PNG)

No ecrã seguinte, terá de fornecer alguns dados

![Fornecer SAP HANA SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como o ID de sistema de HANA (SID), tem de fornecer o mesmo SID, como a fornecida Microsoft quando ordenado de implementação de instância grande HANA. Escolher um SID diferente faz com que a instalação falhar devido a problemas de permissão de acesso nos volumes diferentes

Como a instalação do diretório a utilizar o hana/partilhado diretório. O passo seguinte, terá de fornecer as localizações para os ficheiros de dados HANA e os ficheiros de registo HANA


![Forneça a localização de registo HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Deve definir como dados e ficheiros os volumes que já fornecido com os pontos de montagem que contêm o SID que selecionou na seleção ecrã antes deste ecrã de registo. Se o SID incompatibilidade com o que escreveu no, no ecrã de antes de voltar atrás e ajuste o SID para o valor que nos pontos de montagem.

No próximo passo, reveja o nome de anfitrião e, eventualmente, corrigi-lo. 

![Nome de anfitrião de revisão](./media/hana-installation/image24_review_host_name.PNG)

No próximo passo, também terá de obter dados que forneceu para a Microsoft quando ordenado de implementação de instância grande HANA. 

![Fornecer ao utilizador do sistema UID e GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Tem de fornecer o mesmo ID de utilizador do sistema e ID do grupo de utilizadores como fornecida Microsoft como ordem de implementação de unidade. Se falhar dar os IDs muito mesmos, a instalação de SAP HANA na unidade instância grande HANA falha.

Os seguinte dois ecrãs, o que podemos são não é possível mostrar nesta documentação, tem de fornecer a palavra-passe para o utilizador do sistema da base de dados SAP HANA e a palavra-passe para o utilizador sapadm, que é utilizada para o agente de anfitrião de SAP obtém instalado como parte da instância de base de dados SAP HANA.

Depois de definir a palavra-passe, um ecrã de confirmação está ser apresentado. Verifique todos os dados apresentados e continuar a instalação. Contactar um ecrã de progresso documentos o progresso da instalação, como o abaixo

![Verificar o progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

À medida que conclui a instalação, deve uma imagem como o seguinte

![Concluída a instalação](./media/hana-installation/image28_install_finished.PNG)

Neste momento, a instância de SAP HANA deve estar instalado e em execução e preparada para utilização. Deverá conseguir ligar ao mesmo a partir do SAP HANA Studio. Certifique-se também que verifica as correções mais recentes de SAP HANA e aplicar essas patches.
























































 







 




