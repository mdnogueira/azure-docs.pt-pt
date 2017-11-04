---
title: Campos personalizados no Log Analytics | Microsoft Docs
description: "A funcionalidade de campos personalizados do Log Analytics permite-lhe criar os suas próprias campos pesquisáveis a partir dos dados OMS que adicionar às propriedades de um registo recolhida.  Este artigo descreve o processo de criação de um campo personalizado e fornece instruções detalhadas sobre um evento de exemplo."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
ms.openlocfilehash: 9e02094f155eaade9bc5fb49c4fbb798e546e989
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="custom-fields-in-log-analytics"></a>Campos personalizados na análise de registos
O **campos personalizados** funcionalidade de análise de registos permite-lhe expandir registos existentes no repositório de OMS, adicionando os suas próprias campos pesquisáveis.  Campos personalizados serão preenchidos automaticamente a partir dos dados extraídos de outras propriedades no registo mesmo.

![Descrição geral de campos personalizado](media/log-analytics-custom-fields/overview.png)

Por exemplo, o registo de exemplo abaixo tem dados útil buried na descrição do evento.  Extrair estes dados para propriedades separadas torna a mesma disponível para essas ações, como ordenar e filtrar.

![Botão de procura de registo](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> A pré-visualização, está limitado a 100 campos personalizados na sua área de trabalho.  Este limite será expandido quando esta funcionalidade atinge disponibilidade geral.
> 
> 

## <a name="creating-a-custom-field"></a>Criar um campo personalizado
Quando cria um campo personalizado, análise de registos tem de compreender que dados a utilizar para preencher o respetivo valor.  Utiliza uma tecnologia da Microsoft Research chamado FlashExtract para identificar rapidamente estes dados.  Em vez de exigir que forneça instruções explícitas, análise de registos aprende sobre os dados que pretende extrair dos exemplos que fornecer.

As secções seguintes fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo é uma explicação passo a passo de extração de exemplo.

> [!NOTE]
> O campo personalizado é preenchido registos correspondentes aos critérios especificados são adicionadas para o arquivo de dados do OMS, pelo que só será apresentado nos registos recolhidos depois do campo personalizado é criado.  O campo personalizado não será adicionado para registos que já estão no arquivo de dados quando é criado.
> 
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passo 1 – identificar registos que tem o campo personalizado
O primeiro passo consiste em identificar os registos que obterão o campo personalizado.  Começar com uma [pesquisa de registo padrão](log-analytics-log-searches.md) e, em seguida, selecione um registo para agir como o modelo que ficará a saber de análise de registos.  Quando especificar que pretender extrair dados para um campo personalizado, o **campo extração assistente** é aberto onde validar e refine os critérios.

1. Aceda a **pesquisa registo** e utilizar um [consulta para obter os registos](log-analytics-log-searches.md) que tem o campo personalizado.
2. Selecione um registo de análise de registos irá utilizar para atuar como um modelo para extrair dados para preencher o campo personalizado.  Irá identificar os dados que pretende extrair deste registo e análise de registos utilizará estas informações para determinar a lógica para preencher o campo personalizado para todos os registos semelhantes.
3. Clique no botão à esquerda de uma propriedade de texto do registo e selecione **extrair os campos de**.
4. O **é aberto o Assistente de extração de campo**, e o registo que selecionou é apresentado no **Main exemplo** coluna.  O campo personalizado será definido para esses registos com os mesmos valores nas propriedades que estão selecionadas.  
5. Se a seleção não é exatamente o que pretende, selecione os campos adicionais para restringir os critérios.  Para alterar os valores de campo para os critérios, tem de cancelar e selecione um registo diferente que cumpra os critérios que pretende.

### <a name="step-2---perform-initial-extract"></a>Passo 2 - efetuar extrair inicial.
Assim que identificou os registos que tem o campo personalizado, basta identificar os dados que pretende extrair.  Análise de registos utilizará estas informações para identificar padrões semelhantes nos registos semelhantes.  No passo após este será capaz de validar os resultados e fornecer mais detalhes para a análise de registos utilizar na sua análise.

1. Realce o texto no registo de exemplo que pretende para preencher o campo personalizado.  Em seguida, será apresentada uma caixa de diálogo para fornecer um nome para o campo de e para efetuar o extrair inicial.  Os carateres  **\_CF** automaticamente será anexado.
2. Clique em **extrair** para efetuar uma análise de registos recolhidos.  
3. O **resumo** e **os resultados da pesquisa** secções mostrar os resultados do extrair, pelo que pode inspecionar a precisão.  **Resumo** apresenta os critérios utilizados para identificar os registos e uma contagem de cada um dos valores de dados identificados.  **Resultados da pesquisa** fornece uma lista detalhada dos registos correspondentes aos critérios de.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Passo 3 – verificar a precisão do extrair e criar campos personalizados
Assim que tiver efetuado o extrair inicial, análise de registos irá apresentar os respetivos resultados com base nos dados que já tenham sido recolhidos.  Se os resultados parecer exata, em seguida, pode criar o campo personalizado com nenhum trabalho adicional.  Caso contrário, em seguida, pode refinar os resultados para que a análise de registos pode melhorar a respetiva lógica.

1. Se quaisquer valores no extrair inicial não estiverem corretos, em seguida, clique o **editar** ícone junto a uma incorreta de registo e selecione **modificar este realce** para modificar a seleção.
2. A entrada é copiada para o **exemplos adicionais** secção por baixo do **Main exemplo**.  Pode ajustar o realce aqui para ajudar a compreender a seleção deve efetuou de análise de registos.
3. Clique em **extrair** para utilizar estes novas informações para avaliar todos os registos existentes.  Os resultados podem ser modificados para registos daquele que acabaram modificadas com base nesta nova inteligência.
4. Continue a adicionar as correções até que todos os registos no extrair identificam corretamente os dados para preencher o campo personalizado novo.
5. Clique em **guardar extrair** quando estiver satisfeito com os resultados.  O campo personalizado está agora definido, mas que não irão ser adicionado a quaisquer registos ainda.
6. Aguarde novos registos correspondentes aos critérios especificados a ser recolhido e, em seguida, execute novamente a pesquisa de registo. Novos registos devem ter o campo personalizado.
7. Utilize o campo personalizado, como qualquer outra propriedade registo.  Pode utilizá-la para dados de agregação e de grupo e utilizá-lo, mesmo para produzir insights novo.

## <a name="viewing-custom-fields"></a>Visualizar os campos personalizados
Pode ver uma lista de todos os campos personalizados no seu grupo de gestão do **definições** mosaico do dashboard do OMS.  Selecione **dados** e, em seguida, **campos personalizados** para uma lista de todos os campos personalizados na sua área de trabalho.  

![Campos personalizados](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Remover um campo personalizado
Existem duas formas para remover um campo personalizado.  O primeiro é o **remover** opção para cada campo ao visualizar a lista completa, conforme descrito acima.  O outro método consiste em obter um registo e clique no botão para a esquerda do campo.  O menu tem uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Instruções de exemplo
A secção seguinte explica um exemplo completo de criação de um campo personalizado.  Neste exemplo extrai o nome do serviço em eventos do Windows que indiquem um serviço a alteração de estado.  Isto baseia-se em eventos criados pelo Gestor de controlo de serviços no registo do sistema em computadores Windows.  Se pretender que a seguir neste exemplo, tem de ser [recolha de eventos de informações para o registo do sistema](log-analytics-data-sources-windows-events.md).

Iremos introduza a seguinte consulta para devolver todos os eventos do Gestor de controlo de serviço que têm um ID de evento de 7036 que é o evento que indica um serviço de início ou paragem.

![Consulta](media/log-analytics-custom-fields/query.png)

Vamos, em seguida, selecione qualquer registo com o evento ID 7036.

![Registo de origem](media/log-analytics-custom-fields/source-record.png)

Queremos que o nome do serviço que aparece no **RenderedDescription** propriedade e selecione o botão junto a esta propriedade.

![Extraia campos](media/log-analytics-custom-fields/extract-fields.png)

O **campo extração assistente** é aberto e o **EventLog** e **EventID** campos estão selecionados no **Main exemplo** coluna.  Isto indica que o campo personalizado será definido para os eventos no registo do sistema com um ID de evento de 7036.  Isto é suficiente para que não temos de selecionar quaisquer outros campos.

![Exemplo de principal](media/log-analytics-custom-fields/main-example.png)

Iremos realce o nome do serviço no **RenderedDescription** propriedade e utilize **serviço** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF**.

![Título do campo](media/log-analytics-custom-fields/field-title.png)

Vemos que é identificado corretamente o nome do serviço para alguns registos mas não para outros utilizadores.   O **os resultados da pesquisa** Mostrar parte do nome de para o **adaptador de desempenho de WMI** não foi selecionado.  O **resumo** mostra quatro regista com **DPRMA** serviço incorretamente incluído um word adicional e duas registos identificados **módulos instalador** em vez de **de módulos do Windows Installer**.  

![Resultados da pesquisa](media/log-analytics-custom-fields/search-results-01.png)

Vamos começar a utilizar o **adaptador de desempenho de WMI** registo.  Clicarmos em que o respetivo ícone editar e, em seguida, **modificar este realce**.  

![Modificar o realce](media/log-analytics-custom-fields/modify-highlight.png)

Vamos aumentar o realce para incluir a palavra **WMI** e, em seguida, execute novamente o extrair.  

![Exemplo adicional](media/log-analytics-custom-fields/additional-example-01.png)

É possível ver que as entradas de **adaptador de desempenho de WMI** corrigido, e análise de registos também utilizado essas informações para corrigir os registos para **Windows Installer de módulo**.  É possível ver no **resumo** apesar secção que **DPMRA** ainda não for a ser identificada corretamente.

![Resultados da pesquisa](media/log-analytics-custom-fields/search-results-02.png)

Iremos desloque-se a um registo com o serviço DPMRA e utilizar o mesmo processo para corrigir desse registo.

![Exemplo adicional](media/log-analytics-custom-fields/additional-example-02.png)

 Quando é executada a extração, é possível ver que todos os nossos resultados estão corretos.

![Resultados da pesquisa](media/log-analytics-custom-fields/search-results-03.png)

É possível ver que **Service_CF** é criado, mas é ainda não foram adicionadas quaisquer registos.

![Contagem inicial](media/log-analytics-custom-fields/initial-count.png)

Após algum tempo ter passado, por isso, novos eventos são recolhidos, é possível ver que que o **Service_CF** campo está agora a ser adicionado para registos que correspondem aos nossos critérios.

![Resultados finais](media/log-analytics-custom-fields/final-results.png)

Agora podemos utilizar o campo personalizado, como qualquer outra propriedade de registo.  Para ilustrar isto, iremos criar uma consulta que agrupa pela nova **Service_CF** campo para inspecionar os serviços que são mais ativo.

![Agrupar por consulta](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para criar consultas com campos personalizados para os critérios.
* Monitor [ficheiros de registo personalizado](log-analytics-data-sources-custom-logs.md) que lhe analisar com campos personalizados.

