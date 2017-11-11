---
title: "Cópia de segurança cargas de trabalho do DPM para o portal clássico do Azure | Microsoft Docs"
description: "Uma introdução à cópia de segurança de servidores do DPM utilizando o serviço de cópia de segurança do Azure"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: "System Center Data Protection Manager, o data protection manager, a cópia de segurança do dpm"
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: nkolli;giridham;markgal
ms.openlocfilehash: 7f13739c2d3f7fba700b649f27e02913481ca5e5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparar a criação de cópias de segurança de cargas de trabalho para o Azure com o DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Servidor do Backup do Azure (clássica)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (clássica)](backup-azure-dpm-introduction-classic.md)
>
>

Este artigo fornece uma introdução à utilização do Microsoft Azure Backup para proteger os seus servidores do System Center Data Protection Manager (DPM) e cargas de trabalho. Leia-lo, irá compreender:

* Como funciona a cópia de segurança do Azure DPM server
* Os pré-requisitos para alcançar uma experiência de cópia de segurança uniforme
* Foram encontrados típicos erros e como lidar com os mesmos
* Cenários suportados

O System Center DPM cria cópias de segurança de ficheiros e dados de aplicações. Dados de cópia de segurança do DPM podem ser armazenados na banda, disco ou uma cópia de segurança para o Azure com cópia de segurança do Microsoft Azure. O DPM interage com o Backup do Azure da seguinte forma:

* **DPM implementado como uma máquina virtual do servidor ou no local física** — se o DPM é implementado como um servidor físico ou como uma máquina de virtual de Hyper-V no local que pode fazer cópias de segurança dados para um cofre de cópia de segurança do Azure para além do disco e banda cópia de segurança.
* **DPM implementado como uma máquina virtual do Azure** — do System Center 2012 R2 com Update 3, o DPM pode ser implementado como uma máquina virtual do Azure. Se o DPM for implementado como uma máquina virtual do Azure, que pode fazer uma cópia dos dados nos discos do Azure anexados à máquina virtual do DPM Azure ou pode descarregar o armazenamento de dados através de cópias de até um cofre de cópia de segurança do Azure.

## <a name="why-backup-your-dpm-servers"></a>Por que motivo de cópia de segurança os servidores DPM?
As vantagens empresariais do utilizando a cópia de segurança do Azure para cópia de segurança de servidores do DPM incluem:

* Para implementação do DPM no local, pode utilizar o backup do Azure como uma alternativa à implementação de longa duração em banda.
* Para implementações do DPM no Azure, cópia de segurança do Azure permite-lhe descarregar o armazenamento do disco do Azure, permitindo-lhe aumentar verticalmente ao armazenar os dados mais antigos na cópia de segurança do Azure e novos dados em disco.

## <a name="how-does-dpm-server-backup-work"></a>Como funciona a cópia de segurança do DPM server?
Primeiro para fazer uma cópia de segurança de uma máquina virtual, é necessário um instantâneo de ponto no tempo dos dados. O serviço de cópia de segurança do Azure inicia a tarefa de cópia de segurança na hora agendada e aciona a extensão de cópia de segurança para criar um instantâneo. A extensão de cópia de segurança coordena com o serviço VSS no convidado para alcançar a consistência e invoca a API de instantâneos do blob do serviço de armazenamento do Azure, depois de consistência foi atingida. Isto é feito para obter um instantâneo consistente dos discos da máquina virtual, sem ter de encerrá-la.

Depois do instantâneo foi captado, os dados são transferidos pelo serviço de cópia de segurança do Azure para o Cofre de cópia de segurança. O serviço encarrega-se de identificar e transferir apenas os blocos que foram alterados a partir da última cópia de segurança tornar o armazenamento de cópias de segurança e de rede eficiente. Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação. Este ponto de recuperação pode ser visto no portal clássico do Azure.

> [!NOTE]
> Para máquinas virtuais do Linux, é possível apenas ficheiro de cópias de segurança consistentes.
>
>

## <a name="prerequisites"></a>Pré-requisitos
Prepare a cópia de segurança do Azure para cópia de segurança de dados do DPM da seguinte forma:

1. **Criar um cofre de cópia de segurança**. Se ainda não criou um cofre de cópia de segurança na sua subscrição, consulte a versão do portal do Azure deste artigo - [preparar a cópia de segurança de cargas de trabalho para o Azure com o DPM](backup-azure-dpm-introduction.md).

  > [!IMPORTANT]
  > A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança.
  > Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 30 de Novembro de 2017, não poderá utilizar o PowerShell para criar cofres de cópia de segurança. **Até 30 de Novembro de 2017**:
  >- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
  >- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
  >

2. **Transferir as credenciais do cofre** — na cópia de segurança do Azure, carregue o certificado de gestão que criou para o cofre.
3. **Instalar o agente de cópia de segurança do Azure e registar o servidor** — do Backup do Azure, instale o agente em cada servidor do DPM e registar o servidor do DPM no Cofre de cópia de segurança.

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Requisitos (e limitações)
* O DPM pode estar em execução como um servidor físico ou uma máquina virtual de Hyper-V instalada no System Center 2012 SP1 ou System Center 2012 R2. Também pode executar como uma máquina virtual do Azure em execução no System Center 2012 R2 com, pelo menos, o DPM 2012 R2 Update Rollup 3 ou uma máquina virtual do Windows no VMWare, pelo menos, a ser executado no System Center 2012 R2 com Update Rollup 5.
* Se estiver a executar o DPM com o System Center 2012 SP1, deve instalar o Update Rollup 2 para o System Center Data Protection Manager SP1. Isto é necessário antes de poder instalar o agente de cópia de segurança do Azure.
* O servidor DPM deve ter o Windows PowerShell e .net Framework 4.5 instalados.
* O DPM pode criar cópias de segurança a maioria das cargas de trabalho para cópia de segurança do Azure. Para obter uma lista completa de que tem suportado consulte a cópia de segurança do Azure suporta itens abaixo.
* Não não possível recuperar os dados armazenados em cópia de segurança do Azure com a opção "copiar para banda".
* Irá precisar de uma conta do Azure com a funcionalidade de cópia de segurança do Azure ativada. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre [preços da cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Utilizando a cópia de segurança do Azure requer que o agente de cópia de segurança do Azure ser instalada nos servidores que pretende criar cópias de segurança. Cada servidor tem de ter, pelo menos, 10% do tamanho dos dados que está a ser efetuados, disponível como armazenamento local livre. Por exemplo, a cópia de segurança de 100 GB de dados requer um mínimo de 10 GB de espaço livre na localização scratch. Enquanto o mínimo é de 10, 15% de espaço de armazenamento local livre para ser utilizado para a localização da cache é recomendada.
* Dados serão armazenados no armazenamento do cofre do Azure. Não existe nenhum limite para a quantidade de dados que pode criar uma cópia de segurança uma cópia de segurança do Azure do cofre mas o tamanho de uma origem de dados (por exemplo uma máquina virtual ou a base de dados) não deve exceder 54,400 GB.

Estes tipos de ficheiro são suportados para criar uma cópia de segurança do Azure:

* Encriptados (cópias de segurança completas só)
* Comprimidos (cópias de segurança incrementais suportadas)
* Dispersos (cópias de segurança incrementais suportadas)
* Comprimidos e dispersos (tratados como dispersos)

E estes não são suportadas:

* Não são suportados servidores em sistemas de ficheiros sensíveis a maiúsculas.
* Ligações diretas (ignorados)
* (Ignorados) de pontos de reanálise
* Encriptados e comprimidos (ignorados)
* Encriptados e dispersos (ignorados)
* Sequência comprimida
* Sequência dispersa

> [!NOTE]
> No System Center 2012 DPM com SP1 em diante, pode efetuar cópias de segurança segurança de cargas de trabalho protegidas pelo DPM no Azure utilizando a cópia de segurança do Microsoft Azure.
>
>
