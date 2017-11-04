---
title: "Teste débito de rede de VM do Azure | Microsoft Docs"
description: "Saiba como testar o débito de rede de máquina virtual do Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: ccebc722386a19014674d7a59757a3685bd50793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Largura de banda/débito (NTTTCP) de teste

Quando testar o desempenho de débito de rede no Azure, é melhor utilizar uma ferramenta que destina-se a rede para fins de teste e minimiza a utilização de outros recursos que pode afetar o desempenho. Recomenda-se NTTTCP.

Copie a ferramenta para duas VMs do Azure o mesmo tamanho. Uma VM funciona como remetente e o outro RECETOR.

#### <a name="deploying-vms-for-testing"></a>Implementar as VMs para fins de teste
Para efeitos deste teste, as duas VMs devem ter o mesmo serviço de nuvem ou o mesmo conjunto de disponibilidade, de modo que pode utilizar os IPs interno e excluir os balanceadores de carga de teste. É possível testar com o VIP, mas este tipo de teste está fora do âmbito deste documento.
 
Tome nota do endereço IP do RECETOR. Vamos chamar esse IP "a.b.c.r"

Anote o número de núcleos na VM. Vamos chamar isto "\#núm\_núcleos"
 
Execute o teste NTTTCP de 300 segundos (ou 5 minutos) na VM do remetente e o recetor VM.

Sugestão: Ao configurar este teste pela primeira vez, pode experimentar um período de teste mais curto para obter mais cedo comentários. Depois da ferramenta está a funcionar conforme esperado, prolonga o período de teste a 300 segundos para os resultados mais exatos.

> [!NOTE]
> O remetente **e** recetor tem de especificar **o mesmo** testar o parâmetro de duração (-t).

Para um único fluxo TCP de teste para 10 segundos:

Parâmetros de recetor: ntttcp - r -t 10 - P 1

Parâmetros de remetente: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> O exemplo anterior só deve ser utilizado para confirmar a sua configuração. Válido exemplos de teste descritos mais tarde neste documento.

## <a name="testing-vms-running-windows"></a>VMs de teste com o WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Obter NTTTCP para as VMs.

Transferir a versão mais recente: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Ou procure-lo se movido: <https://www.bing.com/search?q=ntttcp+download> \< – deve ser atingido pela primeira vez

Considere colocar NTTTCP na pasta separada, como c:\\ferramentas

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Permitir NTTTCP através da firewall do Windows
No RECETOR, crie uma regra de permissão na Firewall do Windows para permitir o tráfego NTTTCP a chegada. É mais fácil permitir que o programa NTTTCP todo pelo nome em vez de permitir portas TCP específicas de entrada.

Permitir ntttcp através da Firewall do Windows como esta:

netsh advfirewall firewall adicionar programa de regra =\<caminho\>\\ntttcp.exe nome = "ntttcp" protocolo = quaisquer dir = na ação = permitir enable = yes perfil = ANY

Por exemplo, se tiver copiado ntttcp.exe para o "c:\\ferramentas" pasta, isto seria o comando: 

netsh advfirewall firewall adicionar programa de regra = c:\\ferramentas\\ntttcp.exe nome = "ntttcp" protocolo = quaisquer dir = na ação = permitir enable = yes perfil = ANY

#### <a name="running-ntttcp-tests"></a>Testes NTTTCP em execução

Iniciar o NTTTCP o RECETOR (**executar a partir de CMD**, não a partir do PowerShell):

ntttcp - r-m [2\*\#núm\_núcleos],\*, a.b.c.r -t 300

Se a VM tem quatro núcleos e um endereço IP de 10.0.0.4, deverá ter o seguinte aspeto:

ntttcp - r – m 8,\*, 10.0.0.4 -t 300


Iniciar o NTTTCP o remetente (**executar a partir de CMD**, não a partir do PowerShell):

ntttcp -s – m 8,\*, 10.0.0.4 -t 300 

Aguarde que os resultados.


## <a name="testing-vms-running-linux"></a>Testar as VMs com LINUX:

Utilize nttcp para linux. Está disponível no <https://github.com/Microsoft/ntttcp-for-linux>

Nas VMs Linux (remetente e RECETOR), execute estes comandos para preparar ntttcp para linux nas suas VMs:

CentOS - instalação Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - instalação Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Certifique- e instalar em ambas:
``` bash
 git clone <https://github.com/Microsoft/ntttcp-for-linux>
 cd ntttcp-for-linux/src
 make && make install
```

Como no exemplo Windows, partimos do que IP do RECETOR de Linux está 10.0.0.4

Inicie o RECETOR NTTTCP para Linux:

``` bash
ntttcp -r -t 300
```

E no remetente, execute:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Teste comprimento assume a predefinição de 60 segundos se nenhum parâmetro de hora é dado

## <a name="testing-between-vms-running-windows-and-linux"></a>Teste entre as VMs que executam o Windows e LINUX:

Nos cenários deste iremos deve ativar o modo de sem sincronização pelo que pode executar o teste. Isto é feito utilizando o **sinalizador -N** para Linux e **sinalizador -ns** para Windows.

#### <a name="from-linux-to-windows"></a>Do Linux para o Windows:

Recetor <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Remetente <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>A partir do Windows para Linux:

Recetor <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Remetente <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```

## <a name="next-steps"></a>Passos seguintes
* Consoante os resultados, poderá haver espaço para [otimizar máquinas de débito de rede](virtual-network-optimize-network-bandwidth.md) para o seu cenário.
* Saiba mais com [Azure Virtual Network perguntas mais frequentes sobre (FAQ)](virtual-networks-faq.md)
