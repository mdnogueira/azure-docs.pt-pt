---
title: "Grupo de máquinas com o Azure migrar de dependências de máquina | Microsoft Docs"
description: "Descreve como criar uma avaliação utilizando dependências de máquina com o serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Máquinas de grupo utilizando o mapeamento de dependência de máquina

Este artigo descreve como criar um grupo de computadores para [Azure migrar](migrate-overview.md) avaliação utilizado um mapeamento de dependência de máquina. Normalmente, utilize este método quando pretender avaliar a grupos de VMs com níveis superiores de confiança, a verificação entre máquina as dependências, antes de executar uma avaliação.



## <a name="prepare-machines-for-dependency-mapping"></a>Preparar máquinas para o mapeamento de dependência
Para incluir máquinas no mapeamento de dependência, terá de transferir e instalar agentes em cada máquina no local que pretende avaliar. Além disso, se tiver máquinas sem conectividade internet, terá de transferir e instalar [OMS gateway](../log-analytics/log-analytics-oms-gateway.md) nos mesmos.

### <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes VM
1. No **descrição geral**, clique em **gerir** > **máquinas**e selecione a máquina necessária.
2. No **dependências** coluna, clique em **instalar agentes**. 
3. No **dependências** página, transfira e instale o Microsoft Monitoring Agent (MMA) e o agente de dependência em cada VM que pretende avaliar.
4. Copie o ID da área de trabalho e a chave. Terá de estes quando instalar o MMA na máquina no local.

### <a name="install-the-mma"></a>Instalar o MMA

Para instalar o agente num computador Windows:

1. Faça duplo clique o agente transferido.
2. No **boas-vindas** página, clique em **seguinte**. No **termos de licenciamento** página, clique em **concordo** para aceitar a licença.
3. No **pasta de destino**, manter ou modificar a pasta de instalação predefinida > **seguinte**. 
4. No **opções de configuração do agente**, selecione **análise de registos do Azure (OMS)** > **seguinte**. 
5. Clique em **adicionar** para adicionar uma nova área de trabalho do OMS. Colar o ID da área de trabalho e a chave que copiou do portal. Clique em **Seguinte**.


Para instalar o agente num computador Linux:

1. Transfira o pacote adequado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
2. Instalar o pacote utilizando o – argumento de instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalar o agente de dependência
1. Para instalar o agente de dependência num computador Windows, clique duas vezes o ficheiro de configuração e siga o assistente.
2. Para instalar o agente de dependência num computador Linux, instale como raiz utilizando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

[Saiba mais](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) sobre sistemas operativos suportados pelo agente de dependência. 

## <a name="create-a-group"></a>Criar um grupo

1. Depois de instalar os agentes, vá para o portal e clique em **gerir** > **máquinas**.
2. O **dependências** coluna deve agora mostrar como **dependências de vista**. Clique na coluna para visualizar as dependências.
3. Para cada máquina, pode verificar:
    - Se o MMA e o agente de dependência são instaladas e se a máquina for detetada.
    - O sistema operativo em execução na máquina.
    - As ligações de entrada e saída da IP e portas.
    - Processos em execução em máquinas.
    - Dependências entre as máquinas.

4. Para mais granulares dependências, clique em modificar o intervalo de tempo. Por predefinição, o intervalo é uma hora. Pode modificar o intervalo de tempo, ou especificar o início e fim datas e duração.
5. Depois de ter identificado dependentes máquinas que pretende agrupar, selecione as máquinas no mapa e clique em **grupo máquinas**.
6. Especifique um nome de grupo. Certifique-se de que a máquina é detetada pelo Azure migrar. Se não executar a deteção processo no local novamente. Pode executar imediatamente uma avaliação se pretender.
7. Clique em **OK** para guardar o grupo.

    ![Crie um grupo com dependências de máquina](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Passos seguintes

- [Saiba como](how-to-create-group-dependencies.md) para otimizar o grupo verificando dependências de grupo
- [Saiba mais](concepts-assessment-calculation.md) sobre como avaliações são calculadas.
