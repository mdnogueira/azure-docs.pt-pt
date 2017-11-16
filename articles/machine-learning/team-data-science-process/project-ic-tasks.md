---
title: "A equipa de tarefas do processo de ciência de dados para um contribuinte individuais - Azure | Microsoft Docs"
description: "Uma descrição das tarefas para um contribuinte individuais num projeto de equipa de ciência de dados."
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
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: 526260c3f61a203fe2770a0c6d0d23d95916a2b7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="individual-contributor-tasks"></a>Tarefas de contribuinte individuais

Este tópico de destaca as tarefas que é um contribuinte individuais esperado concluir a sua equipa de ciência de dados. O objetivo consiste em estabelecer o ambiente de colaboração de equipa que uniformiza no [o processo de ciência de dados de equipa](overview.md) (TDSP). Para uma descrição das funções de pessoal e das respetivas tarefas associadas são processados por uma equipa de ciência de dados uniformizar neste processo, consulte o artigo [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md).

As tarefas de contribuintes individuais do projeto (cientistas de dados) para configurar o ambiente de TDSP para o projeto estão representadas da seguinte forma: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** é o repositório do que o grupo é manter para partilhar utilitários útil em grupo inteiro. 
- **TeamUtilities** é o repositório de que mantém a sua equipa especificamente para a sua equipa. 

Para obter instruções sobre como executar um projeto de ciência de dados em TDSP, consulte [execução de projetos de ciência de dados](project-execution.md). 

>[AZURE.NOTE] Iremos descrevem os passos necessários para configurar um ambiente de equipa TDSP utilizando os serviços de equipa do Visual Studio (VSTS) nas instruções seguintes. Especificamos como realizar estas tarefas com VSTS, uma vez que é a forma como implementamos TDSP na Microsoft. Se for utilizada noutra plataforma de alojamento de código para o grupo, as tarefas que necessitam de ser concluídos pela antecedência de equipa, geralmente, não alteram. Mas a forma para concluir estas tarefas vai ser diferentes.


## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tutorial utiliza nomes abreviados para repositórios e diretórios. Estes nomes tornam mais fácil de seguir as operações entre os repositórios e diretórios. Este notation (**R** para repositórios de Git e **D** para diretórios locais no seu DSVM) é utilizado nas seguintes secções:

- **R2**: GroupUtilities o repositório no Git que tiver configurado o Gestor de grupo no seu servidor de grupo VSTS.
- **R4**: TeamUtilities o repositório no Git antecedência sua equipa tiver configurado.
- **R5**: repositório o projeto no Git tiver sido configurada pelo seu antecedência de projeto.
- **D2**: O diretório local clonado a partir do R2.
- **D4**: O diretório local clonado a partir do R4.
- **D5**: O diretório local clonado a partir do R5.


## <a name="step-0-prerequisites"></a>Passo-0: pré-requisitos

Os pré-requisitos são satisfeitos, efetuando as tarefas atribuídas para o Gestor de grupo descrito na [Gestor do grupo de tarefas para uma equipa de ciência de dados](group-manager-tasks.md). Para resumir aqui, os seguintes requisitos tem de ser cumpridos antes de começar com as tarefas de oportunidades potenciais equipa: 
- O Gestor de grupo tiver configurado o **GroupUtilities** repositório (se aplicável). 
- Fabrico sua equipa tiver configurado o **TeamUtilities** repositório (se aplicável).
- As oportunidades potenciais projeto tem configurado o repositório de projeto. 
- -Foram adicionados ao seu repositório de projeto pelo seu antecedência de projeto com o privilégio para clonar do e retraem para o repositório de projeto.

O segundo, **TeamUtilities** repositório, pré-requisitos é opcional, dependendo se a sua equipa tem um repositório de utilitário específicos da equipa. Se qualquer um dos outros três pré-requisitos não foi concluída, contacte a antecedência de equipa, fabrico seu projeto ou os delegados configurá-lo ao seguir as instruções para [tarefas equipa levar para uma equipa de ciência de dados](team-lead-tasks.md) ou para [ Tarefas de antecedência para uma equipa de ciência de dados do projeto](project-lead-tasks.md).

- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), Git tiver sido previamente instalado e estiver pronto para continuar. Caso contrário, consulte o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **Windows DSVM**, tem de ter [Gestor de credenciais de Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo até o **transferir e instalar** secção e clique em de *instalador mais recente*. Isto leva-o para a página de instalador mais recente. Transferir o instalador .exe aqui e executá-la. 
- Se estiver a utilizar **Linux DSVM**, crie uma chave pública SSH no seu DSVM e adicioná-la ao seu servidor VSTS de grupo. Para obter mais informações sobre o SSH, consulte o **criar chave pública SSH** secção o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 
- Se a equipa de e/ou projeto antecedência tiver criado algumas file storage do Azure que precisa para montar a DSVM, deve obter as informações do armazenamento de ficheiros do Azure dos mesmos. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Passo 1-3: clonar grupo, a equipa e repositórios de projeto no computador local

Esta secção fornece instruções sobre a concluir três primeiros tarefas contribuintes individuais do projeto: 

- Clone o **GroupUtilities** repositório R2 para D2
- Clone o **TeamUtilities** repositório R4 para D4 
- Clone o **projeto** repositório R5 para D5.

No seu computador local, crie um diretório ***C:\GitRepos*** (para Windows) ou ***$home/GitRepos*** (forLinux) e, em seguida, altere a esse diretório. 

Execute um dos seguintes comandos (conforme adequado para o SO) para clonar o **GroupUtilities**, **TeamUtilities**, e **projeto** repositórios de diretórios no seu computador local: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Confirme que vê as três pastas sob o diretório de projeto.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Confirme que vê as três pastas sob o diretório de projeto.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Passo 4-5: armazenamento de ficheiros do Azure de montagem ao seu DSVM (opcional)

Para armazenamento de ficheiros do Azure de montagem ao seu DSVM, consulte as instruções na secção 4 a [tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md)

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para as descrições das funções e tarefas definidas pelo processo de ciência de dados de equipa mais detalhadas:

- [Grupo do Gestor de tarefas para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de fabrico de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuintes individuais de projeto para uma equipa de ciência de dados](project-ic-tasks.md)

