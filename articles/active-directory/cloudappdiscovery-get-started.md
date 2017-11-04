---
title: "Configurar o serviço de Cloud App Discovery no Azure Active Directory | Microsoft Docs"
description: "Localizar e gerir aplicações com o Cloud App Discovery para fornecer informações acionáveis na utilização de nuvem e de sombra IT."
services: active-directory
keywords: "o cloud app discovery, gestão de aplicações"
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: c09410bc6ed8b4eac4316e206c75db84ef0b07fb
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Configurar a Cloud App Discovery no Azure AD

O cloud App Discovery no Azure AD é agora com base na integração com dados disponíveis a partir do Microsoft Cloud App Security. Para fornecer informações em curso na utilização de nuvem e de sombra IT, o Cloud App Discovery compara os registos de tráfego para o catálogo de Cloud App Security mais de 15 000 aplicações em nuvem. Este artigo descreve o processo de configuração e contém ligações para as informações detalhadas de cada passo. Também descreve as informações de proxy e firewall e registo de ficheiros de suporte.

## <a name="prerequisites"></a>Pré-requisitos

* A organização tem de ter uma licença do Azure AD Premium P1 para utilizar o produto. Para obter mais informações, consulte [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Para configurar a Cloud App Discovery, tem de ser um Administrador Global ou de um leitor de segurança no Azure Active Directory.

## <a name="setup-steps"></a>Passos de configuração

1. [Configurar relatórios de instantâneo](cloudappdiscovery-set-up-snapshots.md) para verificar o formato do registo Certifique-se os seus registos fornecem informações utilizáveis ao Cloud App Discovery. Pode também fornecem ad-hoc visibilidade sobre os registos de tráfego que carregar manualmente de firewalls e servidores proxy.

2. [Configurar relatórios contínua](https://docs.microsoft.com/cloud-app-security/discovery-docker) para analisar todos os registos que são reencaminhados a partir da rede utilizando o recoletor de registos da Cloud App Security. Pode utilizá-los para identificar tendências de utilização e de novas aplicações.

3. Se os registos não são atualmente suportados, [configurar um analisador de registo personalizado](https://docs.microsoft.com/en-us/cloud-app-security/custom-log-parser) para que o Cloud App Discovery poder analisá-las.
  
## <a name="log-processing-flow"></a>Registo de processamento de fluxo

Pode demorar em qualquer local de alguns minutos a várias horas para gerar relatórios dependendo da quantidade de dados. Eis o que é analisado:

* **Carregar** registos de tráfego da Web da sua rede são carregados para o portal.
* **Analisar** Cloud App Security analisa e extrai dados de tráfego de registos de tráfego com um analisador dedicado para cada origem de dados.
* **Analisar** dados de tráfego são analisados face o catálogo de Cloud App Security para identificar mais de 15 000 aplicações na nuvem. Utilizadores ativos e endereços IP também são identificados como parte da análise.
* **Gerar o relatório** Cloud App Security gera um relatório dos dados extraídos a partir dos ficheiros de registo e torna a mesma disponível para a Cloud App Discovery.

> [!NOTE]
> * Dados de relatório contínua são analisados duas vezes por dia.
> * O recoletor de registos comprime os dados antes de ser carregado. O tráfego de saída no recoletor de registos é cerca de 10% do tamanho de registos de tráfego recebido.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Utilizar registos de tráfego para a Cloud App Discovery

O cloud App Discovery utiliza os dados nos seus registos de tráfego. Mais detalhadamente pode adicionar no seu registo, a melhor visibilidade a obter. O cloud App Discovery requer que dados de tráfego da web com os seguintes atributos:

* Data da transação
* Endereço IP de origem
* Utilizador de origem **recomendado**
* Endereço IP de destino
* URL de destino **recomendado** (URLs fornecem mais exatidão para deteção da aplicação na nuvem que endereços IP)
* Quantidade total de dados
* Quantidade de carregado ou transferidos dados, para informações sobre os padrões de utilização da aplicação na nuvem
* Ação tomada (permitidos/bloqueados)

O cloud App Discovery não é possível mostrar ou analisar os atributos que não estejam incluídos nos seus registos. Por exemplo, **Firewall ASA da Cisco** formato padrão de registo não contém o **quantidade de bytes carregados por transação**, **Username**, ou **URL de destino**  , mas apenas o endereço IP de destino. Assim, poderá ter menos visibilidade para as aplicações em nuvem desta origem de dados. Para firewalls de Cisco ASA, defina o nível de informações para 6.1.

Para gerar com êxito um relatório de Cloud App Discovery, os registos de tráfego tem de satisfazer as seguintes condições:

1.  Origem de dados é [um servidor de firewall ou proxy suportado](#supported-firewalls-and-proxies).
2.  Formato de registo corresponde ao formato padrão esperado. Isto é verificado ao tempo de carregamento. Para otimizar o formato de registo uour, consulte [criar relatórios de Cloud App Discovery do instantâneo](cloudappdiscovery-set-up-snapshots.md).
3.  Os eventos não são mais de 90 dias.
4.  O ficheiro de registo é válido e inclui informações de tráfego de saída.

## <a name="supported-firewalls-and-proxy-servers"></a>Suportado firewalls e servidores proxy

* Barracuda - Firewall de aplicação Web (W3C)
* Blue Coat Proxy SG - registo de acesso (W3C)
* Check Point
* Firewall ASA da Cisco (Cisco ASA firewalls, definir o nível de informações para 6)
* IronPort WSA da Cisco
* Scansafe da Cisco
* Cisco Meraki – registo de URLs
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortigate da Fortinet
* SRX da Juniper
* Juniper SSG
* McAfee Web seguro Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Série da Palo Alto Firewall
* SG da Sophos
* Sophos Cyberoam
* SQUID (comum)
* SQUID (nativo)
* Websense - Web Security Solutions - relatório de investigação detalhado (CSV)
* Websense - Web Security Solutions - registo de atividade da Internet (CEF)
* Zscaler

> [!NOTE]
> O cloud App Discovery suporta endereços IPv4 e IPv6.

Se o início de sessão não é suportado, selecione **outros** como o **origem de dados** e especifique o dispositivo e o registo está a tentar carregar. O início de sessão é revisto pela equipa de analista de nuvem do Cloud App Security. Quando suporte para o tipo de registo é adicionado notificamo-lo, mas em vez disso, pode definir um analisador personalizado que corresponde ao formato de registo. Para obter mais informações, consulte [utilizar um analisador de registo personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Atributos de dados (em conformidade com a documentação do fornecedor)

| Origem de dados         | URL da aplicação de destino | Endereço de IP de aplicações visado | Nome de utilizador | Origem do endereço IP | Total de tráfego | Bytes carregados |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Sim**        | **Sim**       | **Sim**  | **Sim**   | Não            | Não             |
| Blue Coat                               | **Sim**        | Não            | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Ponto de verificação                              | Não             | **Sim**       | Não       | **Sim**   | Não            | Não             |
| Cisco ASA                               | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | Não             |
| Cisco FWSM                              | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | Não             |
| Ironport WSA da Cisco                      | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Cisco Meraki                            | **Sim**        | **Sim**       | Não       | **Sim**   | Não            | Não             |
| Clavister NGFW (Syslog)                 | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Dell SonicWall                          | **Sim**        | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| Fortigate                               | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| SRX da Juniper                             | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| Juniper SSG                             | Não             | **Sim**       | Não       | **Sim**   | **Sim**       | **Sim**        |
| McAfee SWG                              | **Sim**        | Não            | Não       | **Sim**   | **Sim**       | **Sim**        |
| MS TMG                                  | **Sim**        | Não            | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Palo Alto Networks                      | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Sophos                                  | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | Não             |
| SQUID (comum)                          | **Sim**        | Não            | **Sim**  | **Sim**   | Não            | **Sim**        |
| SQUID (nativo)                          | **Sim**        | Não            | **Sim**  | **Sim**   | Não            | **Sim**        |
| Websense - relatório de investigação (CSV)   | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Websense - registo de atividade da Internet (CEF)  | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |
| Zscaler                                 | **Sim**        | **Sim**       | **Sim**  | **Sim**   | **Sim**       | **Sim**        |


## <a name="next-steps"></a>Passos seguintes
Utilize as hiperligações seguintes para continuar a configurar a Cloud App Discovery no Azure AD.

* [Criar relatórios de instantâneos](cloudappdiscovery-set-up-snapshots.md)
* [Configurar os relatórios contínuos](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Utilizar um analisador de registos personalizados](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)