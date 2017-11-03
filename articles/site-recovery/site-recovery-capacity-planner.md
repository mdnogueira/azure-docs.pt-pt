---
title: "Estimar a capacidade de replicação no Azure | Microsoft Docs"
description: Utilize este artigo para estimar a capacidade ao replicar com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: nisoneji
ms.openlocfilehash: 840a559a82f3227a865d3c606b2fa321cb6144ab
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Planear a capacidade para proteger as VMs de Hyper-V com a recuperação de Site

A ferramenta de Planeador de capacidade do Azure Site Recovery ajuda a descobrir os requisitos de capacidade quando replicar VMs de Hyper-V com o Azure Site Recovery.

Utilize o planeador de capacidade de recuperação de Site para analisar o seu ambiente de origem e cargas de trabalho, precisará de largura de banda de estimativa e recursos do servidor que precisará para a localização de origem e os recursos (máquinas virtuais e armazenamento, etc.), que necessita na localização de destino.

Pode executar a ferramenta em alguns dos modos de:

* **Planear rápida**: executar a ferramenta neste modo ao obter projeções de rede e servidor com base num número médio de VMs, discos, armazenamento e a taxa de alteração.
* **Planeamento de detalhado**: executar a ferramenta neste modo e forneça detalhes de cada carga de trabalho ao nível da VM. Analisar a compatibilidade da VM e obter projeções de rede e servidor.

## <a name="before-you-start"></a>Antes de começar


1. Recolha informações sobre o ambiente, incluindo VMs, discos por VM, de armazenamento por disco.
2. Identifica a taxa de alteração (renovação) diária para os dados replicados. Para tal, transfira o [ferramenta o planeamento de capacidade do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta ferramenta. Recomendamos que esta ferramenta é executada através de uma semana para capturar médias.
   

## <a name="run-the-quick-planner"></a>Executar o planeador rápido
1. Transfira e abra o [Planeador de capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel) ferramenta. Terá de executar macros, por isso, selecione para activar a edição e permitir que os conteúdos quando lhe for pedido.
2. No **selecione um tipo de planner** selecione **Planner rápida** da caixa de listagem.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started.png)
3. No **Capacity Planner** folha de cálculo, introduza as informações necessárias. Terá de preencher todos os campos dentro de um círculo vermelho na captura de ecrã abaixo.

   * No **selecione o seu cenário**, escolha **Hyper-V para o Azure** ou **VMware/físico para o Azure**.
   * No **alterações dos dados diários médios taxa (%)**, colocar as informações que recolher utilizando o [ferramenta o planeamento de capacidade do Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou [Planeador de implementação do Azure Site Recovery](./site-recovery-deployment-planner.md).  
   * O **compressão** definição não é utilizada quando replicar VMs Hyper-V para o Azure. Para compressão, utilize uma aplicação de terceiros, tais como Riverbed.
   * No **entradas de retenção**, especificar quanto réplicas devem ser mantidas, em horas.
   * No **número de horas em que a replicação inicial para o lote de máquinas virtuais deve concluir** e **número de máquinas virtuais por cada lote de replicação inicial**, introduza as definições que são utilizadas para requisitos de replicação inicial de computação.  Quando a recuperação de sites é implementada, o conjunto completo de dados inicial deve ser carregado.

   ![Entradas](./media/site-recovery-capacity-planner/inputs.png)
4. Depois de ter a colocar os valores para o ambiente de origem, a saída apresentada inclui:

   * **Largura de banda necessária para replicação de diferenças** (MB/seg). Largura de banda de rede para a replicação de diferenças é calculada sobre a taxa média de alteração de dados diária.
   * **Largura de banda necessária para a replicação inicial** (MB/seg). Largura de banda de rede para a replicação inicial é calculada nos valores que colocar na replicação inicial.
   * **Armazenamento necessário (em GB somando)** é o armazenamento do Azure total necessário.
   * **Total de IOPS em contas de armazenamento standard** é calculado com base no tamanho da unidade de IOPS 8 K em contas de armazenamento standard total.  Para o planeador rápido, o número é calculado com base em todos os discos de VMs de origem e a taxa de alteração de dados diárias. Para o planeador de detalhado, o número é calculado com base no número total de VMs que estão mapeadas para standard VMs do Azure e alterações dos dados da taxa nessas VMs.
   * **Número de contas do standard storage** fornece o número total de contas do standard storage necessário para proteger as VMs. Uma conta de armazenamento standard pode conter até 20000 IOPS em todas as VMs de armazenamento padrão e é suportado um máximo de 500 IOPS por disco.
   * **Número de discos de blob necessários** indica o número de discos que será criado no armazenamento do Azure.
   * **Número de contas de armazenamento premium necessário** fornece o número total de contas de armazenamento premium necessário para proteger as VMs. Uma origem de VM com o IOPS elevado (maior do que 20000) tem uma conta de armazenamento premium. Uma conta do premium storage pode conter até 80000 IOPS.
   * **Total de IOPS no armazenamento premium** é calculado com base no tamanho da unidade de IOPS 256 K em contas de armazenamento total premium.  Para o planeador rápido, o número é calculado com base em todos os discos de VMs de origem e a taxa de alteração de dados diárias. Para o planeador de detalhado, o número é calculado com base no número total de VMs que estão mapeadas para premium VM do Azure (série DS e GS) e as alterações dos dados taxa dessas VMs.
   * **Número de servidores de configuração necessários** mostra o número de servidores de configuração é necessário para a implementação.
   * **Número de servidores de processos adicionais necessários** mostra se os servidores de processos adicional são necessárias, além do servidor de processo está em execução no servidor de configuração por predefinição.
   * **armazenamento adicional de 100% na origem** mostra se armazenamento adicional é necessária a localização de origem.

   ![Saída](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o planeador de detalhado

1. Transfira e abra o [Planeador de capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel) ferramenta. Terá de executar macros, por isso, selecione para activar a edição e permitir que os conteúdos quando lhe for pedido.
2. No **selecione um tipo de planner**, selecione **Planeador de detalhado** da caixa de listagem.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started-2.png)
3. No **qualificação da carga de trabalho** folha de cálculo, introduza as informações necessárias. Terá de preencher os campos marcados.

   * No **núcleos do processador**, especifique o número total de núcleos num servidor de origem.
   * No **a atribuição de memória em MB**, especifique o tamanho da RAM de um servidor de origem.
   * O **número de NICs**, especifique o número de adaptadores de rede num servidor de origem.
   * No **Total de armazenamento (GB)**, especifique o tamanho total do armazenamento VM. Por exemplo, se o servidor de origem tiver 3 discos com 500 GB, tamanho de armazenamento total é 1500 GB.
   * No **número de discos anexados**, especifique o número total de discos de um servidor de origem.
   * No **utilização da capacidade do disco**, especifique a utilização média.
   * No **diariamente alterar taxa (%)**, especifique a alteração de dados diárias taxa de um servidor de origem.
   * No **tamanho mapeamento Azure**, introduza o tamanho da VM do Azure que pretende mapear. Se não quiser fazê-lo manualmente, clique em **computação VMs de IaaS**. Se uma definição manual de entrada e, em seguida, clique em VMs de IaaS de computação, a definição manual pode ser substituída porque o processo de computação identifica automaticamente a melhor correspondência no tamanho da VM do Azure.

   ![Qualificação da carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Se clicar em **computação VMs de IaaS** Eis o que faz:

   * Valida as entradas obrigatórias.
   * Calcula o IOPS e sugere VM do Azure melhor aize correspondência para cada VMs que é elegível para replicação no Azure. Se não é possível detetar um tamanho adequado de VM do Azure que é emitido um erro. Por exemplo, se o número de discos anexados no 65, é emitido um erro porque o tamanho maior VM do Azure é 64.
   * Sugere uma conta de armazenamento que pode ser utilizada para uma VM do Azure.
   * Calcula o número total de contas do standard storage e contas de armazenamento premium necessárias para a carga de trabalho. Desloque para baixo para ver o tipo de armazenamento do Azure e a conta de armazenamento que pode ser utilizada para um servidor de origem.
   * Realiza e ordena o resto da tabela com base no tipo de armazenamento necessária (standard ou premium) atribuído para uma VM e o número de discos anexados. Para todas as VMs que cumprem os requisitos do Azure, a coluna **VM é qualificado?** mostra **Sim**. Se uma VM não pode ser feita para o Azure, é apresentado um erro.

Colunas AA para AE são produzidas e fornecem informações para cada VM.

![Qualificação da carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Por exemplo, para seis VMs com os valores apresentados na tabela, a ferramenta calcula e atribui a melhor correspondência de VM do Azure e os requisitos de armazenamento do Azure.

![Qualificação da carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* No exemplo de saída, tenha em atenção o seguinte:

  * A primeira coluna é uma coluna de validação para as VMs, discos e volume de alterações.
  * Duas contas do standard storage e conta de armazenamento um premium são necessárias para as cinco VMs.
  * VM3 não se qualificar para a proteção, porque um ou mais discos são mais de 1 TB.
  * VM1 e VM2 podem utilizar a primeira conta de armazenamento standard
  * VM4 pode utilizar a segunda conta de armazenamento standard.
  * VM5 e VM6 precisam de uma conta de armazenamento premium, e ambos utilizar uma única conta.

    > [!NOTE]
    > IOPS no storage standard e premium são calculadas ao nível da VM e não ao nível do disco. Uma máquina virtual standard pode processar até 500 IOPS por disco. Se o IOPS para um disco for maiores do que 500, precisa de armazenamento premium. No entanto, se o IOPS para um disco estão mais do que 500, mas IOPS para os discos da VM totais estão dentro de limites suporte padrão de VM do Azure (tamanho da VM, número de discos, número de memória de adaptadores, CPU,), em seguida, o planeador escolhe um padrão série VM e não o DS ou GS. Terá de atualizar manualmente a célula de tamanho do Azure de mapeamento com adequado série DS ou GS de VM.


Depois de todos os detalhes no local, clique em **submeter dados à ferramenta planner** para abrir o **Capacity Planner** cargas de trabalho são realçadas, para mostrar se estiverem elegíveis para proteção ou não.

### <a name="submit-data-in-the-capacity-planner"></a>Submissão de dados no Planeador de capacidade
1. Ao abrir o **Capacity Planner** folha de cálculo é preenchido com base nas definições especificadas. A palavra 'Carga de trabalho' aparece no **origem de entradas de Infra** célula, para mostrar que a entrada é o **qualificação da carga de trabalho** folha de cálculo.
2. Se pretender efetuar alterações, tem de modificar o **qualificação da carga de trabalho** folha de cálculo e clique em **submeter dados à ferramenta planner** novamente.  

   ![Planeador de capacidade](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Passos seguintes

[Saiba como executar](site-recovery-capacity-planning-for-hyper-v-replication.md) a ferramenta Capacity Planner.
