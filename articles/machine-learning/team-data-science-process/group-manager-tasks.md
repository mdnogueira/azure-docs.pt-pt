---
title: "Tarefas do Gestor de grupo do processo do equipa dados ciência - Azure | Microsoft Docs"
description: "Uma descrição das tarefas de um Gestor de grupo num projeto de equipa de ciência de dados."
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
ms.openlocfilehash: 58cea8b0288469a76dd8c4eb967caa8e87cd3dd7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="group-manager-tasks"></a>Grupo do Gestor de tarefas

Destaques neste tópico as tarefas que é esperado um Gestor de grupo para realizar para hist / respetiva organização de ciência de dados. O objetivo consiste em estabelecer o ambiente de grupo de colaboração que uniformiza no [o processo de ciência de dados de equipa](overview.md) (TDSP). Para uma descrição das funções de pessoal e das respetivas tarefas associadas são processados por uma equipa de ciência de dados uniformizar neste processo, consulte o artigo [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md).

O **Gestor grupo** é o Gestor da unidade de ciência de dados completo numa empresa. Uma unidade de ciência de dados pode ter várias equipas, cada um dos quais está a funcionar em vários projetos de ciência de dados no verticals negócio distintos. Um Gestor de grupo pode delegar as suas tarefas para um substituto, mas as tarefas associadas a função são os mesmos. Existem seis tarefas principais, conforme mostrado no diagrama seguinte:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Iremos descrevem os passos necessários para configurar um ambiente de grupo TDSP utilizando VSTS nas instruções que se seguem. Especificamos como realizar estas tarefas com VSTS, uma vez que é a forma como implementamos TDSP na Microsoft. Se for utilizado outro código de plataforma de alojamento para o grupo, as tarefas que têm de ser concluídos pelo Gestor de grupo, geralmente, não alteram. Mas a forma para concluir estas tarefas vai ser diferentes.

1. Configurar **servidor de serviços de equipa do Visual Studio (VSTS)** para o grupo.
2. Criar um **projeto de equipa do grupo** no servidor do Visual Studio Team Services (para utilizadores VSTS)
3. Criar o **GroupProjectTemplate** repositório
4. Criar o **GroupUtilities** repositório
5. Seed o **GroupProjectTemplate** e **GroupUtilities** repositórios para o servidor VSTS com conteúdo de repositórios do TDSP.
6. Configurar o **controlos de segurança** para os membros da equipa a aceder aos repositórios do GroupProjectTemplate e GroupUtilities.

Cada um dos passos anteriores está descrita em detalhe. Mas, em primeiro lugar, iremos familiarizá-lo com as abreviaturas e abordar os pré-requisitos para trabalhar com os repositórios.

### <a name="abbreviations-for-repositories-and-directories"></a>Abreviaturas para repositórios e diretórios

Este tutorial utiliza nomes abreviados para repositórios e diretórios. Estas definições tornam mais fácil de seguir as operações entre os repositórios e diretórios. Este notation é utilizado nas seguintes secções:

- **VM G1**: O repositório de modelo de projeto desenvolvidas e geridos pela equipa TDSP da Microsoft.
- **VM G2**: O repositório de utilitários desenvolvidas e geridos pela equipa TDSP da Microsoft.
- **R1**: GroupProjectTemplate o repositório no Git configurar no seu servidor de grupo VSTS.
- **R2**: GroupUtilities o repositório no Git configurar no seu servidor de grupo VSTS.
- **LG1** e **LG2**: os diretórios locais no computador que pode clonar G1 e G2, respetivamente.
- **LR1** e **LR2**: os diretórios locais no computador que pode clonar R1 e R2, respetivamente.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Pré-requisitos para clonagem repositórios e código de verificação e terminar
 
- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), Git tiver sido previamente instalado e estiver pronto para continuar. Caso contrário, consulte o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **Windows DSVM**, tem de ter [Gestor de credenciais de Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo até o **transferir e instalar** secção e clique em de *instalador mais recente*. Este passo leva-o para a página de instalador mais recente. Transferir o instalador .exe aqui e executá-la. 
- Se estiver a utilizar **Linux DSVM**, crie uma chave pública SSH no seu DSVM e adicioná-la ao seu servidor VSTS de grupo. Para obter mais informações sobre o SSH, consulte o **criar chave pública SSH** secção o [apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Criar conta no servidor VSTS

O servidor VSTS aloja repositórios do seguintes:

- **grupo repositórios comuns**: repositórios para fins gerais que podem ser adotado uma larga maioria por várias equipas dentro de um grupo para vários projetos de ciência de dados. Por exemplo, o *GroupProjectTemplate* e *GroupUtilities* repositórios.
- **repositórios de equipa**: repositórios para equipas específicas dentro de um grupo. Estes repositórios são específicos para a necessidade de uma equipa e podem ser adotado por vários projetos executados por essa equipa, mas não geral suficiente para ser útil para várias equipas dentro de um grupo de ciência de dados. 
- **repositórios de projeto**: repositórios disponíveis para projetos específicos. Essas repositórios não podem ser gerais para ser útil para vários projetos efetuados por uma equipa e para várias equipas de um grupo de ciência de dados.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Configurar o início de sessão do servidor VSTS na sua conta Microsoft
    
Aceda a [Visual Studio online](https://www.visualstudio.com/), clique em **sessão** no canto superior direito e início de sessão na conta Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Se não tiver uma conta Microsoft, clique em **inscrever-se agora** para criar uma conta Microsoft e, em seguida, inicie sessão com esta conta. 

Se a sua organização tiver uma subscrição do Visual Studio/MSDN, clique na verde **iniciar sessão com a sua conta escolar ou profissional** caixa e inicie sessão com as credenciais associadas esta subscrição. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Depois de iniciar sessão, clique em **criar nova conta** no canto superior direito como mostrado na imagem seguinte:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Preencha as informações para o servidor VSTS que pretende criar o **criar a sua conta** assistente com os seguintes valores: 

- **URL do servidor**: substituir *mysamplegroup* com os seus próprios *nome do servidor*. O URL do seu servidor vai ser: *https://\<servername\>. visualstudio.com*. 
- **Gerir utilizando o código:** selecione  **_Git_**.
- **Nome do projeto:** Enter *GroupCommon*. 
- **Organizar através de trabalho:** escolha *Agile*.
- **Alojar o seus projetos em:** escolha uma localização de georreplicação. Neste exemplo, vamos escolher *Sul Central nos*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Se vir a janela de pop-up seguinte depois de clicar em **criar nova conta**, em seguida, tem de clicar em **alterar detalhes** para apresentar todos os campos descritos.

![5](./media/group-manager-tasks/create-account-2.png)


Clique em **continuar**. 

## <a name="2-groupcommon-team-project"></a>2. Projeto de equipa GroupCommon

O **GroupCommon** página (*https://\<servername\>.visualstudio.com/GroupCommon*) abre-se após o seu servidor VSTS é criado.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Criar repositório GroupUtilities (R2)

Para criar o **GroupUtilities** repositório (R2) no servidor VSTS:

- Para abrir o **criar um novo repositório de** assistente, clique em **novo repositório de** no **controlo de versão** separador do seu projeto de equipa. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Selecione *Git* como o **tipo**e introduza *GroupUtilities* como o **nome**e, em seguida, clique em **criar**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Agora deverá ver dois repositórios de Git **GroupProjectTemplate** e **GroupUtilities** na coluna esquerda do **controlo de versão** página: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Criar repositório GroupProjectTemplate (R1)

O programa de configuração dos repositórios para o servidor de grupo VSTS é composto por duas tarefas:

- Mudar o nome predefinido **GroupCommon** repositório***GroupProjectTemplate***.
- Criar o **GroupUtilities** repositório no servidor VSTS em projeto de equipa **GroupCommon**. 

Instruções para a primeira tarefa estão contidas nesta secção após observações em convenções de nomenclatura ou dos nossos repositórios e diretórios. As instruções para a segunda tarefa estão contidas na secção seguinte para o passo 4.

### <a name="rename-the-default-groupcommon-repository"></a>Mudar o nome do repositório de GroupCommon predefinido

Para mudar o nome predefinido **GroupCommon** repositório como *GroupProjectTemplate* (referida como **R1** neste tutorial):
    
- Clique em **Collaborate no código** no **GroupCommon** página do projeto de equipa. Isto leva-o para a página do repositório de Git predefinida do projeto de equipa **GroupCommon**. Atualmente, este repositório de Git está vazio. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Clique em **GroupCommon** no superior esquerdo canto (realçado com uma caixa vermelha na figura seguinte) na página de repositório de Git do **GroupCommon** e selecione **gerir repositórios**(realçado com uma caixa verde na figura seguinte). Este procedimento aparece o **painel de controlo**. 
- Selecione o **controlo de versão** separador do seu projeto de equipa. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Clique em de **...**  à direita do **GroupCommon** repositório no painel esquerdo e selecione **repositório de mudança de nome**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- No **mudar o nome do repositório GroupCommon** assistente pops cópias de segurança, introduza *GroupProjectTemplate* no **nome do repositório** caixa e, em seguida, clique em **mudar o nome** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Efetuar o seeding repositórios do R1 & R2 no servidor de VSTS

Nesta fase do procedimento, pode efetuar o seeding do *GroupProjectTemplate* (R1) e *GroupUtilities* repositórios (R2) que configurou na secção anterior. Estes repositórios são pré-propagadas com o ***ProjectTemplate*** (**G1**) e ***utilitários*** (**G2**) repositórios que são geridos pelo Microsoft para o processo de ciência de dados de equipa. Quando esta propagação concluída:

- o repositório de R1 vai ter o mesmo conjunto de diretórios e modelos de documento que suporta o VM G1
- o repositório de R2 vai conter o conjunto de utilitários de ciência de dados desenvolvida pela Microsoft.

O procedimento de propagação utiliza os diretórios no seu local DSVM como sites de transição intermédios. Eis os passos seguidos nesta secção:

- VM G1 & G2 - clonado para -> LG1 & LG2
- R1 & R2 - clonado para -> LR1 & LR2
- LG1 & LG2 - ficheiros copiados para -> LR1 & LR2
- (Opcional) personalização LR1 & LR2
- LR1 & LR2 - conteúdo Adicionar -> R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>VM G1 do clone & G2 repositórios para sua DSVM local

Neste passo, clone o repositório do processo de ciência de dados de equipa (TDSP) ProjectTemplate (G1) e utilitários (G2) de repositórios do github TDSP para pastas no seu local DSVM como LG1 e LG2:

- Crie um diretório para servir o diretório de raiz para alojar todos os seus clones dos repositórios do. 
    -  Windows DSVM, criar um diretório *C:\GitRepos\TDSPCommon*. 
    -  Linux DSVM, criar um diretório *GitRepos\TDSPCommon* no seu diretório raiz. 

- Execute o seguinte conjunto de comandos a partir do *GitRepos\TDSPCommon* diretório.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Utilizar nomes nosso repositório abreviado, isto é que estes scripts têm obtido: 
    - VM G1 - clonado em -> LG1
    - VM G2 - clonado em -> LG2
- Após a clonagem é concluída, deverá conseguir ver dois diretórios, _ProjectTemplate_ e _utilitários_, em **GitRepos\TDSPCommon** diretório. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Repositórios de clone R1 & R2 ao seu DSVM local

Neste passo, clone o repositório de GroupProjectTemplate (R1) e GroupUtilities repositório (R2) no locais diretórios (referida como LR1 e LR2, respetivamente) em **GitRepos\GroupCommon** no seu DSVM.

- Para obter os URLs dos repositórios do R1 e R2, aceda ao seu **GroupCommon** home page no VSTS. Geralmente, isto não tem o URL *https://\<o nome do servidor VSTS\>.visualstudio.com/GroupCommon*. 
- Clique em **código**. 
- Escolha o **GroupProjectTemplate** e **GroupUtilities** repositórios. Copie e guarde cada um dos URLs (HTTPS para Windows; SSH para Linux) da **URL do Clone** elemento, por sua vez, para utilização nos scripts seguintes:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Mude para o **GitRepos\GroupCommon** diretório no seu Windows ou Linux DSVM e execute um dos seguintes conjuntos de comandos para clonar R1 e R2 no diretório.
        
Seguem-se os scripts do Windows e Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Espera receber mensagens de aviso que LR1 e LR2 estão vazias.    

- Utilizar nomes nosso repositório abreviado, isto é que estes scripts têm obtido: 
    - R1 - clonado em -> LR1
    - R2 - clonado em -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Efetuar o seeding do GroupProjectTemplate (LR1) e GroupUtilities (LR2)

Em seguida, no seu computador local, copie o conteúdo de diretórios ProjectTemplate e utilitários (exceto os metadados nos diretórios .git) em GitRepos\TDSPCommon para os seus diretórios GroupProjectTemplate e GroupUtilities em **GitRepos\ GroupCommon**. Seguem-se as duas tarefas para concluir este passo:

- Copie os ficheiros no GitRepos\TDSPCommon\ProjectTemplate (**LG1**) para GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Copie os ficheiros no GitRepos\TDSPCommon\Utilities (**LG2** para GitRepos\GroupCommon\Utilities (**LR2**). 

Para alcançar estas duas tarefas, execute os scripts seguintes na consola do PowerShell (Windows) ou a consola de script de Shell (Linux). Lhe for pedido para os caminhos de completos para LG1, LR1, LG2 e LR2 de entrada. Os caminhos de entrada são validados. Se introduzir um diretório que não existe, é-lhe pedido para introduzi-la novamente. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Agora, pode ver que os ficheiros em diretórios LG1 e LG1 (exceto os ficheiros no diretório .git) foram copiados para LR1 e LR2, respetivamente.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Agora, pode ver que os ficheiros em duas pastas (exceto os ficheiros no diretório .git) são copiados para GroupProjectTemplate e GroupUtilities, respetivamente.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Utilizar nomes nosso repositório abreviado, isto é que estes scripts têm obtido:
    - LG1 - ficheiros copiados para LR1 ->
    - LG2 - ficheiros copiados para LR2 ->

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opção para personalizar os conteúdos do LR1 & LR2
    
Se pretender personalizar os conteúdos de LR1 e LR2 para satisfazer as necessidades específicas do seu grupo, esta é a fase do procedimento em que é adequado. Pode modificar os documentos de modelo, alterar a estrutura de diretórios e adicionar utilitários existentes que desenvolveu o grupo ou que são úteis para o grupo de todo. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Adicione o conteúdo no LR1 & LR2 R1 & R2 no servidor de grupo

Agora, terá de adicionar o conteúdo no LR1 e LR2 repositórios R1 e R2. Seguem-se o git comandos bash, que pode executar no Windows PowerShell ou Linux. 

Execute os seguintes comandos a partir do diretório GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

A opção -m permite-lhe definir uma mensagem para a consolidação de git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Pode ver se no servidor VSTS do grupo, no repositório GroupProjectTemplate, os ficheiros são sincronizados de forma instantânea.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Por fim, mude para o **GitRepos\GroupCommon\GroupUtilities** diretório e execute o mesmo conjunto de git bash comandos:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Se esta for a primeira vez, aceita um repositório de Git, terá de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Execute os seguintes dois comandos:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Se são consolidar para vários repositórios de Git, utilize o mesmo nome e endereço de correio eletrónico ao consolidar a cada um deles. Utilizar o mesmo nome e endereço de correio eletrónico comprova conveniente mais tarde quando criar dashboards do Power BI para controlar as atividades de Git em repositórios de vários.


- Utilizar nomes nosso repositório abreviado, isto é que estes scripts têm obtido:
    - Adicionar de conteúdo LR1 - para -> R1
    - Adicionar de conteúdo LR2 - para -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Adicionar membros do grupo para o servidor de grupo

Na home page do grupo VSTS do seu servidor, clique em de **engrenagem ícone** junto ao seu nome de utilizador no canto superior direito, em seguida, selecione o **segurança** separador. Pode adicionar membros ao seu grupo com permissões vários.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para as descrições das funções e tarefas definidas pelo processo de ciência de dados de equipa mais detalhadas:

- [Grupo do Gestor de tarefas para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de fabrico da equipa de uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de fabrico de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuintes individuais de projeto para uma equipa de ciência de dados](project-ic-tasks.md)