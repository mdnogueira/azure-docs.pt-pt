---
title: Implementar um dispositivo StorSimple no local | Microsoft Docs
description: "Descreve os passos e as melhores práticas para implementar o serviço e o dispositivo StorSimple. (Aplica-se à versão .3 do Microsoft Azure StorSimple e às versões anteriores.)"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b27f87a2-1363-4e0d-90f7-37b5dd1f21c9
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 2063acbafd6766d00dee9509ee7def73bdc5b982
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-on-premises-storsimple-device"></a>Implementar o dispositivo StorSimple no local
> [!div class="op_single_selector"]
> * [Atualização 2](storsimple-deployment-walkthrough-u2.md)
> * [Atualização 1](storsimple-deployment-walkthrough-u1.md)
> * [Versão GA](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>Descrição geral
Bem-vindo à implementação do dispositivo Microsoft Azure StorSimple. Estes tutoriais de implementação aplicam-se à Versão de Lançamento da Série 8000 do StorSimple, à Atualização 0.1 da Série 8000 do StorSimple, à Atualização 0.2 da Série 8000 do StorSimple e à Atualização 0.3 da Série 8000 do StorSimple. Esta série de tutoriais explica como configurar o dispositivo StorSimple e inclui uma lista de verificação das configurações, pré-requisitos de configuração e passos de configuração detalhados.

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
As secções seguintes explicam os pré-requisitos de configuração do serviço StorSimple Manager, do dispositivo StorSimple e da rede no datacenter.

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
* O dispositivo no datacenter pode ligar-se à rede externa. Execute os seguinte cmdlets do [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) (apresentados nas tabelas em baixo) para validar a conectividade à rede externa. Efetue esta validação num computador (numa rede do datacenter) que tenha conectividade com o Azure e onde irá implementar o dispositivo StorSimple.  

| Para este parâmetro... | Para verificar a validade... | Execute estes comandos/cmdlets. |
| --- | --- | --- |
| **IP**</br>**Sub-rede**</br>**Gateway** |Este é um endereço IPv4 ou IPv6 válido?</br>Esta é uma subrede válida?</br>Este é um gateway válido?</br>Este é um IP duplicado na rede? |`ping ip`</br>`arp -a`</br>Os comandos `ping` e `arp` devem falhar com a indicação de que não existe nenhum dispositivo na rede do datacenter que está a utilizar este IP. |
|  | | |
| **DNS** |Este é um DNS válido e pode resolver os URLs do Azure? |`Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Comando alternativo que pode utilizar:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com` |
| &nbsp; |Verifique se a porta 53 está aberta. Isto só é aplicável se estiver a utilizar um DNS externo para o dispositivo. O DNS interno deve resolver automaticamente os URLs externos. |`Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Mais informações sobre este cmdlet](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/) |
|  | | |
| **NTP** |Acionamos uma sincronização de hora assim que o servidor NTP for introduzido. Verifique se a porta UDP 123 está aberta quando introduzir `time.windows.com` ou os servidores de hora públicos. |[Transfira e utilize este script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca). |
|  | | |
| **Proxy (opcional)** |Esta porta e URI de proxy são válidos? </br> O modo de autenticação está correto? |<code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Este comando deve ser executado imediatamente após a configuração do proxy Web. Se for devolvido o código de estado 200, significa que a ligação foi efetuada com sucesso. |
| &nbsp; |O tráfego é encaminhável através do proxy? |Execute uma vez a validação do DNS, a verificação do NTP ou a verificação do HTTP depois de configurar o proxy no dispositivo. Isto permite perceber se o tráfego está bloqueado no proxy ou noutro local. |
|  | | |
| **Registo** |Verifique se as portas TCP de saída 443, 80, 9354 estão abertas. |`Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Mais informações do cmdlet Test-NetConnection](https://technet.microsoft.com/library/dn372891.aspx) |

## <a name="step-by-step-deployment"></a>Implementação passo-a-passo
Siga as seguintes instruções passo-a-passo para implementar o dispositivo StorSimple no datacenter.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço
Um serviço StorSimple Manager pode gerir diversos dispositivos StorSimple. Para a implementação do primeiro dispositivo StorSimple, terá de criar um novo serviço StorSimple Manager.

> [!IMPORTANT]
> Ignore este passo se já tiver um serviço StorSimple Manager e pretender implementar esse serviço no dispositivo StorSimple.
> 
> 

Execute os seguintes passos para criar uma nova ocorrência do serviço StorSimple Manager.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço. Esta conta do Storage será utilizada quando criar um contentor de volume.
> 
> Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> Se tiver ativado a criação automática de uma conta do Storage, vá para [Passo 2: Obter a chave de registo do serviço](#step-2:-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço
Assim que o serviço StorSimple Manager estiver ativo e em execução, será necessário obter a chave de registo do serviço. Utilize esta chave para registar e ligar o dispositivo StorSimple ao serviço.

Execute os seguintes passos no Portal Clássico do Azure.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple
> [!IMPORTANT]
> Antes de efetuar esta configuração, desligue todas as interfaces de rede que não sejam DADOS 0 nos dois controladores (ativo e passivo).
> 
> 

Utilize o Windows PowerShell para StorSimple para concluir a configuração inicial do dispositivo StorSimple, conforme explicado no procedimento seguinte. Terá de utilizar o software de emulação do terminal para concluir este passo. Para mais informações, consulte [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passo 4: Concluir a configuração mínima do dispositivo
Para a configuração mínima do dispositivo StorSimple, é necessário:

* Configurar o servidor DNS secundário.
* Ativar o iSCSI em, pelo menos, uma interface de rede.
* Atribuir endereços IP fixos a ambos os controladores.

Execute os seguintes passos no Portal Clássico do Azure para concluir a configuração mínima do dispositivo.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Depois de concluída a configuração do dispositivo, tem de procurar as atualizações e, se disponível, instalá-las. As atualizações poderão demorar várias horas a concluir. Siga as instruções em [Procurar e aplicar atualizações](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Passo 5: Criar um contentor de volume
Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. Terá de criar um contentor de volume antes de poder começar a aprovisionar volumes no dispositivo StorSimple.

Execute os seguintes passos no Portal Clássico do Azure para criar um contentor de volume.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passo 6: Criar um volume
Depois de criar um contentor de volume, pode aprovisionar um volume de armazenamento no dispositivo StorSimple para os servidores. Execute os seguintes passos no Portal Clássico do Azure para criar um volume.

> [!IMPORTANT]
> O StorSimple Manager só pode criar volumes de aprovisionamento dinâmico.  Não é possível criar volumes de aprovisionamento totais ou parciais.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passo 7: Montar, inicializar e formatar um volume
> [!IMPORTANT]
> * Para uma maior disponibilidade da solução StorSimple, recomendamos que configure o MPIO no anfitrião do Windows Server (opcional) antes de configurar o iSCSI. A configuração do MPIO nos servidores de anfitrião irá garantir que os servidores podem tolerar falhas de ligação, da rede ou da interface.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI, consulte [Configurar o MPIO para o dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Estas instruções também incluem os passos para montar, inicializar e formatar os volumes do StorSimple.
> 
> 

Se decidir não configurar o MPIO, execute os seguintes passos para montar, inicializar e formatar os volumes do StorSimple.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passo 8: Efetuar uma cópia de segurança
As cópias de segurança fornecem proteção para um ponto anterior no tempo dos volumes e melhoram a capacidade de recuperação enquanto minimizam os tempos de restauro. Pode efetuar dois tipos de cópia de segurança no dispositivo StorSimple: instantâneos locais e instantâneos de nuvem. Cada um destes tipos de cópia de segurança pode ser **Agendado** ou **Manual**.

Execute os seguintes passos no Portal Clássico do Azure para criar uma cópia de segurança agendada.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Pode efetuar uma cópia de segurança manual em qualquer altura. Para obter os procedimentos, vá para [Criar uma cópia de segurança manual](#Create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta do Storage para o serviço
Este passo é opcional e só precisa de o executar se não tiver ativado a criação automática de uma conta do Storage com o serviço. É obrigatória uma conta do Storage do Microsoft Azure para criar um contentor de volume do StorSimple.

Se precisar de criar uma conta do Storage do Azure numa região diferente, consulte [Acerca das Contas do Storage do Azure](../storage/common/storage-create-storage-account.md) para obter instruções passo-a-passo.

Execute os seguintes passos no Portal Clássico do Azure, na página **Serviço StorSimple Manager**.

[!INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizar o PuTTY para ligar à consola de série do dispositivo
Para ligar ao Windows PowerShell para StorSimple, terá de utilizar o software de emulação do terminal como o PuTTY. Pode utilizar o PuTTY quando aceder ao dispositivo diretamente através da consola de série ou ao abrir uma sessão telnet a partir de um computador remoto.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Procurar e aplicar atualizações
Pode atualizar o dispositivo em qualquer lugar e demorará entre 1 e 4 horas. Execute os passos seguintes para procurar e aplicar atualizações no dispositivo.

> [!NOTE]
> Se tiver um gateway configurado numa interface de rede que não seja Dados 0, terá de desativar as interfaces de rede de Dados 2 e Dados 3 antes de instalar a atualização. Vá para **Dispositivos > Configurar** e desative as interfaces de Dados 2 e Dados 3. Deve voltar a ativar estas interfaces quando o dispositivo for atualizado.
> 
> 

#### <a name="to-update-your-device"></a>Para atualizar o dispositivo
1. Na página **Início Rápido** do dispositivo, clique em **Dispositivos**. Selecione o dispositivo físico, clique em **Manutenção** e em **Procurar Atualizações**.  
2. É criada uma tarefa para procurar as atualizações disponíveis. Se as atualizações estiverem disponíveis, a opção **Procurar Atualizações** é alterada para **Instalar Atualizações**. Clique em **Instalar Atualizações**. Pode ser-lhe pedido que desative os Dados 2 e Dados 3 antes de instalar as atualizações. Tem de desativar estas interfaces de rede, Caso contrário, as atualizações poderão falhar.
3. Será criada uma tarefa de atualização. Monitorize o estado da atualização, navegando até **Tarefas**.
   
   > [!NOTE]
   > Quando a tarefa de atualização é iniciada, o estado é imediatamente apresentado como 50%. O estado só muda para 100% depois de concluída a tarefa de atualização. Não existe nenhum estado em tempo real para o processo de atualização.
   > 
   > 
4. Depois de o dispositivo ser atualizado com sucesso, ative as interfaces de rede de Dados 2 e Dados 3, se estas tiverem sido desativadas.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obter o IQN de um anfitrião do Windows Server
Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
Execute os seguintes passos no Portal Clássico do Azure para criar uma cópia de segurança manual, a pedido, de um único volume no dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="next-steps"></a>Passos seguintes
* Configurar um [dispositivo virtual](storsimple-virtual-device-u2.md).
* Utilizar o [serviço StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) para gerir o dispositivo StorSimple.

