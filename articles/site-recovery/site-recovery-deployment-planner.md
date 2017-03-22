---
title: Azure Site Recovery Deployment Planner de VMware para o Azure | Microsoft Docs
description: "Este é o manual do utilizador do Azure Site Recovery Deployment Planner."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2575621d72b7db2b090ba923324697b7fa7b8308
ms.lasthandoff: 03/15/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Este é o manual do utilizador do Azure Site Recovery Deployment Planner para implementações de produção de VMware para o Azure.


##<a name="overview"></a>Descrição geral

Antes de proteger qualquer máquina virtual do VMware com o Azure Site Recovery, tem de alocar largura de banda suficiente com base na sua taxa de alterações de dados diária, de modo a cumprir o RPO pretendido. Tem de implementar o número certo de Servidores de Configuração e de Servidores de Processos no local. Também tem de criar o tipo e o número certos de contas de Armazenamento do Azure de destino, sejam standard ou premium, tendo em conta o crescimento nos servidores de produção de origem devido à maior utilização ao longo do tempo. O tipo de armazenamento é decido individualmente por máquina virtual com base nas características das cargas de trabalho (IOPS de R/W, alterações a dados) e nos limites do Azure Site Recovery.  

O Azure Site Recovery Deployment Planner Public Preview é uma ferramenta de linha de comandos que está disponível, atualmente, apenas para o cenário de VMware para o Azure. Pode utilizar esta ferramenta para criar remotamente o perfil das suas máquinas virtuais de VMware (sem qualquer tipo de impacto na produção) para compreender os requisitos de largura de banda e de armazenamento do Azure necessários para replicação e Ativação Pós-falha de teste bem-sucedidas.  Pode executar a ferramenta sem instalar nenhum componente do Azure Site Recovery no local, embora, no sentido de obter resultados de débito precisos, recomenda-se executar o Planner num Windows Server que cumpra os requisitos mínimos do Servidor de Configuração do Azure Site Recovery que terá de, a determinada altura, implementar, como um dos primeiros passos da implementação de produção.

A ferramenta disponibiliza os seguintes detalhes:

**Avaliação de compatibilidade**<br>
* Avaliação de elegibilidade de máquinas virtuais com base no número de discos, no tamanho do disco, em IOPS e na alteração a dados

**Necessidade de largura de banda de rede vs. avaliação de RPO**<br>
* Largura de banda de rede estimada necessária para a replicação delta<br>
* Débito que o Azure Site Recovery consegue a partir do local para o Azure<br>
* Número de máquinas virtuais a colocar em lotes com base na largura de banda estimada para concluir a replicação inicial dentro de um determinado período de tempo.<br>

**Requisitos de infraestrutura do Microsoft Azure**<br>
* Requisito de tipo de armazenamento (armazenamento standard ou premium) para cada máquina virtual<br>
* Número total de contas de armazenamento standard e premium a aprovisionar para a replicação<br>
* Sugestões de nome de contas de armazenamento com base nas orientações do Armazenamento do Azure<br>
* Posicionamento da conta de armazenamento de todas as máquinas virtuais<br>
* Número de núcleos do Microsoft Azure a aprovisionar antes da ativação pós-falha/reativação pós-falha de teste na subscrição<br>
* Tamanho de máquina virtual do Microsoft Azure recomendado para cada máquina virtual no local<br>

**Requisitos de infraestrutura no local**<br>
* Número necessário de Servidores de Configuração e de Servidores de Processos a implementar no local<br>

>[!IMPORTANT]
>
>Todos os cálculos da ferramenta são feitos assumindo um fator de crescimento de 30% nas características da sua carga de trabalho, devido ao possível aumento na utilização ao longo do tempo, e os valores do percentil 95 relativos a todas as métricas do perfil (IOPS de R/W, alterações a dados, etc.) Ambos os parâmetros – fator de crescimento e cálculo do percentil – são configuráveis. Saiba mais sobre o [fator de crescimento](site-recovery-deployment-planner.md#growth-factor) e o [valor de percentil utilizado no cálculo](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation).
>


## <a name="requirements"></a>Requisitos
A ferramenta tem duas fases principais – a criação de perfis e a geração de relatórios. Também existe uma terceira opção, para calcular apenas o débito. Abaixo, são indicados os requisitos para o servidor a partir do qual a medição da criação de perfis / débito é iniciada.

| Requisito | Descrição|
|---|---|
|Medição da criação de perfis e do débito| <br>Sistema Operativo: Microsoft Windows Server 2012 R2 <br>Idealmente, deve corresponder, pelo menos, ao seguinte [tamanho](https://aka.ms/asr-v2a-on-prem-components) do Servidor de Configuração<br>Configuração da máquina : 8 vCPus, 16 GB de RAM, 300 GB HDD<br [Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[Microsoft Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)<br> Acesso à Internet para o Microsoft Azure a partir deste servidor<br> Conta de armazenamento do Microsoft Azure<Br>Acesso de administrador no servidor<br>Espaço livre mínimo no disco de 100 GB (presumindo mil máquinas virtuais com uma média de três discos cada qual com perfil para 30 dias)|
| Geração de Relatórios| Qualquer PC Windows/Windows Server com o Microsoft Excel 2013 e posterior |
| Permissões de Utilizador | Permissão só de leitura para a conta de utilizador utilizada para aceder ao servidor do VMware vCenter/vSphere durante a criação do perfil|


> [!NOTE]
>
> A ferramenta só pode criar perfis para máquinas virtuais com discos VMDK e RDM. Não pode criar perfis para máquinas virtuais com discos iSCSI ou NFS. Embora o Azure Site Recovery suporte discos iSCSI e NFS para servidores do VMware, uma vez que o planeador de implementação não está dentro do convidado e está a criar perfis apenas com os contadores de desempenho do vCenter, a ferramenta não tem visibilidade para estes tipos de discos.
>


##<a name="download"></a>Transferência
[Transfira](https://aka.ms/asr-deployment-planner) a versão mais recente do Azure Site Recovery Deployment Planner Public Preview.  A ferramenta está comprimida no formato zip.  A versão atual só suporta o cenário de VMware para o Azure.

Copie o ficheiro zip para o Windows Server a partir do qual quer executar a ferramenta. Apesar de poder executá-la em qualquer Windows Server 2012 R2 que tenha acesso à rede para se ligar ao VMware vCenter Server ou ao anfitrião ESXi do VMware vSphere que contém as máquinas virtuais para as quais vão ser criados os perfis, recomenda-se executar a ferramenta num servidor cuja configuração de hardware esteja em conformidade com a [Configuration Server sizing guideline (Diretrizes de dimensionamento do Servidor de Configuração)](https://aka.ms/asr-v2a-on-prem-components).  Se já tiver implementado componentes do Azure Site Recovery no local, deve executar a ferramenta no Servidor de Configuração. Recomenda-se ter a mesma configuração de hardware do Servidor de Configuração (que tem um Servidor de Processos incorporado) no servidor onde vai executar a ferramenta, para que o débito obtido que esta comunica corresponda ao débito real que o Azure Site Recovery pode obter durante a replicação – o cálculo do débito depende da largura de banda de rede disponível no servidor e da configuração do hardware (CPU, armazenamento, etc.) do servidor. Se executar a ferramenta a partir de qualquer outro servidor, o débito será calculado a partir desse servidor para o Microsoft Azure, além de que a configuração de hardware do servidor poderá ser diferente da do Servidor de Configuração, pelo que o débito obtido que a ferramenta vai comunicar não será preciso.

Extraia a pasta zip. Pode ver vários ficheiros e subpastas. O executável é ASRDeploymentPlanner.exe na pasta principal.

Exemplo: copie o ficheiro .zip para a unidade E:\ e extraia-o.
E:\ASR Deployment Planner-Preview_v1.1.zip

E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>Capacidades
A ferramenta de linha de comandos (ASRDeploymentPlanner.exe) pode ser executada num dos três modos seguintes:

1.    Criação de perfis  
2.    Geração de relatórios
3.    Obtenção de débito

Primeiro, tem de executar a ferramenta no modo de criação de perfis, para recolher as alterações a dados e IOPS das máquinas virtuais.  Em seguida, execute a ferramenta para gerar o relatório para localizar os requisitos de largura de banda de rede e de armazenamento.

##<a name="profiling"></a>Criação de perfis
No modo de criação de perfis, a ferramenta Deployment Planner liga-se ao vCenter Server ou aos anfitriões ESXi do vSphere para recolher dados de desempenho da máquina virtual.

* A criação de perfis não afeta o desempenho das máquinas virtuais de produção, pois não é feita qualquer ligação direta às mesmas. Todos os dados de desempenho são recolhidos a partir do vCenter Server/ anfitrião ESXi do vSphere.
* O vCenter Server / anfitrião ESXi do vSphere é consultado a cada quinze minutos, para garantir que a criação de perfis tem um impacto mínimo no servidor. No entanto, a precisão da criação de perfis não é posta em causa, porque a ferramenta armazena os dados dos contadores de desempenho relativos a cada minuto.

####<a name="create-a-list-of-virtual-machines-to-profile"></a>Criar uma lista de máquinas virtuais para as quais criar perfis
Em primeiro lugar, tem de ter a lista de máquinas virtuais para as quais pretende criar perfis. Pode obter todos os nomes das máquinas virtuais num VMware vCenter ou num anfitrião ESXi do VMware vSphere com os comandos do VMware vSphere PowerCLI seguintes. Em alternativa, pode simplesmente listar nomes amigáveis / endereços IP das máquinas virtuais para as quais quer criar perfis manualmente num ficheiro.

1.    Inicie sessão na máquina virtual em que está instalado o VMware vSphere PowerCLI
2.    Abra a consola do VMware vSphere PowerCLI
3.    Confirme que a política de execução não está desativada no script. Se estiver, abra a consola do VMware vSphere PowerCLI no modo de administrador e execute o comando seguinte para ativá-la:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    Execute os dois comandos seguintes para obter todos os nomes das máquinas virtuais no VMware vCenter ou no VMware vSphere ESXi e armazená-los num ficheiro .txt.
Substitua &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo;, &lsaquo;outputfile.txt&rsaquo; pelas suas entradas.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    Abra o ficheiro de saída no Bloco de notas. Copie os nomes de todas as máquinas virtuais para as quais quer criar perfis para outro ficheiro (chamemos-lhe ProfileVMList.txt), com um nome de máquina por linha. Este ficheiro vai ser utilizado como entrada para o parâmetro -VMListFile da ferramenta de linha de comandos

    ![Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>Começar a criar perfis
Quando tiver a lista das VMs para as quais criar perfis, pode executar a ferramenta no modo de criação de perfis. Abaixo, pode ver a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de criação de perfis. Os parâmetros entre [] são opcionais.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Nome do Parâmetro | Descrição |
|---|---|
| -Operation |      StartProfiling |
| -Server | Nome do domínio completamente qualificado ou endereço IP do vCenter Server/anfitrião ESXi para cujas máquinas virtuais vão ser criados perfis.|
| -User | Nome de utilizador para ligar ao vCenter Server/anfitrião ESXi O utilizador tem de ter, pelo menos, acesso só de leitura|
| -VMListFile |    O ficheiro com a lista de máquinas virtuais para as quais criar perfis. O caminho do ficheiro pode ser absoluto ou relativo. Este ficheiro deve conter um nome/endereço IP de máquina por linha. O nome da máquina virtual especificado no ficheiro deve ser igual ao nome da VM no vCenter Server ou no anfitrião ESXi. <br> Por exemplo, o ficheiro “VMList.txt” contém as máquinas virtuais seguintes:<br>máquina_virtual_A <br>10.150.29.110<br>máquina_virtual_B |
| -NoOfDaysToProfile | O número de dias durante os quais a criação de perfis deve ser executada. Recomenda-se executar a criação de perfis durante mais de 15 dias, para garantir que o padrão da carga de trabalho no seu ambiente ao longo do período especificado é respeitado e utilizado para proporcionar uma recomendação precisa |
| [-Directory] |    O UNC ou caminho de diretório local para armazenar os dados de criação de perfis gerados durante a criação. Se não for indicado, será utilizado como diretório predefinido o diretório com o nome “ProfiledData” no caminho atual. |
| [-Password ] | A palavra-passe para ligar ao vCenter Server/anfitrião ESXi. Se não for indica agora, ser-lhe-á pedida quando o comando for executado.|
|  [-StorageAccountName]  | O nome da conta de Armazenamento do Azure para encontrar o débito alcançável para a replicação de dados no local para o Azure. Para calcular o débito, a ferramenta carrega dados de teste para esta conta de armazenamento.|
| [-StorageAccountKey] | A chave da conta de Armazenamento do Azure utilizada para aceder à conta de armazenamento. Aceda ao portal do Azure > Contas de armazenamento > [Nome da conta de armazenamento] > Definições > Chaves de Acesso > Chave1 (ou Chave de acesso primária para contas de armazenamento clássicas). |

Recomenda-se a criação de perfis para as máquinas virtuais durante, pelo menos, 15 a 30 dias. Durante o período de criação de perfis, ASRDeploymentPlanner.exe permanece em execução. A ferramenta aceita a entrada da duração da criação de perfis em dias. Se quiser criar perfis por poucas horas ou minutos para um breve teste à ferramenta, na Pré-visualização Pública, terá de converter a duração na medida equivalente de dias.  Por exemplo, para criar perfis durante 30 minutos, a entrada terá de ser 30 / (60*24) = 0,021 dias.  O período mínimo de criação de perfis são 30 minutos.

Durante a criação de perfis, pode, opcionalmente, transmitir o nome e a chave de uma conta de Armazenamento do Azure para encontrar o débito que o Azure Site Recovery pode alcançar no momento da replicação do Servidor de Configuração / Servidor de Processos para o Azure. Se o nome e a chave da conta de Armazenamento do Azure não forem transmitidos durante a criação de perfis, a ferramenta não calcula o débito alcançável.


Pode executar várias instâncias da ferramenta em diferentes conjuntos de máquinas virtuais. Confirme que não existem nomes de máquinas virtuais repetidos em nenhum dos conjuntos de criação de perfis. Por exemplo, se tiver criado perfis para dez máquinas virtuais (VM&1; a VM&10;) e, ao fim de alguns dias, quiser criar perfis para outras cinco (VM&11; a VM&15;), pode executar a ferramenta noutra consola de linha de comandos para o segundo conjunto de máquinas virtuais (VM&11; a VM&15;). Assegure-se de que o segundo conjunto de máquinas virtuais não tem nenhum nome de máquinas virtuais da primeira instância de criação de perfis ou de que utiliza outro diretório de saída para a segunda execução. Se forem utilizadas duas instâncias da ferramenta para criar perfis para as mesmas máquinas virtuais e o mesmo diretório de saída, o relatório gerado estará incorreto.

A configuração das máquinas virtuais é capturada uma vez no início da operação de criação de perfis e armazenada num ficheiro com o nome VMDetailList.xml. Esta informação vai ser utilizada no momento da geração de relatórios. Não serão capturadas quaisquer alterações à configuração das VMs (por exemplo, aumento no número de núcleos, discos, NICs, etc.) desde o início da criação de perfis até ao fim. Caso a configuração de uma máquina virtual para a qual foram criados perfis tiver sofrido alterações durante a criação dos perfis, existe, na Pré-visualização Pública, uma solução para obter os últimos detalhes da máquina virtual durante a geração do relatório.   

* Criar uma cópia de segurança de “VMdetailList.xml” e eliminar o ficheiro da localização atual.
* Transmitir os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfis gera vários ficheiros no diretório de criação de perfis – não elimine nenhum, caso contrário, a geração de relatórios será afetada.

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemplo 1: para criar perfis para máquinas virtuais durante 30 dias e encontrar o débito no local para o Azure
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  30  **-User** vCenterUser1 **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>Exemplo 2: para criar perfis para máquinas virtuais durante 15 dias
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  15  -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>Exemplo 3: para criar perfis para máquinas virtuais durante uma hora, para um breve teste da ferramenta
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  0.04  **-User** vCenterUser1


>[!NOTE]
>
> * Se o servidor a partir da qual a ferramenta está a ser executada for reiniciado ou falhar ou se utilizar Ctrl + C para sair da ferramenta, os dados da criação de perfis serão preservados. Esta situação pode levar à perda dos últimos 15 minutos de dados de criação de perfis. Tem de voltar a executar a ferramenta no modo de criação de perfis, quando o servidor for iniciado.
>
> * Quando são transmitidos o nome e a chave de uma conta de Armazenamento do Azure, a ferramenta mede o débito no último passo da criação de perfis. Se a ferramenta for terminada antes da conclusão da criação de perfis, o débito não é calculado. Pode sempre executar a operação GetThroughput a partir da consola de linha de comandos para encontrar o débito antes de gerar o relatório, caso contrário, o relatório gerado não terão as informações de débito alcançado.
>

##<a name="generate-report"></a>Gerar o relatório
A ferramenta gera um XLSM (ficheiro do Microsoft Excel com macros ativadas) como a saída do relatório que resume todas as recomendações de implementação – o relatório tem o nome DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm e é colocado no diretório especificado.

Depois de concluída a criação de perfis, pode executar a ferramenta no modo de geração de relatórios. Abaixo, pode ver a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de geração de relatórios. Os parâmetros entre [] são opcionais.

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|Nome do Parâmetro | Descrição |
|-|-|
| -Operation | GenerateReport |
| -Server |  O nome de domínio completamente qualificado ou o endereço IP do vCenter/vSphere Server (utilize exatamente o mesmo nome ou endereço IP que utilizou no momento da criação de perfis) onde estão localizadas as máquinas virtuais para as quais foram criados perfis e para as quais vão ser gerados relatórios. Tenha em atenção que, se tiver utilizado um vCenter Server no momento da criação de perfis, não pode utilizar um do vSphere Server para a geração de relatórios e vice-versa.|
| -VMListFile | O ficheiro com a lista de máquinas virtuais para as quais foram criados perfis e para as quais vai ser gerado o relatório. O caminho do ficheiro pode ser absoluto ou relativo. Este ficheiro deve conter um nome/endereço IP de máquina por linha. Os nomes das máquinas virtuais especificados no ficheiro devem ser os mesmos que os nomes das máquinas virtuais no vCenter Server ou no anfitrião ESXi e corresponder ao que estava a ser utilizado no momento da criação de perfis.|
| [-Directory] | O UNC ou caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado, será utilizado o diretório “ProfiledData”. |
| [-GoalToCompleteIR] |    O número de horas em que a replicação inicial das máquinas virtuais para as quais foram criados perfis tem de ser concluída. O relatório gerado irá fornecer o número de máquinas virtuais cuja replicação inicial possa ser concluída no tempo especificado. A predefinição são 72 horas. |
| [-User] | O nome de utilizador para ligar ao vCenter/vSphere Server. É utilizado para obter as últimas informações de configuração das máquinas virtuais, como o número de discos, de núcleos, de NICs, etc., para utilizar no relatório. Se não for indicado, são utilizadas as informações de configuração recolhidas no início da criação de perfis. |
| [-Password] | A palavra-passe para ligar ao vCenter Server/anfitrião ESXi. Se não for indica como parâmetro, ser-lhe-á pedida mais tarde, quando o comando for executado. |
| [-DesiredRPO] | O Objetivo de Ponto de Recuperação (RPO) Pretendido em minutos. A predefinição são 15 minutos.|
| [-Bandwidth] | A largura de banda em Mbps. É utilizado para calcular o RPO que pode ser alcançado para a largura de banda especificada. |
| [-StartDate]  | A data e hora de início, em MM-DD-AAAA:HH:MM (no formato de 24 horas). “StartDate” tem de ser especificado juntamente com “EndDate”. Quando indicado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| [-EndDate] | A data e hora de fim, em MM-DD-AAAA:HH:MM (no formato de 24 horas). “EndDate” tem de ser especificado juntamente com “StartDate”. Quando indicado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| [-GrowthFactor] |O fator de crescimento em percentagem. A predefinição é 30%.  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>Exemplo 1: para gerar o relatório com valores predefinidos quando os dados de criação de perfis estão na unidade local
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>Exemplo 2: para gerar o relatório quando os dados de criação de perfis estão num servidor remoto. O utilizador deve ter acesso de leitura/escrita no diretório remoto
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “\\\\PS1-W2K12R2\vCenter1_ProfiledData” **-VMListFile** “\\\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exemplo 3: gerar o relatório com largura de banda e objetivos específicos para concluir a replicação inicial no período especificado
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>Exemplo 4: gerar o relatório com um fator de crescimento de 5% em vez dos 30% predefinidos
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>Exemplo 5: gerar o relatório com um subconjunto de dados de criação de perfis. Imaginemos que tem 30 dias de dados de criação de perfis e quer gerar o relatório para apenas 20 dias.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-StartDate**  01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>Exemplo 6: gerar o relatório para um RPO de cinco minutos
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil utilizado para o cálculo
**Que valor de percentil predefinido das métricas de desempenho recolhidas durante a criação de perfis é utilizado no momento da geração de relatórios?**

A ferramenta é predefinida para os valores de percentil de 95 de IOPS de R/W, IOPS de escrita e alterações a dados recolhidas durante a criação de perfis para todas as VMs. Isto garante que o pico de percentil 100 que as suas VMs poderão registar, devido a eventos temporários, como tarefas de criação de cópias de segurança executadas uma vez por dia, atividades periódicas de indexação de bases de dados ou de geração de relatórios de análise ou qualquer outro evento breve pontual que aconteça durante o período de criação de perfis, não é utilizado para determinar os requisitos de Armazenamento do Azure de destino e de largura de banda de origem. Utilizar valores de percentil 95 proporciona uma imagem verdadeira das características reais das cargas de trabalho e confere-lhe o melhor desempenho se essas cargas de trabalho forem executadas no Microsoft Azure. Não esperamos que venha a alterar este número muitas vezes; contudo, se escolher um percentil ainda mais baixo, como o 90, pode atualizar este ficheiro de configuração “ASRDeploymentPlanner.exe.config” na pasta predefinida e guardá-lo para gerar um relatório novo sobre os dados de criação de perfis existentes.

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>Fator de crescimento
**Por que motivo deve considerar o fator de crescimento durante planeamento da implementação?**

É fundamental ter em conta o crescimento nas características da sua carga de trabalho, presumindo um aumento na utilização ao longo do tempo. Isto acontece porque, se estiver protegida, caso as características da sua carga de trabalho sofrerem alterações, não existe atualmente forma de mudar para outra conta de Armazenamento do Azure para proteção sem desativar e reativar a proteção. Por exemplo, Se, hoje, uma máquina virtual estiver numa conta de replicação de armazenamento standard e, daqui a três meses, devido a um aumento dos utilizadores da aplicação que está a ser executada na máquina virtual, e se as alterações a dados na VM aumentarem e exigirem passar para o armazenamento premium para que a replicação do Azure Site Recovery possa acompanhar as novas alterações crescentes, terá de desativar e reativar a proteção para uma conta de armazenamento premium. Assim, é vivamente recomendado planear o crescimento durante o planeamento da implementação e o valor predefinido é 30%. Uma vez que são os utilizadores quem melhor conhece os padrões de utilização e as projeções de crescimento das aplicações deles, podem alterar este número enquanto geram relatórios. Na realidade, pode gerar vários relatórios com diferentes fatores de crescimento com os mesmos dados de criação de perfis e ver que recomendações de Armazenamento do Azure de destino e de largura de banda de origem funcionam melhor para si.

O relatório do Microsoft Excel gerado tem as folhas seguintes

* [Input (Entrada)](site-recovery-deployment-planner.md#input)
* [Recommedations (Recomendações)](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Recommedations-Bandwidth Input (Entrada de Recomendações-Largura de Banda](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Storage Placement (VM<->Colocação de Armazenamento)](site-recovery-deployment-planner.md#vm-storage-placement)
* [Compatible VMs (VMs Compatíveis)](site-recovery-deployment-planner.md#compatible-vms)
* [Incompatible VMs (VMs Não Compatíveis)](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>Obtenção de débito
Para estimar o débito que o Azure Site Recovery pode alcançar no local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula o débito do servidor onde está a ser executada (idealmente, um servidor com base no guia de dimensionamento do Servidor de Configuração).  Se já tiver implementado componentes da infraestrutura do Azure Site Recovery no local, execute a ferramenta no Servidor de Configuração.

Abra uma consola da linha de comandos e aceda à pasta da ASR deployment planning tool.  Execute ASRDeploymentPlanner.exe com os parâmetros seguintes. Os parâmetros entre [] são opcionais.

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|Nome do Parâmetro | Descrição |
|-|-|
| -operation | GetThroughput |
| [-Directory] | O UNC ou caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado, será utilizado o diretório “ProfiledData”.  |
| -StorageAccountName | O nome da conta de Armazenamento do Azure para encontrar a largura de banda consumida para a replicação dos dados no local para o Azure. Para calcular a largura de banda consumida, a ferramenta carrega dados de teste para esta conta de armazenamento. |
| -StorageAccountKey | A Chave da Conta de Armazenamento do Azure utilizada para aceder à conta de armazenamento. Aceda ao portal do Azure > Contas de armazenamento > [Nome da conta de armazenamento] > Definições > Chaves de Acesso > Chave1 (ou Chave de acesso primária para contas de armazenamento clássicas). |
| -VMListFile | O ficheiro com a lista de máquinas virtuais para as quais criar perfis para calcular a largura de banda consumida. O caminho do ficheiro pode ser absoluto ou relativo. Este ficheiro deve conter um nome/endereço IP de máquina por linha. Os nomes das máquinas virtuais especificadas no ficheiro devem ser os mesmos dos nomes das máquinas virtuais no vCenter Server ou no anfitrião ESXi.<br>Por exemplo, o ficheiro “VMList.txt” contém as máquinas virtuais seguintes:<br>máquina virtual_A <br>10.150.29.110<br>máquina virtual_B|

A ferramenta cria vários ficheiros “asrvhdfile<#>.vhd” (em que # corresponde ao número) de 64 MB no diretório especificado.  Carrega estes ficheiros para a conta do Armazenamento do Azure para encontrar o débito. Depois de o débito ser medido, elimina todos estes ficheiros da conta de Armazenamento do Azure e do servidor local. Se a ferramenta for terminada por qualquer razão a meio do cálculo do débito, não eliminará os ficheiros do Armazenamento do Azure nem do servidor local e terá de eliminá-los manualmente.

O débito é medido num determinado ponto no tempo e é o débito máximo que o Azure Site Recovery pode alcançar durante a replicação, desde que todos os outros fatores permaneçam iguais. Por exemplo, se uma aplicação começar a consumir mais largura de banda na mesma rede, o débito real varia durante a replicação. Se estiver a executar o comando GetThroughput num Servidor de Configuração, a ferramenta não está ciente de nenhuma máquina virtual protegida e da replicação em curso. O resultado do débito medido será diferente se a operação GetThroughput for executada numa altura em que as máquinas virtuais protegidas têm um volume de alterações a dados elevado ou numa altura em que esse volume é reduzido.  Recomenda-se que execute a ferramenta em pontos no tempo diferentes durante a criação de perfis, para compreender que débito pode ser alcançado em vários momentos. No relatório, a ferramenta mostra o último débito medido.


##### <a name="example"></a>Exemplo
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory**  E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt  **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * Execute a ferramenta num servidor que tenha as mesmas características de armazenamento e CPU que o Servidor de Configuração
>
> * Para replicação, aprovisione a largura de banda que é recomendada para cumprir o RPO em 100% do tempo. Se, mesmo depois de aprovisionar a largura de banda certa, não vir um aumento no débito alcançado comunicado pela ferramenta, verifique o seguinte:
>
> a. Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Azure Site Recovery
>
> b. Verifique se o cofre do Azure Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede
>
> c. Verifique as características do armazenamento local e tente melhorar o hardware (por exemplo, de HDD para SSD, etc.)
>
> d. Altere as definições do Azure Site Recovery no Servidor de Processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
>

##<a name="recommendations-with-desired-rpo-as-input"></a>Recomendações com o RPO pretendido como entrada

###<a name="profiled-data"></a>Dados de criação de perfis

![Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period (Período dos dados de criação de perfis)** é a duração da execução da criação de perfis. Por predefinição, a ferramenta utiliza todos os dados de criação de perfis para o cálculo, a não ser que o relatório seja gerado para um período específico, mediante a utilização das opções StartDate e EndDate.

**Server Name (Nome do Servidor)** é o nome ou o endereço IP do VMware vCenter ou do anfitrião ESXi para cujas máquinas virtuais é criado o relatório.

O **RPO pretendido** é o Objetivo de Ponto de Recuperação (RPO) para a sua implementação. Por predefinição, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na folha. Se tiver utilizado o parâmetro DesiredRPOinMin ao gerar o relatório, esse valor é mostrado neste menu pendente de RPO Pretendido.

###<a name="profiling-overview"></a>Descrição Geral da Criação de Perfis

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines (Total de Máquinas Virtuais para as quais Foram Criados Perfis)** é o número total de máquinas virtuais cujos dados de criação de perfis estão disponíveis. Se VMListFile tiver nomes de máquinas virtuais para as quais não foram criados perfis, essas máquinas não são consideradas na geração do relatório e são excluídas da contagem total de máquinas virtuais para as quais foram criados perfis.

**Compatible Virtual Machines (Máquinas Virtuais Compatíveis)** são o número de máquinas virtuais que podem ser protegidas para o Azure com o Azure Site Recovery. São o número total de máquinas virtuais compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas de Armazenamento do Azure, o número de núcleos do Microsoft Azure e o número de Servidores de Configuração e de Servidores de Processos adicionais. Os detalhes de cada máquina virtual compatível estão disponíveis na folha Compatible VMs do relatório.

**Incompatible Virtual Machines (Máquinas Virtuais Não Compatíveis)** são o número de máquinas virtuais para as quais foram criados perfis que são incompatíveis para proteção com o Azure Site Recovery. Os motivos da incompatibilidade estão apontados na secção Incompatible VMs, abaixo. Se VMListFile tiver nomes de máquinas virtuais para as quais não foram criados perfis, essas máquinas são excluídas da contagem de máquinas virtuais incompatíveis. Estas máquinas virtuais são listadas como “Data not found” (“Dados não encontrados”) na parte final da folha Incompatible VMs.

**Desired RPO (RPO Pretendido)** é o seu RPO pretendido em minutos. O relatório é gerado para três valores de RPO – 15, 30 e 60 minutos, sendo 15 minutos a predefinição. A recomendação de largura de banda no relatório será alterada com base na sua seleção na lista pendente de RPO Pretendido, no canto superior direito da folha. Se tiver gerado o relatório com o parâmetro "-DesiredRPO” com um valor personalizado, este valor personalizada será mostrado como a predefinição na lista pendente de RPO Pretendido.

###<a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (Largura de Banda de Rede Necessária [Mbps])

![Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100% of the time (Para satisfazer o RPO em 100% do tempo):** é a largura de banda recomendada em Mbps a ser alocada para satisfazer o seu RPO pretendido 100% do tempo. Esta quantidade de largura de banda tem de estar dedicada à replicação delta de estado estável de todas as máquinas virtuais compatíveis, para evitar possíveis violações ao RPO.

**To meet RPO 90% of the time (Para satisfazer o RPO em 90% do tempo)**: se não puder aprovisionar a largura de banda necessária para satisfazer o seu RPO pretendido em 100% do tempo, devido aos preços da largura de banda ou a qualquer outro motivo, pode optar por aprovisionar uma quantidade de largura de banda inferior e que possa satisfazer esse RPO 90% do tempo. Para compreender as implicações de aprovisionar esta menor largura de banda, o relatório disponibiliza uma análise de hipóteses quanto ao número e à duração das violações de RPO que podem ser esperadas.

**Achieved Throughput (Débito Alcançado):** é o débito do servidor onde executou o comando GetThroughput para a região do Microsoft Azure onde está localizada a conta de Armazenamento do Azure. Indica o débito aproximado que pode ser alcançado quando protege as máquinas virtuais compatíveis com o Azure Site Recovery, desde que as características de armazenamento e rede do Servidor de Configuração / Servidor de Processos permaneçam iguais às do servidor de onde executou a ferramenta.    

Para replicação, deve aprovisionar a largura de banda que é recomendada para cumprir o RPO em 100% do tempo. Se, mesmo depois de aprovisionar a largura de banda certa, não vir um aumento no débito alcançado comunicado pela ferramenta, verifique o seguinte:

a.    Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Azure Site Recovery

b.    Verifique se o cofre do Azure Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede

c.    Verifique as características do armazenamento local e tente melhorar o hardware (por exemplo, de HDD para SSD, etc.)

d. Altere as definições do Azure Site Recovery no Servidor de Processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Nos casos em que esteja a executar a ferramenta num Servidor de Configuração / Servidor de Processos que já tenha máquinas virtuais protegidas, execute-a algumas vezes, porque o número do débito alcançado vai alterar-se dependendo da quantidade de alterações a dados que estejam a ser processadas nesse momento em particular.

Para todas as implementações empresariais do Azure Site Recovery, recomenda-se utilizar o [ExpressRoute](https://aka.ms/expressroute).

###<a name="required-azure-storage-accounts"></a>Required Azure Storage Accounts (Contas de Armazenamento do Azure Necessárias)
Este gráfico mostra o número total de contas de Armazenamento do Azure (standard e premium) necessárias para proteger todas as máquinas virtuais compatíveis.  Clique em [Plano de colocação de VMs recomendado](site-recovery-deployment-planner.md#vm-storage-placement) para saber que conta de armazenamento deve ser utilizada para cada máquina virtual.  

![Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>Required Number of Azure Cores (Número de Núcleos do Azure Necessários)
Este é o número total de núcleos a aprovisionar antes da ativação pós-falha ou da ativação pós-falha de teste de todas as máquinas virtuais compatíveis. Se não estiverem disponíveis núcleos suficientes na subscrição, o Azure Site Recovery não conseguirá criar máquina(s) virtual(is) no momento da ativação pós-falha de teste ou da ativação pós-falha.

![Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>Required On-premises Infrastructure (Requisitos da Infraestrutura no Local)
É o número total de Servidores de Configuração e de Servidores de Processos adicionais a configurar para proteger todas as máquinas virtuais compatíveis. Com base nos [limites](https://aka.ms/asr-v2a-on-prem-components) suportados na configuração maior, seja a alteração a dados por dia ou o número máximo de máquinas virtuais protegidas (presumindo uma média de três discos por máquina virtual), o que for atingido primeiro no Servidor de Configuração ou no Servidor de Processos adicional, a ferramenta recomenda servidores adicionais. Os detalhes do volume total de alterações a dados por dia e o número total de discos protegidos estão disponíveis na folha [Input](site-recovery-deployment-planner.md#input).

![Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>What If Analysis (Análise de Hipóteses)
Esta análise mostra quantas violações podem ocorrer durante o período de criação de perfis se aprovisionar uma largura de banda inferior para que o RPO pretendido seja cumprido apenas em 90% do tempo. Podem ocorrer uma ou mais violações de RPO num determinado dia - o gráfico mostra o pico do RPO do dia.
Com base nesta análise, pode decidir se o número de violações de RPO de todos os dias e a contagem de picos do RPO por dia é aceitável com a largura de banda inferior especificada. Se for aceitável, pode alocar a menor largura de banda à replicação; caso contrário, aloque a largura de banda superior, conforme sugerido, para cumprir o RPO pretendido em 100% do tempo.

![Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>Tamanho de lote de VMs recomendado para a replicação inicial (Tamanho de lote de VMs recomendado para a replicação inicial)
Esta secção recomenda o número de máquinas virtuais que podem ser protegidas em paralelo para concluir a replicação inicial dentro de 72 horas (valor configurável – utilize o parâmetro GoalToCompleteIR no momento da geração do relatório para alterá-lo) com a largura de banda sugerida para cumprir o RPO pretendido em 100% do tempo aprovisionada.  O gráfico mostra um intervalo de valores de largura de banda e a contagem do tamanho de lote de máquinas virtuais calculado para concluir a replicação inicial em 72 horas com base no tamanho médio das máquinas virtuais detetado de todas as máquinas virtuais compatíveis.  

Na Pré-visualização Pública, o relatório não especifica que máquinas virtuais devem ser incluídas nos lotes. Pode utilizar o tamanho de disco apresentado na folha Compatible VMs para encontrar o tamanho de cada máquina virtual e selecionar as suas máquinas virtuais para um lote ou selecionar com base nas características conhecidas da carga de trabalho.  O tempo de conclusão da replicação inicial altera-se, proporcionalmente, com base no tamanho de disco real das máquinas virtuais, no espaço em disco utilizado e no débito de rede disponível.

![Deployment Planner](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>Growth factor and percentile values used (Fator de crescimento e valores de percentil utilizados)
Esta secção da parte inferior da folha mostra o valor do percentil utilizado em todos os contadores de desempenho das máquinas virtuais com perfis criados (a predefinição é o percentil 95) e o fator de crescimento em % utilizado em todos os cálculos (a predefinição é 30%).

![Deployment Planner](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações tendo a largura de banda disponível como entrada

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Poderá haver casos em que sabe que não pode aprovisionar uma largura de banda com mais de x Mbps para a replicação do Azure Site Recovery. A ferramenta permite-lhe introduzir a largura de banda disponível (mediante a utilização do parâmetro -Bandwidth durante a geração do relatório) e obter o RPO alcançável em minutos. Com este valor de RPO alcançável, pode decidir se precisa de aprovisionar largura de banda adicional ou se não há inconveniente em ter uma solução de recuperação após desastre com este RPO.

![Deployment Planner](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>Input
A página Input proporciona uma descrição geral do ambiente do VMware com perfis criados.

![Deployment Planner](./media/site-recovery-deployment-planner/Input.png)

**Start Date and End Date (Data de Início e Data de Fim)** é a data de início e de fim dos dados de criação de perfis considerados para a geração de relatórios. Por predefinição, a data de início é a data em que a criação dos perfis é iniciada e a de fim é a data em que a criação para.  Podem ser os valores de “StartDate” e “EndDate”, caso o relatório seja gerado com estes parâmetros. Data de Início e Data de Fim: são as datas de início e de fim dos dados de criação de perfis considerados para a geração de relatórios. Por predefinição, a data de início é a data em que a criação dos perfis é iniciada e a de fim é a data em que a criação para.  Podem ser os valores de “StartDate” e “EndDate”, caso o relatório seja gerado com estes parâmetros.

**Total number of profiling days (Número total de dias de criação de perfis)** é o número total de dias de criação de perfis entre as datas de início e de fim para as quais o relatório é gerado. O número total de dias de criação de perfis é o número total de dias de criação de perfis entre as datas de início e de fim para as quais o relatório é gerado.

**Number of compatible virtual machines (Número de máquinas virtuais compatíveis)** é o número total de máquinas virtuais compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas de Armazenamento do Azure, os núcleos do Microsoft Azure, os Servidores de Configuração e os Servidores de Processos adicionais.
“Total number of disks across all compatible virtual machines” (“Número total de discos em todas as máquinas virtuais compatíveis”) é o número total de discos em todas as máquinas virtuais compatíveis. Este número é utilizado como uma das entradas para decidir o número de Servidores de Configuração e de Servidores de Processos adicionais a utilizar na implementação.

**Average number of disks per compatible virtual machine (Número médio de discos por máquina virtual compatível)** é o número médio de discos calculado em todas as máquinas virtuais compatíveis.

**Average disk size (GB) (Tamanho médio de discos [GB])** é o tamanho de discos médio em todas as máquinas virtuais compatíveis.

**Desired RPO (minutes) RPO Pretendido [minutos])** é o RPO predefinido ou o valor transmitido para o parâmetro “DesiredRPO” no momento da geração do relatório, para calcular a largura de banda necessária.

**Desired bandwidth (Mbps) (Largura de Banda Pretendida [Mbps])** é o valor que transmitiu para o parâmetro “Bandwidth” no momento da geração do relatório, para calcular o RPO alcançável.

**Observed typical data churn per day (GB) (Alterações a dados típicas registadas por dia [GB])** é a alteração a dados média registada em todos os dias de criação de perfis. Este número é utilizado como uma das entradas para decidir o número de Servidores de Configuração e de Servidores de Processos adicionais a utilizar na implementação.


##<a name="vm-storage-placement"></a>VM-Storage placement (Colocação de Armazenamento de VMs)

![Deployment Planner](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type (Tipo de Armazenamento de Disco)** é uma conta de Armazenamento do Azure “Standard” ou “Premium” utilizada para replicar todas as máquinas virtuais correspondentes mencionadas na coluna “VMs to Place” (“VMs a Colocar”).

**Suggested Prefix (Prefixo Sugerido)** é o prefixo de três carateres sugerido que pode ser utilizado para dar um nome à conta de Armazenamento do Azure. Pode sempre utilizar o seu próprio prefixo, mas a ferramenta sugere seguir a [convenção de nomenclatura de partições das contas de Armazenamento do Azure](https://aka.ms/storage-performance-checklist).

**Suggested Account Name (Nome de Conta Sugerido)** indica o aspeto que o nome da sua conta de Armazenamento do Azure deve ter depois de incluir o prefixo sugerido. Substitua o nome em <> pela sua entrada personalizada.

**Log Storage Account (Conta de Armazenamento de Registos):** todos os registos de replicação são armazenados numa conta de Armazenamento do Azure standard. Relativamente às máquinas virtuais que estejam a ser replicadas para uma conta de Armazenamento do Azure premium, tem de ser aprovisionada uma conta de Armazenamento do Azure standard adicional para o armazenamento de registos. Uma conta de armazenamento de registos standard individual pode ser utilizada por várias contas de armazenamento de replicações premium. As máquinas virtuais replicadas para contas de armazenamento standard utilizam a mesma conta de armazenamento para os registos.

**Suggested Log Account Name (Nome de Conta de Registos Sugerido)** indica o aspeto que o nome da sua conta de Armazenamento do Azure de registos deve ter depois de incluir o prefixo sugerido. Substitua o nome em <> pela sua entrada personalizada.

**Placement Summary (Resumo da Colocação)** disponibiliza um resumo da carga total de máquinas virtuais na conta de Armazenamento do Azure no momento da replicação e da ativação pós-falha de teste / ativação pós-falha. Inclui o número total de máquinas virtuais mapeadas para a conta de Armazenamento do Azure, o total de IOPS de leitura/escrita em todas as máquinas virtuais que estão a ser colocadas nesta conta de Armazenamento do Azure, o total de IOPS de escrita (replicação), o tamanho total aprovisionado em todos os discos e o número total de discos.

**Virtual Machines to Place (Máquinas Virtuais a Colocar)** apresenta uma lista de todas as máquinas virtuais que devem ser colocadas nesta conta de Armazenamento do Azure específica, para otimizar o desempenho e a utilização.

## <a name="compatible-vms"></a>Compatible VMs (VMs Compatíveis)
![Deployment Planner](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name (Nome da VM)** é o nome ou o endereço IP da máquina virtual conforme utilizado em VMListFile no momento da geração de relatórios. Esta coluna também apresenta os discos (VMDKs) ligados às máquinas virtuais. As máquinas virtuais num vCenter com nomes ou endereços IP duplicados são mencionados com o nome de anfitrião ESXi para distinguir cada máquina virtual. O anfitrião ESXi listado é o anfitrião no qual a máquina virtual foi colocada quando a ferramenta a detetou pela primeira vez durante o período de criação de perfis.

**VM Compatibility (Compatibilidade de VMs)** tem dois valores – Yes (Sim) / Yes (Sim)*. Yes* destina-se aos casos em que a máquina virtual se adequa ao [Armazenamento do Azure premium](https://aka.ms/premium-storage-workload), recaindo o disco de alterações a dados / IOPS elevadas com perfis criados na categoria P20 ou P30, mas em que o tamanho do disco faz com que seja mapeada para a categoria P10 ou P20. Para decidir para que tipo de disco de armazenamento premium mapear um disco, o Armazenamento do Azure baseia-se no respetivo tamanho, ou seja, < 128 GB é P10, 128 a 512 GB é P20 e 512 GB a 1023 GB é P30. Assim, se as características da carga de trabalho de um disco o colocarem num P20 ou P30, mas o tamanho o mapear para um tipo de disco de armazenamento premium inferior, a ferramenta marca essa máquina virtual como Yes* e recomenda-lhe que altere o tamanho do disco de origem, de modo a que se adeque ao tipo de disco de armazenamento premium recomendado, ou que altere o tipo de disco de destino a seguir à ativação pós-falha.
O Tipo de Armazenamento é standard ou premium.

**Suggested Prefix (Prefixo Sugerido)** é o prefixo da conta de Armazenamento do Azure de três carateres

**Storage Account (Conta de Armazenamento)** é o nome que está a utilizar o prefixo sugerido

**R/W IOPS (with Growth Factor) (IOPS de R/W [com Fator de Crescimento])** é o pico de IOPS da carga de trabalho no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30%). Tenha em conta que o total de IOPS de R/W da máquina virtual não será sempre a soma de IOPS de R/W dos discos individuais da máquina virtual, porque o pico de IOPS de R/W da máquina virtual é o pico da soma de IOPS de R/W dos respetivos discos individuais ao longo de todos os minutos do período de criação de perfis.

**Data Churn in Mbps (with Growth Factor) (Alterações a Dados em Mbps [com Fator de Crescimento])** é o pico da taxa de alterações a dados no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30%). Tenha em conta que o total de alterações a dados da máquina virtual não será sempre a soma das alterações a dados dos discos individuais da máquina virtual, porque o pico de alterações a dados da máquina virtual é o pico da soma das alterações a dados dos respetivos discos individuais ao longo de todos os minutos do período de criação de perfis.

**Azure VM Size (Tamanho da VM do Azure)** é o tamanho mapeado ideal da máquina virtual de computação do Azure para esta máquina virtual no local. Este mapeamento é feito com base na memória, no número de discos/núcleos/NICs e no IOPS de R/W da máquina virtual no local. A recomendação é sempre o tamanho de máquina virtual do Azure mais baixo que corresponda a todas estas características da máquina virtual no local.

**Number of Disks (Número de Discos)** é o número total de discos (VMDKs) na máquina virtual

**Disk size (GB) (Tamanho do disco [GB])** é o tamanho total aprovisionado de todos os discos da máquina virtual. A ferramenta também mostra o tamanho dos discos individuais na máquina virtual.

**Cores (Núcleos)** é o número de núcleos de CPU na máquina virtual.

**Memory (MB) (Memória [MB])** é a RAM na máquina virtual.

**NICs** é o número de NICs na máquina virtual.

##<a name="incompatible-vms"></a>Incompatible VMs (VMs Não Compatíveis)

![Deployment Planner](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name (Nome da VM)** é o nome ou o endereço IP da máquina virtual conforme utilizado em VMListFile no momento da geração de relatórios. Esta coluna também apresenta os discos (VMDKs) ligados às máquinas virtuais. As máquinas virtuais num vCenter com nomes ou endereços IP duplicados são mencionados com o nome de anfitrião ESXi para distinguir cada máquina virtual. O anfitrião ESXi listado é o anfitrião no qual a máquina virtual foi colocada quando a ferramenta a detetou pela primeira vez durante o período de criação de perfis.

**VM Compatibility (Compatibilidade de VM)** indica a razão pela qual a máquina virtual especificada é incompatível para utilização com o Azure Site recovery. As razões são descritas por disco incompatível da máquina virtual e podem ser uma das seguintes, com base nos [limites](https://aka.ms/azure-storage-scalbility-performance) do Armazenamento do Azure publicados.

* Tamanho do disco > 1023 GB – atualmente, o Armazenamento do Azure não suporta tamanhos de disco > 1 TB
* O tamanho total da VM (replicação + ativação pós-falha de teste [TFO]) excede o limite de tamanho suportado da conta de Armazenamento do Azure (35 TB) – normalmente, isto acontece quando existe um único disco na máquina virtual que contém algumas características de desempenho que excedem os limites do Microsoft Azure / Azure Site Recovery máximos suportados relativamente ao armazenamento standard, o que empurra a máquina virtual para terrenos do armazenamento premium. No entanto, o tamanho máximo suportado de uma conta de Armazenamento do Azure premium são 35 TB e não é possível proteger máquinas virtuais protegidas individuais em várias contas de armazenamento. Tenha também em conta que, quando é executada uma TFO numa máquina virtual protegida, aquela é executada na mesma conta de armazenamento em que a replicação está a ser processada, pelo que temos de aprovisionar o dobro do tamanho do disco para que a replicação progrida e que a ativação pós-falha seja bem-sucedida em paralelo
* O IOPS de origem excede o limite de IOPS suportado pelo Armazenamento do Azure de 5000 por disco
* O IOPS de origem excede o limite de IOPS suportado pelo Armazenamento do Azure de 80 000 por VM
* A média de alterações a dados excede o limite de alterações a dados suportado pelo Azure Site Recovery de 10 Mbps para tamanho de ES médio para o disco
* O total de alterações a dados em todos os discos na VM excede o limite máximo de alterações a dados suportado pelo Azure Site Recovery de 54 Mbps por VM
* A média de IOPS de escrita efetiva excede o limite de IOPS suportado pelo Azure Site Recovery de 840 por disco
* O armazenamento de instantâneos calculado excede o limite de armazenamento de instantâneos suportado de 10 TB

**R/W IOPS (with Growth Factor) (IOPS de R/W [com Fator de Crescimento])** é o pico de IOPS da carga de trabalho no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30%). Tenha em conta que o total de IOPS de R/W da máquina virtual não será sempre a soma de IOPS de R/W dos discos individuais da máquina virtual, porque o pico de IOPS de R/W da máquina virtual é o pico da soma de IOPS de R/W dos respetivos discos individuais ao longo de todos os minutos do período de criação de perfis.

**Data Churn in Mbps (with Growth Factor) (Alterações a Dados em Mbps [com Fator de Crescimento])** é o pico da taxa de alterações a dados no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30%). Tenha em conta que o total de alterações a dados da máquina virtual não será sempre a soma das alterações a dados dos discos individuais da máquina virtual, porque o pico de alterações a dados da máquina virtual é o pico da soma das alterações a dados dos respetivos discos individuais ao longo de todos os minutos do período de criação de perfis.

**Number of Disks (Número de Discos)** é o número total de discos (VMDKs) na máquina virtual

**Disk size (GB) (Tamanho do disco [GB])** é o tamanho total aprovisionado de todos os discos da máquina virtual. A ferramenta também mostra o tamanho dos discos individuais na máquina virtual.

**Cores (Núcleos)** é o número de núcleos de CPU na máquina virtual.

**Memory (MB) (Memória [MB])** é a RAM na máquina virtual.

**NICs** é o número de NICs na máquina virtual.


##<a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery

**Destino do Armazenamento da Replicação** | **Tamanho Médio de E/S do Disco de Origem** |**Média de Alterações a Dados do Disco de Origem** | **Total de Alterações a Dados do Disco de Origem por Dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco P10 Premium | 8 KB    | 2 MB/s | 168 GB por disco
Disco P10 Premium | 16 KB | 4 MB/s |    336 GB por disco
Disco P10 Premium | 32 KB ou mais | 8 MB/s | 672 GB por disco
Disco P20/P30 Premium | 8 KB    | 5 MB/s | 421 GB por disco
Disco P20/P30 Premium | 16 KB ou mais |10 MB/s    | 842 GB por disco


Estes são números médios, que pressupõem uma sobreposição de 30% de E/S. O Azure Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho. Os números anteriores pressupõem um atraso típico de cerca de cinco minutos, ou seja, depois de carregados, os dados são processados e o ponto de recuperação criado em cinco minutos.

Os limites publicados anteriormente baseiam-se nos nossos testes, mas não abrangem todas as combinações de E/S de aplicações possíveis. Os resultados reais vão variar consoante a combinação de E/S da sua aplicação. Para obter os melhores resultados, mesmo após o planeamento da implementação, é sempre recomendado efetuar testes extensos às aplicações com a ativação pós-falha de teste, para ter a perspetiva verdadeira quanto ao desempenho.

## <a name="how-to-update-the-deployment-planner"></a>Como atualizar o Deployment Planner?
[Transfira](site-recovery-deployment-planner.md#download) a versão mais recente do Azure Site Recovery Deployment Planner. Copie o ficheiro zip para um servidor onde quer executar. Extraia o ficheiro zip.
Se já tiver a versão anterior do Deployment Planner e a criação de perfis não estiver a funcionar, não tem de parar a criação de perfis, a menos que a nova versão tenha uma correção de criação de perfis. Se a versão tiver correções no componente de criação de perfis, é recomendado parar a criação de perfis com a versão mais antiga e iniciar novamente a criação de perfis com a nova versão. Tenha em atenção que, ao iniciar a criação de perfis com a nova versão, tem de introduzir o mesmo caminho de diretório de saída para que a ferramenta acrescente os dados de perfil nos ficheiros existentes e será utilizado o conjunto completo de dados da criação de perfis na geração de relatórios. Se tiver introduzido um diretório de saída diferente, serão criados novos ficheiros e os dados da criação de perfis antigos não podem ser utilizados na geração de relatórios.<br> Cada atualização é uma atualização cumulativa com um ficheiro zip. Não é necessário copiar os novos ficheiros de versão para a pasta de versão anterior para serem utilizados. Pode utilizar a nova pasta tal.


##<a name="version-history"></a>Histórico de Versões
### <a name="11"></a>1.1
Atualizado a: 09-Mar-2017 <br>

Problemas seguintes corrigidos<br>

* Não é possível criar perfis para máquinas virtuais se o vCenter tiver duas ou mais máquinas virtuais com o mesmo nome/endereço IP em diferentes anfitriões ESXi.<br>
* A cópia e a pesquisa foram desativadas para as folhas VMs Compatíveis e VMs Incompatíveis.


### <a name="10"></a>1.0 
Atualizado a: 23-Fev-2017 

O Azure Site Recovery Deployment Planner Public Preview 1.0 tem os problemas conhecidos seguintes, que serão resolvidos em atualizações futuras.

* A ferramenta só funciona no cenário de implementações do VMware para o Azure, não do Hyper-V para o Azure. Para o cenário de implementações do Hyper-V para o Azure, utilize a [ferramenta Capacity Planner do Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* A operação GetThroughput não é suportada nas regiões US Government e China do Microsoft Azure.
* A ferramenta não consegue criar perfis para máquinas virtuais se o vCenter tiver duas ou mais máquinas virtuais com o mesmo nome/endereço IP em diferentes anfitriões ESXi. Nesta versão, a ferramenta ignora a criação de perfis para nomes/endereços IP de máquinas virtuais duplicados em VMListFile. A solução é criar perfis para máquinas virtuais com o anfitrião ESXi em vez do vCenter Server. Tem de executar uma instância para cada anfitrião ESXi.

