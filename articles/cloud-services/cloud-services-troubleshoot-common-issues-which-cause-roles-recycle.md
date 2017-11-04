---
title: "As causas comuns das funções do serviço em nuvem Reciclagem | Microsoft Docs"
description: "Uma função de serviço de nuvem subitamente recicla pode fazer com que o período de indisponibilidade significativo. Seguem-se alguns problemas comuns que causam funções sejam recicladas, que podem ajudar a reduzir o período de indisponibilidade."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 7f513a7d3fe44cbbf06855059c87c10ddceef7c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que fazem com que as funções reciclem
Este artigo descreve algumas das causas comuns dos problemas de implementação e fornece sugestões de resolução de problemas para o ajudar a resolver estes problemas. É uma indicação de que existe de um problema com uma aplicação quando a instância de função não consegue iniciar ou -ciclos entre os Estados de inicialização, ocupados e parar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo de execução em falta
Se uma função na sua aplicação baseia-se em qualquer biblioteca gerida do assemblagem que não faz parte do .NET Framework ou do Azure, tem de incluir explicitamente essa assemblagem no pacote de aplicação. Tenha em atenção que outras estruturas da Microsoft não estão disponíveis no Azure, por predefinição. Se a sua função depende de uma estrutura, tem de adicionar as assemblagens ao pacote de aplicações.

Antes de criar e empacotar a sua aplicação, verifique o seguinte:

* Se utilizar o Visual studio, certifique-se a **Cópia Local** propriedade está definida como **verdadeiro** para cada assemblagem referenciada no seu projeto que não faz parte do SDK do Azure ou do .NET Framework.
* Certifique-se de que o ficheiro Web. config não referência a assemblagens não utilizadas no elemento de compilação.
* O **ação criar** de cada. cshtml ficheiro está definido como **conteúdo**. Isto garante que os ficheiros serão apresentados corretamente no pacote e permite que outros ficheiros referenciados a aparecer no pacote.

## <a name="assembly-targets-wrong-platform"></a>Plataforma errada de destinos de assemblagem
Azure é um ambiente de 64 bits. Por conseguinte, as assemblagens .NET compiladas para um destino de 32 bits não funcionará no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Função emite exceções não processadas ao inicializar ou a parar
Quaisquer exceções que estão emitidas pelos métodos do [RoleEntryPoint] classe, que inclui o [OnStart], [OnStop], e [executar] os métodos, são exceções não processadas. Se ocorrer uma exceção não processada um destes métodos, a função reciclará. Se a função é Reciclagem repetidamente, poderá gerar uma exceção não processada sempre que tentar iniciar.

## <a name="role-returns-from-run-method"></a>Devolve a função partir método Run
O [executar] método destina-se para executar indefinidamente. Se o seu código substitui o [executar] método, este deve no modo de suspensão indefinidamente. Se o [executar] método devolve a função recicla.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Definição de DiagnosticsConnectionString incorreta
Se a aplicação utiliza diagnósticos do Azure, o ficheiro de configuração do serviço tem de especificar o `DiagnosticsConnectionString` definição de configuração. Esta definição deve especificar uma ligação HTTPS para a sua conta do storage no Azure.

Para se certificar de que o `DiagnosticsConnectionString` definição está correta antes de implementar o seu pacote de aplicação no Azure, verifique o seguinte:  

* O `DiagnosticsConnectionString` definição pontos a uma conta de armazenamento válida no Azure.  
  Por predefinição, esta definição aponta para a conta de armazenamento emulados pelo explicitamente tem de alterar esta definição antes de implementar o pacote de aplicação. Se não altere esta definição, é emitida uma exceção quando a instância de função se tentar iniciar o monitor de diagnóstico. Isto pode fazer com que a instância de função reciclar indefinidamente.
* A cadeia de ligação especificada a seguir [formato](../storage/common/storage-configure-connection-string.md). (O protocolo tem de ser especificado como HTTPS.) Substitua *MyAccountName* com o nome da sua conta do storage e *MyAccountKey* com a sua chave de acesso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se estiver a desenvolver a aplicação, utilizando ferramentas do Azure para o Microsoft Visual Studio, pode utilizar as páginas de propriedades para definir este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificado exportado não incluir a chave privada
Para executar uma função da web em SSL, tem de se certificar de que o certificado de gestão exportado inclui a chave privada. Se utilizar o *Gestor de certificados do Windows* para exportar o certificado, lembre-se de que selecione **Sim** para o **exportar a chave privada** opção. O certificado tem de ser exportado no formato PFX, que é o formato apenas atualmente suportado.

## <a name="next-steps"></a>Passos seguintes
Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços em nuvem.

Ver mais função Reciclagem cenários em [série de blogues de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[executar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
