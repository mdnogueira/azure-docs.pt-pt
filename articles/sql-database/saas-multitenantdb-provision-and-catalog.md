---
title: "Aprovisionar e novos inquilinos numa aplicação SaaS utilizando uma base de dados do SQL Server de base de dados do SQL Azure multi-inquilino de catálogo | Microsoft Docs"
description: "Saiba como aprovisionar e novos inquilinos na aplicação de SaaS SQL Database do Azure multi-inquilino de catálogo"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: ec753027c8ce8040cbc574279a44eb24590fcb05
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>Aprovisionar e catálogo novos inquilinos numa aplicação SaaS utilizando uma base de dados de SQL Server em partição horizontal do multi-inquilino

Neste tutorial, pode saber mais sobre padrões para o aprovisionamento e cataloging inquilinos ao trabalhar com um modelo a base de dados do multi-inquilino. 

Um esquema de multi-inquilino, o que inclui um Id de inquilino na chave primária de tabelas que contém dados de inquilino, permite que vários inquilinos ser armazenada na base de dados individual. Para suportar grande número de inquilinos, os dados do inquilino são distribuídos por vários shards ou bases de dados. Os dados para qualquer um inquilino sempre totalmente estão contidos numa única base de dados.  Um catálogo é utilizado para conter o mapeamento de inquilinos para bases de dados.   

Também pode optar por preencher algumas bases de dados com apenas um único inquilino. Bases de dados que contêm vários inquilinos favor um custo mais baixo por inquilino em detrimento de isolamento de inquilino.  Bases de dados que contêm apenas um único inquilino favor isolamento através de custo baixo.  Bases de dados com múltiplos inquilinos e inquilinos únicos podem ser misto na mesma aplicação SaaS a otimizar os custos ou de isolamento para cada inquilino. Os inquilinos podem ser especificados a sua própria base de dados quando aprovisionado ou podem ser movidas para a sua própria base de dados mais tarde.

   ![A base de dados do multi-inquilino de aplicação com o catálogo de inquilino](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Padrão de catálogo de inquilino

Se os dados de inquilino são distribuídos por várias bases de dados, é importante saber onde os dados de cada inquilino são armazenados. Uma base de dados do catálogo detém o mapeamento entre um inquilino e a base de dados que armazena os respetivos dados.

Cada inquilino é identificado por uma chave no catálogo. A chave de inquilino do aplicações SaaS de bilhetes Wingtip, tem um formato de um hash do nome do inquilino. Isto permite que a aplicação para extrair o nome do inquilino de um URL de página web e utilize esta opção para ligar à base de dados adequada. Também podem ser utilizados outros esquemas de chave de inquilino.

Utilizar um catálogo permite que o nome ou localização de uma base de dados do inquilino para ser alterada após o aprovisionamento sem perturbar a aplicação.  No modelo de base de dados do multi-inquilino, isto permite a mover um inquilino entre bases de dados.  O catálogo também pode ser utilizado para indicar a uma aplicação, se um inquilino está offline para manutenção ou outras ações.

O catálogo pode ser expandido para armazenar metadados adicionais de inquilinos ou de base de dados, tais como a edição de uma base de dados, versão do esquema de base de dados, nome de inquilino e plano de serviço ou SLA e outras informações para ativar a gestão de aplicações, o suporte ao cliente ou devops ou camada processos.  

O catálogo também pode ser utilizado para ativar a gestão de esquema de relatórios, cross-inquilinos e extrair dados para efeitos de análise. 

### <a name="elastic-database-client-library"></a>Biblioteca de Clientes da Base de Dados Elástica 
Nas aplicações SaaS de bilhetes Wingtip, o catálogo está implementado a *tenantcatalog* da base de dados utilizando as funcionalidades de gestão de partições horizontais o [biblioteca de cliente de base de dados elásticas (EDCL)](sql-database-elastic-database-client-library.md). A biblioteca ativa uma aplicação criar, gerir e utilizar uma cópia da base de dados 'mapa de partições horizontais'. Um mapa de partições horizontais contém uma lista de partições horizontais (bases de dados) e o mapeamento entre as chaves (inquilino Ids) e shards.  Funções EDCL podem ser utilizadas de aplicações ou scripts do PowerShell durante inquilino aprovisionamento para criar as entradas no mapa de partições horizontais e versões posterior, para ligar à base de dados correto. A biblioteca coloca em cache as informações da ligação para minimizar o tráfego na base de dados de catálogo e acelerar a ligação. 

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis na base de dados do catálogo, mas *não editá-lo!* Edite os dados de mapeamento apenas com as APIs da Biblioteca de Clientes da Base de Dados Elástica. Se manipular diretamente os dados de mapeamento arrisca-se a danificar o catálogo, além de não ser suportado.


## <a name="tenant-provisioning-pattern"></a>Padrão de aprovisionamento do inquilino

Quando aprovisionar um novo inquilino no modelo da base de dados do multi-inquilino, tem primeiro de ser determinou-se o inquilino está a ser aprovisionado numa base de dados partilhado ou fornecidos a sua própria base de dados. Se uma base de dados partilhada, têm de ser determinada se há espaço na base de dados existente ou uma nova base de dados é necessária. Se não for necessária uma nova base de dados, têm de ser aprovisionado na localização adequada e escalão de serviço, inicializado com dados de esquema e de referência adequada e, em seguida, registados no catálogo. Por fim, o mapeamento do inquilino pode ser adicionado para referenciar o ID de partição horizontal adequado.

Aprovisione a base de dados ao executar scripts de SQL, implementar um bacpac ou copiar uma base de dados do modelo. As aplicações de SaaS de bilhetes Wingtip copiar uma base de dados do modelo para criar novas bases de dados do inquilino.

A abordagem de aprovisionamento de base de dados deve compreender na estratégia de gestão de esquema geral, o que precisa de garantir que as novas bases de dados são aprovisionados com o esquema mais recente.  Isto é explorou posteriormente o [tutorial de gestão de esquema](saas-tenancy-schema-management.md).  

Os scripts de aprovisionamento do inquilino neste tutorial incluem tanto um inquilino para uma base de dados partilhados com outros inquilinos e o aprovisionamento de um inquilino na sua própria base de dados de aprovisionamento. Dados de inquilino, em seguida, são inicializados e registados no mapa de partições horizontais de catálogo. Na aplicação de exemplo, as bases de dados que contêm vários inquilinos recebem um nome genérico, como *tenants1*, *tenants2*, etc., enquanto as bases de dados que contém um único inquilino recebem o nome do inquilino. As convenções de nomenclatura específicas utilizadas no exemplo não são uma parte crucial do padrão, como a utilização de um catálogo permite que qualquer nome a atribuir à base de dados.  

## <a name="provision-and-catalog-tutorial"></a>Tutorial de aprovisionamento e de catálogo

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Aprovisionar um inquilino para uma base de dados do multi-inquilino
> * Aprovisionar um inquilino para uma base de dados de inquilino único
> * Aprovisionar um lote de inquilinos em bases de dados do multi-inquilinos e de inquilino único
> * Registar uma base de dados e o mapeamento no catálogo do inquilino

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-management-scripts"></a>Obter os scripts de gestão de pedidos de Wingtip

Os scripts de gestão e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório do GitHub. <!--See [Steps to download the Wingtip SaaS scripts](saas-tenancy-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).-->


## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>Aprovisionar um inquilino na base de dados partilhada com outros inquilinos

Para compreender como a aplicação de bilhetes Wingtip implementa novo inquilino aprovisionamento numa base de dados partilhada, adicione um ponto de interrupção e passo através do fluxo de trabalho:

1. No _ISE do PowerShell_, abra... \\Learning módulos\\ProvisionAndCatalog\\_demonstração ProvisionAndCatalog.ps1_ e defina os seguintes parâmetros:
   * **$TenantName** = **Bushwillow Blues**, o nome do venue de novo.
   * **$VenueType** = **blues**, um dos tipos venue predefinidos: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer ( minúsculas, sem espaços).
   * **$Scenario** = **1**ao *aprovisionar um inquilino na base de dados partilhada com outros inquilinos*.

1. Adicionar um ponto de interrupção, colocando o cursor em qualquer lugar na linha 38, a linha que diz: *inquilino novo '*e prima **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. Para executar a prima script **F5**.

1. Depois de parar a execução do script, o ponto de interrupção, prima **F11** para o passo no código.

   ![depurar](media/saas-multitenantdb-provision-and-catalog/debug.png)

Execução do script de rastreio utilizando a **depurar** opções de menu **F10** e **F11**, ao passo ao longo de ou para as funções de chamada. Para obter mais informações sobre a depuração de scripts do PowerShell, consulte [sugestões sobre a trabalhar com e a depuração de scripts do PowerShell como](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Seguem-se entre os principais elementos do fluxo de trabalho aprovisionamento, siga os passos:

* **Calcular a nova chave de inquilino**. É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
* **Verificar se a chave de inquilino existe**. O catálogo é verificado para garantir que a chave já não foi registada.
* **Inicializar o inquilino na base de dados do inquilino predefinido**. A base de dados do inquilino é atualizado para adicionar as novas informações de inquilino.  
* **Registar o inquilino no catálogo** o mapeamento entre a nova chave de inquilino e a base de dados existente do tenants1 é adicionado ao catálogo. 
* **Adicionar o nome do inquilino a uma tabela de extensão de catálogo**. O nome de venue é adicionado à tabela inquilinos no catálogo.  Isto mostra como a base de dados do catálogo pode ser expandido para suportar dados específicos da aplicação adicionais.
* **Abra página de eventos para o novo inquilino**. O *Bushwillow Blues* é abrir a página de eventos no browser:

   ![eventos](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>Aprovisionar um inquilino na sua própria base de dados

Agora explicação passo a passo do processo quando criar um inquilino na sua própria base de dados:

1. Ainda no... \\Learning módulos\\ProvisionAndCatalog\\_demonstração ProvisionAndCatalog.ps1_ definir os seguintes parâmetros:
   * **$TenantName** = **sequoia Soccer**, o nome do venue de novo.
   * **$VenueType** = **soccer**, um dos tipos venue predefinidos: blues classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, *soccer* ( minúsculas, sem espaços).
   * **$Scenario** = **2**ao *aprovisionar um inquilino na base de dados partilhada com outros inquilinos*.

1. Adicionar um novo ponto de interrupção, colocando o cursor em qualquer lugar na linha 57, a linha que diz:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase '*e prima **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. Prima **F5** para executar o script.

1. Depois de parar a execução do script, o ponto de interrupção, prima **F11** para o passo no código.  Utilize **F10** e **F11** passo ao longo e avance para as funções para a execução de rastreio.

Seguem-se entre os principais elementos do fluxo de trabalho, siga os passos enquanto o script de rastreio:

* **Calcular a nova chave de inquilino**. É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
* **Verificar se a chave de inquilino existe**. O catálogo é verificado para garantir que a chave já não foi registada.
* **Criar uma nova base de dados do inquilino**. A base de dados é criado ao copiar o *basetenantdb* da base de dados com um modelo do Resource Manager.  O novo nome de base de dados é baseado no nome do inquilino.
* **Adicionar a base de dados ao catálogo**. A nova base de dados de inquilino está registado como um ID de partição horizontal no catálogo.
* **Inicializar o inquilino na base de dados do inquilino predefinido**. A base de dados do inquilino é atualizado para adicionar as novas informações de inquilino.  
* **Registar o inquilino no catálogo** o mapeamento entre a nova chave de inquilino e a *sequoiasoccer* base de dados é adicionada ao catálogo.
* **Nome do inquilino é adicionada ao catálogo de**. O nome de venue é adicionado à tabela de extensão de inquilinos no catálogo.
* **Abra página de eventos para o novo inquilino**. O *Sequoia Soccer* é abrir a página de eventos no browser:

   ![eventos](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionar um lote de inquilinos

Neste exercício aprovisiona um lote de 17 inquilinos. Recomenda-se que aprovisionar este lote de inquilinos antes de iniciar os outros tutoriais de bilhetes Wingtip pelo que existem mais bases de dados para trabalhar com.

1. No *ISE do PowerShell*, abra... \\Learning módulos\\ProvisionAndCatalog\\*demonstração ProvisionAndCatalog.ps1* e altere o *$Scenario* parâmetro para 3:
   * **$Scenario** = **3**ao *aprovisionar um lote de inquilinos numa base de dados partilhado*.
1. Prima **F5** e execute o script.


### <a name="verify-the-deployed-set-of-tenants"></a>Verifique se o conjunto implementado de inquilinos 
Nesta fase tem uma combinação de inquilinos implementados para uma base de dados partilhada e inquilinos implementados para as suas próprias bases de dados. O portal do Azure pode ser utilizado para inspecionar as bases de dados criadas:  

* No [portal do Azure](https://portal.azure.com), abra o **tenants1-mt -\<utilizador\>**  servidor ao navegar para a lista de servidores SQL.  O **bases de dados SQL** lista deve incluir partilhado **tenants1** base de dados e as bases de dados para os inquilinos que estão na sua própria base de dados:

   ![lista de bases de dados](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Enquanto o portal do Azure mostra o inquilino bases de dados, não permitem-lhe ver os inquilinos *dentro* a base de dados partilhado. A lista completa de inquilinos pode ser vista na página do hub de eventos de pedidos de suporte de Wingtip e procurando o catálogo de:   

1. Abra a página de Hub de eventos no browser (http:events.wingtip-mt.\<utilizador\>. trafficmanager.net)  

   A lista completa de inquilinos e a respetiva base de dados correspondente está disponível no catálogo. Uma vista SQL é fornecida na base de dados tenantcatalog que associa o nome do inquilino armazenado na tabela de inquilinos para o nome de base de dados nas tabelas de gestão de partições horizontais. Esta vista será demonstra o valor de expandir os metadados armazenados no catálogo.

2. No *SQL Server Management Studio (SSMS)*, ligar ao servidor de inquilinos em **tenants1 mt.\<utilizador\>. database.windows.net**, com início de sessão: **Programador** , Palavra-passe:**P@ssword1**

    ![Caixa de diálogo de ligação de SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. No *Object Explorer*, navegue para as vistas na *tenantcatalog* base de dados.
2. Clique na vista *TenantsExtended* e escolha **selecionar primeiras 1000 linhas**. Tenha em atenção o mapeamento entre o nome de inquilino e a base de dados para os inquilinos diferentes.

    ![Vista de ExtendedTenants no SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões interessantes aprovisionamento incluem:

**Pré-aprovisionar conjuntos elásticos de bases de dados.** O padrão de pré-aprovisionamento exploits o facto de que, quando utilizar conjuntos elásticos, faturação é para o conjunto não as bases de dados. Deste modo, bases de dados podem ser adicionados ao conjunto elástico antes de que forem necessárias sem incorrer em custos adicionais associados. Pré-aprovisionar um conjunto de bases de dados e atribuir-lhes quando for necessário, o tempo necessário para aprovisionar um inquilino numa base de dados pode ser significativamente reduzido. O número de bases de dados previamente aprovisionadas pode ser ajustado conforme necessário para manter uma memória intermédia adequado para a velocidade de aprovisionamento prevista.

**Aprovisionamento automático.** O padrão de aprovisionamento automático de um serviço de aprovisionamento dedicada é utilizado para aprovisionar automaticamente servidores, os conjuntos e as bases de dados conforme necessário – incluindo as bases de dados previamente aprovisionamento no conjuntos elásticos. E se as bases de dados são anular commissioned e eliminados, lacunas que isto cria em conjuntos elásticos podem ser preenchidas pelo serviço de aprovisionamento conforme pretendido. Tal serviço pode ser simples ou complexas – por exemplo, processamento de aprovisionamento em várias localizações geográficas e pode configurar a georreplicação para recuperação após desastre. Com o padrão de aprovisionamento automático, uma aplicação de cliente ou script seria submeter um pedido de aprovisionamento para uma fila para ser processado por um serviço de aprovisionamento e, em seguida, deverá consultar para determinar a conclusão. Se for utilizado previamente aprovisionamento, pedidos seriam rapidamente, processados enquanto outro serviço iria gerir aprovisionamento da base de dados de substituição em segundo plano.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Aprovisionar um novo inquilino único para uma base de dados partilhada do multi-inquilino e a sua própria base de dados
> * Aprovisionar um lote de inquilinos adicionais
> * Siga os passos os detalhes de aprovisionamento de inquilinos e registá-los para o catálogo

Repita o [tutorial de monitorização de desempenho](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
* [How to Debug Scripts in Windows PowerShell ISE (Como Depurar os Scripts do ISE do Windows PowerShell)](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
