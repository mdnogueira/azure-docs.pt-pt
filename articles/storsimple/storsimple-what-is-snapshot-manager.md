---
title: "O que é o Snapshot Manager do StorSimple? | Microsoft Docs"
description: Descreve o Snapshot Manager do StorSimple, respetiva arquitetura e as respetivas funcionalidades.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 38c197c7bc57110b29b1d8cb789d5b7310823da2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Uma introdução para Snapshot Manager do StorSimple

## <a name="overview"></a>Descrição geral
Snapshot Manager do StorSimple é um snap-in Consola de gestão da Microsoft (MMC) que simplifica a gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple e proteção de dados. Com o Snapshot Manager do StorSimple, pode gerir dados do Microsoft Azure StorSimple no Centro de dados e na nuvem como uma solução de armazenamento integrada único, assim simplificar processos de cópia de segurança e reduzindo os custos.

Esta descrição geral apresenta o Snapshot Manager do StorSimple, descreve as suas funcionalidades e explica a respetiva função no Microsoft Azure StorSimple. 

Para obter uma descrição geral do sistema do Microsoft Azure StorSimple completo, incluindo o dispositivo StorSimple, o serviço StorSimple Manager, o Snapshot Manager do StorSimple e o adaptador do StorSimple para o SharePoint, consulte [série 8000 do StorSimple: uma nuvem híbrida solução de armazenamento](storsimple-overview.md). 

> [!NOTE]
> * Não é possível utilizar o Snapshot Manager do StorSimple para gerir o Microsoft Azure StorSimple Virtual matrizes (também conhecido como StorSimple no local dispositivos virtuais).
> * Se planeia instalar a atualização 2 do StorSimple no dispositivo StorSimple, não se esqueça de transferir a versão mais recente do Snapshot Manager do StorSimple e instalá-la **antes de instalar a atualização 2 do StorSimple**. A versão mais recente do Snapshot Manager do StorSimple é retrocompatível e funciona com todas as versões de lançamento do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do Snapshot Manager do StorSimple, terá de atualizá-lo (não tem de desinstalar a versão anterior antes de instalar a nova versão).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Arquitetura e o objetivo do Snapshot Manager do StorSimple
Snapshot Manager do StorSimple fornece uma consola de gestão central que pode utilizar para criar consistente, cópias de segurança de ponto no tempo da local e dados em nuvem. Por exemplo, pode utilizar a consola para:

* Configurar, fazer cópias de segurança e eliminar os volumes.
* Configurar grupos de volume para se certificar de que a cópia de segurança de dados é consistente com aplicações.
* Gerir políticas de cópia de segurança para que os dados de cópias de segurança com base numa agenda predeterminada.
* Criar local e na nuvem instantâneos, que podem ser armazenados na nuvem e utilizados para recuperação após desastre.

O Snapshot Manager do StorSimple obtém a lista de aplicações registado com o fornecedor VSS no anfitrião. Em seguida, para criar cópias de segurança consistentes com aplicações, verifica os volumes utilizados por uma aplicação e sugere os grupos de volume para configurar. Snapshot Manager do StorSimple utiliza estes grupos de volume para gerar cópias de segurança que sejam consistentes com aplicações. (Consistência da aplicação existe quando todos os ficheiros e relacionados são sincronizadas e representam o estado da aplicação num ponto específico no tempo verdadeiro bases de dados.) 

Cópias de segurança do Snapshot Manager do StorSimple ter a forma de instantâneos incrementais, o que capturar apenas as alterações desde a última cópia de segurança. Como resultado, as cópias de segurança necessitam de menos armazenamento e podem ser criadas e restauradas rapidamente. Snapshot Manager do StorSimple utiliza o serviço cópia do Windows Volume sombra de volumes (VSS) para se certificar de que instantâneos capturar os dados consistentes com aplicações. (Para obter mais informações, aceda a integração com a secção do serviço de cópia de sombra de volumes do Windows.) Com o Snapshot Manager do StorSimple, pode criar agendas de cópia de segurança ou fazer cópias de segurança de imediato, conforme necessário. Se tiver de restaurar dados a partir de uma cópia de segurança, permite do Snapshot Manager do StorSimple selecione catálogo de local ou instantâneos de nuvem. Azure StorSimple restaura apenas os dados que é necessário, uma vez que é necessária, que impede que os atrasos na disponibilidade de dados durante as operações de restauro.)

![Arquitetura do Snapshot Manager do StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura do Snapshot Manager do StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume
Pode utilizar o Snapshot Manager do StorSimple para configurar e efetuar cópias de segurança de volumes, os seguintes tipos: 

* **Volumes básicos** – um volume básico é uma partição única num disco básico. 
* **Volumes simples** – um volume simple é um volume dinâmico que contém o espaço em disco a partir de um único disco dinâmico. Um volume simple é composta por uma única região num disco ou várias regiões que estejam ligadas em conjunto no mesmo disco. (Pode criar volumes simples apenas em discos dinâmicos.) Volumes simples não são tolerante a falhas.
* **Volumes dinâmicos** – um volume dinâmico é um volume criado num disco dinâmico. Discos dinâmicos utilizam uma base de dados para controlar as informações sobre volumes que estão contidas em discos dinâmicos no computador. 
* **Volumes dinâmicos com o espelhamento** – foram criados volumes dinâmicos com o espelhamento a arquitetura de RAID 1. Com RAID 1, dados idênticos são escritos no disco dois ou mais, que produz um conjunto espelhado. Um pedido de leitura, em seguida, pode ser processado por qualquer disco que contém os dados solicitados.
* **Volumes partilhados de cluster** – com volumes partilhados de cluster (CSVs), vários nós numa pode de cluster de ativação pós-falha em simultâneo de leitura ou escrita para o mesmo disco. Ativação pós-falha de um nó para outro nó pode ocorrer rapidamente, sem necessidade de uma alteração de propriedade da unidade ou montar, desmontar e remoção de um volume. 

> [!IMPORTANT]
> Não misture os CSVs e não CSVs no mesmo instantâneo. Não é suportada a mistura de csv e não CSVs num instantâneo. 
> 
> 

Pode utilizar o Snapshot Manager do StorSimple para restaurar os grupos de volume completo ou clone volumes individuais e recuperar ficheiros individuais.

* [Volumes e grupos de volume](#volumes-and-volume-groups) 
* [Tipos de cópia de segurança e políticas de cópia de segurança](#backup-types-and-backup-policies) 

Para obter mais informações sobre as funcionalidades do Snapshot Manager do StorSimple e como utilizá-los, consulte [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volume
Com o Snapshot Manager do StorSimple, pode criar volumes e, em seguida, configurá-los em grupos de volume. 

Snapshot Manager do StorSimple utiliza grupos de volume para criar cópias de segurança que sejam consistentes com aplicações. Consistência de aplicações existe quando todos os ficheiros e relacionados bases de dados estão sincronizados e representam o estado de uma aplicação num ponto específico no tempo verdadeiro. Grupos de volume (que são também conhecidos como *grupos de consistência*) formam a base de uma cópia de segurança ou restaurar a tarefa.

Grupos de volume não são os mesmos que os contentores de volume. Um contentor de volume contém um ou mais volumes que partilham uma conta de armazenamento de nuvem e de outros atributos, por exemplo, o consumo de largura de banda e encriptação. Um contentor de volume único pode conter até 256 volumes do StorSimple com aprovisionamento dinâmico. Para mais informações sobre contentores de volume, aceda a [gerir os contentores de volume](storsimple-manage-volume-containers.md). Os grupos de volume são coleções de volumes que configurar para facilitar a operações de cópia de segurança. Se selecionar dois volumes que pertencem aos contentores de volume diferente, colocá-los num grupo de único volume e, em seguida, criar uma política de cópia de segurança para esse grupo de volume, cada volume será efetuada em cópia de segurança no contentor de volume adequado, com a conta de armazenamento adequado.

> [!NOTE]
> Todos os volumes de um grupo de volume tem de ser de um fornecedor de serviços de nuvem única.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o serviço de cópia de sombra de volumes do Windows
Snapshot Manager do StorSimple utiliza o serviço cópia do Windows Volume sombra de volumes (VSS) para capturar os dados consistentes com aplicações. VSS facilita a consistência de aplicações, ao comunicar com aplicações com suporte para VSS para coordenar a criação de instantâneos incrementais. VSS assegura que as aplicações estão temporariamente inativa ou quiescent, quando são tirados instantâneos. 

A implementação do Snapshot Manager do StorSimple de VSS funciona com o SQL Server e os volumes NTFS genéricos. O processo é o seguinte: 

1. Um requerente, o que é normalmente um gestão de dados e a solução de proteção (tais como Snapshot Manager do StorSimple) ou uma aplicação de cópia de segurança, invoca o VSS e pede-lhe para que possa reunir informações de software o escritor da aplicação de destino.
2. VSS contacta o componente de escritor para obter uma descrição dos dados. O escritor devolve a descrição dos dados a cópia de segurança. 
3. VSS sinalizar o escritor para preparar a aplicação de cópia de segurança. O escritor prepara os dados para cópia de segurança, concluindo transações abertas, ao atualizar os registos de transações e assim sucessivamente e, em seguida, notifica VSS.
4. VSS dá instruções ao escritor temporariamente parar arquivos de dados da aplicação e certifique-se de que não são escritos dados no volume enquanto a cópia sombra é criada. Este passo garante a consistência de dados e demora mais do que 60 segundos.
5. VSS dá instruções ao fornecedor para criar a cópia sombra. Fornecedores, que podem ser software - ou baseada em hardware, gerir os volumes que estão atualmente em execução e criar cópias sombra das-los a pedido. O fornecedor cria a cópia sombra e notifica o VSS quando for concluído.
6. VSS entra em contacto com o escritor para notificar a aplicação que pode retomar a e/s e também para confirmar que a e/s foi parada com êxito durante a criação de cópias sombra. 
7. Se a cópia foi concluída com êxito, o VSS devolve localização de cópia para o requerente. 
8. Se os dados foram escritos na enquanto a cópia sombra foi criada, a cópia de segurança será inconsistente. Elimina a cópia sombra VSS e o notifica o requerente. O requerente pode repetir o processo de cópia de segurança automaticamente ou notificar o administrador para repetir, numa altura posterior.

Veja a ilustração seguinte.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo de serviço de cópia de sombra de volumes do Windows** 

## <a name="backup-types-and-backup-policies"></a>Tipos de cópia de segurança e políticas de cópia de segurança
Com o Snapshot Manager do StorSimple, pode fazer uma cópia de segurança de dados e guarde-o localmente e na nuvem. Pode utilizar o Snapshot Manager do StorSimple para cópias de segurança imediatamente, ou pode utilizar uma política de cópia de segurança para criar uma agenda para fazer cópias de segurança automaticamente. Políticas de cópia de segurança também lhe permite especificar o número de instantâneos irão ser mantidos. 

### <a name="backup-types"></a>Tipos de cópia de segurança
Pode utilizar o Snapshot Manager do StorSimple para criar os seguintes tipos de cópias de segurança:

* **Instantâneos locais** – instantâneos locais são cópias de ponto no tempo dos dados de volumes que estão armazenados no dispositivo StorSimple. Normalmente, este tipo de cópia de segurança pode ser criado e restaurado rapidamente. Pode utilizar um instantâneo local como faria com uma cópia de segurança local.
* **Instantâneos de nuvem** – os instantâneos de nuvem são cópias de ponto no tempo dos dados de volume que estão armazenados na nuvem. Um instantâneo na nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferentes, fora das instalações. Os instantâneos de nuvem são particularmente útil em cenários de recuperação após desastre.

### <a name="on-demand-and-scheduled-backups"></a>Cópias de segurança a pedido e agendadas
Com o Snapshot Manager do StorSimple, pode iniciar uma única cópia de segurança seja criado imediatamente, ou pode utilizar uma política de cópia de segurança para agendar a operações de cópia de segurança periódicas.

Uma política de cópia de segurança é um conjunto de regras automatizadas que pode utilizar para agendar cópias de segurança regulares. Uma política de cópia de segurança permite-lhe definir a frequência e os parâmetros para tirar instantâneos de um grupo específico de volume. Pode utilizar políticas para especificar as datas de início e de expiração, horas, frequências e requisitos de retenção, para ambos os locais e instantâneos de nuvem. Uma política é aplicada imediatamente depois de defini-lo. 

Pode utilizar o Snapshot Manager do StorSimple para configurar ou reconfigurar as políticas de cópia de segurança sempre que necessário. 

Configure as seguintes informações para cada política de cópia de segurança que criar:

* **Nome** – o nome exclusivo da política de cópia de segurança selecionado.
* **Tipo** – o tipo de política de cópia de segurança; local instantâneo ou instantâneo na nuvem.
* **Grupo de volume** – o grupo de volume para o qual é atribuída a política de cópia de segurança selecionada.
* **Retenção** – o número de cópias de segurança para manter. Se selecionar a **todos os** caixa, todas as cópias de segurança são mantidas até for atingido o número máximo de cópias de segurança por volume, altura em que a política irá falhar e gerar uma mensagem de erro. Em alternativa, pode especificar um número de cópias de segurança para manter (entre 1 e 64).
* **Data** – a data em que a política de cópia de segurança foi criada.

Para informações sobre como configurar políticas de cópia de segurança, aceda a [utilize o Gestor de instantâneo StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Tarefa de cópia de segurança a monitorização e gestão
Pode utilizar o Snapshot Manager do StorSimple para monitorizar e gerir tarefas de cópia de segurança futuras, agendadas e concluídas. Além disso, o Snapshot Manager do StorSimple fornece um catálogo de até 64 cópias de segurança foi concluídas. Pode utilizar o catálogo para localizar e restaurar volumes ou ficheiros individuais. 

Para informações sobre a monitorização de tarefas de cópia de segurança, aceda a [utilize o Gestor de instantâneo StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [utilizando Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Transferir [Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

