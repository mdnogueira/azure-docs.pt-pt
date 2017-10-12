---
title: "Atualização 2 do dispositivo virtual StorSimple | Microsoft Docs"
description: "Saiba como criar, implementar e gerir um dispositivo virtual StorSimple numa rede virtual do Microsoft Azure. (Aplica-se a Atualização 2 do StorSimple)."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: alkohli
ms.openlocfilehash: 4ed93f9783efcd700db18aa21b5867c951bd2b97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Implementar e gerir um dispositivo virtual StorSimple no Azure
## <a name="overview"></a>Descrição geral
O dispositivo virtual da série 8000 do StorSimple é uma capacidade adicional que é fornecida com a solução do Microsoft Azure StorSimple. O dispositivo virtual StorSimple é executado numa máquina virtual numa rede virtual do Microsoft Azure e pode utilizá-lo para efetuar cópias de segurança e clonar dados a partir dos anfitriões. Este tutorial descreve como implementar e gerir um dispositivo virtual no Azure, sendo isso aplicável a todos os dispositivos virtuais que executam a versão da Atualização 2 e a versão inferior do software.

#### <a name="virtual-device-model-comparison"></a>Comparação do modelo de dispositivo virtual
O dispositivo virtual StorSimple está disponível em dois modelos, um 8010 convencional (anteriormente conhecido como 1100) e um premium 8020 (apresentado na Atualização 2). Uma comparação dos dois modelos é apresentada na tabela em baixo.

| Modelo do dispositivo | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Capacidade máxima** |30 TB |64 TB |
| **VM do Azure** |Standard_A3 (4 núcleos, 7 GB de memória) |Standard_DS3 (4 núcleos, 14 GB de memória) |
| **Compatibilidade de versões** |Versões em execução anteriores ou posteriores à Atualização 2 |Versões em execução da Atualização 2 ou posteriores |
| **Disponibilidade por regiões** |Todas as regiões do Azure |Todas as regiões do Azure que suportam o Armazenamento Premium e VMs DS3 do Azure<br></br> Utilize [esta lista](https://azure.microsoft.com/en-us/regions/services) para ver se ambas as *Máquinas Virtuais > série DS* e o *Armazenamento > Armazenamento em Disco* estão disponíveis na sua região. |
| **Tipo de armazenamento** |Utiliza o armazenamento padrão do Azure para discos locais<br></br> Saiba como [criar uma conta do Storage padrão](../storage/common/storage-create-storage-account.md) |Utiliza o Armazenamento Premium do Azure para discos locais<sup>2</sup> <br></br>Saiba como [criar uma conta de Premium Storage](../storage/common/storage-premium-storage.md) |
| **Orientações para a carga de trabalho** |Obtenção ao nível de item dos ficheiros a partir de cópias de segurança |Cenários de Cloud dev e test, baixa latência, cargas de trabalho de desempenho superior <br></br>Dispositivo secundário para a recuperação após desastre |

<sup>1</sup> *Anteriormente conhecido como 1100*.

<sup>2</sup> *Os dispositivos 8010 e 8020 utilizam o armazenamento padrão do Azure para o escalão de nuvem. A diferença só existe no escalão local dentro do dispositivo*.

Este artigo descreve o processo de implementação passo-a-passo de um dispositivo virtual StorSimple no Azure. Depois de ler este artigo, irá:

* Compreender como o dispositivo virtual é diferente do dispositivo físico.
* Conseguir criar e configurar o dispositivo virtual.
* Ligar à máquina virtual.
* Aprender a trabalhar com o dispositivo virtual.

Este tutorial aplica-se a todos os dispositivos virtuais StorSimple que executam a Atualização 2 e versões superiores.

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Compreender como o dispositivo virtual é diferente do dispositivo físico
O dispositivo virtual StorSimple é uma versão exclusivamente de software do StorSimple que é executada num único nó na máquina virtual do Microsoft Azure. O dispositivo virtual suporta cenários de recuperação após desastre em que o dispositivo físico não está disponível e é adequado para ser utilizado na obtenção ao nível dos itens a partir de cópias de segurança, na recuperação após desastre no local e em cenários de desenvolvimento e teste da nuvem.

#### <a name="differences-from-the-physical-device"></a>Diferenças do dispositivo físico
A tabela seguinte mostra algumas diferenças fundamentais entre o dispositivo virtual StorSimple e o dispositivo físico StorSimple.

|  | Dispositivo físico | Dispositivo virtual |
| --- | --- | --- |
| **Localização** |Reside no datacenter. |É executado no Azure. |
| **Interfaces de rede** |Possui seis interfaces de rede: de DADOS 0 a DADOS 5. |Possui apenas uma interface de rede: DADOS 0. |
| **Registo** |Registado durante o passo de configuração. |O registo é uma tarefa separada. |
| **Chave de encriptação dos dados do serviço** |Volte a gerá-la no dispositivo físico e, em seguida, atualize o dispositivo virtual com a nova chave. |Não é possível voltar a gerar a chave a partir do dispositivo virtual. |

## <a name="prerequisites-for-the-virtual-device"></a>Pré-requisitos para o dispositivo virtual
As secções seguintes explicam os pré-requisitos de configuração para o dispositivo virtual StorSimple. Antes de implementar um dispositivo virtual, consulte as [considerações de segurança para a utilização de um dispositivo virtual](storsimple-security.md).

#### <a name="azure-requirements"></a>Requisitos do Azure
Antes de aprovisionar o dispositivo virtual, terá de efetuar os seguintes preparativos no ambiente do Azure:

* Para o dispositivo virtual, [configure uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Se utilizar o Premium Storage, tem de criar uma rede virtual numa região do Azure que suporte o Premium Storage. As regiões de armazenamento Premium são regiões que correspondem à linha para *Armazenamento no disco* na lista de [Serviços do Azure por Região](https://azure.microsoft.com/en-us/regions/services).
* É recomendado para a utilização do servidor DNS predefinido fornecido pelo Azure em vez de especificar o próprio nome de servidor DNS. Se o nome de servidor DNS não é válido ou se o servidor DNS não é capaz de resolver corretamente os endereços de IP, a criação do dispositivo virtual irá falhar.
* As opções de ponto a site e site a site são opcionais, mas não obrigatórias. Se desejar, pode configurar estas opções para cenários mais avançados.
* Pode criar [máquinas virtuais do Azure](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (servidores anfitriões) na rede virtual que pode utilizar os volumes expostos pelo dispositivo virtual. Estes servidores devem cumprir os seguintes requisitos:                             

  * Ser uma VMs com Windows ou Linux e ter um software iniciador do iSCSI instalado.
  * Estar em execução na mesma rede virtual que o dispositivo virtual.
  * Conseguir estabelecer ligação ao destino do iSCSI do dispositivo virtual através do endereço IP do dispositivo virtual.
* Certifique-se de que configurou o suporte para o tráfego do iSCSI e da nuvem na mesma rede virtual.

#### <a name="storsimple-requirements"></a>Requisitos do StorSimple
Efetue as seguintes atualizações no serviço Azure StorSimple antes de criar um dispositivo virtual:

* Adicionar [registos de controlo de acesso](storsimple-manage-acrs.md) para as VMs que serão servidores de anfitrião para o dispositivo virtual.
* Utilizar uma [Conta do Storage](storsimple-manage-storage-accounts.md#add-a-storage-account) na mesma região que o dispositivo virtual. As contas do Storage em regiões diferentes poderão ter um fraco desempenho. Pode utilizar uma conta Standard ou Premium com o dispositivo virtual. Pode obter mais informações sobre como criar uma [Conta do Storage Standard](../storage/common/storage-create-storage-account.md) ou uma [conta do Premium Storage](../storage/common/storage-premium-storage.md)
* Utilize uma conta do Storage diferente para a criação do dispositivo virtual a partir da conta utilizada para os seus dados. Com a mesma conta do Storage, poderá obter um fraco desempenho.

Certifique-se de que tem as seguintes informações antes de começar:

* Conta do Portal Clássico do Azure com credenciais de acesso.
* Uma cópia da chave de encriptação dos dados de serviço a partir do dispositivo físico.

## <a name="create-and-configure-the-virtual-device"></a>Criar e configurar o dispositivo virtual
Antes de executar estes procedimentos, certifique-se de que cumpriu os [pré-requisitos para o dispositivo virtual](#prerequisites-for-the-virtual-device).

Depois de ter criado uma rede virtual, de ter configurado um serviço StorSimple Manager e registado o dispositivo físico StorSimple com o serviço, pode executar os seguintes passos para criar e configurar um dispositivo virtual StorSimple.

### <a name="step-1-create-a-virtual-device"></a>Passo 1: Configurar um dispositivo virtual
Execute os seguintes passos para criar o dispositivo virtual StorSimple.

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Se a criação do dispositivo virtual falhar neste passo, é possível que não tenha conectividade à Internet. Para obter mais informações, aceda a [Resolver problemas relacionados com falhas de conectividade Internet](#troubleshoot-internet-connectivity-errors) quando criar um dispositivo virtual.

### <a name="step-2-configure-and-register-the-virtual-device"></a>Passo 2: Configurar e registar o dispositivo virtual
Antes de iniciar este procedimento, certifique-se de que tem uma cópia da chave de encriptação de dados do serviço. A chave de encriptação de dados do serviço foi criada quando configurou o primeiro dispositivo StorSimple e foi-lhe pedido que a guardasse numa localização segura. Se não tiver uma cópia da chave de encriptação de dados do serviço, deve contactar o apoio técnico da Microsoft para obter assistência.

Execute os seguintes passos para configurar e registar o dispositivo virtual StorSimple.

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Passo 3: (opcional) Modificar as definições de configuração do dispositivo
A seguinte secção descreve as definições de configuração do dispositivo necessárias para o dispositivo virtual StorSimple se pretender utilizar o CHAP, o Snapshot Manager do StorSimple ou alterar a palavra-passe de administrador de dispositivos.

#### <a name="configure-the-chap-initiator"></a>Configurar o iniciador do CHAP
Este parâmetro contém as credenciais que o sedispositivo virtual (destino) espera dos iniciadores (servidores) que estão a tentar aceder aos volumes. Os iniciadores irão fornecer um nome de utilizador e uma palavra-passe para o CHAP para se identificarem a si próprios no dispositivo durante esta autenticação. Para obter os passos detalhados, consulte [Configurar o CHAP para o dispositivo](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurar o destino do CHAP
Este parâmetro contém as credenciais que o dispositivo virtual utiliza quando um iniciador ativado pelo CHAP solicita uma autenticação mútua ou bidirecional. O dispositivo virtual utilizará um nome de utilizador e palavra-passe para o CHAP reverso para se identificar a si próprio para o iniciador durante este processo de autenticação. Tenha em atenção que as definições de destino do CHAP são definições globais. Quando estas são aplicadas, todos os volumes associados ao dispositivo virtual de armazenamento utilizarão a autenticação do CHAP. Para obter os passos detalhados, consulte [Configurar o CHAP para o dispositivo](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurar a palavra-passe do Snapshot Manager do StorSimple
O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

> [!NOTE]
> Para o dispositivo virtual, o anfitrião do Windows é uma máquina virtual do Azure.
>
>

Quando configurar um dispositivo no Snapshot Manager do StorSimple, será solicitado para fornecer o endereço IP do dispositivo StorSimple e a palavra-passe para autenticar o dispositivo de armazenamento. Para obter passos detalhados, consulte [Configurar palavra-passe do Snapshot Manager do StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo
Quando utiliza a interface do Windows PowerShell para aceder ao dispositivo virtual, ser-lhe-á pedido para introduzir uma palavra-passe de administrador do dispositivo. Para garantir a segurança dos dados, é necessário alterar esta palavra-passe antes de o dispositivo virtual pode ser utilizado. Para obter passos detalhados, consulte [Configurar palavra-passe de administrador dos dispositivos](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Ligar remotamente à máquina virtual
O acesso remoto ao dispositivo virtual através da interface do Windows PowerShell não está ativado por predefinição. Tem de ativar primeiro a gestão remota do dispositivo virtual e, em seguida, ativá-la no cliente que será utilizado para aceder ao dispositivo virtual.

O processo de dois passos para ligar remotamente é apresentado em baixo em detalhe.

### <a name="step-1-configure-remote-management"></a>Passo 1: Configurar a gestão remota
Execute os seguintes passos para configurar a gestão remota do dispositivo virtual StorSimple.

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Passo 2: Aceder remotamente o dispositivo virtual
Depois de ativar a gestão remota na página de configuração do dispositivo StorSimple, pode utilizar a comunicação remota do Windows PowerShell para se ligar ao dispositivo virtual a partir de outra máquina virtual dentro da mesma rede virtual; por exemplo, pode ligar a partir da VM do anfitrião que configurou e utilizou para estabelecer ligação ao iSCSI. Na maioria das implementações, já abriu um ponto final público para aceder à VM do anfitrião que pode utilizar para aceder ao dispositivo virtual.

> [!WARNING]
> **Para maior segurança, recomendamos vivamente que utilize o HTTPS quando ligar para os pontos finais e, em seguida, elimine os pontos finais depois de ter concluído a sessão remota do PowerShell.**
>
>

Deve seguir os procedimentos em [Ligar remotamente ao dispositivo StorSimple](storsimple-remote-connect.md) para configurar remotamente o dispositivo virtual.

## <a name="connect-directly-to-the-virtual-device"></a>Ligar remotamente ao dispositivo virtual
Também pode ligar remotamente ao dispositivo virtual. Se pretender ligar diretamente ao dispositivo virtual a partir de outro computador fora da rede virtual ou fora do ambiente do Microsoft Azure, terá de criar os pontos finais adicionais, tal como descrito no procedimento seguinte.

Execute os seguintes passos para criar um ponto final público no dispositivo virtual.

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Recomendamos que ligue a partir de outra máquina virtual dentro da mesma rede virtual, porque esta prática minimiza o número de pontos finais públicos na rede virtual. Quando utiliza este método, está simplesmente a ligar-se à máquina virtual através de uma sessão de ambiente de trabalho remoto e, em seguida, configure a máquina virtual para ser utilizada tal como faria com qualquer outro cliente Windows numa rede local. Não é necessário acrescentar o número de porta, porque a porta já irá ser conhecida.

## <a name="work-with-the-storsimple-virtual-device"></a>Trabalhar com o dispositivo virtual StorSimple
Agora que já criou e configurou o dispositivo virtual StorSimple, está pronto para começar a trabalhar com o mesmo. Pode trabalhar com contentores de volumes, volumes e políticas de cópia de segurança num dispositivo virtual, tal como faria num dispositivo físico StorSimple; a única diferença é que tem de se certificar de que seleciona o dispositivo virtual a partir da lista de dispositivos. Consulte [Utilizar o serviço StorSimple Manager para gerir um dispositivo virtual](storsimple-manager-service-administration.md) para obter procedimentos passo-a-passo de várias tarefas de gestão do dispositivo virtual.

As secções seguintes abordam algumas das diferenças que irá encontrar quando trabalha com o dispositivo virtual.

### <a name="maintain-a-storsimple-virtual-device"></a>Efetuar a manutenção de um dispositivo virtual StorSimple
Porque é um dispositivo apenas de software, a manutenção do dispositivo virtual é mínima quando comparada com a manutenção do dispositivo físico. Existem as seguintes opções:

* **Atualizações de software** – pode ver a data em que o software foi atualizado pela última vez, juntamente com quaisquer mensagens de estado da atualização. Pode utilizar a opção **Procurar atualizações** na parte inferior da página para efetuar uma procura manual se pretender verificar a existência de novas atualizações. Se as atualizações estiverem disponíveis, clique em **Instalar atualizações** para as instalar. Porque não existe apenas uma única interface no dispositivo virtual, isto significa que vai haver uma ligeira interrupção do serviço quando as atualizações forem aplicadas. O dispositivo virtual será encerrado e reiniciado (se necessário) para aplicar quaisquer atualizações que tenham sido lançadas. Para um procedimento passo-a-passo, consulte [Atualizar o dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
* **Pacote de suporte** – pode criar e carregar um pacote de suporte para o ajudar a resolver os problemas do Microsoft Support com o dispositivo virtual. Para um procedimento passo-a-passo, consulte [Criar e gerir um pacote de suporte](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Contas do Storage para um dispositivo virtual
As contas do Storage são criadas para utilização pelo serviço StorSimple Manager, pelo dispositivo virtual e pelo dispositivo físico. Ao criar as contas do Storage, recomendamos que utilize um identificador de região no nome amigável para ajudar a garantir que a região é consistente em todos os componentes do sistema. Para um dispositivo virtual, é importante que todos os componentes se encontrem na mesma região para evitar problemas de desempenho.

Para um procedimento passo-a-passo, consulte [Adicionar uma conta do Storage](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Desativar um dispositivo virtual StorSimple
Desativar um dispositivo virtual elimina a VM e os recursos criados no aprovisionamento. Depois de o dispositivo virtual ter sido desativado, não é possível restaurar para o estado anterior. Antes de desativar o dispositivo virtual, certifique-se de que para ou elimina os clientes e os anfitriões que dependem dele.

Desativar um dispositivo virtual resulta nas seguintes ações:

* O dispositivo virtual é removido.
* O disco do SO e os discos de dados criados para o dispositivo virtual são removidos.
* O serviço alojado e a rede virtual criados durante o aprovisionamento são retidos. Se não estiver a utilizá-los, deverá eliminá-los manualmente.
* Os instantâneos de nuvem criados para o dispositivo virtual são retidos.

Para um procedimento passo-a-passo, consulte [Desativar e eliminar o dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Assim que o dispositivo virtual é apresentado como desativado na página do serviço StorSimple Manager, pode eliminar o dispositivo virtual a partir da lista de dispositivos na página **Dispositivos**.

### <a name="start-stop-and-restart-a-virtual-device"></a>Iniciar, parar e reiniciar um dispositivo virtual
Ao contrário do dispositivo físico StorSimple, não existe nenhum botão de ligar ou desligar para ativar um dispositivo virtual StorSimple. No entanto, pode haver ocasiões em que terá de parar e reiniciar o dispositivo virtual. Por exemplo, algumas atualizações podem necessitar que a VM seja reiniciada para concluir o processo de atualização. A forma mais fácil de iniciar, parar e reiniciar um dispositivo virtual é utilizar a consola de gestão de máquinas virtuais.

Quando observar a consola de gestão, o estado do dispositivo virtual é **Em funcionamento** porque este é iniciado por predefinição depois de criado. Pode iniciar, parar e reiniciar uma máquina virtual em qualquer altura.

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Repor as predefinições de fábrica
Se decidir que apenas pretende recomeçar com o dispositivo virtual, basta desativá-lo e eliminá-lo e, em seguida, criar um novo. Tal como quando o dispositivo físico é reposto, o novo dispositivo virtual não terá quaisquer atualizações instaladas; por conseguinte, certifique-se de que verifica a existência de atualizações antes de o utilizar.

## <a name="fail-over-to-the-virtual-device"></a>Ativação pós-falha do dispositivo virtual
A recuperação após desastre (DR) é um dos principais cenários para os quais o dispositivo virtual StorSimple foi concebido. Neste cenário, o dispositivo físico StorSimple ou todo o datacenter pode não estar disponível. Felizmente, pode utilizar um dispositivo virtual para restaurar as operações numa localização alternativa. Durante a DR, os contentores de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo virtual. Os pré-requisitos para a DR são: o dispositivo virtual foi criado e configurado, todos os volumes dentro do contentor de volume foram colocados offline e o contentor de volume tem um instantâneo de nuvem associado.

> [!NOTE]
> * Quando utilizar um dispositivo virtual como dispositivo secundário para DR, tenha em atenção que o 8010 tem 30 TB de armazenamento padrão e o 8020 tem 64 TB de Premium Storage. Quanto maior for a capacidade do dispositivo virtual 8020, mais adequado será para um cenário de DR.
> * Não é possível efetuar uma ativação pós-falha ou clonar a partir de um dispositivo com a Atualização 2 para um dispositivo com o software de pré-Atualização 1. No entanto, pode efetuar uma ativação pós-falha de um dispositivo que esteja a executar a Atualização 2 para um dispositivo que esteja a executar a Atualização 1 (1.1 ou 1.2)
>
>

Para um procedimento passo-a-passo, consulte [Ativação pós-falha num dispositivo virtual](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).

## <a name="shut-down-or-delete-the-virtual-device"></a>Encerrar ou eliminar o dispositivo virtual
Se tiver configurado e utilizado anteriormente o dispositivo virtual StorSimple, mas pretender agora parar a acumulação de encargos de computação pela respetiva utilização, pode encerrar o dispositivo virtual. Encerrar o dispositivo virtual não elimina o sistema operativo ou representa discos de dados no armazenamento. Em vez disso, para os encargos acumulados na subscrição, mas continuarão os encargos de armazenamento relativos ao SO e aos discos de dados.

Se eliminar ou encerrar o dispositivo virtual, este será apresentado individualmente como **Offline** na página Dispositivos do serviço StorSimple Manager. Pode optar por desativar ou eliminar o dispositivo se também pretender eliminar as cópias de segurança criadas pelo dispositivo virtual. Para obter mais informações, consulte [Desativar e eliminar um dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Resolver problemas relacionados com falhas de conectividade Internet
Durante a criação de um dispositivo virtual, se não existir conectividade à Internet, o passo de criação falha. Para saber se a falha se deve à conectividade Internet, execute os passos seguintes no portal clássico do Azure:

1. Crie uma máquina virtual do Windows Server 2012 no Azure. Esta máquina virtual deve utilizar a mesma conta de armazenamento, a mesma VNet e a mesma sub-rede que o dispositivo virtual utiliza. Se já tiver um anfitrião do Windows Server existente no Azure que utilize a mesma conta de armazenamento, a mesma VNet e a mesma sub-rede, também pode utilizá-lo para resolver problemas relacionados com a conectividade Internet.
2. Registe remotamente na máquina virtual criada no passo anterior.
3. Abra uma janela de comando dentro da máquina virtual (Win + R e em seguida, escreva `cmd`).
4. Na linha de comandos, escreva o comando seguinte.

    `nslookup windows.net`
5. Se `nslookup` falhar, significa que a falha de conectividade Internet está a impedir o dispositivo virtual de se registar no serviço StorSimple Manager.
6. Faça as alterações necessárias à rede virtual para se certificar de que o dispositivo virtual é capaz de aceder a sites do Azure, como "windows.net".

## <a name="next-steps"></a>Passos seguintes
* Saber como [utilizar o serviço StorSimple Manager para gerir um dispositivo virtual](storsimple-manager-service-administration.md).
* Compreender como [restaurar um volume StorSimple a partir de um conjunto de cópias de segurança](storsimple-restore-from-backup-set.md).
