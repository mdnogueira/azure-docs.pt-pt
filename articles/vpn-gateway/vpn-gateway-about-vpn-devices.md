---
title: "Acerca dos Dispositivos VPN para as ligações de Gateway de Rede de VPNs das Redes Virtuais do Azure | Microsoft Docs"
description: "Este artigo fala sobre dispositivos VPN e parâmetros IPsec para ligações de Gateway de VPN S2S e contém hiperligações para instruções de configuração e exemplos."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: bbaf89bca07fd2d4c4a12403d2daa8323f4d7be5
ms.openlocfilehash: 12e7768665b8d360fe7241c9879bd1f7bfc63664


---
# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Acerca dos dispositivos de VPN para ligações de Gateway de Rede de VPNs
Precisa de um dispositivo VPN para configurar uma ligação de rede de VPNs (S2S). As ligações Site a Site podem ser utilizadas para criar uma solução híbrida ou sempre que pretender uma ligação segura entre a rede no local e a rede virtual. Este artigo aborda os dispositivos VPN compatíveis e os parâmetros de configuração.

> [!NOTE]
> Ao configurar uma ligação Site a Site, é preciso um endereço IP IPv4 destinado ao público para o dispositivo VPN.                                                                                                                                                                               
>
>

Se o dispositivo não aparecer na tabela de [Dispositivos VPN Validados](#devicetable), veja a secção [Dispositivos VPN Não Validados](#additionaldevices) deste artigo. É possível que o seu dispositivo funcione com o Azure. Para obter suporte para dispositivos VPN, contacte o fabricante do dispositivo.

**Itens a ter em atenção quando visualizar as tabelas:**

* A terminologia para o encaminhamento estático e dinâmico foi alterada. Encontrará provavelmente os dois termos. Não há nenhuma alteração de funcionalidade, só os nomes estão a mudar.
  * Encaminhamento Estático = PolicyBased
  * Encaminhamento Dinâmico = RouteBased
* As especificações para o gateway de VPN de Elevado Desempenho e para o gateway de VPN RouteBased são as mesmas, salvo indicação em contrário. Por exemplo, os dispositivos VPN validados compatíveis com os gateways de VPN RouteBased também são compatíveis com o gateway de VPN de Elevado Desempenho do Azure.

## <a name="a-namedevicetableavalidated-vpn-devices"></a><a name="devicetable"></a>Dispositivos VPN validados
Validámos uma série de dispositivos VPN padrão em parceria com os fornecedores dos dispositivos. Todos os dispositivos nas famílias de dispositivos contidas na lista seguinte deverão funcionar com gateways de VPN do Azure. Veja o artigo [Acerca do VPN Gateway](vpn-gateway-about-vpngateways.md) para verificar o tipo de gateway que tem de criar para a solução que pretende configurar.

Para obter ajuda na configuração do seu dispositivo VPN, veja as ligações que correspondem à família de dispositivos adequada. Para obter suporte para dispositivos VPN, contacte o fabricante do dispositivo.

| **Fornecedor** | **Família de dispositivos** | **Versão mínima do SO** | **PolicyBased** | **RouteBased** |
| --- | --- | --- | --- | --- |
| Allied Telesis |Routers VPN Série AR |2.9.2 |Brevemente |Não compatível |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Instruções de configuração](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Instruções de configuração](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série X |Barracuda Firewall 6.5 |[Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Não compatível |
| Brocade |Vyatta 5400 vRouter |Virtual Router 6.6R3 GA |[Instruções de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Não compatível |
| Check Point |Gateway de Segurança |R75.40<br>R75.40VS |[Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Não compatível |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Exemplos da Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Exemplos da Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e posterior |[Instruções de integração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Não compatível |
| Dell SonicWALL |Série TZ, Série NSA<br>Série SuperMassive<br>Série NSA Classe E |SonicOS 5.8.x<br>[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)<br>[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Guia de configuração do SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guia de configuração do SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Guia de configuração do SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guia de configuração do SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |Série BIG-IP |12.0 |[Instruções de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Instruções de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.x |[Instruções de configuração](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Instruções de configuração](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Instruções de configuração](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Não compatível |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Exemplos da Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Serviço de Encaminhamento e Acesso Remoto |Windows Server 2012 |Não compatível |[Exemplos da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Gateway de Segurança do Controlo da Missão |N/D |[Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Brevemente) |Não compatível |
| Palo Alto Networks |Todos os dispositivos com o PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 ou posterior<br>RouteBased: 7.0.5 ou posterior |[Instruções de configuração](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Instruções de configuração](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |Todos |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Instruções de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Instruções de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) Os routers da série ISR 7200 só suportam VPNs PolicyBased.

## <a name="a-nameadditionaldevicesanon-validated-vpn-devices"></a><a name="additionaldevices"></a>Dispositivos VPN não validados
Se não vir o seu dispositivo listado na tabela de Dispositivos VPN Validados, poderá, mesmo assim, funcionar com uma ligação Site a Site. Verifique se o seu dispositivo VPN cumpre os requisitos mínimos descritos na secção Requisitos de Gateway do artigo [Acerca dos Gateways de VPN](vpn-gateway-about-vpngateways.md). Os dispositivos que cumprem os requisitos mínimos devem também funcionar bem com os gateways de VPN. Contacte o fabricante do dispositivo para obter instruções adicionais de suporte e de configuração.

## <a name="editing-device-configuration-samples"></a>Editar os exemplos de configuração do dispositivo
Depois de transferir o exemplo de configuração do dispositivo VPN fornecido, terá de substituir alguns dos valores para que reflitam as definições do seu ambiente.

**Para editar um exemplo:**

1. Abra o exemplo com o Bloco de Notas.
2. Procure e substitua todas as cadeias <*texto*> pelos valores que dizem respeito ao seu ambiente. Não se esqueça de incluir < e >. O nome especificado deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante do dispositivo.

| **Texto de exemplo** | **Alterar para** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |O nome que escolheu para este objeto. Exemplo: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |O nome que escolheu para este objeto. Exemplo: myAzureNetwork |
| &lt;RP_AccessList&gt; |O nome que escolheu para este objeto. Exemplo: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |O nome que escolheu para este objeto. Exemplo: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |O nome que escolheu para este objeto. Exemplo: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Especifique o intervalo. Exemplo: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Especifique a máscara de sub-rede. Exemplo: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Especifique o intervalo no local. Exemplo: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Especifique a máscara de sub-rede no local. Exemplo: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Esta informação é específica da sua rede virtual e encontra-se no Portal de Gestão como **Endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt; |Esta informação é específicas da sua rede virtual e encontra-se no Portal de Gestão como Gerir Chave. |

## <a name="ipsec-parameters"></a>Parâmetros IPsec
> [!NOTE]
> Apesar de os valores apresentados na tabela seguinte serem suportados pelo VPN Gateway do Azure, não há atualmente um modo de especificar ou de selecionar uma combinação específica no VPN Gateway do Azure. Tem de especificar as eventuais restrições no dispositivo VPN no local. Além disso, tem de fixar MSS em 1350.
>
>

### <a name="ike-phase-1-setup"></a>Configuração da Fase 1 do IKE
| **Propriedade** | **PolicyBased** | **Gateway de VPN Standard ou de Elevado Desempenho e RouteBased** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de Autenticação |Chave Pré-partilhada |Chave Pré-partilhada |
| Algoritmos de Encriptação |AES256 AES128 3DES |AES256 3DES |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 |28 800 segundos |10 800 segundos |

### <a name="ike-phase-2-setup"></a>Configuração da Fase 2 do IKE
| **Propriedade** | **PolicyBased** | **Gateway de VPN Standard ou de Elevado Desempenho e RouteBased** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 2 |3600 segundos |3600 segundos |
| Duração (Débito) da Associação de Segurança (SA) da Fase 2 |102 400 000 KB |- |
| Ofertas de Encriptação e de Autenticação de SA de IPsec (por ordem de preferência) |1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D |Veja *Ofertas de Associação de Segurança (SA) de IPsec do Gateway RouteBased* (abaixo) |
| Perfect Forward Secrecy (PFS) |Não |Não (*) |
| Deteção de Elemento Inutilizado |Não suportado |Suportado |

(*) O Gateway do Azure como dispositivo de resposta IKE pode aceitar o Grupo PFS DH 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Ofertas de Associação de Segurança (SA) de IPsec do Gateway RouteBased
A tabela seguinte apresenta uma lista de Ofertas de Encriptação e Autenticação de SA de IPsec. As ofertas estão listadas pela ordem de preferência com que a oferta é apresentada ou aceite.

| **Ofertas de Encriptação e Autenticação de SA de IPsec** | **Gateway do Azure como iniciador** | **Gateway do Azure como dispositivo de resposta** |
| --- | --- | --- |
| 1 |ESP AES_256 SHA |ESP AES_128 SHA |
| 2 |ESP AES_128 SHA |ESP 3_DES MD5 |
| 3 |ESP 3_DES MD5 |ESP 3_DES SHA |
| 4 |ESP 3_DES SHA |AH SHA1 com ESP AES_128 com HMAC nulo |
| 5 |AH SHA1 com ESP AES_256 com HMAC nulo |AH SHA1 com ESP 3_DES com HMAC nulo |
| 6 |AH SHA1 com ESP AES_128 com HMAC nulo |AH MD5 com ESP 3_DES com HMAC nulo, sem durações propostas |
| 7 |AH SHA1 com ESP 3_DES com HMAC nulo |AH SHA1 com ESP 3_DES SHA1, sem durações |
| 8 |AH MD5 com ESP 3_DES com HMAC nulo, sem durações propostas |AH MD5 com ESP 3_DES MD5, sem durações |
| 9 |AH SHA1 com ESP 3_DES SHA1, sem durações |ESP DES MD5 |
| 10 |AH MD5 com ESP 3_DES MD5, sem durações |ESP DES SHA1, sem durações |
| 11 |ESP DES MD5 |AH SHA1 com ESP DES HMAC nulo, sem durações propostas |
| 12 |ESP DES SHA1, sem durações |AH MD5 com ESP DES HMAC nulo, sem durações propostas |
| 13 |AH SHA1 com ESP DES HMAC nulo, sem durações propostas |AH SHA1 com ESP DES SHA1, sem durações |
| 14 |AH MD5 com ESP DES HMAC nulo, sem durações propostas |AH MD5 com ESP DES MD5, sem durações |
| 15 |AH SHA1 com ESP DES SHA1, sem durações |ESP SHA, sem durações |
| 16 |AH MD5 com ESP DES MD5, sem durações |ESP MD5, sem durações |
| 17 |- |AH SHA, sem durações |
| 18 |- |AH MD5, sem durações |

* Pode especificar a encriptação IPsec ESP NULL com gateways de VPN de Elevado Desempenho e RouteBased. A encriptação baseada em Nulo não proporciona proteção aos dados em trânsito e só deve ser utilizada quando é preciso ter débito máximo e latência mínima.  Os clientes podem optar por utilizar esta opção em cenários de comunicação de VNet a VNet ou quando a encriptação está a ser aplicada noutro ponto da solução.
* Para a conectividade em vários locais através da Internet, utilize as predefinições do gateway de VPN do Azure com a encriptação e os algoritmos hash listados nas tabelas acima, para garantir a segurança da sua comunicação crítica.



<!--HONumber=Dec16_HO2-->


