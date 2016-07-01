<properties
    pageTitle="Armazenamento de Acesso Esporádico do Azure para Blobs | Microsoft Azure"
    description="As camadas de armazenamento para o Blob Storage oferecem um armazenamento económico para dados de objetos com base em padrões de acesso. O armazenamento de acesso esporádico do Azure está otimizado para dados que são acedidos com menor frequência."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Blob Storage do Azure: Camadas de Armazenamento de Acesso Frequente e Esporádico

## Descrição geral

Agora, o Storage do Azure disponibiliza duas camadas de armazenamento para o Blob Storage (armazenamento de objetos), para que possa armazenar os seus dados de forma mais económica consoante o modo como os utiliza. A **camada de armazenamento de acesso frequente** do Azure está otimizada para armazenar dados que são acedidos com frequência. A **camada de armazenamento de acesso esporádico** do Azure está otimizada para armazenar dados que são acedidos com pouca frequência e de longa duração. Os dados na camada de armazenamento de acesso esporádico podem tolerar uma disponibilidade ligeiramente inferior, mas ainda requerem uma durabilidade elevada, bem como tempo de acesso e características de débito semelhantes aos dados de acesso frequente. Para os dados de acesso esporádico, um SLA de disponibilidade ligeiramente inferior e custos de acesso superiores são compromissos aceitáveis em troca de custos de armazenamento muito inferiores.

Atualmente, os dados armazenados na nuvem estão a crescer a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado. Os dados armazenados na nuvem podem ser bastante diferentes em termos da forma como são gerados, processados e acedidos ao longo da respetiva duração. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos com muita frequência no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na nuvem e são raramente acedidos após serem armazenados ou não são acedidos de todo.

Cada um dos cenários de acesso a dados descritos acima beneficia de uma camada diferenciada de armazenamento, otimizada para um padrão de acesso específico. Agora, com a introdução das camadas de acesso frequente e esporádico ao armazenamento, o Blob Storage do Azure dá resposta a esta necessidade de camadas de armazenamento diferenciadas com modelos de preços distintos.

## Contas do Blob Storage

As **Contas do Blob Storage** são contas do Storage especializadas para armazenar os dados não estruturados como blobs (objetos) no Storage do Azure. Com as contas do Blob Storage, pode escolher agora entre camadas de acesso frequente e esporádico ao armazenamento para armazenar os dados acedidos com menor frequência com um custo de armazenamento inferior e armazenar os dados acedidos com maior frequência com um menor custo de acesso. As contas do Blob Storage são semelhantes às suas contas do Storage para fins gerais existentes e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho que utiliza atualmente, incluindo 100% de consistência com a API para blobs de blocos e blobs de acréscimo.

> [AZURE.NOTE] As contas do Blob Storage suportam apenas blobs de blocos e de acréscimo e não suportam blobs de páginas.

As contas do Blob Storage expõem o atributo **Access Tier**, que permite especificar a camada de armazenamento como de acesso **Frequente** ou **Esporádico**, consoante os dados armazenados na conta. Se o padrão de utilização dos dados sofrer uma alteração, também pode alternar entre estas camadas de acesso em qualquer altura.

> [AZURE.NOTE] A alteração da camada de acesso poderá resultar em encargos adicionais. Consulte a secção [Preços e Faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

Os exemplos de cenários de utilização da camada de acesso frequente ao armazenamento incluem:

- Dados que estão a ser utilizados ativamente ou que deverão ser acedidos (para operações de leitura e escrita) com frequência.
- Dados preparados para processamento e eventual migração para a camada de armazenamento de acesso esporádico.

Os exemplos de cenários de utilização da camada de acesso de armazenamento de acesso esporádico incluem:

- Conjuntos de dados de cópia de segurança, arquivo e recuperação após desastre.
- Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
- Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)
- Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,*, ficheiros de multimédia não processados após a transcodificação para outros formatos)
- Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo,*, filmagens de câmaras de segurança, raios X/RMs antigos para organizações de cuidados de saúde, gravações de áudio e transcrições de chamadas dos clientes para serviços financeiros)

Consulte [Acerca das contas do Storage do Azure](storage-create-storage-account.md) para obter mais informações sobre contas do Storage.

Para as aplicações que requerem apenas o Blob Storage de blocos ou de acréscimo, recomendamos que utilize contas do Blob Storage, de modo a tirar partido do modelo de preços diferenciado do armazenamento em camadas. No entanto, compreendemos que tal pode não ser possível em determinadas circunstâncias em que a utilização de contas do Storage para fins gerais seria mais adequada, tais como:

- Tem de utilizar tabelas, filas ou ficheiros e pretende que os blobs estejam armazenados na mesma conta do Storage. Tenha em atenção que o armazenamento destes elementos na mesma conta não tem qualquer vantagem técnica além da utilização das mesmas chaves partilhadas.
- Ainda tem de utilizar o modelo de implementação clássica. As contas do Blob Storage estão disponíveis apenas através do modelo de implementação Azure Resource Manager.
- Tem de utilizar blobs de páginas. As contas do Blob Storage não suportam blobs de páginas. Normalmente, recomendamos que utilize blobs de blocos, a menos que necessite especificamente de blobs de páginas.
- Utiliza uma versão da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior a 14/02/2014 ou uma biblioteca de cliente com uma versão inferior à 4.x e não pode atualizar a sua aplicação.

> [AZURE.NOTE] Atualmente, as contas do Blob Storage são suportadas na maioria das regiões do Azure e serão adicionadas mais regiões no futuro. Pode encontrar a lista atualizada de regiões disponíveis na página [Serviços do Azure por Região](https://azure.microsoft.com/regions/#services).

## Comparação das camadas de acesso

A tabela seguinte realça a comparação entre as duas camadas de acesso:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Camada de acesso frequente ao armazenamento</center></strong></td>
    <td><strong><center>Camada de acesso esporádico ao armazenamento</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilidade</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidade<br>(leituras de RA-GRS)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Custos de utilização</center></strong></td>
    <td><center>Custos de armazenamento superiores<br>Custos de acesso e transação inferiores</center></td>
    <td><center>Custos de armazenamento inferiores<br>Custos de acesso e transação superiores</center></td>
</tr>
<tr>
    <td><strong><center>Tamanho mínimo do objeto<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Duração de armazenamento mínima<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Latência<br>(Tempo até ao primeiro byte)<center></strong></td>
    <td colspan="2"><center>milissegundos</center></td>
</tr>
<tr>
    <td><strong><center>Metas de escalabilidade e desempenho<center></strong></td>
    <td colspan="2"><center>Igual às contas do Storage para fins gerais</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] As contas do Blob Storage suportam as mesmas metas de desempenho e escalabilidade que as contas do Storage para fins gerais. Consulte [Metas de Desempenho e Escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter mais informações.

## Preços e Faturação

As contas do Blob Storage utilizam um novo modelo de preços para o Blob Storage com base na camada de acesso. Ao utilizar uma conta do Blob Storage, aplicam-se as seguintes considerações de faturação:

- **Custos de armazenamento**: para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de acesso. O custo por gigabyte é menor para a camada de acesso esporádico ao armazenamento do que para a camada de acesso frequente ao armazenamento.
- **Custos do acesso a dados**: para os dados na camada de acesso esporádico ao armazenamento, será cobrada uma taxa de acesso a dados por gigabyte pelas operações de leitura e escrita.
- **Custos de transação**: é cobrada uma taxa por transação para ambas as camadas. No entanto, o custo por transação para a camada de acesso esporádico ao armazenamento é superior ao da camada de acesso frequente ao armazenamento.
- **Custos de transferência de dados de georreplicação**: aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
- **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.
- **Alteração da camada de acesso**: a alteração da camada de acesso de acesso esporádico para acesso frequente está sujeita à uma cobrança de uma taxa igual à leitura de todos os dados existentes na conta do Storage para cada transição. Por outro lado, a alteração da camada de acesso de acesso frequente para acesso esporádico será gratuita.

> [AZURE.NOTE] Para permitir que os utilizadores experimentem as novas camadas de armazenamento e validem a funcionalidade após o lançamento, a taxa associada à alteração da camada de acesso de acesso esporádico para acesso frequente não será cobrada até 30 de junho de 2016. A partir de 1 de julho de 2016, a taxa será aplicada a todas as transições de acesso esporádico para acesso frequente. Para obter mais detalhes sobre o modelo de preços para as contas do Blob Storage, consulte a página [Preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais detalhes sobre as taxas aplicáveis às transferências de dados de saída, consulte a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## Guia de Introdução

Nesta secção, iremos demonstrar os seguintes cenários através do Portal do Azure:

- Como criar uma conta do Blob Storage
- Como gerir uma conta do Blob Storage.

### Utilizar o Portal do Azure

#### Criar uma conta do Blob Storage através do Portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** -> **Dados + Armazenamento** -> **Conta do Storage**.

3. Introduza um nome para a conta do Storage.

4. Selecione **Resource Manager** como o modelo de implementação.

5. Selecione **Blob Storage** como o tipo de conta do Storage.

6. Selecione a camada de acesso: **Frequente** ou **Esporádico**. A predefinição é **Frequente**.

7. Selecione a opção de replicação para a conta do Storage: **LRS**, **GRS** ou **RA-GRS**. A predefinição é **RA-GRS**. Consulte [Replicação do Storage do Azure](storage-redundancy.md) para obter detalhes adicionais sobre as opções de replicação do Storage do Azure.

8. Selecione a subscrição na qual pretende criar a nova conta do Storage.

9. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações acerca dos grupos de recursos, consulte [Utilizar o Portal do Azure para gerir os recursos do Azure](../azure-portal/resource-group-portal.md).

10. Selecione a localização geográfica para a conta do Storage.

11. Clique em **Criar** para criar a conta do Storage.

#### Alterar a camada de acesso numa conta do Blob Storage através do Portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e navegue até à sua conta do Storage.

2. Clique em **Todas as definições** e, em seguida, clique em **Configuração** para ver e/ou alterar a configuração da conta.

3. Especifique a camada de acesso pretendida: **Frequente** ou **Esporádico**.

    > [AZURE.NOTE] A alteração da camada de acesso poderá resultar em encargos adicionais. Consulte a secção [Preços e Faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

## Migrar para contas do Blob Storage

O objetivo desta secção consiste em ajudar os utilizadores a fazerem uma transição tranquila para as contas do Blob Storage. Uma conta do Blob Storage é especializada para o armazenamento apenas de blobs de blocos e de acréscimo. Não é possível converter as contas do Storage para fins gerais existentes, que permitem armazenar tabelas, filas, ficheiros, discos e blobs, para contas do Blob Storage. Para utilizar as camadas de armazenamento, terá de criar novas contas do Blob Storage e migrar os dados existentes para as contas recém-criadas.

### Planear a migração dos dados existentes

Se estiver a mover os dados para uma conta do Blob Storage, provavelmente pretende tirar partido da camada de acesso esporádico ao armazenamento para diminuir os custos de armazenamento dos dados utilizados com menor frequência. O primeiro passo no planeamento da migração dos dados numa conta do Blob Storage na camada de acesso esporádico ao armazenamento consiste em avaliar o seu padrão de utilização existente para determinar se a migração para uma conta do Blob Storage lhe trará benefícios. Em geral, precisa de saber o seguinte:

- O padrão de consumo de armazenamento: que quantidade de dados está a ser armazenada e como é que esta muda mensalmente?
- Os padrões de acesso ao armazenamento: que quantidade de dados é lida da conta e escrita na mesma (incluindo dados novos)? Quantas transações são utilizadas para o acesso a dados e qual é o tipo dessas transações?

Para monitorizar as contas do Storage existentes e recolher estes dados, consulte [Ativar métricas do Storage do Azure e ver dados de métricas](storage-enable-and-view-metrics.md). Agora, utilizando estes dados, pode utilizar a [Calculadora de Preços do Storage do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para o ajudar a calcular os custos.

### Migração de dados existentes

Pode utilizar os métodos seguintes para migrar os dados existentes para contas do Blob Storage a partir de dispositivos de armazenamento no local, fornecedores de armazenamento na nuvem externos ou das suas contas do Storage para fins gerais existentes no Azure:

#### AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Pode utilizar o AzCopy para copiar dados para a sua conta do Blob Storage a partir das suas contas do Storage para fins gerais existentes ou para carregar dados dos dispositivos de armazenamento no local para a conta do Blob Storage.

Para obter mais detalhes, consulte [Transferir dados com o utilitário de linha de comandos AzCopy](storage-use-azcopy.md).

#### Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Storage do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples semelhantes às do AzCopy. Isto permite-lhe tirar o máximo partido das funcionalidades fornecidas pelo AzCopy na sua aplicação de forma nativa, sem ter de executar e monitorizar instâncias externas do AzCopy.

Para obter mais detalhes, consulte [Biblioteca de Movimento de Dados do Storage do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

#### API REST ou Biblioteca de Cliente

Pode criar uma aplicação personalizada para migrar os dados para uma conta do Blob Storage utilizando uma das bibliotecas de cliente do Azure ou a API REST dos serviços do Storage do Azure. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais detalhes, consulte [Introdução ao Blob Storage do Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação armazenados com o blob. É fundamental que os mecanismos de cópia utilizados assegurem que os metadados do blob, e, em especial, os metadados relacionados com a encriptação, são preservados. Se copiar os blobs sem estes metadados, não será possível obter novamente o conteúdo do blob. Para obter mais detalhes acerca dos metadados relacionados com a encriptação, consulte [Encriptação do lado do cliente do Storage do Azure](storage-client-side-encryption.md).

## Perguntas mais frequentes

1. **As contas do Storage existentes continuam disponíveis?**

    Sim, as contas do Storage existentes continuam disponíveis e permanecem inalteradas em termos de preços e funcionalidade.  Não têm a capacidade de escolher uma camada de acesso e não terão capacidades de camadas no futuro.

2. **Por que razão devo começar a utilizar as contas do Blob Storage e quando o devo fazer?**

    As contas do Blob Storage são especializadas para armazenar blobs e permitem-nos introduzir novas funcionalidades centradas em blobs. Doravante, as contas do Blob Storage são a forma recomendada de armazenar blobs, uma vez que serão introduzidas capacidades futuras tais como o armazenamento hierárquico e capacidades de camadas com base neste tipo de conta. No entanto, o momento de migração fica ao seu critério com base nos seus requisitos empresariais.

3. **Posso converter a minha conta do Storage existente numa conta do Blob Storage?**

    Não. A conta do Blob Storage é um tipo diferente de conta do Storage e terá de a criar de raiz e migrar os dados tal como explicado anteriormente.

4. **Posso armazenar objetos em ambas as camadas de acesso na mesma conta?**

    O atributo da camada de acesso é definido ao nível de uma conta e aplica-se a todos os objetos dessa conta. Não é possível definir a camada de acesso ao nível do objeto.

5. **Posso alterar a camada de acesso da minha conta do Blob Storage?**

    Sim. Poderá alterar a camada de acesso na conta do Storage. A alteração da camada de acesso ao nível de uma conta aplicar-se-á a todos os objetos armazenados na conta. A alteração da camada de acesso de acesso frequente para acesso esporádico será gratuita, enquanto a alteração de acesso esporádico para acesso frequente estará sujeita a um custo por GB para a leitura de todos os dados na conta.

6. **Com que frequência posso alterar a camada de acesso na minha conta do Blob Storage?**

    Apesar de não impormos uma limitação à frequência com que é possível alterar a camada de acesso, tenha em atenção que a alteração da camada de acesso de acesso esporádico para acesso frequente resultará em encargos significativos. Não recomendamos que altere a camada de acesso com frequência.

7. **Os blobs na camada de acesso esporádico ao armazenamento terão um comportamento diferente dos blobs na camada de acesso frequente ao armazenamento?**

    Os blobs na camada de acesso frequente ao armazenamento têm a mesma latência que os blobs em contas do Storage para fins gerais. Os blobs na camada de acesso esporádico ao armazenamento têm uma latência semelhante (em milissegundos) aos blobs em contas do Storage para fins gerais.

    Os blobs na camada de acesso esporádico ao armazenamento terão um nível de serviço (SLA) de disponibilidade ligeiramente inferior que os blobs armazenados na camada de acesso frequente ao armazenamento. Para obter mais detalhes, consulte [SLA para o armazenamento](https://azure.microsoft.com/support/legal/sla/storage).

8. **Posso armazenar blobs de páginas e discos da máquinas virtuais em contas do Blob Storage?**

    As contas do Blob Storage suportam apenas blobs de blocos e de acréscimo e não suportam blobs de páginas. Os discos de Virtual Machines do Azure são apoiados por blobs de páginas e, como consequência, não é possível utilizar contas do Blob Storage para armazenar discos de máquinas virtuais. No entanto, é possível armazenar cópias de segurança dos discos de máquinas virtuais como blobs de blocos numa conta do Blob Storage.

9. **Terei de alterar as minhas aplicações existentes para utilizar as contas do Blob Storage?**

    As contas do Blob Storage são 100% consistentes com a API com as contas do Storage para fins gerais para blobs de blocos e de acréscimo. Desde que a aplicação utilize blobs de blocos ou blobs de acréscimo e esteja a utilizar a versão de 14/02/2014 da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou superior, a aplicação deverá funcionar normalmente. Se estiver a utilizar uma versão mais antiga do protocolo, terá de atualizar a sua aplicação para utilizar a nova versão, de modo a trabalhar de forma totalmente integrada com ambos os tipos de contas do Storage. Em geral, recomendamos sempre que utilize a versão mais recente, independentemente da conta do Storage utilizada.

10. **Haverá alguma alteração à experiência de utilizador?**

    As contas do Blob Storage são muito semelhantes às contas do Storage para fins gerais para armazenar blobs de blocos e de acréscimo e suportam todas as principais funcionalidades do Storage do Azure, incluindo elevada durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Com exceção das funcionalidades e restrições específicas das contas do Blob Storage e das respetivas camadas de acesso que foram descritas acima, tudo o resto permanece igual.

## Passos seguintes

### Avaliar as contas do Blob Storage

[Verificar a disponibilidade das contas do Blob Storage por região](https://azure.microsoft.com/regions/#services)

[Avaliar a utilização das suas contas do Storage atuais ao ativar as métricas do Storage do Azure](storage-enable-and-view-metrics.md)

[Verificar os preços do Blob Storage por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)

### Começar a utilizar as contas do Blob Storage

[Introdução ao Blob Storage do Azure](storage-dotnet-how-to-use-blobs.md)

[Mover dados de e para o Storage do Azure](storage-moving-data.md)

[Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)



<!--HONumber=Jun16_HO2-->


