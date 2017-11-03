---
title: Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure | Microsoft Docs
description: Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure
Este artigo descreve como implementar um sistema SAP IDES com SQL Server e o sistema operativo Windows no Azure através da biblioteca de aplicação de nuvem de SAP (SAP CAL) 3.0. As capturas de ecrã mostram o processo de passo a passo. Para implementar uma solução diferente, siga os mesmos passos.

Para começar a utilizar o CAL SAP, vá para o [biblioteca de aplicação de nuvem do SAP](https://cal.sap.com/) Web site. SAP também tem um blogue sobre as novas [SAP nuvem aplicação biblioteca 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
A partir de 29 de Maio de 2017, pode utilizar o modelo de implementação Azure Resource Manager para além do modelo de implementação clássica menos preferencial para implementar o CAL SAP. Recomendamos que utilize o novo modelo de implementação do Resource Manager e ignorar o modelo de implementação clássica.

Se já tiver criado uma conta do SAP CAL que utiliza o modelo clássico, *tem de criar outra conta do SAP CAL*. Esta conta tem de implementar exclusivamente no Azure utilizando o modelo do Resource Manager.

Depois de se inscrever CAL o SAP, primeira página normalmente orienta-o para o **soluções** página. As soluções disponibilizadas no CAL SAP estão aumentar firmemente, pelo que poderá ter de deslocar bastante um pouco para localizar a solução que pretende. O realçado solução IDES de SAP baseados em Windows que está disponível no modo exclusivo no Azure demonstra o processo de implementação:

![Soluções de CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta do CAL de SAP
1. Para iniciar sessão para o SAP CAL pela primeira vez, utilize o SAP-utilizador ou outro utilizador registado com o SAP. Em seguida, defina uma conta do SAP CAL que é utilizada pelo CAL SAP para implementar aplicações no Azure. Na definição da conta, tem de:

    a. Selecione o modelo de implementação no Azure (Gestor de recursos ou clássico).

    b. Introduza a sua subscrição do Azure. Uma conta do SAP CAL pode ser atribuída a apenas uma subscrição. Se precisar de mais do que uma subscrição, terá de criar outra conta do SAP CAL.
    
    c. Conceda a permissão de SAP CAL para implementar na sua subscrição do Azure.

    > [!NOTE]
    Os passos seguintes mostram como criar uma conta do SAP CAL para implementações do Resource Manager. Se já tiver uma conta do SAP CAL que está associada ao modelo de implementação clássica, *necessário* a seguir estes passos para criar uma nova conta do SAP CAL. A nova conta do SAP CAL tem de implementar o modelo Resource Manager.

2. Para criar uma nova conta do SAP CAL, o **contas** página mostra duas opções para o Azure: 

    a. **Microsoft Azure (clássica)** é o modelo de implementação clássica e já não é preferencial.

    b. **Microsoft Azure** é o novo modelo de implementação do Resource Manager.

    ![Contas de CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implementar o modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Contas de CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Introduza o Azure **ID de subscrição** que pode ser encontrado no portal do Azure. 

    ![ID de subscrição de CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. Para autorizar o SAP CAL para implementar numa subscrição do Azure que definiu, clique em **autorizar**. É apresentada a página seguinte no separador do browser:

    ![Início de sessão dos serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Se mais do que um utilizador estiver listado, selecione a conta do Microsoft que está ligada a ser o coadministrador da subscrição do Azure que selecionou. É apresentada a página seguinte no separador do browser:

    ![Confirmação de serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Clique em **aceitar**. Se a autorização for bem sucedida, a definição de conta do SAP CAL apresenta novamente. Após um curto período de tempo, uma mensagem confirma que o processo de autorização foi concluída com êxito.

7. Para atribuir a conta do SAP CAL recentemente criada para o utilizador, introduza o **ID de utilizador** na caixa de texto no lado direito e clique em **adicionar**. 

    ![A associação do utilizador da conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Para associar a conta de utilizador que utilizar para iniciar sessão para o CAL SAP, clique em **revisão**. 

9. Para criar a associação entre o utilizador e a conta do SAP CAL recentemente criada, clique em **criar**.

    ![Utilizador para a associação de conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Criou com êxito uma conta do SAP CAL que é capaz de:

- Utilize o modelo de implementação Resource Manager.
- Implemente sistemas SAP na sua subscrição do Azure.

> [!NOTE]
Antes de poder implementar a solução SAP IDES com base no Windows e do SQL Server, poderá ter de inscrever-se para uma subscrição de SAP CAL. Caso contrário, a solução pode aparecer como **está bloqueado** na página de descrição geral.

### <a name="deploy-a-solution"></a>Implementar uma solução
1. Depois de configurar uma conta do SAP CAL, selecione **solução IDES o SAP no Windows e do SQL Server** solução. Clique em **criar instância**e confirme as condições de utilização e os termos. 

2. No **modo básico: Criar instância** página, tem de:

    a. Introduza uma instância **nome**.

    b. Selecione um Azure **região**. Poderá ter uma subscrição de SAP CAL obter várias regiões do Azure, oferecidas.

    c.  Introduza o mestre de **palavra-passe** para a solução, conforme mostrado:

    ![Modo de CAL Basic SAP: Criar uma instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Clique em **Criar**. Após algum tempo, dependendo do tamanho e complexidade da solução (CAL o SAP fornece uma estimativa), o estado é mostrado como Active Directory e pronto a utilizar: 

    ![Instâncias de CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Para encontrar o grupo de recursos e todos os respetivos objetos que foram criados por CAL o SAP, aceda ao portal do Azure. Pode localizar a máquina virtual, começando com o mesmo nome de instância que foi introduzido no CAL SAP.

    ![Objetos de grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. No portal do SAP CAL, vá para as instâncias implementadas e clique em **Connect**. É apresentada a janela de pop-up seguinte: 

    ![Ligue à instância do](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Antes de poder utilizar uma das opções para ligar para os sistemas implementados, clique em **guia de introdução**. A documentação dos nomes de utilizadores para cada um dos métodos de conectividade. As palavras-passe para os utilizadores são definidas para a palavra-passe principal definido no início do processo de implementação. Na documentação do outros mais funcionais utilizadores são listados com as palavras-passe, que pode utilizar para iniciar sessão para o sistema implementado.

    ![Documentação de boas-vindas do SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Dentro de algumas horas, um sistema SAP IDES bom estado de funcionamento é implementado no Azure.

Se comprou a uma subscrição de SAP CAL, SAP suporta totalmente implementações através de CAL SAP no Azure. A fila de suporte é BC-VCM-CAL.

