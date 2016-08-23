<properties
    pageTitle="Quais as cargas de trabalho que podem ser protegidas com o Azure Site Recovery?" 
    description="O Azure Site Recovery protege as cargas de trabalho e aplicações através da coordenação da replicação, da ativação pós-falha e da recuperação de máquinas virtuais no local e de servidores físicos para o Azure ou para um site secundário local." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/06/2016" 
    ms.author="raynew"/>

# Quais as cargas de trabalho que podem ser protegidas com o Azure Site Recovery?


Este artigo descreve quais as cargas de trabalho e aplicações que pode proteger com o Azure Site Recovery.

Publique comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Descrição geral

As organizações necessitam de uma estratégia de continuidade empresarial e de recuperação após desastre (BCDR) que determine como as aplicações, cargas de trabalho e dados permanecem disponíveis durante o período de indisponibilidade planeado e imprevisto, e como retomar as condições de trabalho normais com a maior brevidade possível.

A Recuperação de Sites é um serviço Azure que contribui para a sua estratégia BCDR ao permitir que implemente soluções resilientes que reconhecem aplicações através da orquestração da replicação tanto dos servidores físicos no local como das máquinas virtuais para a nuvem (Azure) ou para um site secundário. Se as aplicações se baseiam em Windows ou Linux, e se executam em servidores físicos, VMware ou VMs Hyper-V, pode utilizar a Recuperação de Sites para orquestrar a replicação, efetuar o teste de recuperação após desastres e executar a ativação e reativação pós-falha.


A Recuperação de Sites integra-se com aplicações da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory. A Microsoft também trabalha diretamente com os principais fornecedores, incluindo a Oracle, SAP, IBM e Red Hat para garantir que os seus serviços e aplicações funcionam bem em plataformas da Microsoft, incluindo o Azure. As soluções podem ser personalizadas aplicação a aplicação.

## Porquê utilizar a Recuperação de Sites para proteção de aplicações?

A Recuperação de Sites contribui para a proteção e recuperação a nível de aplicação da seguinte forma: 

- A replicação quase síncrona com os RPOs em apenas 30 segundos para satisfazer as necessidades das aplicações empresariais mais críticas.
- Instantâneos consistentes com aplicações para aplicações com uma ou múltiplas camadas.
- Integração com o AlwaysOn do SQL Server e parceria com outras tecnologias de replicação ao nível da aplicação, incluindo replicação AD, o AlwaysOn da SQL, Grupos de Disponibilidade de Base de Dados do Exchange (DAGs) e Oracle Data Guard.
- Planos de recuperação flexíveis que lhe permitem recuperar toda uma pilha de aplicações com um único clique e incluem scripts externos e ações manuais no plano. 
- A gestão de rede avançada na Recuperação de Sites e no Azure para simplificar os requisitos de rede de aplicação, incluindo a capacidade de reserva de endereços IP, configurar o balanceamento de carga e integrar com o Azure Traffic Manager o que reduz as mudanças de rede de RTO.
-  Uma biblioteca de Automatização do Azure completa que fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com os planos de recuperação.



##Resumo da carga de trabalho

A Recuperação de Sites pode replicar qualquer aplicação em execução numa máquina virtual. Além disso, fizemos parcerias com equipas de produto para executar testes adicionais específicos da aplicação.

**Carga de trabalho** | **Replicar VMs Hyper-V para um site secundário** | **Replicar VMs Hyper-V para o Azure** | **Replicar VMs VMware para um site secundário** | **Replicar VMs VMware para o Azure**
---|---|---|---|---
Active Directory, DNS | S | S | S | S 
Web Apps (IIS, SQL) | S | S | S | S
SCOM | S | S | S | S
SharePoint | S | S | S | S
SAP<br/><br/>Replicar site SAP para o Azure de não cluster | S (testado pela Microsoft) | S (testado pela Microsoft) | S (testado pela Microsoft) | S (testado pela Microsoft)
Exchange (não DAG) | S | Brevemente | S | S
Ambiente de Trabalho Remoto/VDI | S | S | S | N/D 
Linux (sistema operativo e aplicações) | S (testado pela Microsoft) | S (testado pela Microsoft) | S (testado pela Microsoft) | S (testado pela Microsoft) 
Dynamics AX | S | S | S | S
Dynamics CRM | S | Brevemente | S | Brevemente
Oracle | S (testado pela Microsoft) | S (testado pela Microsoft) | S (testado pela Microsoft) | S (testado pela Microsoft)
Servidor de Ficheiros do Windows | S | S | S | S


## Replicar o Active Directory e o DNS

As infraestruturas de DNS e Active Directory são essenciais para a maioria das aplicações da empresa. Durante a recuperação após desastre terá de proteger e recuperar estes componentes da infraestrutura antes de recuperar as cargas de trabalho e aplicações.

Pode utilizar a Recuperação de Sites para criar um plano de recuperação após desastre automatizado completa para Active Directory e DNS. Para o exemplo, se pretende efetuar a ativação pós-falha do SharePoint e SAP a partir de um site principal para um site secundário, pode configurar um plano de recuperação com ativação pós-falha, primeiro, do Active Directory e, depois, um plano de recuperação para cada aplicação adicional específico da aplicação para a ativação pós-falha das outras aplicações que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre como proteger o Active Directory e DNS.

## Proteger o SQL Server

O SQL Server proporciona uma base de serviços de dados para os serviços de dados de muitas aplicações empresariais num datacenter no local.  A Recuperação de Sites pode ser utilizado em conjunto com as tecnologias de SQL Server HA/DR para proteger aplicações empresariais de várias camadas que utilizam o SQL Server. A Recuperação de Sites proporciona:

- Uma solução de recuperação após desastre simples e rentável para o SQL Server. Replique várias versões e edições de servidores independentes e clusters do SQL Server, para o Azure ou para um site secundário.  
- Integração com grupos de disponibilidade AlwaysOn de SQL Server para gerir a ativação e a reativação pós-falha com planos de recuperação do Azure Site Recovery.
- Planos de recuperação completos para todas as camadas numa aplicação, incluindo as bases de dados do SQL Server.
- Dimensionamento do SQL Server para cargas máximas com a Recuperação de Sites “disparando-as” em tamanhos de máquina virtual IaaS maiores no Azure.
- Testes fáceis de recuperação após desastres do SQL Server. Pode executar as ativações pós-falha de teste para analisar os dados e executar verificações de compatibilidade sem afetar o seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre como proteger o SQL server.

##Proteger o SharePoint

O Azure Site Recovery ajuda a proteger as implementações do SharePoint da seguinte forma:

- Elimina a necessidade e os custos da infraestrutura associada a um farm autónomo na recuperação após desastre. Utilize a Recuperação de Sites para replicar todo o farm (camadas da Web, da aplicação e da base de dados) para o Azure ou para um site secundário.
- Simplifica a gestão e implementação das aplicações. As atualizações implementadas no site primário replicam-se automaticamente e, por conseguinte, estão disponíveis depois da ativação e recuperação pós-falha de um farm de um site secundário. Também reduz a complexidade da gestão e os custos associados com o facto de manter atualizado um farm em espera.
- Simplifica o desenvolvimento e o teste de aplicações de SharePoint através da criação de um ambiente de réplica de cópia a pedido do tipo produção para realizar testes e depuração.
- Simplifica a transição para a nuvem utilizando a Recuperação de Sites para migrar as implementações do SharePoint para o Azure.

[Saiba mais](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sobre como proteger o SharePoint.


## Proteger Dynamics AX

O Azure Site Recovery ajuda-o a proteger a sua solução ERP de Dynamics AX:

- Organização da replicação do ambiente completo do Dynamics AX (camadas Web e de AOS, camadas de base de dados e SharePoint) para o Azure ou para um site secundário.
- Simplificação da migração de implementações de Dynamics AX para a nuvem (Azure).
- Simplificação do desenvolvimento e teste de aplicações do Dynamics AX através de uma cópia do tipo produção a pedido para teste e depuração.

[Saiba mais](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sobre como proteger o Dynamic AX.

## Proteger Serviços de Ambiente de Trabalho Remoto 

Os Serviços de Ambiente de Trabalho Remoto (RDS) ativa a infraestrutura de ambiente de trabalho virtual (VDI), ambientes de trabalho baseados em sessão e aplicações, permitindo aos utilizadores trabalhar em qualquer lugar. Com o Azure Site Recovery, pode:

- Replicar ambientes de trabalho virtuais agrupados, geridos ou não geridos, para um site secundário, e aplicações remotas e sessões para um site secundário ou Azure.
- Eis o que é possível replicar:

**RDS** | **Replicar VMs Hyper-V para um site secundário** | **Replicar VMs Hyper-V para o Azure** | **Replicar VMs VMware para um site secundário** | **Replicar VMs VMware para o Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure**
---|---|---|---|---|---|---
**Ambiente de Trabalho Virtual Agrupado (não gerido)** | Sim | Não | Sim | Não | Sim | Não
**Ambiente de Trabalho Virtual Agrupado (gerido e sem UDP)** | Sim | Não | Sim | Não | Sim | Não
**Aplicações remotas e sessões de Ambiente de Trabalho (sem UDP)** | Sim | Sim | Sim | Sim | Sim | Sim


[Saiba mais](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre como proteger o RDS.


## Proteger o Exchange

A Recuperação de Sites ajuda a proteger o Exchange da seguinte forma:

- Em pequenas implementações do Exchange, como servidores únicos ou que não estão em cluster, a Recuperação de Sites pode replicar e efetuar a ativação pós-falha para o Azure ou para um site secundário.
- Em grandes implementações, a Recuperação de Sites integra-se com os DAGs do Exchange.
- Os DAGs do Exchange são a solução recomendada para a recuperação após desastre do Exchange de uma empresa.  Os planos de recuperação da o Recuperação de Sites podem incluir DAGs para coordenar a ativação pós-falha do DAG entre sites.


[Saiba mais](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre como proteger o Exchange.

## Proteger o SAP

Utilize a Recuperação de Sites para proteger a sua implementação de SAP da seguinte forma: 

- Ativar a proteção de toda a implementação SAP através da replicação de diferentes camadas de implementação para o Azure ou para um site secundário.
- Simplificar a migração para a nuvem utilizando a Recuperação de Sites para migrar a implementação do SAP para o Azure.
- Simplificar o desenvolvimento e teste de SAP através de uma cópia do tipo produção a pedido para teste e depuração de aplicações.

[Saiba mais](http://aka.ms/asr-sap) sobre como proteger o SAP.

## Passos seguintes

[Preparar](site-recovery-best-practices.md) para a implementação da Recuperação de Sites.




<!--HONumber=Aug16_HO1-->


