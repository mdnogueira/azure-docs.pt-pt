---
title: "Implementar as atualizações de aplicações - SQL Database do Azure | Microsoft Docs"
description: "Saiba como utilizar a georreplicação SQL Database do Azure para suportar atualizações online da sua aplicação de nuvem."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 58f42859-1e37-463c-a3d8-a3ca2e867148
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 07/16/2016
ms.author: sashan
ms.openlocfilehash: deb91d55e5b796f7b1b53a99866156fe492e0a24
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Gerir atualizações graduais das aplicações em nuvem utilizando o SQL Server da base de dados-georreplicação ativa
> [!NOTE]
> [Replicação geográfica activa](sql-database-geo-replication-overview.md) está agora disponível para todas as bases de dados em todas as camadas.
> 

Saiba como utilizar [georreplicação](sql-database-geo-replication-overview.md) na base de dados do SQL Server para permitir atualizações graduais da sua aplicação de nuvem. Porque a atualização é uma operação acontece, deve ser a parte do planeamento de continuidade do negócio e design. Neste artigo Vamos observar dois métodos diferentes da orquestração o processo de atualização e discutir as vantagens e os compromissos de cada opção. Para efeitos deste artigo utilizaremos uma aplicação simples que consiste num web site ligado a uma base de dados como a respetiva camada de dados. O nosso objetivo é para atualizar a versão 1 da aplicação para a versão 2 sem qualquer impacto significativo na experiência de utilizador final. 

Ao avaliar as opções de atualização, deve considerar os seguintes fatores:

* Impacto na disponibilidade da aplicação durante as atualizações. Quanto a função de aplicação pode ser limitada ou degradada.
* Capacidade de reversão em caso de uma falha de atualização.
* Vulnerabilidade da aplicação se ocorrer uma falha catastrófica relacionado com durante a atualização.
* Dólar Total custo.  Isto inclui a proporcionar uma redundância adicional e os custos incrementais dos componentes temporários utilizados pelo processo de atualização. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Atualizar aplicações que dependem de cópias de segurança de base de dados para recuperação após desastre
Se a aplicação depende de cópias de segurança da base de dados automática e utiliza georrestauro para recuperação após desastre, normalmente é implementada para uma única região do Azure. Neste caso, o processo de atualização implica a criação de uma implementação de cópia de segurança de todos os componentes da aplicação envolvidos na atualização do. Para minimizar a interrupção do utilizador final, irá tirar partido do Gestor de tráfego do Azure (WATM) com o perfil de ativação pós-falha.  O diagrama seguinte ilustra o ambiente operacional antes do processo de atualização. O ponto final <i>contoso 1.azurewebsites.net</i> representa uma ranhura de produção da aplicação que tem de ser atualizado. Para ativar a capacidade reverter a atualização, tem de criar uma ranhura fase com uma cópia totalmente sincronizada da aplicação. Os seguintes passos são necessários para preparar a aplicação para a atualização:

1. Crie uma ranhura de fase da atualização. Para fazer uma base de dados secundária (1) de criar e implementar um web site idêntico na mesma região do Azure. Monitorize secundário para ver se a propagação conclusão do processo.
2. Criar um perfil de ativação pós-falha no WATM com <i>contoso 1.azurewebsites.net</i> como ponto final online e <i>contoso 2.azurewebsites.net</i> como offline. 

> [!NOTE]
> Tenha em atenção os passos de preparação não irão afetar a aplicação na ranhura de produção e que possa funcionar no modo de acesso total.
>  

![Configuração de base de dados SQL, aceda replicação. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Depois de concluídas os passos de preparação a aplicação esteja pronta para a atualização real. O diagrama seguinte ilustra os passos envolvidos no processo de atualização. 

1. Defina a base de dados primária na ranhura de produção para o modo só de leitura (3). Isto irá garantir que a instância de produção da aplicação (V1) permanecem só de leitura durante a atualização, impedindo a potencial divergência de dados entre as instâncias de base de dados V1 e V2.  
2. Desligar a base de dados secundária a utilizar o modo de terminação planeada (4). -Criará uma cópia de independente totalmente sincronizada da base de dados principal. Esta base de dados será atualizado.
3. Ativar a base de dados principal para o modo de leitura / escrita e execute o script de actualização na ranhura de fase (5).     

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Se a atualização foi concluída com êxito agora está pronto para mudar os utilizadores finais para a cópia de teste a aplicação. Agora, ficará a ranhura de produção da aplicação.  Isto envolve mais alguns passos conforme ilustrado no diagrama seguinte.

1. Mudar o ponto final online no perfil de WATM para <i>contoso 2.azurewebsites.net</i>, que aponta para a versão de V2 do web site (6). Agora, torna a ranhura de produção com a aplicação de V2 e o tráfego do utilizador final é direcionado para o mesmo.  
2. Se já não tem os componentes da aplicação V1 para poder em segurança removê-los (7).   

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Se o processo de atualização for bem-sucedida, por exemplo devido a um erro no script de atualização, a ranhura de fase deve ser considerada comprometidos. Para reverter a aplicação para o estado de pré-atualização que simplesmente reverter a aplicação na ranhura de produção para acesso total. Os passos envolvidos são apresentados no diagrama seguinte.    

1. Defina a cópia da base de dados para o modo de leitura / escrita (8). Este procedimento irá restaurar o V1 completa funcionalmente na ranhura de produção.
2. Efetuar a análise da causa raiz e remova os componentes comprometidos na ranhura de fase (9). 

Neste momento, a aplicação está totalmente funcional e os passos de atualização podem ser repetidos.

> [!NOTE]
> A reversão não necessita de alterações no perfil WATM como já aponta para <i>contoso 1.azurewebsites.net</i> como o ponto final do Active Directory.
> 
> 

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

A chave **partido** desta opção é que pode atualizar uma aplicação numa única região utilizando um conjunto de passos simples. O custo de dólar da atualização é relativamente baixo. O principal **compromisso** é que, se ocorrer uma falha catastrófica durante a atualização a recuperação para o estado de pré-atualização irá implicar a implementação da aplicação numa região diferente e restaurar a base de dados da utilização de cópia de segurança georrestauro. Este processo irá resultar num período de indisponibilidade significativo.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Atualizar aplicações que dependem da base de dados a georreplicação para recuperação após desastre
Se a sua aplicação tira partido da replicação geográfica para a continuidade do negócio, que é implementado regiões diferentes, pelo menos, duas com uma implementação ativa na região primária e uma implementação em modo de espera na região de cópia de segurança. Para além dos fatores mencionados anteriormente, o processo de atualização tem de garantir que:

* A aplicação permanece protegida contra falhas catastrófica de todas as vezes durante o processo de atualização
* Os componentes da aplicação georredundante sejam atualizados em paralelo com os componentes do Active Directory

Para alcançar estes objetivos, irá tirar partido do Gestor de tráfego do Azure (WATM) com o perfil de ativação pós-falha com uma ativa e três pontos finais de cópia de segurança.  O diagrama seguinte ilustra o ambiente operacional antes do processo de atualização. Os web sites <i>contoso 1.azurewebsites.net</i> e <i>contoso dr.azurewebsites.net</i> representam uma ranhura de produção da aplicação com redundância geográfica completa. Para ativar a capacidade reverter a atualização, tem de criar uma ranhura fase com uma cópia totalmente sincronizada da aplicação. Porque tem de garantir que a aplicação pode recuperar rapidamente no caso de ocorrer uma falha catastrófica durante o processo de atualização, a ranhura de fase tem de ser georredundante bem. Os seguintes passos são necessários para preparar a aplicação para a atualização:

1. Crie uma ranhura de fase da atualização. Para fazer criar uma base de dados secundária (1) e implemente uma cópia idêntica do web site na mesma região do Azure. Monitorize secundário para ver se a propagação conclusão do processo.
2. Crie uma base de dados secundária georredundante na ranhura de fase através da replicação de georreplicação da base de dados secundária para a região de cópia de segurança (denominado "encadeados georreplicação"). Monitorize a cópia de segurança secundária se o processo de propagação está concluída (3).
3. Criar uma cópia do web site em modo de espera na região de cópia de segurança e ligá-lo para o secundário georredundante (4).  
4. Adicionar os pontos finais adicionais <i>contoso 2.azurewebsites.net</i> e <i>contoso 3.azurewebsites.net</i> para o perfil de ativação pós-falha em WATM como pontos finais offline (5). 

> [!NOTE]
> Tenha em atenção os passos de preparação não irão afetar a aplicação na ranhura de produção e que possa funcionar no modo de acesso total.
> 
> 

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Depois de concluídas os passos de preparação, o bloco de fase é preparado para a atualização. O diagrama seguinte ilustra os passos de atualização.

1. Defina a base de dados primária na ranhura de produção para o modo só de leitura (6). Isto irá garantir que a instância de produção da aplicação (V1) permanecem só de leitura durante a atualização, impedindo a potencial divergência de dados entre as instâncias de base de dados V1 e V2.  
2. Desligar a base de dados secundária na mesma região a utilizar o modo de terminação planeada (7). -Criará uma cópia de independente totalmente sincronizada da base de dados primária, que ficará automaticamente um site primário depois da terminação. Esta base de dados será atualizado.
3. Ativar a base de dados primária na ranhura de fase para o modo de leitura / escrita e execute o script de atualização (8).    

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Se a atualização foi concluída com êxito, agora está pronto para mudar os utilizadores finais para a versão de V2 da aplicação. O diagrama seguinte ilustra os passos envolvidos.

1. Mudar o ponto final do Active Directory no perfil de WATM para <i>contoso 2.azurewebsites.net</i>, que agora aponta para a versão de V2 do web site (9). Torna agora uma ranhura de produção com a aplicação de V2 e o tráfego do utilizador final é direcionado para o mesmo. 
2. Se já não necessita da aplicação V1 para poder em segurança removê-lo (10 e 11).  

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Se o processo de atualização for bem-sucedida, por exemplo devido a um erro no script de atualização, a ranhura de fase deve ser considerada comprometidos. Para reverter a aplicação para o estado de pré-atualização simplesmente reverter para utilizar a aplicação na ranhura de produção com acesso total. Os passos envolvidos são apresentados no diagrama seguinte.    

1. Defina a cópia da base de dados primária na ranhura de produção para o modo de leitura / escrita (12). Este procedimento irá restaurar o V1 completa funcionalmente na ranhura de produção.
2. Efetuar a análise da causa raiz e remova os componentes comprometidos na ranhura de fase (13 e 14). 

Neste momento, a aplicação está totalmente funcional e os passos de atualização podem ser repetidos.

> [!NOTE]
> A reversão não necessita de alterações no perfil WATM como já aponta para <i>contoso 1.azurewebsites.net</i> como o ponto final do Active Directory.
> 
> 

![Configuração de georreplicação da base de dados SQL. Nuvem de recuperação após desastre.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

A chave **partido** desta opção é que pode atualizar a aplicação e respetiva cópia georredundante em paralelo sem comprometer a continuidade do negócio durante a atualização. O principal **compromisso** é que necessita de redundância dupla de cada componente da aplicação e, por conseguinte, incorre em custos de dólar superior. Também envolve um fluxo de trabalho mais complicado. 

## <a name="summary"></a>Resumo
Os dois métodos de atualização descritos no artigo diferem no complexidade e o dólar de custos, mas ambas as focar-se no que minimiza o tempo quando o utilizador final está limitado a operações só de leitura. Essa hora diretamente é definida pela duração do script de atualização. Não dependem de tamanho de base de dados, a camada de serviço que escolheu, a configuração do web site e ainda outros fatores não é possível controlar facilmente. Isto acontece porque todos os passos de preparação estão desacoplados dos passos de atualização e podem ser realizados sem afetar a aplicação de produção. A eficiência do script de atualização é o fator chave que determina a experiência de utilizador final durante as atualizações. Por isso, a melhor forma de possa melhorá-lo é ao concentrar-se os esforços em efetuar o script de actualização como eficiente possível.  

## <a name="next-steps"></a>Passos seguintes
* Para cenários e uma descrição geral de continuidade de negócio, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).
* Para saber mais sobre o SQL do Azure, base de dados automática de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados de cópias de segurança automatizadas](sql-database-recovery-using-backups.md).
* Para saber mais sobre as opções de recuperação mais rápidas, consulte [georreplicação ativa](sql-database-geo-replication-overview.md).


