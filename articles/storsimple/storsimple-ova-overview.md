---
title: "Descrição geral de matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs"
description: "Descreve a matriz de Virtual StorSimple, uma solução de armazenamento integrada que gere as tarefas de armazenamento entre uma matriz virtual no local e o armazenamento do Microsoft Azure na nuvem."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/16/2017
ms.author: alkohli
ms.openlocfilehash: b9a5797751fa970c569c93e5efe300d4d74319ce
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução à matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

A matriz Virtual do Microsoft Azure StorSimple é uma solução de armazenamento integrada que gere as tarefas de armazenamento entre uma matriz de virtual no local em execução num hipervisor e armazenamento de nuvem do Microsoft Azure. A matriz virtual é um servidor de ficheiros facilmente gerido, eficiente e económico ou uma solução de servidor de iSCSI que elimina muitos dos problemas e as despesas associadas a proteção de armazenamento e os dados da empresa. A matriz virtual é particularmente adequada para o armazenamento de dados de arquivo acedidos com pouca frequência.

Este artigo fornece uma descrição geral da matriz virtual - aqui estão alguns outros recursos:

* Para melhores práticas, consulte [melhores práticas de matriz Virtual StorSimple](storsimple-ova-best-practices.md).
* Para obter uma descrição geral dos dispositivos de série 8000 do StorSimple, aceda a [série 8000 do StorSimple: uma solução de nuvem híbrida](storsimple-overview.md).
* Para informações sobre os dispositivos de série do StorSimple 5000/7000, aceda a [Ajuda Online do StorSimple](http://onlinehelp.storsimple.com/).

A matriz virtual suporta o protocolo Server Message Block (SMB) ou iSCSI. Este é executado na sua infraestrutura existente do hipervisor e fornece a criação de camadas para a nuvem, cópia de segurança de nuvem, restauro rápido, recuperação ao nível do item e funcionalidades de recuperação após desastre.

A tabela seguinte resume as funcionalidades importantes da matriz Virtual StorSimple.

| Funcionalidade | Matriz Virtual do StorSimple |
| --- | --- |
| Requisitos de instalação |Utiliza a infraestrutura de Virtualização (Hyper-V ou VMware) |
| Disponibilidade |Nó único |
| Capacidade total (incluindo a nuvem) |Até 64 capacidade utilizável TB por matriz virtual |
| Capacidade de local |390 GB para a capacidade de utilizável 6.4 TB por matriz virtual (é necessário aprovisionar 500 GB para 8 TB de espaço em disco) |
| Protocolos nativos |iSCSI ou SMB |
| Objetivo de tempo de recuperação (RTO) |iSCSI: inferior a 2 minutos, independentemente do tamanho do |
| Objetivo de ponto de recuperação (RPO) |Cópias de segurança diárias e cópias de segurança a pedido |
| A criação de camadas de armazenamento |Utiliza térmico mapeamento para determinar quais os dados devem ser colocado em camadas ou reduzir |
| Suporte |Infraestrutura de Virtualização suportada pelo fornecedor |
| Desempenho |Varia consoante a infraestrutura subjacente |
| Mobilidade de dados |Pode restaurar o mesmo dispositivo ou ao nível do item recovery (servidor de ficheiros) |
| Camadas de armazenamento |Armazenamento de hipervisor local e na nuvem |
| Tamanho da partilha |Camadas: até 20 TB; afixado localmente: até 2 TB |
| Tamanho do volume |Em camadas: GB 500 TB 5; afixado localmente: 50 GB para 200 GB <br> Reserva local máxima de volumes em camadas é de 200 GB. |
| Instantâneos |Com falhas |
| Recuperação ao nível do item |Sim; os utilizadores podem restaurar a partir de partilhas |

## <a name="why-use-storsimple"></a>Porquê utilizar StorSimple?

StorSimple liga-se os utilizadores e os servidores de armazenamento do Azure em minutos, sem modificações de aplicação.

A tabela seguinte descreve algumas das principais vantagens que fornece a solução de matriz Virtual StorSimple.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |A matriz virtual suporta o iSCSI ou o protocolo SMB. O movimento de dados entre a camada local e o escalão de nuvem é transparente para o utilizador e totalmente integrada. |
| Custos de armazenamento reduzido |Com StorSimple, aprovisionar armazenamento local suficiente para satisfazer as exigências atuais para os dados mais utilizados. Como aumentar necessidades de armazenamento, StorSimple camadas de dados amovíveis económica para armazenamento na nuvem. Os dados são duplicados e comprimidos antes de enviar para a nuvem para reduzir os requisitos de armazenamento e despesas. |
| Gestão de armazenamento simplificada |StorSimple fornece uma gestão centralizada na nuvem utilizando o Gestor de dispositivos do StorSimple para gerir vários dispositivos. |
| Recuperação após desastre melhorada e conformidade |StorSimple facilita a recuperação de desastres mais rápida ao restaurar os metadados imediatamente e restaurar os dados conforme necessário. Isto significa que as operações normais podem continuar com a mínima interrupção. |
| Mobilidade de dados |Camadas de dados na nuvem podem ser acedidos a partir de outros sites para fins de recuperação e de migração. Tenha em atenção que pode restaurar os dados apenas para a matriz de virtual original. No entanto, utilizar funcionalidades de recuperação de desastres para restaurar a matriz virtual completa para outra matriz virtual. |

## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho do StorSimple

Um resumo das cargas de trabalho suportadas do StorSimple é apresentado abaixo.

|Cenário     |Carga de trabalho     |Suportado      |Restrições               |
|-------------|-------------|---------------|---------------------------|
|ROBO  |Partilha de ficheiros     |Sim      |Consulte [os limites máximos para servidor de ficheiros](storsimple-ova-limits.md).<br></br>Consulte [requisitos de sistema para as versões suportadas do SMB](storsimple-ova-system-requirements.md).| Todas as versões     |
|Arquivo de nuvem  |Partilha de ficheiros de arquivo     |Sim      |Consulte [os limites máximos para servidor de ficheiros](storsimple-ova-limits.md).<br></br>Consulte [requisitos de sistema para as versões suportadas do SMB](storsimple-ova-system-requirements.md).| Todas as versões     |

A matriz de Virtual StorSimple é mais adequada para dados acedidos com pouca frequência. Enquanto a matriz virtual tem uma cache local para melhorar o desempenho, os utilizadores devem assumir que o dispositivo dos serviços de ficheiros, o escalão mais baixo de armazenamento (nuvem). Cada matriz virtual pode escrevem e leem ao armazenamento do Azure, aproximadamente 100 Mbps. Essa ligação é partilhada entre todos os pedidos provenientes no dispositivo e pode tornar-se um engarrafamento conforme mostrado no diagrama abaixo.

![Arquivo de nuvem](./media/storsimple-ova-overview/cloud-archiving.png)

Quando vários utilizadores em simultâneo aceder à matriz de virtual, todos eles partilharem a ligação ao Azure líder de mercado para um desempenho inferior. Não existe nenhum garantido de desempenho por utilizador e dispositivo processa pedidos individuais, à medida que chegam.

Matriz Virtual StorSimple não é adequado para cargas de trabalho que requerem elevada disponibilidade. A matriz virtual é um dispositivo de nó única que ocorre no período de indisponibilidade quando as atualizações de software estão instaladas. Os administradores devem planear uma janela de manutenção de 30 minutos 3 - 4 vezes por ano.

## <a name="workflows"></a>Fluxos de trabalho

A matriz de Virtual StorSimple é particularmente adequada para os fluxos de trabalho seguintes:

* [Gestão de armazenamento baseado na nuvem](#cloud-based-storage-management)
* [Independente de localização de cópia de segurança](#location-independent-backup)
* [Recuperação após desastre e proteção de dados](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>gestão de armazenamento baseado na nuvem
Pode utilizar o serviço de Gestor de dispositivos do StorSimple em execução no portal do Azure para gerir os dados armazenados em vários dispositivos e em vários locais. Isto é particularmente útil em cenários de sucursais distribuída. Tenha em atenção que tem de criar instâncias separadas do serviço StorSimple Manager de dispositivos para gerir as matrizes virtuais e físicos dispositivos StorSimple. Note também que a matriz virtual utiliza agora o novo portal do Azure em vez do portal clássico do Azure.

![gestão de armazenamento baseado na nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Independente de localização de cópia de segurança
Com a matriz virtual, instantâneos de nuvem fornecem uma cópia independente de localização, o ponto no tempo de um volume ou partilha. Instantâneos de nuvem estão ativados por predefinição e não podem ser desativados. Todos os volumes e partilhas são cópia de segurança cópias de segurança em simultâneo através de uma única política de cópia de segurança diária, e pode efetuar cópias de segurança ad-hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Recuperação após desastre e proteção de dados
A matriz virtual suporta a proteção de dados seguintes e cenários de recuperação após desastre:

* **Restauro de volume ou partilha** – utilize o restauro como novo fluxo de trabalho para recuperar um volume ou partilha. Utilize esta abordagem para recuperar o volume completo ou a partilha.
* **Ao nível do item recuperação** – partilhas permitirem o acesso simplificado para cópias de segurança recentes. Pode recuperar facilmente um ficheiro individual de uma oferta especial de *.backup* pasta disponível na nuvem. Esta capacidade de restauro é suscitada pelo departamento de utilizador e é necessária nenhuma intervenção administrativa.
* **Recuperação após desastre** – utilizar a capacidade de ativação pós-falha para recuperar todos os volumes ou partilhas a uma matriz de virtual novo. Criar a matriz virtual novo e registá-lo com o serviço do Gestor de dispositivos do StorSimple e efetuar a ativação pós-falha a matriz virtual original. A matriz virtual novo, em seguida, irão assumir os recursos que aprovisionou.

## <a name="storsimple-virtual-array-components"></a>Componentes de matriz Virtual StorSimple

A matriz virtual inclui os seguintes componentes:

* [Matriz virtual](#virtual-array) – um dispositivo de armazenamento de nuvem híbrida com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor.
* [Serviço do Gestor de dispositivos do StorSimple](#storsimple-device-manager-service) – uma extensão do portal do Azure permite-lhe gerir um ou mais dispositivos StorSimple a partir de uma interface web único que pode aceder a partir de localizações geográficas diferentes. Pode utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir serviços, ver e gerir dispositivos e alertas e gerir volumes, partilhas e instantâneos existentes.
* [Interface de utilizador local web](#local-web-user-interface) – uma IU baseada na web que é utilizada para configurar o dispositivo para que possam ligar à rede local e, em seguida, registar o dispositivo com o serviço do Gestor de dispositivos do StorSimple. 
* [Interface de linha de comandos](#command-line-interface) – interface A Windows PowerShell que pode utilizar para iniciar uma sessão de suporte na matriz de virtual.
  As secções seguintes descrevem cada um destes componentes em maior detalhe e explicam como a solução dispõe de dados, atribui o armazenamento e facilita a gestão de armazenamento e da proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz virtual é uma solução de armazenamento de nó único, que fornece armazenamento primário, gere a comunicação com o armazenamento na nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados no dispositivo.

A matriz virtual está disponível um modelo que está disponível para transferência. A matriz virtual tem uma capacidade máxima de 6.4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB, incluindo armazenamento na nuvem.

A matriz virtual tem as seguintes funcionalidades:

* É económica. -Utilizam da sua infraestrutura de Virtualização existente e pode ser implementado na sua hipervisor existente de Hyper-V ou VMware.
* Este reside no Centro de dados e pode ser configurado como um servidor de iSCSI ou de um servidor de ficheiros.
* Está integrado com a nuvem.
* As cópias de segurança são armazenadas na nuvem, que pode facilitar a recuperação após desastre e simplificar a recuperação ao nível do item (ILR).
* Pode aplicar atualizações para a matriz virtual, tal como faria aplicá-las para um dispositivo físico.

> [!NOTE]
> Uma matriz de virtual não pode ser expandida. Por conseguinte, é importante aprovisionar armazenamento adequados ao criar a matriz virtual.

### <a name="storsimple-device-manager-service"></a>Serviço do Gestor de dispositivos do StorSimple

Microsoft Azure StorSimple fornece uma interface de utilizador baseadas na web, o serviço do Gestor de dispositivos do StorSimple, que lhe permite gerir centralmente armazenamento StorSimple. Pode utilizar o serviço StorSimple Manager de dispositivos para realizar as seguintes tarefas:

* Gerir várias matrizes de Virtual StorSimple a partir de um único serviço.
* Configurar e gerir as definições de segurança para as matrizes de Virtual StorSimple. (Encriptação na nuvem está dependente de APIs do Microsoft Azure.)
* Configure as credenciais da conta de armazenamento e as propriedades.
* Configurar e gerir volumes ou partilhas.
* Criar cópias de segurança e restaurar dados em volumes ou partilhas.
* Monitorizar o desempenho.
* Reveja as definições do sistema e identificar informações sobre problemas possíveis.

Utilizar o serviço do Gestor de dispositivos do StorSimple para efetuar a administração diária da sua matriz virtual.

Para obter mais informações, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface de utilizador local web

A matriz virtual inclui uma IU baseada na web que é utilizada para uma única configuração e o registo do dispositivo com o serviço do Gestor de dispositivos do StorSimple. Pode utilizá-lo para encerrar e reiniciar a matriz virtual, executar testes de diagnóstico, atualização de software, alterar a palavra-passe de administrador do dispositivo, ver registos de sistema e contacte Support da Microsoft para um pedido de serviço de ficheiros.

Para informações sobre como utilizar a IU baseada na web, aceda a [utilizar a IU baseada na web para administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha de comandos

Interface do Windows PowerShell incluída permite-lhe iniciar uma sessão de suporte com Support da Microsoft para que o podem ajudar a resolver problemas que poderá encontrar na sua matriz virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento

Para além da matriz virtual e outros componentes, a solução StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido aos dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados na sua matriz virtual:

* [A criação de camadas de armazenamento automática](#automatic-storage-tiering) 
* [Volumes e partilhas afixadas localmente](#locally-pinned-shares-and-volumes)
* [A eliminação de duplicados e compressão de dados em camadas ou uma cópia de segurança para a nuvem](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Cópias de segurança agendadas e a pedido](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>A criação de camadas de armazenamento automática
A matriz virtual utiliza um mecanismo de camadas novo para gerir os dados armazenados em toda a matriz de virtual e a nuvem. Existem apenas duas camadas: o matriz virtual local e o Azure armazenamento na nuvem. A matriz de Virtual StorSimple dispõe automaticamente dados para as camadas com base num mapa térmico, que controla a utilização atual, idade e relações para outros dados. Dados que são mais ativo (hottest) estiver armazenado localmente, enquanto menos dados ativos e inativos é automaticamente migrados para a nuvem. (Todas as cópias de segurança são armazenadas na nuvem.) StorSimple ajusta e reorganiza dados e altere as atribuições de armazenamento como padrões de utilização. Por exemplo, algumas informações podem ficar menos ativas ao longo do tempo. À medida que for progressivamente menos ativa, é camada enviados para a nuvem. Se esse mesmos dados ficam ativos novamente, é camada à matriz de armazenamento.

Dados para um volume ou partilha em camadas específica são garantidos o seu próprio espaço de escalão local (aproximadamente 10% do total de espaço aprovisionado para esse volume ou partilha). Enquanto esta reduz o armazenamento disponível na matriz de virtual para esse volume ou partilha, assegura que a criação de camadas para um volume ou partilha não será afetada pelas necessidades das outras partilhas ou volumes em camadas. Assim uma carga de trabalho muito ocupada, uma partilha ou volume não é possível forçar todas as outras cargas de trabalho para a nuvem.

Os volumes em camadas criados para o iSCSI têm uma reserva local máxima de 200 GB, independentemente do tamanho do volume.

![A criação de camadas de armazenamento automática](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Pode especificar um volume afixado localmente como, caso em que os dados permanecem na matriz de virtual e nunca se camadas para a nuvem. Para obter mais informações, aceda a [afixado localmente partilhas e os volumes](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Volumes e partilhas afixadas localmente

Pode criar partilhas adequadas e como localmente afixados de volumes. Esta capacidade assegura que dados requeridos por aplicações críticas permanecem na matriz virtual e nunca são camados para a nuvem. Localmente afixadas de partilhas e os volumes têm as seguintes funcionalidades:

* Não são sujeitos a latências de nuvem ou problemas de conectividade.
* Ainda beneficiar do StorSimple cópia de segurança e desastre recuperação funcionalidades da nuvem.

Pode restaurar uma partilha afixada localmente ou volume como camadas ou uma partilha em camadas ou volume como localmente afixado. 

Para mais informações sobre volumes localmente afixados, aceda a [utilizar o serviço StorSimple Manager de dispositivos para gerir volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>A eliminação de duplicados e compressão de dados em camadas ou uma cópia de segurança para a nuvem

StorSimple utiliza compressão de dados e a eliminação de duplicados para reduzir os requisitos de armazenamento na nuvem. A eliminação de duplicados reduz a quantidade global de dados armazenados através da eliminação de redundância no conjunto de dados armazenado. Como informação muda, StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações duplicadas.

> [!NOTE]
> Os dados armazenados na matriz de virtual não eliminação de duplicados ou não estão comprimidos. Todos os eliminação de duplicados e compressão ocorre antes dos dados são enviados para a nuvem.

### <a name="scheduled-and-on-demand-backups"></a>Cópias de segurança agendadas e a pedido

Funcionalidades de proteção de dados do StorSimple permitem-lhe criar cópias de segurança a pedido. Além disso, uma agenda de cópia de segurança predefinida assegura que dados é uma cópia de segurança diária. São efetuadas cópias de segurança sob a forma de instantâneos incrementais, que são armazenados na nuvem. Instantâneos, o qual gravar apenas as alterações desde a última cópia de segurança, podem ser criados e restaurados rapidamente. Estes instantâneos podem ser extremamente importantes em cenários de recuperação após desastre, pois substituir sistemas de armazenamento secundário (por exemplo, a cópia de segurança de banda) e, permitem-lhe restaurar dados para o seu centro de dados ou para sites alternativos, se necessário.

## <a name="next-steps"></a>Passos seguintes

Saiba como [preparar o portal de matriz virtual](storsimple-virtual-array-deploy1-portal-prep.md).
