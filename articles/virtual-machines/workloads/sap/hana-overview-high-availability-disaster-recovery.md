---
title: "Elevada disponibilidade e recuperação após desastre de SAP HANA no Azure (instâncias de grande) | Microsoft Docs"
description: "Estabelecer a elevada disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias de grandes)"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09aa98a35fa8286828a99c49a33a80d5938afe3a
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA grande instâncias elevada disponibilidade e recuperação após desastre no Azure 

Elevada disponibilidade e recuperação após desastre (DR) são aspetos importantes do SAP HANA fundamentais em execução no servidor do Azure (instâncias de grande). É importante trabalhar com o SAP, sua integrador de sistema ou Microsoft corretamente architect e implementar a estratégia de recuperação após desastre e elevada disponibilidade à direita. Também é importante considerar o objetivo de ponto de recuperação (RPO) e o objetivo de tempo de recuperação, que são específicas para o seu ambiente.

Microsoft suporta algumas capacidades de elevada disponibilidade de SAP HANA com instâncias de grande HANA. Estas funcionalidades incluem:

- **Replicação de armazenamento**: capacidade do sistema de armazenamento de replicar todos os dados para outro instância grande HANA carimbo na outra região do Azure. SAP HANA funciona independentemente este método.
- **Replicação do sistema HANA**: A replicação de todos os dados no SAP HANA para um sistema de SAP HANA separado. O objetivo de tempo de recuperação é minimizado através da replicação de dados em intervalos regulares. SAP HANA suporta assíncronos, síncronos modos de memória e síncronos. Modo síncrono é recomendado apenas para os sistemas de SAP HANA que estão dentro do mesmo centro de dados ou inferior a 100 km, à excepção. Na estrutura de carimbos de grande instância HANA atual, a replicação do sistema HANA pode ser utilizada para elevada disponibilidade apenas. Atualmente, a replicação do sistema HANA requer um componente de proxy inverso de terceiros para configurações de recuperação de desastres para outra região do Azure. 
- **Auto-ativação pós-falha do anfitrião**: uma solução de recuperação-falha do local para SAP HANA utilizar como alternativa a replicação do sistema HANA. Se o nó principal ficar indisponível, configure um ou mais nós de SAP HANA espera no modo de escalamento horizontal e SAP HANA automaticamente falhar um nó em modo de espera.

SAP HANA no Azure (instâncias de grande) é fornecido em duas regiões do Azure que abrangem diferentes três de regiões geopolíticas (dos EUA, da Austrália e na Europa). Duas regiões diferentes que carimbos de data / instância grande HANA anfitrião está ligadas a circuitos de rede dedicado separadas que são utilizados para replicar os instantâneos de armazenamento para fornecer os métodos de recuperação após desastre. A replicação não for estabelecida por predefinição. É configurado para clientes que ordenadas a funcionalidade de recuperação após desastre. Replicação de armazenamento está dependente da utilização de instantâneos de armazenamento de instâncias de grande HANA. Não é possível escolher uma região do Azure como uma região DR numa área geopolítica diferentes. 

A tabela seguinte mostra os métodos atualmente suportados de elevada disponibilidade e recuperação após desastre e combinações de:

| Cenário suportado em instâncias de grande HANA | Opção de elevada disponibilidade | Opção de recuperação após desastre | Comentários |
| --- | --- | --- | --- |
| Nó único | Não está disponível. | Configuração de DR dedicada.<br /> Configuração de Multipurpose DR. | |
| Auto-ativação pós-falha do anfitrião: N + m<br /> incluindo 1 + 1 | Possível com o modo de espera colocar a função do Active Directory.<br /> HANA controla o comutador de função. | Configuração de DR dedicada.<br /> Configuração de Multipurpose DR.<br /> Sincronização de DR utilizando a replicação de armazenamento. | Conjuntos de volume HANA estão ligados a todos os nós (n + m).<br /> Site de DR tem de ter o mesmo número de nós. |
| Replicação do sistema HANA | Possível com a configuração primária ou secundária.<br /> Move secundário para a função primária em caso de ativação pós-falha.<br /> Ativação pós-falha do controlo de replicação do sistema HANA e SO. | Configuração de DR dedicada.<br /> Configuração de Multipurpose DR.<br /> Sincronização de DR utilizando a replicação de armazenamento.<br /> DR utilizando a replicação do sistema HANA ainda não é possível sem componentes de terceiros. | Conjunto separado dos volumes de discos anexados a cada nó.<br /> Apenas os volumes de disco de réplica secundária no site de produção são replicadas para a localização de DR.<br /> É necessário um conjunto de volumes no site de DR. | 

Uma configuração de DR dedicada é onde a unidade de instância grande HANA no site de DR não é utilizada para executar qualquer carga de trabalho ou sistema de não produção. A unidade é passiva e é implementada apenas se for executada uma ativação pós-falha após desastre. No entanto, isto não é uma opção preferencial para muitos clientes.

Uma configuração de DR multipurpose é onde a unidade de instância grande HANA no site de DR executa uma carga de trabalho de não produção. Em caso de desastre, encerrar o sistema de não produção, montar os conjuntos de armazenamento replicado volume (adicionais) e, em seguida, iniciar a instância HANA de produção. Maioria dos clientes que utilizam a funcionalidade de recuperação após desastre de instância grande HANA, utilize esta configuração. 


Pode encontrar mais informações sobre a disponibilidade elevada de SAP HANA nos seguintes artigos SAP: 

- [Documento técnico de elevada disponibilidade do SAP HANA](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo de SAP Academy na replicação do sistema de SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de suporte #1999880 – FAQ na replicação do sistema de SAP HANA de SAP](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP suporte Nota &#2165547; – SAP HANA novamente a cópia de segurança e restaurar o ambiente de SAP HANA sistema replicação](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP suporte Nota #1984882 – utilizando SAP HANA replicação do sistema para o Exchange do Hardware com o período de indisponibilidade mínimo/Zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para a recuperação de desastre com instâncias de grande HANA

Para tirar partido da funcionalidade de recuperação após desastre de instâncias de grande HANA, terá de conceber a conectividade de rede com dois diferentes regiões do Azure. Precisa de uma ligação de circuito ExpressRoute do Azure no local na sua região do Azure principal e outro circuito ligação no local para a região de recuperação após desastre. Esta medida abrange uma situação em que exista um problema na região do Azure, incluindo uma localização de Router de limite de Enterprise do Microsoft (MSEE).

Como uma segunda medida, pode ligar todas as redes virtuais do Azure que se ligam ao SAP HANA no Azure (instâncias de grande) de uma das regiões para um circuito de ExpressRoute liga HANA instâncias grande na outra região. Com esta *cruzada ligar*, os serviços executados numa Azure virtual network na região n. º 1, pode ligar às unidades de instância grande HANA na região n. º 2 e o inverso. Esta medida endereços um caso onde apenas uma das localizações MSEE que liga à sua localização no local com o Azure fica offline.

O gráfico seguinte ilustra uma configuração resiliente para recuperação após desastre:

![Configuração ideal para recuperação após desastre](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-when-you-use-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos quando utiliza a replicação de armazenamento de instâncias de grande HANA para recuperação após desastre

Requisitos adicionais para uma configuração de recuperação após desastre com instâncias de grande HANA são:

- Tem de ordem de SAP HANA no Azure (instâncias de grande) SKUs o mesmo tamanho como a produção SKUs e implementá-las na região de recuperação após desastre. Nas implementações de cliente atual, estas instâncias são utilizadas para executar instâncias HANA de não produção. Iremos consultá-las como *multipurpose DR setups*.   
- Deve ordenar armazenamento adicional no site de DR para cada um dos seus SAP HANA no SKUs (instâncias de grande) do Azure que pretende recuperar o site de recuperação após desastre. Comprar armazenamento adicional permite-lhe alocar os volumes de armazenamento. Pode alocar os volumes que são o destino de replicação de armazenamento da sua região do Azure de produção para a recuperação de desastres região do Azure.
 

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Um dos aspetos mais importantes para operar as bases de dados é para os proteger, marca de vários eventos catastrófica. A causa destes eventos pode ser qualquer coisa desde perante desastres naturais erros de utilizador simples.

Cópia de segurança das bases de dados, a capacidade de restaurá-lo para qualquer ponto no tempo (tal como alguém eliminar dados críticos), permite que o restauro para um Estado que é o mais parecido possível com a forma como foi antes da interrupção.

Dois tipos de cópias de segurança tem de ser efetuados para obter os melhores resultados:

- As cópias de segurança da base de dados: cópias de segurança completas, incrementais ou diferenciais
- Cópias de segurança do registo de transações

Para além das cópias de segurança de base de dados completo efetuadas ao nível de uma aplicação, pode efetuar cópias de segurança com instantâneos de armazenamento. Instantâneos de armazenamento não substituem as cópias de segurança do registo de transações. Cópias de segurança do registo de transações permanecem importantes para restaurar a base de dados até um certo ponto no tempo ou vazio os registos de transações consolidadas já. No entanto, os instantâneos de armazenamento podem acelerar a recuperação ao fornecer rapidamente uma imagem de reencaminhamento de agregação da base de dados. 

SAP HANA no Azure (instâncias de grande) oferece duas opções de cópia de segurança e restauro:

- Fazê-lo por si (DIY). Depois de calcular para garantir que existe espaço suficiente em disco, execute completa da base de dados e as cópias de segurança de registo utilizando métodos de cópia de segurança de disco. Pode criar cópias de segurança diretamente a volumes anexados às unidades instância grande HANA ou para partilhas NFS (Network File) configurar numa máquina virtual do Azure (VM). No caso desta última opção, os clientes configurar uma VM com Linux no Azure, anexar armazenamento do Azure para a VM em partilhem o armazenamento através de um servidor NFS configurado nessa VM. Se efetuar a cópia de segurança em volumes que ligue diretamente às unidades de instância grande HANA, terá de copiar as cópias de segurança para uma conta de armazenamento do Azure (depois de configurar uma VM do Azure que exporta as partilhas NFS baseiam-se no armazenamento do Azure). Ou pode utilizar um cofre de cópia de segurança do Azure ou o armazenamento de frio do Azure. 

   Outra opção consiste em utilizar uma ferramenta de proteção de dados de terceiros para armazenar as cópias de segurança após são copiados para uma conta de armazenamento do Azure. A opção de cópia de segurança DIY também poderá ser necessária para dados que precisa para armazenar por períodos mais longos de tempo para fins de auditorias e de conformidade. Em todos os casos, as cópias de segurança são copiadas para as partilhas NFS representadas através de uma VM e o armazenamento do Azure.

- Utilize a cópia de segurança e restaurar a funcionalidade que fornece a infraestrutura subjacente de SAP HANA no Azure (instâncias de grande). Esta opção satisfaz a necessidade de cópias de segurança e restauros rápidos. O resto desta secção aborda a funcionalidade de cópia de segurança e restauro é oferecida com instâncias de grande HANA. Esta secção também inclui a cópia de segurança de relação e tem de restaurar a recuperação após desastre funcionalidades oferecidas pelas instâncias grande HANA.

> [!NOTE]
> A tecnologia de instantâneo que é utilizada pela infraestrutura subjacente de instâncias de grande HANA tem uma dependência no instantâneos de SAP HANA. Neste momento, os instantâneos de SAP HANA não funcionam em conjunto com múltiplos inquilinos de contentores de base de dados multi-inquilino de SAP HANA. Assim, este método de cópia de segurança não pode ser utilizado quando implementa vários inquilinos nos contentores de base de dados multi-inquilino de SAP HANA. Se apenas estiver implementado um inquilino, funcionam instantâneos de SAP HANA.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utilizar instantâneos de armazenamento de SAP HANA no Azure (instâncias de grandes)

A infraestrutura de armazenamento subjacente SAP HANA no Azure (instâncias de grande) suporta instantâneos de armazenamento de volumes. Cópia de segurança e restauro de volumes é suportada com as seguintes considerações:

- Em vez de cópias de segurança da base de dados completo, são tirados instantâneos de volume de armazenamento com frequência.
- Quando um instantâneo a acionar sobre/hana/os dados, hana/registo e /hana/shared (inclui /usr/sap) volumes, o instantâneo de armazenamento inicia uma SAP HANA antes de ser executada o instantâneo de armazenamento de instantâneos. Este instantâneo de SAP HANA é o ponto de configuração para restorations eventual registo após a recuperação do instantâneo de armazenamento.
- Após o ponto em que o instantâneo de armazenamento foi executado com êxito, é eliminado o instantâneo de SAP HANA.
- Cópias de segurança do registo de transações efetuadas com frequência e são armazenadas no /hana/logbackups volume ou no Azure. Pode acionar o volume de /hana/logbackups que contém as cópias de segurança do registo de transações para criar um instantâneo separadamente. Nesse caso, não terá de executar um instantâneo HANA.
- Se tem de restaurar uma base de dados para um determinado ponto no tempo, pedido de suporte do Microsoft Azure (para uma falha de produção) ou SAP HANA na gestão de serviço do Azure para restaurar para um determinado instantâneo de armazenamento. Um exemplo é um restauro planeado de um sistema de sandbox para o seu estado original.
- O instantâneo de SAP HANA que está incluído no instantâneo de armazenamento é um ponto de deslocamento para aplicar as cópias de segurança de registo de transações que foram executadas e armazenadas após o armazenamento de instantâneo foi tirado.
- Estas cópias de segurança do registo de transações direcionadas para restaurar a base de dados para um determinado ponto no tempo.

Pode efetuar três diferentes classes de volumes de filtragem de instantâneos de armazenamento:

- Um instantâneo combinado através de dados/hana/e /hana/shared (inclui/usr/sap). Este instantâneo requer a criação de um instantâneo de SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo de SAP HANA será Certifique-se de que a base de dados está num estado consistente a partir de um ponto de vista do armazenamento.
- Um instantâneo separado através de hana/logbackups.
- Uma partição de SO (apenas para o tipo I de instâncias de grande HANA).


### <a name="storage-snapshot-considerations"></a>Considerações de instantâneos de armazenamento

>[!NOTE]
>Instantâneos de armazenamento consumam o espaço de armazenamento que foi atribuído para as unidades de instância grande HANA. Por conseguinte, terá da ter em consideração os seguintes aspetos de agendamento de instantâneos de armazenamento e o número de instantâneos de armazenamento para manter. 

Os mechanics específicos de instantâneos de armazenamento para SAP HANA no Azure (instâncias de grande) incluem:

- Um instantâneo de armazenamento específico (na altura quando é executada) consome armazenamento reduzida.
- Como as alterações de conteúdo de dados e o conteúdo de dados SAP HANA ficheiros alterados no volume de armazenamento, o instantâneo necessita de armazenar o conteúdo original do bloco, bem como as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. O já existe o instantâneo, fica do quanto maior for o instantâneo de armazenamento.
- As mais as alterações efetuadas ao volume de base de dados SAP HANA durante a duração de um instantâneo de armazenamento, a maior o consumo de espaço do instantâneo de armazenamento.

SAP HANA no Azure (instâncias de grande) inclui os tamanhos de volume fixo para os volumes de dados e de registo de SAP HANA. Efetuar instantâneos desses volumes eats no espaço de volume. Terá de determinar quando deve agendar instantâneos de armazenamento. Também precisa de monitorizar o consumo de espaço dos volumes de armazenamento, bem como gerir o número de instantâneos que armazena. Pode desativar os instantâneos de armazenamento quando importar masses de dados ou efetuar outras alterações significativas na base de dados HANA. 


As secções seguintes fornecem informações para efetuar estes instantâneos, incluindo recomendações gerais:

- Apesar do hardware pode suportar 255 instantâneos por volume, recomendamos vivamente que permaneça este número também abaixo.
- Antes de efetuar os instantâneos de armazenamento, monitorizar e manter o registo do espaço livre.
- Reduzi o número de instantâneos de armazenamento com base no espaço livre. Pode reduzir o número de instantâneos que mantenha ou, pode expandir os volumes. Pode ordenar o armazenamento adicional em unidades de 1 terabyte.
- Durante a atividades como mover dados para SAP HANA com ferramentas de migração de plataforma do SAP (R3load) ou o restauro de bases de dados SAP HANA de cópias de segurança, desative os instantâneos de armazenamento no /hana/data volume. 
- Durante a maior reorganizations das tabelas de SAP HANA, os instantâneos de armazenamento devem ser evitados, se possível.
- Os instantâneos de armazenamento são um pré-requisito para tirar partido das capacidades de recuperação após desastre de SAP HANA no Azure (instâncias de grande).

### <a name="setting-up-storage-snapshots"></a>Configurar os instantâneos de armazenamento

Os passos para configurar os instantâneos de armazenamento com instâncias de grande HANA são os seguintes:
1. Certifique-se de que o Perl está instalado no sistema operativo Linux no servidor de instâncias de grande HANA.
2. Modificar o etc/ssh/ssh\_configuração para adicionar a linha _MACs hmac-sha1_.
3. Crie uma conta de utilizador de cópia de segurança de SAP HANA no nó principal para cada instância de SAP HANA que estiver a executar, se aplicável.
4. Instale o cliente de SAP HANA HDB em todos os servidores de instâncias de grandes dimensões do SAP HANA.
5. No primeiro servidor de SAP HANA grande instâncias de cada região, crie uma chave pública para aceder a infraestrutura de armazenamento subjacente que controla a criação do instantâneo.
6. Copiar os scripts e um ficheiro de configuração da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) para a localização do **hdbsql** na instalação do SAP HANA.
7. Modificar o ficheiro de HANABackupDetails.txt conforme necessário para as especificações de cliente adequado.

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passo 1: Instalar o cliente de SAP HANA HDB

O sistema de operativo Linux instalado SAP HANA no Azure (instâncias de grande) inclui as pastas e os scripts necessários para executar os instantâneos de armazenamento de SAP HANA para fins de cópia de segurança e recuperação após desastre. Verifique a existência de versões mais recentes no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). A versão mais recente dos scripts é 2.1.
No entanto, é da responsabilidade do cliente para instalar o cliente de SAP HANA HDB das unidades de instância grande HANA enquanto estiver a instalar o SAP HANA. (Microsoft não instalar o cliente HDB ou SAP HANA.)

### <a name="step-2-change-the-etcsshsshconfig"></a>Passo 2: Altere o etc/ssh/ssh\_configuração

Alteração `/etc/ssh/ssh_config` adicionando o _MACs hmac-sha1_ linha conforme mostrado aqui:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Passo 3: Criar uma chave pública

Para ativar o acesso para as interfaces instantâneos de armazenamento do seu inquilino de instância grande HANA, tem de estabelecer um início de sessão através de uma chave pública. No primeiro SAP HANA no servidor do Azure (instâncias de grande) no seu inquilino, crie uma chave pública a utilizar para aceder à infraestrutura de armazenamento para que possa criar instantâneos. A chave pública garante que uma palavra-passe não é necessário para iniciar sessão para as interfaces instantâneos de armazenamento. Criar uma chave pública também significa que não é necessário manter as credenciais de palavra-passe. No Linux no servidor de SAP HANA grande instâncias, execute o seguinte comando para gerar a chave pública:
```
  ssh-keygen –t dsa –b 1024
```
A nova localização é **_/root/.ssh/id\_dsa.pub**. Não introduza uma palavra-passe real; caso contrário, tem de introduzir a palavra-passe sempre que iniciar sessão. Em vez disso, selecione **Enter** duas vezes para remover o requisito de "Introduza a palavra-passe" para iniciar sessão.

Certifique-se de que a chave pública foi corrigida conforme esperado, alterando as pastas para **/root/.ssh/** e, em seguida, executar o `ls` comando. Se a chave estiver presente, pode copiá-lo executando o seguinte comando:

![A chave pública é copiada por executar este comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Neste momento, contacte SAP HANA na gestão de serviço do Azure e forneça-las com a chave pública. O representante de serviço utiliza a chave pública para registá-lo na infraestrutura de armazenamento subjacente que retirar para o seu inquilino de instância grande HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Passo 4: Criar uma conta de utilizador de SAP HANA

Para iniciar a criação de instantâneos de SAP HANA, terá de criar uma conta de utilizador no SAP HANA que podem utilizar os scripts de instantâneos de armazenamento. Crie uma conta de utilizador de SAP HANA SAP HANA Studio para esta finalidade. Esta conta tem de ter os seguintes privilégios: **Admin de cópia de segurança** e **catálogo leitura**. Neste exemplo, o nome de utilizador é **SCADMIN**. O nome da conta de utilizador criado no HANA Studio diferencia maiúsculas de minúsculas. Certifique-se de que seleciona **não** para exigir ao utilizador alterar a palavra-passe o próximo início de sessão.

![Criar um utilizador no HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passo 5: Autorizar a conta de utilizador de SAP HANA

Neste passo, está a autorizar a conta de utilizador de SAP HANA que criou, para que os scripts não precisam de envio de palavras-passe no tempo de execução. O comando de SAP HANA `hdbuserstore` permite a criação de uma chave de utilizador de SAP HANA, que é armazenada num ou mais nós de SAP HANA. A chave de utilizador permite ao utilizador acesso SAP HANA sem ter de gerir palavras-passe no processo do script. O processo de script é abordado mais tarde.

>[!IMPORTANT]
>Execute o seguinte comando como `root`. Caso contrário, o script não funciona corretamente.

Introduza o `hdbuserstore` comando da seguinte forma:

**Para a configuração não MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Para a configuração de MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

No exemplo seguinte, o utilizador é **SCADMIN01**, o nome do anfitrião é **lhanad01**, e é o número de instância **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Se tiver uma configuração de escalamento horizontal de SAP HANA, deve gerir todos os scripts de um único servidor. Neste exemplo, a chave de SAP HANA **SCADMIN01** tem de ser alterado para cada anfitrião de uma forma que reflete o anfitrião está relacionado com a chave. Corrigir a conta de cópia de segurança de SAP HANA com o número de instância de BD HANA. A chave tem de ter privilégios administrativos no anfitrião que está atribuído à e o utilizador de cópia de segurança para configurações de escalamento horizontal tem de ter direitos de acesso a todas as instâncias de SAP HANA. Partindo do princípio de três nós de escalamento horizontal tem os nomes **lhanad01**, **lhanad02**, e **lhanad03**, a sequência de comandos tem o seguinte aspeto:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passo 6: Obter os scripts de instantâneo, os instantâneos de configurar e testar a configuração e conectividade

Transferir a versão mais recente dos scripts de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Copiar os scripts transferidos e o ficheiro de texto para o directório de trabalho **hdbsql**. Para instalações de HANA atuais, este diretório é como /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl 
HANABackupCustomerDetails.txt 
``` 


Eis o objetivo das diferentes scripts e ficheiros protegidos:

- **Azure\_hana\_backup.pl**: agendar este script de cron para executar os instantâneos de armazenamento em volumes de dados/registo/partilhados HANA, o volume de logbackups/hana/ou o SO (no tipo I SKUs de HANA grande instâncias).
- **Azure\_hana\_replicação\_status.pl**: este script fornece os detalhes básicos em todo o estado de replicação do site de produção para o site de recuperação após desastre. Os monitores de script para se certificar de que a replicação está a decorrer e mostra o tamanho dos itens que estão a ser replicados. Também fornece orientações se uma replicação estiver a demorar demasiado tempo ou se a ligação está inativo.
- **Azure\_hana\_instantâneo\_details.pl**: este script fornece uma lista de detalhes básicos sobre todos os instantâneos, por volume, o que existe no seu ambiente. Este script pode ser executado no servidor primário ou numa unidade do servidor de localização de recuperação após desastre. O script fornece as seguintes informações reduzidas por cada volume que contém os instantâneos:
   * Tamanho de instantâneos totais um volume
   * Cada instantâneo nesse volume inclui os seguintes detalhes: 
      - Nome de instantâneo 
      - Tempo de criação 
      - Tamanho do instantâneo
      - Frequência de instantâneo
      - ID de cópia de segurança de HANA associado nesse instantâneo, se forem relevantes
- **Azure\_hana\_instantâneo\_delete.pl**: este script elimina um instantâneo de armazenamento ou um conjunto de instantâneos. Pode utilizar o ID de cópia de segurança de SAP HANA como encontrada no HANA Studio ou o nome de instantâneos de armazenamento. Atualmente, o ID de cópia de segurança só está associado aos instantâneos criados para os volumes de dados/registo/partilhados HANA. Caso contrário, se o ID de instantâneo é introduzido,-procura todos os instantâneos que correspondem o ID de instantâneo introduzido.  
- **testHANAConnection.pl**: este script testa a ligação à instância do SAP HANA e é necessário para configurar os instantâneos de armazenamento.
- **testStorageSnapshotConnection.pl**: este script tem duas finalidades. Em primeiro lugar, assegura que a unidade de instância grande HANA que executa os scripts tem acesso para a máquina virtual de armazenamento atribuído e a interface de instantâneos de armazenamento de instâncias de grande sua HANA. O segundo objetivo consiste em criar um instantâneo temporário para a instância HANA que estiver a testar. Este script deve ser executado para todas as instâncias HANA num servidor para garantir que os scripts de cópia de segurança função conforme esperado.
- **removeTestStorageSnapshot.pl**: este script elimina o teste de instantâneos, criado com o script **testStorageSnapshotConnection.pl**. 
- **HANABackupCustomerDetails.txt**: este ficheiro é um ficheiro de configuração modificável que terá de modificar para adaptar à sua configuração de SAP HANA.

 
O ficheiro de HANABackupCustomerDetails.txt é o ficheiro de controlo e a configuração para o script que executa os instantâneos de armazenamento. Ajuste o ficheiro para os fins e a configuração. Deve ter recebido o **nome de cópia de segurança de armazenamento** e **endereço de IP de armazenamento** de SAP HANA na gestão de serviço do Azure quando as instâncias que foram implementadas. Não é possível modificar a sequência de ordenação ou espaçamento de qualquer uma das variáveis existentes neste ficheiro. Caso contrário, os scripts não vai ser executado corretamente. Além disso, recebido o endereço IP do nó de vertical ou o nó mestre (se escalável) de SAP HANA na gestão de serviço do Azure. Também souber o número de instância HANA que recebeu durante a instalação de SAP HANA. Agora tem de adicionar um nome de cópia de segurança para o ficheiro de configuração.

Para uma implementação de vertical ou Escalamento horizontal, o ficheiro de configuração seria ter um aspeto semelhante ao seguinte exemplo depois preenchidos o nome de cópia de segurança de armazenamento e o endereço IP de armazenamento. Também terá de preencher os seguintes dados no ficheiro de configuração:
- Nó único ou endereço IP do nó principal
- Número de instância HANA
- Nome da cópia de segurança 
    
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA userstore Name:
```

>[!NOTE]
>Atualmente, apenas os detalhes de 1 de nó são utilizados no script de instantâneos de armazenamento HANA real. Recomendamos que teste o acesso de ou para todos os nós HANA para que, se alguma vez altera o nó mestre de cópia de segurança, já certificar-se de que qualquer outro nó pode ocorrer respetivo modificando os detalhes no nó 1.

Depois de colocar todos os dados de configuração no ficheiro HANABackupCustomerDetails.txt, terá de verificar se as configurações estão corretas sobre os dados da instância HANA. Utilize o script `testHANAConnection.pl`. Este script é independente de uma configuração de vertical ou Escalamento horizontal de SAP HANA.

```
testHANAConnection.pl
```

Se tiver uma configuração de escalamento horizontal de SAP HANA, certifique-se de que a instância HANA mestre tem acesso a todos os servidores HANA e instâncias necessária. Existem não existem parâmetros para o script de teste, mas tem de adicionar os dados para o ficheiro de configuração HANABackupCustomerDetails.txt para o script a executar corretamente. Apenas os códigos de erro de comando shell são devolvidos, pelo que não é possível para o script para erro Verifique todas as instâncias. Ainda assim, o script comentários algumas úteis para que possa verificar.

Para executar o script, introduza o seguinte comando:
```
 ./testHANAConnection.pl
```
Se o script com êxito obtém o estado da instância HANA, é apresentada uma mensagem que a ligação de HANA teve êxito.


É o seguinte passo de teste para verificar a conectividade com o armazenamento com base nos dados colocados o ficheiro de configuração HANABackupCustomerDetails.txt e, em seguida, executar um instantâneo de teste. Antes de executar o `azure_hana_backup.pl` script, tem de executar este teste. Se um volume que contém não existem instantâneos, é impossível determinar se o volume está vazio ou se houver uma falha SSH para obter os detalhes de instantâneo. Por este motivo, o script executa dois passos:

- Verifica que a máquina virtual de armazenamento e interfaces do inquilino estão acessíveis para os scripts de instantâneos de execução.
- Cria um instantâneo de teste ou fictício, para cada volume por instância HANA.

Por este motivo, a instância HANA é incluída como um argumento. Se falhar a execução, não é possível fornecer o erro de verificação para a ligação de armazenamento. Mesmo se não houver nenhum erro de verificação, o script fornece sugestões úteis.

O script é executado como:
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
Em seguida, o script tenta iniciar sessão para o armazenamento utilizando a chave pública fornecida nos passos de configuração anterior e com os dados configurados no ficheiro HANABackupCustomerDetails.txt. Se o início de sessão for bem-sucedida, é apresentado o seguinte conteúdo:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Se ocorrer ao ligar à consola do armazenamento problemas, a saída este aspeto:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Após um bem-sucedida início de sessão para as interfaces de máquina virtual de armazenamento, o script continua com fase #2 e cria um instantâneo de teste. O resultado é apresentado aqui para uma configuração de escalamento horizontal de três nós de SAP HANA:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Se o instantâneo de teste foi executado com êxito com o script, pode avançar com a configuração de instantâneos de armazenamento real. Se não for bem sucedida, investigue os problemas antes de avançar diretamente. O instantâneo de teste deve permanecer em torno até que os instantâneos reais primeiro terminados.


### <a name="step-7-perform-snapshots"></a>Passo 7: Efetuar instantâneos

Como todos os passos de preparação estiverem concluídos, pode começar a configurar a configuração de instantâneos de armazenamento real. O script a ser agendada funciona com configurações de vertical e horizontal de SAP HANA. Deve agendar a execução de scripts através de cron. 

Podem ser criados três tipos de cópias de segurança do instantâneo:
- **HANA**: combinado de cópia de segurança de instantâneos em que os volumes que contêm dados/hana/e hana/partilhados (que contém /usr/sap bem) estão abrangidas pelo instantâneo coordenado. Um restauro de ficheiro único é possível este instantâneo.
- **Os registos**: cópia de segurança do instantâneo do volume de logbackups/hana /. Nenhum instantâneo HANA é acionado para executar este instantâneo de armazenamento. Este volume de armazenamento é o volume que deveria conter as cópias de segurança do registo de transações de SAP HANA. São efetuadas cópias de segurança de registo de transações de SAP HANA com mais frequência para restringir o crescimento do registo e de impedir a potencial perda de dados. Um restauro de ficheiro único é possível este instantâneo. Não deve reduzir a frequência em cinco minutos.
- **Arranque**: instantâneo do volume que contém o número de unidade lógica de arranque (LUN) da instância de grande HANA. Esta cópia de segurança do instantâneo é possível apenas com o tipo I SKUs de HANA grande instâncias. Ficheiro único não é possível efetuar restauros do instantâneo do volume que contém o LUN de arranque. Para o tipo II SKUs de HANA grande instâncias, pode tirar o nível de SO cópia de segurança e restaurar os ficheiros de individuais. Consulte o documento "[como executar SO cópia de segurança para o tipo II SKUs](os-backup-type-ii-skus.md)" para obter mais detalhes.


A sintaxe de chamada para estes três tipos diferentes de instantâneos tem o seguinte aspeto:
```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot none manual 30

```

É necessário especificar os parâmetros seguintes:

- O primeiro parâmetro caracteriza o tipo de cópia de segurança do instantâneo. Os valores permitidos são **hana**, **registos**, e **arranque**. 
- O segundo parâmetro é **HANA SID** (como HM3) ou **nenhum**. Se o primeiro valor de parâmetros fornecidos é **hana** ou **registos**, em seguida, o valor deste parâmetro é **HANA SID** (como HM3), ou para cópia de segurança do arranque volume, o valor é **nenhum**. 
- O terceiro parâmetro é um instantâneo ou uma etiqueta de cópia de segurança para o tipo de instantâneo. Tem duas finalidades. O uma objetivo por si é atribuir-lhe um nome, para que saiba quais estes instantâneos são sobre. O objetivo da segundo destina-se do azure de script\_hana\_backup.pl para determinar o número de instantâneos de armazenamento que são mantidos nessa etiqueta específica. Se agendar duas cópias de instantâneos de armazenamento do mesmo tipo (como **hana**), com duas etiquetas diferentes e definir que devem ser mantidos 30 instantâneos para cada um, vai ficar com 60 instantâneos de armazenamento dos volumes afetados. 
- O parâmetro quarto define a retenção instantâneos indiretamente, ao definir o número de instantâneos de com o mesmo prefixo de instantâneo (etiqueta) que serão guardadas. Este parâmetro é importante para uma execução agendada através de cron. 

No caso de escalamento horizontal, o script efetua algumas adicionais de verificação para se certificar de que pode aceder a todos os servidores do HANA. O script também verifica que todas as instâncias HANA o adequado Estado devolvem de instâncias, antes de cria um instantâneo de SAP HANA. O instantâneo de SAP HANA é seguido de um instantâneo de armazenamento.

A execução do script `azure_hana_backup.pl` cria o armazenamento de instantâneos no seguinte três fases distintas:

1. Executa um instantâneo de SAP HANA
2. Executa um instantâneo de armazenamento
3. Remove o instantâneo de SAP HANA que foi criado antes da execução do instantâneo de armazenamento

Ao executar o script, efetua a chamada da pasta executável HDB que foi copiado para. 

O período de retenção é administrado com o número de instantâneos são submetidos como um parâmetro ao executar o script (tais como **30**, apresentado anteriormente). Por isso, a quantidade de tempo que é abrangido pelos instantâneos de armazenamento é uma função de duas coisas: o período de execução e o número de instantâneos submetida como um parâmetro ao executar o script. Se o número de instantâneos são mantidos excede o número que são designados como um parâmetro na chamada do script, o instantâneo mais antigo do armazenamento da etiqueta do mesmo (no nosso caso anterior, **manual**) são eliminados antes de um novo instantâneo é executado. O número está a conceder como o último parâmetro da chamada é o número pode utilizar para controlar o número de instantâneos são mantidos. Com este número, pode também controlar, indiretamente, o espaço em disco utilizado para instantâneos. 

> [!NOTE]
>Alterar a etiqueta, assim que a contagem começa novamente. Isto significa que tem de ser strict no etiquetagem para que o seu instantâneos não são eliminados acidentalmente.

### <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência de instantâneos para os diferentes tipos depende se utilizar a funcionalidade de recuperação após desastre de instância grande HANA ou não. A funcionalidade de recuperação após desastre de instâncias de grande HANA depende de instantâneos de armazenamento. Depender de instantâneos de armazenamento pode necessitar de algumas recomendações especiais em termos dos períodos de frequência e a execução de instantâneos de armazenamento. 

Considerações e recomendações que se seguem, partimos do princípio que proceda *não* utilizar a funcionalidade de recuperação após desastre instâncias grande HANA oferece. Em alternativa, utilize os instantâneos de armazenamento como uma forma de ter cópias de segurança e ser capaz de fornecer a recuperação do ponto no tempo nos últimos 30 dias. Tendo em conta as limitações do número de instantâneos e o espaço, os clientes têm considera os seguintes requisitos:

- O tempo de recuperação para a recuperação do ponto no tempo.
- O espaço utilizado.
- O ponto de recuperação objetivo e o objetivo de tempo de recuperação para potenciais recuperação após desastre.
- A execução de uma eventual de cópias de segurança de base de dados completo HANA contra discos. Sempre que uma cópia de segurança da base de dados completo contra discos ou **backint** interface é executada, a execução de instantâneos de armazenamento falha. Se planear executar cópias de segurança de base de dados completo por cima de instantâneos de armazenamento, certifique-se de que a execução de instantâneos de armazenamento está desativada durante este período.
- O número de instantâneos por volume está limitado a 255.


Para os clientes que não utilizem a funcionalidade de recuperação após desastre de instâncias de grande HANA, o período de instantâneo é menos frequente. Nestes casos, os clientes a executar os instantâneos combinados /hana/data e /hana/shared (inclui /usr/sap) em períodos de hora 12 ou 24 horas, vemos e mantenha os instantâneos abranger um mês completo. O mesmo acontece com os instantâneos de volume de cópia de segurança de registo. No entanto, a execução de cópias de segurança de registo de transações de SAP HANA contra o volume de cópia de segurança do registo ocorre em 5 minutos para períodos de 15 minutos.

Aconselhamo-lo para efetuar instantâneos de armazenamento agendadas utilizando cron. Também recomendamos que utilize o mesmo script para todas as cópias de segurança e recuperação após desastre, precisa. Tem de modificar o script entradas para corresponder a vários tempos de cópia de segurança de pedido. Estes instantâneos são todos agendados forma diferente no cron, dependendo do seu tempo de execução: horária, 12 horas, diária ou semanal. 

Um exemplo de uma agenda de cron no /etc/crontab poderá ter o seguinte aspeto:
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
No exemplo anterior, é um instantâneo combinado de hora a hora que abrange os volumes que contêm dados/hana/e /hana/shared (inclui/usr/sap) localizações. Este tipo de instantâneo iria ser utilizado para uma recuperação de ponto no tempo mais rápida nos últimos dois dias. Além disso, não há um instantâneo diário nesses volumes. Por isso, tem dois dias de cobertura por instantâneos horárias, mais de quatro semanas de cobertura por instantâneos diários. Além disso, o volume de cópia de segurança do registo de transações de cópias de segurança uma vez para cada dia. Estas cópias de segurança são mantidas durante quatro semanas bem. Como ver na terceira linha de crontab, a cópia de segurança do registo de transação de HANA é agendada para execução a cada cinco minutos. Os minutos de início das tarefas de cron diferente que execute instantâneos de armazenamento são escalonados, para que os instantâneos não são executados ao mesmo tempo num determinado ponto no tempo. 

No exemplo seguinte, efetuar um instantâneo combinado que abrange os volumes que contêm as/hana/dados hana/partilhados (incluindo/usr/sap) localizações e numa base horária. Manter estes instantâneos para dois dias. Os instantâneos dos volumes de cópia de segurança de registo de transações são executados numa base de cinco minutos e são mantidos durante quatro horas. Como anteriormente, a cópia de segurança do ficheiro de registo de transações HANA é agendada para execução a cada cinco minutos. O instantâneo do volume de cópia de segurança de registo de transações é executado com um atraso de dois minutos após a cópia de segurança do registo de transações foi iniciado. Dentro desses dois minutos, a cópia de segurança do registo de transações de SAP HANA deve ser concluída em circunstâncias normais. Como anteriormente, o volume que contém o arranque LUN é feito uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

O gráfico seguinte ilustra as sequências de tarefas do exemplo anterior, excluindo o LUN de arranque:

![Relação entre as cópias de segurança e instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA efetua escritas regulares contra o volume de /hana/log documentar as alterações consolidadas na base de dados. Regularmente, SAP HANA escreve um ponto de reposição de volume /hana/data. Conforme especificado no crontab, uma cópia de segurança do registo de transações de SAP HANA é executada a cada cinco minutos. Também pode ver que um instantâneo de SAP HANA é executado devido a acionar um instantâneo de armazenamento combinado sobre os volumes /hana/data e /hana/shared a cada hora. Depois do instantâneo HANA for bem sucedida, o instantâneo de armazenamento combinado é executado. As instruções na crontab, o instantâneo de armazenamento no /hana/logbackup volume é executado de cada cinco minutos, cerca de dois minutos após a cópia de segurança do registo de transações de HANA.


>[!IMPORTANT]
> A utilização de instantâneos de armazenamento para cópias de segurança de SAP HANA é útil apenas quando os instantâneos são efetuados em conjunto com cópias de segurança de registo de transações de SAP HANA. Estas cópias de segurança do registo de transações tem de ser capaz de forma a cobrir os períodos de tempo entre os instantâneos de armazenamento. 

Se tiver configurado um compromisso aos utilizadores de uma recuperação do ponto no tempo de 30 dias, efetue o seguinte:

- Em casos extremos, terá da capacidade de aceder um armazenamento combinado over/hana/dados de instantâneos e /hana/shared que é de 30 dias.
- As cópias de segurança de registo de transações contíguo que abrangem o tempo entre a quaisquer instantâneos de armazenamento combinado de ter. Por isso, o instantâneo mais antigo do volume de cópia de segurança de registo de transações tem 30 dias. Não é o caso se copiar as cópias de segurança do registo de transações para outra partilha NFS que está localizada no armazenamento do Azure. Nesse caso, poderá solicitar cópias de segurança de registo de transações antigas do que a partilha NFS.

Para beneficiar de instantâneos de armazenamento e a replicação de armazenamento eventual de cópias de segurança do registo de transações, terá de alterar a localização que o SAP HANA escreve as registo de transações cópias de segurança. Pode efetuar esta alteração no HANA Studio. Apesar de SAP HANA efetua cópias de segurança segmentos de registo completo automaticamente, deve especificar um intervalo de cópia de segurança do registo ser determinística. Isto é especialmente verdadeiro quando utilizar a opção de recuperação após desastre, porque, normalmente, pretende executar cópias de segurança de registos com um período de determinista. No caso de seguinte, vamos demorou 15 minutos como o intervalo de cópia de segurança do registo.

![Agendar cópia de segurança de SAP HANA regista no SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Pode escolher as cópias de segurança que são mais frequentes do que 15 em 15 minutos. Isto é frequentemente Feito em conjunto com a recuperação de desastre. Alguns clientes efetuar cópias de segurança do registo de transações a cada cinco minutos.  

Se a base de dados nunca foi efetuada cópia de, é o último passo efetuar uma cópia de segurança da base de dados baseada em ficheiros para criar uma única entrada de cópia de segurança que tem de existir no catálogo de cópias de segurança. Caso contrário, SAP HANA não é possível iniciar as cópias de segurança de registo especificado.

![Faça uma cópia de segurança baseada em ficheiros para criar uma única entrada de cópia de segurança](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Depois de tem foi executados o seu primeiro instantâneos de armazenamento com êxito, também pode eliminar o instantâneo de teste foi executado no passo 6. Para tal, execute o script `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>O número e tamanho de instantâneos no volume de disco de monitorização

No volume de armazenamento específico, pode monitorizar o número de instantâneos e o consumo de armazenamento dos instantâneos. O `ls` comandos não mostram o directório de instantâneo ou os ficheiros. No entanto, o comando de SO Linux `du` mostra detalhes sobre os instantâneos de armazenamento, porque são armazenados nos volumes a mesmos. O comando pode ser utilizado com as seguintes opções:

- `du –sh .snapshot`: Fornece um total de todos os instantâneos dentro do diretório de instantâneo.
- `du –sh --max-depth=1`: Apresenta uma lista de todos os instantâneos são guardados no **.snapshot** pasta e o tamanho de cada instantâneo.
- `du –hc`: Fornece o tamanho total utilizado por todos os instantâneos.

Utilize estes comandos para se certificar de que os instantâneos que são executados e armazenados não estão a consumir todas as armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de arranque não estão visíveis com os comandos anteriores.

### <a name="getting-details-of-snapshots"></a>A obter os detalhes de instantâneos
Para obter mais detalhes em instantâneos, também pode utilizar o script `azure_hana_snapshot_details.pl`. Este script pode ser executado em qualquer localização, se existir um servidor ativo na localização de recuperação após desastre. O script fornece o seguinte resultado, discriminado segundo cada volume que contém os instantâneos: 
   * Tamanho de instantâneos totais um volume
   * Cada instantâneo nesse volume inclui os seguintes detalhes: 
      - Nome de instantâneo 
      - Tempo de criação 
      - Tamanho do instantâneo
      - Frequência de instantâneo
      - ID de cópia de segurança de HANA associado nesse instantâneo, se forem relevantes

A sintaxe de execução do script tem o seguinte aspeto:

```
./azure_hana_snapshot_details.pl 
```

Porque o script tenta obter o ID de cópia de segurança HANA, tem de ligar à instância do SAP HANA. Esta ligação requer o ficheiro de configuração HANABackupCustomerDetails.txt estar configurada corretamente. Uma saída de dois instantâneos num volume poderá ter o seguinte aspeto:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>Restauro ao nível dos ficheiros de um instantâneo de armazenamento
Para o instantâneo tipos hana e os registos, é capazes de aceder aos instantâneos diretamente nos volumes no **.snapshot** diretório. Não há um subdiretório para cada um dos instantâneos. Deve ser capaz de copiar cada ficheiro que é abrangido pelo instantâneo no Estado tinha at the point of o instantâneo do que subdiretório para a estrutura de diretórios real.

>[!NOTE]
>Ficheiro único restauro não funciona para instantâneos do LUN de arranque. O **.snapshot** diretórios não está exposto no LUN de arranque. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos, num servidor

Tal como explicado anteriormente, pode reduzir o número de determinados etiquetas de instantâneos que armazena. Os últimos dois parâmetros do comando para iniciar um instantâneo são a etiqueta e o número de instantâneos que pretenda manter.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

No exemplo anterior, a etiqueta de instantâneo é **cliente** e é o número de instantâneos com esta etiqueta deve ser retida **30**. Como responder para consumo de espaço em disco, pode querer reduzir o número de instantâneos armazenados. É a forma mais fácil de reduzir o número de instantâneos para 15, por exemplo, para executar o script com o último parâmetro definido como **15**:

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

Se executar o script com esta definição, o número de instantâneos, incluindo o instantâneo de armazenamento novo, é 15. 15 mais recentes instantâneos são mantidos, enquanto que os 15 instantâneos mais antigos são eliminados.

 >[!NOTE]
 > Este script reduz o número de instantâneos só se existirem instantâneos que são mais de um horas depois. O script não eliminar instantâneos são inferior a um horas depois. Estas restrições relacionadas com a funcionalidade de recuperação de desastres opcional fornecida.

Se já não pretender manter um conjunto de instantâneos com uma etiqueta de cópia de segurança específica **hanadaily** nos exemplos de sintaxe, pode executar o script com **0** como o número de retenção. Esta ação remove todos os instantâneos correspondente essa etiqueta. No entanto, a remoção de todos os instantâneos pode afetar as capacidades de recuperação após desastre.

Uma segundo possibilidade para eliminar instantâneos específicos consiste em utilizar o script `azure_hana_snapshot_delete.pl`. Este script foi concebido para eliminar um instantâneo ou um conjunto de instantâneos ou ao utilizar o ID de cópia de segurança HANA como encontrada no HANA Studio ou através de instantâneos nome próprio. Atualmente, o ID de cópia de segurança só está associado aos instantâneos criados para o **hana** tipo de instantâneos. As cópias de segurança do tipo de instantâneos **registos** e **arranque** não efetuar um instantâneo de SAP HANA. Por conseguinte, não há nenhum ID de cópia de segurança para localizar os instantâneos. Se o nome de instantâneo é introduzido, procura todos os instantâneos em volumes diferentes que corresponde ao nome do instantâneo introduzido. A sintaxe de chamada do script é:

```
./azure_hana_snapshot_delete.pl 

```

Execute o script como utilizador **raiz**.

Se selecionar um instantâneo, ter a capacidade de eliminar cada instantâneo individualmente. Primeiro a fornecer o volume que contém o instantâneo e, em seguida, forneça o nome do instantâneo. Se o instantâneo existe esse volume e é mais de uma hora antigo, é eliminado. Pode encontrar os nomes de volume e nomes de instantâneo executando o `azure_hana_snapshot_details` script. 

>[!IMPORTANT]
>Se houver dados que só existe no instantâneo que está a eliminar, em seguida, se executar a eliminação, os dados são perdidos indefinidamente.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo HANA mais recente

Se ocorrer um cenário de produção pendente, o processo de recuperação a partir de um instantâneo de armazenamento pode ser iniciado como um incidente de cliente com o suporte do Microsoft Azure. É um fim de urgência alta se dados foi eliminados um sistema de produção e é a única forma de obter os dados para restaurar a base de dados de produção.

Numa situação diferentes, uma ponto no tempo recuperação poderá ser urgência baixa e planeados com antecedência dias. Pode planear a recuperação com SAP HANA na gestão de serviço do Azure em vez de gerar um problema de alta prioridade. Por exemplo, poderá ser planeamento atualizar o software SAP através da aplicação de um novo pacote de melhoramento. Em seguida, terá de reverter para um instantâneo que representa o estado antes da atualização do pacote de melhoramento.

Antes de enviar o pedido, terá de preparar. O SAP HANA na equipa de gestão de serviço do Azure, em seguida, pode processar o pedido e fornecer os volumes restaurados. Posteriormente, restaure a base de dados HANA com base nos instantâneos. Eis como preparar o pedido:

>[!NOTE]
>A interface de utilizador pode variar das capturas de ecrã seguintes, consoante a versão de SAP HANA que está a utilizar.

1. Decida qual instantâneo para restaurar. Apenas o volume de dados/hana é restaurado a menos que instruir; caso contrário. 

2. Encerre a instância HANA.

 ![Encerre a instância HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte os volumes de dados em cada nó de base de dados HANA. Se os volumes de dados ainda são instalados no sistema operativo, o restauro do instantâneo falha.
 ![Desmontar os volumes de dados em cada nó de base de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra um pedido de suporte do Azure para instrui-los sobre o restauro de um instantâneo específico.

 - Durante o restauro: SAP HANA na gestão do serviço Azure poderá pedir que aguardem uma chamada de conferência para Certifique-se coordenação, a verificação e a confirmação de que o instantâneo de armazenamento correta é restaurado. 

 - Após o restauro: SAP HANA na gestão de serviço do Azure notifica-o quando o instantâneo de armazenamento ter sido restaurado.

5. Depois do processo de restauro está concluído, remontar todos os volumes de dados.

 ![Remontar todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Selecione as opções de recuperação SAP HANA Studio, se não automaticamente entram cópias de segurança quando voltar a ligar à base de dados de HANA através de SAP HANA Studio. O exemplo seguinte mostra um restauro para o último instantâneo HANA. Um instantâneo de armazenamento o atacante incorpora um instantâneo HANA. Se restaurar o instantâneo de armazenamento mais recente, deve ser o instantâneo HANA mais recente. (Se restaurar um instantâneo mais antigo do armazenamento, tem de localizar o instantâneo HANA com base na hora que o armazenamento de instantâneo foi tirado.)

 ![Selecione as opções de recuperação SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Selecione **recuperar a base de dados para um instantâneo de cópia de segurança ou de armazenamento de dados específicos**.

 ![A janela de especificar o tipo de recuperação](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Selecione **especificar cópia de segurança sem catálogo**.

 ![A janela de especificar localização de cópia de segurança](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. No **tipo de destino** lista, selecione **instantâneo**.

 ![A especificar cópia de segurança a janela de recuperação](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Selecione **concluir** para iniciar o processo de recuperação.

 ![Selecione "Concluir" para iniciar o processo de recuperação](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. A base de dados HANA é restaurada e recuperada para o instantâneo HANA que está incluído no instantâneo de armazenamento.

 ![Base de dados HANA é restaurada e recuperada para o instantâneo HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Recuperar para o estado mais recente

O seguinte processo restaura o instantâneo HANA que está incluído no instantâneo de armazenamento. Em seguida, restaura as cópias de segurança do registo de transações para o estado mais recente da base de dados antes de restaurar o instantâneo de armazenamento.

>[!IMPORTANT]
>Antes de continuar, certifique-se de que tem uma cadeia completa e contígua de cópias de segurança do registo de transações. Sem estas cópias de segurança, não é possível restaurar o estado atual da base de dados.

1. Conclua os passos 1-6 da [recuperar para o instantâneo HANA mais recente](#recovering-to-the-most-recent-hana-snapshot).

2. Selecione **recuperar a base de dados para o estado mais recente**.

 ![Selecionar "Recuperar a base de dados para o estado mais recente"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Especifique a localização das cópias de segurança do registo HANA mais recentes. A localização tem de conter todas HANA registo de transações cópias de segurança do instantâneo HANA para o estado mais recente.

 ![Especifique a localização das cópias de segurança do registo HANA mais recentes](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Selecione uma cópia de segurança como uma base a partir do qual recuperar a base de dados. No nosso exemplo, o instantâneo HANA na captura de ecrã é o instantâneo HANA foi incluído no instantâneo de armazenamento. 

 ![Selecione uma cópia de segurança como uma base a partir do qual recuperar a base de dados](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Limpar o **cópias de segurança do utilize Delta** caixa de verificação se não existem diferenças entre a hora do instantâneo HANA e o estado mais recente.

 ![Desmarque a caixa de verificação "Utilização Delta cópias de segurança", não se existem nenhum deltas](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. No ecrã de resumo, selecione **concluir** para iniciar o procedimento de restauro.

 ![Clique em "Concluir" no ecrã de resumo](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Recuperar para um outro ponto no tempo
Recuperar para um ponto no tempo entre o instantâneo HANA (incluído no instantâneo armazenamento) e outro que é posterior à recuperação de ponto no tempo de instantâneo HANA, efetue o seguinte:

1. Certifique-se de que todos os registo de transações cópias de segurança do instantâneo HANA ao momento em que pretende recuperar.
2. Iniciar o procedimento em [recuperar para o estado mais recente](#recovering-to-the-most-recent-state).
3. No passo 2 do procedimento, além de **especificar tipo de recuperação** janela, selecione **recuperar a base de dados para o seguinte ponto no tempo**e especifique o ponto no tempo. Em seguida, conclua os passos 3 a 6.

### <a name="monitoring-the-execution-of-snapshots"></a>Monitorizar a execução de instantâneos

Como utilizar instantâneos de armazenamento de instâncias de grande HANA, também terá de monitorizar a execução dos instantâneos de armazenamento. O script que executa um instantâneo de armazenamento escreve resultado num ficheiro e, em seguida, guarda-a mesma localização que os scripts de Perl. É escrito um ficheiro separado para cada instantâneo de armazenamento. O resultado de cada ficheiro mostra claramente as várias fases que executa o script de instantâneo:

1. Localize os volumes que tem de criar um instantâneo.
2. Localize os instantâneos obtidos a partir destes volumes.
3. Elimine os instantâneos existentes eventual corresponder ao número de instantâneos que especificou.
4. Crie um instantâneo de SAP HANA.
5. Crie o instantâneo de armazenamento através de volumes.
6. Elimine o instantâneo de SAP HANA.
7. Mudar o nome o instantâneo da mais recente **.0**.

A parte mais importante do ficheiro cab do script identificado é esta peça:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Pode ver deste exemplo como o script regista a criação do instantâneo HANA. No caso de escalamento horizontal, este processo é iniciado no nó principal. O nó mestre inicia a criação de instantâneos de SAP HANA em cada um de nós de trabalho síncrona. Em seguida, se obtém o instantâneo de armazenamento. Após a execução bem-sucedida de instantâneos de armazenamento, o instantâneo HANA é eliminado. A eliminação do instantâneo HANA é iniciada a partir do nó principal.


## <a name="disaster-recovery-principles"></a>Princípios de recuperação de desastre
Com instâncias de grande HANA, que oferecemos uma funcionalidade de recuperação após desastre entre carimbos de data / instância grande HANA em diferentes regiões do Azure. Por exemplo, se implementar unidades de instância grande HANA na região dos EUA oeste do Azure, pode utilizar as unidades de instância grande HANA na região dos EUA Leste como unidades de recuperação após desastre. Conforme mencionado anteriormente, recuperação após desastre não está configurada automaticamente, porque é necessária a pagar para outra unidade de instância grande HANA na região DR. A configuração de recuperação após desastre funciona para setups vertical, bem como de escalamento horizontal. 

Nos cenários de implementação até ao momento, os nossos clientes utilizam a unidade na região DR para executar os sistemas de não produção que utilizam uma instância HANA instalada. A unidade de instância grande HANA tem de ser do mesmo SKU como o SKU utilizado para fins de produção. A configuração de disco entre a unidade do servidor na região do Azure de produção e a região de recuperação após desastre tem o seguinte aspeto:

![Configuração do programa de configuração de DR do disco ponto de vista](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Conforme apresentado no gráfico descrição geral, em seguida, terá de ordenar segundo conjunto de volumes de disco. Os volumes de disco de destino são o mesmo tamanho que os volumes de produção para a instância de produção em unidades de recuperação após desastre. Estes volumes de disco estão associadas a unidade de instância grande HANA de servidor no site de recuperação após desastre. Os volumes seguintes são replicados a partir da região de produção para o site de DR:

- hana/dados
- hana/logbackups 
- /Hana/Shared (inclui/usr/sap)

O volume de /hana/log não é replicado porque não é necessário o registo de transações de SAP HANA da forma que é efetuado o restauro nesses volumes. 

A base da funcionalidade de recuperação após desastre oferecidos é a funcionalidade de replicação de armazenamento fornecida pela infraestrutura instância grande HANA. A funcionalidade que é utilizada no lado de armazenamento não é um fluxo constante de alterações que são replicados de forma assíncrona, como a alteração efetuada para o volume de armazenamento. Em vez disso, é um mecanismo que depende do facto que são criar instantâneos destes volumes regularmente. As diferenças entre um instantâneo já replicado e um novo instantâneo que ainda não foi replicado, em seguida, é transferida para o site de recuperação de desastres para volumes de disco de destino.  Estes instantâneos são armazenados em volumes e no caso de uma ativação pós-falha de recuperação de desastres, tem de ser restaurado nesses volumes.  

A transferência dos dados do volume completos primeiro deve ter antes da quantidade de dados fica mais pequena do que as diferenças entre instantâneos. Como resultado, os volumes no site de DR contenham todos os instantâneos de volume efetuada no site de produção. Este facto permite-lhe acaba por utilizar esse sistema DR para obter um estado anterior para recuperar dados perdidos, sem reverter o sistema de produção.

Em casos onde utilizar replicação do sistema HANA como a funcionalidade de elevada disponibilidade do site de produção, são replicados apenas os volumes da instância de camada 2 (ou réplica). Esta configuração poderá dar origem a um atraso na replicação de armazenamento para o site de DR se mantenha ou tirá a unidade do servidor de réplica secundária (camada 2) ou a instância de SAP HANA nessa unidade. 

>[!IMPORTANT]
>Tal como acontece com a replicação do sistema HANA com várias camadas, um encerramento da unidade de camada 2 HANA instância ou servidor bloqueia a replicação para o site de recuperação após desastre ao utilizar a funcionalidade de recuperação após desastre de instância grande HANA.


>[!NOTE]
>A funcionalidade de replicação de armazenamento de instância grande HANA é espelhamento e replicar instantâneos de armazenamento. Por conseguinte, se não efetuar instantâneos de armazenamento, tal como apresentado na secção de cópia de segurança deste documento, não pode ser qualquer replicação para o site de recuperação após desastre. Execução de instantâneos de armazenamento é um pré-requisito para a replicação de armazenamento para o site de recuperação após desastre.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparação do cenário de recuperação após desastre
Iremos partem do princípio de que tem um sistema de produção em execução no instâncias grande HANA numa região do Azure de produção. Para o seguinte documentação, vamos assumir que o SID do sistema que HANA é "PRD." Podemos também partem do princípio de que tem um sistema de não produção em execução em instâncias de grande HANA executar a recuperação de desastre região do Azure. Para a documentação, vamos partem do princípio de que o respetivo SID é "TST." Para que a configuração tem o seguinte aspeto:

![Início do programa de configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se a instância de servidor não tem sido ordenada já com o conjunto de volume de armazenamento adicional, SAP HANA na gestão de serviço do Azure irá anexar o conjunto de volumes adicionais como um destino para a réplica de produção para a unidade de instância grande HANA que está a executar o TST Instância HANA no. Para essa finalidade, tem de fornecer o SID da sua instância HANA de produção. Depois de SAP HANA na gestão de serviço do Azure confirma o anexo desses volumes, terá de montar os volumes para a unidade de instância grande HANA.

![Passo seguinte do programa de configuração DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

O passo seguinte para si é instalar a segunda instância de SAP HANA na unidade instância grande HANA na recuperação de desastres região do Azure, onde executa a instância de TST HANA. A instância de SAP HANA recentemente instalada tem de ter o mesmo SID. Os utilizadores criou tem de ter o mesmo UID e ID de grupo que tenha da instância de produção. Se a instalação foi concluída com êxito, tem de:
- Pare a instância de SAP HANA recentemente instalada na unidade HANA de instância grande na recuperação de desastres região do Azure.
- Desmonte estes volumes PRD e contacte o SAP HANA na gestão de serviço do Azure. Os volumes de mensagens em fila não é possível permanecem montados para a unidade porque não pode estar acessíveis à funcionar como destino de replicação de armazenamento.  

![Passo de configuração de DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A equipa de operações vai para estabelecer a relação de replicação entre os volumes PRD na região do Azure de produção e os volumes PRD na recuperação de desastres região do Azure.

>[!IMPORTANT]
>O volume de /hana/log não será replicado porque não é necessário restaurar a base de dados SAP HANA replicada num estado consistente no site de recuperação após desastre.

O passo seguinte para está a configurar ou ajustar a agenda de cópia de segurança de instantâneos de armazenamento para aceder ao seu RTO e RPO no caso de desastre. Para minimizar o objetivo de ponto de recuperação, defina os seguintes intervalos de replicação no serviço de instância grande HANA:
- Os volumes que estão abrangidos pelo instantâneo combinado (tipo de instantâneos = **hana**) replicar a cada 15 minutos para os destinos de volume de armazenamento equivalente no site de recuperação após desastre.
- O volume de cópia de segurança do registo de transações (tipo de instantâneos = **registos**) replica a cada três minutos para os destinos de volume de armazenamento equivalente no site de recuperação após desastre.

Para minimizar o objetivo de ponto de recuperação, configure o seguinte:
- Efetuar uma **hana** instantâneos de armazenamento de tipo (consulte "passo 7: efetuar instantâneos") a cada 30 minutos para 1 hora.
- Efetue cópias de segurança de registo de transações de SAP HANA a cada 5 minutos.
- Efetuar uma **registos** escreva a cada 5-15 minutos de instantâneos de armazenamento. Com este período de intervalo, deve conseguir alcançar um RPO de cerca de 25 de 15 minutos.

Com esta configuração, a sequência de cópias de segurança do registo de transações, os instantâneos de armazenamento e a replicação do registo de transações HANA de cópia de segurança volume e /hana/data e /hana/shared (inclui/usr/sap) aspeto que poderá ter os dados apresentados neste gráfico:

 ![Relação entre um instantâneo de cópia de segurança do registo de transações e um espelho snap num eixo de tempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para alcançar um RPO melhor mesmo no caso de recuperação após desastre, pode copiar as cópias de segurança do registo de transações de HANA de SAP HANA no Azure (instâncias de grande) para outro região do Azure. Para alcançar esta redução RPO adicional, execute os seguintes passos aproximados:

1. Cópia de segurança a transação HANA inicie sessão com uma frequência como possível /hana/logbackups.
2. Utilize rsync para copiar as cópias de segurança do registo de transações para a partilha NFS alojado máquinas virtuais do Azure. As VMs estão em redes virtuais do Azure na região do Azure de produção e nas regiões de DR. Terá de ligar ambas as redes virtuais do Azure para o circuito ligar a instâncias de grande HANA de produção para o Azure. Ver gráficos no [rede considerações para a recuperação de desastre com instâncias de grande HANA](#Network-considerations-for-disaster-recovery-with-HANA-Large-Instances) secção. 
3. Manter que as cópias de segurança do registo de transações na região na VM ligadas para o NFS exportados armazenamento.
4. No caso de desastre-ativação pós-falha, complementar as cópias de segurança do registo de transações que encontrar no /hana/logbackups volume com mais recentemente executadas cópias de segurança do registo de transações no NFS partilham no site de recuperação após desastre. 
5. Agora pode iniciar uma cópia de segurança do registo de transações para restaurar a cópia de segurança mais recente que poderão estar guardada através da região DR.

Como as operações de instância grande HANA confirmar que o programa de configuração de relação de replicação e iniciar as cópias de segurança de instantâneos de armazenamento execução, os dados começa a ser replicada.

![Passo de configuração de DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Como avança ser a replicação, os instantâneos nos volumes PRD na recuperação de desastres do Azure não estão restauradas regiões. Apenas são armazenados. Se os volumes são instalados nesse Estado, estes representam o estado em que a desmontar os volumes depois da instância de PRD SAP HANA foi instalada na unidade de servidor na recuperação de desastres região do Azure. Eles também representam as cópias de segurança de armazenamento que ainda não foram restauradas.

Em caso de uma ativação pós-falha, também pode escolher restaurar um instantâneo mais antigo do armazenamento em vez do instantâneo de armazenamento mais recente.

## <a name="disaster-recovery-failover-procedure"></a>Procedimento de recuperação após desastre de ativação pós-falha
Se pretende ou precisa a ativação pós-falha para o site de DR, tem de interagir com o SAP HANA na equipa de operações do Azure. Nos passos aproximados, o processo até ao momento este aspeto:

1. Como está a executar uma instância de não produção do HANA na unidade de recuperação após desastre de instâncias de grande HANA, tem de encerrar esta instância. Partimos do princípio que é uma instância de produção HANA dormant pré-instaladas.
2. Certifique-se de que não existem processos de SAP HANA estão em execução. Utilize o seguinte comando para esta verificação: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. O resultado deve mostrar o **hdbdaemon** processos num estado parado e outros processos HANA num Estado em execução ou foi iniciado.
3. Determine o nome de instantâneo ou o ID de cópia de segurança de SAP HANA pretende que o site de recuperação após desastre restaurado. Em casos de recuperação de desastres real, este instantâneo é, normalmente, o instantâneo mais recente. Se precisar de recuperar dados perdidos, escolha um instantâneo anterior.
4. Azure contacto suportam através de um pedido de suporte de alta prioridade e peça para o restauro desse instantâneo (nome e a data do instantâneo) ou um ID de cópia de segurança HANA no site de DR. A predefinição é que as operações restaurar apenas o volume de /hana/data. Se pretender ter/hana/logbackups volumes bem, tem de estado de especificamente. *Não recomendamos que restaure o volume de /hana/shared.* Em vez disso, deve escolher ficheiros específicos, como global.ini fora do **.snapshot** diretório e nos seus subdiretórios depois remontar o hana/volume partilhado de PRD. No lado de operações, os passos seguintes vão continuar a ocorrer: um. A replicação de instantâneos de volume de produção para os volumes de recuperação após desastre está parada. Isto poderá ter já aconteceu se uma falha no site de produção é o motivo que é necessário um DR.
    b. O armazenamento nome de instantâneo ou instantâneo com a cópia de segurança ID que escolheu é restaurado nos volumes de recuperação após desastre.
    c. Após o restauro, os volumes de recuperação após desastre estão disponíveis para ser montado para as unidades de instância grande HANA na região de recuperação após desastre.
5. Monte os volumes de recuperação de desastres para a unidade de instância grande HANA no site de recuperação após desastre. 
6. Inicie a instância de produção de SAP HANA dormant até ao momento.
7. Se optar por copiar registos de cópia de segurança de registo de transações, além disso, para reduzir o tempo RPO, tem de unir as cópias de segurança do registo de transações para o diretório de logbackups recentemente montado DR hana /. Não substitua cópias de segurança existentes. Apenas cópia mais recentes cópias de segurança que não foram replicadas com a replicação mais recente de um instantâneo de armazenamento.
8. Também pode restaurar ficheiros único os instantâneos de tem sido replicados para o volume de /hana/shared/PRD a recuperação de desastre região do Azure.

A seguinte sequência de passos envolve a recuperar a instância de produção de SAP HANA com base no instantâneo de armazenamento restaurada e as cópias de segurança do registo de transações que estão disponíveis. Os passos tem o seguinte aspeto:

1. Altere a localização de cópia de segurança para **/hana/logbackups** através da utilização de SAP HANA Studio.
   ![Alterar a localização de cópia de segurança para recuperação do DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA analisa através as localizações de ficheiros de cópia de segurança e sugere a mais recente cópia de segurança do registo de transações para restaurar. A análise pode demorar alguns minutos até um ecrã de como é apresentado o seguinte: ![lista de cópias de segurança do registo de transações para a recuperação do DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Ajuste algumas das predefinições:

      - Limpar **utilizar cópias de segurança diferencial**.
      - Selecione **inicializar registo área**.

   ![Definir a área de registo de inicializar](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Selecione **Concluir**.

   ![Concluir o restauro de DR](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em atenção que o exemplo é de um restauro de recuperação após desastre de uma configuração de SAP HANA de escalamento horizontal do nó de 3.

![Restaurar progresso](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se o restauro parecer fique suspensa no **concluir** ecrã e não apresentar o ecrã de progresso, verificação para confirmar que todas as o SAP HANA instâncias em nós de trabalho em execução. Se necessário, inicie manualmente as instâncias de SAP HANA.


### <a name="failback-from-dr-to-a-production-site"></a>Reativação pós-falha a partir de DR para um site de produção
Pode efetuar a reativação a partir de um DR para um site de produção. Vamos observar o caso em que a ativação pós-falha para o site de recuperação após desastre foi causada por problemas na região do Azure de produção e não satisfaça as suas necessidades para recuperar dados perdidos. Isto significa que tenha sido executados a carga de trabalho de produção do SAP durante tempo no site de recuperação após desastre. Como são resolvidos os problemas no site de produção, pretende haja reativação para o site de produção. Porque não é possível perder dados, o passo anterior para o site de produção envolve vários passos e fechar cooperação com o SAP HANA na equipa de operações do Azure. Trata-se até a acionar a equipa de operações a iniciá-la para o site de produção após os problemas são resolvidos.

A sequência de passos tem o seguinte aspeto:

1. O SAP HANA na equipa de operações do Azure obtém o acionador para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastres, que agora representam o estado de produção. Neste estado, a unidade de instância grande HANA no site de produção é encerrada.
2. O SAP HANA na equipa de operações do Azure monitoriza a replicação e certifica-se de que um catch-up é alcançado antes informando-o como um cliente.
3. Encerre as aplicações que utilizam a produção HANA instância no site de recuperação após desastre. Em seguida, efetuar uma cópia de segurança do registo de transações de HANA. Em seguida, pare a execução das unidades de instância grande HANA no site de recuperação após desastre da instância HANA.
4. Depois da instância HANA em execução na unidade de instância grande HANA no site de recuperação de desastre é encerrada, a equipa de operações manualmente sincroniza os volumes de disco novamente.
5. O SAP HANA na equipa de operações do Azure começa novamente a unidade de instância grande HANA no site de produção e passa-lo ao longo para si. Certifique-se de que a instância de SAP HANA está num Estado de encerramento ao tempo de arranque da unidade instância grande HANA.
6. Efetuar os mesmos passos de restauro de base de dados, tal como fez quando efetuar a ativação pós-falha para o site de recuperação após desastre anteriormente.

### <a name="monitoring-disaster-recovery-replication"></a>Monitorizar a replicação de recuperação de desastre

Pode monitorizar o estado do seu progresso de replicação de armazenamento, executar o script `azure_hana_replication_status.pl`. Este script tem de ser executado a partir de uma unidade com a localização de recuperação após desastre. Caso contrário, é não vai funcionar conforme esperado. O script funciona independentemente se pretende ou não a replicação está ativa. O script pode ser executado para cada unidade de instância grande HANA do seu inquilino na localização de recuperação após desastre. Não pode ser utilizado para obter detalhes sobre o volume de arranque.

Chame o script como:
```
./replication_status.pl <HANA SID>
```

O resultado é reduzido, por volume, nas seguintes secções:  

- Estado da ligação
- Atividade de replicação atual
- Mais recente instantâneo replicado 
- Tamanho do instantâneo mais recente
- Tempo de desfasamento atual entre a instantâneos – a última replicação de instantâneo concluído e agora  

O estado da ligação é apresentado como **Active Directory** , a menos que a ligação entre localizações está pendente ou está em curso um evento de ativação pós-falha. A atividade de replicação endereços se quaisquer dados está atualmente a ser replicados ou está inativos, ou se outras atividades atualmente estão a ocorrer para a ligação. O último instantâneo replicado só deve ser apresentadas como `snapmirror…`. Em seguida, é apresentado o tamanho do último instantâneo. Por fim, o tempo de atraso é apresentado. O atraso representa o tempo desde o momento da replicação agendada para quando a replicação estiver concluída. Um atraso pode ser maior do que uma hora para a replicação de dados, especialmente na replicação inicial, apesar da replicação foi iniciada. O tempo de desfasamento vai continuar a aumentar até que seja concluída a replicação em curso.

Um exemplo de uma saída pode ter o seguinte aspeto:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












