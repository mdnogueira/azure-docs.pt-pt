---
title: "Idiomas para as máquinas de ciência de dados no Azure | Microsoft Docs"
description: "Idiomas para as máquinas de ciência de dados no Azure"
keywords: "ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux"
services: machine-learning
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
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 2f2125e739b738847e03ce429d65801969611685
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Idiomas suportados na máquina de Virtual de ciência de dados 

A Máquina Virtual de ciência de dados (DSVM) inclui vários idiomas previamente concebidos e ferramentas de desenvolvimento para criar as suas aplicações AI. Aqui estão algumas do aqueles salientes. 

## <a name="python"></a>Python

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | 2.7 e 3.5 |
| Edições de DSVM suportados      | Linux, Windows     |
| Como é mesmo configurado / instalado o DSVM?  | Dois global `conda` ambientes são criados. <br /> * `root`ambiente localizado em `/anaconda/` é Python 2.7. <br/> * `py35`ambiente localizado em `/anaconda/envs/py35`é Python 3.5       |
| Ligações para amostras      | Notas do Jupyter de exemplo para o Python estão incluídas     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Leonor      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

**Windows**:

* Executar numa linha de comandos

Abra a linha de comandos e efetue o seguinte, dependendo da versão do Python que pretende executar. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Utilizar um IDE

Utilize as ferramentas do Python para Visual Studio (PTVS) instaladas na edição Visual Studio Community. A configuração de ambiente só automaticamente em PTVS no Python 2.7. 
> [!NOTE]
> Para apontarem das PTVS Python 3.5, terá de criar um ambiente personalizadas no PTVS. Para definir esta caminhos de ambiente a edição de Comunidade do Visual Studio, navegue até à **ferramentas** -> **ferramentas do Python** -> **ambientes do Python** e, em seguida, clique em **+ personalizada**. Em seguida, defina a localização para `c:\anaconda\envs\py35` e, em seguida, clique em _de deteção automática_. 

* Utilizar no Jupyter

Abra o Jupyter e clique em de `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _Python [Conda raiz]_ para Python 2.7 e _Python [Conda env:py35]_ para o ambiente do Python 3.5. 

* Instalar pacotes de Python

Os ambientes do Python predefinido no DSVM são global ambiente legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, ativar para a raiz ou py35 ambiente utilizando o `activate` comando como administrador. Em seguida, pode utilizar o Gestor de pacotes como `conda` ou `pip` para instalar ou atualizar os pacotes. 


**Linux**:

* Em execução no terminal

Abra o terminal e efetue o seguinte, dependendo da versão do Python que pretende executar. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Utilizar um IDE

Utilize PyCharm instalada na edição Visual Studio Community. 

* Utilizar no Jupyter

Abra o Jupyter e clique em de `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _Python [Conda raiz]_ para Python 2.7 e _Python [Conda env:py35]_ para o ambiente do Python 3.5. 

* Instalar pacotes de Python

Os ambientes do Python predefinido no DSVM são globais ambientes legíveis por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, ativar para a raiz ou py35 ambiente utilizando o `source activate` comando como administrador ou um utilizador com permissão de sudo. Em seguida, pode utilizar um Gestor de pacote como `conda` ou `pip` para instalar ou atualizar os pacotes. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | Microsoft R abra 3 (100% compatível com CRAN R<br /> Edição do programador do Microsoft R Server 9.x (Enterprise dimensionável A pronta R plataforma)|
| Edições de DSVM suportados      | Linux, Windows     |
| Como é mesmo configurado / instalado o DSVM?  | Windows:`C:\Program Files\Microsoft\R Server\R_SERVER` <br />Linux:` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Ligações para amostras      | Blocos de notas do Jupyter exemplo de R estão incluídos     |
| Ferramentas relacionadas no DSVM      | Leonor SparkR, Python,      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

**Windows**:

* Executar numa linha de comandos

Abra a linha de comandos e escreva `R`.

* Utilizar um IDE

Utilize RTools para Visual Studio (RTVS) instaladas na edição do Visual Studio Community ou RStudio. Estes estão disponíveis no menu Iniciar ou como um ícone de ambiente de trabalho. 

* Utilizar no Jupyter

Abra o Jupyter e clique em de `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _R_ para utilizar o kernel do Jupyter R (IRKernel). 

* Instalar pacotes de R

R está instalado no DSVM num ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute R utilizando um dos métodos acima. Em seguida, pode executar o Gestor de pacotes de R `install.packages()` para instalar ou atualizar os pacotes. 

**Linux**:

* Em execução no terminal

Abra o terminal e execute apenas `R`.  

* Utilizar um IDE

Utilize RStudio instalado o DSVM Linux.  

* Utilizar no Jupyter

Abra o Jupyter e clique em de `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _R_ para utilizar o kernel do Jupyter R (IRKernel). 

* Instalar pacotes de R

R está instalado no DSVM num ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute R utilizando um dos métodos acima. Em seguida, pode executar o Gestor de pacotes de R `install.packages()` para instalar ou atualizar os pacotes. 


## <a name="julia"></a>Leonor

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | 0.5 |
| Edições de DSVM suportados      | Linux, Windows     |
| Como é mesmo configurado / instalado o DSVM?  | Windows: Uma instalação em`C:\JuliaPro-VERSION`<br /> Linux: Instalado`/opt/JuliaPro-VERSION`    |
| Ligações para amostras      | Notas do Jupyter de exemplo para Leonor estão incluídas     |
| Ferramentas relacionadas no DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

**Windows**:

* Executar numa linha de comandos

Abra a linha de comandos e execute apenas `julia`. 
* Utilizar um IDE

Utilize `Juno` IDE Leonor instalado no DSVM e disponível como um atalho do ambiente de trabalho.

* Utilizar no Jupyter

Abra o Jupyter e clique em de `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como`Julia VERSION` 

* Instalar pacotes de Leonor

A Leonor localização predefinida é um ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute Leonor utilizando um dos métodos acima. Em seguida, pode executar o pacote de Leonor comandos do Gestor de como `Pkg.add()` para instalar ou atualizar os pacotes. 


**Linux**:
* Em execução no terminal.

Abra o terminal e execute apenas `julia`. 
* Utilizar um IDE

Utilize `Juno` IDE Leonor instalado no DSVM e disponível como um atalho de menu de aplicação.

* Utilizar no Jupyter

Abra o Jupyter e clique em de `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como`Julia VERSION` 

* Instalar pacotes de Leonor

A Leonor localização predefinida é um ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute Leonor utilizando um dos métodos acima. Em seguida, pode executar o pacote de Leonor comandos do Gestor de como `Pkg.add()` para instalar ou atualizar os pacotes. 

## <a name="other-languages"></a>Outros idiomas

**C#**: disponível no Windows e acessível através da edição do Visual Studio Community ou num `Developer Command Prompt for Visual Studio` onde apenas pode executar `csc` comando. 

**Java**: OpenJDK não está disponível na edição de Linux e Windows DSVM e conjunto no caminho. Pode escrever `javac` ou `java` comando na linha de comandos do Windows ou na shell de deteção no Linux para utilizar o Java. 

**NODE.js**: node.js não está disponível na edição de Linux e Windows DSVM e conjunto no caminho. Pode escrever `node` ou `npm` comando na linha de comandos do Windows ou na shell de deteção no Linux para aceder a node.js. No Windows, as ferramentas de Node.js para a extensão do Visual Studio está instalado para fornecer um IDE gráfica para desenvolver a sua aplicação node.js. 

**O F #**: disponível no Windows e acessível através da edição do Visual Studio Community ou num `Developer Command Prompt for Visual Studio` onde apenas pode executar `fsc` comando. 


