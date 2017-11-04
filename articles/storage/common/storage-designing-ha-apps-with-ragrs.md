---
title: "Conceber aplicações altamente disponíveis através do Azure armazenamento Georredundante com acesso de leitura (RA-GRS) | Microsoft Docs"
description: "Como utilizar o storage do Azure. o RA-GRS para architect uma aplicação altamente disponível flexível o suficiente para lidar com falhas."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 9/06/2017
ms.author: tamram
ms.openlocfilehash: 4100e8b90e37d6f4ab5123dfd682452c21c77998
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Conceber aplicações altamente disponíveis utilizando RA-GRS

Uma funcionalidade comum das infraestruturas baseado na nuvem, como o Storage do Azure é que fornecem uma plataforma de elevada disponibilidade para alojar aplicações. Os programadores de aplicações baseado na nuvem tem de considerar atentamente como tirar partido nesta plataforma para fornecer aplicações de elevada disponibilidade para os seus utilizadores. Este artigo foca-se na forma como os programadores podem utilizar armazenamento Georredundante com acesso de leitura (RA-GRS) para se certificar de que as aplicações de armazenamento do Azure são de elevada disponibilidade.

Storage do Azure oferece quatro opções para a redundância de dados na sua conta de armazenamento:

– LRS (localmente Refdundant armazenamento)
- ZRS (zona de armazenamento redundantes) 
- GRS (armazenamento Georredundante)
- RA-GRS (armazenamento Georredundante com acesso de leitura). 

Este artigo incida no GRS e RA-GRS. Com a GRS, três cópias dos seus dados são mantidas na região primária que selecionou ao configurar a conta de armazenamento. Três cópias adicionais são mantidas no modo assíncrono numa região secundária especificada pelo Azure. RA-GRS é a mesma coisa como GRS, exceto que tem acesso de leitura para a cópia secundário. Para mais informações sobre as diferentes opções de redundância do armazenamento do Azure, consulte [replicação de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-redundancy). O artigo de replicação também mostra emparelhamentos das regiões do principais e secundários.

Existem fragmentos de código incluídos neste artigo e uma hiperligação para um exemplo completo no final que pode transferir e executar.

## <a name="key-features-of-ra-grs"></a>Principais funcionalidades de RA-GRS

Tenha em atenção estes pontos de chaves ao conceber a sua aplicação para RA-GRS:

* O armazenamento do Azure mantém uma cópia só de leitura dos dados que armazena na sua região primária numa região secundária. Conforme indicado acima, o serviço de armazenamento determina a localização da região secundária.

* A cópia só de leitura é [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para os blobs, tabelas e filas, pode consultar a região secundária para um *hora da última sincronização* valor que indica quando ocorreu a última replicação de principal para a região secundária. (Isto não é suportado para ficheiros do Azure, que não tem de redundância de RA-GRS neste momento.)

* Pode utilizar a biblioteca de clientes de armazenamento para interagir com os dados na região primária ou secundária. Também pode redirecionar pedidos automaticamente para a região secundária de leitura se um pedido de leitura para a região primária exceder o tempo limite.

* Se existir um problema de principais que afetam a acessibilidade dos dados na região primária, a equipa do Azure pode acionar um georreplicação-ativação pós-falha, altura em que as entradas DNS que aponta para a região primária vai ser alteradas para apontar para a região secundária.

* Se ocorrer uma ativação pós-falha georreplicação, Azure irá Selecione uma nova localização secundária e replicar os dados para essa localização, em seguida, aponte as entradas DNS secundárias ao mesmo. O ponto final secundário estará indisponível até que a conta de armazenamento foi concluída a replicar. Para obter mais informações, consulte [o que fazer se ocorrer uma falha de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-disaster-recovery-guidance).

## <a name="application-design-considerations-when-using-ra-grs"></a>Considerações de design da aplicação ao utilizar o RA-GRS

O objetivo deste artigo é para lhe mostrar como estruturar uma aplicação que irá continuar a funcionar (se uma capacidade limitada), mesmo em caso de desastre grave no Centro de dados principal. Pode criar uma aplicação para lidar com problemas transitórios ou execução longa ao ler a partir da região secundária quando houver um problema que interfere com leitura a partir da região primária. Quando a região primária estiver novamente disponível, a aplicação pode devolver a leitura de região primária.

### <a name="using-eventually-consistent-data"></a>Utilizando os dados eventualmente consistentes

A solução proposta parte do princípio de que é aceitável para devolver dados potencialmente obsoletos para a aplicação de chamada. Porque os dados na região secundária estão eventualmente consistentes, é possível que a região primária pode tornar-se inacessível antes de uma atualização para a região secundária terminou a replicar.

Por exemplo, suponha que o cliente submete uma atualização com êxito, mas a região primária falha antes da atualização é propagada para a região secundária. Quando o cliente pede-lhe ler os dados novamente, ele recebe os dados obsoletos da região secundária em vez dos dados atualizados. Ao conceber a sua aplicação, terá de decidir se é aceitável e, se assim for, a forma como irá mensagem do cliente. 

Neste artigo, vamos mostrar como verificar a hora da última sincronização de dados secundários para verificar se o elemento secundário for atualizado.

### <a name="handling-services-separately-or-all-together"></a>Processamento de serviços em separado ou em todos os conjunto

Embora seja pouco provável, é possível para um serviço fiquem indisponíveis enquanto os outros serviços ainda são totalmente funcionais. Pode processar as tentativas e o modo só de leitura para cada serviço separadamente (blobs, filas, tabelas) ou pode processar tentativas genericamente para todos os serviços de armazenamento em conjunto.

Por exemplo, se utilizar as filas e blobs na sua aplicação, pode optar por colocar no código separado para processar erros de repetição para cada um deles. Em seguida, se obter uma nova tentativa do serviço blob, mas o serviço fila ainda está a funcionar, apenas a parte da sua aplicação que processa blobs será afetada. Se optar por processar todas as tentativas de serviço de armazenamento genericamente e uma chamada para o serviço blob devolve um erro não permanente, irão ser afetados pedidos para o serviço blob e o serviço fila.

Em última análise, isto depende a complexidade da sua aplicação. Pode optar por não processar de acordo com as falhas de serviço, mas em vez disso, para redirecionar pedidos para todos os serviços de armazenamento para a região secundária de leitura e executar a aplicação no modo só de leitura quando detetar um problema com a qualquer serviço de armazenamento na região primária.

### <a name="other-considerations"></a>Outras considerações

Estas são as outras considerações, vamos abordar o resto deste artigo.

*   Processamento de tentativas de pedidos de leitura com o padrão de disjuntor automático

*   Os dados eventualmente consistente e a hora da última sincronização

*   Testes

## <a name="running-your-application-in-read-only-mode"></a>Executar a aplicação no modo só de leitura

Para utilizar o armazenamento RA-GRS, tem de ser capaz de lidar com ambos os pedidos falhados de leitura e não conseguiu pedidos de atualização (com atualização neste caso, o que significa introduções, atualizações e eliminações). Se os dados primários center falhar, leia os pedidos podem ser redirecionados para o Centro de dados secundária. No entanto, pedidos de atualização não podem ser redirecionados para o secundário porque o elemento secundário for só de leitura. Por este motivo, terá de criar uma aplicação para ser executado no modo só de leitura.

Por exemplo, pode definir um sinalizador que é verificado antes de quaisquer pedidos de atualização são submetidos ao Storage do Azure. Quando um dos pedidos de atualização é fornecido através de, pode ignorar este e devolver uma resposta adequada ao cliente. Pode ainda pretender desativar algumas funcionalidades Folders até o problema seja resolvido e notificar os utilizadores que essas funcionalidades estão temporariamente indisponíveis.

Se optar por processar erros para cada serviço separadamente, também terá de lidar com a capacidade de executar a sua aplicação no modo só de leitura pelo serviço. Por exemplo, pode ter sinalizadores de só de leitura para cada serviço que podem ser ativados e desativados. Em seguida, pode processar o sinalizador nos locais adequados no seu código.

Ser capaz de executar a sua aplicação no modo só de leitura tem outra vantagem de lado – dá-lhe a capacidade para garantir a funcionalidade limitada durante uma atualização da aplicação principal. Pode acionar a aplicação para ser executado no modo só de leitura e aponte para o Centro de dados secundária, garantindo que ninguém está a aceder aos dados na região primária enquanto estiver a efetuar atualizações.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Processamento de atualizações quando executado no modo só de leitura

Existem várias formas para processar pedidos de atualização quando executado no modo só de leitura. Iremos não abrange isto abrangente, mas geralmente, existem alguns padrões que considerar.

1.  Pode responder ao seu utilizador e informe-o de que não é atualmente aceitar as atualizações. Por exemplo, um sistema de gestão de contacto foi permitir aos clientes aceder a informações de contacto, mas não fazer atualizações.

2.  Pode colocar as atualizações noutra região. Neste caso, teria de escrever os seus pedidos de atualização pendentes para uma fila numa região diferente e, em seguida, ter uma forma de processar esses pedidos depois o Datacenter primário fica online novamente. Neste cenário, deve permitir que o cliente sabe que a atualização pedida é colocada na fila para processamento posterior.

3.  Pode escrever as atualizações para uma conta de armazenamento em noutra região. Em seguida, quando o Centro de dados primária volta a ficar online, pode ter uma forma de intercalação essas atualizações para os dados primários, consoante a estrutura dos dados. Por exemplo, se estiver a criar ficheiros separados com um carimbo de data/hora no nome, pode copiar esses ficheiros novamente para a região primária. Isto funciona para algumas cargas de trabalho, tais como dados de registo e de iOT.

## <a name="handling-retries"></a>Processamento de tentativas

Como fazer a saber que erros são repetição? Isto é determinado pela biblioteca de clientes de armazenamento. Por exemplo, um erro 404 (recurso não encontrado) não é repetição porque não é provável que resultará na êxito repeti-lo. Por outro lado, um erro 500 é repetição porque é um erro de servidor e apenas pode ser um problema transitório. Para obter mais detalhes, veja o [abrir o código de origem para a classe de ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca de cliente de armazenamento do .NET. (Procure o método ShouldRetry).

### <a name="read-requests"></a>Pedidos de leitura

Pedidos de leitura podem ser redirecionados para o armazenamento secundário se houver um problema com o armazenamento principal. Como mencionado acima no [utilizando, eventualmente, os dados consistentes](#using-eventually-consistent-data), tem de ser aceitável para a sua aplicação potencialmente ler os dados obsoletos. Se estiver a utilizar a biblioteca de clientes do storage para aceder aos dados de RA-GRS, pode especificar o comportamento de repetição de um pedido de leitura, definindo um valor para o **LocationMode** propriedade para um dos seguintes:

*   **PrimaryOnly** (predefinição)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Quando configurar o **LocationMode** para **PrimaryThenSecondary**, se o pedido inicial de leitura para a falha de ponto final principal com um erro de repetição, o cliente automaticamente fizer outro pedido de leitura para o ponto final secundário. Se o erro é um tempo limite do servidor, o cliente terá de aguardar que o tempo limite expirar antes de receber um erro não permanente do serviço.

Basicamente, existem dois cenários a considerar quando decidir como reagir a um erro de repetição:

*   Este é um problema isolado e os pedidos subsequentes para o ponto final principal não irão devolver um erro não permanente. É um exemplo de onde isto pode acontecer quando existe um erro de rede transitórios.

    Neste cenário, não há nenhum penalidade de desempenho significativas na ter **LocationMode** definido como **PrimaryThenSecondary** como isto só acontece com pouca frequência.

*   Este é um problema com, pelo menos, um dos serviços de armazenamento na região primária e todos os pedidos subsequentes para esse serviço na região primária são provável que devolvam erros de repetição para um período de tempo. Um exemplo desta situação é a região primária estiver inacessível completamente.

    Neste cenário, não há uma penalidade de desempenho porque todos os seus pedidos de leitura irão tente primeiro o ponto final principal, aguarde que o tempo limite expirar, em seguida, mude para o ponto final secundário.

Nestes cenários, deve identificar que existe um problema com o ponto final primário em curso e enviar todos os pedidos diretamente para o ponto final secundário de leitura, definindo o **LocationMode** propriedade **SecondaryOnly** . Neste momento, também, deve alterar a aplicação para ser executado no modo só de leitura. Esta abordagem é conhecida como o [disjuntor padrão](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Pedidos de atualização

O padrão de disjuntor também pode ser aplicado a pedidos de atualização. No entanto, pedidos de atualização não podem ser redirecionados para o armazenamento secundário, o que é só de leitura. Para esses pedidos, deve deixar o **LocationMode** propriedade definida como **PrimaryOnly** (predefinição). Para resolver estes erros, pode aplicar uma métrica para estes pedidos – por exemplo, 10 falhas numa linha – e quando o limiar for cumprida, a aplicação no modo só de leitura de comutador. Pode utilizar os mesmos métodos para devolver ao atualizar o modo como as descritas abaixo na secção seguinte sobre o padrão de disjuntor automático.

## <a name="circuit-breaker-pattern"></a>Padrão de disjuntor automático

Utilizar o padrão de disjuntor na sua aplicação pode impedir que uma operação que é provável que falhem repetidamente. Permite que a aplicação para continuar a ser executada em vez de demorar tempo enquanto a operação é repetida exponencialmente. Também Deteta quando o índice de falhas correção, em que momento a aplicação pode tentar novamente a operação.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor automático

Para identificar o que há um problema com um ponto de final primário em curso, pode monitorizar a frequência o cliente encontra erros de repetição. Porque cada caso for diferente, terá de decidir o limiar que pretende utilizar para a decisão mude para o ponto final secundário e executar a aplicação no modo só de leitura. Por exemplo, pode optar por efetuar o comutador se existirem 10 falhas numa linha com nenhuma sucessos. Outro exemplo é a mudança para o caso de falha de 90% dos pedidos num período de 2 minutos.

Para o primeiro cenário, pode simplesmente mantenha uma contagem de falhas e se não houver concluído com êxito antes de atingir o máximo, definir a contagem de volta a zero. Para o segundo cenário, uma forma de implementá-las é utilizar o objeto de MemoryCache (.NET). Para cada pedido, adicionar um CacheItem à cache, defina o valor para o êxito (1) ou falha (0) e defina o período de expiração como 2 minutos do agora (ou o que é a restrição de hora). Quando for atingida a hora de expiração de uma entrada, a entrada é removida automaticamente. Isto irá dar-lhe uma graduais janela de 2 minutos. Sempre que efetuar um pedido para o serviço de armazenamento, primeiro utilizar uma consulta de Linq entre o objeto de MemoryCache para calcular a taxa de êxito por cento por summing os valores e dividindo pela contagem. Quando a taxa de êxito por cento descerem abaixo de algumas limiar (por exemplo, 10%), defina o **LocationMode** propriedade para leitura pedidos para **SecondaryOnly** e mudar a aplicação no modo só de leitura antes de continuar.

O limiar de erros utilizado para determinar quando deve efetuar o comutador pode variar de serviços na sua aplicação, pelo que deve considerar a torná-los parâmetros configuráveis. Este é também onde decidir processar erros de repetição de cada serviço em separado ou como um, tal como explicado anteriormente.

Outra consideração é como lidar com várias instâncias de uma aplicação e o que fazer quando detetar erros repetição em cada instância. Por exemplo, pode ter 20 VMs com a mesma aplicação carregada. Para processar cada instância separadamente? Se uma instância inicia a ter problemas, pretende limitar a resposta a apenas que uma instância ou. pretende tentar ter respondem todas as instâncias da mesma forma, quando uma instância tem um problema? As instâncias de processamento separadamente é muito mais simples do que tentar coordenar a resposta em eles, mas como fazê-lo depende da arquitetura da sua aplicação.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções para monitorizar a frequência de erro

Tem três opções principais para a monitorização a frequência de tentativas na região primária para determinar quando deve mude ao longo para a região secundária e alterar as aplicações sejam executadas em modo só de leitura.

*   Adiciona um processador para o [ **repetir** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) evento o [ **OperationContext** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) passar para o armazenamento de objeto pedidos – este é o método apresentado neste artigo e utilizado na amostra associada. Estes eventos acionados sempre que o cliente tenta repetir um pedido, permitindo-lhe controlar com que frequência o cliente encontra erros não permanente de um ponto final principal.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   No [ **Evaluate** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) método uma política de repetição personalizado, pode executar código personalizado sempre que uma nova tentativa ocorre. Para além de gravação quando uma nova tentativa ocorre, isto também dá-lhe a oportunidade para modificar o comportamento de repetição.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

*   A abordagem terceira é implementar um componente de monitorização personalizado na sua aplicação que continuamente efetua ping o ponto final de armazenamento primário com fictício ler pedidos (tais como a leitura de um blob pequeno) para determinar o estado de funcionamento. Isto seria demorar alguns recursos, mas não uma quantidade significativa. Quando um problema é detetado que atingir o limiar, em seguida, executaria o comutador para **SecondaryOnly** e modo só de leitura.

A determinada altura, irá querer voltar ao utilizar o ponto final principal e permitindo que as atualizações. Se utilizar um dos dois primeiros métodos listados acima, pode simplesmente voltar a mudar para o ponto final principal e ativar o modo de atualização depois de ter sido efetuado um período de tempo selecionado arbitrariamente ou número de operações. Em seguida, pode permitir que percorrer a lógica de repetição novamente. Se o problema foi corrigido, continuará a utilizar o ponto final principal e permitir que as atualizações. Se ainda não existe um problema, é mais uma vez irá mudar para o ponto final secundário e o modo só de leitura após os critérios que definiu a falhar.

Para o cenário de terceiro, quando o ponto final de armazenamento primário de envio de ping torna-se com êxito, pode acionar o comutador para **PrimaryOnly** e continuar a permitir que as atualizações.

## <a name="handling-eventually-consistent-data"></a>Processar os dados eventualmente consistentes

RA-GRS funciona ao replicar as transações do primário para a região secundária. Este processo de replicação garante que os dados na região secundária estão *eventualmente consistente*. Isto significa que todas as transações na região primária, eventualmente, serão apresentados na região secundária, mas que pode existir um atraso antes de serem apresentados e que não há nenhuma garantia as transações chegam a região secundária pela mesma ordem que em que estes original de aplicação na região primária. Se as suas transações chegam na região secundária fora de ordem, *poderá* considerar os dados na região secundária para estar num estado inconsistente até que o serviço intercete cópias de segurança.

A tabela seguinte mostra um exemplo de que pode acontecer quando atualizar os detalhes de um funcionário para tornar durante um membro do *administradores* função. Com vista à, neste exemplo, é necessário atualizar o **empregado** entidade e a atualização de um **função de administrador** entidade com uma contagem do número total de administradores. Repare como as atualizações são aplicadas fora de ordem na região secundária.

| **Tempo** | **Transação**                                            | **Replicação**                       | **Hora da última sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação r: <br> Inserir o empregado <br> entidade no principal |                                   |                    | A Inserir primário, de transação<br> não foram replicadas ainda. |
| T1       |                                                            | Transação A <br> replicado para o<br> secundário | T1 | Transação A replicado para o secundário. <br>Hora da última sincronização atualizado.    |
| T2       | Transação b:<br>Atualizar<br> entidade de empregado<br> no site primário  |                                | T1                 | Transação B escrito para o servidor primário,<br> não foram replicadas ainda.  |
| T3       | Transação c:<br> Atualizar <br>Administrador<br>entidade de função no<br>primário |                    | T1                 | Transação C escrito para o servidor primário,<br> não foram replicadas ainda.  |
| *T4*     |                                                       | Transação C <br>replicado para o<br> secundário | T1         | Transação C replicado para o secundário.<br>LastSyncTime não atualizada porque <br>transação B ainda não foram replicada.|
| *T5*     | Entidades de leitura <br>do secundário                           |                                  | T1                 | Obter o valor para o empregado obsoleto <br> entidade porque ainda não transação B <br> ainda replicadas. Obter o novo valor<br> entidade de função de administrador porque tem C<br> replicadas. Hora da última sincronização ainda não<br> foram atualizados porque transação B<br> ainda não replicadas. Pode indicar a<br>entidade de função de administrador está inconsistente <br>porque a entidade data/hora é depois <br>a hora da última sincronização. |
| *T6*     |                                                      | Transação B<br> replicado para o<br> secundário | T6                 | *T6* – tem todas as transações através de C <br>foi replicado, hora da última sincronização<br> é atualizado. |

Neste exemplo, suponha que o cliente muda para ler a partir da região secundária em T5. Pode leu com êxito o **função de administrador** entidade neste momento, mas a entidade contém um valor para a contagem dos administradores que não está consistente com o número de **empregado** entidades são marcado como administradores na região secundária neste momento. O cliente simplesmente foi possível apresentar este valor, com o risco que está a informações inconsistentes. Em alternativa, o cliente pode tentar determinar que o **função de administrador** está num estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem e, em seguida, informam o utilizador do facto deste.

Para reconhecer que tem dados potencialmente inconsistentes, o cliente pode utilizar o valor da *hora da última sincronização* que pode entrar em qualquer altura através de consultas um serviço de armazenamento. Isto indica a hora quando os dados na região secundária foram efetuada a últimos consistente e quando o serviço tinha aplicadas a todas as transações antes desse ponto no tempo. No exemplo mostrado acima, depois do serviço insere a **empregado** entidade na região secundária, a hora da última sincronização está definida como *T1*. Este irá permanecer na *T1* até as atualizações de serviço a **empregado** entidade na região secundária está definido *T6*. Se o cliente obtém a última hora de sincronização quando lê-lo a entidade no *T5*, pode compará-lo com o carimbo na entidade. Se timestamp na entidade é posterior à hora da última sincronização, em seguida, a entidade está num estado inconsistente, potencialmente, e que pode tomar que é a ação adequada para a sua aplicação. Através deste campo requer que conhece quando a última atualização para o site primário foi concluída.

## <a name="testing"></a>Testes

É importante testar a aplicação se comporta conforme esperado quando encontra erros de repetição. Por exemplo, terá de testar a que os comutadores de aplicação para o secundário e no modo só de leitura quando Deteta um problema e comutadores uma quando a região primária fica disponível novamente. Para tal, precisa de uma forma para simular repetição erros e controlo frequência que ocorrem.

Pode utilizar [Fiddler](http://www.telerik.com/fiddler) para intercetar e modificar as respostas HTTP num script. Este script pode identificar as respostas que vêm do ponto final principal e altere o código de estado HTTP para um que reconhece a biblioteca de clientes de armazenamento como um erro não permanente. Este fragmento de código mostra um exemplo simples de um script de Fiddler que interceta respostas ler pedidos contra o **employeedata** tabela para devolver um Estado 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Pode expandir este exemplo de uma vasta gama de pedidos de intercetar e alterar apenas o **responseCode** em algumas para melhor simular um cenário do mundo real. Para obter mais informações sobre como personalizar Fiddler scripts, consulte [modificar um pedido ou resposta](http://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) na documentação do Fiddler.

Se tiver efetuado os limiares para comutar a sua aplicação para o modo só de leitura configurável, será mais fácil testar o comportamento com volumes de transação de não produção.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o acesso de leitura-a redundância geográfica, incluindo outro exemplo de como o LastSyncTime estiver definido, consulte [opções de redundância de armazenamento do Windows Azure e o armazenamento Georredundante com acesso de leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Para um exemplo completo que mostra como efetuar o comutador novamente e estabelecido entre os pontos finais principais e secundários, consulte [exemplos do Azure – com o padrão de disjuntor armazenamento RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
