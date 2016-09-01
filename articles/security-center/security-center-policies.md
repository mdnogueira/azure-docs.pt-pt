<properties
   pageTitle="Definir políticas de segurança no Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="yurid"/>

# Definir políticas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança, ao servir de orientação pelos passos necessários para realizar esta tarefa.

## O que são políticas de segurança?
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada ou do grupo de recursos. No Centro de Segurança, é possível definir políticas para as suas subscrições ou grupo de recursos do Azure, de acordo com as necessidades de segurança da empresa e do tipo de aplicações ou sensibilidade dos dados em cada subscrição.

Por exemplo, os recursos utilizados para programação ou testes podem ter requisitos de segurança diferentes dos que são utilizados para aplicações de produção. Da mesma forma, as aplicações com dados regulados como PII (Informação Identificativa) podem exigir um nível mais elevado de segurança. As políticas de segurança ativadas no Centro de Segurança do Azure aplicam recomendações de segurança e monitorização para o ajudar a identificar potenciais vulnerabilidades e a mitigar ameaças. Leia o [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) para obter mais informações sobre como decidir qual a opção mais adequada para si.

## Definir políticas de segurança para subscrições

As políticas de segurança podem ser configuradas para cada subscrição ou grupo de recursos. Para modificar uma política de segurança, tem de ser um Proprietário ou Contribuinte dessa subscrição. Aceda ao Portal do Azure e siga os passos abaixo para configurar as políticas de segurança no Centro de Segurança:

1. Clique no mosaico **Política** no dashboard do Centro de Segurança.

2. No painel **Política de Segurança – Definir política por subscrição ou grupo de recursos** que se abre no lado direito, selecione a subscrição na qual pretende ativar a política de segurança. Se preferir ativar a Política de Segurança para um Grupo de Recursos em vez de para a subscrição completa, desloque para baixo até à secção seguinte onde abordamos a configuração de políticas de segurança para Grupos de Recursos.

    ![Definir política](./media/security-center-policies/security-center-policies-fig1-ga.png)

3. O painel **Política de segurança** dessa subscrição abre-se com um conjunto de opções semelhantes às que são mostradas no seguinte ecrã:

    ![Ativar a recolha de dados](./media/security-center-policies/security-center-policies-fig2-ga.png)

4. Certifique-se de que a opção **Recolher dados de máquinas virtuais** está **Ativada**. Esta opção permite a recolha automática de registos relativos a recursos novos e existentes. 

    >[AZURE.NOTE] Recomendamos que ative a recolha de dados para cada uma das suas subscrições para garantir que a monitorização de segurança está disponível em todas as VMs novas e existentes. Ativar a recolha de dados instala o agente de monitorização. Se não pretender ativar a recolha de dados agora a partir desta localização, pode fazê-lo mais tarde a partir das vistas de estado de funcionamento e recomendações. Também pode ativar a recolha de dados apenas para a subscrição ou para VMs selecionadas. Consulte [FAQ do Centro de Segurança do Azure](security-center-faq.md) para saber mais sobre as VMs suportadas.

5. Se a sua conta de armazenamento ainda não estiver configurada, pode ver um aviso semelhante ao mostrado no ecrã seguinte quando abre a **Política de Segurança**:

    ![Seleção de armazenamento](./media/security-center-policies/security-center-policies-fig2.png)

6. Se vir este aviso, clique nesta opção e selecione a região, conforme mostrado no ecrã seguinte:

    ![Seleção de armazenamento](./media/security-center-policies/security-center-policies-fig3-ga.png)

7. Para cada região na qual tenha máquinas virtuais em execução, selecione a conta do Storage onde os dados recolhidos dessas máquinas virtuais estão armazenados. Isto faz com que seja mais fácil manter os dados na mesma área geográfica para fins de privacidade e soberania dos dados. Assim que decidir qual a região que irá utilizar, selecione a região e, em seguida, selecione a conta do Storage.

8. No painel **Escolher contas do Storage**, clique em **OK**.

    > [AZURE.NOTE] Se preferir, pode agregar dados a partir de máquinas virtuais em várias regiões numa conta do Storage central. Consulte [FAQ do Centro de Segurança do Azure](security-center-faq.md) para obter mais informações.

9. No painel **Política de Segurança**, clique em **Ativada** para ativar as recomendações de segurança que pretende utilizar nesta subscrição. Clique na opção **Política de prevenção**. O painel **Política de Segurança** abrirá conforme mostrado no ecrã seguinte: 

    ![Selecionar as políticas de segurança](./media/security-center-policies/security-center-policies-fig4-ga.png)

Utilize a tabela seguinte como uma referência para compreender o que irá fazer cada opção:

| Política | Quando o Estado é Ativada |
|----- |-----|
| Atualizações do Sistema | Diariamente, obtém uma lista de atualizações críticas e de segurança disponíveis a partir do Windows Update ou dos WSUS, dependendo do serviço que está configurado para essa máquina virtual e recomenda que as atualizações em falta sejam aplicadas. Esta ação verifica as atualizações mais recentes nos sistemas Linux através do sistema de gestão de pacotes fornecido por distro para determinar quais os pacotes com atualizações disponíveis. Também verifica se existem atualizações críticas e de segurança de máquinas virtuais dos [Serviços Cloud](./cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do SO | Diariamente, analisa as configurações do SO que podem tornar a máquina virtual mais vulnerável a ataques e recomenda alterações na configuração para resolver estas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas a serem monitorizadas. |
| Endpoint Protection | Recomenda que a Endpoint Protection seja aprovisionada para todas as máquinas virtuais do Windows de modo a ajudar a identificar e remover vírus, spyware e outro software malicioso.|
| Encriptação de Disco | Recomenda a ativação da encriptação de disco em todas as máquinas virtuais para melhorar a proteção de dados inativos. 
| Grupos de Segurança de Rede | Recomenda que sejam configurados [Grupos de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) (NSGs) para controlar o tráfego de entrada e de saída para VMs com pontos finais públicos. Os NSGs configurados para uma sub-rede serão herdados por todas as interfaces de rede de máquina virtual, exceto se for especificado em contrário. Para além de verificar que um NSG foi configurado, esta opção avalia as regras de segurança de entrada para identificar se existem regras que permitam o tráfego de entrada. |
| Firewall de Aplicação Web | Recomenda que uma Firewall de Aplicação Web seja aprovisionada em máquinas virtuais quando: [IP Público de Nível de Instância](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) é utilizado e as Regras de Segurança de Entrada do NSG associado estão configuradas para permitir o acesso à porta 80/443. O IP com Balanceamento de Carga (VIP) é utilizado e o balanceamento de carga associado e as regras de NAT de entrada estão configuradas para permitir o acesso à porta 80/443 (para obter mais informações, consulte [Suporte do Azure Resource Manager para Load Balancer](../load-balancer/load-balancer-arm.md)) |
| Firewall da Próxima Geração | Isto expande as proteções de rede para além dos Grupos de Segurança de Rede, que estão incorporados no Azure. O Centro de Segurança irá detetar as implementações para as quais se recomenda uma Firewall da Próxima Geração e permite-lhe aprovisionar uma aplicação virtual. |
| Auditoria do SQL | Recomenda que a auditoria de acesso aos Servidores e Bases de Dados SQL do Azure esteja ativada para fins de conformidade, deteção avançada e investigação. |
| Encriptação de Dados Transparente SQL | Recomenda que a encriptação inativa seja ativada para as suas bases de dados SQL do Azure, cópias de segurança associadas e ficheiros de registo de transação para que, mesmo que os seus dados sofram falhas de segurança, não sejam legíveis. |
    
Depois de concluir a configuração de todas as opções, clique em **OK** no painel **Política de Segurança** que tem as recomendações e clique em **Guardar** no painel **Política de Segurança** que tem as definições iniciais.

## Definir políticas de segurança para grupos de recursos

Se preferir configurar as políticas de segurança por grupo de recursos, os passos são semelhantes aos que utiliza para a configuração de políticas de segurança para subscrições. A diferença principal é que será necessário expandir o nome da subscrição e selecionar o grupo de recursos no qual pretende configurar a política de segurança exclusiva:

![Seleção de grupos de recursos](./media/security-center-policies/security-center-policies-fig5-ga.png)

Depois de selecionar o grupo de recursos, o painel **Política de Segurança** abre-se. Por predefinição, a opção **Herança** está ativada, o que significa que todas as políticas de segurança para este grupo de recursos são herdadas do nível da subscrição. Pode alterar esta configuração no caso de pretender uma política de segurança personalizada por grupo de recursos. Se for esse o caso, tem de selecionar **Exclusiva** e efetuar as alterações na opção **Política de prevenção**.

![Política de segurança por grupo de recursos](./media/security-center-policies/security-center-policies-fig6-ga.png)

> [AZURE.NOTE] Em caso de conflito entre a política de nível da subscrição e a política de nível do grupo de recursos, a política de nível de recursos tem prioridade.


## Consultar também

Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

- [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
- [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure



<!--HONumber=ago16_HO4-->


