---
title: Requisitos do sistema StorSimple | Microsoft Docs
description: "Descreve o software, funcionamento em rede e os requisitos de elevada disponibilidade e melhores práticas para uma solução do Microsoft Azure StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2b6ca34a-d758-48e7-ab1e-4fdd80cf48d4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 55e1cd90df0e4413ce027361c636257d823a50d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-software-high-availability-and-networking-requirements"></a>Software do StorSimple, elevada disponibilidade e requisitos de rede
## <a name="overview"></a>Descrição geral
Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve os requisitos de sistema importantes e melhores práticas para o dispositivo StorSimple e para os clientes de armazenamento aceder ao dispositivo. Recomendamos que reveja as informações cuidadosamente antes de implementar o sistema StorSimple e, em seguida, se referem novamente à mesma conforme necessário durante a implementação e operação subsequente.

Os requisitos de sistema incluem:

* **Requisitos de software para clientes de armazenamento** -descreve os sistemas operativos suportados e quaisquer requisitos adicionais para nesses sistemas operativos.
* **Requisitos de rede para o dispositivo StorSimple** -fornece informações sobre as portas que têm de ser aberta na firewall para permitir tráfego de gestão, iSCSI ou em nuvem.
* **Requisitos de elevada disponibilidade para StorSimple** - descreve os requisitos de elevada disponibilidade e melhor práticas para o seu computador de anfitrião e o dispositivo StorSimple. 

## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de armazenamento
Os seguintes requisitos de software são para os clientes de armazenamento que acedem ao seu dispositivo StorSimple.

| Sistemas operativos suportados | Versão necessária | Notas/requisitos adicionais |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012R2, 2016 |Volumes do StorSimple iSCSI são suportadas para utilização no apenas os tipos de disco de Windows seguintes:<ul><li>Volume simples num disco básico</li><li>Volume Simple e espelhado no disco dinâmico</li></ul>Apenas os software iniciadores iSCSI presentes nativamente no sistema operativo são suportados. Os iniciadores iSCSI de hardware não são suportados.<br></br>Windows Server 2012 e 2016 o aprovisionamento dinâmico e funcionalidades ODX são suportadas se estiver a utilizar um volume do StorSimple iSCSI.<br><br>Criar StorSimple com aprovisionamento dinâmico e totalmente aprovisionado volumes. -Não é possível criar volumes parcialmente aprovisionados.<br><br>Reformatação de um volume com aprovisionamento dinâmico, pode demorar muito tempo. Recomendamos a eliminação do volume e, em seguida, criar um novo em vez de reformatação. No entanto, se ainda preferir reformatar um volume:<ul><li>Execute o seguinte comando antes do reformat para evitar atrasos de recuperação de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Após a formatação estiver concluída, utilize o seguinte comando para ativar novamente a recuperação de espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique a correção do Windows Server 2012, conforme descrito em [KB 2878635](https://support.microsoft.com/kb/2870270) para o seu computador Windows Server.</li></ul></li></ul></ul> Se estiver a configurar o Snapshot Manager do StorSimple ou StorSimple adaptador para o SharePoint, vá para [requisitos de Software para os componentes opcionais](#software-requirements-for-optional-components). |
| VMWare ESX |5.5 e 6.0 |Suportado com o VMWare vSphere como cliente de iSCSI. Funcionalidade de bloqueio VAAI é suportada com o VMware vSphere nos dispositivos StorSimple. |
| Linux RHEL/CentOS |5, 6 e 7 |Suporte para Linux iSCSI clientes com versões de iniciador do open iSCSI 5, 6 e 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX não é suportado com StorSimple.
> 
> 

## <a name="software-requirements-for-optional-components"></a>Requisitos de software para os componentes opcionais
Os seguintes requisitos de software são para os componentes opcionais do StorSimple (Snapshot Manager do StorSimple e StorSimple adaptador para o SharePoint).

| Componente | Plataforma de anfitrião | Notas/requisitos adicionais |
| --- | --- | --- |
| Snapshot Manager do StorSimple |Windows Server 2008 R2 SP1, 2012, 2012R2 |Utilização do Snapshot Manager do StorSimple no Windows Server é necessária para a cópia de segurança/restauro de discos dinâmicos espelhados e para quaisquer cópias de segurança consistentes com aplicações.<br> Snapshot Manager do StorSimple é suportado apenas no Windows Server 2008 R2 SP1 (64 bits), o Windows 2012 R2 e o Windows Server 2012.<ul><li>Se estiver a utilizar janela Server 2012, tem de instalar o .NET 3.5 – 4.5 antes de instalar o Snapshot Manager do StorSimple.</li><li>Se estiver a utilizar o Windows Server 2008 R2 SP1, tem de instalar Windows Management Framework 3.0 antes de instalar o Snapshot Manager do StorSimple.</li></ul> |
| Adaptador do StorSimple para o SharePoint |Windows Server 2008 R2 SP1, 2012, 2012R2 |<ul><li>Placa StorSimple para SharePoint só é suportada no SharePoint 2010 e o SharePoint 2013.</li><li>RBS requer o SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para o dispositivo StorSimple
O dispositivo StorSimple é um dispositivo bloqueado para / para baixo. No entanto, as portas tem de ser aberta na firewall para permitir a iSCSI, de nuvem e de tráfego de gestão. A tabela seguinte lista as portas que têm de ser aberta na firewall. Nesta tabela, *no* ou *entrada* refere-se a direção pedidos de cliente recebidos aceder a partir do qual o dispositivo. *Saída* ou *saída* refere-se a direção na qual o dispositivo StorSimple envia dados externamente, para além da implementação: por exemplo, a saída à Internet.

| Um número de porta<sup>1,2</sup> | Entrada ou saída | Âmbito de porta | Necessário | Notas |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Saída |WAN |Não |<ul><li>Porta de saída é utilizada para acesso à Internet para obter atualizações.</li><li>O proxy web de saída é configurável de utilizador.</li><li>Para permitir atualizações do sistema, esta porta têm de estar aberta para o controlador de IPs fixo.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Saída |WAN |Sim |<ul><li>Porta de saída é utilizada para aceder aos dados na nuvem.</li><li>O proxy web de saída é configurável de utilizador.</li><li>Para permitir atualizações do sistema, esta porta têm de estar aberta para o controlador de IPs fixo.</li><li>Esta porta é também utilizada em ambos os controladores para recolha de lixo.</li></ul> |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor DNS baseado na Internet. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseado na Internet. |
| TCP 9354 |Saída |WAN |Sim |A porta de saída é utilizada pelo dispositivo StorSimple para comunicar com o serviço StorSimple Manager. |
| 3260 (iSCSI) |No |LAN |Não |Esta porta é utilizada para aceder aos dados através de iSCSI. |
| 5985 |No |LAN |Não |Porta de entrada é utilizada pelo Snapshot Manager do StorSimple para comunicar com o dispositivo StorSimple.<br>Esta porta é também utilizada quando a ligação remota para o Windows PowerShell para StorSimple através de HTTP. |
| 5986 |No |LAN |Não |Esta porta é utilizada quando a ligação remota para o Windows PowerShell para StorSimple através de HTTPS. |

<sup>1</sup> nenhuma porta de entrada tem de estar abertas na Internet pública.

<sup>2</sup> se várias portas de efetuar uma configuração de gateway, a ordem de saída tráfego encaminhado será determinada com base na ordem de encaminhamento porta descrita [porta encaminhamento](#routing-metric), abaixo.

<sup>3</sup> o controlador de IPs fixo no dispositivo StorSimple tem de ser encaminháveis e conseguir estabelecer ligação à Internet diretamente ou através do proxy web configurada. Os endereços IP fixos são utilizados para manutenção de atualizações para o dispositivo. Se os controladores de dispositivo não é possível ligar à Internet através dos IPs fixos, não poderá atualizar o dispositivo StorSimple.

> [!IMPORTANT]
> Certifique-se de que a firewall não modificar ou desencriptar o tráfego SSL entre o dispositivo StorSimple e o Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para as regras de firewall
Os administradores de rede, muitas vezes, podem configurar regras de firewall avançada com base nos padrões de URL para filtrar a entrada e o tráfego de saída. O dispositivo StorSimple e o serviço StorSimple Manager dependem outras aplicações da Microsoft, tais como o Service Bus do Azure, controlo de acesso do Azure Active Directory, as contas do storage e servidores do Microsoft Update. Os padrões de URL associados estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender que podem alterar os padrões de URL associados estas aplicações. Isto por sua vez, irá necessitar de administrador de rede para monitorizar e atualizar regras de firewall para o StorSimple como e quando necessário.

Recomendamos que defina as regras de firewall para o tráfego de saída, com base em endereços IP, fixos liberally na maioria dos casos do StorSimple. No entanto, pode utilizar as informações abaixo para definir regras de firewall avançada que são necessárias para criar ambientes seguras.

> [!NOTE]
> O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede ativada. O destino IPs deve ser definido como [intervalos IP do datacenter do Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).
> 
> 

#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para o portal do Azure
| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Serviço do Gestor de dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de autenticação |Interfaces de rede com capacidade de nuvem |
| `https://*.backup.windowsazure.com` |Registo de dispositivo |Apenas dados 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Revogação de certificados |Interfaces de rede com capacidade de nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorização e de contas do storage do Azure |Interfaces de rede com capacidade de nuvem |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |IPs fixos do controlador só |
| `http://*.deploy.akamaitechnologies.com` |CDN da Akamai |IPs fixos do controlador só |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de suporte |Interfaces de rede com capacidade de nuvem |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para o portal do Azure Government
| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*` <br>`https://login-us.microsoftonline.com` |Serviço do Gestor de dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de autenticação |Interfaces de rede com capacidade de nuvem |
| `https://*.backup.windowsazure.us` |Registo de dispositivo |Apenas dados 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Revogação de certificados |Interfaces de rede com capacidade de nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorização e de contas do storage do Azure |Interfaces de rede com capacidade de nuvem |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |IPs fixos do controlador só |
| `http://*.deploy.akamaitechnologies.com` |CDN da Akamai |IPs fixos do controlador só |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de suporte |Interfaces de rede com capacidade de nuvem |

### <a name="routing-metric"></a>Métrica de encaminhamento
Uma métrica de encaminhamento está associada com as interfaces e o gateway que encaminhar os dados para as redes especificadas. Métrica de encaminhamento é utilizar o protocolo de encaminhamento para calcular o caminho para um determinado destino melhor se aprende as que existem vários caminhos para o mesmo destino. Menor a métrica de encaminhamento, maior preferência.

No contexto do StorSimple, se várias interfaces de rede e gateways estão configurados para tráfego de canal, as métricas de encaminhamento ficará para reproduzir para determinar a ordem relativa na qual irão obter utilizadas as interfaces. As métricas de encaminhamento não podem ser alteradas pelo utilizador. No entanto pode utilizar o `Get-HcsRoutingTable` cmdlet para imprimir a tabela de encaminhamento (e as métricas) no dispositivo StorSimple. Obter mais informações sobre o cmdlet Get-HcsRoutingTable no [StorSimple de resolução de problemas de implementação](storsimple-troubleshoot-deployment.md).

Os algoritmos de métricos de encaminhamento são diferentes consoante a versão do software em execução no dispositivo StorSimple.

**Versões antes da atualização 1**

Isto inclui as versões de software antes da atualização 1 como DG, 0.1, 0.2 ou 0,3 versão. A ordem, com base nas métricas de encaminhamento é o seguinte:

   *Última configurado interface de rede 10 GbE > interface de rede de outros 10 GbE > pela última vez configurado 1 interface de rede GbE > interface de rede de outro 1 GbE*

**Versões a partir da atualização 1 e antes da atualização 2**

Isto inclui as versões de software, como 1, 1.1 ou 1.2. A ordem, com base nas métricas de encaminhamento é decidida da seguinte forma:

   *DATA 0 > pela última vez configurado o interface de rede 10 GbE > interface de rede de outros 10 GbE > pela última vez configurado 1 interface de rede GbE > interface de rede de outro 1 GbE*

   Na atualização 1, a métrica de encaminhamento de dados 0 é efetuada a mais baixa; Por conseguinte, todas as o nuvem-tráfego é encaminhado através de dados 0. Tome nota deste se existirem mais de uma interface de rede de nuvem ativada no dispositivo StorSimple.

**Versões iniciados a partir da atualização 2**

A atualização 2 tem vários melhoramentos relacionado com redes e as métricas de encaminhamento foi alterada. O comportamento pode ser explicado da seguinte forma.

* Um conjunto de valores predeterminados tenham sido atribuídos a interfaces de rede.     
* Considere uma tabela de exemplo mostrada abaixo, com valores atribuídos para as várias interfaces de rede quando este for cloud-ativado ou desativado de nuvem, mas com um gateway configurado. Tenha em atenção de que os valores aqui atribuídos são apenas valores de exemplo.

    | Interface de rede | Capacidade de nuvem | Nuvem-desativado com gateway |
    |-----|---------------|---------------------------|
    | Dados 0  | 1            | -                        |
    | Dados 1  | 2            | 20                       |
    | Dados 2  | 3            | 30                       |
    | Dados 3  | 4            | 40                       |
    | Dados 4  | 5            | 50                       |
    | Dados 5  | 6            | 60                       |


* A ordem em que o tráfego de nuvem será encaminhado através de interfaces de rede é:
  
    *Dados 0 > dados 1 > data 2 > dados 3 > dados 4 > dados 5*
  
    Isto pode ser explicado no seguinte exemplo.
  
    Considere um dispositivo StorSimple com duas interfaces de rede ativado para a nuvem, dados 0 e 5 de dados. Dados 1 a 4 de dados são nuvem-desativado, mas tem um gateway configurado. A ordem em que o tráfego será encaminhado para este dispositivo será:
  
    *Dados 0 (1) > dados 5 (6) > dados 1 (20) > dados 2 (30) > dados 3 (40) > dados 4 (50)*
  
    *onde os números de parênteses indicam as respetivas métricas de encaminhamento.*
  
    Se falhar a Data 0, o tráfego de nuvem irá obter encaminhado através de dados 5. Uma vez que um gateway está configurado em todos os outro rede, se Data 0 e 5 de dados falhar, o tráfego de nuvem passarão 1 de dados.
* Se falhar uma interface de rede ativado para a nuvem, em seguida, são 3 repetições com um atraso segundo 30 para estabelecer a ligação para a interface. Se todas as tentativas falharem, o tráfego é encaminhado para a seguinte disponível ativado para a nuvem interface conforme determinado pela tabela de encaminhamento. Se todos os rede ativado para a nuvem das interfaces falhar, o dispositivo serão ativadas pós-falha para o controlador (sem reinício neste caso).
* Se existir uma falha de VIP para uma interface de rede com o iSCSI, vai ser 3 repetições com um atraso de 2 segundos. Este comportamento tem stayed os mesmos a partir de versões anteriores. Se falharem a todas as interfaces de rede iSCSI, irá ocorrer uma ativação pós-falha de controlador (accompanied por um reinício).
* É também desencadeado um alerta no dispositivo StorSimple quando ocorre uma falha de VIP. Para obter mais informações, aceda a [referência rápida de alerta](storsimple-manage-alerts.md).
* Em termos de tentativas, iSCSI terá precedência sobre na nuvem.
  
    Considere o seguinte exemplo: StorSimple de um dispositivo tem duas interfaces de rede ativadas, os dados 0 e 1 de dados. Data 0 é ativado para a nuvem enquanto 1 de dados é tanto na nuvem e o iSCSI ativado. Outras interfaces de rede neste dispositivo estão ativados para a nuvem ou de iSCSI.
  
    Se falhar de 1 de dados, dado é o último interface de rede iSCSI, tal resultará numa ativação pós-falha controlador a 1 de dados no outro controlador de.

### <a name="networking-best-practices"></a>Melhores práticas de rede
Para além dos requisitos de rede acima, para otimizar o desempenho da solução StorSimple,. respeitar as seguintes melhores práticas:

* Certifique-se de que o dispositivo StorSimple tem uma largura de banda Mbps dedicado 40 (ou mais) disponível em todas as vezes. Não deve ser partilhada este largura de banda (ou a alocação deve ser garantida através da utilização de políticas de QoS) com outras aplicações.
* Certifique-se de que a conectividade de rede à Internet está disponível em todas as vezes. Esporádicas ou pouco fiáveis ligações à Internet para os dispositivos, incluindo sem conectividade Internet contratutal, irão resultar numa configuração não suportada.
* Isole o tráfego iSCSI e na nuvem por ter dedicados interfaces de rede no seu dispositivo para o acesso de iSCSI e na nuvem. Para obter mais informações, consulte como [modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces) no dispositivo StorSimple.
* Não utilize uma configuração de protocolo de controlo de agregação de ligação (LACP) para as interfaces de rede. Esta é uma configuração suportada.

## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de elevada disponibilidade do StorSimple
A plataforma de hardware que está incluída com a solução StorSimple tem disponibilidade e fiabilidade funcionalidades que fornecem uma foundation para uma infraestrutura de armazenamento de elevada disponibilidade, com tolerância a falhas no seu centro de dados. No entanto, existem requisitos e melhores práticas que devem estar em conformidade com para ajudar a garantir a disponibilidade da solução StorSimple. Antes de implementar StorSimple, reveja com atenção os seguintes requisitos e melhores práticas para o dispositivo StorSimple e computadores anfitriões ligado.

Para obter mais informações sobre como monitorizar e manter os componentes de hardware do dispositivo StorSimple, aceda a [utilizar o serviço StorSimple Manager para monitorizar componentes de hardware e estado](storsimple-monitor-hardware-status.md) e [hardware do StorSimple substituição de componente](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos de elevada disponibilidade e procedimentos para o dispositivo StorSimple
Reveja as seguintes informações cuidadosamente para garantir a elevada disponibilidade do dispositivo StorSimple.

#### <a name="pcms"></a>PCMs
Dispositivos StorSimple incluem redundante, frequente-swappable energia e arrefecimento módulos (PCMs). Cada PCM tem capacidade suficiente para fornecer um serviço para o chassis de todo. Para garantir a elevada disponibilidade, ambos os PCMs tem de estar instalados.

* Ligar o seu PCMs a fontes de alimentação diferentes para fornecer disponibilidade se falhar de uma origem de energia.
* Se um PCM falhar, pedir imediatamente uma substituição.
* Remova um PCM falhada apenas quando tiver de substituição e está pronto para instalá-lo.
* Não remova ambas PCMs em simultâneo. O módulo PCM inclui o módulo de bateria cópia de segurança. A ambos os PCMs fará um encerramento sem proteção de bateria e não será possível guardar o estado do dispositivo. Para obter mais informações sobre a bateria, aceda a [manter o módulo de cópia de segurança de bateria](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos de controlador
Dispositivos StorSimple incluem módulos do controlador de acesso frequente-swappable, redundante. Os módulos de controlador funcionam de forma ativo/passivo. Em qualquer momento, um controlador está ativo e o módulo está a fornecer serviço, enquanto o módulo de controlador é passivo. O módulo de controlador passivo está ligado e se fica operacional se o módulo do Active Directory controlador falha ou for removido. Cada módulo controlador tem capacidade suficiente para fornecer um serviço para o chassis de todo. Ambos os módulos do controlador tem de estar instalados para garantir a elevada disponibilidade.

* Certifique-se de que ambos os módulos de controlador estão instalados em qualquer momento.
* Se falhar um módulo de controlador, pedir imediatamente uma substituição.
* Remove um módulo de controlador falhada apenas quando tiver de substituição e está pronto para instalá-lo. Remover um módulo de períodos prolongados afetará o airflow e, por conseguinte, ao arrefecimento do sistema.
* Certifique-se de que as ligações de rede para ambos os módulos do controlador são idênticas e de que as interfaces de rede ligada tem uma configuração de rede idênticas.
* Se um módulo de controlador falha ou tiver substituição, certifique-se de que o módulo de controlador está no Estado ativo antes de substituir o módulo de controlador falhada. Para verificar que se ativa um controlador, aceda ao [identificar controlador ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
* Não remova ambos os módulos de controlador ao mesmo tempo. Se uma ativação pós-falha controlador está em curso, não encerrar o módulo de controlador em modo de espera ou removê-lo a partir de chassis.
* Após uma ativação pós-falha de controlador, aguarde, pelo menos, cinco minutos antes de remover o módulo de controlador.

#### <a name="network-interfaces"></a>Interfaces de rede
StorSimple controlador módulos do dispositivo cada tem quatro de 1 Gigabit e 10 de duas interfaces de rede de Gigabit Ethernet.

* Certifique-se de que as ligações de rede para ambos os módulos do controlador são idênticas e interfaces de rede que as interfaces de módulo de controlador estão ligadas a ter uma configuração de rede idênticas.
* Sempre que possível, implemente ligações de rede entre diferentes comutadores, para garantir a disponibilidade do serviço em caso de falha de dispositivo de rede.
* Quando desligar o único ou última interface preparada para iSCSI restantes (com IPs atribuídos), desative primeiro a interface e, em seguida, desligue os cabos. Se a interface está desligada pela primeira vez, em seguida, fará com que o controlador de Active Directory para a ativação pós-falha para o controlador passivo. Se o controlador de passivo tem também as interfaces correspondentes desligadas, ambos os controladores irão reiniciar várias vezes antes de settling num controlador de um.
* Ligar pelo menos duas interfaces de dados para a rede de cada módulo de controlador.
* Se tiver ativado as duas interfaces de 10 GbE, implementá-los através de comutadores diferentes.
* Sempre que possível, utilize o MPIO nos servidores para se certificar de que os servidores podem tolerar uma ligação, a rede ou a falha de interface.

Para obter mais informações sobre o seu dispositivo para elevada disponibilidade e desempenho de rede, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs
Dispositivos StorSimple incluem discos de estado sólido (SSDs) e unidades de disco rígido (HDDs) que estão protegidas utilizando espelhada espaços. Utilização de espaços espelhados assegura que o dispositivo é capaz de tolerar a falha de uma ou mais SSDs ou HDDs.

* Certifique-se de que todos os módulos SSD e HDD estão instalados.
* Se falhar um SSD ou HDD, pedir imediatamente uma substituição.
* Se um SSD ou HDD falha ou necessita de substituição, certifique-se de que remova apenas SSD ou HDD que necessita de substituição.
* Não remova mais do que um SSD ou HDD do sistema em qualquer ponto no tempo.
  Uma falha de 2 ou mais discos de determinado tipo (HDD, SSD) ou falha consecutivo dentro de um curto período de tempo pode resultar na perda de dados de avaria e potenciais do sistema. Se isto ocorrer, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md) para obter assistência.
* Durante a substituição, monitorizar o **estado do Hardware** no **manutenção** página para as unidades de SSDs e HDDs. Um Estado de verificação verde indica que os discos estão em bom estado ou OK, enquanto que o ponto de exclamação uma vermelha indica uma falha SSD ou HDD.
* Recomendamos que configure instantâneos de nuvem para todos os volumes que é necessário proteger em caso de falha de sistema.

#### <a name="ebod-enclosure"></a>Inclusão EBOD
Modelo do dispositivo StorSimple 8600 inclui um bastidor expandido Bunch de discos (EBOD), para além de inclusão principal. Um EBOD contém controladores EBOD e unidades de disco rígido (HDDs) que estão protegidas utilizando espelhada espaços. Utilização de espaços espelhados assegura que o dispositivo é capaz de tolerar a falha de um ou mais HDDs. A inclusão EBOD está ligada ao bastidor principal através de cabos SAS redundantes.

* Certifique-se de que ambos os módulos de controlador de inclusão EBOD, cabos SAS tanto todas as unidades de disco rígido estão instaladas em qualquer momento.
* Se um módulo de controlador de inclusão EBOD falhar, pedir imediatamente uma substituição.
* Se um módulo de controlador de inclusão EBOD falhar, certifique-se de que o módulo de controlador está ativo antes de substituir o módulo falhou. Para verificar que se ativa um controlador, aceda ao [identificar controlador ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
* Durante a substituição de módulo de controlador um EBOD, monitorizar continuamente o estado do componente no serviço StorSimple Manager acedendo **manutenção** > **estado do Hardware**.
* Se um cabo SAS falha ou necessita de substituição (deve estar envolvido Support da Microsoft para efetuar essa uma determinação), certifique-se de que remove apenas o cabo SAS que necessita de substituição.
* Não em simultâneo remova os dois cabos SAS do sistema em qualquer ponto no tempo.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de elevada disponibilidade para os seus computadores de anfitrião
Reveja com atenção estas melhores práticas para garantir a elevada disponibilidade dos anfitriões ligados ao dispositivo StorSimple.

* Configurar StorSimple com [configurações de cluster de servidor de ficheiros de dois nós][1]. Ao remover pontos únicos de falha e a criação de redundância do lado do anfitrião, a solução completa torna-se elevada.
* Utilize partilhas continuamente disponíveis (AC) disponíveis no Windows Server 2012 (SMB 3.0) para elevada disponibilidade durante a ativação pós-falha dos controladores de armazenamento. Para obter informações adicionais para configurar clusters de servidor de ficheiros e partilhas continuamente disponíveis com o Windows Server 2012, consulte este [demonstração vídeo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre os limites de sistema do StorSimple](storsimple-limits.md).
* [Saiba como implementar a sua solução StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
