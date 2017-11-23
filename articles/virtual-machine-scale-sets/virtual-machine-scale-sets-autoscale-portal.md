---
title: "Conjuntos de dimensionamento de máquina virtual de dimensionamento automático no portal do Azure | Microsoft Docs"
description: "Define como criar regras de dimensionamento automático para dimensionamento da máquina virtual no portal do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 3714a4feb14bc47132e501629fc339bc7d0e40a1
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Dimensionar automaticamente um conjunto no portal do Azure de dimensionamento de máquina virtual
Quando cria um conjunto de dimensionamento, é possível definir o número de instâncias VM que pretende executar. Como muda o seu pedido de aplicação, pode automaticamente aumentar ou reduzir o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a pedido do cliente ou responder a alterações de desempenho da aplicação durante todo o ciclo de vida da sua aplicação.

Este artigo mostra como criar regras de dimensionamento automático no portal do Azure que monitorizar o desempenho das instâncias da VM no seu conjunto de dimensionamento. Estas regras de dimensionamento automático aumentam ou reduzir o número de instâncias de VM em resposta a estas métricas de desempenho. Também pode executar estes passos com [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de dimensionamento automático, terá de uma máquina virtual existente conjunto de dimensionamento. Pode criar uma escala definida com o [portal do Azure](virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell), ou [Azure CLI 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli).


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para ampliar automaticamente
Se aumentar o seu pedido de aplicação, a carga sobre as instâncias de VM no seu dimensionamento definir aumenta. Se este aumento de carga é consistente, em vez de apenas um breve pedido, pode configurar regras de dimensionamento automático para aumentar o número de instâncias VM no conjunto de dimensionamento. Quando estas instâncias VM são criadas e as suas aplicações são implementadas, inicia o conjunto de dimensionamento distribuir o tráfego aos mesmos através do Balanceador de carga. Controlar as métricas para monitorizar, tais como CPU ou disco, quanto a carga da aplicação têm de cumprir um determinado limiar, e quantas instâncias VM para adicionar à escala definido.

1. Abra o Azure portal e selecione **grupos de recursos** no menu no lado esquerdo do dashboard.
2. Selecione o grupo de recursos que contém o conjunto de dimensionamento, em seguida, escolha o conjunto de dimensionamento da lista de recursos.
3. Escolha **dimensionamento** no menu no lado esquerdo da escala definir janela. Selecione o botão para **ativar o dimensionamento automático**:

    ![Ativar o dimensionamento automático no portal do Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Introduza um nome para as suas definições, tais como *dimensionamento automático*, em seguida, selecione a opção de **adicionar uma regra**.

5. Vamos criar uma regra que aumenta o número de instâncias VM na escala definida quando a carga média da CPU é superior a 70% durante um período de 10 minutos. Quando a regra é acionado, o número de instâncias VM é aumentado por 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, pode configurar o **operação** para *aumentar a contagem por* e, em seguida, especifique *1* ou *2* para o *instância contagem*. Conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 20% ou de 10% instâncias de VM podem ser mais adequadas.

    Especifique as seguintes definições para a regra:
    
    | Parâmetro              | Explicação                                                                                                         | Valor          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregação de tempo*     | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
    | *Nome da métrica*          | A métrica de desempenho para monitorizar e aplicar escala definir ações.                                                   | Percentagem da CPU |
    | *Estatística de grão de tempo* | Define a forma como as métricas recolhidas em cada intervalo de tempo devem ser agregadas para análise.                             | Média        |
    | *Operador*             | Operador utilizado para comparar os dados métricos contra o limiar.                                                     | Mais do que   |
    | *Limiar*            | A percentagem que faz com que a regra de dimensionamento automático acionar uma ação.                                                 | 70             |
    | *Duração*             | A quantidade de tempo monitorizado antes em comparação com os valores da métrica e de limiar.                                   | 10 minutos     |
    | *Operação*            | Define se o conjunto de dimensionamento deve Dimensionar cópias de segurança ou para baixo quando se aplica a regra e que incremento                        | Aumentar percentagem em |
    | *Contagem de instâncias*       | A percentagem de instâncias de VM deve ser alterada quando a regra é acionado.                                            | 20             |
    | *Esporádico para baixo (minutos)*  | A quantidade de tempo de espera antes da regra é aplicada novamente para que as ações de dimensionamento automático de ter a hora entre em vigor. | 5 minutos      |

    Os exemplos seguintes mostram uma regra que criar no portal do Azure que corresponda a estas definições:    

    ![Criar uma regra de dimensionamento automático para aumentar o número de instâncias de VM](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Para criar a regra, selecione **adicionar**


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para dimensionar automaticamente de
Num evening ou fim de semana, pode diminuir o seu pedido de aplicação. Se este carregamento menor consistente durante um período de tempo, pode configurar regras de dimensionamento automático para reduzir o número de instâncias VM no conjunto de dimensionamento. Esta ação de dimensionamento reduz o custo para executar a escala definida como executar apenas o número de instâncias necessário para satisfazer a procura atual.

1. Optar por **adicionar uma regra** novamente.
2. Crie uma regra que reduz o número de instâncias VM na escala definido quando a carga de CPU média, em seguida, descerem abaixo de 30% durante um período de 10 minutos. Quando a regra é acionado, o número de instâncias VM é diminuído por 20%.

    Utilize a mesma abordagem tal como acontece com a regra anterior. Ajuste as seguintes definições para a regra:
    
    | Parâmetro              | Explicação                                                                                                          | Valor          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operador*             | Operador utilizado para comparar os dados métricos contra o limiar.                                                      | Menos do que   |
    | *Limiar*            | A percentagem que faz com que a regra de dimensionamento automático acionar uma ação.                                                 | 30             |
    | *Operação*            | Define se o conjunto de dimensionamento deve Dimensionar cópias de segurança ou para baixo quando se aplica a regra e que incremento                         | Diminuir percentagem em |
    | *Contagem de instâncias*       | A percentagem de instâncias de VM deve ser alterada quando a regra é acionado.                                             | 20             |

3. Para criar a regra, selecione **adicionar**


## <a name="define-autoscale-instance-limits"></a>Definir limites de instância de dimensionamento automático
O perfil de dimensionamento automático tem de definir um número predefinido de instâncias de VM, máximo e mínimo. Quando são aplicadas as regras de dimensionamento automático, estes limites de instância Certifique-se de que pode não aumentar horizontalmente para além do número máximo de instâncias ou escala em além o mínimo de instâncias.

1. Defina os limites de instância seguintes:

    | Mínimo | Máximo | Predefinição|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Para aplicar as suas regras de dimensionamento automático e limites de instância, selecione **guardar**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorizar o número de instâncias de um conjunto de dimensionamento
Para ver o número e o estado de instâncias VM, selecione **instâncias** no menu no lado esquerdo da escala definir janela. O estado indica se a instância VM está *criar* dado que a escala definida automaticamente aumenta horizontalmente de forma ou é *eliminação* como escala reduz horizontalmente automaticamente.

![Ver uma lista de instâncias de VM de conjunto de dimensionamento](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base numa agenda
Os exemplos anteriores ampliada automaticamente uma escala definida no ou terminar com a métrica de anfitrião básica, tais como a utilização da CPU. Também pode criar regras de dimensionamento automático com base no agendas. Estas regras baseadas na agenda permitem-lhe automaticamente aumentar horizontalmente o número de instâncias VM à frente de um aumento previsto no pedido de aplicação, tais como as horas de núcleo de trabalho e, em seguida, dimensionar automaticamente o número de instâncias de cada vez que antecipa inferior pedido, tal como o fim de semana.

1. Escolha **dimensionamento** no menu no lado esquerdo da escala definir janela. Para eliminar as regras de dimensionamento automático existentes criadas nos exemplos anteriores, escolha o lixo pode ícone.

    ![Elimine as regras de dimensionamento automático existente](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Optar por **adicionar uma condição de escala**. Selecione o ícone de lápis junto ao nome da regra e forneça um nome como *aumentar horizontalmente durante cada dia de trabalho*.

    ![Mudar o nome da regra de dimensionamento automático predefinida](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecione o botão de opção para **escala a uma contagem de instâncias específicas**.
4. Para aumentar verticalmente o número de instâncias, introduza *10* como a contagem de instâncias.
5. Escolha **repetir dias específicos** para o **agenda** tipo.
6. Selecione todos os trabalho dias, segunda através de sexta-feira.
7. Escolha o fuso horário adequado, em seguida, especifique um **hora de início** de *09:00*.
8. Optar por **adicionar uma condição de escala** novamente. Repetir o processo para criar uma agenda chamada *dimensionar de durante o evening* que dimensiona *3* instâncias, repete-se cada dia da semana e inicia no *18:00*.
9. Para aplicar as regras de dimensionamento automático baseadas na agenda, selecione **guardar**.

    ![Criar regras de dimensionamento automático que dimensionadas com base numa agenda](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Para ver como são aplicadas as regras de dimensionamento automático, selecione **histórico de execução** na parte superior do **dimensionamento** janela. O gráfico e os eventos listam mostra ao acionam as regras de dimensionamento automático e o número de instâncias VM no seu dimensionamento aumenta ou diminui.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar regras de dimensionamento automático para aumentar horizontalmente e aumentar ou diminuir o *número* de instâncias VM no seu dimensionamento definido. Também pode reduzir verticalmente para aumentar ou diminuir a instância VM *tamanho*. Para obter mais informações, consulte [Vertical dimensionamento automático com conjuntos de dimensionamento da Máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as instâncias de VM, consulte [conjuntos de dimensionamento de máquina virtual de gerir com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando o dimensionamento automático regras acionador, consulte [utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Também pode [registos de auditoria de Utilize para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
