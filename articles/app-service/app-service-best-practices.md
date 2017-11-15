---
title: "Melhores Práticas do Serviço de Aplicações do Azure"
description: "Saiba as melhores práticas e resolução de problemas do App Service do Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 251ce238b745734bdfb508b30097304a9a650a8c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="best-practices-for-azure-app-service"></a>Melhores Práticas do Serviço de Aplicações do Azure
Este artigo resume as melhores práticas para utilizar [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Colocalização
Quando os recursos do Azure composição uma solução como uma aplicação web e uma base de dados estão localizados em regiões diferentes os efeitos podem incluir o seguinte:

* Uma maior latência de comunicação entre recursos
* Transferência de custos monetários de dados de saída por várias regiões conforme indicado no [do Azure de página de preços](https://azure.microsoft.com/pricing/details/data-transfers).

A colocalização na mesma região é melhor para composição uma solução como uma aplicação web e uma conta de armazenamento ou base de dados utilizado para reter dados ou conteúdo de recursos do Azure. Durante a criação de recursos, deve certificar-se de que estão na mesma região do Azure, exceto se tiver empresarial específico ou razão para os mesmos não deve ser de design. Pode mover uma aplicação de serviço de aplicações para a mesma região que a base de dados ao tirar partido de [funcionalidade de clonagem do serviço de aplicações](app-service-web-app-cloning.md) atualmente disponível para aplicações plano do Premium App Service.   

## <a name="memoryresources"></a>Quando as aplicações consumir mais memória do que o previsto
Quando é que uma aplicação consome mais memória do que era esperado dado indicados através da monitorização ou considere recomendações de serviço Repare o [funcionalidade aplicação serviço Autorrecuperação](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para a funcionalidade de recuperação automática está a demorar ações personalizadas com base num limiar de memória. Ações span espetro de notificações por e-mail para investigação através do Estado da memória no-a-lugar para cima mitigação ao reciclar o processo de trabalho. Autorrecuperação pode ser configurado através da Web. config e através de uma interface de utilizador amigável conforme descrito neste blogue para o [extensão de Site de suporte de serviço de aplicação](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando as aplicações consumir mais CPU que o previsto
Quando Repare que uma aplicação consome CPU mais que o esperado ou experiências repetido picos de CPU, conforme indicado através de monitorização ou recomendações de serviço considere como aumentar verticalmente ou aumentar horizontalmente o plano de serviço de aplicações. Se a aplicação for statefull, como aumentar verticalmente é a única opção, enquanto se a aplicação está fora do dimensionamento, sem monitorização de estado irá dar-lhe mais flexibilidade e a maior possibilidade de escala. 

Para obter mais informações sobre aplicações "sem monitorização de estado" do "statefull" vs pode ver este vídeo: [planeamento de uma aplicação de várias camadas dimensionável ponto-a-ponto na aplicação do Microsoft Azure Web](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre as opções de dimensionamento e o dimensionamento automático do serviço de aplicações, leia: [dimensionar uma aplicação Web no App Service do Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Quando os recursos de socket ficam esgotados
Um motivo comum esgotar os ligações de TCP de saída é a utilização de bibliotecas de cliente que não são implementadas para reutilizar ligações TCP ou no caso de um protocolo de nível superior como HTTP - ligação Keep-Alive não a ser utilizada. Reveja a documentação de cada uma das bibliotecas referenciadas pelas aplicações no seu plano do App Service para garantir que estão configurados ou acedidos no seu código para reutilização eficiente de ligações de saída. Siga também as orientações de documentação de biblioteca para criação adequada e a versão ou a limpeza para evitar a fuga de dados de ligações. Embora estas bibliotecas de cliente são as investigações no impacto de progresso podem ser mitigadas ao aumentar horizontalmente a várias instâncias.

### <a name="nodejs-and-outgoing-http-requests"></a>NODE.js e pedidos de http de saída
Ao trabalhar com o Node.js e muitos pedidos de http de saída, lidar com HTTP - Keep-Alive, é extremamente importante. Pode utilizar o [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` pacote para o facilitar no seu código.

Sempre deve processar o `http` resposta, mesmo se o fizer nada do processador. Se não processar a resposta corretamente, a aplicação acaba por ficar bloqueada porque não existem mais sockets estão disponíveis.

Por exemplo, quando trabalhar com o `http` ou `https` pacote:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Se estiver a executar no App Service no Linux num computador com vários núcleos, outro procedimento recomendado é utilizar PM2 iniciar vários processos de Node.js para executar a aplicação. Pode fazê-lo especificando um comando de arranque para o contentor.

Por exemplo, para iniciar a quatro instâncias:

`pm2 start /home/site/wwwroot/app.js --no-daemon -i 4`

## <a name="appbackup"></a>Quando a aplicação criar cópias de segurança começa a falhar
As duas razões mais comuns são por que motivo de falha de cópia de segurança de aplicação: definições de armazenamento inválido e a configuração de base de dados inválido. Estas falhas acontecer, normalmente, quando existem alterações a recursos de armazenamento ou base de dados ou alterações como aceder a estes recursos (por exemplo, as credenciais atualizados para a base de dados selecionado nas definições de cópia de segurança). Normalmente, as cópias de segurança com base numa agenda e necessitam de acesso a armazenamento (para exportar a cópia de segurança de ficheiros) e bases de dados (para copiar e ler conteúdo sejam incluídos na cópia de segurança). O resultado da não consegue aceder a estes recursos seria falhas de cópia de segurança consistente. 

Quando ocorrem falhas de cópia de segurança, reveja os resultados mais recentes para compreender qual o tipo de falha está a acontecer. No caso de falhas de acesso de armazenamento, rever e atualizar as definições de armazenamento utilizadas na configuração de cópia de segurança. No caso de falhas de acesso de base de dados, rever e atualizar as cadeias de ligações como parte das definições da aplicação; em seguida, avance para atualizar a configuração de cópia de segurança para incluir corretamente necessários bases de dados. Para obter mais informações sobre a cópia de segurança da aplicação, consulte o [cópia de segurança numa aplicação web no App Service do Azure](web-sites-backup.md) documentação.

## <a name="nodejs"></a>Quando as novas aplicações Node.js são implementadas para o App Service do Azure
Destina-se a configuração predefinida do serviço de aplicações do Azure para aplicações Node.js a melhor as necessidades de aplicações mais comuns. Se a configuração para a sua aplicação Node.js seria beneficiar da otimização personalizada para melhorar o desempenho ou a otimizar a utilização de recursos para recursos de CPU/memória/rede, pode rever nossos melhores práticas e passos de resolução de problemas. Este artigo de documentação descreve as definições do iisnode poderá ter de configurar para a sua aplicação Node.js, descreve os vários cenários ou problemas que a aplicação pode ser enfrentam e mostra como resolver estes problemas: [melhores práticas e guia de resolução de problemas para aplicações de nó no App Service do Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   

