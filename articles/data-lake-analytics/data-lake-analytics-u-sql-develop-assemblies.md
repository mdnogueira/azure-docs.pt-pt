---
title: Desenvolver assemblagens de U-SQL para tarefas do Azure Data Lake Analytics | Microsoft Docs
description: 'Saiba como desenvolver assemblagens a ser utilizada e reutilizada em tarefas de Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Desenvolver assemblagens de U-SQL para tarefas do Azure Data Lake Analytics
Saiba como ativar o code-behind para assemblagens a ser utilizada e reutilizada em tarefas de Data Lake Analytics. 

U-SQL torna mais fácil adicionar o seu próprio código personalizado no linguagens .net, como c#, VB.Net ou F #. Ainda pode implementar o seu próprio tempo de execução para suportar outros idiomas.

É a forma mais fácil de utilizar o código personalizado para utilizar as ferramentas do Data Lake para funcionalidades do Visual Studio code-behind. Para obter mais informações, consulte [Tutorial: desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Existem algumas desvantagens da utilização do code-behind:

- O código fonte obtém carregado para cada submissão de script.
- por detrás do código não podem ser partilhada com outros trabalhos.

Para resolver estas desvantagens, pode ativar por detrás do código em assemblagens e registar as assemblagens ao catálogo de Data Lake Analytics.

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2017, o Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado
* SDK do Microsoft Azure para .NET versão 2.5 ou superior.  Instalá-lo utilizando o Visual Studio Installer ou instalador de plataforma Web
* Uma conta de Data Lake Analytics.  Veja [Get Started with Azure Data Lake Analytics using Azure portal (Introdução à Análise do Azure Data Lake com o portal do Azure)](data-lake-analytics-get-started-portal.md).
* Avance o [introdução ao Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md) tutorial.
* Ligar ao Azure.
* Carregar os dados de origem, consulte [introdução ao Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Desenvolver assemblagens U-SQL

**Para criar e submeter uma tarefa de U-SQL**

1. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**.
2. Expanda **instalada**, **modelos**, **do Azure Data Lake**, **U-SQL(ADLA)**, selecione o **biblioteca de classes (para a aplicação U-SQL)** modelo e, em seguida, clique em **OK**.
3. Escreva o código na class1. cs.  Segue-se um exemplo de código.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Clique em de **criar** e, em seguida, clique em **compilar solução** para criar a dll.

## <a name="register-assemblies"></a>Registar assemblagens

Consulte [utilize Data Lake Analytics(U-SQL) catálogo](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Utilize as assemblagens

Consulte [utilizar o Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Consultar também
* [Introdução ao Data Lake Analytics com o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Utilizar as ferramentas do Data Lake para Visual Studio para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Catálogo de Analytics(U-SQL) utilize Data Lake](data-lake-analytics-use-u-sql-catalog.md)
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)