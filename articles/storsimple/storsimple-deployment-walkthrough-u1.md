---
title: "Implementar a Atualização 1 no dispositivo StorSimple | Microsoft Docs"
description: "Descreve os passos e as melhores práticas para implementar a Atualização 1 no serviço e dispositivo StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ac631d3c-3c53-4c9e-9e4a-5c61c0cd8167
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 4d568fb2eca418ca939f7a76ac24197a0457fe47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-on-premises-storsimple-device-update-1"></a>Implementar o dispositivo StorSimple no local (Atualização 1)
> [!div class="op_single_selector"]
> * [Atualização 2](storsimple-deployment-walkthrough-u2.md)
> * [Atualização 1](storsimple-deployment-walkthrough-u1.md)
> * [Versão GA](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>Descrição geral
Bem-vindo à implementação do dispositivo Microsoft Azure StorSimple. Estes tutoriais de implementação aplicam-se à Atualização 1.0 do StorSimple da Série 8000. Esta série de tutoriais explica como configurar o dispositivo StorSimple e inclui uma lista de verificação das configurações, pré-requisitos de configuração e passos de configuração detalhados.

As informações nestes tutoriais pressupõem que leu as precauções de segurança e descompactou, montou em bastidor e instalou os cabos do dispositivo StorSimple. Se ainda precisar de efetuar essas tarefas, comece por ler as [precauções de segurança](storsimple-safety.md). Dependendo do modelo do dispositivo, pode descompactar, montar em bastidor e instalar os cabos seguindo as instruções em:

* [Descompactar, montar em bastidor e instalar os cabos do 8100](storsimple-8100-hardware-installation.md)
* [Descompactar, montar em bastidor e instalar os cabos do 8600](storsimple-8600-hardware-installation.md)

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que consulte a lista de verificação das configurações antes de começar. O processo de implementação e configuração pode demorar algum tempo a concluir.

> [!NOTE]
> As informações de implementação do StorSimple publicadas no site do Microsoft Azure aplicam-se exclusivamente aos dispositivos StorSimple da série 8000. Para obter todas as informações sobre os dispositivos das séries 5000 e 7000, aceda a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obter informações sobre a implementação das séries 5000 e 7000, consulte o [ Guia de Introdução do Sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).
> 
> 

## <a name="deployment-steps"></a>Passos da implementação
Efetue estes passos obrigatórios para configurar o dispositivo StorSimple e ligá-lo ao serviço StorSimple Manager. Para além dos passos obrigatórios, existem passos e procedimentos opcionais que poderão ser necessários durante a implementação. As instruções passo-a-passo para a implementação indicam quando deve efetuar cada um dos seguintes passos opcionais.

| Passo | Descrição |
| --- | --- |
| **PRÉ-REQUISITOS** |Estes têm de ser concluídos no processo de preparação para a implementação futura. |
| Lista de verificação das configurações de implementação. |Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação. |
| Pré-requisitos da implementação. |Estes validam o ambiente como estando preparado para a implementação. |
|  | |
| **IMPLEMENTAÇÃO PASSO-A-PASSO** |Estes passos são obrigatórios para implementar o dispositivo StorSimple para produção. |
| Passo 1: Criar um novo serviço. |Configure a gestão e o armazenamento na nuvem do dispositivo StorSimple. Se já existir um serviço para outros dispositivos StorSimple, ignore este passo. |
| Passo 2: Obter a chave de registo do serviço. |Utilize esta chave para registar e ligar o dispositivo StorSimple ao serviço de gestão. |
| Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple. |Ligue o dispositivo à rede e registe-o com o Azure para concluir a configuração através do serviço de gestão. |
| Passo 4: Concluir a configuração mínima do dispositivo</br>Opcional: Atualizar o dispositivo StorSimple. |Utilize o serviço de gestão para concluir a configuração do dispositivo e ativá-lo para fornecer armazenamento. |
| Passo 5: Criar um contentor de volume. |Crie um contentor para os volumes de aprovisionamento. Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. |
| Passo 6: Criar um volume. |Aprovisione o(s) volume(s) de armazenamento no dispositivo StorSimple para os servidores. |
| Passo 7: Montar, inicializar e formatar um volume.</br>Opcional: Configurar o MPIO. |Ligue os servidores ao armazenamento iSCSI fornecido pelo dispositivo. Opcionalmente, configure o MPIO para se certificar de que os servidores podem tolerar falhas de ligação, da rede e da interface. |
| Passo 8: Efetuar uma cópia de segurança. |Configure a política de cópia de segurança para proteger os seus dados |
|  | |
| **OUTROS PROCEDIMENTOS** |Poderá ter de consultar estes procedimentos durante a implementação da solução. |
| Configurar uma nova conta do Storage para o serviço. | |
| Utilizar o PuTTY para ligar à consola de série do dispositivo. | |
| Obter o IQN de um anfitrião Windows Server. | |
| Criar uma cópia de segurança manual. | |

## <a name="deployment-configuration-checklist"></a>Lista de verificação das configurações de implementação
A seguinte lista de verificação das configurações de implementação descreve as informações que precisa de recolher antes e quando configura o software no dispositivo StorSimple. Preparar com antecedência algumas destas informações ajudará a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Utilize esta lista de verificação para anotar os detalhes de configuração quando implementa o dispositivo.

| Fase | Parâmetro | Detalhes | Valores |
| --- | --- | --- | --- |
| **Instalação dos cabos do dispositivo** |Acesso de série |Configuração inicial do dispositivo |Sim/Não |
|  | | | |
| **Configurar e registar o dispositivo** |Definições da rede de dados 0 |Endereço IP de Dados 0:</br>Máscara de subrede:</br>Gateway:</br>Servidor DNS primário:</br>Servidor NTP primário:</br>IP/FQDN do servidor proxy Web (opcional):</br>Porta de proxy Web: | |
| &nbsp; |Palavra-passe do administrador do dispositivo |A palavra-passe tem de ter entre 8 e 15 carateres e deve incluir minúsculas, maiúsculas, números e carateres especiais. | |
| &nbsp; |Palavra-passe do Snapshot Manager StorSimple |A palavra-passe tem de ter entre 14 e 15 carateres e deve incluir minúsculas, maiúsculas, números e carateres especiais. | |
| &nbsp; |Chave de Registo do Serviço |Esta chave é gerada no Portal Clássico do Azure. | |
| &nbsp; |Chave de Encriptação dos Dados do Serviço |Esta chave é criada quando o dispositivo é registado com o serviço de gestão através do Windows PowerShell para StorSimple. Copie esta chave e guarde-a numa localização segura. | |
|  | | | |
| **Concluir a configuração mínima do dispositivo** |Nome amigável do dispositivo |Este é um nome descritivo do dispositivo. | |
| &nbsp; |Fuso horário |O dispositivo utiliza este fuso horário para todas as operações agendadas. | |
| &nbsp; |Servidor DNS secundário |Esta é uma configuração obrigatória. | |
| &nbsp; |Interface de rede: IPs fixos do controlador de dados 0 |Estes IPs devem ser encaminháveis para a Internet.</br>Endereço IP fixo do controlador 0:</br>Endereço IP fixo do controlador 1: | |
|  | | | |
| **Definições adicionais da interface de rede** |Interface de rede: dados 1</br>Se o iSCSI estiver ativado, não configure o gateway. |Objetivo: nuvem/iSCSI/Não utilizado</br>Endereço IP:</br>Máscara de subrede:</br>Gateway: | |
| &nbsp; |Interface de rede: dados 2</br>Se o iSCSI estiver ativado, não configure o gateway. |Objetivo: nuvem/iSCSI/Não utilizado</br>Endereço IP:</br>Máscara de subrede:</br>Gateway: | |
| &nbsp; |Interface de rede: dados 3</br>Se o iSCSI estiver ativado, não configure o gateway. |Objetivo: nuvem/iSCSI/Não utilizado</br>Endereço IP:</br>Máscara de subrede:</br>Gateway: | |
| &nbsp; |Interface de rede: dados 4</br>Se o iSCSI estiver ativado, não configure o gateway. |Objetivo: nuvem/iSCSI/Não utilizado</br>Endereço IP:</br>Máscara de subrede:</br>Gateway: | |
| &nbsp; |Interface de rede: dados 5</br>Se o iSCSI estiver ativado, não configure o gateway. |Objetivo: nuvem/iSCSI/Não utilizado</br>Endereço IP:</br>Máscara de subrede:</br>Gateway: | |
|  | | | |
| **Criar um contentor de volumes** |Nome do contentor de volume: |Nome do contentor | |
| &nbsp; |Conta do Storage do Azure: |Chave de acesso e nome da conta do Storage associados a este contentor de volume | |
| &nbsp; |Chave de encriptação do armazenamento na nuvem: |Chave de encriptação do armazenamento em cada contentor | |
|  | | | |
| **Criar um volume** |Detalhes de cada volume |Nome do volume: | |
| &nbsp; |&nbsp; |Tamanho: | |
| &nbsp; |&nbsp; |Tipo de utilização: | |
| &nbsp; |&nbsp; |Nome ACR: | |
| &nbsp; |&nbsp; |Política de cópia de segurança predefinida: | |
|  | | | |
| **Montar, inicializar e formatar um volume** |Detalhes de cada servidor de anfitrião que está ligado ao armazenamento |Nome do Windows Server: | |
| &nbsp; |&nbsp; |IQN do Windows Server: | |
| &nbsp; |&nbsp; |Nome do volume do Windows Server: | |
| &nbsp; |&nbsp; |Letra da unidade/ponto de montagem NTFS: | |

## <a name="deployment-prerequisites"></a>Pré-requisitos da implementação
As secções seguintes explicam os pré-requisitos de configuração do serviço StorSimple Manager e do dispositivo StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço StorSimple Manager
Antes de começar, certifique-se de que:

* Tem a conta Microsoft com credenciais de acesso.
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* A subscrição do Microsoft Azure está ativada para o serviço StorSimple Manager. A subscrição deve ser comprada através do [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Tem acesso ao software de emulação do terminal como o PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para o dispositivo no datacenter
Antes de configurar o dispositivo, certifique-se de que:

* O dispositivo é completamente descompactado, montado em bastidor e é feita a instalação dos cabos de alimentação, rede e acesso de série, conforme descrito em:
  
  * [Descompactar, montar em bastidor e instalar os cabos do dispositivo 8100](storsimple-8100-hardware-installation.md)
  * [Descompactar, montar em bastidor e instalar os cabos do dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* As portas na firewall do datacenter estão abertas para permitir tráfego no iSCSI e na nuvem, tal como descrito em [Requisitos de rede do dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Implementação passo-a-passo
Siga as seguintes instruções passo-a-passo para implementar o dispositivo StorSimple no datacenter.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço
Um serviço StorSimple Manager pode gerir diversos dispositivos StorSimple. Execute os seguintes passos para criar uma nova ocorrência do serviço StorSimple Manager.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço. Esta conta do Storage será utilizada quando criar um contentor de volume.
> 
> * Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se tiver ativado a criação automática de uma conta do Storage, vá para [Passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço
Assim que o serviço StorSimple Manager estiver ativo e em execução, será necessário obter a chave de registo do serviço. Utilize esta chave para registar e ligar o dispositivo StorSimple ao serviço.

Execute os seguintes passos no Portal Clássico do Azure.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple
Utilize o Windows PowerShell para StorSimple para concluir a configuração inicial do dispositivo StorSimple, conforme explicado no procedimento seguinte. Terá de utilizar o software de emulação do terminal para concluir este passo. Para mais informações, consulte [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passo 4: Concluir a configuração mínima do dispositivo
Para a configuração mínima do dispositivo StorSimple, é necessário:

* Configurar o servidor DNS secundário.
* Ativar o iSCSI em, pelo menos, uma interface de rede.
* Atribuir endereços IP fixos a ambos os controladores.

Execute os seguintes passos no Portal Clássico do Azure para concluir a configuração mínima do dispositivo.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Passo 5: Criar um contentor de volume
Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. Terá de criar um contentor de volume antes de poder começar a aprovisionar volumes no dispositivo StorSimple.

Execute os seguintes passos no Portal Clássico do Azure para criar um contentor de volume.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passo 6: Criar um volume
Depois de criar um contentor de volume, pode aprovisionar um volume de armazenamento no dispositivo StorSimple para os servidores. Execute os seguintes passos no Portal Clássico do Azure para criar um volume.

> [!IMPORTANT]
> O StorSimple Manager só pode criar volumes de aprovisionamento dinâmico. Não é possível criar volumes de aprovisionamento totais ou parciais.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passo 7: Montar, inicializar e formatar um volume
Os seguintes passos são executados no anfitrião do Windows Server.

> [!IMPORTANT]
> * Para uma maior disponibilidade da solução StorSimple, recomendamos que configure o MPIO nos servidores de anfitrião (opcionais) antes de configurar o iSCSI. A configuração do MPIO nos servidores de anfitrião irá garantir que os servidores podem tolerar falhas de ligação, da rede ou da interface.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI no anfitrião do Windows Server, consulte [Configurar o MPIO para o dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Estas instruções também incluem os passos para montar, inicializar e formatar os volumes do StorSimple.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI no anfitrião do Linux, consulte [Configurar o MPIO para o anfitrião Linux do StorSimple](storsimple-configure-mpio-on-linux.md).
> 
> 

Se decidir não configurar o MPIO, execute os seguintes passos para montar, inicializar e formatar os volumes do StorSimple num anfitrião do Windows Server.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passo 8: Efetuar uma cópia de segurança
As cópias de segurança fornecem proteção para um ponto anterior no tempo dos volumes e melhoram a capacidade de recuperação enquanto minimizam os tempos de restauro. Pode efetuar dois tipos de cópia de segurança no dispositivo StorSimple: instantâneos locais e instantâneos de nuvem. Cada um destes tipos de cópia de segurança pode ser **Agendado** ou **Manual**.

Execute os seguintes passos no Portal Clássico do Azure para criar uma cópia de segurança agendada.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Pode efetuar uma cópia de segurança manual em qualquer altura. Para obter os procedimentos, vá para [Criar uma cópia de segurança manual](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta do Storage para o serviço
Este passo é opcional e só precisa de o executar se não tiver ativado a criação automática de uma conta do Storage com o serviço. É obrigatória uma conta do Storage do Microsoft Azure para criar um contentor de volume do StorSimple.

Se precisar de criar uma conta do Storage do Azure numa região diferente, consulte [Acerca das Contas do Storage do Azure](../storage/common/storage-create-storage-account.md) para obter instruções passo-a-passo.

Execute os seguintes passos no Portal Clássico do Azure, na página **Serviço StorSimple Manager**.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizar o PuTTY para ligar à consola de série do dispositivo
Para ligar ao Windows PowerShell para StorSimple, terá de utilizar o software de emulação do terminal como o PuTTY. Pode utilizar o PuTTY quando aceder ao dispositivo diretamente através da consola de série ou ao abrir uma sessão telnet a partir de um computador remoto.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Procurar e aplicar atualizações
A atualização do dispositivo pode demorar várias horas. Execute os passos seguintes para procurar e aplicar atualizações no dispositivo.
<!--can take 1-4 hours-->

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Para atualizar o dispositivo
1. Na página **Início Rápido** do dispositivo, clique em **Dispositivos**. Selecione o dispositivo físico, clique em **Manutenção** e em **Procurar Atualizações**.  
2. É criada uma tarefa para procurar as atualizações disponíveis. Se as atualizações estiverem disponíveis, a opção **Procurar Atualizações** é alterada para **Instalar Atualizações**. Clique em **Instalar Atualizações**.
3. Será criada uma tarefa de atualização. Monitorize o estado da atualização, navegando até **Tarefas**.
   
   > [!NOTE]
   > Quando a tarefa de atualização é iniciada, o estado é imediatamente apresentado como 50%. O estado muda para 100% apenas depois de concluída a tarefa de atualização. Não existe nenhum estado em tempo real para o processo de atualização.
   > 
   > 
4. Depois de o dispositivo ser atualizado com sucesso, ative as interfaces de rede de Dados 2 e Dados 3, se estas tiverem sido desativadas.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obter o IQN de um anfitrião do Windows Server
Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
Execute os seguintes passos no Portal Clássico do Azure para criar uma cópia de segurança manual, a pedido, de um único volume no dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="configure-mpio"></a>Configurar o MPIO
Multipath I/O (MPIO) é uma funcionalidade opcional e, por predefinição, não está instalada no Windows Server. Deve ser instalada como uma funcionalidade através do Server Manager. Para obter as instruções de instalação do MPIO, vá para [Configurar o MPIO para o dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

Para obter as instruções de instalação do MPIO para o dispositivo StorSimple ligado ao anfitrião Linux, vá para [Configurar o MPIO para o anfitrião Linux](storsimple-configure-mpio-on-linux.md).

> [!NOTE]
> O MPIO não é suportado num dispositivo virtual StorSimple.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Configurar um [dispositivo virtual](storsimple-virtual-device-u2.md).
* Utilizar o [serviço StorSimple Manager](storsimple-manager-service-administration.md) para gerir o dispositivo StorSimple.

