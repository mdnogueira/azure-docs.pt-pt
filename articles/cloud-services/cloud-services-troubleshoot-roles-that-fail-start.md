---
title: "Resolver problemas de funções que não conseguem iniciar | Microsoft Docs"
description: "Seguem-se algumas razões comuns por que motivo uma função de serviço em nuvem pode falhar ao iniciar. Também são fornecidas soluções para esses problemas."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: ec33ba08c6284e90edc1870eef4bf3059b917efb
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Resolver problemas relacionados com funções de serviço em nuvem que não conseguem iniciar
Seguem-se alguns problemas comuns e soluções relacionadas com a Cloud Services do Azure funções que não conseguem iniciar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs em falta ou dependências
Funções que não responde e funções que são ciclos entre **Initializing**, **ocupado**, e **parar** Estados podem ser causados pela falta DLLs ou assemblagens.

Podem ser sintomas de em falta DLLs ou assemblagens:

* A instância de função é ciclos através de **Initializing**, **ocupado**, e **parar** Estados.
* A instância de função foi movida para **pronto** , mas se navegar para a sua aplicação web, a página não é apresentada.

Existem vários métodos recomendados para estes problemas a investigar.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas DLL em falta numa função da web
Quando navega para um site que é implementado uma web função e o browser apresenta um erro semelhante ao seguinte, isto poderá indicar que um DLL está em falta.

![Erro de servidor na aplicação '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desativar erros personalizados
Mais completas informações de erro podem ser visualizadas por configurar o Web. config para a função da web definir o modo de erros personalizados como desligado e voltar a implementar o serviço.

Para ver os erros mais completos sem utilizar o ambiente de trabalho remoto:

1. Abra a solução no Microsoft Visual Studio.
2. No **Explorador de soluções**, localize o ficheiro Web. config e abra-o.
3. No ficheiro Web. config, localize a secção System. Web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```
4. Guarde o ficheiro.
5. Empacote novamente esta e reimplemente o serviço.

Assim que o serviço está implementada novamente, verá uma mensagem de erro com o nome da assemblagem em falta ou DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas visualizando o erro remotamente
Pode utilizar o ambiente de trabalho remoto para aceder a função e ver informações de erro mais completas remotamente. Utilize os seguintes passos para ver os erros, utilizando o ambiente de trabalho remoto:

1. Certifique-se de que o Azure SDK 1.3 ou posterior está instalado.
2. Durante a implementação da solução utilizando o Visual Studio, optar por "Configurar ambientes de trabalho ligações remotas...". Para obter mais informações sobre como configurar a ligação de ambiente de trabalho remoto, consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](../vs-azure-tools-remote-desktop-roles.md).
3. No portal do Microsoft Azure clássico, assim que a instância mostra um Estado de **pronto**, clique das instâncias da função.
4. Clique em de **Connect** ícone no **acesso remoto** área do Friso.
5. Iniciar sessão para a máquina virtual, utilizando as credenciais que foram especificadas durante a configuração do ambiente de trabalho remoto.
6. Abra uma janela de comando.
7. Digite `IPconfig`.
8. Tenha em atenção o valor de endereço IPV4.
9. Abra o Internet Explorer.
10. Escreva o endereço e o nome da aplicação web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar para o Web site irá agora devolver mais explícitas mensagens de erro:

* Erro de servidor na aplicação '/'.
* Descrição: Ocorreu uma exceção não processada durante a execução do pedido web atual. Reveja o rastreio da pilha para obter mais informações sobre o erro e onde teve origem no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: não foi possível carregar o ficheiro ou assemblagem ' Microsoft.WindowsAzure.StorageClient, versão = 1.1.0.0, Culture = independente, PublicKeyToken = 31bf856ad364e35' ou uma das respetivas dependências. O sistema não é possível localizar o ficheiro especificado.

Por exemplo:

![Erro de servidor explícita na aplicação '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas ao utilizar o emulador de computação
Pode utilizar o emulador de computação do Microsoft Azure para diagnosticar e resolver problemas de em falta as dependências e erros de Web. config.

Para obter os melhores resultados na através deste método de diagnóstico, deve utilizar um computador ou máquina virtual que tenha uma instalação limpa do Windows. Para melhor simular o ambiente do Azure, utilize o Windows Server 2008 R2 x64.

1. Instale a versão autónoma do [Azure SDK](https://azure.microsoft.com/downloads/).
2. No computador de desenvolvimento, compilar o projeto de serviço em nuvem.
3. No Explorador do Windows, navegue para a pasta de bin\debug do projeto de serviço em nuvem.
4. Copie o ficheiro de pasta e. cscfg do .csx no computador que estiver a utilizar para os problemas de depuração.
5. Na máquina limpa, abra uma janela de linha de comandos do Azure SDK e escreva `csrun.exe /devstore:start`.
6. Na linha de comandos, escreva `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Quando a função é iniciado, irá ver informações de erro detalhadas no Internet Explorer. Também pode utilizar as ferramentas de resolução de problemas padrão do Windows para obter mais diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas através da utilização de IntelliTrace
Para o trabalho e funções da web que utilizam o .NET Framework 4, pode utilizar [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), que está disponível no Microsoft Visual Studio Enterprise.

Siga estes passos para implementar o serviço com o IntelliTrace ativada:

1. Confirme que o Azure SDK 1.3 ou posterior está instalado.
2. Implemente a solução utilizando o Visual Studio. Durante a implementação, verifique o **ativar IntelliTrace para funções de .NET 4** caixa de verificação.
3. Assim que a instância for iniciada, abra o **Explorador de servidores**.
4. Expanda o **Azure\\serviços em nuvem** nós e localize a implementação.
5. Expanda a implementação até verá as instâncias da função. Faça duplo clique das instâncias.
6. Escolha **registos do IntelliTrace de vista**. O **resumo de IntelliTrace** irá abrir.
7. Localize a secção de exceções do resumo. Se existirem exceções, a secção será assinalada como **os dados da exceção**.
8. Expanda o **os dados da exceção** e procure **System.IO.FileNotFoundException** erros semelhantes ao seguinte:

![Dados de exceção, ficheiro em falta ou assemblagem](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Endereço em falta DLLs e assemblagens
Para resolver DLL em falta e erros de assemblagem, siga estes passos:

1. Abra a solução no Visual Studio.
2. No **Explorador de soluções**, abra o **referências** pasta.
3. Clique em assemblagem identificada no erro.
4. No **propriedades** painel, localize **propriedade Cópia Local** e defina o valor **verdadeiro**.
5. Reimplemente o serviço de nuvem.

Depois de ter confirmado que todos os erros tem sido corrigidos, pode implementar o serviço sem verificar o **ativar IntelliTrace para funções de .NET 4** caixa de verificação.

## <a name="next-steps"></a>Passos seguintes
Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços em nuvem.

Para saber como resolver problemas de função Serviço de nuvem, utilizando os dados de diagnóstico do computador PaaS do Azure, consulte [série de blogues de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
