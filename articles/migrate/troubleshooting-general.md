---
title: "Resolver problemas de migração do Azure | Microsoft Docs"
description: "Fornece uma descrição geral dos problemas conhecidos no serviço Azure migrar, resolução de problemas e sugestões para erros comuns."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 40faffa3f-1f44-4a72-94bc-457222ed7ac8
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 8c7c79a23ee09a7de35252d7819d1f0e5b1d98c5
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-migrate"></a>Resolver problemas do Azure migrar

## <a name="troubleshoot-common-errors"></a>Resolver erros comuns

[Migrar do Azure](migrate-overview.md) avalia cargas de trabalho no local para a migração para o Azure. Utilize este artigo para resolver problemas quando implementar e utilizar a migração do Azure.


**O recoletor não é possível ligar ao projeto com o ID de projeto e chave posso copiada a partir do portal.**

Certifique-se ter copiados e colados as informações corretas. Para resolver problemas, instale o Microsoft Monitoring Agent (MMA) da seguinte forma:

1. No recoletor VM, transfira o [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Para iniciar a instalação, faça duplo clique o ficheiro transferido.
3. Na configuração, no **boas-vindas** página, clique em **seguinte**. No **termos de licenciamento** página, clique em **concordo** para aceitar a licença.
4. No **pasta de destino**, manter ou modificar a pasta de instalação predefinida > **seguinte**.
5. No **opções de configuração do agente**, selecione **análise de registos do Azure (OMS)** > **seguinte**.
6. Clique em **adicionar** para adicionar uma nova área de trabalho de análise de registos. Cole no ID de projeto e a chave que copiou. Clique depois em **Seguinte**.
7. Certifique-se de que o agente pode ligar ao projeto. Se não pode ser, verifique as definições. Se o agente pode ligar, mas o recoletor não é possível, contacte o suporte.


**Erro 802: posso obter um erro de sincronização de data e hora.**

O relógio do servidor pode ser fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio de recoletor de VM para corresponder à hora atual, da seguinte forma:

1. Abra uma linha de comandos de administrador na VM.
2. Para verificar o fuso horário, execute w32tm /tz.
3. Sincronizar o tempo, execute w32tm /resync.

**A minha chave de projeto tem símbolos "= =" para o fim. Estes são codificadas para outros carateres alfanuméricos pelo recoletor. É esta esperada?**

Sim, cada chave de projeto termina com "= =". O recoletor encripta a chave de projeto antes do processamento-lo.

**Dados de desempenho para os adaptadores de discos e redes mostram como zeros**

Isto pode ocorrer se o nível de definição de estatísticas no vCenter server estiver definido como inferior a três. Em três nível ou superior, o vCenter armazena histórico do desempenho da VM de computação, armazenamento e rede. Para três de nível inferior, vCenter não armazena o armazenamento e dados de rede, mas apenas para dados da CPU e memória. Neste cenário, dados de desempenho mostra como zero no Azure migrar e migrar do Azure fornece a recomendação de tamanho para discos e redes com base nos metadados recolhidos das máquinas no local.

Para ativar a recolha de dados de desempenho de disco e da rede, altere o nível de definições de estatísticas a três. Em seguida, aguarde pelo menos um dia para detetar o seu ambiente e avaliá-lo. 

**Posso agentes instalados e utilizados a visualização de dependência para criar grupos. Agora publique ativação pós-falha, as máquinas mostram ação "Instalar o agente", em vez de "Dependências de vista"**
* Post planeada ou ativação pós-falha não planeada, no local as máquinas estão desativadas e do Azure são aceleradas máquinas equivalentes. Estas máquinas adquiram um endereço MAC diferente. Estes podem adquirir um endereço IP diferente, com base em se o utilizador optou por manter o endereço IP no local ou não. Se forem diferentes endereços IP e MAC, Azure migrar associa as máquinas no local quaisquer dados de dependência de mapa de serviço e pede-lhe utilizador para instalar agentes em vez de visualização de dependências.
* Após a ativação pós-falha de teste, as máquinas no local se mantêm ativadas conforme esperado. Máquinas equivalentes aceleradas no Azure adquiram outro endereço de MAC e poderão adquirir o endereço IP diferente. A menos que o utilizador bloqueia o tráfego de saída OMS dessas máquinas, Azure migrar associa as máquinas no local quaisquer dados de dependência de mapa de serviço e pede-lhe utilizador para instalar agentes em vez de visualização de dependências.


## <a name="troubleshoot-readiness-issues"></a>Resolver problemas de preparação

**Problema** | **Corrigir**
--- | ---
Tipo de arranque não suportado | Alterar a BIOS antes de executar uma migração.
Contagem de discos excede o limite | Remova os discos da máquina antes da migração.
Tamanho do disco excede o limite | Reduzir discos para menos de 4 TB antes da migração. 
Disco disponível na localização especificada | Certifique-se o disco na localização de destino antes de migrar.
Disco disponível para a redundância especificada | O disco deve utilizar o tipo de armazenamento de redundância definido nas definições de avaliação (LRS por predefinição).
Não foi possível determinar a adequabilidade do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo. 
VM com núcleos necessários e a memória não encontrado | Não foi possível ao Azure ajustar um tipo VM adequado. Reduza a memória e o número de núcleos da máquina no local antes de migrar. 
Um ou mais discos unsuitable. | Disponibilizar Certifique-se no local são de discos de 4 TB ou em antes de executar uma migração.
Um ou mais adaptadores de rede unsuitable. | Remova os adaptadores de rede não utilizadas da máquina antes da migração.
Não foi possível determinar a adequabilidade da VM devido a um erro interno. | Tente criar uma nova avaliação para o grupo. 
Não foi possível determinar o adequação a um ou mais discos devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar o adequação a um ou mais adaptadores de rede devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
VM não encontrada para o desempenho de armazenamento necessária. | O desempenho de armazenamento (IOPS/débito) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrada para o desempenho de rede necessários. | O desempenho de rede (na/saída) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de rede da máquina. 
VM não encontrada para o escalão de preço especificado. | Verifique as definições de escalão de preço. 
VM não foi encontrado na localização especificada. | Utilize outra localização de destino antes da migração.
Problemas de suporte de SO Linux | Certifique-se de que está em execução de 64 bits com estas suportado [sistemas operativos](../virtual-machines/linux/endorsed-distros.md).
Problemas de suporte do sistema operativo Windows | Certifique-se de que está a executar um sistema operativo suportado. [Saiba mais](concepts-assessment-calculation.md#azure-suitability-analysis)
Sistema de operativo desconhecido. | Verifique se o sistema operativo especificado no vCenter está correto e repita o processo de deteção.
Requer subscrição do Visual Studio. | Sistemas operativos cliente Windows só são suportados em subscrições do Visual Studio (MSDN).


## <a name="collect-logs"></a>Recolher registos

**Como posso recolher registos no recoletor VM?**

Registo está ativado por predefinição. Os registos estão localizados da seguinte forma:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Para recolher o rastreio de eventos para o Windows, efetue o seguinte:

1. No recoletor VM, abra uma janela de comando do PowerShell.
2. Executar **Get-registo de eventos - LogName aplicação | export-csv eventlog.csv**.

**Como posso recolher registos de tráfego de rede portal?**

1. Abra o browser e navegue até e inicie sessão no [para o portal](https://portal.azure.com).
2. Premir a tecla F12 para iniciar as ferramentas de programador. Se necessário, desmarque a definição **limpar as entradas navegação**.
3. Clique em de **rede** separador e começar a capturar o tráfego de rede:
 - No Chrome, selecione **Preserve registo**. A gravação deve iniciar automaticamente. Um círculo vermelho indica que o tráfego está a ser captura. Se não for apresentada, clique no círculo preto para iniciar
 - No Edge/IE, gravação deve iniciar automaticamente. Se não, clique no botão Reproduzir verde.
4. Tente recriar o erro.
5. Depois de ter encontrou o erro durante a gravação, parar a gravação e guarde uma cópia da atividade registada:
 - No Chrome, clique com botão direito e clique em **guardar como HAR com conteúdo**. Isto zips e exporta os registos como um ficheiro de .har.
 - No Edge/IE, clique em de **exportação capturado o tráfego** ícone. Isto zips e exporta o registo.
6. Navegue para o **consola** separador para verificar a existência de avisos ou erros. Para guardar o registo da consola:
 - No Chrome, clique com botão direito em qualquer local no registo de consola. Selecione **guardar como**, para exportar e zip o registo.
 - No Edge/IE, clique com botão direito sobre os erros e selecione **copie todos os**. 
7. Feche as ferramentas de programador.
 



