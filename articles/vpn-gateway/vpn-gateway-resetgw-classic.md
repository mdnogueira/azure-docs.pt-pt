<properties
   pageTitle="Repor um VPN Gateway do Azure | Microsoft Azure"
   description="Este artigo explica como repor o VPN Gateway do Azure. O artigo aplica-se a gateways de VPN criados utilizando o modelo de implementação clássica."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/26/2016"
   ms.author="cherylmc"/>

# Repor um VPN Gateway do Azure com o PowerShell


Este artigo explica como repor o VPN Gateway do Azure com cmdlets PowerShell. Estas instruções aplicam-se ao modelo de implementação clássica.  Ainda não temos um cmdlet ou uma API REST para repor um gateway de VPN para redes virtuais criadas com o modelo de implementação Resource Manager, mas já estão em curso. Pode saber se o gateway de VPN foi criado com o modelo de implementação clássica visualizando a rede virtual no Portal do Azure. As redes virtuais que foram criadas com o modelo de implementação clássica são apresentadas na parte Rede Virtual (clássica) do Portal do Azure.

Repor o VPN Gateway do Azure poderá ser útil se perder a conetividade VPN em vários locais num ou mais túneis VPN S2S. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Quando utiliza o cmdlet *Reset-AzureVNetGateway*, reinicia o gateway e, em seguida, volta a aplicar as configurações em vários locais ao mesmo. O gateway irá manter o endereço IP público que já tem, ou seja, não terá de atualizar a configuração do router da VPN com um novo endereço IP público para o VPN Gateway do Azure.  


Antes de repor o gateway, verifique os itens principais listados abaixo para cada túnel de rede de VPNs (S2S) do IPsec. Qualquer erro de correspondência nos itens desligará os túneis VPN S2S. A verificação e a correção das configurações do VPN Gateway do Azure e do gateway no local evitam reinícios desnecessários e interrupções para as outras ligações nos gateways a funcionar.

Verifique os itens seguintes antes de repor o gateway.

- Os endereços IP de Internet (VIPs) do VPN Gateway do Azure e do gateway de VPN no local estão corretamente configurados nas políticas de VPN do Azure e no local.
- A chave pré-partilhada tem de ser igual no VPN Gateway do Azure e no gateway no local.
- Se aplicar uma configuração IPsec/IKE específica, tais como encriptação, algoritmos hash e PFS (Perfect Forward Secrecy), confirme se o VPN Gateway do Azure e o gateway no local têm as mesmas configurações.


## Repor um VPN Gateway com o PowerShell

O cmdlet PowerShell para repor o VPN Gateway do Azure é *Reset-AzureVNetGateway*. Cada VPN Gateway do Azure é composto por duas instâncias da VM em execução com uma configuração ativa/em modo de espera. Quando o comando é emitido, a instância ativa atual do VPN Gateway do Azure será reiniciada imediatamente. Existirá um intervalo breve durante a ativação pós-falha da instância ativa (que está a ser reiniciada) para a instância no modo de espera. O intervalo deve ser inferior a um minuto. 

O exemplo seguinte repõe o VPN Gateway do Azure para a rede virtual denominada “ContosoVNet”.
 
        Reset-AzureVNetGateway –VnetName “ContosoVNet” 

        Error          :
        HttpStatusCode : OK
        Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
        Status         : Successful
        RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
        StatusCode     : OK


Se a ligação não for restaurada após o primeiro reinício, execute o mesmo comando novamente para reiniciar a segunda instância da VM (o novo gateway ativo). Se forem pedidos dois reinícios seguidos, haverá um período ligeiramente superior em que ambas as instâncias da VM (ativa e no modo de espera) estão a ser reiniciadas. Neste caso, resultará num intervalo maior na conetividade da VPN, até 2 a 4 minutos para as VMs concluírem os reinícios.

Depois de dois reinícios, se ainda ocorrerem problemas de conetividade em vários locais, abra um pedido de suporte no Portal Clássico do Azure para contactar o suporte do Microsoft Azure.

## Passos seguintes
    
Veja a [Referência do PowerShell](https://msdn.microsoft.com/library/azure/mt270366.aspx) para obter mais informações sobre este cmdlet.









<!--HONumber=Jun16_HO2-->


