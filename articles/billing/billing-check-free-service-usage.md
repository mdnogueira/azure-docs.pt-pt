---
title: "Monitorizar e controlar a utilização do Azure os serviços gratuitos - | Microsoft Docs"
description: "Saiba verificar a utilização dos serviços livres. Utilize csv de utilização e o portal do Azure."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 27ff6c92904a0b32cd4a37c8b1930adc121a7231
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Verifique a utilização dos serviços livres incluído com a sua conta gratuita do Azure 

Não lhe serem cobrados para os serviços incluídos gratuitamente com uma conta gratuita do Azure, a menos que excede os limites destes serviços. Para permanecerem com os limites, pode utilizar o portal do Azure ou o ficheiro de utilização para monitorizar e controlar a utilização dos serviços livres. 

## <a name="check-usage-on-the-azure-portal"></a>Utilização de verificação no portal do Azure

1.  Inicie sessão no [Portal do Azure]( http://portal.azure.com).

2.  Na parte inferior da navegação à esquerda, selecione **mais serviços**.

3.  Selecione **subscrições**.

4.  Selecione a subscrição que criou quando se inscreveu gratuitamente conta.

    ![Captura de ecrã que mostra todas as subscrições](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Secção Descrição geral de mostra-lhe informações essenciais sobre a sua subscrição, tais como o ID de subscrição, oferecem tipo e o nome da subscrição. Também pode encontrar informações sobre quando o crédito de conta gratuita seria expirar.

    ![Captura de ecrã que mostra as informações essenciais de subscrição](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Desloque para baixo para localizar informações no seus custos atuais e previstos. O custo inclui a utilização dos serviços não incluídas com a sua conta gratuita e a utilização excede o limite de serviços livres. 

    ![Captura de ecrã que mostra as informações de custo de subscrição](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  A parte final do secção Descrição geral de tem uma tabela na utilização dos serviços livres. 

    ![Captura de ecrã que mostra a utilização de serviços gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tabela contém as seguintes colunas:

* **Nome do medidor:** identifica a unidade de medida para medir a ser consumido. Para saber mais sobre o serviço para o mapeamento do medidor, consulte [compreender serviço gratuito para o mapeamento do medidor](billing-understand-free-service-meter-mapping.md). 
* **Utilização/limite:** utilização e limite para a medição do mês atual. Também pode encontrar estas informações na barra de estado.
* **Estado:** estado de utilização do medidor. Com base no seu padrão de utilização, pode ter um destes statutes.
  * **Não em utilização:** não tiver utilizado a medição ou a utilização de medição não atingiu o sistema de faturação.
  * **Foi excedido no \<data >:** excedeu o limite para a medição no \<data >.
  * **Pouco provável para Exceed:** é pouco provável que excedem o limite para a medição.
  * **Excede no \<data >:** que é provável que excedem o limite para a medição no \<data >.


## <a name="check-usage-through-the-usage-file"></a>Verifique a utilização através do ficheiro de utilização

O ficheiro de utilização fornece informações detalhadas para a sua subscrição do Azure. Pode transferir o mensal e diariamente ficheiros de utilização do Centro de contas do Azure. Para saber como transferir o ficheiro de utilização e compreender o acesso requerido, consulte [obter fatura e utilização](billing-download-azure-invoice-daily-usage-date.md). Para saber mais sobre as colunas no ficheiro de utilização, consulte [compreender os termos de licenciamento em conta a utilização](billing-understand-your-usage.md). 

O ficheiro de utilização contém informações de utilização para os serviços gratuitos e pagos. Medidores serviço gratuito teria **livres** no final do nome do medidor. Localizar medidores livres, abra o ficheiro no excel e filtrar o **coluna categoria de medição** de células que contêm texto **- livre** (utilize os filtros de texto &rarr; filtro Contains)&nbsp;

![Captura de ecrã que mostra a utilização de serviços gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
