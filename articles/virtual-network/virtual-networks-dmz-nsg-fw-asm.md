---
title: "Exemplo de rede de Perímetro – criar uma rede de Perímetro a proteger as aplicações com uma Firewall e NSGs | Microsoft Docs"
description: "Criar uma rede de Perímetro com uma Firewall e grupos de segurança de rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2 – criar uma rede de Perímetro a proteger as aplicações com uma Firewall e NSGs
[Regressar à página de segurança limites melhores práticas][HOME]

Neste exemplo irá criar uma rede de Perímetro com uma firewall, quatro windows servidores e grupos de segurança de rede. Também irá orientá-através de cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. Há também uma secção de cenário de tráfego para fornecer um aprofundada passo a passo como procede de tráfego através das camadas de defesa na rede de Perímetro. Por fim, as referências de secção é o código de conclusão e instruções para criar este ambiente de teste e experimentar vários cenários. 

![Entrada DMZ com NVA e o NSG][1]

## <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, não há uma subscrição que contém o seguinte:

* Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
* Uma rede Virtual "CorpNetwork", com duas sub-redes: "FrontEnd" e "BackEnd"
* Um único grupo de segurança de rede que é aplicada a ambas as sub-redes
* Uma aplicação virtual de rede, neste exemplo, um Barracuda NextGen Firewall, ligado à sub-rede do front-end
* Um servidor do Windows que representa um servidor de web de aplicação ("IIS01")
* Dois windows servers que representam aplicações novamente terminar servidores ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

> [!NOTE]
> Embora este exemplo utiliza um Barracuda NextGen Firewall, muitas da diferentes aplicações virtuais de rede pode ser utilizadas para este exemplo.
> 
> 

Na secção referências abaixo há um script do PowerShell que irá criar a maior parte do ambiente descrito acima. Criação de VMs e redes virtuais, embora são efetuadas pelo script de exemplo, não são descritas em detalhe neste documento.

Para criar o ambiente:

1. Guarde o ficheiro de xml de configuração de rede incluído na secção de referências (atualizado com os nomes e localização e IP endereços para fazer corresponder o cenário determinado)
2. Atualizar as variáveis de utilizador no script para fazer corresponder o ambiente que de script está a ser executado contra (subscrições, nomes de serviço, etc.)
3. Execute o script do PowerShell

**Tenha em atenção**: região signified no PowerShell script tem de corresponder à região signified no ficheiro xml de configuração de rede.

Assim que o script é executado com êxito os seguintes passos de scripts de pós-implementação podem ser demorados:

1. Configurar as regras de firewall, isto é explicado na secção abaixo intitulada: regras de Firewall.
2. Opcionalmente, na secção de referências são dois scripts para configurar o servidor web e o servidor de aplicação com uma aplicação web simples para permitir testes com esta configuração de rede de Perímetro.

A secção seguinte explica a maioria das instruções scripts relativo para grupos de segurança de rede.

## <a name="network-security-groups-nsg"></a>Grupos de Segurança de Rede (NSG)
Para este exemplo, um grupo NSG está incorporado e, em seguida, carregar com regras de seis. 

> [!TIP]
> Um modo geral, deve criar as regras de "Permitir" específicas primeiro e, em seguida, as regras de "Deny" mais genéricas última. Dita a prioridade atribuída que as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. Podem aplicar regras do NSG na direção de entrada ou de saída (da perspetiva da sub-rede).
> 
> 

De forma declarativa, as seguintes regras estão a ser criadas para o tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido
2. Tráfego RDP (porta 3389) da Internet para qualquer VM é permitido
3. O tráfego HTTP (porta 80) da Internet para a NVA (firewall) é permitido
4. Qualquer tráfego (todas as portas) do IIS01 para AppVM1 é permitido
5. Qualquer tráfego (todas as portas) da Internet a toda a VNet (ambas as sub-redes) é negado
6. Qualquer tráfego (todas as portas) da sub-rede do front-end para a sub-rede de back-end é negado

Com estas regras vinculada a cada sub-rede, se um pedido HTTP foi recebido a partir da Internet para o servidor web, ambas as regras de 3 (permitir) e 5 (negar) seria aplicada, mas uma vez que a regra 3 tem uma prioridade mais alta apenas seria aplicada e regra 5 não seria entrem em play. Deste modo, os pedidos de HTTP seriam permissão para ser a firewall. Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar e o tráfego seria não autorizado para o servidor. Regra 6 (negar) bloqueia a sub-rede do front-end de falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras de 1 e 4), esta ação protege a compromissos um atacante a aplicação web no front-end, o atacante poderia limitada acesso à rede de back-end "protegido" (apenas para recursos expostos no servidor de AppVM01) de rede de back-end nas maiúsculas e minúsculas.

Há uma regra de saída predefinida que permita o tráfego de saída à internet. Neste exemplo, vamos está a permitir o tráfego de saída e não modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, utilizador encaminhamento definido pelo não é necessária, isto é explorou um exemplo de diferentes que pode encontrar no [documento de limite de segurança principal][HOME].

As regras do NSG referidas acima são muito semelhantes às regras do NSG [exemplo 1 - criar uma rede de Perímetro simples com NSGs][Example1]. Reveja a descrição de NSG esse documento para um detalhadas de olhos a cada regra NSG e de atributos.

## <a name="firewall-rules"></a>Regras da Firewall
Um cliente de gestão terão de ser instalada num PC para gerir a firewall e criar as configurações necessárias. Consulte o fornecedor de documentação da sua firewall (ou outro NVA) sobre como gerir o dispositivo. O resto esta secção descreve a configuração da firewall, através do cliente de gestão de fornecedores (ou seja, não o portal do Azure ou o PowerShell).

Instruções para a transferência de cliente e os ligam à Barracuda utilizado neste exemplo, podem ser encontradas aqui: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Na firewall, as regras de reencaminhamento terá de ser criado. Uma vez que este exemplo apenas encaminha o tráfego de internet no vinculados à firewall e, em seguida, para o servidor web, é necessário o reencaminhamento apenas uma regra NAT. Na Barracuda NextGen Firewall utilizada neste exemplo, a regra seria uma regra de NAT de destino ("Dst NAT") ao transmitir este tráfego.

Para criar a regra seguinte (ou certifique-se de regras de predefinidas existente), a partir do dashboard de cliente Barracuda NG administrador, navegue até ao separador de configuração, na configuração operacional secção clique Ruleset. Uma grelha chamada, "Main regras" mostrará as regras existentes do Active Directory e desativadas na firewall. No canto superior direito desta grelha é um pequeno, verde "+" botão, clique em esta opção para criar uma nova regra (Nota: a firewall pode ser "bloqueada" para as alterações, se vir um botão marcada "Bloquear" e não é possível criar ou editar regras, clicar neste botão para "desbloquear" o ruleset e permitir a edição). Se pretender editar uma regra existente, selecione essa regra, clique com botão direito e selecione Editar regra.

Criar uma nova regra e forneça um nome, como "WebTraffic". 

O ícone de regra NAT de destino tem o seguinte aspeto: ![ícone de NAT de destino][2]

A regra de si próprio seria algo semelhante ao seguinte:

![Regra de firewall][3]

Aqui qualquer endereço que chega a Firewall de entrada a tentar aceder a HTTP (porta 80 ou 443 para HTTPS) será enviada a interface de "DHCP1 Local IP" da Firewall e redirecionada para o servidor Web com o endereço IP do 10.0.1.5. Uma vez que o tráfego proveniente na porta 80 e avançar para o servidor web na porta 80 não foi necessária nenhuma alteração de porta. No entanto, a lista de destino pode ter sido 10.0.1.5:8080 se listened nosso servidor Web na porta 8080 traduzir, por conseguinte, a porta de entrada 80 na firewall de entrada porta 8080 no servidor web.

Um método de ligação deve também ser signified, para a regra de destino a partir da Internet, "Realizar o SNAT dinâmica" é mais adequado. 

Embora apenas uma regra foi criada é importante que a prioridade é definida corretamente. Se na grelha de todas as regras na firewall esta nova regra estiver na parte inferior (abaixo a regra "BLOCKALL") nunca será colocada na play. Certifique-se da regra criada recentemente para o tráfego web acima a regra BLOCKALL.

Assim que a regra é criada, tem de ser enviada para a firewall e, em seguida, ativar, se isto não é efetuado a alteração da regra não entrarão em vigor. O processo de emissão e a ativação é descrito na secção seguinte.

## <a name="rule-activation"></a>Ativação de regra
Com o ruleset modificado para adicionar esta regra, o ruleset tem de ser carregado para a firewall e ativado.

![Ativação da regra de firewall][4]

O canto superior direito do cliente de gestão estão num cluster de botões. Clique no botão "Enviar alterações" para enviar as regras de modificação para a firewall e, em seguida, clique no botão "Ativar".

Com a ativação de ruleset a firewall a compilação de ambiente este exemplo está concluída. Opcionalmente, os scripts de compilação post na secção referências podem ser executados para adicionar uma aplicação a este ambiente para testar o abaixo cenários de tráfego.

> [!IMPORTANT]
> É fundamental tenha em atenção que irá não atingir o servidor web diretamente. Quando um browser solicita uma página HTTP FrontEnd001.CloudApp.Net, o ponto final de HTTP (porta 80) transmite este tráfego para a firewall não o servidor web. A firewall, em seguida, de acordo com a regra criado acima, os NATs que o pedido para o servidor Web.
> 
> 

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Permitida) Servidor Web para Web através da Firewall
1. Página da Internet utilizador pedidos HTTP de FrontEnd001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Tráfego de transmite do serviço de nuvem através de pontos finais abertos na porta 80 para interface local do firewall 10.0.1.4:80
3. Sub-rede de front-end inicia o processamento da regra de entrada:
   1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
   2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
   3. Aplicar regras de NSG 3 (Internet à Firewall), o tráfego é o processamento da regra permitido, parar
4. O tráfego chega a um endereço IP da firewall (10.0.1.4) do
5. Regra de reencaminhamento de firewall consulte este tráfego de porta 80, redireciona-o para o servidor de web IIS01
6. IIS01 está à escuta para o tráfego web, este pedido de recebe e inicia o processamento do pedido
7. IIS01 pede-lhe o SQL Server no AppVM01 informações
8. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
9. A sub-rede de back-end inicia o processamento da regra de entrada:
   1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
   2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
   3. NSG regra 3 (Internet à Firewall) não se aplicam, mova a regra seguinte
   4. 4 de regras do NSG aplicar (IIS01 para AppVM01), o tráfego é permitido, pare o processamento da regra
10. AppVM01 recebe a consulta de SQL e responde
11. Uma vez que não existem quaisquer regras de saída na sub-rede de back-end a resposta é permitida
12. Sub-rede de front-end inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG aplica-se a entrada para a sub-rede do front-end, para que nenhuma do NSG regras aplicam-se o tráfego da sub-rede de back-end
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
13. O servidor IIS recebe a resposta do SQL Server e concluir a resposta HTTP e envia para o requerente
14. Uma vez que esta é uma sessão NAT de firewall, o destino de resposta (inicialmente) destina-se a Firewall
15. A firewall recebe a resposta do servidor Web e reencaminha novamente para o utilizador de Internet
16. Uma vez que existem não existem regras de saída na sub-rede do front-end a resposta é permitido e o utilizador de Internet recebe a página web pedida.

#### <a name="allowed-rdp-to-backend"></a>(Permitida) RDP ao back-end
1. Administrador de servidor na internet pedidos de sessão do RDP para AppVM01 no BackEnd001.CloudApp.Net:xxxxx onde xxxxx é o número de porta aleatoriamente atribuída para RDP para AppVM01 (a porta atribuída pode ser encontrada no Portal do Azure ou através do PowerShell)
2. Uma vez que a Firewall só está a escutar no endereço FrontEnd001.CloudApp.Net, não está envolvido com este fluxo de tráfego
3. Sub-rede de back-end inicia o processamento da regra de entrada:
   1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
   2. Aplicar regras de NSG 2 (RDP), o tráfego é o processamento da regra permitido, parar
4. Não existem regras de saída, aplicam regras predefinidas e retorno tráfego é permitido
5. Sessão do RDP está ativado
6. AppVM01 solicita a palavra-passe de nome de utilizador

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitida) Pesquisa de DNS do servidor Web num servidor DNS
1. Web Server, IIS01, tem de dados de um feed em www.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede de back-end) como o servidor DNS primário, IIS01 envia o pedido DNS para DNS01
3. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
4. Sub-rede de back-end inicia o processamento da regra de entrada:
   1. Aplicar regras de NSG 1 (DNS), o tráfego é o processamento da regra permitido, parar
5. Servidor DNS recebe o pedido
6. Servidor DNS não tem o endereço em cache e pede-lhe um servidor DNS de raiz na internet
7. Não existem regras de saída na sub-rede de back-end, o tráfego é permitido
8. Servidor DNS de Internet responde, uma vez que esta sessão foi iniciada internamente, a resposta é permitida
9. Servidor DNS coloca a resposta em cache e responde ao pedido inicial para IIS01
10. Não existem regras de saída na sub-rede de back-end, o tráfego é permitido
11. Sub-rede de front-end inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG aplica-se a entrada para a sub-rede do front-end, para que nenhuma do NSG regras aplicam-se o tráfego da sub-rede de back-end
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido
12. IIS01 recebe a resposta do DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Permitida) Ficheiro de acesso do servidor Web no AppVM01
1. IIS01 pede-lhe um ficheiro num AppVM01
2. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
3. A sub-rede de back-end inicia o processamento da regra de entrada:
   1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
   2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
   3. NSG regra 3 (Internet à Firewall) não se aplicam, mova a regra seguinte
   4. 4 de regras do NSG aplicar (IIS01 para AppVM01), o tráfego é permitido, pare o processamento da regra
4. AppVM01 recebe o pedido e responde com o ficheiro (partindo do princípio de acesso é autorizado)
5. Uma vez que não existem quaisquer regras de saída na sub-rede de back-end a resposta é permitida
6. Sub-rede de front-end inicia o processamento da regra de entrada:
   1. Não há nenhuma regra NSG aplica-se a entrada para a sub-rede do front-end, para que nenhuma do NSG regras aplicam-se o tráfego da sub-rede de back-end
   2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
7. O servidor IIS recebe um ficheiro

#### <a name="denied-web-direct-to-web-server"></a>(Negado) Web direta para o servidor Web
Uma vez que o servidor Web, IIS01 e a Firewall no mesmo serviço em nuvem partilham o mesmo endereço IP destinado ao público. Deste modo, qualquer tráfego HTTP seria direcionado para a firewall. Enquanto o pedido teria de ser servido com êxito, não é possível ir diretamente para o servidor Web, transmitido, como concebido, através da Firewall primeiro. Consulte o primeiro cenário nesta secção para o fluxo de tráfego.

#### <a name="denied-web-to-backend-server"></a>(Negado) Web para o servidor back-end
1. Utilizador de Internet tenta aceder a um ficheiro no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2. Uma vez que existem não existem pontos finais abertos para partilha de ficheiros, isto não seria com êxito o serviço em nuvem e não conseguirem contactar o servidor
3. Se os pontos finais tiverem sido abertos por algum motivo, a regra NSG 5 (Internet a VNet) bloqueariam este tráfego

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS da Web num servidor DNS
1. Utilizador de Internet tenta pesquisar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net
2. Uma vez que existem não existem pontos finais abertos para o DNS, este não seria com êxito o serviço em nuvem e não conseguirem contactar o servidor
3. Se os pontos finais tiverem sido abertos por algum motivo, a regra NSG 5 (Internet a VNet) bloqueariam este tráfego (Nota: essa regra 1 (DNS) não seriam aplicadas por duas razões, primeiro o endereço de origem é internet, apenas se aplica esta regra para a VNet local como origem, também esta é uma regra de permissão, pelo que nunca iriam negar o tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Negado) Web para o acesso através da Firewall do SQL Server
1. Internet utilizador solicita dados SQL FrontEnd001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Uma vez que não existem não existem pontos finais aberto para o SQL Server, este não seria com êxito o serviço em nuvem e não alcançar a firewall
3. Se os pontos finais tiverem sido abertos por algum motivo, a sub-rede do front-end inicia o processamento da regra de entrada:
   1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
   2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
   3. Aplicar regras de NSG 2 (Internet à Firewall), o tráfego é o processamento da regra permitido, parar
4. O tráfego chega a um endereço IP da firewall (10.0.1.4) do
5. Não tem reencaminhamento regras para o SQL e ignora o tráfego de firewall

## <a name="conclusion"></a>Conclusão
Esta é uma forma direta relativamente diretamente de proteger a sua aplicação com uma firewall e isolamento da sub-rede de back-end do tráfego de entrada.

Obter mais exemplos e uma descrição geral dos limites de segurança de rede podem ser encontrados [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e a configuração de rede
Guarde o Script completo num ficheiro de script do PowerShell. Guarde a configuração de rede para um ficheiro com o nome "NetworkConf2.xml".
Modificar as variáveis definidas pelo utilizador, conforme necessário. Execute o script e, em seguida, siga as instruções de configuração da regra de Firewall acima.

#### <a name="full-script"></a>Script completa
Este script irão, com base nas variáveis definidas pelo utilizador:

1. Ligar a uma subscrição do Azure
2. Criar uma nova conta de armazenamento
3. Criar uma nova VNet e duas sub-redes, tal como definido no ficheiro de configuração de rede
4. Criar 4 windows VMs de servidor
5. Configure o NSG, incluindo:
   * Criar um NSG
   * Preenchê-lo com as regras
   * Enlace o NSG para as sub-redes adequadas

Este script do PowerShell deve ser executado localmente num que Internet ligado PC ou servidor.

> [!IMPORTANT]
> Quando este script é executado, pode haver avisos ou outras mensagens informativas destaque no PowerShell. Apenas mensagens de erro vermelho são a causa possível para preocupação.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Ficheiro de configuração de rede
Guarde este ficheiro xml com localização atualizada e adicionar a ligação a este ficheiro para a variável de $NetworkConfigFile no script acima.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Scripts de aplicação de exemplo
Se pretender instalar uma aplicação de exemplo para isto e outros exemplos de rede de Perímetro, um foi fornecido na seguinte hiperligação: [Script de aplicação de exemplo][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Rede de Perímetro entrada com o NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ícone NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regra de firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Ativação da regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
