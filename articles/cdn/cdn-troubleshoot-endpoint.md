---
title: "Resolução de problemas de pontos finais da CDN do Azure que estado 404 | Microsoft Docs"
description: "Resolver problemas de 404 códigos de resposta com pontos finais da CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f59fbd18413fb44026d8c92b7f6940ed2f8a00a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Resolução de problemas em pontos finais da CDN que devolvem estados 404
Este artigo ajuda-o a resolver problemas com [pontos finais da CDN](cdn-create-new-endpoint.md) devolver 404 erros.

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [do MSDN Azure e os fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode também ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
Criou um perfil da CDN e um ponto final, mas o conteúdo não parece estar disponíveis na CDN.  Os utilizadores que tentam aceder ao conteúdo através do URL de CDN recebem códigos de estado de HTTP 404. 

## <a name="cause"></a>Causa
Existem várias causas possíveis, incluindo:

* Origem do ficheiro não está visível para a CDN
* O ponto final configurada incorretamente, fazendo com que a CDN parecer no local incorreto
* O anfitrião está a rejeitar o cabeçalho de anfitrião da CDN
* O ponto final não registou a tempo para propagar em toda a CDN

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!IMPORTANT]
> Depois de criar um ponto final de CDN, não imediatamente será disponível para utilização, que demora algum tempo para que o registo propagar pela CDN.  Para os perfis <b>CDN do Azure da Akamai</b>, a propagação normalmente fica concluída num minuto.  Para os perfis <b>CDN do Azure da Verizon</b>, a propagação normalmente fica concluída no prazo de 90 minutos, mas em certos casos pode demorar mais tempo.  Se concluir os passos neste documento e ainda está a obter 404 respostas, considere a aguardar a algumas horas para verificar novamente antes de abrir um pedido de suporte.
> 
> 

### <a name="check-the-origin-file"></a>Verifique o ficheiro de origem
Em primeiro lugar, iremos deverá certificar-se de que o ficheiro queremos em cache está disponível no nosso origem e é acessível publicamente.  A forma mais rápida para o fazer é abra um browser numa sessão Incognito ou em privado e procurar diretamente o ficheiro.  Basta escreva ou cole o URL na caixa de endereço e ver se o que resulta no ficheiro de que espera.  Neste exemplo, vou utilizar um ficheiro tiver uma conta de armazenamento do Azure, acessível em `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Como pode ver, passa com êxito o teste.

![Êxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora a forma mais rápida e mais fácil para verificar que o ficheiro está publicamente disponível, algumas configurações de rede na sua organização poderia conceder a ilusão de que este ficheiro está publicamente disponível quando faz parte de facto, apenas visível para os utilizadores da rede (mesmo está alojado no Azure).  Se tiver um browser externo a partir da qual pode testar, tais como um dispositivo móvel que não está ligado à rede da sua organização ou uma máquina virtual no Azure, que será melhor.
> 
> 

### <a name="check-the-origin-settings"></a>Verifique as definições de origem
Agora que iremos tiver confirmado que o ficheiro é publicamente disponível na internet, mas deve verificar as nossas definições de origem.  No [Portal do Azure](https://portal.azure.com), navegue para o perfil de CDN e clique no ponto final que está a resolução de problemas.  No resultante **Endpoint** painel, clique em origem.  

![Painel de ponto final com origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

O **origem** é apresentado o painel. 

![Painel de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origem e nome de anfitrião
Certifique-se a **tipo de origem** estão corretas e, certifique-se a **nome de anfitrião de origem**.  A minha exemplo `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, a parte do nome de anfitrião do URL é `cdndocdemo.blob.core.windows.net`.  Como pode ver na captura de ecrã, este está correta.  Para armazenamento do Azure, aplicação Web e origens de serviço em nuvem, o **nome de anfitrião de origem** campo é uma lista pendente, pelo que não temos preocupar spelling-la corretamente.  No entanto, se estiver a utilizar uma origem personalizado, é *absolutamente essencial* seu nome do anfitrião está escrito corretamente!

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
A outra coisa para verificar aqui é o **HTTP** e **portas HTTPS**.  Na maioria dos casos, 80 e 443 estão corretas e que vai necessitar sem alterações.  No entanto, se o servidor de origem está à escuta numa porta diferente, que terá de ser representado aqui.  Se não tiver a certeza, procure no URL para o ficheiro de origem.  As especificações de HTTP e HTTPS especificar as portas 80 e 443 como as predefinições. No meu URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, uma porta não for especificada, pelo que a predefinição de 443 pressupõe-se e as minhas definições estão corretas.  

No entanto, indicar o URL para o ficheiro de origem que testada anteriormente é `http://www.contoso.com:8080/file.txt`.  Tenha em atenção o `:8080` no fim do segmento do nome do anfitrião.  Indica que o browser para utilizar a porta `8080` para ligar ao servidor web em `www.contoso.com`, pelo que terá de introduzir 8080 no **porta HTTP** campo.  É importante ter em atenção que estas definições de porta afetam apenas as portas que o ponto final utiliza para obter informações da origem.

> [!NOTE]
> Os pontos finais da **CDN do Azure da Akamai** não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](https://msdn.microsoft.com/library/mt757337.aspx).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verifique as definições de ponto final
Reverter o **Endpoint** painel, clique em de **configurar** botão.

![Painel de ponto final com o botão Configurar realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

O ponto final **configurar** é apresentado o painel.

![Configurar o painel](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **protocolos**, certifique-se de que o protocolo a ser utilizado pelos clientes está selecionado.  O mesmo protocolo utilizado pelo cliente será utilizada para aceder à origem, pelo que é importante que tenha as portas de origem configuradas corretamente na secção anterior.  Apenas o ponto final de escuta HTTP e HTTPS portas predefinidas (80 e 443), independentemente das portas de origem.

Vamos voltar para o nosso exemplo hipotético com `http://www.contoso.com:8080/file.txt`.  Como irá lembrar, Contoso especificado `8080` como as respetivas HTTP de porta, mas vamos também partem do princípio de que o especificado `44300` como a respetiva porta HTTPS.  Se um ponto final com o nome criadas `contoso`, seria o respetivo nome de anfitrião de ponto final CDN `contoso.azureedge.net`.  Um pedido para `http://contoso.azureedge.net/file.txt` é um pedido HTTP, pelo que o ponto final teria de utilizar HTTP na porta 8080 para obtê-lo da origem.  Um pedido seguro através de HTTPS, `https://contoso.azureedge.net/file.txt`, fará com que o ponto final para utilizar HTTPS numa porta 44300 ao obter o ficheiro da origem.

#### <a name="origin-host-header"></a>Cabeçalho de anfitrião de origem
O **cabeçalho de anfitrião de origem** é o valor de cabeçalho de anfitrião enviado para a origem com cada pedido.  Na maioria dos casos, isto deve ser o mesmo que o **nome de anfitrião de origem** foi verificado anteriormente.  Um valor incorreto neste campo, geralmente, não irá fazer com que devolvem 404 Estados, mas provavelmente, causar outros Estados 4xx, consoante o que espera que a origem.

#### <a name="origin-path"></a>Caminho de origem
Por último, deve verificar a nossa **caminho de origem**.  Por predefinição, está em branco.  Só deve utilizar este campo se pretender restringir o âmbito dos recursos alojados de origem que pretende disponibilizar na CDN.  

Por exemplo, no meu ponto final, posso pretendia todos os recursos na minha conta de armazenamento esteja disponível, para que posso deixado **caminho de origem** em branco.  Isto significa que um pedido para `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` resulta numa ligação do meu ponto final para `cdndocdemo.core.windows.net` que solicita `/publicblob/lorem.txt`.  Da mesma forma, um pedido para `https://cdndocdemo.azureedge.net/donotcache/status.png` resulta no pedido de ponto final `/donotcache/status.png` da origem.

Mas e se não pretender utilizar a CDN para cada caminho no meu origem?  Diga I pretendia apenas para expor o `publicblob` caminho.  Se introduzo */publicblob* na minha **caminho de origem** campo, o que fará com que o ponto final para inserir */publicblob* antes de todos os pedidos efetuados para a origem.  Isto significa que o pedido para `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` agora irá demorar, na verdade, a parte do pedido de URL, `/publicblob/lorem.txt`e de acréscimo `/publicblob` para o início. Isto resulta num pedido para `/publicblob/publicblob/lorem.txt` da origem.  Se o caminho não resolve para um ficheiro real, a origem irá devolver um estado 404.  O URL correto para obter lorem.txt neste exemplo, na verdade, seria `https://cdndocdemo.azureedge.net/lorem.txt`.  Tenha em atenção que iremos não incluem o */publicblob* caminho de todo, porque a parte do pedido do URL é `/lorem.txt` e adiciona o ponto final `/publicblob`, resultando num `/publicblob/lorem.txt` a ser pedido transmitido para a origem.

