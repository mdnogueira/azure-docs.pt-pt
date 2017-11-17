---
title: "Computação do Azure - extensão de diagnóstico do Linux | Microsoft Docs"
description: "Como configurar o Azure Linux diagnóstico extensão (LAD) para recolher métricas e eventos de registo de VMs do Linux em execução no Azure."
services: virtual-machines-linux
author: jasonzio
manager: anandram
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: jasonzio
ms.openlocfilehash: ebb963236a069f272499fce59945d0cf0d3d647f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Utilizar a extensão de diagnóstico do Linux para monitorizar métricas e registos

Este documento descreve a versão 3.0 e mais recente da extensão de diagnóstico de Linux.

> [!IMPORTANT]
> Para obter informações sobre a versão mais antiga e 2.3, consulte [neste documento](./classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introdução

Um monitor de utilizador o estado de funcionamento de uma VM com Linux em execução no Microsoft Azure ajuda-o a extensão de diagnóstico do Linux. Tem as seguintes capacidades:

* Recolhe métricas de desempenho do sistema a partir da VM e armazena-os numa tabela específica numa conta do storage designada.
* Obtém o registo de eventos do syslog e armazena-os numa tabela específica na conta do storage designada.
* Permite aos utilizadores personalizar as métricas de dados que são recolhidas e a ser carregadas.
* Permite aos utilizadores personalizar as instalações do syslog e níveis de gravidade dos eventos que são recolhidos e a ser carregados.
* Permite aos utilizadores carregar ficheiros de registo especificado para uma tabela de armazenamento designada.
* Suporta o envio de métricas e registo de eventos para pontos finais de EventHub arbitrários e formatado em JSON blobs na conta do storage designada.

Esta extensão funciona com ambos os modelos de implementação do Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalação da extensão na VM

Pode ativar esta extensão utilizando os cmdlets do Azure PowerShell, scripts da CLI do Azure ou modelos de implementação do Azure. Para obter mais informações, consulte [extensões funcionalidades](./extensions-features.md).

Não é possível utilizar o portal do Azure para ativar ou configurar LAD 3.0. Em vez disso, instala e configura versão 2.3. Gráficos de portais do Azure e alertas de trabalham com dados de ambas as versões da extensão.

Estas instruções de instalação e um [configuração de exemplo transferível](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configurar LAD 3.0 para:

* capturar e armazenar as mesmas métricas que foram fornecidas pelo LAD 2.3;
* captura de um conjunto de métricas de sistema de ficheiros, novos para LAD 3.0; útil
* captura a coleção de syslog predefinido ativada por LAD 2.3;
* Ative a experiência do portal do Azure para charting e alertas nas métricas VM.

A configuração transferível é apenas um exemplo; Modifique-a para se adequarem às suas necessidades.

### <a name="prerequisites"></a>Pré-requisitos

* **Agente Linux do Azure versão 2.2.0 ou posterior**. A maioria das imagens de galeria do Azure VM Linux incluem versão 2.2.7 ou posterior. Executar `/usr/sbin/waagent -version` para confirmar a versão instalada na VM. Se a VM estiver a executar uma versão mais antiga do agente convidado, siga [estas instruções](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) atualizá-lo.
* **CLI do Azure**. [Configurar o 2.0 CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ambiente no seu computador.
* O comando wget, se ainda não tiver: executar `sudo apt-get install wget`.
* Uma subscrição do Azure existente e uma conta de armazenamento existente dentro da mesma para armazenar os dados.

### <a name="sample-installation"></a>Instalação de exemplo

Preencha os parâmetros corretos nos primeiros três linhas, em seguida, execute este script como raiz:

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

O URL para a configuração de exemplo e o respetivo conteúdo, estão sujeitos a alterações. Transfira uma cópia do ficheiro JSON definições do portal e personalizá-lo para as suas necessidades. Qualquer modelos ou automatização que constrói, deve utilizar a sua cópia, em vez de a transferir esse URL cada vez.

### <a name="updating-the-extension-settings"></a>Ao atualizar as definições de extensão

Depois de alterou a sua protegido ou definições público, implementá-las para a VM, executando o mesmo comando. Se nada alterado nas definições, as definições atualizadas são enviadas para a extensão. LAD reloads a configuração e reinicia si próprio.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração a partir de versões anteriores da extensão

A versão mais recente da extensão é **3.0**. **Todas as versões antigas (2) foram preteridas e pode ser anuladas nesta ou após 31 de Julho de 2018**.

> [!IMPORTANT]
> Esta extensão introduz alterações à configuração da extensão. Foi efetuada uma essas alterações para melhorar a segurança da extensão; como resultado, efeitos compatibilidade com 2 não foi possível manter. Além disso, o publicador de extensão para esta extensão é diferente do publicador para as versões de 2. x.
>
> Para migrar a partir de 2. x para esta nova versão da extensão, tem de desinstalar a extensão antiga (sob o nome do publicador antigo) e instale a versão 3 da extensão.

Recomendações:

* Instale a extensão com a atualização de versões de secundárias automática ativada.
  * No modelo de implementação clássica VMs, especifique '3.*' como a versão, se estiver a instalar a extensão através da CLI XPLAT do Azure ou do Powershell.
  * Na implementação do Azure Resource Manager modelo VMs, incluir ' "autoUpgradeMinorVersion": true' no modelo de implementação de VM.
* Utilize uma conta de armazenamento nova/diferente para LAD 3.0. Existem várias incompatibilidades pequenas entre LAD 2.3 e LAD 3.0 que tornam uma conta troublesome de partilha:
  * LAD 3.0 armazena eventos syslog numa tabela com um nome diferente.
  * O counterSpecifier cadeias para `builtin` métricas diferem no LAD 3.0.

## <a name="protected-settings"></a>Definições protegidas

Este conjunto de informações de configuração contém informações confidenciais que devem ser protegidas a partir da vista pública, por exemplo, as credenciais do armazenamento. Estas definições são transmitidas para e armazenadas pela extensão no formato encriptado.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nome | Valor
---- | -----
storageAccountName | O nome da conta de armazenamento na qual os dados são escritos pela extensão.
storageAccountEndPoint | (opcional) O ponto final que identifica a nuvem em que a conta de armazenamento existe. Se esta definição estiver ausente, LAD predefinições para a nuvem pública do Azure, `https://core.windows.net`. Para utilizar uma conta de armazenamento em Datacenters do Azure, Azure Government ou Azure China, defina este valor em conformidade.
storageAccountSasToken | Um [token SAS de conta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para serviços Blob e a tabela (`ss='bt'`), aplica a objetos e contentores (`srt='co'`), que concede adiciona, criar, listar, atualizar e permissões de escrita (`sp='acluw'`). Efetue *não* incluem a leading ponto-de interrogação (?).
mdsdHttpProxy | (opcional) Informações de proxy HTTP necessárias para ativar a extensão para estabelecer ligação com a conta de armazenamento especificado e o ponto final.
sinksConfig | (opcional) Detalhes de destinos alternativos para os quais podem ser fornecidos métricas e eventos. Os detalhes específicos de cada sink de dados suportado pela extensão são abordados nas secções que se seguem.

Pode criar facilmente o necessário token SAS através do portal do Azure.

1. Selecione a conta de armazenamento para fins gerais a que pretende que a extensão de escrita
1. Selecione "Assinatura de acesso partilhado" da parte de definições do menu à esquerda
1. Tornar as secções apropriadas conforme descritas anteriormente
1. Clique no botão "SAS gerar".

![Imagem](./media/diagnostic-extension/make_sas.png)

Copie a SAS gerada para o campo de storageAccountSasToken; remover o ponto de leading de interrogação ("?").

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Esta secção opcional define destinos adicionais para que a extensão envia as informações que recolhe. A matriz de "sink" contém um objeto para cada sink de dados adicionais. O atributo "tipo" determina os outros atributos no objeto.

Elemento | Valor
------- | -----
nome | Uma cadeia utilizada para fazer referência a este sink noutro local na configuração da extensão.
tipo | O tipo de sink que está a ser definido. Determina os outros valores (se aplicável) em instâncias deste tipo.

Versão 3.0 da extensão de diagnóstico Linux suporta dois tipos de receptores: EventHub e JsonBlob.

#### <a name="the-eventhub-sink"></a>O sink de EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

A entrada de "sasURL" contém o URL completo, incluindo o SAS token, para o Hub de eventos para o qual os dados devem ser publicados. LAD requer uma SAS uma política que permite o envio de nomenclatura de afirmação. Um exemplo:

* Criar um espaço de nomes de Event Hubs chamado`contosohub`
* Criar um Hub de eventos no espaço de nomes chamado`syslogmsgs`
* Criar uma política de acesso partilhado no Hub de eventos com o nome `writer` que permite que a afirmação de envio

Se tiver criado uma SAS boa até à meia-noite UTC em 1 de Janeiro de 2018, o valor de sasURL poderá ser:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obter mais informações sobre a criação de tokens SAS para os Event Hubs, consulte [esta página web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>O sink de JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Dados direcionados para um receptor de JsonBlob são armazenados em blobs no armazenamento do Azure. Cada instância de LAD cria um blob a cada hora para cada nome de sink. Cada blob contém sempre uma matriz JSON é sintaticamente válida do objeto. Novas entradas atomically são adicionadas à matriz. Os BLOBs são armazenados num contentor com o mesmo nome como o sink. As regras de armazenamento do Azure para os nomes de contentor do blob são aplicadas aos nomes das JsonBlob sinks: entre 3 e 63 carateres ASCII alfanuméricos minúsculos ou travessões.

## <a name="public-settings"></a>Definições de público

Esta estrutura contém vários blocos de definições que controlam as informações recolhidas pela extensão. Cada definição é opcional. Se especificar `ladCfg`, também tem de especificar `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | Valor
------- | -----
StorageAccount | O nome da conta de armazenamento na qual os dados são escritos pela extensão. Tem de ser o mesmo nome é especificado no [protegidos definições](#protected-settings).
mdsdHttpProxy | (opcional) Mesmo que no [protegidos definições](#protected-settings). O valor público é substituído pelo valor privado, se definir. Colocar as definições de proxy que contenham um segredo, tais como uma palavra-passe no [protegidos definições](#protected-settings).

Os elementos restantes são descritos em detalhe nas secções seguintes.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Controlos esta estrutura opcional a recolha de registos de entrega para o serviço de métricas do Azure e outros dados e métricas sinks. Tem de especificar um `performanceCounters` ou `syslogEvents` ou ambos. Tem de especificar o `metrics` estrutura.

Elemento | Valor
------- | -----
eventVolume | (opcional) Controla o número de partições criadas na tabela de armazenamento. Tem de ser um dos `"Large"`, `"Medium"`, ou `"Small"`. Se não for especificado, o valor predefinido é `"Medium"`.
sampleRateInSeconds | (opcional) O intervalo predefinido entre a coleção de métricas (unaggregated) não processadas. A taxa de menor suportados exemplo é 15 segundos. Se não for especificado, o valor predefinido é `15`.

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | Valor
------- | -----
resourceId | Definir o ID de recurso do Azure Resource Manager, da VM ou da escala de máquina virtual para que a VM pertence. Esta definição tem de ser também especificado se qualquer sink JsonBlob é utilizado na configuração.
scheduledTransferPeriod | A frequência com que métricas agregadas estão a ser calculado e transferidos para o Azure métricas, expresso como um intervalo de tempo é 8601. O período de transferência mais pequeno é 60 segundos, ou seja, PT1M. Tem de especificar pelo menos um scheduledTransferPeriod.

Exemplos das métricas especificados na secção performanceCounters são recolhidos a cada 15 segundos ou na amostra classificar explicitamente definida para o contador. Se forem apresentados vários scheduledTransferPeriod frequências (do exemplo), cada agregação é calculada independentemente.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Esta secção opcional controla a coleção de métricas. Exemplos não processados são agregados para cada [scheduledTransferPeriod](#metrics) para produzir estes valores:

* média
* mínimo
* Máximo
* recolhido o último valor
* Contagem de amostras em bruto utilizadas para calcular o agregado

Elemento | Valor
------- | -----
sinks | (opcional) Uma lista separada por vírgulas dos nomes do sinks para que LAD envia agregado métricos resultados. Todas as métricas agregadas são publicadas em cada sink listada. Consulte [sinksConfig](#sinksconfig). Exemplo: `"EHsink1, myjsonsink"`.
tipo | Identifica o fornecedor real da métrica.
Classe | Em conjunto com "contador", identifica a métrica específica no espaço de nomes do fornecedor.
Contador | Em conjunto com "class", identifica a métrica específica no espaço de nomes do fornecedor.
counterSpecifier | Identifica a métrica específica no espaço de nomes métricas do Azure.
Condição | (opcional) Seleciona uma instância específica do objeto ao qual a métrica aplica-se ou seleciona a agregação em todas as instâncias desse objeto. Para obter mais informações, consulte o [ `builtin` as definições de métrica](#metrics-supported-by-builtin).
sampleRate | É o intervalo de 8601 que define a taxa a que são recolhidos exemplos não processados para esta métrica. Se não definir o intervalo de recolha é definido pelo valor de [sampleRateInSeconds](#ladcfg). A frequência de amostragem suportados mais curta é 15 segundos (PT15S).
unidade | Deve ser um estas cadeias: "Count", "Bytes", "Segundos", "Percentagem", "CountPerSecond", "BytesPerSecond", "Millisecond". Define a unidade para a métrica. Os consumidores dos dados recolhidos esperar que os valores de dados recolhidos para corresponderem esta unidade. LAD ignora este campo.
displayName | A etiqueta (no idioma especificado pela definição de região associada) ligado a estes dados no Azure métricas. LAD ignora este campo.

O counterSpecifier é um identificador arbitrário. Os consumidores de métricas, como charting portal do Azure e alertas funcionalidade, utilize counterSpecifier como a "chave" que identifica uma métrica ou uma instância de uma métrica. Para `builtin` métricas, recomendamos que utilize valores counterSpecifier que começam com `/builtin/`. Se está a recolher uma instância específica de uma métrica, recomendamos que ligue o identificador da instância para o valor de counterSpecifier. Alguns exemplos:

* `/builtin/Processor/PercentIdleTime`-Tempo inativo, Considerando todos os vCPUs
* `/builtin/Disk/FreeSpace(/mnt)`-Espaço para o sistema de ficheiros /mnt
* `/builtin/Disk/FreeSpace`-Espaço Considerando todos os sistemas de ficheiros instalados montados

Nem o LAD como o portal do Azure espera o valor de counterSpecifier para corresponder a qualquer padrão. Ser consistente na forma como pode construir valores de counterSpecifier.

Quando especificar `performanceCounters`, LAD sempre escreve dados para uma tabela no armazenamento do Azure. Pode ter os mesmos dados escritos para JSON blobs e/ou os Event Hubs, mas não é possível desativar o armazenamento de dados para uma tabela. Todas as instâncias de extensão de diagnóstico configurado para utilizar o mesmo nome de conta de armazenamento e ponto final de adicionar os seus registos e as métricas à mesma tabela. Se estiver a escrever demasiados VMs para a mesma partição de tabela, o Azure pode limitar operações de escrita para essa partição. A definição de eventVolume faz com que as entradas ser propagação entre 10 (pequeno), 1 (mediano) ou 100 partições de diferentes (grande). Normalmente, "Médio" é suficiente para garantir que o tráfego não é limitado. A funcionalidade de métricas de Azure do portal do Azure utiliza os dados nesta tabela para produzir gráficos ou para acionar alertas. O nome da tabela é a concatenação estas cadeias de:

* `WADMetrics`
* O "scheduledTransferPeriod" para os valores agregados, armazenados na tabela
* `P10DV2S`
* Uma data, no formato "AAAAMMDD", que altera a cada 10 dias

Os exemplos incluem `WADMetricsPT1HP10DV2S20170410` e `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Esta secção opcional controla a recolha de eventos de registo do syslog. Se a secção for omitida, eventos syslog não são capturados de todo.

A coleção de syslogEventConfiguration tem uma entrada para cada função do syslog de interesse. Se minSeverity "NONE" para uma instalação específica, ou se essa função não aparece no elemento de todo, não há eventos de instalação de que são capturados.

Elemento | Valor
------- | -----
sinks | Uma lista separada por vírgulas dos nomes do sinks ao qual os eventos de registo individuais são publicados. Todos os eventos de registo correspondentes as restrições na syslogEventConfiguration são publicados para cada sink listada. Exemplo: "EHforsyslog"
facilityName | Um nome de função do syslog (tal como "registo\_utilizador" ou "registo\_LOCAL0"). Consulte a secção "instalações" o [página de man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obter uma lista completa.
minSeverity | Um nível de gravidade syslog (tal como "registo\_ERR" ou "registo\_informações"). Consulte a secção "nível de" a [página de man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obter uma lista completa. A extensão de captura eventos enviados para o local de igual ou superior o nível especificado.

Quando especificar `syslogEvents`, LAD sempre escreve dados para uma tabela no armazenamento do Azure. Pode ter os mesmos dados escritos para JSON blobs e/ou os Event Hubs, mas não é possível desativar o armazenamento de dados para uma tabela. O comportamento de criação de partições para esta tabela é igual à descrita para `performanceCounters`. O nome da tabela é a concatenação estas cadeias de:

* `LinuxSyslog`
* Uma data, no formato "AAAAMMDD", que altera a cada 10 dias

Os exemplos incluem `LinuxSyslog20170410` e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Esta secção opcional controla a execução de arbitrários [OMI](https://github.com/Microsoft/omi) consultas.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
Espaço de nomes | (opcional) O espaço de nomes OMI, no qual a consulta deve ser executada. Se não for indicado, o valor predefinido é "raiz/scx", implementado pelo [fornecedores de plataforma do System Center](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
consulta | A consulta OMI para ser executada.
Tabela | (opcional) A tabela de armazenamento do Azure, na conta do storage designada (consulte [protegidos definições](#protected-settings)).
frequência | (opcional) O número de segundos entre a execução da consulta. Valor predefinido é de 300 (5 minutos) valor mínimo é de 15 segundos.
sinks | (opcional) Uma lista separada por vírgulas dos nomes do sinks adicionais para que os resultados de métrica de exemplo em bruto devem ser publicados. Nenhuma agregação destes exemplos em bruto é calculada pela extensão ou por métricas do Azure.

O "tabela" ou "sinks" ou ambos, tem de ser especificados.

### <a name="filelogs"></a>fileLogs

Controla a captura de ficheiros de registo. LAD captura novas linhas de texto que são escritos no ficheiro de e escreve-as linhas da tabela e/ou qualquer sinks especificados (JsonBlob ou EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
ficheiro | Caminho completo do ficheiro de registo sejam observada e capturadas. O nome do caminho tem o nome num único ficheiro; -Não é um diretório de nomes ou conter carateres universais.
Tabela | (opcional) A tabela de armazenamento do Azure, na conta do storage designada (conforme especificado na configuração protegida), na qual são escritas novas linhas de "seguimento" do ficheiro.
sinks | (opcional) Uma lista separada por vírgulas dos nomes do sinks adicionais para os quais as linhas de registo enviadas.

O "tabela" ou "sinks" ou ambos, tem de ser especificados.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas suportadas pelo fornecedor builtin

O fornecedor de métrica de builtin é uma origem de métricas mais interessantes para um conjunto amplo de utilizadores. Estas métricas enquadram-se a cinco classes abrangentes:

* Processador
* Memória
* Rede
* Sistema de ficheiros
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>métricas de Builtin para a classe de processador

A classe de processador de métricas fornece informações sobre a utilização de processador na VM. Ao agregar percentagens, o resultado é a média em todas as CPUs. Numa VM dois vCPU, se um vCPU 100% ocupado e o outro era 100% inativo, o PercentIdleTime comunicado seria 50. Se cada vCPU estava ocupado para o mesmo período a 50%, o resultado que relatados também seria 50. Numa VM quatro vCPU, com um vCPU 100% ocupada e a outras pessoas inativo, o PercentIdleTime comunicado seria 75.

Contador | Significado
------- | -------
PercentIdleTime | Percentagem de tempo durante o período de agregação que processadores foram a executar o ciclo de inatividade de kernel
percentProcessorTime | Percentagem de tempo de execução de um thread não inativo
PercentIOWaitTime | Percentagem de tempo a aguardar que as operações de e/s concluídas
PercentInterruptTime | Percentagem de tempo de execução de hardware/software interrupções e DPCs (chamadas de procedimento diferidas)
PercentUserTime | De tempo não inativo durante a janela de agregação, a percentagem de tempo despendido num utilizador mais com prioridade normal
PercentNiceTime | De tempo de inatividade não, a percentagem despende em lowered prioridade (nice)
PercentPrivilegedTime | De tempo de inatividade não, a percentagem despendido no modo privilegiado (kernel)

Os primeiro quatro contadores devem soma para 100%. A última três também contadores soma para 100%; Estes subdividir a soma de PercentProcessorTime, PercentIOWaitTime e PercentInterruptTime.

Para obter uma métrica único agregada para todos os processadores, defina `"condition": "IsAggregate=TRUE"`. Para obter uma métrica para um processador específico, como o segundo processador lógico de uma VM de quatro vCPU, defina `"condition": "Name=\\"1\\""`. Processador lógico números estão no intervalo `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas de Builtin para a classe de memória

A classe de memória de métricas fornece informações sobre a utilização da memória, paginação e troca.

Contador | Significado
------- | -------
AvailableMemory | Memória física disponível no MiB
PercentAvailableMemory | Memória física disponível como uma percentagem do total de memória
UsedMemory | Na utilização memória física (MiB)
PercentUsedMemory | Em utilização memória física como percentagem do total de memória
PagesPerSec | Paginação total (leitura/escrita)
PagesReadPerSec | Páginas de ler a partir da cópia de arquivo (ficheiro de troca, os ficheiros de programa, ficheiro mapeado, etc.)
PagesWrittenPerSec | Páginas escritas para o arquivo de cópia de segurança (ficheiro de comutação, ficheiro mapeado, etc.)
AvailableSwap | Espaço de comutação utilizado (MiB)
PercentAvailableSwap | Espaço de comutação utilizado como uma percentagem de comutação total
UsedSwap | Na utilização de espaço de comutação (MiB)
PercentUsedSwap | Em utilização de espaço de comutação como uma percentagem de comutação total

Esta classe de métricas tem apenas uma única instância. O atributo "condição" sem outras definições útil e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas de Builtin para a classe de rede

A classe de rede de métricas fornece informações sobre a atividade de rede em interfaces de rede individuais desde o arranque. LAD não expõe as métricas de largura de banda, que podem ser obtidas a partir de métricas de anfitrião.

Contador | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde o arranque
BytesReceived | Total de bytes recebidos desde o arranque
BytesTotal | Total de bytes enviadas ou recebidas desde o arranque
PacketsTransmitted | Total de pacotes enviados desde o arranque
PacketsReceived | Total de pacotes recebidos desde o arranque
TotalRxErrors | Número de recebe erros desde o arranque
TotalTxErrors | Número de erros de transmitir desde o arranque
TotalCollisions | Número de colisões é comunicado pelo portas de rede desde o arranque

 Embora esta classe é instanced, LAD não suporta a captura as métricas da rede agregadas em todos os dispositivos de rede. Para obter as métricas para uma interface específica, tal como eth0, defina `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>métricas de Builtin para a classe de sistema de ficheiros

A classe de sistema de ficheiros de métricas fornece informações sobre a utilização do sistema de ficheiros. Valores absoluto e percentagem são comunicados como serão apresentados ao utilizador comum (não raiz).

Contador | Significado
------- | -------
FreeSpace | Espaço em disco disponível em bytes
UsedSpace | Espaço em disco em bytes utilizado
PercentFreeSpace | Percentagem de espaço livre
PercentUsedSpace | Percentagem de espaço utilizado
PercentFreeInodes | Percentagem de inodes não utilizadas
PercentUsedInodes | Percentagem de inodes alocado (em utilização) somadas em todos os sistemas de ficheiros instalados
BytesReadPerSecond | Bytes lidos por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes lidos ou escritos por segundo
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações por segundo de escrita
TransfersPerSecond | Operações de leitura ou escrita por segundo

Valores agregados em todos os sistemas de ficheiros podem ser obtidos através da definição `"condition": "IsAggregate=True"`. Os valores de um sistema de ficheiros montado específico, tal como "/ mnt", pode ser obtido através da definição `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>métricas de Builtin para a classe de disco

A classe de disco de métricas fornece informações sobre a utilização do dispositivo de disco. Estas estatísticas aplicam-se para a unidade completa. Se existirem vários sistemas de ficheiros num dispositivo, os contadores desse dispositivo estão, efetivamente, agregados em todos eles.

Contador | Significado
------- | -------
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações por segundo de escrita
TransfersPerSecond | Totais operações por segundo
AverageReadTime | Segundos média por operação de leitura
AverageWriteTime | Segundos média por operação de escrita
AverageTransferTime | Segundos média por operação
AverageDiskQueueLength | Número médio de operações de disco em fila
ReadBytesPerSecond | Número de bytes lidos por segundo
WriteBytesPerSecond | Número de bytes escritos por segundo
BytesPerSecond | Número de bytes lidos ou escritos por segundo

Valores agregados em todos os discos podem ser obtidos através da definição `"condition": "IsAggregate=True"`. Para obter informações para um dispositivo específico (por exemplo, dev/sdf1), defina `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalar e configurar LAD 3.0 através da CLI

Pressupondo que as definições de protegidos estão no ficheiro PrivateConfig.json e as suas informações de configuração pública estão a ser PublicConfig.json, execute este comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

O comando assume que está a utilizar o modo de gestão de recursos do Azure (arm) da CLI do Azure. Para configurar LAD para implementação clássica VMs (ASM) do modelo, mude para o modo de "asm" (`azure config mode asm`) e omitir o nome do grupo de recursos no comando. Para obter mais informações, consulte o [documentação da CLI de plataforma](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Uma configuração de exemplo LAD 3.0

Com base nas definições anteriores, eis uma configuração de extensão de LAD 3.0 de exemplo com algumas explicação. Para aplicar este exemplo para o caso, deve utilizar o seu próprio nome de conta de armazenamento, token SAS de conta e os tokens de EventHubs SAS.

### <a name="privateconfigjson"></a>PrivateConfig.json

Configuram estas definições privadas:

* uma conta de armazenamento
* um token SAS conta correspondente
* vários sinks (JsonBlob ou EventHubs com SAS tokens)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Estas definições públicas causam LAD para:

* Carregue as métricas de tempo de processador de percentagem e utilizado--espaço em disco para o `WADMetrics*` tabela
* Carregar mensagens do syslog instalações "utilizador" e a gravidade "informações" para o `LinuxSyslog*` tabela
* Carregar resultados de consulta, em bruto OMI (PercentProcessorTime e PercentIdleTime) para o nomeado `LinuxCPU` tabela
* Carregar anexadas linhas no ficheiro `/var/log/myladtestlog` para o `MyLadTestLog` tabela

Em cada caso, os dados também são carregados para:

* Armazenamento de Blobs do Azure (nome do contentor é definido no receptor de JsonBlob)
* Ponto final de EventHubs (conforme especificado no receptor de EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

O `resourceId` na configuração tem de corresponder ao que de VM ou o dimensionamento da máquina virtual definida.

* Métricas de plataforma do Azure charting e alertas sabe resourceId da VM que está a trabalhar. -Espera localizar os dados para a VM utilizando o resourceId a chave de pesquisa.
* Se utilizar o dimensionamento automático do Azure, resourceId na configuração de dimensionamento automático tem de corresponder ao resourceId utilizado pelo LAD.
* O resourceId está incorporado nos nomes dos JsonBlobs escritas pelo LAD.

## <a name="view-your-data"></a>Ver os seus dados

Utilize o portal do Azure para visualizar os dados de desempenho ou definir alertas:

![Imagem](./media/diagnostic-extension/graph_metrics.png)

O `performanceCounters` sempre os dados são armazenados uma tabela de armazenamento do Azure. APIs de armazenamento do Azure estão disponíveis para vários idiomas e plataformas.

Os dados enviados para JsonBlob sinks são armazenados em blobs na conta de armazenamento com o nome no [protegidos definições](#protected-settings). Pode consumir os dados de blob utilizando as APIs de armazenamento de Blobs do Azure.

Além disso, pode utilizar estas ferramentas de IU para aceder aos dados no armazenamento do Azure:

* Explorador de servidores do Visual Studio.
* [Explorador de armazenamento do Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Explorador de armazenamento do Azure").

Este instantâneo de uma sessão do Explorador de armazenamento do Microsoft Azure mostra o gerado tabelas de armazenamento do Azure e contentores de uma extensão de LAD 3.0 configurada corretamente na VM de teste. A imagem não corresponde exatamente com a [exemplo de configuração LAD 3.0](#an-example-lad-30-configuration).

![Imagem](./media/diagnostic-extension/stg_explorer.png)

Consulte o relevante [EventHubs documentação](../../event-hubs/event-hubs-what-is-event-hubs.md) para aprender a consumir mensagens publicadas para um ponto final EventHubs.

## <a name="next-steps"></a>Passos seguintes

* Criar métricas alertas no [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) para as métricas que recolhe.
* Criar [monitorização gráficos](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para as métricas.
* Saiba como [criar um conjunto de dimensionamento de máquina virtual](/azure/virtual-machines/linux/tutorial-create-vmss) utilizar métricas para controlar o dimensionamento automático.
