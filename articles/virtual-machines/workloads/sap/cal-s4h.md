---
title: Implementar SAP S/4HANA ou BW/4HANA numa VM do Azure | Microsoft Docs
description: Implementar SAP S/4HANA ou BW/4HANA numa VM do Azure
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 342bd20466cdeb0b9df1cdacd4664fa4e3e0c604
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Implementar SAP S/4HANA ou BW/4HANA no Azure
Este artigo descreve como implementar o S/4HANA no Azure através da biblioteca de aplicação de nuvem de SAP (SAP CAL) 3.0. Para implementar outras soluções baseadas em SAP HANA, tais como BW/4HANA, siga os mesmos passos.

> [!NOTE]
Para obter mais informações sobre o CAL SAP, vá para o [biblioteca de aplicação de nuvem do SAP](https://cal.sap.com/) Web site. SAP também tem um blogue sobre o [SAP nuvem aplicação biblioteca 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
A partir de 29 de Maio de 2017, pode utilizar o modelo de implementação Azure Resource Manager para além do modelo de implementação clássica menos preferencial para implementar o CAL SAP. Recomendamos que utilize o novo modelo de implementação do Resource Manager e ignorar o modelo de implementação clássica.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implementar a solução

A sequência de capturas de ecrã seguinte mostra como implementar o S/4HANA no Azure utilizando o CAL SAP. O processo funciona da mesma forma para outras soluções, tais como BW/4HANA.

O **soluções** página mostra algumas das soluções baseado em CAL de SAP HANA disponíveis no Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated aplicação** é na linha da média:

![Soluções de CAL SAP](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta do CAL de SAP
1. Para iniciar sessão para o SAP CAL pela primeira vez, utilize o SAP-utilizador ou outro utilizador registado com o SAP. Em seguida, defina uma conta do SAP CAL que é utilizada pelo CAL SAP para implementar aplicações no Azure. Na definição da conta, tem de:

    a. Selecione o modelo de implementação no Azure (Gestor de recursos ou clássico).

    b. Introduza a sua subscrição do Azure. Uma conta do SAP CAL pode ser atribuída a apenas uma subscrição. Se precisar de mais do que uma subscrição, terá de criar outra conta do SAP CAL.

    c. Conceda a permissão de SAP CAL para implementar na sua subscrição do Azure.

    > [!NOTE]
    Os passos seguintes mostram como criar uma conta do SAP CAL para implementações do Resource Manager. Se já tiver uma conta do SAP CAL que está associada ao modelo de implementação clássica, *necessário* a seguir estes passos para criar uma nova conta do SAP CAL. A nova conta do SAP CAL tem de implementar o modelo Resource Manager.

2. Crie uma nova conta do SAP CAL. O **contas** página mostra três opções para o Azure: 

    a. **Microsoft Azure (clássica)** é o modelo de implementação clássica e já não é preferencial.

    b. **Microsoft Azure** é o novo modelo de implementação do Resource Manager.

    c. **O Microsoft Azure operado pela 21Vianet** é uma opção na China que utiliza o modelo de implementação clássica.

    Para implementar o modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Detalhes de conta do CAL SAP](./media/cal-s4h/s4h-pic-2a.png)

3. Introduza o Azure **ID de subscrição** que pode ser encontrado no portal do Azure.

   ![Contas de CAL SAP](./media/cal-s4h/s4h-pic3c.png)

4. Para autorizar o SAP CAL para implementar numa subscrição do Azure que definiu, clique em **autorizar**. É apresentada a página seguinte no separador do browser:

   ![Início de sessão dos serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

5. Se mais do que um utilizador estiver listado, selecione a conta do Microsoft que está ligada a ser o coadministrador da subscrição do Azure que selecionou. É apresentada a página seguinte no separador do browser:

   ![Confirmação de serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

6. Clique em **aceitar**. Se a autorização for bem sucedida, a definição de conta do SAP CAL apresenta novamente. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi concluída com êxito.

7. Para atribuir a conta do SAP CAL recentemente criada para o utilizador, introduza o **ID de utilizador** na caixa de texto no lado direito e clique em **adicionar**.

   ![A associação do utilizador da conta](./media/cal-s4h/s4h-pic8a.png)

8. Para associar a conta de utilizador que utilizar para iniciar sessão para o CAL SAP, clique em **revisão**. 
 
9. Para criar a associação entre o utilizador e a conta do SAP CAL recentemente criada, clique em **criar**.

   ![Utilizador para a associação de conta do SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Criou com êxito uma conta do SAP CAL que é capaz de:

- Utilize o modelo de implementação Resource Manager.
- Implemente sistemas SAP na sua subscrição do Azure.

Agora pode começar a implementar S/4HANA na sua subscrição de utilizador no Azure.

> [!NOTE]
Antes de continuar, determine se tem de quotas vCPU do Azure para as VMs do Azure série H. De momento, o SAP CAL utiliza H série VMs do Azure para implementar algumas das soluções do SAP HANA. A subscrição do Azure poderá não ter quaisquer quotas de vCPU H série para série H. Se assim for, poderá ter de contactar o suporte do Azure para obter uma quota de pelo menos 16 vCPUs de série de H.

> [!NOTE]
Ao implementar uma solução no Azure no CAL SAP, poderá achar que pode escolher apenas uma região do Azure. Para implementar em regiões do Azure diferente daquela sugerida pelo CAL SAP, tem de comprar uma subscrição de CAL SAP. Também poderá ter de abrir uma mensagem com o SAP a sua conta do CAL ativada para a entrega em regiões do Azure sem ser aqueles inicialmente sugeridos.

### <a name="deploy-a-solution"></a>Implementar uma solução

Eis como implementar uma solução do **soluções** página do CAL SAP. O SAP CAL tem duas sequências para implementar:

- Uma sequência básica que utiliza uma página para definir o implementação do sistema
- Uma sequência avançada que dá-lhe algumas opções em tamanhos de VM 

Iremos demonstrar o caminho básico para implementação aqui.

1. No **detalhes da conta** página, tem de:

    a. Selecione uma conta do SAP CAL. (Utilizar uma conta que está associada a implementar com o modelo de implementação Resource Manager).

    b. Introduza uma instância **nome**.

    c. Selecione um Azure **região**. O SAP CAL sugere uma região. Se precisar de outra região do Azure e não tiver uma subscrição de SAP CAL, terá de uma subscrição de CAL com SAP de ordem.

    d. Introduza um mestre **palavra-passe** para a solução de nove ou oito carateres. A palavra-passe é utilizada para os administradores dos componentes diferentes.

   ![Modo de CAL Basic SAP: Criar uma instância](./media/cal-s4h/s4h-pic10a.png)

2. Clique em **criar**e na caixa de mensagem que é apresentado, clique em **OK**.

   ![CAL de SAP suportado tamanhos de VM](./media/cal-s4h/s4h-pic10b.png)

3. No **chave privada** caixa de diálogo, clique em **armazenar** para armazenar a chave privada no CAL SAP. Para utilizar a proteção de palavra-passe para a chave privada, clique em **transferir**. 

   ![Chave privada de CAL SAP](./media/cal-s4h/s4h-pic10c.png)

4. Leia o SAP CAL **aviso** de mensagens e clique em **OK**.

   ![Aviso de CAL SAP](./media/cal-s4h/s4h-pic10d.png)

    Agora a implementação ocorre. Após algum tempo, dependendo do tamanho e complexidade da solução (CAL o SAP fornece uma estimativa), o estado é mostrado como Active Directory e pronto a utilizar.

5. Para localizar as máquinas virtuais recolhidas com os outros recursos associados num grupo de recursos, aceda ao portal do Azure: 

   ![Objetos de SAP CAL implementados no portal do novo](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. No portal do SAP CAL, o estado é apresentado como **Active Directory**. Para ligar à solução, clique em **Connect**. Opções diferentes para estabelecer ligação com os diferentes componentes são implementadas dentro desta solução.

   ![Instâncias de CAL SAP](./media/cal-s4h/active_solution.png)

7. Antes de poder utilizar uma das opções para ligar para os sistemas implementados, clique em **guia de introdução**. 

   ![Ligue à instância do](./media/cal-s4h/connect_to_solution.png)

    A documentação dos nomes de utilizadores para cada um dos métodos de conectividade. As palavras-passe para os utilizadores são definidas para a palavra-passe principal definido no início do processo de implementação. Na documentação do outros mais funcionais utilizadores são listados com as palavras-passe, que pode utilizar para iniciar sessão para o sistema implementado. 

    Por exemplo, se utilizar o GUI de SAP está pré-instalado na máquina de ambiente de trabalho remoto do Windows, o sistema S/4 poderá ter o seguinte aspeto:

   ![SM50 na GUI pré-instalada do SAP](./media/cal-s4h/gui_sm50.png)

    Ou, se utilizar o DBACockpit, a instância pode ter o seguinte aspeto:

   ![SM50 na DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Dentro de algumas horas, um dispositivo de SAP S/4 bom estado de funcionamento é implementado no Azure.

Se comprou a uma subscrição de SAP CAL, SAP suporta totalmente implementações através de CAL SAP no Azure. A fila de suporte é BC-VCM-CAL.







