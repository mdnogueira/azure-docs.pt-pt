---
title: "Obter informações a partir de dados do Centro de Segurança do Azure com Power BI| Microsoft Docs"
description: "O pacote de conteúdo do Power BI do Centro de Segurança do Azure faz com que seja mais fácil encontrar alertas de segurança, recomendações, recursos atacados e tendências, com base num conjunto de dados que tenha sido criado para o seu relatório."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 0ded6bc7-52e8-43b4-8940-0bee137526e3
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: yurid
ms.openlocfilehash: 10f7b8f20cc41a5ebb1b1376e2bf17be02600ae4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Obter informações a partir de dados do Centro de Segurança do Azure com Power BI
O [Dashboard do Power BI](http://aka.ms/azure-security-center-power-bi) para o Centro de Segurança do Azure permite-lhe visualizar, analisar e filtrar recomendações e alertas de segurança a partir de qualquer lugar, incluindo o seu dispositivo móvel. Utilize o dashboard do Power BI para revelar as tendências e padrões de ataques – veja alertas de segurança por recurso ou endereço IP de origem e riscos de segurança não abordados por recurso ou idade.

Também pode misturar recomendações e alertas de segurança do Centro de Segurança com outros dados de formas interessantes, por exemplo, utilizando dados dos [Registos de Auditoria do Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) e da [Auditoria da Base de Dados SQL do Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Ambos oferecem Dashboards do Power BI e também pode exportar estes dados para o Excel para elaborar facilmente relatórios sobre o estado de segurança dos seus recursos de nuvem.

## <a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Utilizar o dashboard do Centro de Segurança do Azure para aceder ao Power BI
Também pode utilizar o dashboard do Centro de Segurança do Azure para aceder aos relatórios do Power BI. Siga os passos para realizar esta tarefa:

1. No dashboard do **Centro de Segurança do Azure**, clique no botão **Power BI**.

    ![Ligar-se ao Centro de Segurança do Azure através do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-1-newUI-2017.png)
2. O painel **Power BI** abre-se no lado direito, conforme mostrado no ecrã seguinte:

    ![Ligar-se ao Centro de Segurança do Azure através do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new11-2017.png)
3. Se estiver a criar o dashboard do Power BI pela primeira vez, pode escolher uma das opções seguintes no painel **Explorar no Power BI**:

   * **Dashboard de informações de segurança**: escolha esta opção se pretende criar um dashboard que inclua o estado de segurança, threads e deteções. Esta opção é mais comum para a função DevOps que é responsável por analisar o respetivo estado de proteção e os alertas detetados nas subscrições.
   * **Dashboard de gestão de políticas**: escolha esta opção se pretende explorar a política de gestão e de imposição.  Esta opção é mais comum para os departamentos de TI centrais mais focados no setor governativo. Podem utilizar este dashboard para obter visibilidade e conhecimentos aprofundados acerca do cumprimento das políticas de segurança na respetiva organização.
   * Se já tiver um dashboard do Power BI, clique em **Aceder ao seu dashboard do Power BI atual**.
4. Para efeitos deste exemplo, clique na opção **Dashboard de conhecimentos de segurança aprofundados**. Se for a primeira vez que cria um dashboard do Power BI para o Centro de Segurança, ser-lhe-á solicitado que instale o pacote de conteúdos. Clique no botão **Obter** na janela **Pacotes de conteúdo do Power BI**, conforme mostrado no ecrã seguinte:

    ![Dashboard de Conhecimentos de Segurança Aprofundados do Centro de Segurança do Azure](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)
5. É apresentada a janela **Ligar aos Conhecimentos de Segurança Aprofundados do Centro de Segurança do Azure**. Certifique-se de que o método de **Autenticação** é **oAuth2**, conforme mostrado abaixo, e clique no botão **Iniciar sessão**.

    ![Autenticação](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)
6. Poderá ser-lhe pedido para se autenticar novamente com as suas credenciais do Azure. O seu dashboard será criado assim que se autenticar. Assim que o dashboard for criado, é apresentado um relatório com uma estrutura semelhante à que é mostrada no ecrã seguinte:

    ![Dashboard do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)

> [!NOTE]
> Uma atualização do relatório está agendada para ser efetuada diariamente. Se ocorrer uma falha nesta atualização, leia [Potential Refresh Issues with the Azure Security Center Power BI (Potenciais Problemas de Atualização com o Power BI do Centro de Segurança do Azure)](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) para obter mais informações sobre como resolver problemas.
>
>

Aqui pode ver o número de alertas de segurança e as recomendações, bem como o número de VMs, bases de dados SQL do Azure e recursos de rede monitorizados pelo Centro de Segurança do Azure.

Uma ligação para o Centro de Segurança do Azure redireciona-o para o Portal do Azure. Os gráficos fazem como que seja mais fácil visualizar informações sobre as recomendações e os alertas de segurança, incluindo:

* Estado de Segurança do Recurso
* Recomendações Pendentes
* Recomendações da VM
* Alertas ao longo do tempo
* Recursos atacados
* IPs atacados

Atrás de cada gráfico existem informações adicionais. Selecione um mosaico para obter mais informações. Por exemplo, o mosaico **Estado de Segurança do Recurso** mostra-lhe detalhes adicionais acerca das recomendações pendentes por recursos, conforme mostrado no ecrã seguinte:

![Recomendações](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Se clicar em qualquer linha deste gráfico, as outras ficam cinzentas e o cursor foca-se apenas na que selecionou. Para regressar ao dashboard, clique em **Centro de Segurança do Azure** sob a opção **Dashboards** no painel à esquerda desta página.

> [!NOTE]
> Se pretender personalizar os seus relatórios ao adicionar campos adicionais ou alterar os efeitos visuais existentes, pode editar o relatório. Leia [Interagir com um relatório na Vista de Edição no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) para obter mais informações.
>
>

Os mosaicos **Alertas ao longo do Tempo, Recursos Atacados** e **IPs Atacantes** terão o resultado semelhante quando clica em cada um deles. Isto acontece porque o relatório agrega as informações relativas a essas três variáveis e denomina-o **Recursos sob Ataque** conforme é mostrado no ecrã seguinte:

![Recursos sob ataque](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

Neste momento também pode guardar uma cópia deste relatório, imprimi-la ou publicá-la na Web ao utilizar as opções disponíveis no menu **Ficheiro**.

![Menu Ficheiro](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Explorar os dados do Centro de Segurança do Azure com os serviços Power BI
Ligue-se aos [Serviços do Pacote de Conteúdo do Power BI](https://msit.powerbi.com/groups/me/getdata/services) no Power BI e execute os seguintes passos:

1. Na janela **Pacote de conteúdo do Power BI** verá duas opções conforme é mostrado abaixo.

    ![Pacote de conteúdo para o Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

   > [!NOTE]
   > Se já tiver executado a primeira parte deste artigo, apenas verá uma opção, ou seja, a Gestão de Políticas do Centro de Segurança do Azure.
   >
   >
2. Para efeitos deste exemplo, clique em **Obter** no mosaico **Gestão de Políticas do Centro de Segurança do Azure**.
3. Na janela **Ligar à Gestão de Políticas do Centro de Segurança do Azure**, certifique-se de que seleciona **oAuth2** no menu pendente **Método de Autenticação** conforme mostrado abaixo e clique no botão **Iniciar sessão**.

    ![Janela de Gestão de Políticas](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)
4. Será redirecionado para uma página de autenticação onde deve escrever as credenciais que está a utilizar para se ligar ao Centro de Segurança do Azure. Após a conclusão do processo de autenticação, o Power BI inicia a importação dos dados para criar os seus relatórios. Durante esta altura é possível que apareça a mensagem no canto superior direito do seu browser:

    ![Ligar-se ao Centro de Segurança do Azure através do Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

   > [!NOTE]
   > quando o dashboard está a ser criado pela primeira vez, poderá demorar mais do que o habitual, principalmente para cenários em que tem várias subscrições.
   >
   >
5. Assim que o processo estiver concluído, o dashboard do Power BI do Centro de Segurança do Azure será carregado com o relatório da **Gestão de Políticas**, semelhante ao apresentado abaixo:

    ![Dashboard de Gestão de Políticas](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a utilizar o Power BI no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear a adoção do Centro de Segurança do Azure.
* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar definições de segurança no Centro de Segurança do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure
