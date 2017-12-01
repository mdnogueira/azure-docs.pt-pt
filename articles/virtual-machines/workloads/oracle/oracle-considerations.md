---
title: "Soluções de Oracle no Microsoft Azure | Microsoft Docs"
description: "Saiba mais sobre as configurações suportadas e limitações das soluções de Oracle no Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: 1bc03d15096e7f1d4538d6642a61aaee9bb572f7
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Soluções de Oracle e a respetiva implementação no Microsoft Azure
Este artigo aborda as informações necessárias para implementar com sucesso várias soluções de Oracle no Microsoft Azure. Estas soluções são baseadas em imagens da Máquina Virtual publicadas pela Oracle no Azure Marketplace. Para obter uma lista de imagens atualmente disponíveis, execute o seguinte comando:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
A partir de 6-16-2017 a lista de imagens são os seguintes:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Estas imagens são consideradas "Traga a sua própria licença" e como tal será apenas cobrada para computação, armazenamento e os custos de funcionamento em rede tarifas executando uma VM.  Presume-se que estão devidamente licenciados para utilizar o Oracle software e de que tem um contrato de suporte atual no local com o Oracle. Oracle tem garantida mobilidade de licenças no local para o Azure. Consulte o artigo publicado [Oracle e Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) nota para obter detalhes sobre mobilidade de licenças. 

Indivíduos também podem optar por base as soluções numa imagem personalizada que criar a partir do zero no Azure ou carregar uma imagem personalizada do respetivo ambiente no local no.

## <a name="support-for-jd-edwards"></a>Suporte para JD Edwards
De acordo com nota de suporte de Oracle [2178595.1 de ID do documento](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , JD Edwards EnterpriseOne versões 9.2 e superior, são suportadas em **qualquer público na nuvem oferta** que satisfaz as respetivas específicos `Minimum Technical Requirements` (MTR).  Terá de criar imagens personalizadas que cumprem as respetivas especificações MTR para compatibilidade de aplicações do SO e software. 

## <a name="oracle-database-virtual-machine-images"></a>Imagens de máquina virtual de base de dados Oracle
Oracle suporta edições Oracle DB 12.1 Standard e Enterprise em execução no Azure em imagens da máquina virtual com base no Oracle Linux.  Para obter o melhor desempenho para cargas de trabalho de produção de Oracle DB no Azure, lembre-se de que corretamente o tamanho da imagem VM e utilizar discos geridos que sejam copiados pelo armazenamento Premium. Para obter instruções sobre como obter rapidamente uma base de dados Oracle e em execução no Azure com o Oracle publicado a imagem de VM, [tente as instruções do guia de introdução do Oracle DB](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco ligado

Discos ligados baseiam-se no serviço de armazenamento de Blobs do Azure. Cada disco padrão é capaz de um máximo de teórico de aproximadamente 500 operações de entrada/saída por segundo (IOPS). A nossa oferta do disco premium preferido para cargas de trabalho de base de dados de elevado desempenho e pode conseguir até 5000 IOps por disco. Enquanto pode utilizar um único disco se de que satisfaz as necessidades de desempenho, pode melhorar o desempenho de IOPS Efetivo se utilizar vários discos ligados, distribuídos a base de dados por-los e, em seguida, utilize a gestão de armazenamento automática da Oracle (ASM). Consulte [descrição geral de armazenamento automática Oracle](http://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas de ASM do Oracle. Para obter um exemplo de como instalar e configurar Oracle ASM numa VM do Azure Linux - pode experimentar o [instalar e configurar Oracle automatizada gestão de armazenamento](configure-oracle-asm.md) tutorial.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real da aplicação Cluster (Oracle RAC)
Oracle RAC foi concebido para mitigar a falha de um único nó uma configuração de cluster de vários nós no local. Baseia-se em duas tecnologias no local que não são nativas para ambientes de nuvem pública de hiper escala: multicast rede e de disco partilhado. Se a sua solução de base de dados requer RAC Oracle no Azure, terá de 3rd software de terceiros para permitir estas tecnologias.  A **certificadas do Microsoft Azure** oferta chamada [FlashGrid nós para Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) está disponível no Azure Marketplace, publicados pelo FlashGrid Inc. Para obter mais informações sobre esta solução e como funciona no Azure, consulte o [página de solução FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações de recuperação após desastre e disponibilidade elevada
Quando utilizar bases de dados Oracle no Azure, é responsável por implementar uma solução de recuperação de disponibilidade e após desastre elevada para evitar qualquer período de inatividade. 

Elevada disponibilidade e recuperação após desastre para Oracle da base de dados Enterprise Edition (sem depender Oracle RAC) pode ser conseguida utilizando Azure [Data Guard, Active Directory Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), ou [Oracle Golden porta](http://www.oracle.com/technetwork/middleware/goldengate), com duas bases de dados em duas máquinas virtuais separadas. Ambas as máquinas virtuais deve estar no mesmo [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que estes possam aceder aos entre si através do endereço IP privado persistente.  Além disso, recomendamos que colocar as máquinas virtuais no mesmo conjunto para permitir que o Azure para colocá-los em domínios de falhas separada e domínios de atualização de disponibilidade.  Se pretender que tenham georredundância - podem ter estas duas bases de dados são replicados entre duas regiões diferentes e ligar duas instâncias com um Gateway de VPN.

Temos um tutorial "[DataGuard Oracle de implementar no Azure](configure-oracle-dataguard.md)", que explica o procedimento de configuração básica para a versão de avaliação isto no Azure.  

Com o Oracle Data Guard, elevada disponibilidade pode ser conseguida com uma base de dados primária em máquinas virtuais, uma base de dados (modo de espera) secundário na outra máquina virtual e replicação unidirecional configurar entre eles. O resultado é acesso de leitura para a cópia da base de dados. Com o Oracle GoldenGate, pode configurar a replicação bidirecional entre as duas bases de dados. Para saber como configurar uma solução de elevada disponibilidade para as bases de dados a utilizar estas ferramentas, consulte [Active Directory Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) documentação no Web site da Oracle. Se o precisa de leitura e escrita acesso para a cópia da base de dados, pode utilizar [Active Directory Oracle Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Temos um tutorial "[GoldenGate Oracle de implementar no Azure](configure-oracle-golden-gate.md)", que explica o procedimento de configuração básica para a versão de avaliação isto no Azure.

Apesar de ter uma solução HA e DR criada no Azure, pretender certificar-se de que tem uma estratégia de cópia de segurança para restaurar a base de dados.  Temos um tutorial [cópia de segurança e recuperar uma base de dados Oracle](oracle-backup-recovery.md) que orienta-o procedimento básico para estabelecer uma cópia de segurança consistente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens da máquina virtual Oracle WebLogic Server
* **Clustering só é suportado na edição Enterprise.** Está licenciado para utilizar o clustering de WebLogic apenas quando utilizar a Enterprise Edition do WebLogic Server. Não utilize o clustering com WebLogic Server Standard Edition.
* **Não é suportado multicast do UDP.** Azure suporta UDP unicasting, mas não utilizar multicast ou difusão. Servidor de WebLogic é capaz de baseiam-se nas capacidades do Azure UDP unicast. Para melhor resultados depender UDP unicast, recomendamos que o tamanho do cluster WebLogic mantidos estático, ou ser mantidas com mais do que 10 servidores geridos incluídos no cluster.
* **Servidor WebLogic espera portas públicas e privadas para ser o mesmo para T3 acesso (por exemplo, ao utilizar a Enterprise JavaBeans).** Considere um cenário de várias camado onde uma aplicação de camada (EJB) do serviço está em execução num cluster de servidores de WebLogic constituídas por duas ou mais VMs, numa vNet com o nome **SLWLS**. O escalão de cliente está numa sub-rede diferente na mesma vNet, executar um programa de Java simple tentar chamar EJB na camada de serviço. Porque é necessário equilibrar a camada de serviço, um ponto de final público com balanceamento de carga tem de ser criadas para as máquinas virtuais no cluster de servidores de WebLogic. Se a porta privada que especificou é diferente da porta pública (por exemplo, 7006:7008), ocorre um erro, tais como o seguinte:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Isto acontece porque para qualquer T3 o acesso remoto, WebLogic Server espera que a porta de Balanceador de carga e a porta do servidor WebLogic gerido para ser o mesmo. No caso de anterior, o cliente está a aceder a porta 7006 (a porta do Balanceador de carga) e o servidor gerido está a escutar 7008 (a porta privada). Esta restrição se aplica apenas T3 acesso, não HTTP.

   Para evitar este problema, utilize uma das seguintes soluções:

  * Utilize os mesmos números da porta privada e pública para pontos finais com balanceamento de carga dedicados ao acesso T3.
  * Incluem o seguinte parâmetro JVM ao iniciar WebLogic Server:

         -Dweblogic.rjvm.enableprotocolswitch=true

Para informações relacionadas, consulte o artigo BDC **860340.1** em <http://support.oracle.com>.

* **Dinâmica clustering e limitações de balanceamento de carga.** Suponha que pretende utilizar um cluster dinâmico no servidor de WebLogic e expô-la através de um único e público com balanceamento de carga ponto final no Azure. Isto pode ser efetuado desde que utilizar um número de porta fixo para cada um dos servidores geridos (não dinamicamente atribuídos de um intervalo) e não se iniciam mais servidores geridos que existem máquinas que o administrador está a controlar (ou seja, não mais do que um gerido servidor por virtual m achine). Se a configuração resulta em mais servidores de WebLogic sejam iniciados que existem máquinas virtuais (ou seja, em que várias instâncias do servidor de WebLogic partilham a mesma máquina virtual), então, não é possível mais do que um essas instâncias de servidores de WebLogic Para vincular a um número de porta especificado – outros nessa máquina virtual falharem.

   Se configurar o servidor de admin para atribuir automaticamente os números de porta exclusivo para os respetivos servidores geridos, em seguida, o balanceamento de carga não é possível porque o Azure não suporta o mapeamento de uma única porta pública para várias portas privados, como seriam necessários para este configuração.
* **Várias instâncias do Weblogic Server numa máquina virtual.** Dependendo dos requisitos da sua implementação, pode considerar a opção de executar várias instâncias do servidor de WebLogic na mesma máquina virtual, se a máquina virtual é suficientemente grande. Por exemplo, uma máquina virtual de tamanho médio, que contém dois núcleos, pode optar por executar duas instâncias do WebLogic Server. Tenha em atenção contudo que recomendamos que evite pontos únicos de falha a introduzir na sua arquitetura que seria caso se tiver utilizado apenas uma máquina virtual que está a executar várias instâncias do WebLogic Server. Utilizar, pelo menos, duas máquinas virtuais pode ser uma abordagem melhor e cada uma dessas máquinas virtuais, em seguida, pode executar várias instâncias do WebLogic Server. Cada um destes instâncias de servidor WebLogic ainda pode ser parte do mesmo cluster. Tenha em atenção, no entanto, atualmente não é possível utilizar o Azure para pontos finais de balanceamento de carga que sejam expostos pelo implementações deste tipo WebLogic Server dentro da mesma máquina virtual, porque o Balanceador de carga do Azure requer os servidores com balanceamento de carga para ser distribuída entre exclusivo máquinas virtuais.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens da máquina virtual JDK Oracle
* **JDK 6 e 7 atualizações mais recentes.** Enquanto Recomendamos que utilize a versão mais recente pública, suportada do Java (atualmente Java 8), Azure também disponibiliza JDK 6 e 7 imagens. Destina-se para aplicações antigas que ainda não estão prontas para serem atualizados para o JDK 8. Durante as atualizações para imagens JDK anteriores já não poderão ser disponíveis ao público em geral, dado parceria de Microsoft com o Oracle, o JDK 6 e 7 imagens fornecidas pelo Azure destinam-se para contêm uma atualização mais recente do não público que normalmente é oferecida pelo Oracle para apenas a um grupo de Oracle do suportada clientes. Novas versões das imagens JDK serão disponibilizadas ao longo do tempo com versões atualizadas do JDK 6 e 7.

   O JDK disponível neste JDK 6 e 7 imagens e as máquinas virtuais e imagens derivadas deles, só pode ser utilizado no Azure.
* **JDK de 64 bits.** As imagens de máquina virtual Oracle WebLogic Server e as imagens de máquina virtual de Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

## <a name="next-steps"></a>Passos seguintes
Tem agora uma descrição geral das soluções de Oracle atual no Microsoft Azure. O próximo passo é implementar a sua primeira base de dados Oracle no Azure.
- Repita o [criar uma base de dados Oracle no Azure](oracle-database-quick-create.md) tutorial para começar.