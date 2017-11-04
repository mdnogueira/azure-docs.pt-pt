---
title: "Exemplo de rede de Perímetro do Azure – criar uma rede de Perímetro Simple com NSGs | Microsoft Docs"
description: "Criar uma rede de Perímetro com grupos de segurança de rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Exemplo 1 – criar uma rede de Perímetro simple utilizando NSGs com um modelo Azure Resource Manager
[Regressar à página de segurança limites melhores práticas][HOME]

> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clássico - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Este exemplo cria uma rede de Perímetro primitivo com quatro servidores do Windows e grupos de segurança de rede. Este exemplo descreve cada um das secções relevantes do modelo para fornecer uma compreensão mais aprofundada de cada passo. Também é uma secção de cenário de tráfego para fornecer uma visão passo a passo detalhada das como procede de tráfego através das camadas de defesa na rede de Perímetro. Por fim, as referências de secção é o código de modelo completo e instruções para criar este ambiente de teste e experimentar vários cenários. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Rede de Perímetro entrada com o NSG][1]

## <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, uma subscrição contém os seguintes recursos:

* Um grupo de recursos única
* Uma rede Virtual com duas sub-redes; "FrontEnd" e "BackEnd"
* Um grupo de segurança de rede que é aplicada a ambas as sub-redes
* Um servidor do Windows que representa um servidor de web de aplicação ("IIS01")
* Servidores de dois windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")
* Um endereço IP público associado ao servidor de web de aplicação

Na secção references, há uma ligação para um modelo Azure Resource Manager que cria o ambiente descrito neste exemplo. Criação de VMs e redes virtuais, embora efetuada pelo modelo de exemplo, não são descritas em detalhe neste documento. 

**Para criar este ambiente** (instruções detalhadas são na secção referências deste documento);

1. Implementar o modelo Azure Resource Manager em: [modelos de início rápido do Azure][Template]
2. Instalar a aplicação de exemplo em: [Script de aplicação de exemplo][SampleApp]

>[!NOTE]
>Para o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizada como uma "caixa ir". Primeiro RDP para o servidor IIS e, em seguida, para o RDP de servidor de IIS para o servidor de back-end. Em alternativa um IP público pode ser associado a cada servidor NIC para RDP mais fácil.
> 
>

As secções seguintes fornecem uma descrição detalhada do grupo de segurança de rede e como funciona para este exemplo orientando através de linhas de chaves do modelo de Gestor de recursos do Azure.

## <a name="network-security-groups-nsg"></a>Grupos de Segurança de Rede (NSG)
Para este exemplo, um grupo NSG está criado e, em seguida, carregar com regras de seis. 

>[!TIP]
>Um modo geral, deve criar as regras de "Permitir" específicas primeiro e, em seguida, as regras de "Deny" mais genéricas última. Dita a prioridade atribuída que as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. Podem aplicar regras do NSG na direção de entrada ou de saída (da perspetiva da sub-rede).
>
>

De forma declarativa, as seguintes regras estão a ser criadas para o tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido
2. Tráfego RDP (porta 3389) da Internet para qualquer VM é permitido
3. O tráfego HTTP (porta 80) da Internet para o servidor web (IIS01) é permitido
4. Qualquer tráfego (todas as portas) do IIS01 para AppVM1 é permitido
5. Qualquer tráfego (todas as portas) da Internet a toda a VNet (ambas as sub-redes) é negado
6. Qualquer tráfego (todas as portas) da sub-rede do front-end para a sub-rede de back-end é negado

Com estas regras vinculada a cada sub-rede, se um pedido de HTTP foi recebido a partir da Internet para o servidor web, ambas as regras de 3 (permitir) e 5 (negar) seria aplicada, mas uma vez que a regra 3 tem uma prioridade mais alta apenas seria aplicada e regra 5 não seria entrem em play. Deste modo, os pedidos de HTTP seriam permissão para ser o servidor web. Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar e o tráfego seria não autorizado para o servidor. Regra 6 (negar) bloqueia a sub-rede do front-end de falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras de 1 e 4), este conjunto de regras protege a rede de back-end caso compromissos um atacante a aplicação web no front-end, o atacante poderia limitada acesso à rede de back-end "protegido" (apenas para recursos expostos no servidor de AppVM01).

Há uma regra de saída predefinida que permita o tráfego de saída à internet. Neste exemplo, vamos está a permitir o tráfego de saída e não modificar quaisquer regras de saída. Para aplicar a política de segurança para o tráfego em ambas as direções, utilizador definido de encaminhamento é necessária e é explorou "Exemplo 3" no [segurança limites melhores práticas página][HOME].

Cada regra é abordada mais detalhadamente:

1. Um recurso do grupo de segurança de rede tem de ser instanciado para conter as regras:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. Neste exemplo, a primeira regra permite o tráfego DNS entre todas as redes internos para o servidor DNS na sub-rede de back-end. A regra tem alguns parâmetros importantes:
  * "destinationAddressPrefix" - regras podem utilizar um tipo especial de prefixo de endereço denominado um "etiqueta predefinida", estas etiquetas são identificadores fornecidos pelo sistema que permitem uma forma fácil abordar uma categoria superior dos prefixos de endereço. Esta regra utiliza a etiqueta predefinida "Internet" para indicar qualquer endereço fora da VNet. Outras etiquetas de prefixo são VirtualNetwork e AzureLoadBalancer.
  * "Direção" significa na direção do fluxo de tráfego esta regra entra em vigor. É a direção da perspetiva de Máquina Virtual ou a sub-rede (dependendo de onde está vinculado esta NSG). Deste modo, se direção "Entrada" e o tráfego está a entrar a sub-rede, seria aplicada a regra e tráfego que sai a sub-rede não seria afetado por esta regra.
  * "Prioridade" define a ordem de avaliação de um fluxo de tráfego. O o número maior de prioridade inferior. Quando uma regra aplica-se a um fluxo de tráfego específica, não existem regras adicionais são processadas. Deste modo, se uma regra com prioridade 1 permite que o tráfego e uma regra com prioridade 2 nega tráfego e ambas as regras são aplicadas ao tráfego, em seguida, o tráfego seria ter permissão para fluxo (uma vez que a regra 1 tinha uma prioridade mais alta demorou em vigor e não existem regras adicionais foram aplicadas).
  * "Acesso" significa tráfego afetado por esta regra estiver bloqueado ("Deny") ou permitido ("Permitir").

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Esta regra permite que o tráfego RDP a partir da internet à porta RDP em qualquer servidor na sub-rede vinculada. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Esta regra permite o tráfego de internet de entrada para o servidor web de acessos. Esta regra não altera o comportamento de encaminhamento. A regra permite apenas tráfego destinado IIS01 passem. Deste modo, se o tráfego da Internet tinha o servidor web como respectivo destino desta regra seria permitir que e param de processar mais regras. (Regra com a prioridade é bloqueado 140 todos os outro internet tráfego de entrada). Se apenas estiver a processar tráfego HTTP, esta regra foi mais restringida para permitir apenas o destino porta 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Esta regra permite o tráfego para passar do servidor IIS01 para o servidor de AppVM01, um posterior blocos de regra a todos os outro front-end para o tráfego de back-end. Para melhorar a esta regra, se a porta é conhecida que devem ser adicionados. Por exemplo, se o servidor IIS é atingir apenas SQL Server em AppVM01, o intervalo de portas de destino deve ser alterado de "*" (Any) é 1433 (a porta do SQL Server), permitindo assim que uma superfície de ataque de entrada inferior em AppVM01 a aplicação web alguma vez comprometida.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Esta regra nega o tráfego da internet para quaisquer servidores na rede. Com as regras com a prioridade 110 e 120, o efeito é permitir que apenas internet tráfego de entrada para as portas RDP nos servidores e blocos e de firewall tudo o resto. Esta regra é uma regra para bloquear todos os fluxos inesperados "isento de falhas".

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. A regra final nega o tráfego da sub-rede do front-end para a sub-rede de back-end. Uma vez que esta regra é uma regra de só de entrada, o tráfego inverso é permitido (a partir de back-end para o front-end).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-internet-to-web-server"></a>(*Permitidos*) Internet para o servidor web
1. Um utilizador de internet solicita uma página HTTP do endereço IP público do NIC associada com a NIC de IIS01
2. O endereço IP público transmite o tráfego para a VNet para IIS01 (o servidor web)
3. Sub-rede de front-end inicia o processamento da regra de entrada:
  1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
  2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
  3. Aplicar regras de NSG 3 (Internet para IIS01), o tráfego é o processamento da regra permitido, parar
4. O tráfego chega a um endereço IP do servidor web IIS01 (10.0.1.5)
5. IIS01 está à escuta para o tráfego web, este pedido de recebe e inicia o processamento do pedido
6. IIS01 pede-lhe o SQL Server no AppVM01 informações
7. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
8. A sub-rede de back-end inicia o processamento da regra de entrada:
  1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
  2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
  3. NSG regra 3 (Internet à Firewall) não se aplicam, mova a regra seguinte
  4. 4 de regras do NSG aplicar (IIS01 para AppVM01), o tráfego é permitido, pare o processamento da regra
9. AppVM01 recebe a consulta de SQL e responde
10. Uma vez que não existem quaisquer regras de saída na sub-rede de back-end, a resposta é permitida
11. Sub-rede de front-end inicia o processamento da regra de entrada:
  1. Não há nenhuma regra NSG aplica-se a entrada para a sub-rede do front-end, para que nenhuma do NSG regras aplicam-se o tráfego da sub-rede de back-end
  2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
12. O servidor IIS recebe a resposta do SQL Server e concluir a resposta HTTP e envia para o autor do pedido
13. Uma vez que não existem quaisquer regras de saída na sub-rede do front-end, a resposta é permitida e o utilizador de Internet recebe a página web pedida.

#### <a name="allowed-rdp-to-iis-server"></a>(*Permitidos*) RDP ao servidor IIS
1. Um administrador do servidor na internet solicita uma sessão do RDP para IIS01 no endereço IP público do NIC associada com a NIC de IIS01 (este endereço IP público que pode ser encontrado através do Portal ou o PowerShell)
2. O endereço IP público transmite o tráfego para a VNet para IIS01 (o servidor web)
3. Sub-rede de front-end inicia o processamento da regra de entrada:
  1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
  2. Aplicar regras de NSG 2 (RDP), o tráfego é o processamento da regra permitido, parar
4. Não existem regras de saída, aplicam regras predefinidas e retorno tráfego é permitido
5. Sessão do RDP está ativado
6. IIS01 solicita o nome de utilizador e palavra-passe

>[!NOTE]
>Para o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizada como uma "caixa ir". Primeiro RDP para o servidor IIS e, em seguida, para o RDP de servidor de IIS para o servidor de back-end.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitidos*) pesquisar DNS de servidor Web num servidor DNS
1. Web Server, IIS01, tem de dados de um feed em www.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede de back-end) como o servidor DNS primário, IIS01 envia o pedido DNS para DNS01
3. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
4. Sub-rede de back-end inicia o processamento da regra de entrada:
  * Aplicar regras de NSG 1 (DNS), o tráfego é o processamento da regra permitido, parar
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitidos*) ficheiro de acesso do servidor Web no AppVM01
1. IIS01 pede-lhe um ficheiro num AppVM01
2. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
3. A sub-rede de back-end inicia o processamento da regra de entrada:
  1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
  2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
  3. NSG regra 3 (Internet para IIS01) não se aplicam, mova a regra seguinte
  4. 4 de regras do NSG aplicar (IIS01 para AppVM01), o tráfego é permitido, pare o processamento da regra
4. AppVM01 recebe o pedido e responde com o ficheiro (partindo do princípio de acesso é autorizado)
5. Uma vez que não existem quaisquer regras de saída na sub-rede de back-end, a resposta é permitida
6. Sub-rede de front-end inicia o processamento da regra de entrada:
  1. Não há nenhuma regra NSG aplica-se a entrada para a sub-rede do front-end, para que nenhuma do NSG regras aplicam-se o tráfego da sub-rede de back-end
  2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
7. O servidor IIS recebe um ficheiro

#### <a name="denied-rdp-to-backend"></a>(*Negado*) RDP ao back-end
1. Um utilizador de internet tenta RDP para servidor AppVM01
2. Uma vez que existem não existem endereços IP públicos associados a esta NIC de servidores, este tráfego nunca introduzir a VNet e não a aceder ao servidor
3. No entanto se foi ativado um endereço IP público por algum motivo, a regra NSG 2 (RDP) permitiria que este tráfego

>[!NOTE]
>Para o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizada como uma "caixa ir". Primeiro RDP para o servidor IIS e, em seguida, para o RDP de servidor de IIS para o servidor de back-end.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web para o servidor back-end
1. Um utilizador de internet tenta aceder a um ficheiro no AppVM01
2. Uma vez que existem não existem endereços IP públicos associados a esta NIC de servidores, este tráfego nunca introduzir a VNet e não a aceder ao servidor
3. Se um endereço IP público foi ativado por algum motivo, a regra NSG 5 (Internet a VNet) bloqueariam este tráfego

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Negado*) pesquisar Web DNS num servidor DNS
1. Um utilizador de internet tenta procurar um registo DNS interno no DNS01
2. Uma vez que existem não existem endereços IP públicos associados a esta NIC de servidores, este tráfego nunca introduzir a VNet e não a aceder ao servidor
3. Se um endereço IP público foi ativado por algum motivo, a regra NSG 5 (Internet a VNet) bloqueariam este tráfego (Nota: se regra 1 (DNS) não seriam aplicadas porque os pedidos de endereço de origem é internet e Rule 1 apenas se aplica a VNet local como origem)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Negado*) acesso do SQL Server no servidor web
1. Um utilizador de internet solicita dados SQL IIS01
2. Uma vez que existem não existem endereços IP públicos associados a esta NIC de servidores, este tráfego nunca introduzir a VNet e não a aceder ao servidor
3. Se um endereço IP público foi ativado por algum motivo, a sub-rede do front-end inicia o processamento da regra de entrada:
  1. Regras de NSG 1 (DNS) não se aplicam, mova a regra seguinte
  2. Regras de NSG 2 (RDP) não se aplicam, mova a regra seguinte
  3. Aplicar regras de NSG 3 (Internet para IIS01), o tráfego é o processamento da regra permitido, parar
4. O tráfego chega a um endereço IP do IIS01 (10.0.1.5)
5. IIS01 não está a escutar na porta 1433, por isso, sem resposta ao pedido

## <a name="conclusion"></a>Conclusão
Neste exemplo é uma forma avançar diretamente e relativamente simple de isolar a sub-rede de back-end do tráfego de entrada.

Obter mais exemplos e uma descrição geral dos limites de segurança de rede podem ser encontrados [aqui][HOME].

## <a name="references"></a>Referências
### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Este exemplo utiliza um modelo Azure Resource Manager predefinidos num repositório GitHub mantido pela Microsoft e abra-a para a Comunidade. Este modelo pode ser implementado diretamente do GitHub ou transferido e modificado para atender às suas necessidades. 

O modelo de principal está no ficheiro denominado "azuredeploy. JSON". Este modelo pode ser submetido através do PowerShell ou o CLI (com o ficheiro associado "azuredeploy.parameters.json") para implementar este modelo. Posso encontrar que a forma mais fácil consiste em utilizar o botão "Implementar para Azure" na página README.md no GitHub.

Para implementar o modelo que cria este exemplo a partir do GitHub e o portal do Azure, siga estes passos:

1. Num browser, navegue para o [modelo][Template]
2. Clique no botão "Implementar para Azure" (ou no botão "Visualizar" para ver uma representação gráfica deste modelo)
3. Introduza a conta de armazenamento, nome de utilizador e palavra-passe no painel parâmetros, em seguida, clique em **OK**
5. Criar um grupo de recursos para esta implementação (pode utilizar um existente, mas recomendada a um novo para obter os melhores resultados)
6. Se necessário, altere as definições de subscrição e localização para a sua VNet.
7. Clique em **consultar termos legais**, leia os termos de licenciamento e clique em **Compra** para aceitar.
8. Clique em **criar** para iniciar a implementação deste modelo.
9. Após a conclusão da implementação com êxito, navegue para o grupo de recursos criado para esta implementação para ver os recursos configurados no interior.

>[!NOTE]
>Este modelo permite que o RDP para apenas o servidor de IIS01 (localizar o IP público para IIS01 no Portal). Para o RDP para quaisquer servidores de back-end nesta instância, o servidor IIS é utilizada como uma "caixa ir". Primeiro RDP para o servidor IIS e, em seguida, para o RDP de servidor de IIS para o servidor de back-end.
>
>

Para remover esta implementação, elimine o grupo de recursos e todos os recursos subordinados também serão eliminados.

#### <a name="sample-application-scripts"></a>Scripts de aplicação de exemplo
Assim que o modelo é executada com êxito, pode configurar o servidor web e o servidor de aplicação com uma aplicação web simples para permitir testes com esta configuração de rede de Perímetro. Para instalar uma aplicação de exemplo para isto e outros exemplos de rede de Perímetro, um foi fornecido na seguinte hiperligação: [Script de aplicação de exemplo][SampleApp]

## <a name="next-steps"></a>Passos seguintes

* Implementar neste exemplo
* Criar a aplicação de exemplo
* Testar os fluxos de tráfego diferentes através desta rede de Perímetro

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Rede de Perímetro entrada com o NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md