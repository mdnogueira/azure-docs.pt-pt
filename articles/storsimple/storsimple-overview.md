---
title: "Descrição geral da solução de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve a criação de camadas do StorSimple, o dispositivo, dispositivo virtual, serviços e gestão de armazenamento e apresenta chaves termos utilizados no StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 10f4b6ec29c2d9539fa7fe61c96581755e630822
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série 8000 do StorSimple: uma solução de armazenamento de nuvem híbrida
## <a name="overview"></a>Descrição geral
Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrada que gere as tarefas de armazenamento entre dispositivos no local e o armazenamento de nuvem do Microsoft Azure. StorSimple é uma solução de rede (SAN) de área de armazenamento eficiente, económica e facilmente gerível que elimina muitos dos problemas e as despesas associadas a proteção de armazenamento e os dados da empresa. Utiliza o dispositivo de série 8000 do StorSimple proprietário, integrado com serviços em nuvem e fornece um conjunto de ferramentas de gestão para uma vista totalmente integrada de todo o armazenamento empresarial, incluindo o armazenamento na nuvem. (As informações de implementação do StorSimple publicadas no Web site do Microsoft Azure aplicam-se apenas a dispositivos série 8000 do StorSimple. Se estiver a utilizar um dispositivo de série do StorSimple 5000/7000, aceda a [StorSimple ajuda](http://onlinehelp.storsimple.com/).)

Utiliza StorSimple [armazenamento em camadas](#automatic-storage-tiering) para gerir os dados armazenados em vários suportes de dados de armazenamento. O conjunto de trabalho atual está armazenada no local em unidades de estado sólido (SSDs), os dados que são utilizados com menos frequência são armazenados em unidades de disco rígido (HDDs) e os dados de arquivo são enviados para a nuvem. Além disso, StorSimple utiliza e compressão de eliminação de duplicados para reduzir a quantidade de armazenamento que consome os dados. Para obter mais informações, aceda a [eliminação de duplicados e compressão](#deduplication-and-compression). Definições das outras chaves termos e conceitos utilizados na documentação de série 8000 do StorSimple, aceda a [terminologia do StorSimple](#storsimple-terminology) no final deste artigo.

Para além da gestão de armazenamento, as funcionalidades de proteção de dados do StorSimple permitem-lhe criar a pedido e cópias de segurança agendadas e, em seguida, armazená-los localmente ou na nuvem. São efetuadas cópias de segurança sob a forma de instantâneos incrementais, o que significa que pode ser criados e restaurados rapidamente. Instantâneos de nuvem podem ser extremamente importantes em cenários de recuperação após desastre, pois substituir sistemas de armazenamento secundário (por exemplo, a cópia de segurança de banda) e, permitem-lhe restaurar dados para o seu centro de dados ou para sites alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Veja o vídeo para uma introdução rápida aos Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Porquê utilizar StorSimple?
A tabela seguinte descreve algumas das principais vantagens que fornece o Microsoft Azure StorSimple.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |Utiliza o protocolo iSCSI invisibly ligar instalações do armazenamento de dados. Isto garante que os dados armazenados na nuvem, no Centro de dados, ou em servidores remotos parece estar armazenado numa única localização. |
| Custos de armazenamento reduzido |Atribui suficientes locais ou de armazenamento na nuvem para satisfazer os pedidos atuais e expande o armazenamento na nuvem apenas quando necessário. Mais reduz os requisitos de armazenamento e despesas por eliminando redundantes versões dos mesmos dados (eliminação de duplicados) e através da utilização de compressão. |
| Gestão de armazenamento simplificada |Fornece ferramentas de administração para configurar e gerir dados armazenados no local, num servidor remoto e na nuvem de sistema. Além disso, pode gerir a cópia de segurança e restaurar as funções a partir de um snap-in Consola de gestão da Microsoft (MMC).|
| Recuperação após desastre melhorada e conformidade |Não necessita de tempo de recuperação expandida. Em vez disso, restaura dados conforme for necessário. Isto significa que as operações normais podem continuar com a mínima interrupção. Além disso, pode configurar políticas para especificar as agendas de cópia de segurança e retenção de dados. |
| Mobilidade de dados |Dados carregados nos serviços de nuvem do Microsoft Azure podem ser acedidos a partir de outros sites para fins de recuperação e de migração. Além disso, pode utilizar StorSimple para configurar aplicações do StorSimple na nuvem em máquinas virtuais (VMs) em execução no Microsoft Azure. As VMs, em seguida, podem utilizar dispositivos virtuais para aceder aos dados armazenados para fins de teste ou de recuperação. |
| Continuidade do negócio |Permite StorSimple aos utilizadores de série de 5000 7000 migrar os seus dados para um dispositivo de série 8000 do StorSimple. |
| Disponibilidade no Portal do Azure Government |StorSimple está disponível no Portal do Azure Government. Para obter mais informações, consulte [implementar o dispositivo StorSimple no local no Portal de administração pública](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Proteção de dados e a disponibilidade |A série 8000 do StorSimple suporta zona redundante armazenamento (ZRS), para além do armazenamento localmente redundante armazenamento (LRS) e georredundante (GRS). Consulte [neste artigo sobre as opções de redundância do armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para detalhes ZRS. |
| Suporte para aplicações críticas |StorSimple permite-lhe identificar os volumes adequados como localmente afixados que por sua vez garante que os dados necessários por aplicações críticas não em camadas para a nuvem. Volumes localmente afixados não são sujeitos a latências de nuvem ou problemas de conectividade. Para mais informações sobre volumes localmente afixados, consulte [utilizar o serviço StorSimple Manager de dispositivos para gerir volumes](storsimple-8000-manage-volumes-u2.md). |
| Baixa latência e elevado desempenho |Pode criar aplicações de nuvem que tirar partido de elevado desempenho, baixa latência as funcionalidades do armazenamento do Azure premium. Para obter mais informações sobre aplicações de nuvem do StorSimple premium, consulte [implementar e gerir um dispositivo de nuvem do StorSimple no Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Componentes do StorSimple
A solução do Microsoft Azure StorSimple inclui os seguintes componentes:

* **Dispositivo do Microsoft Azure StorSimple** – uma matriz de armazenamento híbrida no local que contém SSDs e HDDs, juntamente com os controladores de redundantes e capacidades de ativação pós-falha automática. Os controladores de gerir o armazenamento em camadas, colocando atualmente utilizados (ou quentes) dados no armazenamento local (por dispositivo ou no local servidores), ao mover dados menos utilizados com frequência para a nuvem.
* **Aplicação de nuvem do StorSimple** – também conhecido como o dispositivo Virtual StorSimple, esta é uma versão de software do dispositivo StorSimple que replica a arquitetura e a maioria das capacidades do dispositivo de armazenamento físico híbrida. A aplicação de nuvem do StorSimple é executado num único nó numa máquina virtual do Azure. Dispositivos virtuais Premium que tirar partido do armazenamento premium do Azure, estão disponíveis na atualização 2 e versões posteriores.
* **Serviço do Gestor de dispositivos do StorSimple** – uma extensão do portal do Azure permite-lhe gerir um dispositivo StorSimple ou um dispositivo de nuvem do StorSimple a partir de uma interface web único. Pode utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir serviços, ver e gerir dispositivos, ver alertas, gira volumes e ver e gerir políticas de cópia de segurança e o catálogo de cópias de segurança.
* **O Windows PowerShell para StorSimple** – uma interface de linha de comandos que pode utilizar para gerir o dispositivo StorSimple. O Windows PowerShell para StorSimple tem funcionalidades que lhe permitem registar o dispositivo StorSimple, configure a interface de rede no seu dispositivo, instalar determinados tipos de atualizações, resolver problemas relacionados com o seu dispositivo acedendo a sessão de suporte e alteram o estado do dispositivo. Pode aceder a do Windows PowerShell para StorSimple estabelecendo ligação à consola de série ou ao utilizar a comunicação remota do Windows PowerShell.
* **Cmdlets do Azure PowerShell StorSimple** – uma coleção de cmdlets do Windows PowerShell que permitem-lhe automatizar tarefas de nível de serviço e a migração a partir da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para o [referência de cmdlet](/powershell/module/azure/?view=azuresmps-3.7.0#azure).
* **Snapshot Manager do StorSimple** – um snap-in da MMC, que utiliza grupos de volume e o serviço de cópia de sombra de Volume do Windows para gerar as cópias de segurança consistentes com aplicações. Além disso, pode utilizar o Snapshot Manager do StorSimple para criar as agendas de cópia de segurança e clone ou restauro de volumes.
* **Placa StorSimple para SharePoint** – farms de uma ferramenta de forma transparente expande a proteção de dados e de armazenamento do Microsoft Azure StorSimple para SharePoint Server, ao tornar o armazenamento StorSimple podem ser visualizados e geridos a partir do portal de Administração Central do SharePoint.

O diagrama a seguir fornece uma vista detalhada da arquitetura do Microsoft Azure StorSimple e componentes.

![Arquitetura do StorSimple](./media/storsimple-overview/overview-big-picture.png)

As secções seguintes descrevem cada um destes componentes em maior detalhe e explicam como a solução dispõe de dados, atribui o armazenamento e facilita a gestão de armazenamento e da proteção de dados. A última seção fornece definições para alguns dos termos importantes e conceitos relacionadas com componentes do StorSimple e a respetiva gestão.

## <a name="storsimple-device"></a>Dispositivo StorSimple
O dispositivo do Microsoft Azure StorSimple é uma matriz de armazenamento híbrida no local que fornece armazenamento primário e o iSCSI acesso aos dados armazenados no mesmo. Gere a comunicação com o armazenamento na nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados na solução de Microsoft Azure StorSimple.

O dispositivo StorSimple inclui SSDs e HDDs de unidades de disco rígido, bem como suporte de ativação pós-falha de clustering e automático. Contém um processador partilhado, o armazenamento partilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

* Ligação para um computador anfitrião
* Até seis portas de rede para ligar à rede de área local (LAN)
* Monitorização de hardware
* Memória de acesso aleatório não volátil (NVRAM), que mantém informações mesmo energia é interrompida
* Cluster com suporte para a atualização para gerir atualizações de software em servidores num cluster de ativação pós-falha para que as atualizações tenham mínima ou sem qualquer efeito na disponibilidade do serviço
* Cluster do serviço, as funções, como um cluster de back-end, fornecer elevada disponibilidade e minimizar os efeitos adversos que podem ocorrer se um HDD ou o SSD falha ou for colocado offline

Apenas um controlador é Active Directory em qualquer ponto no tempo. Se o controlador de Active Directory falhar, o controlador de segundo fica ativo automaticamente.

Para obter mais informações, aceda a [StorSimple componentes de hardware e estado](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Pode utilizar StorSimple para criar uma aplicação de nuvem que replica a arquitetura e as capacidades do dispositivo de armazenamento físico híbrida. A aplicação de nuvem do StorSimple (também conhecido como a StorSimple aplicação Virtual) é executado num único nó numa máquina virtual do Azure. (Uma aplicação de nuvem só é possível criar uma máquina virtual do Azure. Não é possível criar um dispositivo StorSimple ou um servidor no local.)

A aplicação de nuvem tem as seguintes funcionalidades:

* Este comporta-se como um dispositivo físico e pode oferecer uma interface de iSCSI para máquinas virtuais na nuvem.
* Pode criar um número ilimitado de aplicações em nuvem na nuvem e ativá-los e desativar o conforme necessário.
* Pode ajudá-lo a simular ambientes no local na recuperação após desastre, desenvolvimento e teste cenários e pode ajudar a obtenção ao nível do item de cópias de segurança.

A aplicação de nuvem do StorSimple está disponível em dois modelos: o dispositivo de 8010 (anteriormente conhecido como o modelo de 1100) e o dispositivo 8020. O dispositivo 8010 tem uma capacidade máxima de 30 TB. O dispositivo 8020, que tira partido do armazenamento premium do Azure, tem uma capacidade máxima de 64 TB. (No locais camadas, armazenamento premium do Azure armazena os dados em SSDs enquanto o armazenamento standard armazena dados em HDDs.) Tenha em atenção que tem de ter uma conta do storage do Azure premium para utilizar o armazenamento premium. Para obter mais informações sobre o armazenamento premium, aceda a [Premium Storage: armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual de Azure](../virtual-machines/windows/premium-storage.md).

Para obter mais informações sobre a aplicação de nuvem do StorSimple, aceda a [implementar e gerir um dispositivo de nuvem do StorSimple no Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Serviço do Gestor de dispositivos do StorSimple
Microsoft Azure StorSimple fornece uma interface de utilizador baseadas na web (o serviço do Gestor de dispositivos do StorSimple) que lhe permite gerir centros de dados e armazenamento na nuvem de forma centralizada. Pode utilizar o serviço StorSimple Manager de dispositivos para realizar as seguintes tarefas:

* Configure definições do sistema para dispositivos StorSimple.
* Configurar e gerir as definições de segurança para dispositivos StorSimple.
* Configure as credenciais de nuvem e de propriedades.
* Configurar e gerir volumes num servidor.
* Configure grupos de volume.
* Cópia de segurança e restaurar dados.
* Monitorizar o desempenho.
* Reveja as definições do sistema e identificar informações sobre problemas possíveis.

Pode utilizar o serviço do Gestor de dispositivos do StorSimple para executar todas as tarefas de administração, exceto aqueles que necessitam de sistema pendente tempo, tais como a configuração inicial e a instalação das atualizações.

Para obter mais informações, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple
O Windows PowerShell para StorSimple fornece uma interface de linha de comandos que pode utilizar para criar e gerir o serviço do Microsoft Azure StorSimple e configurar e monitorizar dispositivos StorSimple. É uma interface de linha de comandos baseado no Windows PowerShell, que inclui cmdlets dedicados para gerir o dispositivo StorSimple. O Windows PowerShell para StorSimple tem funcionalidades que lhe permitem:

* Registe um dispositivo.
* Configure a interface de rede num dispositivo.
* Instale determinados tipos de atualizações.
* Resolver problemas relacionados com o seu dispositivo acedendo a sessão de suporte.
* Altere o estado do dispositivo.

Pode aceder ao Windows PowerShell para StorSimple a partir de uma consola de série (num computador anfitrião ligado diretamente ao dispositivo) ou remotamente através da utilização de comunicação remota do Windows PowerShell. Tenha em atenção que alguns do Windows PowerShell para StorSimple tarefas, tais como o registo de dispositivos inicial, só pode ser efetuado na consola de série.

Para obter mais informações, aceda a [utilize o Windows PowerShell para StorSimple administrar o seu dispositivo](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Cmdlets do Azure PowerShell StorSimple
Os cmdlets do Azure PowerShell StorSimple são um conjunto de cmdlets do Windows PowerShell que permitem-lhe automatizar tarefas de nível de serviço e a migração a partir da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para o [referência de cmdlet](/powershell/module/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>Snapshot Manager do StorSimple
Snapshot Manager do StorSimple é um snap-in da consola de gestão da Microsoft (MMC), que pode utilizar para criar consistente, cópias de segurança de ponto no tempo da local e dados em nuvem. O snap-in é executada num anfitrião baseado no Windows Server. Pode utilizar o Snapshot Manager do StorSimple para:

* Configurar, fazer cópias de segurança e eliminar os volumes.
* Configurar grupos de volume para se certificar de que a cópia de segurança de dados é consistente com aplicações.
* Gerir políticas de cópia de segurança para que os dados são uma cópia de segurança com base numa agenda predeterminada e armazenados numa localização designada (local ou na nuvem).
* Restaure volumes e ficheiros individuais.

As cópias de segurança são capturadas como instantâneos, que apenas as alterações de registo uma vez que o último instantâneo foi tirado e necessitam de menos espaço de armazenamento de cópias de segurança completas. Pode criar agendas de cópia de segurança ou fazer cópias de segurança de imediato, conforme necessário. Além disso, pode utilizar Snapshot Manager do StorSimple para estabelecer as políticas de retenção esse controlo instantâneos quantos serão guardados. Se pretender restaurar dados a partir de uma cópia de segurança, permite do Snapshot Manager do StorSimple, subsequentemente, selecione o catálogo da local ou instantâneos de nuvem. 

Se ocorrer um desastre ou se tiver de restaurar os dados por outro motivo, Snapshot Manager do StorSimple restaura-incrementalmente conforme for necessário. Restauro de dados não necessita que encerrada todo o sistema ao restaurar um ficheiro, substitua o equipamento ou move operações para outro site.

Para obter mais informações, aceda a [Novidades Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador do StorSimple para o SharePoint
Microsoft Azure StorSimple inclui o adaptador do StorSimple para o SharePoint, um componente opcional de forma transparente expande funcionalidades de proteção de dados e armazenamento StorSimple para farms do SharePoint server. O adaptador funciona com um fornecedor de armazenamento de BLOBs remoto (RBS) e a funcionalidade de RBS do SQL Server, permitindo-lhe mover BLOBs para um servidor de cópia de segurança do sistema do Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os dados de BLOBS localmente ou na nuvem, com base na utilização.

O adaptador do StorSimple para SharePoint é gerido a partir do portal de Administração Central do SharePoint. Por conseguinte, gestão do SharePoint permanece centralizada e todo o armazenamento parece estar no farm do SharePoint.

Para obter mais informações, aceda a [StorSimple adaptador para o SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento
Para além do dispositivo StorSimple dedicado, dispositivo virtual e outros componentes, o Microsoft Azure StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido aos dados e reduzir o consumo de armazenamento:

* [A criação de camadas de armazenamento automática](#automatic-storage-tiering) 
* [Aprovisionamento dinâmico](#thin-provisioning) 
* [A eliminação de duplicados e compressão](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>A criação de camadas de armazenamento automática
Microsoft Azure StorSimple dispõe automaticamente os dados na lógicas camadas com base na utilização atual, a idade e a relação a outros dados. Dados que são mais ativos são armazenados localmente, enquanto menos dados ativos e inativos é automaticamente migrados para a nuvem. O diagrama seguinte ilustra esta abordagem de armazenamento.

![Camadas de armazenamento do StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para ativar o acesso rápido, StorSimple armazena dados muito ativos (dados) em SSDs no dispositivo StorSimple. Armazena dados que são ocasionalmente utilizados (transfira dados) em HDDs no dispositivo ou em servidores no Centro de dados. Move os dados inativos, dados de cópia de segurança, e os dados retidos de arquivo ou efeitos de compatibilidade para a nuvem. 

> [!NOTE]
> Na atualização 2 ou posterior, pode especificar um volume afixado localmente como, caso em que os dados permanecem no dispositivo local e não está em camadas para a nuvem. 


StorSimple ajusta e reorganiza dados e altere as atribuições de armazenamento como padrões de utilização. Por exemplo, algumas informações podem ficar menos ativas ao longo do tempo. À medida que for progressivamente menos ativa, é migrada de SSDs em HDDs e, em seguida, para a nuvem. Se esse mesmos dados ficam ativos novamente, é migrada para o dispositivo de armazenamento.

O processo de camadas de armazenamento, ocorre o seguinte:

1. Um administrador de sistema configura uma conta de armazenamento de nuvem do Microsoft Azure.
2. O administrador utiliza a consola de série e o serviço do Gestor de dispositivos do StorSimple (em execução no portal do Azure) para configurar o dispositivo e servidores de ficheiros, criar políticas de proteção de volumes e dados. Máquinas no local (como servidores de ficheiros) utilizam o Internet Small Computer System Interface (iSCSI) para aceder ao dispositivo StorSimple.
3. Inicialmente, StorSimple armazena os dados na camada SSD rápida do dispositivo.
4. Como a camada SSD se aproxima capacidade, StorSimple deduplicates comprime os blocos de dados mais antigos e move-los para a camada HDD.
5. Como a capacidade de abordagens de camada HDD, StorSimple encripta os blocos de dados mais antigos e envia-os de forma segura para a conta de armazenamento do Microsoft Azure através de HTTPS.
6. Microsoft Azure cria várias réplicas dos dados no seu datacenter e num centro de dados remoto, garantindo que os dados podem ser recuperados se ocorrer um desastre.
7. Quando o servidor de ficheiros solicita dados armazenados na nuvem, StorSimple devolve-lo de forma totalmente integrada e armazena uma cópia na camada SSD do dispositivo StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Como StorSimple gere dados em nuvem

StorSimple deduplicates dados de cliente em todos os instantâneos e os dados primários (os dados escritos pelos anfitriões). Enquanto a eliminação de duplicados é excelente eficiência de armazenamento, torna-se à pergunta "o que é na nuvem" complicou. Os dados primários em camadas e os dados de instantâneos sobrepor-se entre si. Um segmento de dados na nuvem único podia ser utilizado como dados primários em camadas e também ser referenciado por vários instantâneos. Todos os instantâneos de nuvem garante que uma cópia de todos os dados de ponto no tempo está bloqueada para a nuvem até esse instantâneo é eliminado.

Dados só são eliminados da nuvem quando existem sem referências para que os dados. Por exemplo, se iremos tirar um instantâneo de todos os dados na nuvem que o dispositivo StorSimple e, em seguida, elimine alguns dados primários, seria vemos o _dados primários_ drop imediatamente. O _da nuvem_ que inclui os dados em camadas e as cópias de segurança, permanece igual. Isto acontece porque há um instantâneo ainda referenciar os dados de nuvem. Depois da nuvem instantâneo é eliminado (e quaisquer outros instantâneos referenciado os mesmos dados), cloud remoções de consumo. Antes de Iremos remover dados em nuvem, iremos Verifique se não existem instantâneos ainda fazem referência a dados. Este processo é denominado _recolha de lixo_ e é um serviço em segundo plano em execução no dispositivo. Remoção de dados em nuvem não está imediata, como o serviço de recolha de lixo verifica a existência de outras referências a que os dados antes da eliminação. A velocidade de recolha de lixo depende o número total de instantâneos e o total de dados. Normalmente, os dados de nuvem foram limpos em menos de uma semana.


### <a name="thin-provisioning"></a>Aprovisionamento dinâmico
Aprovisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível aparece exceder os recursos físicos. Em vez de reservar antecipadamente armazenamento suficiente, StorSimple utiliza o aprovisionamento dinâmico para alocar espaço apenas suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento na nuvem facilita esta abordagem porque StorSimple pode aumentar ou diminuir o armazenamento na nuvem para satisfazer os requisitos evolutivos.

> [!NOTE]
> Volumes localmente afixados não são com aprovisionamento dinâmico. Armazenamento atribuído a um volume só local é aprovisionado na íntegra quando o volume é criado.


### <a name="deduplication-and-compression"></a>A eliminação de duplicados e compressão
Microsoft Azure StorSimple utiliza compressão de dados e a eliminação de duplicados para reduzir os requisitos de armazenamento.

A eliminação de duplicados reduz a quantidade global de dados armazenados através da eliminação de redundância no conjunto de dados armazenado. Como informação muda, StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações desnecessárias. 

> [!NOTE]
> Dados em volumes localmente afixados não eliminação de duplicados ou não estão comprimidos. No entanto, as cópias de segurança de volumes localmente afixados são duplicadas e comprimidas.


## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho do StorSimple
Um resumo das cargas de trabalho suportadas do StorSimple é apresentado abaixo.

| Cenário | Carga de trabalho | Suportado | Restrições | Versão |
| --- | --- | --- | --- | --- |
| Colaboração |Partilha de ficheiros |Sim | |Todas as versões |
| Colaboração |Partilha de ficheiros distribuído |Sim | |Todas as versões |
| Colaboração |SharePoint |Sim* |Suportado apenas com volumes localmente afixados |Atualização 2 e posterior |
| Arquivo |Arquivo de ficheiro simples |Sim | |Todas as versões |
| Virtualização |Máquinas virtuais |Sim* |Suportado apenas com volumes localmente afixados |Atualização 2 e posterior |
| Base de Dados |SQL |Sim* |Suportado apenas com volumes localmente afixados |Atualização 2 e posterior |
| Vigilância de vídeo |Vigilância de vídeo |Sim* |Suportada quando o dispositivo StorSimple dedicado apenas a esta carga de trabalho |Atualização 2 e posterior |
| Cópia de segurança |Cópia de segurança do destino principal |Sim* |Suportada quando o dispositivo StorSimple dedicado apenas a esta carga de trabalho |Update 3 e posterior |
| Cópia de segurança |Cópia de segurança de destino secundária |Sim* |Suportada quando o dispositivo StorSimple dedicado apenas a esta carga de trabalho |Update 3 e posterior |

*Sim &#42; -Solução diretrizes e restrições devem ser aplicadas.*

As seguintes cargas de trabalho não são suportadas pelos dispositivos de série 8000 do StorSimple. Se tiver implementado num StorSimple, estas cargas de trabalho irão resultar numa configuração não suportada.

* Processamento de imagens médicas
* Troca
* VDI
* Oracle
* SAP
* Macrodados
* Distribuição de conteúdo
* Arranque a partir de SCSI

Segue-se uma lista dos componentes de infraestrutura do StorSimple suportado.

| Cenário | Carga de trabalho | Suportado | Restrições | Versão |
| --- | --- | --- | --- | --- |
| Geral |ExpressRoute |Sim | |Todas as versões |
| Geral |DataCore FC |Sim* |Suportado com DataCore SANsymphony |Todas as versões |
| Geral |DFSR |Sim* |Suportado apenas com volumes localmente afixados |Todas as versões |
| Geral |Indexação |Sim* |Volumes em camadas, a indexação de metadados só é suportada (sem dados).<br>Para volumes afixados localmente, a indexação completa é suportada. |Todas as versões |
| Geral |Software antivírus |Sim* |A volumes em camadas, é suportada apenas análise na abertura e fecho.<br> Para volumes afixados localmente, análise completa é suportada. |Todas as versões |

*Sim &#42; -Solução diretrizes e restrições devem ser aplicadas.*

Segue-se uma lista de outro software que são utilizadas com StorSimple para criar soluções.

| Tipo de carga de trabalho | Software utilizados com StorSimple | Versões suportadas|Associar ao guia de soluções| 
| --- | --- | --- | --- |
| Destino de cópia de segurança |Veeam |Veeam v 9 e posterior |[StorSimple como destino de cópia de segurança com Veaam](storsimple-configure-backup-target-veeam.md)|
| Destino de cópia de segurança |Exec VERITAS cópia de segurança |Cópia de segurança Exec 16 e posterior |[StorSimple como destino de cópia de segurança com Exec cópia de segurança](storsimple-configure-backup-target-using-backup-exec.md)|
| Destino de cópia de segurança |VERITAS NetBackup |NetBackup 7.7.x e posterior  |[StorSimple como destino de cópia de segurança com NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Partilha de ficheiros global <br></br> Colaboração |Talon  |[StorSimple com Talon](https://www.talonstorage.com/products/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia do StorSimple
Antes de implementar a sua solução do Microsoft Azure StorSimple, recomendamos que reveja os seguintes termos de licenciamento e definições.

### <a name="key-terms-and-definitions"></a>Termos de chaves e definições
| Duração (acrónimo ou abreviatura) | Descrição |
| --- | --- |
| registo de controlo de acesso (ACR) |Um registo associado um volume no dispositivo Microsoft Azure StorSimple que determina os anfitriões que podem ligar ao mesmo. A determinação é baseada em iSCSI nome qualificado (IQN) de anfitriões (contidos o ACR), que estão a ligar o dispositivo StorSimple. |
| AES 256 |Um algoritmo de encriptação AES (Advanced Standard) 256 bits para encriptar os dados são transmitidos para e da nuvem. |
| tamanho da unidade de alocação (AUS) |A mais pequena quantidade de espaço em disco que pode ser atribuída a conter um ficheiro no seu Windows sistemas de ficheiros. Se um tamanho de ficheiro não é um múltiplo par do tamanho do cluster, espaço adicional tem de ser utilizado para conter o ficheiro (até o seguinte múltiplo do tamanho do cluster) resultando em perda de espaço e a fragmentação do disco rígido. <br>O AUS recomendadas para volumes de Azure StorSimple é 64 KB porque funciona bem com os algoritmos de eliminação de duplicados. |
| a criação de camadas de armazenamento automático |Mover automaticamente os dados menos ativos de SSDs em HDDs e, em seguida, para um escalão na nuvem e, em seguida, ativar a gestão de todo o armazenamento de uma interface de utilizador central. |
| Catálogo de cópias de segurança |Uma coleção de cópias de segurança, normalmente, relacionados pelo tipo de aplicação que foi utilizado. Esta coleção é apresentada no painel do catálogo de cópia de segurança do serviço Gestor de dispositivos do StorSimple da IU. |
| ficheiro de cópia de segurança de catálogo |Um ficheiro que contém uma lista de instantâneos disponíveis atualmente armazenados na base de dados cópia de segurança do Snapshot Manager do StorSimple. |
| política de cópia de segurança |Uma seleção de volumes, tipo de cópia de segurança e um timetable que permite-lhe criar cópias de segurança numa agenda predefinida. |
| objetos de grandes dimensões binários (BLOBs) |Uma coleção de dados binários armazenados como uma única entidade no sistema de gestão da base de dados. Os bLOBs são, normalmente, imagens, áudio ou outros objetos multimedia, embora código executável binário, por vezes, é armazenado como um BLOB. |
| Protocolo de autenticação de Handshake de desafio (CHAP) |Um protocolo utilizado para autenticar o elemento de rede de uma ligação, com base no elemento de rede de partilha uma palavra-passe ou o segredo. CHAP pode ser unidirecional ou mútua. Com unidirecional CHAP, o destino autentica um iniciador. CHAP mútua requer que o destino de autenticar o iniciador e que o iniciador autentica o destino. |
| Clone |Uma cópia duplicada de um volume. |
| Nuvem como uma camada (CaaT) |Nuvem de armazenamento integrado como uma camada na arquitetura de armazenamento para que todo o armazenamento é apresentado como parte da rede de armazenamento de uma empresa. |
| fornecedor de serviço em nuvem (CSP) |Um fornecedor de serviços de computação na nuvem. |
| instantâneo na nuvem |Uma cópia de ponto no tempo dos dados do volume que são armazenadas na nuvem. Um instantâneo na nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferentes, fora das instalações. Os instantâneos de nuvem são particularmente útil em cenários de recuperação após desastre. |
| Chave de encriptação de armazenamento na nuvem |Uma palavra-passe ou uma chave utilizados pelo seu dispositivo StorSimple para aceder aos dados encriptados enviados pelo seu dispositivo para a nuvem. |
| atualização com suporte para cluster |Gerir atualizações de software em servidores num cluster de ativação pós-falha para que as atualizações tenham mínima ou sem qualquer efeito na disponibilidade do serviço. |
| DataPath |Uma coleção de unidades funcionais que efetuar operações de processamento de dados entre ligado. |
| Desativar |Uma ação permanente que interrompe a ligação entre o dispositivo StorSimple e o serviço de nuvem associada. Instantâneos de nuvem do dispositivo permanecem após este processo e podem ser clonados ou utilizados para recuperação após desastre. |
| espelhamento do disco |Unidades de replicação de volumes de disco lógico no disco rígido separadas em tempo real para garantir a disponibilidade contínua. |
| espelhamento de disco dinâmico |Replicação de volumes de disco lógico em discos dinâmicos. |
| discos dinâmicos |Formato de volume de disco que utiliza o Gestor de discos lógicos (LDM) para armazenar e gerir os dados entre vários discos físicos. Os discos dinâmicos podem ser alargados para fornecer mais espaço livre. |
| Inclusão de Bunch de discos (EBOD) expandida |Um bastidor secundário do dispositivo Microsoft Azure StorSimple que contém discos de disco rígido extra de armazenamento adicional. |
| Aprovisionamento FAT |Um convencional aprovisionamento de armazenamento no armazenamento de que o espaço é alocado com base no antecipado necessidades (e, normalmente, se não ultrapassa a necessidade de atual). Consulte também *aprovisionamento dinâmico*. |
| unidade de disco rígido (HDD) |Uma unidade que utiliza rodar platters para armazenar dados. |
| armazenamento de nuvem híbrida |Arquitetura de armazenamento que utiliza recursos locais e fora das instalações, incluindo o armazenamento na nuvem. |
| Internet Small Computer System Interface (iSCSI) |Um padrão de rede armazenamento baseado em IP do protocolo de Internet para ligar o equipamento de armazenamento de dados ou instalações. |
| iniciador iSCSI |Um componente de software que permite que um computador anfitrião com o Windows para ligar a uma rede de armazenamento baseado em iSCSI externo. |
| iSCSI nome qualificado (IQN) |Um nome exclusivo que identifica um destino de iSCSI ou de iniciador. |
| destino iSCSI |Um componente de software que fornece iSCSI centralizado subsistemas de disco em redes de armazenamento. |
| em direto arquivo |Uma abordagem de armazenamento na qual os dados de arquivo estão acessíveis sempre (não são armazenadas off-site em banda, por exemplo). Microsoft Azure StorSimple utiliza arquivar em direto. |
| volume afixado localmente |um volume que reside no dispositivo e nunca é camado para a nuvem. |
| instantâneo local |Uma cópia de ponto no tempo dos dados do volume que são armazenados no dispositivo Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Uma solução poderosa constituída por um dispositivo de armazenamento do Centro de dados e o software que permite que as organizações de TI tirar partido do armazenamento na nuvem como se fosse o armazenamento do Centro de dados. StorSimple simplifica a gestão de dados e proteção de dados ao reduzir os custos. A solução consolida primária armazenamento, arquivo, cópia de segurança e recuperação após desastre (DR) através da integração totalmente integrada com a nuvem. Ao combinar a gestão de dados de armazenamento e da nuvem SAN uma plataforma de classe empresarial, os dispositivos StorSimple ativar velocidade, simplicidade e fiabilidade para todas as necessidades relacionadas com o armazenamento. |
| Energia e arrefecimento módulo (PCM) |Componentes de hardware do dispositivo StorSimple constituída por fontes de alimentação e arrefecimento ventoinha, por conseguinte, o nome de energia e arrefecimento módulo. A inclusão principal do dispositivo tem dois 764W PCMs ao passo que o bastidor EBOD tem dois 580W PCMs. |
| Inclusão principal |Inclusão principal do dispositivo StorSimple que contém os controladores de plataforma da aplicação. |
| Objetivo de tempo de recuperação (RTO) |A quantidade máxima de tempo que deve ser expended antes de um processo empresarial ou o sistema está totalmente restaurada depois de um desastre. |
| série attached SCSI (SAS) |Um tipo de unidade de disco rígido (HDD). |
| chave de encriptação de dados do serviço |Uma chave disponibilizada para qualquer novo dispositivo StorSimple que regista com o serviço do Gestor de dispositivos do StorSimple. Os dados de configuração transferidos entre o serviço do Gestor de dispositivos do StorSimple e o dispositivo são encriptados utilizando uma chave pública e, em seguida, podem ser desencriptados apenas no dispositivo com uma chave privada. Chave de encriptação de dados do serviço permite que o serviço obter esta chave privada para desencriptação. |
| Chave de registo do serviço |Uma chave que ajuda a registar o dispositivo StorSimple com o serviço do Gestor de dispositivos do StorSimple, para que seja apresentado no portal do Azure para obter ações de gestão. |
| Small Computer System Interface (SCSI) |Um conjunto de normas de fisicamente ligação de computadores e passar dados entre eles. |
| unidade de estado sólido (SSD) |Um disco que não contém as peças; Por exemplo, uma unidade flash. |
| conta de armazenamento |Um conjunto de credenciais de acesso ligado à sua conta de armazenamento para um fornecedor de serviços de nuvem especificada. |
| Adaptador do StorSimple para o SharePoint |Um componente do Microsoft Azure StorSimple que transparente expande a proteção de dados e armazenamento StorSimple para farms de servidores do SharePoint. |
| Serviço do Gestor de dispositivos do StorSimple |Extensão do portal do Azure permite-lhe gerir o Azure StorSimple no local e os dispositivos virtuais. |
| Snapshot Manager do StorSimple |Snap-in uma consola de gestão da Microsoft (MMC) para gerir as operações de cópia de segurança e restauro no Microsoft Azure StorSimple. |
| Efetuar cópia de segurança |Uma funcionalidade que permite ao utilizador efetuar uma cópia de segurança interativa de um volume. É uma maneira alternativa de fazer uma cópia de segurança manual de um volume, por oposição a fazer uma cópia de segurança automatizada através de uma política definida. |
| Aprovisionamento dinâmico |Método de otimizar a eficiência com que o espaço de armazenamento disponível é utilizado em sistemas de armazenamento. No aprovisionamento dinâmico, o armazenamento é alocado entre vários utilizadores com base no espaço mínimo necessário para cada utilizador em qualquer momento. Consulte também *aprovisionamento fat*. |
| Criação de camadas |Arranging dados em agrupamentos lógicos com base na utilização atual, a idade e a relação a outros dados. StorSimple dispõe automaticamente os dados em camadas. |
| Volume |Áreas de armazenamento lógica apresentadas sob a forma de unidades. Volumes do StorSimple correspondem aos volumes montados pelo anfitrião, incluindo as que são detetados através da utilização de iSCSI e um dispositivo StorSimple. |
| contentor de volume |Um agrupamento de volumes e as definições que se aplicam aos mesmos. Todos os volumes no dispositivo StorSimple estão agrupados em contentores de volume. Definições de contentor de volume incluem as contas de armazenamento, as definições de encriptação de dados enviados para a nuvem com as chaves de encriptação associado e largura de banda consumida para operações que envolve a nuvem. |
| grupo de volume |No Snapshot Manager do StorSimple, um grupo de volume é uma coleção de volumes configurados para facilitar o processamento de cópia de segurança. |
| Serviço de cópia sombra de volumes (VSS) |Um serviço de sistema operativo Windows Server que facilita a consistência da aplicação ao comunicar com aplicações com suporte para VSS para coordenar a criação de instantâneos incrementais. VSS assegura que as aplicações estão temporariamente Inativas, quando são tirados instantâneos. |
| Windows PowerShell para StorSimple |Uma interface de linha de comandos baseada no Windows PowerShell utilizada para operar e gerir o dispositivo StorSimple. Enquanto mantém algumas das funcionalidades básicas do Windows PowerShell, esta interface tem cmdlets dedicados adicionais que são adaptados para gerir um dispositivo StorSimple. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [segurança do StorSimple](storsimple-8000-security.md).

