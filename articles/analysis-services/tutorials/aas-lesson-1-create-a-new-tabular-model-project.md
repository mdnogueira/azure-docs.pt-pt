---
title: "Lição 1 do tutorial do Azure Analysis Services: Criar um projeto de modelo em tabela novo | Microsoft Docs"
description: Descreve como criar um projeto de tutorial novo do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.openlocfilehash: d523e3e103b4c351d01af6f1eb3c396f9a63016a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-1-create-a-tabular-model-project"></a>Lição 1: Criar um projeto de modelo em tabela

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, vai utilizar o SQL Server Data Tools (SSDT) para criar um projeto de modelo em tabela novo no nível de compatibilidade 1400. Depois de o projeto estar criado, pode começar a adicionar dados e a criar o modelo. Esta lição também mostra uma breve introdução ao ambiente de criação de modelos em tabela no SSDT.  
  
Tempo estimado para concluir esta lição: **dez minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico é a primeira lição num tutorial de criação de modelo em tabela. Para concluir esta lição, tem de ter implementados vários pré-requisitos. Para saber mais, veja [Azure Analysis Services - Adventure Works tutorial](../tutorials/aas-adventure-works-tutorial.md) (Azure Analysis Services - Tutorial da Adventure Works).  
  
## <a name="create-a-new-tabular-model-project"></a>Criar um novo projeto de modelo em tabela  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Para criar um novo projeto de modelo em tabela  
  
1.  No SSDT, no menu **Ficheiro**, clique em **Novo** > **Projeto**.  
  
2.  Na caixa de diálogo **Novo Projeto**, expanda **Instalados** > **Business Intelligence** > **Analysis Services** e escolha **Projeto em Tabela do Analysis Services**.  
  
3.  Em **Nome**, escreva **AW Internet Sales** e especifique uma localização para os ficheiros do projeto.  
  
    Por predefinição, o **Nome da Solução** é o mesmo nome do projeto; contudo, pode escrever outro nome para a solução.  
  
4.  Clique em **OK**.  
  
5.  Na caixa de diálogo **Estruturador de modelo em tabela**, selecione **Área de trabalho integrada**.  
  
    A área de trabalho aloja uma base de dados de modelo em tabela com o mesmo nome que o projeto durante a criação do modelo. A área de trabalho integrada significa que o SSDT utiliza uma instância incorporada, eliminando a necessidade de instalar uma instância do servidor do Analysis Service apenas para a criação do modelo.
      
6.  Em **Nível de compatibilidade**, selecione **SQL Server 2017 / Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Se não vir SQL Server 2017 / Azure Analysis Services (1400) na caixa de listagem do Nível de compatibilidade, significa que não está a utilizar a mais recente versão do SQL Server Data Tools. Para obter esta versão, veja [Install SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) (Instalar o SQL Server Data Tools).  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>Compreender o ambiente de criação do modelo em tabela do SSDT  
Agora que já criou um projeto de modelo em tabela novo, vamos aproveitar para explorar o ambiente de criação no SSDT.  
  
Quando o projeto estiver criado, é aberto no SSDT. No lado direito, em **Explorador de Modelos em Tabela**, pode ver uma vista de árvore dos objetos no modelo. Uma vez que ainda não importou dados, as pastas estão vazias. Pode clicar com o botão direito do rato numa pasta de objetos para realizar ações, de forma semelhante à barra de menus. À medida que avança neste tutorial, vai utilizar o Explorador de Modelos em Tabela para navegar pelos diferentes objetos no projeto de modelo.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Clique no separador **Explorador de Soluções**. Aqui, pode ver o ficheiro **Model.bim**. Se não vir a janela do estruturador à esquerda (a janela vazia com o separador Model.bim), no **Explorador de Soluções**, em **AW Internet Sales Project**, faça duplo clique no ficheiro **Model.bim**. O ficheiro Model.bim contém os metadados do seu projeto de modelo. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Clique em **Model.bim**. Na janela **Propriedades**, pode ver as propriedades do modelo, sendo que a mais importante é **Modo DirectQuery**. Esta propriedade especifica se o modelo vai ser implementado no Modo dentro da memória (Desativado) ou no Modo DirectQuery (Ativado). Neste tutorial, o modelo é criado e implementado no Modo dentro da memória.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Quando cria um projeto de modelo, são definidas automaticamente determinadas propriedades do mesmo, de acordo com as definições da Modelação de Dados que podem ser indicadas no menu **Ferramentas** > caixa de diálogo **Opções**. As propriedades Cópia de Segurança de Dados, Retenção de Área de Trabalho e Servidor da Área de Trabalho especificam como e onde é que é feita, retida dentro da memória e compilada a cópia de segurança da base de dados da área de trabalho (a base de dados da criação do modelo). Se for necessário, pode alterar estas definições mais tarde, mas deixemo-las como estão por agora.  

No **Explorador de Soluções**, clique com o botão direito do rato em **AW Internet Sales** (projeto) e clique em **Propriedades**. É apresentada a caixa de diálogo **Páginas de Propriedades de AW Internet Sales**. Algumas destas definições são definidas mais tarde, quando o modelo for implementado.  
  
No momento em que instalou o SSDT, foram adicionados vários itens de menu novos ao ambiente do Visual Studio. Clique no menu **Modelo**. Aqui, pode importar dados, atualizar dados da área de trabalho, navegar no projeto no Excel, criar perspetivas e funções, selecionar a vista do modelo e definir opções de cálculo. Clique no menu **Tabela**. Aqui, pode criar e gerir relações, especificar definições das tabelas de datas, criar partições e editar propriedades da tabela. Se clicar no menu **Coluna**, pode adicionar e eliminar colunas em tabelas, fixar colunas e especificar a ordenação. O SSDT também adiciona alguns botões à barra. O mais útil é a funcionalidade Soma Automática, para criar uma medida de agregação standard para colunas específicas. Outros botões da barra de ferramentas dão acesso rápido a funcionalidades e comandos utilizados frequentemente.  
  
Explore algumas das caixas de diálogo e localizações de várias funcionalidades específicas para a criação de modelos em tabela. Embora alguns itens ainda não estejam ativos, pode ficar com uma ideia clara do ambiente de criação de modelos em tabela.  
  

## <a name="whats-next"></a>Passos seguintes?
[Lição 2: Obter dados (Lesson 2: Get data)](../tutorials/aas-lesson-2-get-data.md).

  
  
  
