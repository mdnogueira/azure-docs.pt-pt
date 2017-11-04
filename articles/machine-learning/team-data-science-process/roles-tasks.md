---
title: "Equipa de funções do processo de ciência de dados e tarefas - Azure | Microsoft Docs"
description: "Uma descrição dos componentes chave, funções de pessoal e tarefas associadas para um projeto de equipa de ciência de dados."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: b502a586cdb7351d5b22c6b0db966723b8ebb7b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-roles-and-tasks"></a>Funções do processo de ciência de dados de equipa e tarefas

O processo de ciência de dados de equipa é uma arquitetura desenvolvida pela Microsoft que fornece uma estruturados metodologia para criar soluções de Análise Preditiva e aplicações inteligentes de forma eficiente. Este artigo descreve as funções de chave técnicos, e das respetivas tarefas associadas são processados por uma ciência de dados equipa uniformizar neste processo. 

Este ligações de introdução para tutoriais com instruções sobre como configurar o ambiente de TDSP para o grupo de ciência de dados completo, as equipas de ciência de dados e projetos. Podemos fornecer instruções detalhadas sobre como utilizar os serviços de equipa do Visual Studio (VSTS) nos tutoriais como nossa plataforma que aloja o código e a ferramenta de planeamento seja ágil para gerir as tarefas de equipa, controlar o acesso e gerir os repositórios. 

Também poderá utilizar estas informações para implementar TDSP na sua própria ferramenta seja ágil e que aloja o código de planeamento. 

## <a name="structures-of-data-science-groups-and-teams"></a>Estruturas de grupos de ciência de dados e equipas
As funções de ciência de dados em empresas, muitas vezes, poderão sejam organizadas da hierarquia seguinte:

1. ***Grupo de ciência de dados/s***

2. ***Dados ciência equipa/s, no grupo/s***

Essa uma estrutura existe será são grupo em equipa de clientes potenciais clientes potenciais. Normalmente, um projeto de ciência de dados é efetuado por um agrupamento de ciência de dados, o que pode ser composto por n clientes potenciais do projeto clientes potenciais (para tarefas de gestão e governação de projeto) e cientistas de dados ou engenheiros (contribuintes individuais / técnica técnico) que executará a ciência de dados dados e partes do projeto de engenharia. Antes da execução, a configuração e governação é feito através de clientes potenciais clientes potenciais grupo, a equipa ou projeto.

## <a name="definition-of-four-tdsp-roles"></a>Definição de funções TDSP quatro
Com pressuposto acima, especificámos a quatro funções distintas para o nosso pessoal equipa:

1. ***Gestor de grupo***. Gestor de grupo é o Gestor da unidade de ciência de dados completo numa empresa. Uma unidade de ciência de dados pode ter várias equipas, cada um dos quais está a funcionar em vários projetos de ciência de dados no verticals negócio distintos. Um Gestor de grupo pode delegar as suas tarefas para um substituto, mas as tarefas associadas a função de não alterar.

2. ***Equipa de oportunidades potenciais***. Uma antecedência de equipa está a gerir uma equipa na unidade de ciência de dados de uma empresa. Uma equipa é composta por vários cientistas de dados. Para a unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o Gestor de grupo e liderar a equipa podem ser a mesma pessoa.

3. ***Projeto oportunidades potenciais***. Uma antecedência de projeto gere as atividades diárias de cientistas de dados individual num projeto de ciência de dados específicos.

4. ***Projeto contribuinte individuais***. Dados Scientist, analista de negócios, engenheiro de dados, arquiteto de sistemas, etc. Um contribuinte individuais de projeto executa um projeto de ciência de dados. 


**[AZURE.NOTE]**: Dependendo da estrutura de uma empresa, uma única pessoa pode reproduzir funções mais do que um ou poderá existir mais do que uma pessoa trabalhar numa função. Frequentemente, isto poderá ser o caso em que as pequenas empresas ou as empresas com um pequeno número de técnico na respetiva organização de ciência de dados.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Tarefas a ser concluída por quatro técnico

A imagem seguinte ilustra as tarefas de nível superior para técnico pela função na adoção e implementar o processo de ciência de dados de equipa que conceptualized pela Microsoft. 

![Descrição geral de funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

Este esquema e o contorno seguinte, mais detalhado de tarefas que são atribuídos a cada função de TDSP devem ajudar a escolher o tutorial adequado com base nas suas responsabilidades na organização.

>[AZURE.NOTE] Nas instruções que se seguem, mostramos passos sobre como configurar um ambiente TDSP e outras tarefas de ciência de dados nos serviços de equipa de Studio Visual (VSTS) ser concluído. Especificamos como realizar estas tarefas com VSTS, uma vez que é o que estamos a utilizar para implementar TDSP na Microsoft. VSTS facilita a colaboração ao integrar a gestão de itens de trabalho que controlam as tarefas e um serviço de alojamento de código utilizado para partilhar utilitários, organizar versões e fornecer a segurança baseada em funções. É possível escolher outras plataformas, se preferir, para implementar as tarefas descritas pelo TDSP. Mas, dependendo da sua plataforma, algumas funcionalidades, que iremos tirar partido do VSTS poderão não estar disponíveis. 
>
>Também utilizamos o [Máquina Virtual de ciência de dados (DSVM)](http://aka.ms/dsvm) do Azure na nuvem como o ambiente de trabalho de análise com várias ferramentas de ciência de dados populares pré-configurados e integrados com vários serviços do Azure e software da Microsoft. Pode utilizar o DSVM ou qualquer outro ambiente de desenvolvimento para implementar TDSP. 


## <a name="group-manager-tasks"></a>Grupo do Gestor de tarefas

As seguintes tarefas são concluídas o Gestor de grupo (ou um administrador de sistema TDSP designado) adotar o TDSP:

- Criar um **conta de grupo** num código de plataforma (como o Github, Git, VSTS ou outros) de alojamento
- Criar um **repositório de modelo de projeto** na conta de grupo e seed-lo no repositório de modelo de projeto desenvolvida pela equipa do Microsoft TDSP. O repositório de modelo de projeto TDSP da Microsoft fornece um **padronizado estrutura de diretórios** , incluindo diretórios de dados, o código e documentos e fornece um conjunto de **padronizado modelos de documento** ajudará a um processo de ciência de dados eficiente. 
- Criar um **repositório de utilitário**e o seeding do repositório utilitário desenvolvido pela Microsoft TDSP equipa. O repositório de utilitário TDSP da Microsoft fornece um conjunto de utilitários útil para tornar o trabalho de um scientist dados mais eficaz, incluindo os utilitários de exploração de dados interativa, análises e relatórios e para a linha de base de modelação e relatórios.
- Configurar o **política de controlo de segurança** destas duas repositórios na sua conta de grupo.  

Para obter instruções passo a passo detalhadas, consulte [Gestor do grupo de tarefas para uma equipa de ciência de dados](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Tarefas de fabrico de equipa

As seguintes tarefas são concluídas liderar a equipa (ou um administrador de projeto de equipa designado) adotar o TDSP:

- Se VSTS está selecionado para ser a plataforma de alojamento de código para o controlo de versões e de colaboração, crie um **projeto de equipa** no servidor VSTS do grupo. Caso contrário, esta tarefa pode ser ignorada.
- Criar o **repositório de modelo de projeto de equipa** sob o projeto de equipa e seed-lo no repositório de modelo de projeto de Grupo configurar o Gestor de grupo ou o delegado do gestor. 
- Criar o **repositório do utilitário de equipa**e adicione os utilitários de equipa específicos para o repositório. 
- (Opcional) Criar  **[file storage do Azure](https://azure.microsoft.com/services/storage/files/)**  a ser utilizado para armazenar os recursos de dados que podem ser úteis para a equipa de toda. Outros membros da equipa podem montar este arquivo de ficheiros de nuvem partilhada nos respetivos ambientes de trabalho de análise.
- (Opcional) Monte o file storage do Azure para o **máquinas de virtuais de ciência de dados** (DSVM) da equipa de levar e adicionar os recursos de dados no mesmo.
- Configurar o **controlo de segurança** ao adicionar os membros da equipa e configurar os seus privilégios. 

Para obter instruções passo a passo detalhadas, consulte [tarefas equipa levar para uma equipa de ciência de dados](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Tarefas de oportunidades potenciais do projeto

As seguintes tarefas são concluídas levar de projeto adotar o TDSP:

- Criar um **repositório de projeto** sob o projeto de equipa e seed-lo da equipa do repositório de modelo de projeto. 
- (Opcional) Criar **file storage do Azure** a ser utilizado para armazenar recursos de dados do projeto. 
- (Opcional) Monte o file storage do Azure para o **máquinas de virtuais de ciência de dados** (DSVM) do projeto levar e adicionar os recursos de dados do projeto no mesmo.
- Configurar o **controlo de segurança** ao adicionar membros de projeto e configurar os seus privilégios. 

Para obter instruções passo a passo detalhadas, consulte [tarefas projeto levar para uma equipa de ciência de dados](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Tarefas de contribuinte individuais do projeto

As seguintes tarefas são concluídas um contribuinte individuais para realizar o projeto de ciência de dados utilizando o TDSP projeto (normalmente, um Scientist de dados):

- Clone o **repositório de projeto** configurado por antecedência o projeto. 
- (Opcional) Montar partilhado **file storage do Azure** da equipa e projeto no respetivo **máquinas de virtuais de ciência de dados** (DSVM).
- Execute o projeto. 

 
Para obter instruções passo a passo detalhadas para Dependency para um projeto, consulte [contribuintes individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Execução de projeto de ciência de dados
 
Seguindo o conjunto de instruções relevante, cientistas de dados, oportunidades potenciais do projeto e clientes de potenciais clientes potenciais equipa podem criar itens de trabalho para controlar todas as tarefas e fases que necessita de um projeto desde o início para o respetivo fim. Também utilizando o git promove colaboração entre cientistas de dados e assegura que os artefactos gerados durante a execução do projeto têm a versão controlado e partilhado por todos os membros de projeto.

As instruções fornecidas para a execução do projeto tem foi desenvolvidas com base no pressuposto de que ambos os itens de trabalho e git repositórios estão no VSTS do projeto. Utilizar VSTS para ambos permite-lhe associar os itens de trabalho com os ramos de Git dos seus repositórios de projeto. Desta forma, pode controlar facilmente o que não foi executado para um item de trabalho. 

A figura seguinte descreve este fluxo de trabalho para a execução do projeto com o TDSP.

![Execução de projeto de ciência de dados típicas](./media/roles-tasks/overview-project-execute.png)

O fluxo de trabalho inclui passos que podem ser agrupados em três atividades:

- Sprint planeamento (projeto levar)
- Desenvolver artefactos nos ramos de git para resolver itens de trabalho (dados Scientist)
- Revisão de código e intercalação ramos com ramos mestres (levar do projeto ou outros membros do agrupamento)

Para obter instruções passo a passo detalhadas sobre o fluxo de trabalho de execução de projeto, consulte [execução de projetos de ciência de dados](project-execution.md).

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para as descrições das funções e tarefas definidas pelo processo de ciência de dados de equipa mais detalhadas:

- [Grupo do Gestor de tarefas para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de fabrico de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuintes individuais de projeto para uma equipa de ciência de dados](project-ic-tasks.md)