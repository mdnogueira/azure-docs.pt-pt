---
title: Executar Cassandra com Linux no Azure | Microsoft Docs
description: "Como executar um cluster de Cassandra em Linux no Azure as máquinas virtuais a partir de uma aplicação Node.js"
services: virtual-machines-linux
documentationcenter: nodejs
author: tomarcher
manager: routlaw
editor: 
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: tarcher
ms.openlocfilehash: 1ff3d77ced6c9d90029b251490c05e52d9b43515
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Executar o Cassandra com o Linux no Azure e Aceder a partir de Node.js
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Ver modelos do Resource Manager para [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) e [Spark cluster e Cassandra em CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Descrição geral
Microsoft Azure é uma plataforma de nuvem aberto que executa ambos os Microsoft como bem como software não Microsoft que inclui os sistemas operativos, servidores de aplicações, mensagens middleware, bem como bases de dados NoSQL e SQL de ambos os modelos de comerciais e abrir a origem. Criação de resilientes serviços em nuvens públicas, incluindo o Azure requer um planeamento cuidadoso e arquitetura deliberate para ambos os servidores de aplicações como camadas de armazenamento bem. Arquitetura de armazenamento distribuído do Cassandra naturalmente ajuda na criação de sistemas elevados tolerante a falhas de cluster de falhas. Cassandra é uma base de dados NoSQL mantido pelo Apache Software Foundation a cassandra.apache.org; escala da nuvem Cassandra é escrito em Java e, por conseguinte, é executado no Windows, bem como em Linux plataformas.

O foco este artigo é para mostrar implementação Cassandra no Ubuntu como um cluster único e vários dados center tirar partido das máquinas virtuais do Microsoft Azure e redes virtuais. A implementação do cluster para cargas de trabalho de produção otimizada está fora do âmbito deste artigo porque requer configuração do nó de disco multi, design de topologia do anel adequado e dados de modelação para suportar a replicação necessária, consistência dos dados, débito e requisitos de elevada disponibilidade.

Este guia de artigo uma abordagem fundamental para mostrar que esteja envolvida na criação do Cassandra cluster em comparação com Docker, Chef ou Puppet, que pode fazer muito mais fácil a implementação de infraestrutura.  

## <a name="the-deployment-models"></a>Os modelos de implementação
Funcionamento em rede do Microsoft Azure permite a implementação de clusters privadas isolados, o acesso dos quais pode ser restringido a obter a segurança de rede detalhada.  Uma vez que este artigo sobre que mostra a implementação de Cassandra um nível de fundamentais, incidirá não no nível de consistência e a conceção do armazenamento otimizado para débito. Eis a lista de requisitos de rede para o nosso hipotético cluster:

* Sistemas externos que não é possível aceder Cassandra base de dados da dentro ou fora do Azure
* Cassandra cluster tem de estar protegido por um balanceador de carga para tráfego de thrift
* Implementar Cassandra nós em dois grupos em cada centro de dados para um disponibilidade do cluster avançada
* Bloqueie o cluster, por isso, que só o farm de servidores de aplicação tem acesso à base de dados diretamente
* Não existem públicas pontos finais de rede diferente de SSH
* Cada nó de Cassandra tem um endereço IP interno fixo

Cassandra pode ser implementado para uma única região do Azure ou para várias regiões, com base na natureza distribuída da carga de trabalho. Modelo de implementação de multirregião pode ser aproveitado para servir os utilizadores finais próximo para um determinado geografia através da infraestrutura de Cassandra mesma. Resolvida replicação do nó incorporada do Cassandra da sincronização do mestre multi escreve provenientes de múltiplos centros de dados e apresenta uma vista consistente dos dados para aplicações. Também pode ajudar a implementação de multirregião mitigação de riscos de falhas de serviço do Azure mais ampla. Topologia de replicação e consistência ajustáveis do Cassandra irão ajudar a satisfazer as necessidades de RPO diversificadas das aplicações.

### <a name="single-region-deployment"></a>Implementação única região
Iremos começar com uma implementação única região e recolher learnings na criação de um modelo de multirregião. Redes virtuais do Azure serão utilizada para criar sub-redes isoladas que podem ser cumpridos os requisitos de segurança de rede mencionados acima.  O processo descrito na criação da implementação única região utiliza Ubuntu 14.04 LTS e Cassandra 2.08; No entanto, o processo pode facilmente ser adotado uma larga maioria para outros variantes do Linux. Seguem-se algumas das características systemic da implementação única região.  

**Elevada disponibilidade:** nós Cassandra mostrados na figura 1 são implementados nos dois conjuntos de disponibilidade, para que os nós são distribuídos entre vários domínios de falhas de elevada disponibilidade. VMs anotadas com cada conjunto de disponibilidade está mapeada para 2 domínios de falhas.  O conceito de domínio de falhas para gerir não planeado pendente tempo (por exemplo, falhas de hardware ou software) enquanto o conceito de domínio de atualização (por exemplo, o convidado ou anfitrião SO aplicação de patches/atualizações, as atualizações de aplicações) é utilizado para gerir agendada tempo utiliza de Microsoft Azure. Consulte [recuperação após desastre e elevada disponibilidade para aplicações do Azure](http://msdn.microsoft.com/library/dn251004.aspx) para a função de domínios de falhas e a atualização na attaining elevada disponibilidade.

![Implementação única região](./media/cassandra-nodejs/cassandra-linux1.png)

Figura 1: Implementação de única região

Tenha em atenção que o do momento desta redação, Azure não permite o mapeamento de um grupo de VMs a um domínio de falhas específico; explícito Por conseguinte, mesmo com o modelo de implementação apresentado na figura 1, é estatisticamente provável que todas as máquinas virtuais podem ser mapeadas para os dois domínios de falhas em vez de quatro.

**Tráfego de Thrift de balanceamento de carga:** bibliotecas de cliente de Thrift no servidor web ligar ao cluster através de um balanceador de carga interno. Isto requer que o processo de adicionar balanceador de carga interno para a sub-rede "dados" (consulte a figura 1) no contexto do serviço de nuvem que aloja o cluster Cassandra. Assim que o Balanceador de carga interno está definido, cada nó requer o ponto final com balanceamento de carga para ser adicionado com as anotações de um conjunto com balanceamento de carga com o nome do Balanceador de carga definida anteriormente. Consulte [Azure interno balanceamento de carga ](../../../load-balancer/load-balancer-internal-overview.md)para obter mais detalhes.

**Cluster Seeds:** é importante selecionar a maioria de nós elevada para seeds como os novos nós irão comunicar connosco seed para detetar a topologia do cluster. Um nó de cada conjunto de disponibilidade está designado como nós seed para evitar ponto único de falha.

**Fator de replicação e o nível de consistência:** compilação na elevada disponibilidade e dados durabilidade do Cassandra é caracterizada pelo fator de replicação (RF - número de cópias de cada linha armazenadas no cluster) e o nível de consistência (número de réplicas para ser de leitura/escrita antes da devolução do resultado para o autor da chamada). Fator de replicação é especificado durante a criação de KEYSPACE (semelhante a uma base de dados relacional), enquanto que o nível de consistência é especificado ao emitir a consulta CRUD. Consulte a documentação de Cassandra em [configurar para consistência](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para detalhes de consistência e a fórmula para o cálculo de quórum.

Cassandra suporta dois tipos de modelos de integridade de dados – consistência e a consistência Eventual; o fator de replicação e nível de consistência serão em conjunto determinam se os dados serão consistentes, assim que uma operação de escrita foi concluída ou que será eventualmente consistente. Por exemplo, como o nível de consistência será sempre a especificação QUÓRUM garante a consistência de dados ao qualquer nível de consistência, abaixo o número de réplicas para serem escritos conforme necessário para obter um QUÓRUM (por exemplo, um) resulta em dados que está a ser eventualmente consistente.

O cluster de nó de 8 mostrado acima, com um fator de replicação de 3 e QUÓRUM (2 nós são lidas ou escritas para consistência) ao nível de consistência de leitura/escrita, pode sobreviver a perda teórico de, mais 1 nó por grupo de replicação antes do início da aplicação dar por isso a falha. Esta parte do princípio de que todos os espaços de chaves também tem balanceamento de leitura/escritam pedidos.  Seguem-se os parâmetros que iremos utilizar para o cluster implementado:

Configuração de cluster de Cassandra única região:

| Parâmetro de cluster | Valor | Observações |
| --- | --- | --- |
| Número de nós (N) |8 |Número total de nós no cluster |
| Fator de replicação (RF) |3 |Número de réplicas de uma linha especificada |
| Nível de consistência (escrita) |QUORUM[(RF/2) +1) = 2] o resultado da fórmula é arredondado para baixo |Escreve no máximo de 2 réplicas antes da resposta é enviada para o autor da chamada; réplica 3rd é escrita de forma eventualmente consistente. |
| Nível de consistência (leitura) |QUÓRUM [(RF/2) + 1 = 2] o resultado da fórmula é arredondado para baixo |Lê 2 réplicas antes de enviar a resposta para o autor da chamada. |
| Estratégia de replicação |Consulte NetworkTopologyStrategy [replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações |Compreende a topologia de implementação e coloca réplicas em nós, para que todas as réplicas não acabar em Bastidor mesmo |
| Snitch |Consulte GossipingPropertyFileSnitch [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações |NetworkTopologyStrategy utiliza um conceito de snitch para compreender a topologia. GossipingPropertyFileSnitch proporciona um melhor controlo em cada nó de mapeamento para o Centro de dados e bastidor. O cluster utiliza, em seguida, gossip para propagar estas informações. Isto é muito mais simples na definição de IP dinâmica relativo PropertyFileSnitch |

**Considerações do Azure para o Cassandra Cluster:** capacidade de máquinas virtuais do Microsoft Azure utiliza o Blob storage do Azure para a persistência de disco; Armazenamento do Azure guarda 3 réplicas de cada disco para uma durabilidade elevada. Isto significa que cada linha da dados inseridos na tabela Cassandra já está armazenada na 3 réplicas e, por conseguinte, consistência de dados está já tratada, mesmo que o fator de replicação (RF) é 1. O problema principal com fator de replicação que está a ser 1 é que a aplicação será tempo de inatividade, mesmo se a um único nó Cassandra falha. No entanto, se um nó para baixo para os problemas (por exemplo, hardware, falhas de software do sistema) reconhecidos pelo controlador de recursos do Azure, aprovisionar um novo nó no seu lugar com as mesmo unidades de armazenamento. Aprovisionamento de um novo nó ao substituir a antiga pode demorar alguns minutos.  Da mesma forma para atividades de manutenção planeada, como as alterações de SO convidado, Cassandra atualiza e as alterações de aplicação controlador de recursos do Azure efetua a anular atualizações de nós no cluster.  Implementar atualizações também poderá demorar baixo alguns nós numa altura e, por conseguinte, o cluster pode tempo de inatividade breve para partições alguns. No entanto, os dados não será perdidos devido a redundância incorporada do Storage do Azure.  

Para sistemas implementados no Azure que não necessita de elevada disponibilidade (por exemplo, cerca de 99,9 que é equivalente a 8.76 hrs/ano; consulte [elevada disponibilidade](http://en.wikipedia.org/wiki/High_availability) para obter detalhes) poderá executar com RF = 1 e o nível de consistência = ONE.  Para aplicações com requisitos de elevada disponibilidade, RF = 3 e o nível de consistência = QUÓRUM serão tolerar o tempo de indisponibilidade de um de nós de uma das réplicas. RF = 1 em implementações tradicionais (por exemplo, no local) não pode ser utilizado devido a possíveis perdas de dados resultantes de problemas como falhas de disco.   

## <a name="multi-region-deployment"></a>Implementação de multirregião
Ajuda dados System center-com suporte para replicação e consistência modelo do Cassandra descrito acima com a implementação de multirregião Box sem a necessidade de quaisquer ferramentas externas. É bastante diferente das bases de dados relacionais tradicionais em que o programa de configuração para espelhamento da base de dados para escritas multi-mestre pode ser bastante complexo. Cassandra numa região multi configurar pode ajudá-lo com os cenários de utilização, incluindo o seguinte:

**Implementação baseada em proximidade:** aplicações multi-inquilino, com Limpar mapeamento de utilizadores de inquilino-para-região, pode ser benefited por baixas latências do cluster de multirregião. Por exemplo, um gestão de aprendizagem sistemas operativos instituições educational podem implementar um cluster distribuído em regiões EUA leste e EUA oeste para servir os respetivos campuses para transacional, bem como a análise. Os dados podem ser consistentes localmente no tempo de leituras e escritas e podem ser eventualmente consistentes em ambas as regiões. Existem outros exemplos de como a distribuição de suporte de dados, comércio e nada e tudo o que funciona georreplicação concentrated utilizador base é um caso de utilização boa para este modelo de implementação.

**Elevada disponibilidade:** redundância é um fator chave attaining elevada disponibilidade de hardware e software; Consulte edifício fiável sistemas de nuvem no Microsoft Azure para obter mais detalhes. No Microsoft Azure, de forma fiável apenas de atingirem a total redundância verdadeira é ao implementar um cluster de multirregião. É possível implementar aplicações num modo ativo-ativo ou ativo-passivo e se uma das regiões estiver em baixo, Gestor de tráfego do Azure pode redirecionar o tráfego para a região de Active Directory.  Com a implementação única região, se a disponibilidade de 99,9, uma implementação de dois região pode obter um disponibilidade dos 99.9999 calculado pela fórmula: (1-(1-0.999) * (1-0.999)) * 100); consulte o documento acima para obter mais detalhes.

**Recuperação após desastre:** cluster multirregião Cassandra, se concebido corretamente, pode conseguir falhas de center catastrófica de dados. Se uma região estiver desativado, a aplicação implementada a outras regiões pode começar a servir os utilizadores finais. Como quaisquer outras empresas continuidade implementações, a aplicação tem de ser tolerância a falhas para algumas perda de dados resultantes de dados no pipeline assíncrono. No entanto, Cassandra faz com que a recuperação muito swifter que o tempo que os processos de recuperação de bases de dados tradicionais. Figura 2 mostra o modelo de implementação de multirregião típica com oito nós em cada região. Ambas as regiões são imagens de espelho de si para o mesmo do symmetry; estruturas de mundo real dependem do tipo de carga de trabalho (por exemplo, analítico ou transacional), RPO, RTO, consistência dos dados e requisitos de disponibilidade.

![Implementação de região de múltipla](./media/cassandra-nodejs/cassandra-linux2.png)

Figura 2: Implementação de multirregião Cassandra

### <a name="network-integration"></a>Integração de rede
Conjuntos de máquinas virtuais implementadas em redes privadas, localizados em duas regiões comunica com entre si utilizando um túnel VPN. O túnel VPN liga-se dois gateways de software aprovisionados durante o processo de implementação de rede. Ambas as regiões têm a arquitetura de rede semelhante em termos de sub-redes "web" e "dados"; Redes do Azure permite a criação de sub-redes tantos conforme necessário e aplicar ACLs conforme necessário para a segurança de rede. Ao conceber a topologia de cluster inter-latência de comunicação de centro de dados e o impacto do tráfego de rede económico têm de ser considerados.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Consistência dos dados para a implementação de centro de dados de vários
Distribuída implementações têm de ter em consideração o impacto da topologia de cluster no débito e elevada disponibilidade. O nível de consistência e RF precisam de ser selecionada forma a que o quórum não depende da disponibilidade de todos os centros de dados.
Para um sistema que tem de consistência elevada, um LOCAL_QUORUM para o nível de consistência (para leituras e escritas) será Certifique-se de que o locais leituras e escritas são forem satisfeitas a partir de nós locais enquanto os dados são replicados de forma assíncrona para os centros de dados remota.  Tabela 2 resume os detalhes de configuração para o cluster de multirregião descritos mais tarde na operação de escrita cópias de segurança.

**Configuração de cluster Cassandra dois-região**

| Parâmetro de cluster | Valor | Observações |
| --- | --- | --- |
| Número de nós (N) |8 + 8 |Número total de nós no cluster |
| Fator de replicação (RF) |3 |Número de réplicas de uma linha especificada |
| Nível de consistência (escrita) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] o resultado da fórmula é arredondado para baixo |2 nós serão escritos no Centro de dados primeiro forma síncrona; os nós de 2 adicionais necessários para quórum serão escritos no modo assíncrono para o Centro de dados 2nd. |
| Nível de consistência (leitura) |LOCAL_QUORUM ((RF/2) + 1) = 2, o resultado da fórmula é arredondado para baixo |Pedidos de leitura são satisfeitos de apenas uma região; são lidos a 2 nós antes de é enviada a resposta de volta para o cliente. |
| Estratégia de replicação |Consulte NetworkTopologyStrategy [replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações |Compreende a topologia de implementação e coloca réplicas em nós, para que todas as réplicas não acabar em Bastidor mesmo |
| Snitch |Consulte GossipingPropertyFileSnitch [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações |NetworkTopologyStrategy utiliza um conceito de snitch para compreender a topologia. GossipingPropertyFileSnitch proporciona um melhor controlo em cada nó de mapeamento para o Centro de dados e bastidor. O cluster utiliza, em seguida, gossip para propagar estas informações. Isto é muito mais simples na definição de IP dinâmica relativo PropertyFileSnitch |

## <a name="the-software-configuration"></a>A CONFIGURAÇÃO DO SOFTWARE
As seguintes versões de software são utilizadas durante a implementação:

<table>
<tr><th>Software</th><th>Origem</th><th>Versão</th></tr>
<tr><td>JRE    </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Uma vez que transferir da JRE requer manual aceitação de licença do Oracle, para simplificar a implementação, transferir o software necessário para o ambiente de trabalho para carregar mais tarde para a imagem de modelo Ubuntu, que iremos criar como um precursor para a implementação do cluster.

Transferir o software acima para um diretório de transferências bem conhecido (por exemplo, %TEMP%/downloads no Windows ou ~/Downloads na maioria das distribuições de Linux ou Mac) no computador local.

### <a name="create-ubuntu-vm"></a>CRIAR A VM COM UBUNTU
Neste passo do processo de iremos criar Ubuntu imagem com o software de pré-requisito para que possa ser reutilizada a imagem para o aprovisionamento de vários nós Cassandra.  

#### <a name="step-1-generate-ssh-key-pair"></a>PASSO 1: Gerar par de chaves de SSH
Azure tem uma chave pública que é PEM ou DER codificado o momento de aprovisionamento de X509. Gere um par de chaves públicas/privadas utilizando as instruções localizadas como utilizar o SSH com o Linux no Azure. Se planeia utilizar putty.exe como um cliente SSH no Windows ou Linux, tem de converter PEM codificado RSA a chave privada formato PPK utilizando puttygen.exe; as instruções para isto podem ser encontradas na página web acima.

#### <a name="step-2-create-ubuntu-template-vm"></a>PASSO 2: Criar o modelo de Ubuntu VM
Para criar o modelo de VM, inicie sessão no portal clássico do Azure e utilize a seguinte sequência: clique em novo, COMPUTAÇÃO, máquina VIRTUAL, FROM GALERIA, UBUNTU, Ubuntu Server 14.04 LTS e, em seguida, clique na seta à direita. Para um tutorial que descreve como criar uma VM com Linux, consulte Criar uma Linux em execução de Máquina Virtual.

Introduza as seguintes informações no ecrã "configuração de Máquina Virtual" #1:

<table>
<tr><th>NOME DO CAMPO              </td><td>       VALOR DO CAMPO               </td><td>         OBSERVAÇÕES                </td><tr>
<tr><td>DATA DE LANÇAMENTO DA VERSÃO    </td><td> Selecione uma data na lista pendente</td><td></td><tr>
<tr><td>NOME DA MÁQUINA VIRTUAL    </td><td> modelo de CASS                   </td><td> Este é o nome do anfitrião da VM </td><tr>
<tr><td>CAMADA                     </td><td> STANDARD                           </td><td> Deixe a predefinição              </td><tr>
<tr><td>TAMANHO                     </td><td> A1                              </td><td>Selecione a VM com base nas necessidades de e/s; para este fim, deixe a predefinição </td><tr>
<tr><td> NOVO NOME DE UTILIZADOR             </td><td> localadmin                       </td><td> "admin" é um nome de utilizador reservado no Ubuntu 12. xx e depois</td><tr>
<tr><td> AUTENTICAÇÃO         </td><td> Clique em caixa de verificação                 </td><td>Verifique se pretende proteger com uma chave SSH </td><tr>
<tr><td> CERTIFICADO             </td><td> nome de ficheiro do certificado de chave pública </td><td> Utilize a chave pública gerada anteriormente</td><tr>
<tr><td> Nova palavra-passe    </td><td> palavra-passe segura </td><td> </td><tr>
<tr><td> Confirmar palavra-passe    </td><td> palavra-passe segura </td><td></td><tr>
</table>

Introduza as seguintes informações no ecrã "configuração de Máquina Virtual" #2:

<table>
<tr><th>NOME DO CAMPO             </th><th> VALOR DO CAMPO                       </th><th> OBSERVAÇÕES                                 </th></tr>
<tr><td> SERVIÇO EM NUVEM    </td><td> Criar um novo serviço de nuvem    </td><td>Serviço em nuvem é um recursos de computação do contentor como máquinas virtuais</td></tr>
<tr><td> NOME DE DNS DO SERVIÇO DE NUVEM    </td><td>ubuntu template.cloudapp.net    </td><td>Dê um nome de Balanceador de carga com máquina</td></tr>
<tr><td> REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL </td><td>    EUA Oeste    </td><td> Selecione uma região a partir da qual as suas aplicações web, aceder ao Cassandra cluster</td></tr>
<tr><td>CONTA DE ARMAZENAMENTO </td><td>    Utilizar predefinição    </td><td>Utilizar a conta do storage predefinida ou uma conta de armazenamento previamente criada numa região específica</td></tr>
<tr><td>CONJUNTO DE DISPONIBILIDADE </td><td>    Nenhuma </td><td>    Pode deixar em branco</td></tr>
<tr><td>PONTOS FINAIS    </td><td>Utilizar predefinição </td><td>    Utilize a configuração predefinida do SSH </td></tr>
</table>

Clique em seta para a direita, deixe as predefinições no ecrã #3 e clique no botão "verificar" para concluir a processo de aprovisionamento de VM. Após alguns minutos, a VM com o nome "ubuntu-template" deve estar no estado "em execução".

### <a name="install-the-necessary-software"></a>INSTALAR O SOFTWARE NECESSÁRIO
#### <a name="step-1-upload-tarballs"></a>PASSO 1: Carregamento tarballs
Utilizar o scp ou pscp, copie o software de anteriormente transferido para diretório ~/downloads utilizando o seguinte formato de comando:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp servidor-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Repita o comando acima para JRE, bem como para o bits Cassandra.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>PASSO 2: Preparar a estrutura de diretórios e extrair os arquivos
Iniciar sessão na VM e criar a estrutura de diretórios e extrair software como um Superutilizador utilizando o script de bash abaixo:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Se cole este script janela vim, certifique-se remover o avanço retorno ('\r ") com o seguinte comando:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Passo 3: Editar perfil/etc.
Acrescente o seguinte no final:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Passo 4: Instalar JNA para sistemas de produção
Utilize a seguinte sequência de comandos: O seguinte comando vai instalar jna 3.2.7.jar e jna plataforma 3.2.7.jar /usr/share.java diretório apt sudo-get instalar libjna java

Crie ligações simbólicas no diretório CASS_HOME/lib $ para que o script de arranque Cassandra pode encontrar estas v7:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Passo 5: Configurar cassandra.yaml
Edite cassandra.yaml em cada VM para refletir a configuração necessária por todas as máquinas virtuais [que irão otimizar este durante o aprovisionamento real]:

<table>
<tr><th>Nome do campo   </th><th> Valor  </th><th>    Observações </th></tr>
<tr><td>cluster_name </td><td>    "CustomerService"    </td><td> Utilize o nome que reflete a implementação</td></tr>
<tr><td>listen_address    </td><td>[pode deixar em branco]    </td><td> Eliminar "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[pode deixar em branco]    </td><td> Eliminar "localhost" </td></tr>
<tr><td>Seeds    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Lista de todos os endereços IP que são designados como seeds.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Isto é utilizado pelo NetworkTopologyStrateg ao inferir o Centro de dados e o bastidor da VM</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Passo 6: Capturar a imagem VM
Iniciar sessão na máquina virtual utilizando o nome de anfitrião (hk-ACs-template.cloudapp.net) e a chave SSH privada criada anteriormente. Ver como utilizar o SSH com o Linux no Azure para obter detalhes sobre como iniciar sessão utilizando o comando ssh ou putty.exe.

Execute a sequência de ações para capturar a imagem seguinte:

##### <a name="1-deprovision"></a>1. Desaprovisionamento
Utilize o comando "sudo waagent – deprovision + utilizador" para remover informações específicas de instância de Máquina Virtual. Consulte para [como capturar uma Máquina Virtual Linux](capture-image.md) para utilizar como um modelo mais detalhes sobre o processo de captura de imagem.

##### <a name="2-shutdown-the-vm"></a>2: encerre a VM
Certifique-se de que a máquina virtual é realçada e clique na ligação de encerramento da barra de comando na parte inferior.

##### <a name="3-capture-the-image"></a>3: capturar a imagem
Certifique-se de que a máquina virtual é realçada e clique na ligação de captura a partir da barra de comando na parte inferior. No ecrã seguinte, atribuir um nome de imagem (por exemplo, hk-cas-2-08-ub-14-04-2014071), conforme apropriado descrição da imagem e clique na marca de "verificação" para concluir o processo de captura.

Esta ação irá demorar alguns segundos e a imagem deve estar disponível na secção das minhas imagens da galeria da imagem. A VM de origem será eliminado automaticamente depois da imagem ser capturada com êxito. 

## <a name="single-region-deployment-process"></a>Processo de implementação única região
**Passo 1: Criar a rede Virtual** iniciar sessão no portal do Azure e criar uma rede virtual (clássica) com os atributos mostrados na seguinte tabela. Consulte [criar uma rede virtual com o portal do Azure (clássica)](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para obter passos detalhados do processo.      

<table>
<tr><th>Nome de atributo VM</th><th>Valor</th><th>Observações</th></tr>
<tr><td>Nome</td><td>vnet-cass--EUA oeste</td><td></td></tr>
<tr><td>Região</td><td>EUA Oeste</td><td></td></tr>
<tr><td>Servidores DNS</td><td>Nenhuma</td><td>Ignore esta indicação como podemos não estiver a utilizar um servidor DNS</td></tr>
<tr><td>Espaço de endereços</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>IP inicial</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes:

<table>
<tr><th>Nome</th><th>IP inicial</th><th>CIDR</th><th>Observações</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Sub-rede para a web farm</td></tr>
<tr><td>dados</td><td>10.1.2.0</td><td>/24 (251)</td><td>Sub-rede para os nós de base de dados</td></tr>
</table>

Dados e sub-redes Web podem ser protegidas através de grupos de segurança de rede a cobertura do qual está fora do âmbito deste artigo.  

**Passo 2: Aprovisionar máquinas** utilizando a imagem criada anteriormente, iremos criar as seguintes máquinas virtuais no servidor de cloud "hk-c-svc-Oeste" e vinculá-los para as respetivas sub-redes, como mostrado abaixo:

<table>
<tr><th>Nome do computador    </th><th>Subrede    </th><th>Endereço IP    </th><th>Conjunto de disponibilidade</th><th>DC/Rack</th><th>Seed?</th></tr>
<tr><td>HK-c1--EUA oeste    </td><td>dados    </td><td>10.1.2.4    </td><td>HK-c-aset-1    </td><td>DC = WESTUS bastidor = rack1 </td><td>Sim</td></tr>
<tr><td>HK-c2--EUA oeste    </td><td>dados    </td><td>10.1.2.5    </td><td>HK-c-aset-1    </td><td>DC = WESTUS bastidor = rack1    </td><td>Não </td></tr>
<tr><td>HK-c3--EUA oeste    </td><td>dados    </td><td>10.1.2.6    </td><td>HK-c-aset-1    </td><td>DC = WESTUS bastidor = rack2    </td><td>Sim</td></tr>
<tr><td>HK-c4--EUA oeste    </td><td>dados    </td><td>10.1.2.7    </td><td>HK-c-aset-1    </td><td>DC = WESTUS bastidor = rack2    </td><td>Não </td></tr>
<tr><td>HK-c5--EUA oeste    </td><td>dados    </td><td>10.1.2.8    </td><td>HK-c-aset-2    </td><td>DC = WESTUS bastidor = rack3    </td><td>Sim</td></tr>
<tr><td>HK-c6--EUA oeste    </td><td>dados    </td><td>10.1.2.9    </td><td>HK-c-aset-2    </td><td>DC = WESTUS bastidor = rack3    </td><td>Não </td></tr>
<tr><td>HK-c7--EUA oeste    </td><td>dados    </td><td>10.1.2.10    </td><td>HK-c-aset-2    </td><td>DC = WESTUS bastidor = rack4    </td><td>Sim</td></tr>
<tr><td>HK-c8--EUA oeste    </td><td>dados    </td><td>10.1.2.11    </td><td>HK-c-aset-2    </td><td>DC = WESTUS bastidor = rack4    </td><td>Não </td></tr>
<tr><td>HK-w1--EUA oeste    </td><td>Web    </td><td>10.1.1.4    </td><td>HK-w-aset-1    </td><td>                       </td><td>N/D</td></tr>
<tr><td>HK-w2--EUA oeste    </td><td>Web    </td><td>10.1.1.5    </td><td>HK-w-aset-1    </td><td>                       </td><td>N/D</td></tr>
</table>

Criação da lista acima de VMs requer o seguinte processo:

1. Criar um serviço em nuvem vazio numa região específica
2. Criar uma VM a partir da imagem capturada anteriormente e ligue-à rede virtual criada anteriormente; Repita esta para todas as VMs
3. Adicionar um balanceador de carga interno para o serviço em nuvem e ligue-à sub-rede "dados"
4. Para cada VM criada anteriormente, adicione um ponto de final com balanceamento de carga para tráfego de thrift através de um conjunto com balanceamento de carga ligado ao balanceador de carga interno criada anteriormente

O processo de acima pode ser executado através do portal clássico do Azure; utilizar uma máquina de Windows (utilize uma VM no Azure se não tiver acesso a uma máquina Windows), utilize o seguinte script do PowerShell para aprovisionar automaticamente todos os 8 VMs.

**Lista 1: Script do PowerShell para o aprovisionamento de máquinas virtuais**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Passo 3: Configurar Cassandra em cada VM**

Iniciar sessão na VM e efetuar as seguintes tarefas:

* Edite $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de centro e bastidor de dados:
  
       dc =EASTUS, rack =rack1
* Edite cassandra.yaml para configurar nós seed como abaixo:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Passo 4: Iniciar as VMs e o cluster de teste**

Inicie sessão num de nós (por exemplo, hk-c1--EUA oeste) e execute o seguinte comando para ver o estado do cluster:

       nodetool –h 10.1.2.4 –p 7199 status

Deverá ver a apresentação semelhante às abaixo para um cluster do nó de 8:

<table>
<tr><th>Estado</th><th>Endereço    </th><th>Carregar    </th><th>Tokens    </th><th>É proprietário </th><th>ID de anfitrião    </th><th>Bastidor</th></tr>
<tr><th>ANULAR    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>GUID (removido)</td><td>rack1</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>GUID (removido)</td><td>rack1</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (removido)</td><td>rack2</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (removido)</td><td>rack2</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (removido)</td><td>rack3</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (removido)</td><td>rack3</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (removido)</td><td>rack4</td></tr>
<tr><th>ANULAR    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (removido)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testar o Cluster única região
Utilize os seguintes passos para o cluster de teste:

1. Utilizando o commandlet de Get-AzureInternalLoadbalancer de comando do Powershell, obter o endereço IP do Balanceador de carga interno (por exemplo  10.1.2.101). A sintaxe do comando é mostrada abaixo: Get-AzureLoadbalancer – ServiceName "hk-c-svc--EUA Oeste" [apresenta os detalhes de Balanceador de carga interno, juntamente com o respetivo endereço IP]
2. Iniciar sessão no web farm VM (por exemplo, hk-w1--EUA oeste) a utilizar o Putty ou ssh
3. Executar $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Utilize os seguintes comandos CQL para verificar se o cluster está a funcionar:
   
     Criar KEYSPACE customers_ks com replicação = {'class': 'SimpleStrategy', 'replication_factor': 3};   UTILIZAR customers_ks;   Criar tabela Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Inserir em Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Inserir em Customers(customer_id, firstname, lastname) os valores (2, 'Joana', 'Silva');
   
     SELECIONAR * de clientes;

Deverá ver uma apresentação como abaixo:

<table>
  <tr><th> customer_id </th><th> nome próprio </th><th> Apelido </th></tr>
  <tr><td> 1 </td><td> João </td><td> Silva </td></tr>
  <tr><td> 2 </td><td> Joana </td><td> Silva </td></tr>
</table>

Tenha em atenção que o keyspace criado no passo 4 utiliza SimpleStrategy com um replication_factor de 3. SimpleStrategy é recomendada para os dados únicos center implementações enquanto NetworkTopologyStrategy para dados multi center implementações. Um replication_factor de 3 proporcionará tolerância a falhas de nó.

## <a id="tworegion"></a>Processo de implementação de Multirregião
Irá tirar partido de implementação única região concluída e repita o mesmo processo para instalar a segunda região. A principal diferença entre a implementação única e várias a região é a configuração de túnel VPN para a comunicação entre região; Iremos começar com a instalação de rede, aprovisionar as VMs e configurar Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Passo 1: Criar a rede Virtual a região 2nd
Inicie sessão no portal clássico do Azure e criar uma rede Virtual com a mostrar os atributos na tabela. Consulte [configurar uma rede Virtual Cloud-Only no portal clássico do Azure](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para obter passos detalhados do processo.      

<table>
<tr><th>Nome do atributo    </th><th>Valor    </th><th>Observações</th></tr>
<tr><td>Nome    </td><td>vnet-cass-Leste-nos</td><td></td></tr>
<tr><td>Região    </td><td>EUA Leste</td><td></td></tr>
<tr><td>Servidores DNS        </td><td></td><td>Ignore esta indicação como podemos não estiver a utilizar um servidor DNS</td></tr>
<tr><td>Configurar uma VPN ponto a site</td><td></td><td>        Ignore esta indicação</td></tr>
<tr><td>Configurar uma rede de VPN</td><td></td><td>        Ignore esta indicação</td></tr>
<tr><td>Espaço de endereços    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>IP inicial    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes:

<table>
<tr><th>Nome    </th><th>IP inicial    </th><th>CIDR    </th><th>Observações</th></tr>
<tr><td>Web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Sub-rede para a web farm</td></tr>
<tr><td>dados    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Sub-rede para os nós de base de dados</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Passo 2: Criar redes locais
Uma rede Local na rede virtual do Azure é um espaço de endereços de proxy que mapeia para um site remoto, incluindo uma nuvem privada ou outra região do Azure. Este espaço de endereços de proxy está vinculado a um gateway remoto para o encaminhamento de rede para os destinos de rede à direita. Consulte [configurar uma ligação VNet a VNet](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para as instruções em estabelecer a ligação VNET a VNET.

Crie duas redes locais pelos seguintes detalhes:

| Nome da rede | Endereço de Gateway VPN | Espaço de endereços | Observações |
| --- | --- | --- | --- |
| HK-lnet-Map-to-East-US |23.1.1.1 |10.2.0.0/16 |Durante a criação da rede Local, atribua um marcador de posição endereço de gateway. O endereço de real gateway é preenchido quando é criado o gateway. Certifique-se de que o espaço de endereços corresponde exatamente a respetiva VNET remota; Neste caso a VNET criada na região EUA Leste. |
| HK-lnet-Map-to-West-US |23.2.2.2 |10.1.0.0/16 |Durante a criação da rede Local, atribua um marcador de posição endereço de gateway. O endereço de real gateway é preenchido quando é criado o gateway. Certifique-se de que o espaço de endereços corresponde exatamente a respetiva VNET remota; Neste caso a VNET criada na região EUA oeste. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Passo 3: Mapa "Local" rede as respetivas VNETs
No portal clássico do Azure, selecione cada vnet, clique em "Configurar", consulte "Ligar à rede local" e selecione as redes locais pelos seguintes detalhes:

| Rede Virtual | Rede local |
| --- | --- |
| HK-vnet--EUA oeste |HK-lnet-Map-to-East-US |
| HK-vnet-Leste-nos |HK-lnet-Map-to-West-US |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Passo 4: Criar Gateways no VNET1 e VNET2
A partir do dashboard das redes virtuais, clique em criar GATEWAY que activarão o gateway de VPN do processo de aprovisionamento. Após alguns minutos, o dashboard de cada rede virtual deve apresentar o endereço de gateway real.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Passo 5: Redes de "Local" de atualização com os endereços do respetivo "Gateway"
Edite as redes locais para substituir o endereço IP do gateway de marcador de posição com o endereço IP real dos gateways apenas aprovisionados. Utilize o seguinte mapeamento:

<table>
<tr><th>Rede local    </th><th>Gateway de Rede Virtual</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Gateway de hk-vnet--EUA oeste</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Gateway de hk-vnet-Leste-nos</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Passo 6: Atualizar a chave partilhada
Utilize o seguinte script do Powershell para atualizar a chave de IPSec de cada gateway VPN [Utilize a chave de sake para ambas as gateways]: hk-lnet-map-to-west-us de - LocalNetworkSiteName do conjunto AzureVNetGatewayKey - VNetName hk-vnet-Leste-nos - SharedKey hk-lnet-map-to-east-us de - LocalNetworkSiteName do D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet--EUA oeste - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Passo 7: Estabelecer a ligação de VNET a VNET
No portal clássico do Azure, utilize o menu de "DASHBOARD" das redes virtuais para estabelecer a ligação de gateway a gateway. Utilize os itens de menu "Ligar" na barra de ferramentas inferior. Após alguns minutos o dashboard deve apresentar os detalhes da ligação graficamente.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Passo 8: Criar as máquinas virtuais na região #2
Criar a imagem do Ubuntu conforme descrito na implementação de região #1 ao seguir os mesmos passos ou copiar o ficheiro VHD de imagem para a conta de armazenamento do Azure localizado numa região #2 e criar a imagem. Utilizar esta imagem e crie a seguinte lista de máquinas virtuais para um novo serviço em nuvem hk-c-svc-leste--na:

| Nome do computador | Subrede | Endereço IP | Conjunto de disponibilidade | DC/Rack | Seed? |
| --- | --- | --- | --- | --- | --- |
| HK-c1-Leste-nos |dados |10.2.2.4 |HK-c-aset-1 |DC = EASTUS bastidor = rack1 |Sim |
| HK-c2-Leste-nos |dados |10.2.2.5 |HK-c-aset-1 |DC = EASTUS bastidor = rack1 |Não |
| HK-c3-Leste-nos |dados |10.2.2.6 |HK-c-aset-1 |DC = EASTUS bastidor = rack2 |Sim |
| HK-c5-Leste-nos |dados |10.2.2.8 |HK-c-aset-2 |DC = EASTUS bastidor = rack3 |Sim |
| HK-c6-Leste-nos |dados |10.2.2.9 |HK-c-aset-2 |DC = EASTUS bastidor = rack3 |Não |
| HK-c7-Leste-nos |dados |10.2.2.10 |HK-c-aset-2 |DC = EASTUS bastidor = rack4 |Sim |
| HK-c8-Leste-nos |dados |10.2.2.11 |HK-c-aset-2 |DC = EASTUS bastidor = rack4 |Não |
| HK-w1-Leste-nos |Web |10.2.1.4 |HK-w-aset-1 |N/D |N/D |
| HK-w2-Leste-nos |Web |10.2.1.5 |HK-w-aset-1 |N/D |N/D |

Siga as mesmas instruções como região #1, mas utilize 10.2.xxx.xxx espaço de endereço.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Passo 9: Configurar Cassandra em cada VM
Iniciar sessão na VM e efetuar as seguintes tarefas:

1. Editar $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de centro e bastidor de dados no formato: dc = EASTUS bastidor = rack1
2. Editar cassandra.yaml para configurar nós seed: Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>Passo 10: Iniciar Cassandra
Inicie sessão em cada VM e inicie Cassandra em segundo plano, executando o seguinte comando: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testar o Cluster de Multirregião
Por agora Cassandra implementado 16 nós com 8 nós em cada região do Azure. Estes nós estão no mesmo cluster em virtude o nome comum do cluster e a configuração do nó seed. Utilize o seguinte processo para o cluster de teste:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Passo 1: Obter o IP de Balanceador de carga interno para ambas as regiões com o PowerShell
* Get-AzureInternalLoadbalancer - ServiceName "hk-c-svc--EUA Oeste"
* Get-AzureInternalLoadbalancer - ServiceName "hk-c-svc-Leste-us"  
  
    Tenha em atenção os endereços IP (por exemplo, oeste - 10.1.2.101 Leste - 10.2.2.101) apresentado.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Passo 2: Executar o seguinte na região Oeste depois de iniciar sessão em hk-w1--EUA oeste
1. Executar $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Execute os seguintes comandos CQL:
   
     Criar KEYSPACE customers_ks com replicação = {'class': 'NetworkToplogyStrategy', 'WESTUS': 3, 'EASTUS': 3};   UTILIZAR customers_ks;   Criar tabela Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Inserir em Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Inserir em Customers(customer_id, firstname, lastname) os valores (2, 'Joana', 'Silva');   SELECIONAR * de clientes;

Deverá ver uma apresentação como abaixo:

| customer_id | nome próprio | Apelido |
| --- | --- | --- |
| 1 |João |Silva |
| 2 |Joana |Silva |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Passo 3: Execute o seguinte na região Leste depois de iniciar sessão em hk-w1-leste--na:
1. Executar $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Execute os seguintes comandos CQL:
   
     UTILIZAR customers_ks;   Criar tabela Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Inserir em Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Inserir em Customers(customer_id, firstname, lastname) os valores (2, 'Joana', 'Silva');   SELECIONAR * de clientes;

Deverá ver a apresentação mesma visto para a região Oeste:

| customer_id | nome próprio | Apelido |
| --- | --- | --- |
| 1 |João |Silva |
| 2 |Joana |Silva |

Executar alguns inserções mais e ver que os obterem replicados oeste-na parte do cluster.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cluster de Cassandra de teste do Node.js
Utilizar uma das VMs Linux criados no "web" camada anteriormente, iremos irá executar um script de Node.js simple para ler os dados anteriormente inseridos

**Passo 1: Instalar o Node.js e Cassandra cliente**

1. Instalar Node.js e npm
2. Instalar o pacote "cassandra-cliente de nó" utilizando npm
3. Execute o seguinte script na linha de shell que apresenta a cadeia json dos dados obtidos:
   
        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };
   
        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }
   
        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }
   
        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);
   
           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }
   
        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }
   
        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }
   
        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)

## <a name="conclusion"></a>Conclusão
Microsoft Azure é uma plataforma flexível que permite a execução de ambos os Microsoft, bem como o software de código aberto, como é demonstrado neste exercício. Clusters de Cassandra elevadas podem ser implementados no Centro de dados único através do propagando-se os nós do cluster em vários domínios de falhas. Cassandra clusters também podem ser implementados em várias regiões do Azure, geograficamente distantes para sistemas de prova de desastre. Azure e Cassandra permite em conjunto da construção da altamente dimensionável e altamente disponíveis e serviços de cloud possível recuperar de desastres necessitam através da internet de hoje aumentar.  

## <a name="references"></a>Referências
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

