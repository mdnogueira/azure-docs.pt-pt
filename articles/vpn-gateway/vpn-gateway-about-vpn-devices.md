<properties 
   pageTitle="Acerca dos Dispositivos VPN para as ligações de Gateway de Rede de VPNs das Redes Virtuais do Azure | Microsoft Azure"
   description="Saiba mais sobre os dispositivos de VPN e os parâmetros IPsec para ligações do VPN Gateway S2S. As ligações Site a Site podem ser utilizadas para configurações híbridas. Este artigo contém ligações para as instruções de configuração e os exemplos de dispositivos de gateway de VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# Acerca dos dispositivos de VPN para ligações de Gateway de Rede de VPNs

Precisa de um dispositivo VPN para configurar uma ligação de rede de VPNs (S2S). As ligações Site a Site podem ser utilizadas para criar uma solução híbrida ou sempre que pretender uma ligação segura entre a rede no local e a rede virtual. Este artigo aborda os dispositivos VPN compatíveis e os parâmetros de configuração. Tenha em atenção que, quando configurar uma ligação Site a Site, precisa de um endereço de IPv4 destinado ao público para o dispositivo VPN.                                                                                                                                                                                

Se o dispositivo não aparecer na tabela de [Dispositivos VPN Validados](#devicetable), veja a secção [Dispositivos VPN Não Validados](#additionaldevices) deste artigo. É possível que o seu dispositivo funcione com o Azure. Para obter suporte para dispositivos VPN, contacte o fabricante do dispositivo.

**Itens a ter em atenção quando visualizar as tabelas:**

- A terminologia para o encaminhamento estático e dinâmico foi alterada. Encontrará provavelmente os dois termos. Não há nenhuma alteração de funcionalidade, só os nomes estão a mudar.
    - Encaminhamento Estático = Baseado em políticas
    - Encaminhamento Dinâmico = Baseado na rota 
- As especificações para o gateway de VPN de Elevado Desempenho e para o gateway de VPN baseado na rota são as mesmas, salvo indicação em contrário. Por exemplo, os dispositivos VPN validados compatíveis com os gateways de VPN baseados na rota também são compatíveis com o gateway de VPN de Elevado Desempenho do Azure. 


## <a name="devicetable"></a>Dispositivos VPN validados 

Validámos uma série de dispositivos VPN padrão em parceria com os fornecedores dos dispositivos. Todos os dispositivos nas famílias de dispositivos contidas na lista seguinte deverão funcionar com gateways de VPN do Azure. Veja o artigo [Acerca do VPN Gateway](vpn-gateway-about-vpngateways.md) para verificar o tipo de gateway que tem de criar para a solução que pretende configurar. 

Para obter ajuda na configuração do seu dispositivo VPN, veja as ligações que correspondem à família de dispositivos adequada. 



| **Fornecedor**                      | **Família de dispositivos**                                        | **Versão mínima do SO**                             | **Baseado em políticas**                                                                                                                                                                                                             | **Baseado na rota**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | Routers VPN Série AR                                    | 2.9.2                                              | Brevemente                                                                                                                                                                                                                                          | Não compatível                                                                                                                                                                                               |
| Barracuda Networks, Inc.        | Barracuda NextGen Firewall série F             | Baseado em políticas: 5.4.3; Baseado na rota: 6.2.0  | [Instruções de configuração](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Instruções de configuração](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda Networks, Inc.        |  Barracuda NextGen Firewall série X                 | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Não compatível                                                                                                                                                                                               |
| Brocade                         | Vyatta 5400 vRouter                                      | Virtual Router 6.6R3 GA                            | [Instruções de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | Não compatível                                                                                                                                                                                               |
| Check Point                     | Gateway de Segurança                                         | R75.40, R75.40VS                                     | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | Não compatível                                                                                                                                                                                               |
| Cisco                           | ASR                                                      | IOS 15.1 (baseado em políticas), IOS 15.2 (baseado na rota)                | [Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (baseado em políticas), IOS 15.1 (baseado na rota*)               | [Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Exemplos da Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX, SDX, VPX      |10.1 e posterior                                           | [Instruções de integração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | Não compatível                                                                                                                                                                                               |
| Dell SonicWALL                  | Série TZ, Série NSA, Série SuperMassive, Série NSA classe E | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6. x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instruções – SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [instruções – SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instruções – SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [instruções – SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Série BIG-IP                                            | N/D                                                | [Instruções de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | Não compatível                                                                                                                                                                                               |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Instruções de configuração](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Instruções de configuração](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Internet Initiative Japan (IIJ) | Série SEIL                                              | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20            | [Instruções de configuração](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | Não compatível                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (baseado em políticas), JunOS 11.4 (baseado na rota)            | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | Série J                                                 | JunOS 10.4r9 (baseado em políticas), JunOS 11.4 (baseados em rota)          | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (baseado em políticas e baseado na rota)                  | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (baseado em políticas e baseado na rota)                  | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Serviço de Encaminhamento e Acesso Remoto                        | Windows Server 2012                                | Não compatível                                                                                                                                                                                                                                       | [Exemplos da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Open Systems AG | Gateway de Segurança do Controlo da Missão | N/D | [Guia de Instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guia de Instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Brevemente)                                                                                                                                                                                                                                        | Não compatível                                                                                                                                                                                               |
| Palo Alto Networks              | Todos os dispositivos com o PAN-OS     | PAN-OS 6.1.5 ou posterior (baseado em políticas), PAN-OS 7.0.5 ou posterior (baseado na rota)       | [Instruções de Configuração]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Instruções de Configuração](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Todos                                                      | Fireware XTM v11.x                                 | [Instruções de configuração](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | Não compatível                                                                                                                                                                                               |

(*) Os routers da série ISR 7200 só suportam VPNs baseadas em políticas.

## <a name="additionaldevices"></a>Dispositivos VPN não validados

Se não vir o seu dispositivo listado na tabela de Dispositivos VPN Validados (acima), poderá, mesmo assim, funcionar com uma ligação Site a Site. Verifique se o seu dispositivo VPN cumpre os requisitos mínimos descritos na secção Requisitos de Gateway do artigo [Acerca dos Gateways de VPN](vpn-gateway-about-vpngateways.md#gateway-requirements). Os dispositivos que cumprem os requisitos mínimos devem também funcionar bem com os gateways de VPN. Contacte o fabricante do dispositivo para obter instruções adicionais de suporte e de configuração.


## Editar os exemplos de configuração do dispositivo

Depois de transferir o exemplo de configuração do dispositivo VPN fornecido, terá de substituir alguns dos valores para que reflitam as definições do seu ambiente. 

**Para editar um exemplo:**

1. Abra o exemplo com o Bloco de Notas. 
1. Procure e substitua todas as cadeias <*texto*> pelos valores que dizem respeito ao seu ambiente. Não se esqueça de incluir < e >. O nome especificado deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante do dispositivo.

| **Texto de exemplo**                | **Alterar para**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | O nome que escolheu para este objeto. Exemplo: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | O nome que escolheu para este objeto. Exemplo: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | O nome que escolheu para este objeto. Exemplo: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | O nome que escolheu para este objeto. Exemplo: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | O nome que escolheu para este objeto. Exemplo: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Especifique o intervalo. Exemplo: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Especifique a máscara de sub-rede. Exemplo: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Especifique o intervalo no local. Exemplo: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Especifique a máscara de sub-rede no local. Exemplo: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Esta informação é específica da sua rede virtual e encontra-se no Portal de Gestão como **Endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt;                | Esta informação é específicas da sua rede virtual e encontra-se no Portal de Gestão como Gerir Chave.          |



## Parâmetros IPsec

>[AZURE.NOTE] Apesar de os valores apresentados na tabela seguinte serem suportados pelo VPN Gateway do Azure, não há atualmente um modo de especificar ou de selecionar uma combinação específica no VPN Gateway do Azure. Tem de especificar as eventuais restrições no dispositivo VPN no local. Além disso, tem de fixar MSS em 1350.

### Configuração da Fase 1 do IKE

| **Propriedade**                                       | **Baseado em políticas** | **Gateway de VPN Standard ou de Elevado Desempenho e baseado na rota** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versão do IKE                                        | IKEv1                          | IKEv2                                                            |
| Grupo Diffie-Hellman                               | Grupo 2 (1024 bits)             | Grupo 2 (1024 bits)                                               |
| Método de Autenticação                              | Chave Pré-partilhada                 | Chave Pré-partilhada                                                   |
| Algoritmos de Encriptação                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| Algoritmo Hash                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 | 28 800 segundos                 | 10 800 segundos                                                   |


### Configuração da Fase 2 do IKE

| **Propriedade**                                                             | **Baseado em políticas**                 | **Gateway de VPN Standard ou de Elevado Desempenho e baseado na rota**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versão do IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algoritmo Hash                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 2                        | 3600 segundos                                  | 3600 segundos                                                                  |
| Duração (Débito) da Associação de Segurança (SA) da Fase 2                  | 102 400 000 KB                                 | -                                                                  |
| Ofertas de Encriptação e de Autenticação de SA de IPsec (por ordem de preferência) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D | Veja *Ofertas de Associação de Segurança (SA) de IPsec do Gateway Baseado na Rota* (abaixo) |
| Perfect Forward Secrecy (PFS)                                            | Não                                             | Sim (Grupo DH 1, 2, 5, 14, 24)                                                    |
| Deteção de Elemento Inutilizado                                                      | Não suportado                                  | Suportado                                                          |

### Ofertas de Associação de Segurança (SA) de IPsec do Gateway Baseado na Rota

A tabela seguinte apresenta uma lista de Ofertas de Encriptação e Autenticação de SA de IPsec. As ofertas estão listadas pela ordem de preferência com que a oferta é apresentada ou aceite.

| **Ofertas de Encriptação e Autenticação de SA de IPsec** | **Gateway do Azure como iniciador**                               | **Gateway do Azure como dispositivo de resposta**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | AH SHA1 com ESP AES_128 com HMAC nulo                      |
| 5                                                 | AH SHA1 com ESP AES_256 com HMAC nulo                      | AH SHA1 com ESP 3_DES com HMAC nulo                        |
| 6                                                 | AH SHA1 com ESP AES_128 com HMAC nulo                      | AH MD5 com ESP 3_DES com HMAC nulo, sem durações propostas |
| 7                                                 | AH SHA1 com ESP 3_DES com HMAC nulo                        | AH SHA1 com ESP 3_DES SHA1, sem durações                    |
| 8                                                 | AH MD5 com ESP 3_DES com HMAC nulo, sem durações propostas | AH MD5 com ESP 3_DES MD5, sem durações                     |
| 9                                                 | AH SHA1 com ESP 3_DES SHA1, sem durações                    | ESP DES MD5                                                  |
| 10                                                | AH MD5 com ESP 3_DES MD5, sem durações                     | ESP DES SHA1, sem durações                                   |
| 11                                                | ESP DES MD5                                                  | AH SHA1 com ESP DES HMAC nulo, sem durações propostas        |
| 12                                                | ESP DES SHA1, sem durações                                   | AH MD5 com ESP DES HMAC nulo, sem durações propostas        |
| 13                                                | AH SHA1 com ESP DES HMAC nulo, sem durações propostas        | AH SHA1 com ESP DES SHA1, sem durações                      |
| 14                                                | AH MD5 com ESP DES HMAC nulo, sem durações propostas        | AH MD5 com ESP DES MD5, sem durações                       |
| 15                                                | AH SHA1 com ESP DES SHA1, sem durações                      | ESP SHA, sem durações                                        |
| 16                                                | AH MD5 com ESP DES MD5, sem durações                       | ESP MD5, sem durações                                        |
| 17                                                | -                                                            | AH SHA, sem durações                                         |
| 18                                                | -                                                            | AH MD5, sem durações                                         |


- Pode especificar a encriptação IPsec ESP NULO com gateways de VPN de Elevado Desempenho e baseados na rota. A encriptação baseada em Nulo não proporciona proteção aos dados em trânsito e só deve ser utilizada quando é preciso ter débito máximo e latência mínima.  Os clientes podem optar por utilizar esta opção em cenários de comunicação de vnet a vnet ou quando a encriptação está a ser aplicada noutro ponto da solução.

- Para a conectividade em vários locais através da Internet, utilize as predefinições do gateway de VPN do Azure com a encriptação e os algoritmos hash listados nas tabelas acima, para garantir a segurança da sua comunicação crítica.








<!--HONumber=ago16_HO4-->


