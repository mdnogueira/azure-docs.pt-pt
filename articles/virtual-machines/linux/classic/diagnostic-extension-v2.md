---
title: "Monitorização de uma VM com Linux com uma extensão VM | Microsoft Docs"
description: "Saiba como utilizar a extensão de diagnóstico do Linux para monitorizar o desempenho e dados de diagnóstico de uma VM com Linux no Azure."
services: virtual-machines-linux
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: b8c6e2e22d8478b6e92e7b7942f15d37a840fed3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Utilizar a Extensão de Diagnóstico do Linux para monitorizar os dados de desempenho e diagnóstico de uma VM do Linux

Este documento descreve a versão 2.3 a extensão de diagnóstico do Linux.

> [!IMPORTANT]
> Esta versão foi preterido e poderá ser anulada qualquer altura após 30 de Junho de 2018. Foi substituído por versão 3.0. Para obter mais informações, consulte o [documentação para a versão 3.0 de extensão de diagnóstico do Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Introdução

(**Nota**: A extensão de diagnóstico de Linux está aberta obtido no [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) onde as informações mais recentes sobre a extensão são publicadas pela primeira vez. Poderá verificar o [página do GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) primeiro.)

Um monitor de utilizador as VMs do Linux que estão em execução no Microsoft Azure ajuda-o a extensão de diagnóstico do Linux. Tem as seguintes capacidades:

* Recolhe e carrega as informações de desempenho do sistema da VM com Linux a tabela de armazenamento do utilizador, incluindo informações de diagnóstico e de syslog.
* Permite aos utilizadores personalizar as métricas de dados que serão recolhidas e carregadas.
* Permite aos utilizadores carregar ficheiros de registo especificado para uma tabela de armazenamento designada.

Na versão atual 2.3, os dados incluem:

* Todos os registos de Linux Rsyslog, incluindo o sistema, segurança e os registos de aplicações.
* Todos os dados de sistema que estão especificados na [o site de soluções de plataforma cruzada do System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Ficheiros de registo especificado de um utilizador.

Esta extensão funciona com clássica e modelos de implementação do Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Versão atual da extensão e descontinuação de versões antigas

A versão mais recente da extensão é **2.3**, e **quaisquer versões antigas (2.0, 2.1 e 2.2) serão preteridos e não publicados pela extremidade deste ano (2017)**. Se tiver instalado a extensão de diagnóstico do Linux com a atualização de versões de secundárias automática desativada, é vivamente recomendado que desinstalar a extensão e reinstalá-la com a atualização de versões de secundárias automática ativada. Em clássicas (ASM) VMs, pode conseguir isto, especificando '2.*' como a versão, se estiver a instalar a extensão através da CLI XPLAT do Azure ou do Powershell. Em VMs do ARM, pode conseguir isto, incluindo ' "autoUpgradeMinorVersion": true' no modelo de implementação de VM. Além disso, qualquer nova instalação da extensão deve ter a versão secundária de automática atualizar opção ativada.

## <a name="enable-the-extension"></a>Ativar a extensão

Pode ativar esta extensão utilizando o [portal do Azure](https://portal.azure.com/#), Azure PowerShell ou scripts da CLI do Azure.

Para ver e configurar os dados de sistema e o desempenho diretamente a partir do portal do Azure, siga [estes passos no blogue do Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Este artigo incida no como ativar e configurar a extensão através da utilização de comandos da CLI do Azure. Isto permite-lhe ler e ver os dados diretamente a partir da tabela de armazenamento.

Tenha em atenção que os métodos de configuração que são descritos aqui não funcionará para o portal do Azure. Para ver e configurar os dados de sistema e o desempenho diretamente a partir do portal do Azure, a extensão deve ser ativada através do portal.

## <a name="prerequisites"></a>Pré-requisitos

* **Agente Linux do Azure versão 2.0.6 ou posterior**.

  Tenha em atenção que a maioria das imagens de galeria do Azure VM Linux incluem a versão 2.0.6 ou posterior. Pode executar **WAAgent-versão** para confirmar a versão está instalada na VM. Se a VM está a executar uma versão anterior ao 2.0.6, pode seguir [estas instruções no GitHub](https://github.com/Azure/WALinuxAgent "instruções") atualizá-lo.
* **CLI do Azure**. Siga [este orientações para instalar a CLI](../../../cli-install-nodejs.md) para configurar o ambiente da CLI do Azure no seu computador. Após instalar a CLI do Azure, pode utilizar o **azure** comando da sua interface de linha de comandos (Bash, Terminal ou linha de comandos) para aceder aos comandos da CLI do Azure. Por exemplo:

  * Executar **conjunto de extensão de vm do azure – ajuda** para obter informações de ajuda detalhada.
  * Executar **início de sessão do azure** para iniciar sessão no Azure.
  * Executar **vm do azure lista** para listar todas as máquinas virtuais que tenham no Azure.
* Uma conta do storage para armazenar os dados. É necessário um nome de conta de armazenamento que foi criado anteriormente e uma chave de acesso para carregar os dados para o armazenamento.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Utilize o comando da CLI do Azure para ativar a extensão de diagnóstico do Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Cenário 1. Ativar a extensão com o conjunto de dados predefinido

Na versão 2.3 ou posterior, os dados de predefinidos que serão recolhidos incluem:

* Todas as informações de Rsyslog (incluindo o sistema, segurança e os registos de aplicações).  
* Um conjunto de núcleos de dados de base do sistema. Tenha em atenção que o conjunto completo de dados é descrito no [site soluções de plataforma cruzada do System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Se pretender ativar dados adicionais, continue com os passos em cenários de 2 e 3.

Passo 1. Crie um ficheiro denominado PrivateConfig.json com o seguinte conteúdo:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Passo 2. Executar  **extensão da vm do azure definir vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* – o caminho de configuração privada PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Cenário 2. Personalizar as métricas de monitor de desempenho

Esta secção descreve como personalizar o desempenho e a tabela de dados de diagnóstico.

Passo 1. Crie um ficheiro denominado PrivateConfig.json com o conteúdo que foi descrito no cenário 1. Também crie um ficheiro denominado PublicConfig.json. Especifique os dados específicos que pretende recolher.

Para todos suportados fornecedores e variáveis, referenciar a [site soluções de plataforma cruzada do System Center](https://scx.codeplex.com/wikipage?title=xplatproviders). Pode ter várias consultas e armazená-las em várias tabelas, acrescentando mais consultas para o script.

Por predefinição, os dados de Rsyslog sempre são recolhidos.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Passo 2. Executar  **extensão da vm do azure definir vm_name LinuxDiagnostic Microsoft.OSTCExtensions ' 2.*' – o caminho de configuração privada PrivateConfig.json – o caminho de configuração público PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Cenário 3. Carregar os seus próprios ficheiros de registo

Esta secção descreve como recolher e carregar ficheiros de registo específicos à sua conta de armazenamento. Tem de especificar ambos os caminho para o ficheiro de registo e o nome da tabela onde pretende armazenar o início de sessão. Pode criar vários ficheiros de registo adicionando várias entradas de ficheiros/tabela para o script.

Passo 1. Crie um ficheiro denominado PrivateConfig.json com o conteúdo que foi descrito no cenário 1. Em seguida, crie outro ficheiro com o nome PublicConfig.json com o seguinte conteúdo:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Passo 2. Execute `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Tenha em atenção que, com esta definição nas versões de extensão antes de 2.3, todos os registos escritos `/var/log/mysql.err` poderá ser duplicadas para `/var/log/syslog` (ou `/var/log/messages` consoante o distro Linux), bem como. Se quiser evitar este registo duplicado, pode excluir o registo de `local6` registos de instalação na configuração rsyslog. Depende do distro Linux, mas num sistema Ubuntu 14.04, é o ficheiro para modificar `/etc/rsyslog.d/50-default.conf` e pode substituir a linha `*.*;auth,authpriv.none -/var/log/syslog` para `*.*;auth,authpriv,local6.none -/var/log/syslog`. Este problema ser corrigido na versão de correções mais recente do 2.3 (2.3.9007), pelo que o se tiver a versão da extensão 2.3, não deve ocorrer este problema. Se continuar, mesmo depois de reiniciar a VM, contacte-nos e ajude-na resolver problemas com a razão pela qual a versão mais recente de correção não é instalada automaticamente.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Cenário 4. Parar a extensão de recolher os registos

Esta secção descreve como interromper a extensão de recolha de registos. Tenha em atenção que o processo de agente de monitorização estejam continua em execução mesmo com esta reconfiguração. Se gostaria de parar o processo de agente de monitorização completamente, pode fazê-lo ao desativar a extensão. O comando para desativar a extensão é `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Passo 1. Crie um ficheiro denominado PrivateConfig.json com o conteúdo que foi descrito no cenário 1. Crie outro ficheiro designado PublicConfig.json com o seguinte conteúdo:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Passo 2. Executar  **extensão da vm do azure definir vm_name LinuxDiagnostic Microsoft.OSTCExtensions ' 2.*' – o caminho de configuração privada PrivateConfig.json – o caminho de configuração público PublicConfig.json**.

## <a name="review-your-data"></a>Reveja os dados

O desempenho e a dados de diagnóstico são armazenados numa tabela de armazenamento do Azure. Reveja [como utilizar o Azure Table Storage do Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) para saber como aceder aos dados na tabela de armazenamento ao utilizar scripts da CLI do Azure.

Além disso, pode utilizar os seguintes ferramentas da IU para aceder aos dados:

1. Explorador de servidores do Visual Studio. Aceda à sua conta do storage. Depois da VM estiver em execução durante cerca de cinco minutos, verá as tabelas de quatro predefinida: "LinuxCpu", "LinuxDisk", "LinuxMemory" e "Linuxsyslog". Faça duplo clique os nomes de tabela para ver os dados.
1. [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/ "Explorador de armazenamento do Azure").

![Imagem](./media/diagnostic-extension/no1.png)

Se ativou fileCfg ou perfCfg (conforme descrito em cenários de 2 e 3), pode utilizar o Explorador de servidores do Visual Studio e o Explorador de armazenamento do Azure para ver os dados não predefinidas.

## <a name="known-issues"></a>Problemas conhecidos

* As informações de Rsyslog e o ficheiro de registo especificado de cliente só podem ser acedidos através do scripting.
