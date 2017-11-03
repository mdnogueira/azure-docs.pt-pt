---
title: "Resolução de problemas degradado Estado no Gestor de tráfego do Azure"
description: Como resolver problemas de perfis do Traffic Manager quando mostra como degradada estado.
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: kumud
ms.openlocfilehash: b1d00fb84695d2289f37647f55a7c56cf28c8c96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Resolução de problemas degradado Estado no Gestor de tráfego do Azure

Este artigo descreve como resolver um perfil do Traffic Manager do Azure que está a ser mostrada Estado degradado. Para este cenário, considere que tiver configurado um perfil de Gestor de tráfego que apontam para alguns dos seus serviços cloudapp.net alojado. Se o estado de funcionamento do seu Gestor de tráfego apresenta um **Degraded** Estado, em seguida, o estado de um ou mais pontos finais poderá **Degraded**:

![Estado de degradada ponto final](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se apresenta o estado de funcionamento do seu Gestor de tráfego um **inativo** Estado, em seguida, ambos os pontos finais poderá **desativado**:

![Estado inativo do Gestor de tráfego](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Compreender o Gestor de tráfego

* Gestor de tráfego considera um ponto final para estar ONLINE apenas quando a sonda recebe uma resposta HTTP 200 volta a partir do caminho de pesquisa. Quaisquer outra resposta 200 não é uma falha.
* Falha um redirecionamento 30 x, mesmo que o URL redirecionado devolve um 200.
* Para as pesquisas de HTTPs, erros de certificado são ignorados.
* O conteúdo real do caminho de pesquisa não é relevante, desde que é devolvido um 200. Pesquisar um URL para algum conteúdo estático, como "/ favicon.ico" é uma técnica comuns. Conteúdo dinâmico, como as páginas ASP, pode não sempre devolver 200, mesmo quando a aplicação está em bom estada.
* Uma melhor prática é definir o caminho de pesquisa para algo que tem suficiente lógica para determinar se o site é cima ou para baixo. No exemplo anterior, definindo o caminho "/ favicon.ico", só são testar essa w3wp.exe está a responder. Esta pesquisa não pode indicar que a sua aplicação web está em bom estada. Uma opção melhor seria definido um caminho para uma algo como "/ Probe.aspx" que tem lógica para determinar o estado de funcionamento do site. Por exemplo, pode utilizar os contadores de desempenho para utilização da CPU ou medir o número de pedidos falhados. Ou pode tentar aceder a recursos da base de dados ou o estado da sessão para se certificar de que a aplicação web está a funcionar.
* Se todos os pontos finais num perfil estão degradados, em seguida, Gestor de tráfego trata todos os pontos finais como bom estado de funcionamento e o tráfego de rotas para todos os pontos finais. Este comportamento garante que os problemas com o mecanismo de pesquisa não resulta numa falha completa do seu serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver uma falha de pesquisa, precisa de uma ferramenta que mostra o código de estado HTTP devolvido a partir do URL de sonda. Existem várias ferramentas disponíveis que mostram a resposta HTTP não processada.

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, pode utilizar o separador de rede das ferramentas de depuração F12 no Internet Explorer para ver as respostas HTTP.

Para este exemplo, queremos ver a resposta do nosso URL de sonda: http://watestsdp2008r2.cloudapp.net:80/sonda. O exemplo do PowerShell seguinte ilustra o problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemplo de saída:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Tenha em atenção que recebemos uma resposta de redirecionamento. Conforme indicado anteriormente, qualquer StatusCode diferente de 200 é considerado uma falha. Gestor de tráfego altera o estado de ponto final para Offline. Para resolver o problema, verifique a configuração do Web site para se certificar de que o StatusCode adequada pode ser devolvido a partir do caminho de pesquisa. Reconfigure a sonda do Gestor de tráfego para apontar para um caminho que devolve um 200.

Se a sua pesquisa está a utilizar o protocolo HTTPS, poderá ter de desativar o certificado de verificação para evitar erros SSL/TLS durante o teste. As seguintes declarações de PowerShell desativar validação de certificado para a sessão atual do PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Passos Seguintes

[Sobre os métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md)

[O que é o Gestor de tráfego](traffic-manager-overview.md)

[Serviços Cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Aplicações Web do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações do Gestor de Tráfego (Referência da API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gestor de tráfego do Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
