---
title: Pesquisas de registo de grupos de computadores no Log Analytics | Microsoft Docs
description: "Grupos de computadores na análise de registos permitem-lhe para pesquisas de registo do âmbito para um conjunto específico de computadores.  Este artigo descreve os diferentes métodos que pode utilizar para criar grupos de computadores e como utilizá-los numa pesquisa de registo."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: f27f038e0507270c0bfe200cb8c86622ebac5372
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Pesquisas de registo de grupos de computadores na análise de registos

Grupos de computadores na análise de registos permitem-lhe âmbito [pesquisas de registo](log-analytics-log-search-new.md) para um conjunto específico de computadores.  Cada grupo é preenchido com computadores ou utilizando uma consulta por si ou através da importação de grupos de diferentes origens.  Quando o grupo é incluído na pesquisa de registo, os resultados estão limitados a registos que correspondem aos computadores no grupo.

## <a name="creating-a-computer-group"></a>Criar um grupo de computadores
Pode criar um grupo de computadores na análise de registos utilizando qualquer um dos métodos na seguinte tabela.  Detalhes sobre cada método são fornecidos nas secções abaixo. 

| Método | Descrição |
|:--- |:--- |
| Pesquisas de registos |Crie uma pesquisa de registo que devolva uma lista de computadores. |
| API de Pesquisas de Registos |Utilize a API de pesquisa de registo para programaticamente criar um grupo de computadores com base nos resultados de pesquisa de registo. |
| Active Directory |Analise automaticamente a associação do grupo de computadores do agente que são membros de um domínio do Active Directory e criar um grupo na análise de registos para cada grupo de segurança. |
| Configuration Manager | Importar coleções do System Center Configuration Manager e criar um grupo na análise de registos para cada. |
| Windows Server Update Services |Procurar o filtragem de grupos de clientes ou servidores WSUS e criar um grupo na análise de registos para cada automaticamente. |

### <a name="log-search"></a>Pesquisas de registos
Grupos de computadores criados a partir de uma pesquisa de registo contém todos os computadores devolvidos por uma consulta por si.  Esta consulta é executada sempre que o grupo de computadores é utilizado para que quaisquer alterações, uma vez que o grupo foi criado é refletido.  

Pode utilizar qualquer consulta para um grupo de computadores, mas tem de devolver um conjunto diferente de computadores utilizando `distinct Computer`.  Segue-se uma pesquisa de exemplo típico que pode utilizar para como um grupo de computadores.

    Heartbeat | where Computer contains "srv" | distinct Computer

A tabela seguinte descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| Nome a apresentar   | Nome da pesquisa para apresentar no portal. |
| Categoria       | Categoria para organizar as procuras no portal. |
| Consulta          | A consulta para o grupo de computadores. |
| Alias de função | Um alias exclusivo utilizado para identificar o grupo de computadores numa consulta. |

Utilize o procedimento seguinte para criar um grupo de computadores a partir de uma pesquisa de registo no portal do Azure.

2. Abra **pesquisa registo** e, em seguida, clique em **pesquisas guardadas** na parte superior do ecrã.
3. Clique em **adicionar** e forneça valores para cada propriedade para o grupo de computadores.
4. Selecione **guardar esta consulta como um grupo de computadores** e clique em **OK**.


Utilize o procedimento seguinte para criar um grupo de computadores a partir de uma pesquisa de registo no portal do OMS.

1. Abra **pesquisa registo** e criar a pesquisa de registo para o grupo de computadores.  
2. Clique em de **guardar** botão na parte superior do ecrã.
3. Selecione **Sim** para **guardar esta consulta como um grupo de computadores**.
5. Fornece valores para cada propriedade para o grupo de computadores. 


>[!NOTE]
> Se a sua área de trabalho ainda está a utilizar o [idioma de consulta de análise de registos legado](log-analytics-log-search-upgrade.md) , em seguida, utilizar o mesmo procedimento para criar um grupo de computadores, mas o tem de utilizar a sintaxe da linguagem de consulta legado.


### <a name="log-search-api"></a>API de pesquisa de registo
Grupos de computadores criados com a API de pesquisa de registo são os mesmos que procura criada com uma pesquisa de registo.  Para obter detalhes sobre a criação de um grupo de computadores utilizando a API de pesquisa de registo consulte [REST API de pesquisa de grupos de computadores no registo de análise de registos](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Quando configura a análise de registos para importar associações a grupos do Active Directory, analisa a associação do grupo de todos os computadores associados a um domínio com o agente do OMS.  É criado um grupo de computador na análise de registos para cada grupo de segurança no Active Directory e é adicionado a cada computador para os grupos de computadores correspondentes aos grupos de segurança são membros do.  Esta associação é constantemente atualizada a cada 4 horas.  

Configurar a análise de registos para importar os grupos de segurança do Active Directory de análise de registos **definições avançadas** no portal do Azure.  Selecione **grupos de computadores**, **do Active Directory**e, em seguida, **associações a grupos de computadores do Active Directory de importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Quando os grupos foram importados, o menu de lista o número de computadores com associações a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver o **grupo de computador** registos com estas informações.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Quando configura a análise de registos para importar associações a grupos WSUS, analisa as associações a filtragem de todos os computadores com o agente do OMS.  Se estiver a utilizar o lado do cliente a filtragem, qualquer computador que está ligado à OMS e faz parte de qualquer WSUS filtragem de grupos tem respetiva associação a grupos importada para análise de registos. Se estiver a utilizar o lado do servidor como objetivo, o OMS agente deve ser instalado no servidor WSUS para que as informações de associação de grupo para serem importados para o OMS.  Esta associação é constantemente atualizada a cada 4 horas. 

Configurar a análise de registos para importar os grupos do WSUS da análise de registos **definições avançadas** no portal do Azure.  Selecione **grupos de computadores**, **WSUS**e, em seguida, **associações a grupos WSUS de importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores do WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Quando os grupos foram importados, o menu de lista o número de computadores com associações a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver o **grupo de computador** registos com estas informações.

### <a name="system-center-configuration-manager"></a>O System Center Configuration Manager
Quando configura a análise de registos para importar associações a coleção do Configuration Manager, cria um grupo de computadores para cada coleção.  As informações de associação da coleção são obtidas todos os 3 horas para manter os grupos de computador atual. 

Antes de importar coleções do Configuration Manager, tem [ligar o Configuration Manager ao Log Analytics](log-analytics-sccm.md).  Em seguida, pode configurar a importação da análise de registos **definições avançadas** no portal do Azure.  Selecione **grupos de computadores**, **SCCM**e, em seguida, **associações de coleção do Gestor de configuração da importação**.  Não há nenhuma configuração adicional.

![Grupos de computadores a partir do SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Quando tiverem sido importadas coleções, o menu de lista o número de computadores com associações a grupos detetados e o número de grupos importados.  Pode clicar em qualquer uma destas ligações para devolver o **grupo de computador** registos com estas informações.

## <a name="managing-computer-groups"></a>Gerir grupos de computadores
Pode ver grupos de computadores que foram criados a partir de uma pesquisa de registo ou a API de pesquisa de registo da análise de registos **definições avançadas** no portal do Azure.  Selecione **grupos de computadores** e, em seguida, **guardar grupos**.  

Clique em de **x** no **remover** coluna ao eliminar o grupo de computadores.  Clique em de **ver membros** ícone para um grupo executar a pesquisa de registo do grupo que devolve os seus membros.  Não é possível modificar um grupo de computadores, mas em vez disso, deve eliminar e, em seguida, recriá-lo com as definições modificadas.

![Grupos de computadores guardados](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Utilizar um grupo de computadores numa pesquisa de registo
Utilize um grupo de computadores de uma consulta por encará-los o alias como uma função, normalmente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, pode utilizar o seguinte procedimento para devolver UpdateSummary registos para apenas os computadores num grupo de computadores chamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`

>[!NOTE]
> Se a sua área de trabalho ainda está a utilizar o [idioma de consulta de análise de registos legado](log-analytics-log-search-upgrade.md)>, em seguida, utilize a seguinte sintaxe para fazer referência a um grupo de computadores numa pesquisa de registo.  Especificar o **categoria** > é opcional e apenas necessário se tiver grupos de computadores com o mesmo nome em diferentes categorias. 
>
>    `$ComputerGroups[Category: Name]`
>
>Grupos de computadores são normalmente utilizados com o **IN** cláusula na pesquisa de registo como no exemplo seguinte:
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>Registos do grupo de computadores
É criado um registo no repositório de OMS para cada associação a grupos criado a partir do Active Directory ou o WSUS.  Estes registos de tem um tipo de **grupo de computador** e ter as propriedades na tabela seguinte.  Os registos não são criados para grupos de computadores com base na procura de registo.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Grupo de computador* |
| SourceSystem |*SourceSystem* |
| Computador |Nome do computador membro. |
| Grupo |Nome do grupo. |
| GroupFullName |Caminho completo para o grupo, incluindo a origem e o nome de origem. |
| GroupSource |Esse grupo de origem foi recolhido a partir. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nome da origem de que o grupo foi recolhido a partir de.  Para o Active Directory, este é o nome de domínio. |
| ManagementGroupName |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, o que é AOI -\<ID da área de trabalho\> |
| TimeGenerated |Data e hora para o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  

