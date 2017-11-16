---
title: Requisitos de sistema de matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: Saiba mais sobre o software e requisitos de rede para a matriz de Virtual StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.openlocfilehash: 5d01523f326bd7e2518bff06e62ae62db8f318d3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema da Matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral
Este artigo descreve os requisitos de sistema importantes para a matriz Virtual do Microsoft Azure StorSimple e para os clientes de armazenamento aceder à matriz. Recomendamos que reveja as informações cuidadosamente antes de implementar o sistema StorSimple e, em seguida, se referem novamente à mesma conforme necessário durante a implementação e operação subsequente.

Os requisitos de sistema incluem:

* **Requisitos de software para clientes de armazenamento** -descreve as plataformas de Virtualização suportado, browsers da web, os iniciadores iSCSI, SMB clientes, requisitos de mínima do dispositivo virtual e quaisquer requisitos adicionais para nesses sistemas operativos.
* **Requisitos de rede para o dispositivo StorSimple** -fornece informações sobre as portas que têm de ser aberta na firewall para permitir tráfego de gestão, iSCSI ou em nuvem.

As informações de requisitos de sistema do StorSimple publicadas neste artigo aplica-se apenas às matrizes de Virtual StorSimple.

* Para dispositivos de 8000 série, aceda a [requisitos de sistema para o seu dispositivo de série 8000 do StorSimple](storsimple-system-requirements.md).
* Para dispositivos das 7000 séries, aceda a [requisitos de sistema para o dispositivo de série do StorSimple 5000 7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Requisitos de software
Os requisitos de software incluem informações sobre os browsers suportados, as versões da SMB, plataformas de Virtualização e os requisitos de mínima do dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de Virtualização suportado
| **Hipervisor** | **Versão** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 e posterior |
| VMware ESXi |5.0, 5.5 e 6.0 <br> (6.5 não é suportada.) |

> [!IMPORTANT]
> Não instale as ferramentas do VMware na sua matriz Virtual StorSimple; Este procedimento resultará numa configuração não suportada.

### <a name="virtual-device-requirements"></a>Requisitos do dispositivo virtual
| **Componente** | **Requisito** |
| --- | --- |
| Número mínimo de processadores virtuais (núcleos) |4 |
| Memória mínima de memória (RAM) |8 GB <br> Para um servidor de ficheiros, 8 GB para os ficheiros de menos de milhões de 2 e 16 GB para 2-4 milhões de ficheiros|
| Espaço em disco<sup>1</sup> |Disco do SO - 80 GB <br></br>Disco de dados - 500 GB para 8 TB |
| Número mínimo de interfaces de rede |1 |
| Largura de banda de Internet<sup>2</sup> |Largura de banda mínima necessária: 5 Mbps <br> Recomendado largura de banda: 100 Mbps <br> A velocidade de escalas de transferência de dados com a largura de banda de Internet. Por exemplo, 100 GB de dados demora 2 dias para transferir Mbps 5 levar a falhas de cópia de segurança porque as cópias de segurança diárias não seriam concluída num dia. Com uma largura de banda de 100 Mbps, 100 GB de dados pode ser transferido em 2,5 horas.   |

<sup>1</sup> - dinâmico aprovisionado

<sup>2</sup> -requisitos de rede podem variar consoante a taxa de alteração de dados diária. Por exemplo, se um dispositivo tem de criar cópias de segurança de 10 GB ou mais alterações durante um dia, em seguida, a cópia de segurança diária através de uma ligação de Mbps 5 pode demorar até 4.25 horas (se os dados não foi possível ser comprimidos ou eliminação de duplicados).

### <a name="supported-web-browsers"></a>Browsers suportados
| **Componente** | **Versão** | **Notas/requisitos adicionais** |
| --- | --- | --- |
| Microsoft Edge |versão mais recente | |
| Internet Explorer |versão mais recente |Testar com o Internet Explorer 11 |
| Google Chrome |versão mais recente |Testar com Chrome 46 |

### <a name="supported-storage-clients"></a>Clientes de armazenamento suportadas
Os seguintes requisitos de software são para os iniciadores iSCSI que acedem a matriz de Virtual StorSimple (configurado como um servidor de iSCSI).

| **Sistemas operativos suportados** | **Versão necessária** | **Notas/requisitos adicionais** |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012R2 |Criar StorSimple com aprovisionamento dinâmico e totalmente aprovisionado volumes. -Não é possível criar volumes parcialmente aprovisionados. Volumes do StorSimple iSCSI só são suportados para: <ul><li>Simples volumes em discos básicos do Windows.</li><li>Windows NTFS para formatar um volume.</li> |

Os seguintes requisitos de software são para os clientes do SMB que acedem a matriz de Virtual StorSimple (configurado como um servidor de ficheiros).

| **Versão do SMB** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Não copie ou armazenar os ficheiros protegidos pelo Windows sistema EFS (Encrypting File) para o servidor de ficheiros de matriz Virtual StorSimple; Este procedimento resultará numa configuração não suportada.


### <a name="supported-storage-format"></a>Suportado formato de armazenamento
Apenas o armazenamento de BLOBs de blocos do Azure é suportado. Os blobs de página não são suportados. Obter mais informações [sobre blobs de blocos e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Requisitos de rede
A tabela seguinte lista as portas que têm de ser aberta na firewall para permitir a iSCSI, SMB, nuvem ou tráfego de gestão. Nesta tabela, *no* ou *entrada* refere-se a direção pedidos de cliente recebidos aceder a partir do qual o dispositivo. *Saída* ou *saída* refere-se a direção na qual o dispositivo StorSimple envia dados externamente, para além da implementação: por exemplo, a saída à Internet.

| **Um número de porta<sup>1</sup>** | **Entrada ou saída** | **Âmbito de porta** | **Necessário** | **Notas** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Saída |WAN |Não |Porta de saída é utilizada para acesso à Internet para obter atualizações. <br></br>O proxy web de saída é configurável de utilizador. |
| TCP 443 (HTTPS) |Saída |WAN |Sim |Porta de saída é utilizada para aceder aos dados na nuvem. <br></br>O proxy web de saída é configurável de utilizador. |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor DNS baseado na Internet. <br></br> Tenha em atenção que, se implementar um servidor de ficheiros, é recomendável utilizar o servidor DNS local. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseado na Internet.<br></br> Tenha em atenção que se implementar um servidor de ficheiros, recomendamos que a sincronização de hora com os controladores de domínio do Active Directory. |
| TCP 80 (HTTP) |No |LAN |Sim |Esta é a porta de entrada para a IU do local no dispositivo StorSimple para gestão local. <br></br> Tenha em atenção que a IU do local a aceder através de HTTP será automaticamente redirecionado para HTTPS. |
| TCP 443 (HTTPS) |No |LAN |Sim |Esta é a porta de entrada para a IU do local no dispositivo StorSimple para gestão local. |
| 3260 TCP (iSCSI) |No |LAN |Não |Esta porta é utilizada para aceder aos dados através de iSCSI. |

<sup>1</sup> nenhuma porta de entrada tem de estar abertas na Internet pública.

> [!IMPORTANT]
> Certifique-se de que a firewall não modificar ou desencriptar o tráfego SSL entre o dispositivo StorSimple e o Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para as regras de firewall
Os administradores de rede, muitas vezes, podem configurar regras de firewall avançada com base nos padrões de URL para filtrar a entrada e o tráfego de saída. A matriz de virtual e o serviço do Gestor de dispositivos do StorSimple dependem outras aplicações da Microsoft, tais como o Service Bus do Azure, controlo de acesso do Azure Active Directory, as contas do storage e servidores do Microsoft Update. Os padrões de URL associados estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender que podem alterar os padrões de URL associados estas aplicações. Isto por sua vez, irá necessitar de administrador de rede para monitorizar e atualizar regras de firewall para o StorSimple como e quando necessário. 

Recomendamos que defina as regras de firewall para o tráfego de saída, com base em endereços IP, fixos liberally na maioria dos casos do StorSimple. No entanto, pode utilizar as informações abaixo para definir regras de firewall avançada que são necessárias para criar ambientes seguras.

> [!NOTE]
> 
> * O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede ativado para a nuvem. 
> * O destino IPs deve ser definido como [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Padrão de URL | Componente/funcionalidade |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Serviço do Gestor de dispositivos do StorSimple<br>Serviço de Controlo de Acesso<br>Service Bus do Azure<br>Serviço de autenticação|
| `http://*.backup.windowsazure.com` |Registo de dispositivo |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Revogação de certificados |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorização e de contas do storage do Azure |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN da Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de suporte |
| `http://*.data.microsoft.com ` |Serviço de telemetria no Windows, consulte o [atualização para a experiência do cliente e telemetria de diagnóstico](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Passos seguintes
* [Preparar o portal para implementar a matriz de Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
