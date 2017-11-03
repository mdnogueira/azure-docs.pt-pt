---
title: "Monitorizar o estado de replicação do Active Directory com o Log Analytics do Azure | Microsoft Docs"
description: "O pacote de solução de estado de replicação do Active Directory regularmente monitoriza o ambiente do Active Directory para o eventuais falhas de replicação e reporta os resultados no seu dashboard do OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfe52ef5d9d09ffe179faaf6ffbd90ef964fbda9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Monitorizar o estado de replicação do Active Directory com a análise de registos

![Símbolo de estado de replicação do AD](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Active Directory é um componente fundamental de uma empresa de ambiente de TI. Para garantir a elevada disponibilidade e elevado desempenho, cada controlador de domínio tem a respetiva cópia da base de dados do Active Directory. Os controladores de domínio replicar entre si para propagar as alterações em toda a empresa. Falhas neste processo de replicação podem causar uma variedade de problemas em toda a empresa.

O pacote de solução de estado de replicação do AD regularmente monitoriza o ambiente do Active Directory para o eventuais falhas de replicação e reporta os resultados no seu dashboard do OMS.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Tem de instalar agentes nos controladores de domínio que são membros do domínio a ser avaliada. Em alternativa, tem de instalar agentes em servidores membro e configurar os agentes a enviar dados de replicação do AD para OMS. Para compreender como ligar a computadores com o Windows ao OMS, consulte [computadores Windows ligar ao Log Analytics](log-analytics-windows-agents.md). Se o controlador de domínio já faz parte de um ambiente existente do System Center Operations Manager que pretende ligar ao OMS, consulte [estabelecer a ligação do Operations Manager ao Log Analytics](log-analytics-om-agents.md).
* Adicionar a solução de estado de replicação do Active Directory para a sua área de trabalho do OMS utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).  Não há nenhuma configuração adicional.

## <a name="ad-replication-status-data-collection-details"></a>Detalhes de recolha de dados de estado de replicação do AD
A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o estado de replicação do AD.

| Plataforma | Direcionar o agente | Agente do SCOM | Storage do Azure | SCOM necessário? | Dados de agente do SCOM enviados através do grupo de gestão | Frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |cada cinco dias |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Opcionalmente, ativar a um controlador de domínio enviar dados de AD para OMS
Se não pretender ligar qualquer um dos seus controladores de domínio diretamente ao OMS, pode utilizar qualquer outro computador ligado à OMS no seu domínio para recolher dados para o pacote de solução de estado de replicação do AD e tiver enviar os dados.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Para ativar um controlador de domínio enviar dados de AD para OMS
1. Certifique-se de que o computador é um membro do domínio que pretende monitorizar utilizando a solução de estado de replicação do AD.
2. [Ligue o computador Windows à OMS](log-analytics-windows-agents.md) ou [ligá-lo utilizando o seu ambiente existente do Operations Manager para OMS](log-analytics-om-agents.md), se já não está ligado.
3. Nesse computador, defina a seguinte chave de registo:

   * Chave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management grupos\<ManagementGroupName > \Solutions\ADReplication**
   * Valor: **IsTarget**
   * Dados do valor: **verdadeiro**

   > [!NOTE]
   > Estas alterações não entram em vigor até que o reinício do serviço Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Noções sobre erros de replicação
Assim que tiver dados de estado de replicação de AD enviados OMS, verá um mosaico semelhante ao seguinte imagem no dashboard do OMS que indica o número de erros de replicação tem atualmente.  
![Mosaico estado de replicação do AD](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Erros críticos de replicação** erros que são igual ou superior 75% do [duração tombstone](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) para a sua floresta do Active Directory.

Quando clica no mosaico, pode ver mais informações sobre os erros.
![Dashboard de estado de replicação do AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Estado do servidor de destino e o estado do servidor de origem
Estas colunas mostram o estado dos servidores de destino e os servidores de origem que estão a experienciar erros de replicação. O número após cada nome de controlador de domínio indica o número de erros de replicação nesse controlador de domínio.

Os erros para os servidores de origem e de servidores de destino são apresentados porque alguns problemas são mais fáceis de resolução de problemas da perspectiva do servidor de origem e outros perspectiva do servidor de destino.

Neste exemplo, pode ver que muitos servidores de destino tem, aproximadamente, o mesmo número de erros, mas não existe um servidor de origem (ADDC35) que tem demasiados erros mais que todos os outros. É provável que há um problema no ADDC35 que está a causar a falha ao enviar dados para os respetivos parceiros de replicação. Corrigir os problemas em ADDC35 poderá resolver muitos dos erros que aparecem na área de servidor de destino.

### <a name="replication-error-types"></a>Tipos de erro de replicação
Esta área dá-lhe informações sobre os tipos de erros, detetados em toda a empresa. Cada erro tem um código numérico exclusivo e uma mensagem que pode ajudar a determinar a causa do erro.

O anel na parte superior dá uma ideia do que erros aparecem mais e com menos frequência no seu ambiente.

Mostra quando o mesmo erro de replicação de experiência de vários controladores de domínio. Neste caso, poderá conseguir detetar ou identificar uma solução de um controlador de domínio, em seguida, repita-a em outros controladores de domínio afetados pelo mesmo erro.

### <a name="tombstone-lifetime"></a>Duração tombstone
A duração tombstone determina quanto tempo um objeto eliminado, referido como um tombstone, são retidas na base de dados do Active Directory. Quando um objeto eliminado passa a duração tombstone, um processo de recolha de lixo remove-automaticamente da base de dados do Active Directory.

A predefinição de duração tombstone é de 180 dias para versões mais recentes do Windows, mas foi 60 dias em versões anteriores e pode ser alterada explicitamente por um administrador do Active Directory.

É importante saber se está a ter erros de replicação que estão a aproximar-se ou passado a duração tombstone. Se dois controladores de domínio ocorre um erro de replicação que persistir após a duração tombstone, é desativar a replicação entre os controladores de dois domínio, mesmo se o erro de replicação subjacente estiver resolvido.

A área de duração Tombstone ajuda-o a identificar os locais onde o desativado a replicação está em perigo de acontecer. Cada erro no **mais de 100% TSL** categoria representa uma partição que não foi replicado entre o servidor de origem e destino para, pelo menos, a duração tombstone da floresta.

Nesta situação, basta corrigir o erro de replicação não poderá ser suficiente. No mínimo, tem de investigar manualmente para identificar e limpar objetos remanescentes antes de reiniciar a replicação. Ainda poderá ter de desativar um controlador de domínio.

Para além de identificar quaisquer erros de replicação tem persistida passado a duração tombstone, também deve prestar atenção à erros baixar para o **50 75% TSL** ou **75 100% TSL** categorias.

Estes são os erros que são claramente remanescentes, não é transitório, pelo que, provavelmente, precisam de sua intervenção para resolver. Boas notícias é que estes não ainda atingiu a duração tombstone. Se corrigir estes problemas retomadas rapidamente e *antes* atingirem a duração tombstone, a replicação pode reiniciar com mínima intervenção manual.

Conforme indicado anteriormente, no mosaico do dashboard para a solução de estado de replicação do AD mostra o número de *críticos* erros de replicação no seu ambiente, o que é definido como erros que são mais de 75% da duração tombstone (incluindo erros que são mais 100% de TSL). Esforçar-na manter este número em 0.

> [!NOTE]
> Todos os tombstone duração percentagem cálculos do baseiam-se de duração tombstone real para a sua floresta do Active Directory, pelo que pode confiar que esses percentagens estão corretas, mesmo que tenha um valor de duração tombstone personalizado definido.
>
>

### <a name="ad-replication-status-details"></a>Detalhes de estado de replicação do AD
Ao clicar em qualquer item de uma das listas, pode ver detalhes adicionais sobre-la utilizando a pesquisa de registo. Os resultados são filtrados para mostrar apenas os erros relacionados com que o item. Por exemplo, se clicar no primeiro controlador de domínio listados na **o estado do servidor de destino (ADDC02)**, consulte os resultados da pesquisa filtrados para mostrar erros com esse controlador de domínio listado como o servidor de destino:

![Erros de estado de replicação AD nos resultados da pesquisa](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Aqui, pode filtrar adicional, modifique a consulta de pesquisa e assim sucessivamente. Para obter mais informações sobre como utilizar a pesquisa de registo, consulte [pesquisas de registo](log-analytics-log-searches.md).

O **HelpLink** campo mostra o URL de uma página da TechNet com detalhes adicionais sobre este erro específico. Pode copiar e colar esta hiperligação para a janela do browser para ver informações sobre resolução de problemas e corrigir o erro.

Também pode clicar em **exportar** para exportar os resultados para o Excel. Exportar os dados pode ajudar a visualizar dados de erros de replicação de qualquer forma que gostaria de.

![exportado erros de estado de replicação AD no Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Estado de replicação AD FAQ
**P: como muitas vezes, são atualizados de dados de estado de replicação de AD?**
R: as informações são atualizadas a cada cinco dias.

**P: existe uma forma de configurar a frequência com que estes dados são atualizados?**
R: neste momento, não.

**P: é necessário adicionar todos os meus de controladores de domínio a minha área de trabalho do OMS para ver o estado de replicação?**
R: não, apenas um único controlador de domínio tem de ser adicionado. Se tiver vários controladores de domínio na sua área de trabalho do OMS, dados de todos eles são enviados para OMS.

**P: Posso não quiser adicionar quaisquer controladores de domínio a minha área de trabalho do OMS. Posso continuar a utilizar a solução de estado de replicação do AD?**
R: Sim. Pode definir o valor de uma chave de registo para ativá-la. Consulte [para permitir que um controlador de domínio enviar dados de AD para OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**P: qual é o nome do processo de que a recolha de dados?**
R: AdvisorAssessment.exe

**P: quanto tempo demora para dados a serem recolhidos?**
R: hora de recolha de dados de depende do tamanho do ambiente do Active Directory, mas normalmente demora menos de 15 minutos.

**P: o tipo de dados é recolhido?**
R: as informações de replicação de são recolhidas através de LDAP.

**P: existe uma forma de configurar quando são recolhidos os dados?**
R: neste momento, não.

**P: que permissões precisa recolher dados?**
R: permissões de utilizador normal de ao Active Directory são suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Resolução de problemas de recolha de dados
Para recolher dados, o pacote de solução de estado de replicação do AD requer pelo menos um controlador de domínio estar ligados à sua área de trabalho do OMS. Até que o se liga um controlador de domínio, é apresentada uma mensagem indicando que **ainda estão a ser recolhidos dados**.

Se precisar de assistência para ligar um dos seus controladores de domínio, pode ver a documentação em [computadores Windows ligar ao Log Analytics](log-analytics-windows-agents.md). Em alternativa, se o controlador de domínio já está ligado a um ambiente existente do System Center Operations Manager, pode ver documentação em [ligar o System Center Operations Manager ao Log Analytics](log-analytics-om-agents.md).

Se não pretender ligar qualquer um dos seus controladores de domínio diretamente ao OMS ou ao SCOM, consulte o artigo [para permitir que um controlador de domínio enviar dados de AD para OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver os dados de estado de replicação do Active Directory detalhados.
