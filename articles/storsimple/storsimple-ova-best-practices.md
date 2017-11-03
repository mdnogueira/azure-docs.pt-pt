---
title: "Melhores práticas para a matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve as melhores práticas para implementar e gerir a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2017
ms.author: alkohli
ms.openlocfilehash: 264764c5e9c32574d97beb2cc3c1bb1cfb555568
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-best-practices"></a>Melhores práticas de matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral
Matriz Virtual do Microsoft Azure StorSimple é uma solução de armazenamento integrada que gere as tarefas de armazenamento entre um dispositivo de virtual no local em execução num hipervisor e armazenamento de nuvem do Microsoft Azure. Matriz Virtual StorSimple é uma alternativa económica, eficiente para a matriz físico da 8000 série. A matriz virtual pode ser executado na sua infraestrutura existente do hipervisor, suporta o iSCSI e os protocolos SMB e é adequada para cenários de sucursais/office remoto. Para obter mais informações sobre as soluções do StorSimple, aceda a [descrição geral do Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo abrange os procedimentos recomendados implementados durante a configuração inicial, a implementação e a gestão da matriz Virtual StorSimple. Estas melhores práticas para fornecem validadas diretrizes para a configuração e gestão da sua matriz virtual. Este artigo é direcionado para os administradores de TI que implementar e gerir as matrizes de virtuais nos respetivos centros de dados.

Recomendamos uma revisão periódica das melhores práticas para ajudar a garantir o que seu dispositivo ainda está em conformidade quando são efetuadas alterações para o fluxo de configuração ou a operação. Deve encontrar problemas ao implementar estas melhores práticas na sua matriz virtual, [contacte a Microsoft Support](storsimple-virtual-array-log-support-ticket.md) para obter assistência.

## <a name="configuration-best-practices"></a>Melhores práticas de configuração
Estas melhores práticas abrangem as diretrizes que precisam de ser seguidas durante a configuração inicial e a implementação de matrizes de virtuais. Estas melhores práticas para incluem os relacionados com o aprovisionamento da máquina virtual, definições de política de grupo, o dimensionamento, configurar o funcionamento em rede, configurar contas de armazenamento e criar partilhas e volumes para a matriz virtual. 

### <a name="provisioning"></a>Aprovisionamento
A matriz Virtual StorSimple é uma máquina virtual (VM) aprovisionada num hipervisor (Hyper-V ou VMware) do seu servidor de anfitrião. Quando o aprovisionamento da máquina virtual, certifique-se de que o seu anfitrião é capaz de dediquem recursos suficientes. Para obter mais informações, aceda a [requisitos de recursos mínimo](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) para Aprovisionar uma matriz.

Implemente as seguintes melhores práticas quando aprovisionar a matriz virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**Geração 2** VM para utilização com o Windows Server 2012 ou posterior e um *. vhdx* imagem. <br></br> **Geração 1** VM para utilização com o Windows Server 2008 ou posterior e um *. vhd* imagem. |Utilize a versão de máquina virtual 8-11 ao utilizar *. vmdk* imagem. |
| **Tipo de memória** |Configurar como **memória estática**. <br></br> Não utilize o **memória dinâmica** opção. | |
| **Tipo de disco de dados** |Se aprovisionar como **expansão dinâmica**.<br></br> **Um tamanho fixo** demora muito tempo. <br></br> Não utilize o **diferenciação** opção. |Utilize o **dinâmico aprovisionar** opção. |
| **Modificação do disco de dados** |Não é permitida a expansão ou reduzir. Uma tentativa para tal resulta na perda de todos os dados locais no dispositivo. |Não é permitida a expansão ou reduzir. Uma tentativa para tal resulta na perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Dimensionamento
Quando o dimensionamento a matriz de Virtual StorSimple, considere os seguintes fatores:

* Reserva de local para volumes ou partilhas. Está reservado aproximadamente 12% do espaço na camada para cada volume em camadas aprovisionado ou partilha local. Aproximadamente 10% do espaço também está reservado para um volume localmente afixado de sistema de ficheiros.
* Instantâneo de sobrecarga. Aproximadamente 15% de espaço no escalão local está reservado para instantâneos.
* É necessário para restauros. Se efetuar restauro como uma operação de novo, dimensionamento deve ter em conta o espaço necessário para restauro. É efetuar um restauro para um volume do mesmo tamanho ou partilha.
* Alguns memória intermédia deve ser alocada para qualquer crescimento inesperado.

Com base nos fatores anteriores, os requisitos de dimensionamento podem ser representados por equação a seguinte:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Os exemplos seguintes mostram como pode tamanho uma matriz de virtual com base nos seus requisitos.

#### <a name="example-1"></a>Exemplo 1:
Na sua matriz virtual, quer ser capaz de

* Aprovisionar um 2 TB volume em camadas ou partilha.
* Aprovisionar um 1 TB em camadas volume ou partilha.
* Aprovisionar um 300 GB de volume localmente afixado ou partilha.

Para os volumes ou partilhas, informe-nos calcule os requisitos de espaço no escalão local.

Em primeiro lugar, para cada volume em camadas/partilha, reserva local seria igual a % de 12 do tamanho do volume/partilha. Para a partilha/volume localmente afixada, reserva local é 10% do tamanho do volume localmente afixado/partilha (para além do tamanho de aprovisionamento). Neste exemplo, é necessário

* Reserva local 240 GB (para um 2 TB em camadas volume/partilha)
* Reserva de local de 120 GB (para um 1 TB em camadas volume/partilha)
* 330 GB para volume localmente afixado ou partilha (adicionar 10% de reserva local para o tamanho de 300 GB aprovisionado)

É o total de espaço necessário no escalão local até ao momento: 240 GB + 120 GB + 330 GB = 690 GB.

Segundo, é necessário, pelo menos, o mesmo espaço no escalão local como a reserva único maior. Esta quantidade adicional é utilizada no caso de ser necessário restaurar a partir de um instantâneo na nuvem. Neste exemplo, a maior reserva local é 330 GB (incluindo a reserva de sistema de ficheiros), pelo que deverá ser adicionado que para os 690 GB: 690 GB + 330 GB = 1020 GB.
Se efetuámos subsequentes restauros adicionais, iremos pode sempre libertar espaço da operação de restauro anterior.

Terceira, precisamos 15% do seu total de espaço local, por isso, até que ponto para armazenar os instantâneos locais, para que apenas 85% do mesmo está disponível. Neste exemplo, seria em torno 1020 GB = 0.85&ast;TB de disco de dados de aprovisionamento. Por isso, seria o disco de dados de aprovisionamento (1020&ast;(1/0.85)) = 1200 GB = 1.20 TB ~ 1.25 TB (arredondamento para quartile mais próximo)

Factoring em crescimento inesperado e restauros novo, deve aprovisionar um local de disco em torno 1.25-1,5 TB.

> [!NOTE]
> Recomendamos também que o disco local é fracamente aprovisionado. Esta recomendação é porque o espaço de restauro só é necessário quando pretender restaurar dados com mais de cinco dias. Recuperação ao nível do item permite-lhe restaurar os dados nos últimos cinco dias sem necessidade do espaço extra para restauro.


#### <a name="example-2"></a>Exemplo 2:
Na sua matriz virtual, quer ser capaz de

* Aprovisionar um 2 TB camadas volume
* Aprovisionar um volume localmente afixado de 300 GB

Com base em % 12 de reserva de espaço local para partilhas/volumes em camadas e de 10% de volumes localmente afixadas/partilhas, é necessário

* Reserva local 240 GB (para 2 TB em camadas volume/partilha)
* 330 GB para volume localmente afixado ou partilha (adicionar 10% de reserva local para o espaço aprovisionado de 300 GB)

Total de espaço necessário no escalão local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para restauro é 330 GB.

15% do seu total do disco é utilizado para armazenar os instantâneos de modo a que 0.85 só está disponível. Por isso, é o tamanho do disco (900&ast;(1/0.85)) = 1.06 TB ~ 1.25 TB (arredondamento para quartile mais próximo)

Factoring em qualquer crescimento inesperado, pode aprovisionar um disco local 1.25-1,5 TB.

### <a name="group-policy"></a>Política de grupo
Política de grupo é uma infraestrutura que permite-lhe implementar configurações específicas para utilizadores e computadores. Definições de política de grupo estão contidas nos objetos de política de grupo (GPOs), que estão ligados para os seguintes contentores do Active Directory Domain Services (AD DS): sites, domínios ou unidades organizacionais (UOs). 

Se a matriz de virtual está associado a um domínio, os GPOs podem ser aplicadas ao mesmo. Estes GPOs podem instalar aplicações como um software antivírus que pode afetar de forma adversa a operação da matriz Virtual StorSimple.

Por conseguinte, recomendamos que tem:

* Certifique-se de que a matriz de virtual está no seu próprio unidade organizacional (UO) do Active Directory.
* Certifique-se de que não existem objetos de política de grupo (GPOs) são aplicados para a matriz de virtual. Pode bloquear a herança para se certificar de que a matriz virtual (nó subordinado) não herda automaticamente quaisquer GPOs do principal. Para obter mais informações, aceda a [bloquear herança](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Redes
A configuração de rede para a matriz de virtual é feita através da IU da web local. Interface de rede virtual é ativada através do hipervisor no qual a matriz virtual é aprovisionada. Utilize o [as definições de rede](storsimple-virtual-array-deploy3-fs-setup.md) página para configurar o endereço IP da interface de rede virtual, a sub-rede e o gateway.  Também pode configurar a primário e secundário servidor DNS, as definições de hora e as definições de proxy opcional para o seu dispositivo. A maior parte da configuração da rede é uma configuração única. Reveja o [StorSimple requisitos de rede](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar a matriz virtual.

Quando implementar a matriz de virtual, recomendamos que siga estas práticas recomendadas:

* Certifique-se de que a rede em que a matriz virtual é implementada sempre tem capacidade para dedicar largura de banda de Mbps Internet 5 (ou mais).
  
  * Necessidade de largura de banda de Internet varia consoante as características de carga de trabalho e a taxa de alteração de dados.
  * A alteração de dados que pode ser processada é diretamente proporcional ao seu largura de banda de Internet. Por exemplo ao colocar uma cópia de segurança, uma largura de banda Mbps 5 pode acomodar uma alteração de dados de cerca de 18 GB de 8 horas. Com quatro vezes mais largura de banda (20 Mbps), pode processar mais de quatro vezes alteração de dados (GB de 72).
* Certifique-se a conectividade à Internet está sempre disponível. Ligações de Internet esporádicas ou pouco fiáveis para os dispositivos poderão resultar em perda de acesso a dados na nuvem e pode resultar numa configuração não suportada.
* Se planeia implementar o dispositivo como um servidor de iSCSI:
  
  * Recomendamos que desative o **obter automaticamente endereço IP** opção (DHCP).
  * Configure endereços IP estáticos. Tem de configurar um site primário e um servidor DNS secundário.
  * Se definir várias interfaces de rede no seu virtual matriz, apenas a primeira interface de rede (por predefinição, esta interface está **Ethernet**) pode contactar a nuvem. Para controlar o tipo de tráfego, pode criar várias interfaces de rede virtual na sua matriz virtual (configurado como um servidor de iSCSI) e ligar esses interfaces a sub-redes diferentes.
* Para limitar a largura de banda do cloud apenas (utilizado pela matriz de virtual), configurar a limitação no router ou da firewall. Se definir a limitação no seu hipervisor, irá limitar a todos os protocolos, incluindo o iSCSI e SMB em vez de apenas a largura de banda de nuvem.
* Certifique-se que a sincronização de tempo para hipervisores está ativada. Se utilizar o Hyper-V, selecione a matriz de virtual no Gestor de Hyper-V, aceda a **definições &gt; serviços de integração**e certifique-se de que o **sincronização de hora** está marcada.

### <a name="storage-accounts"></a>Contas de armazenamento
Matriz Virtual StorSimple pode estar associado uma única conta de armazenamento. Esta conta de armazenamento pode ser uma conta de armazenamento gerado automaticamente, uma conta na mesma subscrição que o serviço, ou uma conta de armazenamento relacionadas com a outra subscrição. Para obter mais informações, consulte como [gerir contas de armazenamento para a matriz de virtual](storsimple-virtual-array-manage-storage-accounts.md).

Utilize as seguintes recomendações para contas de armazenamento associadas a matriz de virtual.

* Quando ligar várias matrizes de virtuais com uma única conta de armazenamento, pesar a capacidade máxima (64 TB) para uma matriz de virtual e o tamanho máximo (500 TB) de uma conta de armazenamento. Isto limita o número de tamanho completo virtuais as matrizes que podem ser associados essa conta do storage para cerca de 7.
* Quando criar uma nova conta de armazenamento
  
  * Recomendamos que crie-lo na região mais próxima do office/escritório remoto em que a matriz de Virtual StorSimple é implementada para minimizam as latências.
  * Tenha em consideração que não é possível mover uma conta do storage em regiões diferentes. Também não é possível mover um serviço nas subscrições.
  * Utilize uma conta de armazenamento que implementa redundância dos centros de dados. Armazenamento Georredundante (GRS), armazenamento redundante de zona (ZRS) e localmente redundante armazenamento (LRS) são todos suportados para utilização com a matriz de virtual. Para obter mais informações sobre os diferentes tipos de contas do storage, vá para [replicação do storage do Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Partilhas e os volumes
Para a matriz de Virtual StorSimple, pode aprovisionar partilhas quando é configurado como um servidor de ficheiros e volumes quando configurado como um servidor de iSCSI. As melhores práticas para a criação de partilhas e os volumes estão relacionados com o tamanho e o tipo configurado.

#### <a name="volumeshare-size"></a>Tamanho do volume/partilha
Na sua matriz virtual, pode aprovisionar partilhas quando é configurado como um servidor de ficheiros e volumes quando configurado como um servidor de iSCSI. Melhores práticas para criar partilhas e volumes relacionadas com o tamanho e tipo configurado. 

Tenha em atenção as seguintes melhores práticas quando aprovisionar partilhas ou volumes no seu dispositivo virtual.

* Os tamanhos de ficheiro relativo para o tamanho de aprovisionamento de uma partilha em camadas podem afetar o desempenho em camadas. Trabalhar com ficheiros grandes pode resultar numa camada lenta de saída. Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é inferior a 3 de % do tamanho da partilha.
* É possível criar um máximo de volumes 16/partilhas na matriz de virtual. Para os limites de tamanho de volumes/partilhas localmente afixados e em camadas, sempre, consulte o [matriz Virtual StorSimple limita](storsimple-ova-limits.md).
* Ao criar um volume, o fator de consumo de dados esperado, bem como crescimento futuro. O volume não pode ser expandido mais tarde.
* Quando o volume tiver sido criado, não é possível reduzir o tamanho do volume no StorSimple.
* Quando são escritos para um volume em camadas no StorSimple, quando os dados do volume atingirem um determinado limiar (relativo para o espaço local reservado para o volume), está limitado a e/s. Continua a escrever este volume atrasar a e/s significativamente. Embora pode escrever um volume em camadas para além do respetivo capacidade aprovisionada (não ativamente paragem o utilizador de escrita para além da capacidade aprovisionada), verá uma notificação de alerta para o efeito que tenham sobre-subscrita. Depois de ver o alerta, é imperativo que tomar medidas toma, tais como eliminar os dados do volume (expansão de volume não é atualmente suportada).
* Para casos de utilização de recuperação após desastre, como o número de partilhas/volumes permitidos é de 16 e o número máximo de partilhas/volumes que podem ser processados em paralelo também é 16, o número de partilhas/volumes não tem um efeito na sua RPO e RTOs.

#### <a name="volumeshare-type"></a>Tipo de volume/partilha
StorSimple suporta dois tipos de volume/partilhar com base na utilização da: localmente afixado e em camadas. Volumes localmente afixadas/partilhas são fortemente aprovisionadas, enquanto as partilhas/volumes em camadas são com aprovisionamento dinâmico. Não é possível converter um volume localmente afixado/partilhar para em camadas ou *vice-versa* depois de criado.

Recomendamos que implemente as seguintes melhores práticas quando configurar StorSimple volumes/partilhas:

* Identifique o tipo de volume com base nas cargas de trabalho que pretende implementar antes de criar um volume. Utilize volumes localmente afixados para cargas de trabalho que necessitam de garantias locais dos dados (mesmo durante uma falha de nuvem) e que requerem latências de nuvem de baixa. Depois de criar um volume na sua matriz virtual, não é possível alterar o tipo de volume do afixado localmente a em camadas ou *vice-versa-se*. Por exemplo, criar volumes localmente afixados quando implementar cargas de trabalho do SQL Server ou cargas de trabalho que aloja máquinas virtuais (VMs); Utilize volumes em camadas para cargas de trabalho de partilha de ficheiros.
* Selecione a opção para dados de arquivo menos utilizados frequentemente ao lidar com tamanhos de ficheiros grandes. Um tamanho de segmentos de eliminação de duplicados maior de 512 K é utilizado quando esta opção está ativada para agilizar a transferência de dados para a nuvem.

#### <a name="volume-format"></a>Formato de volume
Depois de criar volumes do StorSimple no seu servidor de iSCSI, tem de inicializar, montar e formatar os volumes. Esta operação é executada no anfitrião ligado ao dispositivo StorSimple. Melhores práticas seguintes são recomendadas quando montar e formatar volumes do anfitrião StorSimple.

* Efetue uma formatação rápida em todos os volumes do StorSimple.
* Quando formatar um volume StorSimple, utilize um tamanho de unidade de alocação (AUS) de 64 KB (a predefinição é 4 KB). A 64 KB AUS baseia-se em testes feito internamente para cargas de trabalho comuns do StorSimple e outras cargas de trabalho.
* Quando utilizar a matriz de Virtual StorSimple configurado como um servidor de iSCSI, não utilize expandidos volumes ou discos dinâmicos estes volumes ou discos não são suportados pelo StorSimple.

#### <a name="share-access"></a>Partilhe o acesso
Ao criar partilhas no servidor de ficheiros virtual matriz, siga estas diretrizes:

* Ao criar uma partilha, atribua um grupo de utilizadores como um administrador de partilha em vez de um único utilizador.
* Pode gerir as permissões de NTFS depois da partilha for criada, editando as partilhas através do Explorador do Windows.

#### <a name="volume-access"></a>Acesso ao volume
Quando configurar os volumes do iSCSI na sua matriz de Virtual StorSimple, é importante controlar o acesso sempre que necessário. Para determinar quais os servidores de anfitrião podem aceder aos volumes, criar e associar registos de controlo de acesso (ACRs) de volumes do StorSimple.

Utilize as seguintes melhores práticas, ao configurar ACRs para volumes do StorSimple:

* Associe sempre ACR pelo menos um volume.

* Ao atribuir ACR mais do que um a um volume, certifique-se de que o volume não está exposto de forma onde pode ser simultaneamente acedido por mais do que um anfitrião agrupado. Se tiver atribuído ACRs vários para um volume, uma mensagem de aviso aparece para rever a configuração.

### <a name="data-security-and-encryption"></a>Encriptação e de segurança dos dados
A matriz de Virtual StorSimple tem funcionalidades de segurança e a encriptação de dados que garantem a integridade dos dados e confidencialidade. Ao utilizar estas funcionalidades, recomenda-se que siga estas práticas recomendadas: 

* Defina uma chave de encriptação de armazenamento na nuvem para gerar a encriptação AES 256 antes dos dados são enviados da sua matriz virtual à nuvem. Esta chave não é necessária se os dados são encriptados lugar. A chave pode ser gerada e mantida segura através de um sistema de gestão de chaves, como [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md).
* Quando configurar a conta de armazenamento através do serviço StorSimple Manager, certifique-se de que ativa o modo SSL criar um canal seguro para a comunicação de rede entre o dispositivo StorSimple e a nuvem.
* Gerar as chaves para as contas de armazenamento (através do serviço de armazenamento do Azure) periodicamente para a conta para todas as alterações aceder a com base na lista alterada dos administradores.
* Os dados na sua matriz virtual são comprimidos e eliminação de duplicados antes de ser enviada para o Azure. Não é recomendada a utilizar o serviço de função de eliminação de dados duplicados no anfitrião do Windows Server.

## <a name="operational-best-practices"></a>Melhores práticas operacionais
Melhores práticas operacionais são as diretrizes que devem ser seguidas durante a operação da matriz virtual ou de gestão diária. Estas práticas abranger tarefas de gestão específicas, tais como fazer cópias de segurança, restaurar a partir de um conjunto de cópia de segurança, efetuar uma ativação pós-falha, desativar e eliminar a matriz de monitorização de estado de funcionamento e de utilização do sistema e execução de vírus analisa na sua matriz virtual.

### <a name="backups"></a>Cópias de segurança
Os dados na sua matriz virtual cópias de segurança para a nuvem de duas formas, uma predefinição automatizada cópia de segurança diária de todo o dispositivo iniciar às 22:30 ou através de uma cópia de segurança a pedido manual. Por predefinição, o dispositivo cria automaticamente os instantâneos de nuvem diária de todos os dados que residem no mesmo. Para obter mais informações, aceda a [cópia de segurança a matriz de Virtual StorSimple](storsimple-virtual-array-backup.md).

Não não possível alterar a frequência e a retenção associada com as cópias de segurança predefinida, mas pode configurar a hora em que as cópias de segurança diárias são iniciadas todos os dias. Quando configurar a hora de início para cópias de segurança automatizadas, recomendamos que:

* Agende as cópias de segurança para as horas de ponta. Hora de início de cópia de segurança não deve operação com o anfitrião várias e/s.
* Inicie uma cópia de segurança a pedido manual quando planear a efetuar uma ativação pós-falha do dispositivo ou antes da janela de manutenção, para proteger os dados na sua matriz virtual.

### <a name="restore"></a>Restauro
Pode restaurar a partir de uma cópia de segurança definida de duas formas: Restaurar para outro volume ou partilha ou efetuar uma recuperação ao nível do item (disponível apenas numa matriz virtual configurada como um servidor de ficheiros). Recuperação ao nível do item permite-lhe efetuar uma recuperação granular dos ficheiros e pastas a partir de uma cópia de segurança de nuvem de todas as partilhas no dispositivo StorSimple. Para obter mais informações, aceda a [restaurar a partir de uma cópia de segurança](storsimple-virtual-array-clone.md).

Quando efetuar um restauro, mantenha as seguintes diretrizes em mente:

* A matriz de Virtual StorSimple não suportam o restauro de no local. No entanto pode ser prontamente conseguido através de um processo de dois passos: disponibilizar espaço virtual na matriz e, em seguida, restaure para outra/partilha de volume.
* Quando restaurar a partir de um volume local, tenha em atenção o restauro será uma operação de execução longa. Embora o volume pode rapidamente ficar online, os dados continuam a ser hydrated em segundo plano.
* O tipo de volume permanece igual durante o processo de restauro. Um volume em camadas é restaurado para outro volume em camadas e um volume localmente afixado para outro localmente afixado volume.
* Quando tentar restaurar um volume ou uma partilha de um conjunto de cópia de segurança, se a tarefa de restauro falhar, um volume de destino ou uma partilha ainda pode ser criada no portal. É importante se eliminar este volume de destino não utilizado ou partilhar no portal para minimizar os problemas futuros resultantes deste elemento.

### <a name="failover-and-disaster-recovery"></a>Ativação pós-falha e recuperação após desastre
Uma ativação pós-falha do dispositivo poder migrar os dados de um *origem* dispositivo no Centro de dados para outro *destino* dispositivos localizados na mesmo ou outra localização geográfica. A ativação pós-falha de dispositivo destina-se a todo o dispositivo. Durante a ativação pós-falha, os dados de nuvem para o dispositivo de origem alterações de propriedade para que o dispositivo de destino.

Para a matriz de Virtual StorSimple, apenas pode proceder ao longo de outra matriz virtual gerido pelo mesmo serviço StorSimple Manager. Não é permitida uma ativação pós-falha para um dispositivo da 8000 série ou uma matriz gerido por um serviço StorSimple Manager diferente (para o dispositivo de origem). Para saber mais sobre as considerações de ativação pós-falha, aceda a [pré-requisitos para a ativação pós-falha de dispositivo](storsimple-virtual-array-failover-dr.md).

Quando efetuar uma falha ao longo para a matriz de virtual, tenha em atenção o seguinte:

* Para uma ativação pós-falha planeada, é uma melhor prática recomendada para colocar todos os volumes/partilhas offline antes de iniciar a ativação pós-falha. Siga as instruções específicas do sistema operativo para colocar as volumes/partilhas offline no anfitrião primeiro e, em seguida, colocar as offline no seu dispositivo virtual.
* Para uma ficheiro servidor recuperação após desastre (DR), recomendamos que associa o dispositivo de destino ao mesmo domínio como origem para que as permissões de partilha são automaticamente resolvidas. Apenas a ativação pós-falha de um dispositivo de destino no mesmo domínio é suportada nesta versão.
* Depois da DR é concluída com êxito, o dispositivo de origem é eliminado automaticamente. Apesar do dispositivo já não estiver disponível, a máquina virtual que aprovisionado no sistema anfitrião ainda está a consumir recursos. Recomendamos que elimine esta máquina virtual do sistema anfitrião para impedir que os eventuais encargos acumulados.
* Tenha em atenção que, mesmo se não for bem-sucedida, a ativação pós-falha **os dados são sempre seguros na nuvem**. Considere os seguintes três cenários em que a ativação pós-falha não foi concluída com êxito:
  
  * Ocorreu uma falha nas fases iniciais da ativação pós-falha, tais como quando as pré-verificações de DR estão a ser efetuadas. Nesta situação, o dispositivo de destino está ainda utilizável. Pode tentar novamente a ativação pós-falha no mesmo dispositivo de destino.
  * Ocorreu uma falha durante o processo de ativação pós-falha real. Neste caso, o dispositivo de destino está marcado como não utilizável. Tem de aprovisionar e configurar outra matriz virtual de destino e utilize essa para a ativação pós-falha.
  * A ativação pós-falha foi concluída seguinte que o dispositivo de origem foi eliminado, mas o dispositivo de destino tem problemas e não pode aceder a quaisquer dados. Os dados são ainda seguros na nuvem e podem ser facilmente obtidos através da criação de outra matriz virtual e, em seguida, utilizá-lo como um dispositivo de destino para a DR.

### <a name="deactivate"></a>Desativar
Ao desativar uma matriz de Virtual StorSimple, sever a ligação entre o dispositivo e o serviço StorSimple Manager correspondente. Desativação é um **permanente** operação e não pode ser anulada. Um dispositivo desativado não é possível registar novamente com o serviço StorSimple Manager. Para obter mais informações, aceda a [desativar e eliminar a matriz de Virtual StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Mantenha as seguintes melhores práticas em mente quando desativar a matriz de virtual:

* Tire um instantâneo de nuvem de todos os dados antes de desativar um dispositivo virtual. Ao desativar uma matriz de virtual, todos os dados de dispositivo local é perdido. Criar um instantâneo de nuvem permitirá recuperar dados de uma fase posterior.
* Antes de desativar uma matriz de Virtual StorSimple, certifique-se para ou elimina os clientes e os anfitriões que dependem desse dispositivo.
* Elimine um dispositivo desativado se já não estiver a utilizar para que este não acumular encargos.

### <a name="monitoring"></a>Monitorização
Para garantir que a matriz de Virtual StorSimple está em bom estado de funcionamento contínuo, terá de monitorizar a matriz e certifique-se de que recebe informações do sistema, incluindo alertas. Para monitorizar o estado de funcionamento geral da matriz virtual, implemente as seguintes melhores práticas:

* Configure a monitorização para controlar a utilização de disco do disco de dados de matriz virtual, bem como o disco do SO. Se executar o Hyper-V, pode utilizar uma combinação de System Center Virtual Machine Manager (SCVMM) e o System Center Operations Manager (SCOM) para monitorizar os anfitriões de virtualização.
* Configure notificações por correio eletrónico na sua matriz virtual para enviar alertas de determinados níveis de utilização.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Pesquisa do índice e antivírus analise as aplicações
Uma matriz de Virtual StorSimple automaticamente pode de camada de dados de escalão local para a nuvem do Microsoft Azure. Quando uma aplicação como uma pesquisa do índice ou uma análise de vírus é utilizada para analisar os dados armazenados no StorSimple, terá de asseguramos que os dados na nuvem não obter acedidos e solicitados novamente para o escalão local.

Recomendamos que implemente as seguintes melhores práticas quando configurar a análise índice de pesquisa ou vírus na sua matriz virtual:

* Desative quaisquer operações automaticamente configurado da análise completa.
* Volumes em camadas, configure a aplicação de análise de índice pesquisa ou vírus, para efetuar uma análise incremental. Isto seria analisar apenas os novos dados provável que reside no escalão local. Os dados que estão em camadas para a nuvem não são acedidos durante uma operação incremental.
* Certifique-se de que os filtros de pesquisa correta e as definições estão configuradas para que apenas os tipos de ficheiros que se destinam obterem analisados. Por exemplo, os ficheiros (JPEG, GIF e TIFF) de imagem e engenharia drawings não deve ser analisado durante a reconstrução do índice completas ou incrementais.

Se utilizar janelas de indexação processo, siga estas diretrizes:

* Não utilize o indexador do Windows para volumes em camadas como recalls grandes quantidades de dados (TBs) da nuvem, se o índice tem de ser reconstruídos com frequência. Reconstrua o índice seria obter todos os tipos de ficheiro para indexar o respetivo conteúdo.
* Utilize a indexação de processo para volumes afixados localmente, isto só acederia a dados nas camadas locais para criar o índice (os dados na nuvem não serão acedidos) do Windows.

### <a name="byte-range-locking"></a>Bloqueio de intervalo de bytes
As aplicações podem bloquear um intervalo especificado de bytes dos ficheiros. Se o bloqueio de intervalo de byte está ativado nas aplicações que estiver a escrever a sua StorSimple, em seguida, em camadas não funcionam na sua matriz virtual. Para a criação de camadas funcione, todas as áreas de ficheiros acedidas devem ser desbloqueadas. Bloqueio de intervalo de bytes não é suportado com volumes em camadas na sua matriz virtual.

Medidas recomendadas para reduzir o bloqueio de intervalo de byte incluem:

* Desative o intervalo de byte bloquear na lógica da sua aplicação.
* Utilize localmente afixado de volumes (em vez de em camadas) para os dados associados esta aplicação. Volumes localmente afixados não camada para a nuvem.
* Quando utilizar afixado localmente volumes com bloqueio de intervalo de byte ativada, o volume pode ficar online, mas antes do restauro foi concluído. Nestes casos, tem de aguardar o restauro estar concluída.

## <a name="multiple-arrays"></a>Várias matrizes de
Várias matrizes de virtuais poderão ter de ser implementado para a conta para um conjunto de trabalho crescente de dados que foi transbordam para a nuvem, afetando assim o desempenho do dispositivo. Nestes casos, é melhor dimensionamento dispositivos à medida que cresce o conjunto de trabalho. Isto requer um ou mais dispositivos a adicionar no Centro de dados no local. Ao adicionar os dispositivos, pode:

* Divida o conjunto de dados atual.
* Implemente novas cargas de trabalho em novos dispositivos.
* Se implementar várias matrizes de virtuais, recomendamos que do balanceamento de carga perspetiva, distribuir a matriz em todos os anfitriões hipervisores diferentes.
* Várias matrizes de virtuais (quando configurado como um servidor de ficheiros ou um servidor de iSCSI) podem ser implementadas num distribuídas ficheiro sistema espaço de nomes. Para obter passos detalhados, aceda a [distribuídas ficheiro espaço de nomes de solução do sistema com o guia de implementação de armazenamento de nuvem híbrida](https://www.microsoft.com/download/details.aspx?id=45507). Replicação de sistema de ficheiros distribuído atualmente não é recomendada para utilização com a matriz virtual. 

## <a name="see-also"></a>Consultar também
Saiba como [administrar a matriz de Virtual StorSimple](storsimple-virtual-array-manager-service-administration.md) através do serviço StorSimple Manager.

