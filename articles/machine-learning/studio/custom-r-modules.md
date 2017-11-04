---
title: "Autor dos módulos de personalizado R no Azure Machine Learning | Microsoft Docs"
description: "Início rápido para a criação dos módulos R personalizados no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 03/24/2017
ms.author: bradsev;ankarlof
ms.openlocfilehash: cf3f0e79a9f873a57ef6b7f5233d324faee3e017
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Criar módulos R personalizados no Azure Machine Learning
Este tópico descreve como criar e implementar um módulo R personalizado no Azure Machine Learning. Explica o que são dos módulos R personalizados e que os ficheiros que são utilizados para defini-las. Ilustra como construir os ficheiros que definem um módulo e como registar o módulo para a implementação numa área de trabalho do Machine Learning. Os elementos e atributos utilizados na definição do módulo personalizado, em seguida, são descritos em mais detalhe. Como utilizar a funcionalidade auxiliar e de ficheiros e de várias saídas também é abordado. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>O que é um módulo R personalizado?
A **módulo personalizado** é um módulo definido pelo utilizador que pode ser carregado para a sua área de trabalho e executado como parte de uma experimentação do Azure Machine Learning. A **módulo R personalizado** é um módulo personalizado que executa uma função de R definida pelo utilizador. **R** é uma linguagem de programação para análises de computação e gráficos que é amplamente utilizados pela cientistas de dados e statisticians para implementar algoritmos. Atualmente, o R é o único idioma suportado em módulos personalizados, mas o suporte para idiomas adicionais está agendada para versões futuras.

Tem de módulos personalizados **estado de primeira classe** no Azure Machine Learning no sentido que podem ser utilizados tal como qualquer outro módulo. Estes podem ser executados com outros módulos, incluídos nas experimentações publicadas ou em visualizações. Tem controlo sobre o algoritmo implementado pelo módulo, a entrada e portas de saída que serão utilizadas, os parâmetros de modelação e noutros comportamentos de tempo de execução várias. Uma experimentação que contém módulos personalizados também pode ser publicada na galeria da Cortana Intelligence para a partilha fácil.

## <a name="files-in-a-custom-r-module"></a>Ficheiros de um módulo R personalizado
Um módulo R personalizado é definido por um ficheiro. zip que contém, no mínimo, dois ficheiros:

* A **ficheiro de origem** que implementa a função de R exposta pelo módulo
* Um **ficheiro de definição de XML** que descreve a interface de módulo personalizado

Ficheiros auxiliares adicionais também podem ser incluídos no ficheiro. zip que fornece uma funcionalidade que pode ser acedida a partir do módulo personalizado. Esta opção é abordada no **argumentos** fazem parte da secção de referência **elementos no ficheiro de definição de XML** seguinte o exemplo de início rápido.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Exemplo de início rápido: definir, o pacote e registar um módulo R personalizado
Este exemplo ilustra como construir os ficheiros necessários a um módulo R personalizado, empacotá-las para um ficheiro zip e, em seguida, registe o módulo na sua área de trabalho do Machine Learning. Os ficheiros de pacote e um exemplo de zip do exemplo podem ser transferidos do [CustomAddRows.zip transferir ficheiro](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>O ficheiro de origem
Considere o exemplo de um **personalizada adicionar linhas** módulo que modifica a implementação padrão da **adicionar linhas** módulo utilizado para concatenar linhas (as observações) a partir de dois conjuntos de dados (pacotes de dados). A norma **adicionar linhas** módulo acrescenta as linhas do segundo conjunto de dados de entrada para o fim do conjunto de dados de entrada do primeiro, utilizando o `rbind` algoritmo. O personalizado `CustomAddRows` função da mesma forma aceita dois conjuntos de dados, mas também aceita um parâmetro de comutação booleano como entrada adicional. Se o parâmetro de comutação é definido como **falso**, devolve o mesmo conjunto de dados como a implementação padrão. Porém, se o parâmetro de comutação é **verdadeiro**, a função acrescenta linhas do primeiro conjunto de dados de entrada para o fim do segundo conjunto de dados em vez disso. O ficheiro de CustomAddRows.R que contém a implementação do R `CustomAddRows` função exposta pelo **personalizada adicionar linhas** módulo tem o seguinte código do R.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>O ficheiro de definição de XML
Para expor este `CustomAddRows` funcione como um módulo do Azure Machine Learning, um ficheiro de definição de XML tem de ser criada para especificar como o **personalizada adicionar linhas** módulo deve ter o aspeto e comportam-se. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


É fundamental ter em atenção que o valor da **id** atributos do **entrada** e **Arg** elementos no ficheiro XML têm de corresponder EXATAMENTE os nomes de parâmetro de função do código R no ficheiro CustomAddRows.R: (*dataset1*, *dataset2*, e *troca* no exemplo). Da mesma forma, o valor da **entryPoint** atributo o **idioma** elemento tem de corresponder ao nome da função do R script EXATAMENTE: (*CustomAddRows* no exemplo). 

Em contrapartida, o **id** atributo para o **saída** elemento não corresponde a quaisquer variáveis do R script. Quando for necessária mais do que uma saída, basta devolver uma lista na função de R com resultados colocados *pela mesma ordem* como **saídas** elementos declarados no ficheiro XML.

### <a name="package-and-register-the-module"></a>O pacote e registar o módulo
Guarde estes dois ficheiros como *CustomAddRows.R* e *CustomAddRows.xml* e, em seguida, zip em conjunto os dois ficheiros para um *CustomAddRows.zip* ficheiro.

Para registá-los na sua área de trabalho do Machine Learning, aceda à sua área de trabalho no Machine Learning Studio, clique em de **+ novo** botão na parte inferior e escolha **módulo -> a partir do pacote ZIP** para carregar a nova **personalizada adicionar linhas** módulo.

![Carregar Zip](./media/custom-r-modules/upload-from-zip-package.png)

O **personalizada adicionar linhas** módulo está agora pronto para ser acedido por das suas experimentações de Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementos no ficheiro de definição de XML
### <a name="module-elements"></a>Elementos de módulo
O **módulo** elemento é utilizado para definir um módulo personalizado no ficheiro XML. Vários módulos podem ser definidos num ficheiro XML com vários **módulo** elementos. Cada módulo na sua área de trabalho tem de ter um nome exclusivo. Registar um módulo personalizado com o mesmo nome que um módulo personalizado existente e substitui o módulo existente pelo novo. Módulos personalizados no entanto, podem ser registado com o mesmo nome que um módulo existente do Azure Machine Learning. Se Sim, estas aparecem no **personalizada** categoria de paleta do módulo.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Dentro do **módulo** elemento, pode especificar dois elementos opcionais adicionais:

* um **proprietário** elemento que está incorporado para o módulo  
* um **Descrição** elemento que contém o texto que é apresentado na Ajuda rápida para o módulo e quando paira o rato sobre o módulo no Machine Learning IU.

Regras para limites de carateres dos elementos do módulo de:

* O valor da **nome** atributo no **módulo** elemento não pode exceder 64 carateres de comprimento. 
* O conteúdo a **Descrição** elemento não pode exceder 128 carateres de comprimento.
* O conteúdo a **proprietário** elemento não pode exceder 32 carateres de comprimento.

Resultados de um módulo podem ser determinísticos ou nondeterministic.* * por predefinição, todos os módulos são considerados como sendo determinística. Ou seja, determinado um unchanging conjunto de dados e os parâmetros de entrada, o módulo deve devolver o mesmo eacRAND de resultados ou uma hora de functionh que for executada. Este comportamento, Azure Machine Learning Studio volta a executar apenas módulos marcados como determinista se um parâmetro ou os dados de entrada foi alterada. Devolver os resultados em cache igualmente muito mais rápida execução de experimentações.

Existem funções que são não determinístico, como RAND ou uma função que devolve a data atual ou hora. Se o módulo utiliza uma função não determinístico, pode especificar que o módulo não é determinística definindo a opção **isDeterministic** atributo para **falso**. Desta forma, assegura que o módulo será novamente executado sempre que executa a experimentação, mesmo que os parâmetros de entrada do módulo e não foram alteradas. 

### <a name="language-definition"></a>Definição de idioma
O **idioma** elemento no ficheiro de definição de XML é utilizado para especificar o idioma de módulo personalizado. Atualmente, o R é o único idioma suportado. O valor da **sourceFile** atributo deve ser o nome do ficheiro de R que contém a função a chamar quando o módulo está a ser executado. Este ficheiro tem de fazer parte do pacote zip. O valor da **entryPoint** atributo é o nome da função a ser chamado e tem de coincidir com uma função válida definida com no ficheiro de origem.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portas
As portas de entrada e de saída para um módulo personalizado estão especificadas na elementos subordinados do **portas** secção do ficheiro de definição de XML. A ordem destes elementos determina o esquema experiente (UX) pelos utilizadores. O primeiro subordinado **entrada** ou **saída** listado no **portas** elemento do ficheiro XML torna-se a porta de entrada de mais à esquerda UX. de aprendizagem máquina
Cada entrada e saída de porta poderá ter opcional **Descrição** elemento subordinado que especifica o texto apresentado quando paira o rato o cursor do rato sobre a porta na IU de aprendizagem máquina.

**Portas regras**:

* Número máximo de **portas de entrada e de saída** é de 8 para cada.

### <a name="input-elements"></a>Elementos de entrada
Portas de entrada permitem-lhe transmitir dados para a função de R e área de trabalho. O **tipos de dados** que são suportados para portas de entrada são as seguintes: 

**DataTable:** deste tipo é transmitido para a função de R como um data.frame. Na realidade, todos os tipos (por exemplo, ficheiros CSV ou ficheiros ARFF) que são suportados pelo Machine Learning e que são compatíveis com **DataTable** são convertidos num data.frame automaticamente. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

O **id** atributo associado a cada **DataTable** porta de entrada tem de ter um valor exclusivo e este valor tem de corresponder ao seu correspondente com o nome do parâmetro na sua função de R.
Opcional **DataTable** portas que não são transmitidas como entrada numa experiência de ter o valor **nulo** transmitido para a função de R e zip opcional portas são ignoradas se a entrada não está ligada. O **isOptional** atributo é opcional para ambos os **DataTable** e **Zip** tipos e é *falso* por predefinição.

**Zip:** módulos personalizados, podem aceitar um ficheiro zip como entrada. Esta entrada é descompactada para o diretório de trabalho de R da sua função

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Dos módulos R personalizados, o id de uma porta de Zip não tem de corresponder ao quaisquer parâmetros da função de R. Isto acontece porque o ficheiro zip é extraído automaticamente para o diretório de trabalho do R.

**Regras de entrada:**

* O valor da **id** atributo o **entrada** elemento tem de ser um nome de variável de R válido.
* O valor da **id** atributo o **entrada** elemento não pode ser superior a 64 carateres.
* O valor da **nome** atributo o **entrada** elemento não pode ser superior a 64 carateres.
* O conteúdo a **Descrição** elemento não pode conter mais de 128 carateres
* O valor da **tipo** atributo o **entrada** elemento tem de ser *Zip* ou *DataTable*.
* O valor da **isOptional** atributo do **entrada** elemento não é necessário (e é *falso* por predefinição, quando não especificado); mas se for especificado, tem de ser *verdadeiro* ou *falso*.

### <a name="output-elements"></a>Elementos de saída
**Portas de saída padrão:** portas de saída estão mapeadas para os valores de retorno da sua função de R, o que, em seguida, pode ser utilizada por módulos subsequentes. *DataTable* é o tipo de porta de saída padrão apenas suportado atualmente. (Suporte para *Learners* e *transforma* é lançamento.) A *DataTable* saída está definida como:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Para saídas dos módulos R personalizados, o valor da **id** atributo não tem nada no R script correspondem, mas tem de ser exclusivo. Para uma saída de módulo único, o valor de retorno da função de R tem de ser um *data.frame*. Para mais do que um objeto de um tipo de dados suportados de saída, as portas de saída adequado tem de ser especificado no ficheiro XML de definição e os objetos têm de ser devolvido como uma lista. Os objetos de resultado são atribuídos a portas da esquerda para a direita, ao refletir a ordem em que os objetos são colocados na lista devolvida de saída.

Por exemplo, se pretender modificar o **personalizada adicionar linhas** módulo para a saída originais dois conjuntos de dados, *dataset1* e *dataset2*, para além do novo conjunto de dados associado, *conjunto de dados*, (por uma ordem, da esquerda para a direita, como: *conjunto de dados*, *dataset1*, *dataset2*), em seguida, defina as portas de saída no ficheiro CustomAddRows.xml da seguinte forma:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


E devolver a lista de objetos numa lista na ordem correta no 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Saída de visualização:** também pode especificar uma porta de saída do tipo *visualização*, que apresenta o resultado da saída R gráficos dispositivo e a consola. Esta porta não faz parte do resultado da função de R e não interfere com a ordem dos outros tipos de porta de saída. Para adicionar uma porta de visualização para os módulos personalizados, adicione um **saída** elemento com um valor de *visualização* para o respetivo **tipo** atributo:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Regras de saída:**

* O valor da **id** atributo o **saída** elemento tem de ser um nome de variável de R válido.
* O valor da **id** atributo o **saída** elemento não pode conter mais de 32 carateres.
* O valor da **nome** atributo o **saída** elemento não pode ser superior a 64 carateres.
* O valor da **tipo** atributo o **saída** elemento tem de ser *visualização*.

### <a name="arguments"></a>Argumentos
Dados adicionais podem ser transmitidos para a função de R através dos parâmetros do módulo que estão definidos no **argumentos** elemento. Estes parâmetros aparecem no painel de propriedades mais â direita da IU de aprendizagem máquina quando o módulo é selecionado. Argumentos podem ser qualquer um dos tipos suportados ou pode criar uma enumeração personalizada quando for necessário. Semelhante para o **portas** elementos, **argumentos** elementos podem ter opcional **Descrição** elemento que especifica o texto que é apresentado quando paira o rato o rato sobre o nome do parâmetro.
As propriedades opcionais para um módulo, tal como defaultValue, minValue e maxValue podem ser adicionadas a qualquer argumento como atributos para um **propriedades** elemento. Propriedades válidas para o **propriedades** elemento dependem do tipo de argumento e são descritas com os tipos de argumento suportados na secção seguinte. Argumentos com o **isOptional** propriedade definida como **"true"** requer que o utilizador introduza um valor. Se um valor não for fornecido para o argumento, o argumento não é transmitido para a função de ponto de entrada. Os argumentos da função de ponto de entrada que são opcionais precisam de ser explicitamente processados pela função, por exemplo, atribuída um valor predefinido nulo na definição de função de ponto de entrada. Um argumento opcional só irá impor as outras restrições de argumento, ou seja, min ou max, se um valor é fornecido pelo utilizador.
Tal como acontece com entradas e saídas, é extremamente importante que cada um dos parâmetros têm valores de id exclusivo associados aos mesmos. No nosso exemplo de início rápido o parâmetro/id associado foi *troca*.

### <a name="arg-element"></a>Elemento arg
Um parâmetro do módulo é definido utilizando o **Arg** elemento subordinado do **argumentos** secção do ficheiro de definição de XML. Tal como com os elementos subordinados de **portas** secção, a ordenação de parâmetros a **argumentos** secção define o esquema encontrado no UX. Os parâmetros aparecem, desde a parte superior para baixo na IU pela mesma ordem pela qual são definidos no ficheiro XML. Os tipos suportados pelo Machine Learning para os parâmetros estão listados aqui. 

**Int** – um parâmetro de tipo (32-bit) de número inteiro.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **min**, **máximo**, **predefinido** e **isOptional**

**duplo** – um parâmetro do tipo duplo.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **min**, **máximo**, **predefinido** e **isOptional**

**bool** – um parâmetro booleano que é representado por uma caixa de verificação no UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Propriedades opcionais*: **predefinido** -false Se não definida

**cadeia**: uma cadeia padrão

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Propriedades opcionais*: **predefinido** e **isOptional**

**ColumnPicker**: um parâmetro de seleção de coluna. Este tipo compõe no UX como um selector de coluna. O **propriedade** elemento é utilizado aqui para especificar o id da porta partir da qual as colunas são selecionadas, onde o tipo de porta de destino tem de ser *DataTable*. O resultado da seleção de coluna é transmitido para a função de R como uma lista de cadeias que contém os nomes de coluna selecionada. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Propriedades necessárias*: **portId** -corresponde ao id de um elemento de entrada com o tipo *DataTable*.
* *Propriedades opcionais*:
  
  * **allowedTypes** -filtros a coluna tipos a partir da qual pode escolher. Os valores válidos incluem: 
    
    * um valor numérico
    * Valor booleano
    * Categórico
    * Cadeia
    * Etiqueta
    * Funcionalidade
    * Classificação
    * Todos
  * **predefinição** -predefinições válido para o Seletor de coluna incluem: 
    
    * Nenhuma
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Todos

**Pendente**: uma lista de utilizador especificado enumerados (pendente). Os itens de lista pendente são especificados a **propriedades** elemento utilizando um **Item** elemento. O **id** para cada **Item** tem de ser exclusivo e uma variável de R válida. O valor da **nome** de um **Item** funciona como o texto que visualiza e o valor que é transmitido para a função de R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Propriedades opcionais*:
  * **predefinição** -tem de corresponder o valor para a propriedade predefinida com um valor de id de um do **Item** elementos.

### <a name="auxiliary-files"></a>Ficheiros auxiliares
Qualquer ficheiro que é colocado no seu ficheiro ZIP do módulo personalizado vai estar disponível para utilização durante o tempo de execução. As estruturas de diretório presentes são preservadas. Isto significa que o ficheiro sourcing funciona da mesma localmente e na execução do Azure Machine Learning. 

> [!NOTE]
> Tenha em atenção que todos os ficheiros são extraídos para o diretório 'src' pelo que devem ter todos os caminhos ' src /' prefixo.
> 
> 

Por exemplo, imagine que pretende remover quaisquer linhas do conjunto de dados e também de remover quaisquer linhas duplicadas, antes de exportá-lo para CustomAddRows e já escrevemos uma função de R que suporta que num ficheiro RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Pode originar o ficheiro auxiliar RemoveDupNARows.R na função CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Em seguida, carregue um ficheiro zip que contém 'CustomAddRows.R', 'CustomAddRows.xml' e 'RemoveDupNARows.R' como um módulo R personalizado.

## <a name="execution-environment"></a>Ambiente de execução
O ambiente de execução para o script do R utiliza a mesma versão do R como os **executar Script do R** módulo e pode utilizar os mesmos pacotes e predefinido. Também pode adicionar pacotes de R adicionais ao seu módulo personalizado, incluindo-los do pacote zip do módulo personalizado. Apenas carregá-las no R script tal como faria no seu próprio ambiente de R. 

**Limitações do ambiente de execução** incluem:

* Sistema de ficheiros não persistentes: ficheiros escritos ao executar o módulo personalizado não são mantidos em várias execuções do mesmo módulo.
* Sem acesso de rede

