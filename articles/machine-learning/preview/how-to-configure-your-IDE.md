---
title: Como configurar o Azure Machine Learning Workbench para trabalhar com um IDE?  | Microsoft Docs
description: Um guia para configurar o Azure Machine Learning Workbench para trabalhar com o seu IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 4e18a413a0559b1ddebecf1b29722d21ef35c337
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Como configurar o Azure Machine Learning Workbench para trabalhar com um IDE 

Azure Workbench de aprendizagem máquina pode ser configurado para trabalhar com populares Python IDEs (ambiente de desenvolvimento integrado). Permite uma experiência de desenvolvimento de ciência de dados uniforme mover entre a preparação de dados, criação de código, execução e o controlo operationalization. Atualmente as IDEs suportados são:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Configurar o workbench
1. Clique em de **ficheiro** menu na parte superior esquerda canto da aplicação. 
2. Selecione o **configurar o projeto IDE** opção do flyout 
3. Escreva `VS Code` ou `PyCharm` no **nome** campo (o nome é arbitrário)
4. Introduza a localização para o executável IDE (completo com o nome executável e extensão) no **caminho de execução**

### <a name="default-install-path-for-visual-studio-code"></a>Caminho de instalação predefinido para o Visual Studio Code  

* Windows de 32 bits-`C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows de 64 bits-`C:\Program Files\Microsoft VS Code\Code.exe`
* macOS - selecione o caminho. App, por exemplo `/Applications/Visual Studio Code.app`, e a aplicação acrescenta o resto do caminho para si. O caminho completo para o executável por predefinição é `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Se tiver executado o `Shell Command: Install 'code' command in PATH` comando no VS Code, em seguida, também pode referenciar o script de código de VS em`/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Caminho de instalação predefinido para PyCharm 

* Windows de 32 bits - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows de 64 bits - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS - selecione o caminho de App, por exemplo "aplicações/PyCharm CE.app" e a aplicação acrescenta o resto do caminho para si. O caminho completo para o executável por predefinição é `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Também pode encontrar PyCharm na pasta bin,`/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Abrir projeto no IDE 
Assim que a configuração estiver concluída, pode abrir um projeto do Azure Machine Learning, abrindo o **ficheiro** menu no Azure Machine Learning Workbench, em seguida, clique em **abrir projeto (< IDE_Name >)**. Esta ação abre o projeto de Active Directory atual no IDE configurado. _Nota: Se não estiver num projeto, a **abrir projeto (< IDE_Name >)** será desativada._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Configurar o terminal integrado no Visual Studio Code

### <a name="windows"></a>Windows 
Iremos tenham substituído a shell predefinida ser cmd em vez do PowerShell. Ao clicar no **abrir projeto (< IDE_Name >)**, verá uma linha de comandos: 

_Pode permitir a shell: `C:\windows\System32\cmd.exe` (definido como uma definição de área de trabalho) para ser iniciada no terminal?_

Resposta `yes` para permitir a configuração de shell para trabalhar de forma totalmente integrada com a interface de linha de comandos do Azure ML Workbench.

### <a name="mac"></a>Mac
Para executar um `az` comando utilizando o Visual Studio Code integrado terminal no Mac, tem de definir manualmente as `PATH` para ser o mesmo valor que `PATH` no projeto de `.vscode/settings.json` ficheiro sob a chave `terminal.integrated.env.osx`. Pode fazê-lo executando o comando seguinte no terminal:`PATH=<PATH in .vscode/settings>`
