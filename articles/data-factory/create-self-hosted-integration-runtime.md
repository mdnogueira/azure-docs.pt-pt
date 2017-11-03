---
title: "Criar o tempo de execução de integração personalizada alojada no Azure Data Factory | Microsoft Docs"
description: "Saiba como criar o tempo de execução de integração personalizada alojada no Azure Data Factory, o que lhe permite fábricas de dados aceder aos arquivos de dados de uma rede privada."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 63e4bb600d053a43c500b601a3942eb96ac16b07
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Como criar e configurar o tempo de execução do Self-hosted integração
O tempo de execução de integração (IR) é a infraestrutura de computação utilizada pelo Azure Data Factory para fornecer capacidades de integração de dados entre ambientes de rede diferentes. Para obter detalhes sobre a resposta a incidentes, consulte [descrição geral de tempo de execução de integração](concepts-integration-runtime.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [Data Factory version 1 documentation](v1/data-factory-introduction.md) (Documentação da versão 1 do Data Factory).

Um tempo de execução automática alojada integração é capaz de executar atividades de cópia entre um dados em nuvem arquivos e um arquivo de dados na rede privada e a distribuição de atividades de transformação contra recursos de computação no local ou de rede Virtual do Azure. Instale necessidades de integração personalizada alojada de tempo de execução de uma máquina no local ou uma máquina virtual no interior de uma rede privada.  

Este documento apresenta a forma como pode criar e configurar Self-hosted IR.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Passos de alto nível para instalar IR personalizada alojada
1.  Crie um tempo de execução de integração personalizada alojada. Eis um exemplo do PowerShell:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Transfira e instale o runtime de integração personalizada alojada (no computador local).
3.  Obter a chave de autenticação e registe o tempo de execução automática alojada integração com a chave. Eis um exemplo do PowerShell:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Fluxo de comando e o fluxo de dados
Quando move os dados no local e na nuvem, a atividade utiliza um tempo de execução automática alojada integração para transferir os dados da origem de dados no local para se nuvem e vice-versa.

Eis um fluxo de dados de alto nível para o resumo dos passos para a cópia com IR personalizada alojada:

![Descrição geral de alto nível](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Programador de dados cria um tempo de execução automática alojada integração dentro de um Azure data factory utilizando um cmdlet do PowerShell. Atualmente, o portal do Azure não suporta esta funcionalidade.
2. Programador de dados cria um serviço ligado para um arquivo de dados no local, especificando a instância de tempo de execução de integração personalizada alojada que deve utilizar para ligar aos arquivos de dados. Como parte da configuração de serviço ligado, o Programador de dados utiliza a aplicação Gestor de credenciais (atualmente, não suportada) para definir os tipos de autenticação e credenciais. A caixa de diálogo de aplicação de Gestor de credenciais comunica com o arquivo de dados para testar a ligação e o tempo de execução automática alojada integração para guardar as credenciais.
4.  Nó de tempo de execução de integração personalizada alojada encripta as credenciais utilizando o Windows Data Protection Application Programming Interface (DPAPI) e guarda-os localmente. Se vários nós são definidos para elevada disponibilidade, as credenciais mais são sincronizadas em todos os outros nós. Cada nó encripta-la utilizando a DPAPI e armazena-la localmente. Sincronização de credenciais é transparente para o Programador de dados e é processada pelo IR. personalizada alojada    
5.  Serviço de fábrica de dados comunica com o tempo de execução automática alojada integração para agendamento e a gestão de tarefas através de **canal de controlo** que utiliza uma fila de barramento de serviço do Azure partilhado. Quando uma tarefa de atividade tem de ser executado, o Data Factory fila o pedido, juntamente com informações de credenciais (caso as credenciais já não são armazenadas no tempo de execução automática alojada integração). Tempo de execução automática alojada integração arranca, a tarefa depois da fila de consulta.
6.  Tempo de execução automática alojada integração copia dados a partir de um arquivo local para um armazenamento na nuvem, ou vice versa dependendo de como a atividade de cópia é configurada no pipeline de dados. Para este passo, o tempo de execução automática alojada integração comunica diretamente com os serviços de armazenamento baseado em nuvem, como o armazenamento de Blobs do Azure através de um canal seguro do (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Considerações sobre a utilização de resposta a incidentes personalizada alojada

- Um tempo de execução de integração personalizada alojada única pode ser utilizado para várias origens de dados no local. No entanto, um **tempo de execução única integração personalizada alojada está associado a apenas um Azure data factory** e não pode ser partilhado com outro data factory.
- Pode ter **apenas uma instância do tempo de execução automática alojada integração** instaladas num único computador. Suponhamos, tiver dois fábricas de dados que precisem de aceder a origens de dados no local, terá de instalar o runtime de integração personalizada alojada em dois computadores no local. Por outras palavras, um tempo de execução automática alojada integração está associado a uma fábrica de dados específico
- O **integração personalizada alojada runtime não precisa de estar no mesmo computador como origem de dados**. No entanto, ter autoalojado integração runtime próximo à origem de dados reduz o tempo para o tempo de execução automática alojada integração ligar à origem de dados. Recomendamos que instale o runtime de integração personalizada alojada num computador que é diferente da que essa origem de dados no local anfitriões. Quando a origem de dados e de tempo de execução de integração personalizada alojada em computadores diferentes, o tempo de execução automática alojada integração não competem para ter recursos com a origem de dados.
- Pode ter **vários tempos de execução automática alojada integração em computadores diferentes, ligar à mesma origem de dados no local**. Por exemplo, pode ter dois integração personalizada alojada em tempo de execução que servem dois fábricas de dados mas a mesma origem de dados no local é registada em ambas as fábricas de dados.
- Se já tiver um gateway instalado no seu servir de computador um **Power BI** cenário, instale um **separado integração personalizada alojada runtime para o Azure Data Factory** noutra máquina.
- Tempo de execução automática alojada integração tem de ser utilizado para suportar a integração de dados dentro da rede Virtual do Azure.
- Tratar a origem de dados como uma origem de dados no local (que é protegido por uma firewall), mesmo quando utilize **ExpressRoute**. Utilize o tempo de execução automática alojada integração para estabelecer conectividade entre o serviço e a origem de dados.
- Tem de utilizar o runtime de integração personalizada alojada, mesmo que esteja o arquivo de dados na nuvem num **máquina virtual IaaS do Azure**.

## <a name="prerequisites"></a>Pré-requisitos

- Suportado **sistema operativo** versões são o Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Instalação do tempo de execução integração personalizada alojada num **controlador de domínio não é suportado**.
- **.NET framework 4.6.1 ou posterior** é necessária. Se estiver a instalar o runtime de integração personalizada alojada num computador Windows 7, instale o .NET Framework 4.6.1 ou posterior. Consulte [requisitos de sistema do .NET Framework](/dotnet/framework/get-started/system-requirements) para obter mais detalhes.
- O recomendada **configuração** para o tempo de execução automática alojada integração máquina é, pelo menos, 2 GHz, 4 núcleos, 8 GB de RAM e o disco de 80 GB.
- Se o computador anfitrião hibernates, o tempo de execução automática alojada integração não responder a pedidos de dados. Por conseguinte, configure uma esquema de energia adequadas no computador antes de instalar o runtime de integração personalizada alojada. Se o computador está configurado para hibernação, a instalação de tempo de execução automática alojada integração pede-lhe uma mensagem.
- Tem de ser um administrador no computador para instalar e configurar o tempo de execução automática alojada integração com êxito.
- Como ocorrem execuções de atividade de cópia numa frequência específica, a utilização de recursos (CPU, memória) na máquina segue também o mesmo padrão das horas de ponta e tempos de inatividade. Utilização de recursos também depende descontos elevados a quantidade de dados a ser movidos. Quando várias tarefas de cópia estão em curso, pode ver a utilização de recursos, aceda a cópia de segurança durante períodos máximos.

## <a name="installation-best-practices"></a>Melhores práticas de instalação
Tempo de execução automática alojada integração pode ser instalado ao transferir um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte [mover dados entre no local e na nuvem artigo](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure o esquema de energia no computador anfitrião para o tempo de execução automática alojada integração para que a máquina não hibernação. Se o computador anfitrião hibernates, o tempo de execução automática alojada integração fica offline.
- Cópia de segurança as credenciais associadas com o tempo de execução automática alojada integração regularmente.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Instalar e registar IR Self-hosted do Centro de transferências

1. Navegue para [página de transferência de Runtime de integração do Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **transferir**, selecione a versão adequada (**32-bit** vs. **64-bit**) e clique em **seguinte**.
3. Execute o **MSI** diretamente ou guarde-o para o disco rígido e a execução.
4. No **boas-vindas** página, selecione um **idioma** clique **seguinte**.
5. **Aceitar** o contrato de licença de utilizador final e clique em **seguinte**.
6. Selecione **pasta** para instalar o tempo de execução automática alojada integração e clique em **seguinte**.
7. No **pronto para instalar** página, clique em **instalar**.
8. Clique em **concluir** para concluir a instalação.
9. Obter a chave de autenticação com o Azure PowerShell. Exemplo do PowerShell para obter a chave de autenticação:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. No **registar integração tempo de execução (autoalojado)** página do Microsoft integração Runtime Configuration Manager em execução no seu computador, efetue os seguintes passos:
    1. Colar o **chave de autenticação** na área de texto.
    2. Opcionalmente, clique em **chave de autenticação de mostrar** para ver o texto da chave.
    3. Clique em **registar**.


## <a name="high-availability-and-scalability"></a>Elevada disponibilidade e escalabilidade
Um tempo de execução de integração Self-hosted pode ser associateed para várias máquinas no local. Estas máquinas são denominadas nós. Pode ter até quatro nós associado um tempo de execução de integração Self-hosted. Os benefícios de ter vários nós (máquinas no local com o gateway instalado) para um gateway lógico são:
1. Maior disponibilidade de um tempo de execução de Self-hosted integração para que já não esteja o ponto único de falha na sua integração de dados de macrodados solução ou na nuvem, com o Azure Data Factory, garantindo a continuidade com até 4 nós.
2. Desempenho e débito de melhorados durante o movimento de dados no local e na nuvem arquivos de dados. Obter mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Pode associar vários nós instalando o software de tempo de execução de integração de Self-hosted simplesmente o [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=39717) e registando-lo por qualquer um dos obtainined chaves de autenticação do Cmdlet de novo AzureRmDataFactoryV2IntegrationRuntimeKey conforme descrito no [Tutorial](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Não é necessário criar um novo Runtime de integração Self-hosted para associar cada nó.

## <a name="system-tray-icons-notifications"></a>Os ícones do tabuleiro de sistema / notificações
Se mova o cursor sobre a mensagem de notificação/ícone de tabuleiro de sistema, pode encontrar os detalhes sobre o estado do tempo de execução automática alojada integração.

![Notificações de tabuleiro do sistema](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portas e firewall
Existem duas firewalls precisa de considerar: **firewall empresarial** em execução no router central da organização, e **firewall do Windows** configurado como um daemon no computador local onde o tempo de execução automática alojada integração está instalado.

![Firewall](media\create-self-hosted-integration-runtime\firewall.png)

Em **firewall empresarial** nível, tem de configurar as portas de saída e domínios seguintes:

Nomes de domínio | Portas | Descrição
------------ | ----- | ------------
*. servicebus.windows.net | 443, 80 | Utilizado para comunicação com o back-end do serviço de movimento de dados
*. core.windows.net | 443 | Utilizado para cópia de teste utilizando o Blob do Azure (se configurada)
*. frontend.clouddatahub.net | 443 | Utilizado para comunicação com o back-end do serviço de movimento de dados

Em **firewall do Windows** nível (nível de computador), estas portas de saída normalmente estão ativadas. Se não, pode configurar as portas em conformidade no autoalojado e domínios máquina de tempo de execução de integração.

> [!NOTE]
> Com base na sua origem / sinks, poderá ter de domínios adicionais da lista branca e portas de saída na firewall empresarial/Windows.
>
> Para algumas bases de dados de nuvem (por exemplo: SQL Database do Azure, Azure Data Lake, etc.), poderá ter de endereço IP da lista branca de máquina de tempo de execução de integração personalizada alojada na respetiva configuração da firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados a partir de uma origem para um receptor de
Certifique-se de que as regras de firewall estão ativadas corretamente da firewall da empresa, firewall do Windows na máquina de tempo de execução automática alojada integração, e os dados de arquivo em si. Ativar estas regras, permite que o tempo de execução automática alojada integração ligar à origem de ambos e sink com êxito. Ative regras para cada arquivo de dados que esteja envolvido na operação de cópia.

Por exemplo, a cópia a partir de um **do arquivo de dados no local para um receptor de SQL Database do Azure ou um receptor de Azure SQL Data Warehouse**, efetue os seguintes passos:

- Permitir saída **TCP** comunicações na porta **1433** para firewall da empresa e a firewall do Windows.
- Configure as definições de firewall do servidor SQL do Azure para adicionar o endereço IP da máquina de tempo de execução automática alojada integração à lista de endereços IP permitidos.

> [!NOTE]
> Se a firewall não permite a saída da porta é 1433, tempo de execução de integração personalizada alojada não é possível aceder SQL do Azure diretamente. Neste caso, pode utilizar [testado cópia](copy-activity-performance.md) à base de dados do SQL Azure / armazém de dados do SQL do Azure. Neste cenário, precisaria de que apenas HTTPS (porta 443) para o movimento de dados.


## <a name="proxy-server-considerations"></a>Considerações sobre o servidor proxy
Se o seu ambiente de rede empresarial utiliza um servidor proxy para aceder à internet, configure a integração personalizada alojada tempo de execução para utilizar definições de proxy adequado. Pode definir o proxy durante a fase de registo inicial.

![Especifique o proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Tempo de execução de integração personalizada alojada utiliza o servidor proxy para ligar ao serviço em nuvem. Clique na hiperligação alteração durante a configuração inicial. É apresentada a caixa de diálogo de definição de proxy.

![Proxy de conjunto](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Existem três opções de configuração:

- **Não utilizar o proxy**: integração personalizada alojada runtime não explicitamente utilizar qualquer proxy para ligar a serviços em nuvem.
- **Utilizar o proxy de sistema**: autoalojado utiliza de tempo de execução de integração a definição de proxy está configurado no diahost.exe.config e diawp.exe.config. Se não existe nenhum proxy está configurado no diahost.exe.config e diawp.exe.config, tempo de execução automática alojada integração liga-se ao serviço em nuvem diretamente, sem passar através do proxy.
- **Utilizar proxy personalizado**: configurar a definição a utilizar para o tempo de execução de integração personalizada alojada, em vez de utilizar configurações de diahost.exe.config e diawp.exe.config de proxy HTTP. Endereço e a porta são necessários. Nome de utilizador e palavra-passe são opcionais, dependendo da definição de autenticação do proxy. Todas as definições são encriptadas com a DPAPI do Windows no tempo de execução automática alojada integração e armazenadas localmente no computador.

O tempo de execução para a integração serviço de anfitrião reiniciará automaticamente depois de guardar as definições de proxy atualizadas.

Depois de personalizada alojada integração runtime foi registou com êxito, se pretender ver ou atualizar as definições de proxy, utilize o Gestor de configuração de tempo de execução de integração.

1.  Iniciar **Gestor de configuração de tempo de execução de integração do Microsoft**.
2.  Mudar para o **definições** separador.
3.  Clique em **alteração** ligação na **HTTP Proxy** secção para iniciar o **definir o Proxy de HTTP** caixa de diálogo.
4.  Depois de clicar no **seguinte** botão, verá uma caixa de diálogo de aviso solicitando a sua permissão Guardar a definição de proxy e reinicie o serviço de anfitrião de tempo de execução de integração.

Pode ver e atualizar o proxy HTTP utilizando a ferramenta Gestor de configuração.

![Proxy de vista](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Se configurar um servidor proxy com a autenticação NTLM, tempo de execução de integração anfitrião serviço é executado sob a conta de domínio. Se alterar a palavra-passe para a conta de domínio mais tarde, não se esqueça de atualizar as definições de configuração para o serviço e reiniciá-lo em conformidade. Devido a este requisito, sugerimos que utilize uma conta de domínio dedicado para aceder ao servidor proxy que não requerem que Atualize a palavra-passe com frequência.

### <a name="configure-proxy-server-settings"></a>Configurar definições do servidor proxy

Se selecionar **utilizar o proxy de sistema** definir para o proxy HTTP, tempo de execução de integração personalizada alojada utiliza a definição no diahost.exe.config e diawp.exe.config de proxy. Se não for especificado nenhum proxy diahost.exe.config e diawp.exe.config, tempo de execução automática alojada integração liga-se ao serviço em nuvem diretamente, sem passar através do proxy. O procedimento seguinte fornece instruções para atualizar o ficheiro diahost.exe.config.

1. No Explorador de ficheiros, efetue uma cópia de segurança de C:\Program Files\Microsoft integração Runtime\3.0\Shared\diahost.exe.config para fazer uma cópia de segurança do ficheiro original.
2. Inicie o Notepad.exe executar como administrador e abrir o ficheiro de texto "C:\Program Files\Microsoft integração Runtime\3.0\Shared\diahost.exe.config. Localize a etiqueta predefinida para system.net conforme mostrado no seguinte código:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Em seguida, pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo seguinte:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas no interior da tag de proxy para especificar as definições necessárias como scriptLocation. Consulte [proxy (definições de rede) do elemento](https://msdn.microsoft.com/library/sa91de1e.aspx) de sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Guarde o ficheiro de configuração na localização original e, em seguida, reinicie o serviço de anfitrião de tempo de execução de integração Self-hosted, escolherá as alterações. Para reiniciar o serviço: utilizar a miniaplicação serviços no painel de controlo ou do **integração Runtime Configuration Manager** > clique no **parar o serviço** botão, em seguida, clique no **iniciar Serviço**. Se o serviço não iniciar, é provável que foi adicionada uma sintaxe de tag XML inválida para o ficheiro de configuração de aplicação que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Para além destes pontos, terá também de certificar-se de que o Microsoft Azure está na lista de permissões da sua empresa. A lista de endereços IP do Microsoft Azure válidos pode ser transferida do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Sintomas possíveis para problemas relacionados com o servidor de proxy e firewall
Se encontrar erros semelhantes dos seguintes, trata-se provavelmente devido a configuração incorreta do servidor proxy ou uma firewall, que bloqueia integração personalizada alojada tempo de execução se liguem à fábrica de dados para se autenticar. Consulte a secção anterior para garantir que a firewall e servidor proxy estão configuradas corretamente.

1.  Ao tentar registar o tempo de execução automática alojada integração, receberá o seguinte erro: "Falha ao registar a este nó de tempo de execução de integração! Certifique-se de que a chave de autenticação é válida e o serviço de anfitrião do serviço de integração está em execução neste computador. "
2.  Quando abrir o Gestor de configuração de tempo de execução de integração, consulte o estado como "**desligado**"ou"**ligação**". Quando visualiza registos de eventos do Windows, sob "Visualizador de eventos" > "Aplicação e registos de serviços" > "Microsoft integração tempo de execução", vê mensagens de erro, tais como o seguinte erro:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="open-port-8060-for-credential-encryption"></a>Abra a porta 8060 para encriptação de credenciais
O **definição credenciais** aplicação (atualmente não suportada) utiliza a porta de entrada 8060 às credenciais de reencaminhamento para o tempo de execução automática alojada integração quando configurar um serviço ligado no local no portal do Azure. Durante a configuração de tempo de execução automática alojada integração, por predefinição, a instalação de tempo de execução automática alojada integração abre-lo na máquina de tempo de execução automática alojada integração.

Se estiver a utilizar uma firewall de terceiros, pode abrir a porta 8050 manualmente. Caso se depare com problema firewall durante a configuração de tempo de execução automática alojada integração, pode tentar utilizar o seguinte comando para instalar o runtime de integração personalizada alojada sem configurar a firewall.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8060 na máquina de tempo de execução automática alojada integração, utilize mecanismos que não a utilização de * * definição credenciais * * aplicação a configurar credenciais do arquivo de dados. Por exemplo, pode utilizar cmdlet New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell. Consulte a secção de definição de credenciais e segurança na forma como as credenciais do arquivo de dados pode ser definida.


## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial seguinte para obter instruções passo a passo: [Tutorial: copiar dados no local para a nuvem](tutorial-hybrid-copy-powershell.md).
