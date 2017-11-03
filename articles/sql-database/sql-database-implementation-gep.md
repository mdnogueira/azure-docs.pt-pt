---
title: "Base de dados SQL do Azure do Azure caso prático - GEP | Microsoft Docs"
description: "Saiba mais sobre como GEP utiliza a base de dados SQL para aceder aos clientes mais globais e alcançarem maior eficiência de"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ae8bcb10-c251-4bac-b666-10a253918583
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: bced4e04f541dde58410e25fe0c3aa5493a5e5fd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure dá-alcance global GEP e eficiência
![Logótipo GEP](./media/sql-database-implementation-gep/geplogo.png)

GEP fornece o software e serviços que permitem líderes de aprovisionamento, todo o mundo maximizar o respetivo impacto nas operações dos respetivos empresas, estratégias e os desempenhos financeiros. Para além dos serviços de consultoria e geridos, a empresa oferece SMART por GEP®, uma plataforma de software de aprovisionamento baseado na nuvem, abrangente. No entanto, ocorreu GEP limitações tentar suporta soluções como SMART por GEP com a sua própria nas instalações dos centros de dados: investimentos necessários foram muito acentuados e requisitos de regulamentação noutros países seriam efetuou os necessários investimentos hercúleas ainda mais. Ao mover para a nuvem, GEP foi libertado dos recursos de TI, permitindo que se preocupe menor sobre as operações de TI e concentrar-se mais informações sobre desenvolver novas origens de valor para que os seus clientes em todo o mundo.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expandir os serviços e crescimento através da utilização do Azure
SMART pelos clientes GEP adoram funcionalidades e a facilidade de utilização; a plataforma os clientes podem gerir os seus processos de qualquer lugar, em qualquer altura e em qualquer dispositivo — telemóvel, tablet ou portátil. Ao mover para o Microsoft Azure, GEP foi capaz de suportar o crescimento rápido e o potencial de expandir para mercados de novo. De acordo com a VP de tecnologia do GEP Dhananjay Nagalkar, "Microsoft Azure tenha jogado uma função de chave no êxito da GEP ao permitir-na aumentar rapidamente com agilidade e fornecendo centros de dados regionais que ajudam-na satisfazer as necessidades de regulamentação dos nossos clientes global."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>As limitações de um datacenter do-it-yourself
Em 2013, líderes das GEP reconhecer que estes necessária uma forma de garantir o desempenho e escalabilidade como queria os respetivos clientes base. Nagalkar explicado, "para satisfazer esse pedido com os nossos centros de dados existentes, iremos teria expanda consideravelmente a nossa infraestrutura e os recursos de TI. O investimento e o período de tempo para que podem ter sido bastantes." No local máquinas virtuais e físicas requerem um vasto conjunto configuração, gestão, dimensionamento, cópia de segurança e uma taxa que poderia ter sido custos destas proibitivos para GEP a aplicação de patches. As soluções de nuvem, por outro lado, oferecem simplicidade e a conveniência que ativado GEP focar-se mais no desenvolvimento em vez de gerir grande — e a crescer — as operações de TI. Nagalkar deverem que GEP poderia reduzir a sobrecarga de compra, a configuração e gestão de infraestrutura ao migrar para a nuvem.

GEP necessárias também uma forma de ultrapassar regulamentação barreiras que as eficazes mantidos-lo fora de algumas mercados global. Para muitos clientes de alfabetos potenciais do GEP, a conformidade regulamentar seria necessários ter dados armazenados no seus locais regiões geográficas. Mas seria não ter sido prático para GEP criar os vários centros de dados. "Os investimentos em infraestruturas ampla e custos IT custos seriam colocar um impacto significativo para as margens,", de acordo com Nagalkar. "Como resultado, iremos foram realmente forçados a ativar ausente potenciais clientes que são necessários dados armazenados localmente."

Nagalkar deverem que uma solução de nuvem poderá estar a resposta para este dilema. Se GEP foi movido para um fornecedor de nuvem com uma presença global, foi cumpre melhor dos seus clientes regulamentares e tem de desempenho ao armazenar dados próximo em localizações físicas dos clientes.

## <a name="finding-smooth-skies-in-the-cloud"></a>Localizar skies uniforme na nuvem
Nagalkar e a equipa explorou várias opções de nuvem, mas a maioria foram infraestrutura-como-um-serviço (IaaS)-com base em soluções que necessitaria GEP de investir descontos elevados no recursos de TI para configurar e gerir o serviço. A solução (PaaS) de plataforma-como-um-serviço do Azure acabou de ser muito melhor ajustar.

"Com o Azure, GEP não tem de lidar com a gestão de base de dados, a configuração da máquina virtual, a aplicação de patches ou outras tarefas de gestão de infraestruturas" indicado Nagalkar. "Em vez disso, vamos pode concentrar-se a nossa recursos no qual podemos fazer de melhor: tirar partido dos nossos conhecimentos no aprovisionamento escrever software verdadeiramente fornece resultados para os nossos clientes." 

Na verdade, a mudança para o Azure ativou GEP reduzir a sua equipa de operações de TI durante a ativação da funcionalidade superior em simultâneo para os clientes.

Ao tirar partido dos centros de dados do Azure em todo o mundo, GEP pode expandir facilmente o alcance na Europa e Ásia. Os datacenters globais ativar GEP dimensionem com agilidade e para satisfazer as necessidades do cliente para dados armazenados localmente que reduz a latência e cumpre os requisitos de regulamentação.

## <a name="smart-by-gep-architecture"></a>INTELIGENTE pela arquitetura de GEP
GEP construídos SMART por GEP a partir do zero no Azure. Uma motivação crítico para GEP era maior escalabilidade, menos o período de inatividade, e os custos de manutenção reduzida GEP foi experiência com a base de dados do SQL do Azure em comparação com a qual GEP podem alcançar no local. No entanto, depois, movida para a nuvem, GEP detetados novas oportunidades de desenvolvimento na nuvem, como fazer o protótipo rápido e de engenharia lean melhor responder às necessidades do cliente. Desenvolver no Azure permite GEP fazer ausente com o headaches que os programadores foi depare no local de licenciamento de software. O núcleo dos INTELIGENTE por GEP é base de dados SQL do Azure, embora GEP utiliza muitos outros serviços do Azure rapidamente e facilmente continuar a melhorar SMART por GEP.

![INTELIGENTE pela arquitetura de GEP](./media/sql-database-implementation-gep/figure1.png) figura 1. INTELIGENTE pela arquitetura de GEP

## <a name="structured-data"></a>Dados estruturados
No Centro de SMART por aplicação GEP são, por exemplo, essa solução de energia a gestão de aprovisionamento empresarial as instâncias de SQL Database do Azure. Quando GEP foi desenvolvido SMART por GEP, vimos SQL Database do Azure como uma opção perfeita para a respetiva arquitetura, aquele que pretende ativar a empresa para atingir o nível mais elevado de proteção de dados e para cumprir os seus requisitos de regulamentação. GEP torna a utilização de várias camadas de proteção de dados que ofertas de base de dados SQL do Azure, incluindo:

* Encriptar dados Inativos através da encriptação transparente de dados.
* Proteger a autenticação através da integração SQL Database do Azure com o Azure Active Directory.
* Limitar o acesso a um subconjunto de dados com segurança ao nível da linha adequado.
* Máscara de dados em tempo real através de políticas.
* Registo de eventos de base de dados através de auditoria de base de dados SQL do Azure.

> "Podemos utilizar todas estas opções sem quaisquer alterações de código principal e com um impacto mínimo no desempenho," consiga aceder tal Nagalkar.
> 
> 

Ao utilizar a SQL Database do Azure, GEP automaticamente tem capacidades de recuperação após desastre maiores do que foi tem forma económica projetada no local devido às funcionalidades de tolerância a falhas incorporadas no SQL Database do Azure. GEP utiliza a capacidade de replicação geográfica activa na SQL Database do Azure, conjugada com vários online, ativas e legíveis réplicas secundárias (grupos de disponibilidade Always) em diferentes regiões geográficas para formar pares de elevada disponibilidade. Replicar SMART por GEP dados em regiões significa que, no caso de desastre ao nível da região, GEP pode recuperar facilmente dados de cliente com um objetivo de ponto mínima de recuperação (RPO) e o objetivo de tempo de recuperação (RTO).

Cada SMART pelo cliente GEP tem duas instâncias de SQL Database do Azure: um para o processamento de transações online (OLTP) e outra para o Analysis Services (por exemplo, o cliente passam e relatório de análise). Conjuntos elásticos de base de dados SQL do Azure permitem GEP gerir facilmente milhares de bases de dados globalmente para processar exigências de recursos de base de dados imprevisível. Conjuntos elásticos fornecem um meio para GEP para se certificar de que as bases de dados do cliente podem Dimensionar conforme necessários, sem sobre-aprovisionar ou em aprovisionamento, permitindo também GEP para controlar os custos. Além disso, porque se trata de um serviço de PaaS, GEP obtém todas as funcionalidades de base de dados do Azure SQL novo com as atualizações automáticas.

## <a name="unstructured-and-semi-structured-data"></a>Dados não estruturados e semiestruturados
No entanto, algumas SMART por dados de cliente GEP necessita de armazenamento sem rigidly structured. Para este tipo de dados, GEP emprega o armazenamento de Blobs do Azure, Table Storage do Azure e a Cache de Redis do Azure. Armazenamento de Blobs do Azure aloja os anexos que SMART ao carregamento de utilizadores GEP na aplicação. É também onde INTELIGENTE por GEP arquivos de conteúdo estático, como folhas de estilo em cascata (CSS) e ficheiros de JavaScript.

GEP armazena os dados de não-cliente-com acesso à, como SMART por dados de registo GEP no Table Storage do Azure, que lhe dá GEP eficazmente ilimitado armazenamento económico e tempos de obtenção rápido sem ter de se preocupar com a configuração de um esquema para os dados. GEP utiliza a Cache de Redis do Azure para uma cache principal.

## <a name="authentication-and-routing"></a>Autenticação e o encaminhamento
Serviço de controlo de acesso do Azure (ACS) fornece SMART por GEP os utilizadores com uma ampla variedade de opções para a assinatura para o software. ACS do Azure podem federar com qualquer fornecedor de identidade trocas de dados de autenticação através de Security Assertion Markup Language (SAML), tais como serviços de domínio do Active Directory, a identidade de Ping, OneLogin ou SiteMinder. Isto ajuda a GEP implementar início de sessão único (SSO) para os clientes sem se preocupar armazenar credenciais de utilizador e manter políticas de palavra-passe de cliente.

Depois de iniciar sessão, os clientes têm acesso aos recursos empresariais correto no SMART por GEP. GEP utiliza o Gestor de tráfego do Azure para redirecionar e pedidos de balanceamento de carga provenientes de dispositivos móveis dos clientes e sessões do browser.

## <a name="other-azure-services"></a>Outros serviços do Azure
GEP emprega um número de outros serviços do Azure para tornar SMART por GEP responder ao cliente precisa. GEP utiliza cloud services do Azure (funções web e de trabalho) para o anfitrião de aplicações-apresentação e os serviços de lógica de negócio seguros. Serviços cloud permitem aos programadores para gerir a infraestrutura como código (IAC) e para implementar o novo SMART por aplicações GEP numa fração do tempo que é necessária com centros de dados no local — tudo sem qualquer intervenção do IT. Os programadores GEP podem utilizar os serviços em nuvem ambiente para testar novas versões sem afetar a implementação atual de produção de teste. Depois de testar, GEP utiliza as funcionalidades de alternância de VIP do cloud services do Azure para mover o código de teste para a ranhura de produção dentro de um minuto, reduzindo assim o período de indisponibilidade de implementação.

Para reduzir a latência de aplicação, GEP utiliza a rede de entrega de conteúdos (CDN) do Azure para colocar conteúdo estático armazenado no Blob storage do Azure (por exemplo, ficheiros CSS e JavaScript) nos servidores edge próximo de utilizadores. Utiliza GEP Service Bus do Azure para suportar os padrões de arquitetura da aplicação de publicação-subscrição para parcialmente comando consulta reativo segregação (CQRS) e em camadas arquitetura com coupling soltas e comunicação assíncrona. GEP utiliza Media Services do Azure para melhorar o serviço de suporte ao cliente. GEP encontrado que-lo facilmente foi publicar vídeos do suporte de utilizador nos serviços de suporte de dados do Azure. Estes vídeos responder a perguntas comuns do utilizador, que ajuda a melhorar SMART satisfação do utilizador GEP ao colocar alguns da carga de suporte retire pessoal de suporte ao cliente da GEP.

Para enviar e-mails transacionais gerados diariamente por SMART por GEP milhares, o firm utiliza a API .NET do SendGrid para integrar com o Azure. Para programadores GEP, isto é fácil fazer — o suplemento do SendGrid para o Azure está disponível diretamente no Azure Marketplace. Os programadores GEP conseguiu configurar SMART por GEP utilizando o direito de pacote NuGet do SendGrid no Microsoft Visual Studio; GEP TI pode monitorizar o tráfego de e-mail do SendGrid o software diretamente a partir do Azure.

Por fim, SMART por GEP utiliza máquinas virtuais do Azure — o serviço IaaS do Azure — para serviços e aplicações de anfitrião que não foi possível fazer sentido, no momento da engenharia, para substituir com soluções de PaaS ou de software-como-um-serviço (SaaS). Por exemplo, GEP aloja serviços da API integração nas máquinas virtuais para a integração do empresa-empresa (B2B) com sistemas no local planeamento de recursos de empresa (ERP clientes) como SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP e Lawson e com soluções de SaaS de cliente para trocar eficientemente documentos de aprovisionamento, tais como faturas.

> "Edifício INTELIGENTE por GEP na nuvem do Microsoft Azure completamente removeu a necessidade para on-premises departamento de TI não só para GEP, mas também para operações de aprovisionamento dos nossos clientes." 
> 
> — Dhananjay Nagalkar, VP das soluções de tecnologia
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Expanda a satisfação do cliente sem expandir IT
Desde migrar dos centros de dados no local para o Azure e criar SMART por GEP a partir do zero na plataforma do Azure, GEP aumentou a flexibilidade e escalabilidade sem ter de expandir a sua infraestrutura ou de uma equipa de TI. Na verdade, a empresa ainda não adicionou recursos de TI no mais de quatro anos. O modelo PaaS conveniente do Azure ativou GEP reduzir os gastos com gestão de suporte e operações de fornecedor. Como resultado, GEP foi recursos foco capaz de desenvolvimento de software; e desenvolver na nuvem permite aos programadores GEP testar rapidamente a novas ideias sem ter gaste tempo em coordenação com IT ou preocupar no local os requisitos de licenciamento. Base de dados SQL do Azure ajuda-o GEP melhor Certifique-se de que os seus clientes tenham sempre excecional serviço e o desempenho.

## <a name="more-information"></a>Mais informações
* Home page do GEP: [GEP](http://www.gep.com)
* Inteligente por GEP: [inteligente por GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>Contribuintes do GEP
* Huzaifa Matawala, associar Director — arquiteto de sistemas, GEP
* GEP Sathyan Narasingh, Gestor de engenharia,
* GEP Deepa Velukutty, Arquiteto de base de dados

