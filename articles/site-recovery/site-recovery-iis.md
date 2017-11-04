---
title: "Aplicação web utilizando o Azure Site Recovery com base de replicar IIS um multicamadas | Microsoft Docs"
description: "Este artigo descreve como replicar máquinas virtuais IIS web farm utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 4ac79df703de00ac009d9845772d8be740e74f29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Replicar uma aplicação web do IIS com base de várias camadas utilizando o Azure Site Recovery

## <a name="overview"></a>Descrição geral


Software de aplicação é o motor de produtividade da empresa numa organização. Várias aplicações web podem servir diferentes fins numa organização. Alguns deles como o processamento de folha de pagamentos, aplicações financeiras e cliente Web sites podem ser utmost críticos de uma organização. Será importante para a organização tenha-los a cópia de segurança e em execução em todas as vezes para evitar a perda de produtividade e mais importante ainda impedir que os danos à imagem de marca da organização.

Aplicações web críticas são normalmente configuradas como aplicações de várias camadas com o web, base de dados e aplicações em diferentes camadas. Para além dos que está a ser distribuídos por várias camadas, as aplicações podem também utilizar vários servidores em cada camada para equilibrar o tráfego. Além disso, os mapeamentos entre várias camadas e no servidor web podem ser baseados em endereços IP estáticos. Na ativação pós-falha, alguns destes mapeamentos terá de ser atualizado, especialmente, se tiver vários sites configurados no servidor web. Em caso de aplicações web através de SSL, enlaces de certificado terá de ser atualizados.

Métodos de recuperação com base de replicação não tradicional envolvem a cópia de segurança de vários ficheiros de configuração, definições de registo, enlaces, componentes personalizados (COM ou .NET), conteúdo e também certificados e recuperação de ficheiros através de um conjunto de passos manuais. Estes técnicas são claramente complexos, erro suscetível e não dimensionável. É, por exemplo, facilmente possível se esquecer da cópia de segurança de certificados e ficar com a opção de não mas comprar novos certificados para o servidor após a ativação pós-falha.

Uma solução de recuperação de desastre boa, deve permitir a modelação de planos de recuperação em torno acima arquiteturas de aplicações complexas e também de ter a capacidade de adicionar passos personalizados para processar os mapeamentos de aplicação entre várias camadas, por conseguinte, certifique-se a fornecer um único clique captura a solução em caso de desastre, originando um RTO inferior.


Este artigo descreve como proteger uma através de aplicações web do IIS com base um [do Azure Site Recovery](site-recovery-overview.md). Este artigo abordará as melhores práticas para replicar uma camada de três IIS com base da aplicação web do Azure, como pode fazer um exercício de recuperação após desastre e como pode de ativação pós-falha, a aplicação no Azure.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende o seguinte:

1. [Uma máquina virtual a replicar para o Azure](site-recovery-vmware-to-azure.md)
1. Como [estruturar uma rede de recuperação](site-recovery-network-design.md)
1. [Efetuar uma ativação pós-falha de teste para o Azure](./site-recovery-test-failover-to-azure.md)
1. [Efetuar uma ativação pós-falha para o Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como [replicar do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implementação
Uma aplicação web IIS com base normalmente segue-se um dos padrões de implementação seguintes:

* * O padrão de implementação 1 * * com base do IIS uma web farm com Routing(ARR) de pedido de aplicação, o servidor de IIS e o Microsoft SQL Server.

![Implementação padrão](./media/site-recovery-iis/deployment-pattern1.png)

**O padrão de implementação 2** com base do IIS uma web farm com Routing(ARR) de pedido de aplicação, o servidor de IIS, o servidor de aplicações e o Microsoft SQL Server.


![Implementação padrão](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte de recuperação de site

Para fins de criação de máquinas virtuais VMware neste artigo com o servidor de IIS versão 7.5 no Windows Server 2012 R2 Enterprise foram utilizados. Como a replicação de recuperação de site desconhece de aplicação, as recomendações fornecidas aqui são esperadas para reter nos seguintes cenários bem e para uma versão diferente do IIS.

### <a name="source-and-target"></a>A origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Não | Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Siga [esta orientação](site-recovery-vmware-to-azure.md) iniciar a replicação de todas as IIS web farm máquinas virtuais no Azure.

Se estiver a utilizar um IP estático em seguida, especifique o IP que pretende que a máquina virtual para efetuar para a [ **IP de destino** ](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) definição nas definições de rede e computação.

![IP de destino](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação permite a ativação pós-falha de várias camadas numa aplicação de várias camada, por conseguinte, manter a consistência da aplicação da sequência. Siga o procedimento abaixo ao criar um plano de recuperação para uma aplicação web de várias camadas.  [Saiba mais sobre como criar um plano de recuperação](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha
Uma aplicação web do IIS de várias camada típica será consistem de uma camada de base de dados com máquinas virtuais do SQL Server, a camada web constituted por um servidor IIS e uma camada de aplicação. Adicione todas estas máquinas virtuais para outro grupo com base na camada como abaixo. [Saiba mais sobre customising plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Crie um plano de recuperação. Adicione as máquinas de virtuais de camada de base de dados em grupo 1 para garantir que são encerramento pela última vez e produzidos primeiro.

1. Adicione as máquinas de virtuais de camada de aplicação de grupo 2, de modo a que estes são produzidos depois da camada de base de dados foram produzida.

1. Adicione as máquinas de virtuais de camada web no grupo 3, de modo a que estes são produzidos depois da camada de aplicação foram produzida.

1. Adicione máquinas de virtuais de balanceamento de carga na 4 do grupo de forma a que estes são produzidos depois da camada web foram produzida.


### <a name="adding-scripts-to-the-recovery-plan"></a>A adição de scripts para o plano de recuperação
Terá de fazer algumas operações em máquinas virtuais do Azure após ativação pós-falha/ativação pós-falha de teste para efetuar corretamente a função do IIS web farm. Pode automatizar a operação de ativação pós-falha do post, como atualizar a entrada DNS, alterar o enlace de site, alterar na cadeia de ligação, adicionando scripts correspondentes no plano de recuperação, tal como indicado abaixo. [Saiba mais sobre como adicionar o plano de recuperação de script](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>Atualização de DNS
Se o DNS está configurado para a atualização dinâmica de DNS, em seguida, as máquinas virtuais, normalmente, atualize o DNS com as novas IP assim que for iniciado. Se pretender adicionar um passo explícito para atualizar o DNS com os IPs novo das máquinas virtuais, em seguida, adicionar este [script para atualizar o IP no DNS](https://aka.ms/asr-dns-update) como uma ação de post em grupos de plano de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de ligação em Web. config de uma aplicação
A cadeia de ligação Especifica a base de dados que o web site comunica com.

Se a cadeia de ligação acarreta o nome da máquina virtual da base de dados, não existem passos adicionais ativação pós-falha de post necessários e a aplicação será capaz de comunicar automaticamente para a base de dados. Além disso, se o endereço IP para a máquina virtual de base de dados é mantido, que será não ser necessária para atualizar a cadeia de ligação. Se a cadeia de ligação refere-se para a máquina virtual de base de dados utilizando um endereço IP, terá de ser de ativação pós-falha de post atualizado. Por exemplo, o abaixo pontos de cadeia de ligação para a base de dados com o IP 127.0.1.2

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Pode atualizar a cadeia de ligação na camada web adicionando [script de atualização de ligação de IIS](https://aka.ms/asr-update-webtier-script-classic) após grupo 3 no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Enlaces de site para a aplicação
Cada site é composta por enlace informações que inclui o tipo de enlace, o endereço IP no qual o servidor IIS escuta os pedidos para o site, o número de porta e os nomes de anfitrião para o site. Durante uma ativação pós-falha, destes enlaces poderão ter de ser atualizada se existir uma alteração de endereço IP associados aos mesmos.

> [!NOTE]
>
> Se marcou 'todos os não atribuídos' para o enlace de site como no exemplo abaixo, não terá de atualizar esta ativação pós-falha post de enlace. Além disso, se o endereço IP associado um site não é alterado post ativação pós-falha, o enlace de site tem de não estar atualizado (depende da retenção do endereço IP a arquitetura de rede e sub-redes atribuídas aos sites primário e de recuperação e, por conseguinte, podem ou não podem ser exequível para a sua organização.)

![Enlace SSL](./media/site-recovery-iis/sslbinding.png)

Se tiver associado o endereço IP um site, terá de atualizar todos os enlaces de site com o novo endereço IP. Pode adicionar [script de atualização de escalão de IIS Web](https://aka.ms/asr-web-tier-update-runbook-classic) após grupo 3 no plano de recuperação para alterar os enlaces de site.


#### <a name="update-load-balancer-ip-address"></a>Atualizar o endereço IP do Balanceador de carga
Se tiver máquinas de virtuais de encaminhamento de pedidos de aplicação, adicionar [script de ativação pós-falha do IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) após 4 de grupo para atualizar o endereço IP.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>O enlace de certificado SSL para uma ligação https
Web sites podem ter um certificado SSL associado que ajuda a garantir uma comunicação segura entre o servidor Web e o browser do utilizador. Se o Web site tiver uma ligação https e um enlace de site para o endereço IP do servidor de IIS https associado com um enlace de certificado SSL, um novo enlace de site terá de ser adicionada para o certificado com o IP da máquina virtual IIS após ativação pós-falha.

O certificado SSL pode ser emitido contra-

a) o nome de domínio completamente qualificado do Web site<br>
b) o nome do servidor<br>
c) um certificado de caráter universal para o nome de domínio<br>
d) um endereço IP – se o certificado SSL é emitido contra o IP do servidor IIS de outro certificado SSL tem de ser emitido contra o endereço IP do servidor do IIS no site do Azure e um enlace SSL adicional para este certificado terá de ser criado. Por conseguinte, é recomendado para não utilizar um certificado SSL emitido relativamente IP. Esta é uma opção menos amplamente utilizada e logo que vai ser preterida de acordo com novas alterações de fórum de AC/browser.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Atualizar a dependência entre web e a camada de aplicação
Se tiver uma dependência específico de aplicação com base no endereço IP de máquinas virtuais, terá de atualizar esta ativação pós-falha post de dependência.

## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste
Siga [esta orientação](site-recovery-test-failover-to-azure.md) para efetuar uma ativação pós-falha de teste.

1.  Aceda ao portal do Azure e selecione o Cofre de serviço de recuperação.
1.  Clique no plano de recuperação criado para a web farm do IIS.
1.  Clique em "Ativação pós-falha de teste".
1.  Selecione o ponto de recuperação e de rede virtual do Azure para iniciar o processo de ativação pós-falha de teste.
1.  Assim que o ambiente secundário está ativo, pode realizar as validações.
1.  Depois de validações estiverem concluídas, pode selecionar 'Validações Concluir' e o ambiente de ativação pós-falha de teste será limpa.

## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha
Siga [esta orientação](site-recovery-failover.md) quando estão a fazer uma ativação pós-falha.

1.  Aceda ao portal do Azure e selecione o Cofre de serviço de recuperação.
1.  Clique no plano de recuperação criado para a web farm do IIS.
1.  Clique em 'Failover'.
1.  Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre [replicar outras aplicações](site-recovery-workload.md) utilizando a recuperação de sites.
