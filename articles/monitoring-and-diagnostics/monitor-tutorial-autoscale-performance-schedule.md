---
title: "Recursos do Azure de dimensionamento automático com base nos dados de desempenho ou numa agenda | Microsoft Docs"
description: "Criar uma definição de dimensionamento automático para um plano do app service utilizando dados métricos e uma agenda"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 3a85e288fa6f7d6c7138b7fea8319bd8dee01c2c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Criar uma definição de dimensionamento automático para recursos do Azure com base nos dados de desempenho ou numa agenda

Definições de dimensionamento automático permitem-lhe adicionar/remover instâncias de serviço com base nas condições predefinidas. Estas definições podem ser criadas através do portal. Este método fornece uma interface de utilizador baseada no browser para criar e configurar uma definição de dimensionamento automático. 

Neste tutorial, irá 
> [!div class="checklist"]
> * Criar uma aplicação Web e o plano do App Service
> * Configurar regras para a escala de entrada e de escalamento horizontal com base no número de pedidos a receber de uma aplicação Web de dimensionamento automático
> * Acionar uma ação de escalamento horizontal e ver o número de instâncias aumentar
> * Acionar uma ação de dimensionamento e ver o número de instâncias diminuir
> * Apague os seus recursos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Criar uma aplicação Web e o plano do App Service
Clique em de **novo** opção no painel de navegação esquerdo

Procure e selecione o *aplicação Web* item e clique em **criar**

Selecione um nome de aplicação como *MyTestScaleWebApp*. Criar um novo grupo de recursos * myResourceGroup' e coloque-o para o grupo de recursos da sua escolha.

Dentro de alguns minutos, os recursos deverão ser aprovisionados. Utilizar a aplicação Web e correspondente plano do App Service no resto deste tutorial.

    ![Create a new app service in the portal](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navegue para definições de dimensionamento automático
1. A partir do painel de navegação esquerdo, selecione o **Monitor** opção. Depois da página for carregada selecione o **dimensionamento automático** separador.
2. Uma lista de recursos na sua subscrição que suporta o dimensionamento automático estão listados aqui. Identificar o plano do App Service que foi criado anteriormente no tutorial e clique no mesmo.

    ![Navegue para definições de dimensionamento automático](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Na definição de dimensionamento automático, clique em de **ativar o dimensionamento automático** botão

A seguinte ajuda de passos alguns preenchidos o ecrã de dimensionamento automático para aspeto a imagem seguinte:

   ![Guardar a definição de dimensionamento automático](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Configurar o perfil predefinido
1. Forneça um **nome** para a definição de dimensionamento automático
2. No perfil predefinido, certifique-se a **modo de dimensionamento** está definido como 'Escala a uma contagem de instâncias específicas'
3. Definir a contagem de instâncias **1**. Esta definição garante que, quando nenhum outro perfil estiver ativo ou em vigor, o perfil predefinido devolve a contagem de instâncias para 1.

  ![Navegue para definições de dimensionamento automático](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrance-profile"></a>Criar perfil de recurrance

1. Clique em de **adicionar uma condição de escala** ligação sob o perfil predefinido

2. Editar o **nome** deste perfil para ser 'Perfil de segunda a sexta '

3. Certifique-se a **modo de dimensionamento** está definido como 'Escala com base numa métrica'

4. Para **instância limites** definido o **mínimo** como '1', o **máximo** como '2' e o **predefinido** como '1'. Esta definição garantirá que este perfil a não dimensionamento automático da serviço planeia ter menos do que 1 instância ou mais de 2 instâncias. Se o perfil não tem dados suficientes para tomar uma decisão, utiliza o número predefinido de instâncias (em 1 neste caso).

5. Para **agenda**, selecione 'dias específicos Repeat'

6. Defina o perfil de repetir a segunda através de sexta-feira, de 09:00 PST para 18:00 PST. Esta definição garantirá que este perfil é apenas aplicável e Active Directory 09: 00 para as 18: 00, segunda através de sexta-feira. Todos os outros fora das horas, o 'Default' é o perfil utiliza o definição de dimensionamento automático.

## <a name="create-a-scale-out-rule"></a>Criar uma regra de escalamento horizontal

1. Na segunda ao perfil sexta-feira

2. Clique em de **adicionar uma regra** ligação

3. Definir o **origem métrica** ser 'outros recursos'. Definir o **tipo de recurso** como serviços de aplicações e o **recursos** enquanto a aplicação Web criada anteriormente no tutorial.

4. Definir o **tempo agregação** como 'Totais', o **o nome da métrica** como 'Pedidos' e o **estatística de grão de tempo** como 'Soma'

5. Definir o **operador** "Maior do que", como o **limiar** como '10' e o **duração** como '5' minutos.

6. Selecione o **operação** como 'Aumento contagem por', o **instância contagem** como '1' e o **útil para baixo** como '5' minutos

7. Clique em de **adicionar** botão

Esta regra assegura que o se a sua aplicação Web recebe mais de 10 pedidos dentro de 5 minutos ou menos, uma instância adicional é adicionada ao seu plano do App Service para gerir a carga.

   ![Criar uma regra de escalamento horizontal](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Criar uma regra de escala
Recomendamos-lhe sempre ter uma regra de dimensionamento para acompanham uma regra de escalamento horizontal. Que assegura que os recursos não são mais aprovisionados. Relativamente ao aprovisionamento significa que tem mais instâncias em execução que necessária para processar a carga atual. 

1. Na segunda ao perfil sexta-feira

2. Clique em de **adicionar uma regra** ligação

3. Definir o **origem métrica** ser 'outros recursos'. Definir o **tipo de recurso** como serviços de aplicações e o **recursos** enquanto a aplicação Web criada anteriormente no tutorial.

4. Definir o **tempo agregação** como 'Totais', o **o nome da métrica** como 'Pedidos' e o **estatística de grão de tempo** como 'Média'

5. Definir o **operador** como 'Inferior a', o **limiar** como '5' e o **duração** como '5' minutos.

6. Selecione o **operação** como 'Diminuir contagem por', o **instância contagem** como '1' e o **útil para baixo** como '5' minutos

7. Clique em de **adicionar** botão

    ![Criar uma regra de escala](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Guardar** a definição de dimensionamento automático

    ![Guardar a definição de dimensionamento automático](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Ação de escalamento horizontal do acionador
Para acionar a condição de ampliação na definição de dimensionamento automático acabou de criar, a aplicação Web tem de ter mais de 10 pedidos em menos de 5 minutos.

1. Abra uma janela do browser e navegue para a aplicação Web que criou anteriormente neste tutorial. Pode encontrar o URL para a sua aplicação Web no Portal do Azure, ao navegar para o recurso de aplicação Web e clicando no **procurar** botão no separador de 'Descrição Geral'.

2. Sucessivamente rápida recarregar a página mais de 10 vezes

3. A partir do painel de navegação esquerdo, selecione o **Monitor** opção. Depois da página for carregada selecione o **dimensionamento automático** separador.

4. Na lista, selecione o plano do App Service utilizados neste tutorial

5. Na definição de dimensionamento automático, clique em de **histórico de execução** separador

6. Verá um gráfico ao refletir a contagem de instâncias do plano do App Service ao longo do tempo

7. Dentro de alguns minutos, a contagem de instâncias deve aumentar a partir de 1 para 2.

8. Sob o gráfico, consulte as entradas de registo de atividade para cada ação de dimensionamento executadas por esta definição de dimensionamento automático.

## <a name="trigger-scale-in-action"></a>Ação de dimensionamento de Acionador
A condição de escala na definir acionadores se existirem menos de 5 pedidos para a aplicação Web durante um período de 10 minutos de dimensionamento automático. 

1. Certifique-se de que não existem pedidos estão a ser enviados para a sua aplicação Web

2. Carregar o Portal do Azure

3. A partir do painel de navegação esquerdo, selecione o **Monitor** opção. Depois da página for carregada selecione o **dimensionamento automático** separador.

4. Na lista, selecione o plano do App Service utilizados neste tutorial

5. Na definição de dimensionamento automático, clique em de **histórico de execução** separador

6. Verá um gráfico ao refletir a contagem de instâncias do plano do App Service ao longo do tempo.

7. Dentro de alguns minutos, a contagem de instâncias deve remover o 2, como 1. O processo demora menos de 100 minutos.  

8. Sob o gráfico, é o conjunto de atividade correspondente entradas de registo para cada ação de dimensionamento executadas por esta definição de dimensionamento automático

    ![Ações de dimensionamento de vista](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Limpar recursos

1. No menu da esquerda no portal do Azure, clique em **todos os recursos** e, em seguida, selecione a aplicação Web criada neste tutorial.

2. Na sua página de recursos, clique em **eliminar**, confirmar a eliminação escrevendo **Sim** na caixa de texto e, em seguida, clique em **eliminar**.

3. Em seguida, selecione o recurso do plano do App Service e clique em **eliminar**

4. Confirmar eliminação escrevendo **Sim** na caixa de texto e, em seguida, clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, é  
> [!div class="checklist"]
> * Criar uma aplicação Web e o plano do App Service
> * Regras de dimensionamento automático configurado para dimensionamento e ampliação com base no número de pedidos da aplicação Web recebidos
> * Acionada uma ação de escalamento horizontal e observada o número de instâncias aumentar
> * Acionada uma ação de dimensionamento e observada o número de instâncias diminuir
> * Limpar os recursos


Para saber mais sobre as definições de dimensionamento automático, avançar para o [descrição geral de dimensionamento automático](monitoring-overview-autoscale.md).

> [!div class="nextstepaction"]
> [Arquivar dados de monitorização](monitor-tutorial-archive-monitoring-data.md)
