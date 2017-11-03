---
title: "Gerir funções de base de dados e os utilizadores no Azure Analysis Services | Microsoft Docs"
description: "Saiba como gerir funções de base de dados e os utilizadores num servidor do Analysis Services no Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 53e946bba6bbd882e78b51ee8d222ab0d3ec056a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-database-roles-and-users"></a>Gerir utilizadores e funções de base de dados

O nível de base de dados do modelo, todos os utilizadores têm de pertencer a uma função. Funções definem os utilizadores com permissões específicas para a base de dados do modelo. Qualquer utilizador ou grupo de segurança adicionado a uma função, tem de ter uma conta num inquilino do Azure AD na mesma subscrição que o servidor.

Como definir funções é diferente consoante a ferramenta que utilizar, mas o efeito é o mesmo.

Permissões de função incluem:
*  **Administrador** -os utilizadores têm permissões completas da base de dados. Funções de base de dados com permissões de administrador são diferentes dos administradores de servidores.
*  **Processo** -os utilizadores podem ligar e efetuar operações de processo na base de dados e analisar dados de base de dados do modelo.
*  **Leitura** -os utilizadores podem utilizar uma aplicação de cliente para ligar a e analisar dados de base de dados do modelo.

Ao criar um projeto de modelo de tabela, pode criar funções e adiciona utilizadores ou grupos para essas funções utilizando o Gestor de funções no SSDT. Quando implementada para um servidor, utilize o SSMS, [cmdlets do PowerShell do Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx), ou [a linguagem de Scripting de modelo em tabela](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) para adicionar ou remover funções e membros de utilizador.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Para adicionar ou gerir funções e os utilizadores no SSDT  
  
1.  No SSDT > **Explorador de modelo em tabela**, faça duplo clique **funções**.  
  
2.  No **Gestor de Funções**, clique em **Nova**.  
  
3.  Escreva um nome para a função.  
  
     Por predefinição, o nome da função predefinida é incrementalmente numerado para cada função de novo. Recomenda-se que escreva um nome que identifica o tipo de membro, por exemplo, gestores de finanças ou especialistas de recursos humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Descrição|  
    |----------------|-----------------|  
    |**Nenhum**|Os membros não é possível modificar o esquema do modelo e não é possível consultar dados.|  
    |**Leitura**|Os membros podem consultar dados (com base em filtros de linha), mas não é possível modificar o esquema do modelo.|  
    |**Leitura e de processo**|Os membros podem consultar dados (com base em ao nível da linha filtros) e executadas operações de processo e processar tudo, mas não é possível modificar o esquema do modelo.|  
    |**Processo**|Operações de processo e todos os processos podem ser executada por membros. Não é possível modificar o esquema do modelo e não é possível consultar dados.|  
    |**Administrador**|Os membros podem modificar o esquema do modelo e consultar todos os dados.|   
  
5.  Se a função estiver a criar tem de leitura ou permissão de leitura e o processo, pode adicionar filtros de linha, utilizando uma fórmula DAX. Clique em de **filtros de linha** separador, em seguida, selecione uma tabela, em seguida, clique em de **DAX filtro** campo e, em seguida, escreva uma fórmula DAX.
  
6.  Clique em **membros** > **adicionar externo**.  
  
8.  No **Adicionar membro externo**, introduza utilizadores ou grupos no seu inquilino do Azure AD, o endereço de correio eletrónico. Depois de clicar em OK e fechar o Gestor de funções, funções e os membros da função de aparecem no Explorador de modelo em tabela. 
 
     ![Funções e os utilizadores no Explorador de modelo em tabela](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implemente o servidor de Analysis Services do Azure.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para adicionar ou gerir funções e os utilizadores no SSMS
Para adicionar funções e os utilizadores a uma base de dados do modelo implementado, tem de estar ligado ao servidor como um administrador do servidor ou já se encontra numa função de base de dados com permissões de administrador.

1. No objeto Exporer, faça duplo clique **funções** > **nova função**.

2. No **criar função**, introduza um nome de função e a descrição.

3. Selecione uma permissão.
   |Permissão|Descrição|  
   |----------------|-----------------|  
   |**Controlo total (administrador)**|Os membros podem modificar o esquema do modelo, processamento e pode consultar todos os dados.| 
   |**Base de dados do processo**|Operações de processo e todos os processos podem ser executada por membros. Não é possível modificar o esquema do modelo e não é possível consultar dados.|  
   |**Leitura**|Os membros podem consultar dados (com base em filtros de linha), mas não é possível modificar o esquema do modelo.|  
  
4. Clique em **associação**, em seguida, introduza um utilizador ou grupo no seu inquilino do Azure AD por endereço de correio eletrónico.

     ![Adicionar utilizador](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se a função que está a criar tem permissão de leitura, pode adicionar filtros de linha, utilizando uma fórmula DAX. Clique em **filtros de linha**, selecione uma tabela e, em seguida, escreva uma fórmula DAX no **DAX filtro** campo. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e os utilizadores utilizando um script TMSL
Pode executar um script TMSL na janela de XMLA no SSMS ou utilizando o PowerShell. Utilize o [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) comando e o [funções](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objeto.

**Exemplo de script TMSL**

Neste exemplo, um utilizador externo B2B e um grupo são adicionados à função do analista com permissões de leitura para a base de dados SalesBI. O utilizador externo e o grupo devem estar no mesmo inquilino do Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para adicionar funções e os utilizadores com o PowerShell
O [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) módulo fornece cmdlets de gestão de base de dados específicas de tarefas e o cmdlet Invoke-ASCmd para fins gerais que aceita uma consulta da linguagem de Scripting de modelo em tabela (TMSL) ou script. Os cmdlets seguintes são utilizados para gerir funções de base de dados e utilizadores.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[RoleMember adicionar](https://msdn.microsoft.com/library/hh510167.aspx)|Adicione um membro a uma função de base de dados.| 
|[Remover RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Remova um membro de uma função de base de dados.|   
|[ASCmd invocar](https://msdn.microsoft.com/library/hh479579.aspx)|Execute um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  
Filtros de linha definem as linhas numa tabela podem ser consultadas por membros da função específica. Filtros de linha são definidos para cada tabela de um modelo através da utilização de fórmulas DAX.  
  
Filtros de linha podem ser definidos apenas para leitura e de leitura e permissões do processo. Por predefinição, se um filtro de linha não está definido para uma tabela específica, os membros podem consultar todas as linhas na tabela, exceto se a filtragem cruzada aplica-se de outra tabela.
  
 Filtros de linha necessitam de uma fórmula DAX, que tem de devolver um valor TRUE/FALSE, para definir as linhas que podem ser consultadas por membros da função específica. Não não possível consultar as linhas não incluídas na fórmula DAX. Por exemplo, a tabela de clientes com a linha seguinte filtra expressão, *= clientes [País] = "EUA"*, membros da função de vendas só podem ver os clientes em o EUA.  
  
Aplicam filtros de linha para as linhas especificadas e as linhas relacionadas. Quando uma tabela tem várias relações, filtros aplicam-se em segurança para a relação está ativa. Filtros de linha são intersected com outros filers da linha definidas para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Região|= Região [País] = "EUA"|  
|ProductCategory|= ProductCategory [nome] = "Bicycles"|  
|Transações|= Transações [ano] = 2016|  
  
 O efeito net é membros podem consultar as linhas de dados em que o cliente está em a EUA, a categoria de produto é bicycles, não sendo ano 2016. Os utilizadores não podem consultar transações fora EUA, transações que não sejam bicycles ou transações não 2016, a menos que sejam membros de outra função que concede as permissões.
  
 Pode utilizar o filtro *=FALSE()*, para negar o acesso a todas as linhas de uma tabela inteira.

## <a name="next-steps"></a>Passos seguintes
  [Gerir administradores de servidor](analysis-services-server-admins.md)   
  [Gerir serviços de análise do Azure com o PowerShell](analysis-services-powershell.md)  
  [Modelo de tabela Scripting referência de linguagem (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

