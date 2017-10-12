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
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/28/2017
ms.author: nisoneji
ms.openlocfilehash: 60b0641076c2fa8ed2feb5c64e7b119519f46cf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Este artigo é o manual do utilizador do Azure Site Recovery Deployment Planner para implementações de produção de VMware para o Azure.

## <a name="overview"></a>Descrição geral

Antes de começar a proteger máquinas virtuais do VMware (VMs) com o Site Recovery, tem de alocar largura de banda suficiente com base na sua taxa de alterações de dados diária, de modo a cumprir o objetivo de ponto de recuperação (RPO) pretendido. Certifique-se de que implementa o número certo de servidores de configuração e de servidores de processos no local.

Também tem de criar o tipo e o número certo de contas de armazenamento do Azure de destino. Vai criar contas de armazenamento standard ou premium levando em linha de conta o crescimento dos servidores de produção de origem devido ao aumento da utilização ao longo do tempo. O tipo de armazenamento é escolhido por VM, com base nas características da carga de trabalho (por exemplo, operações de leitura / escrita / E/S por segundo [IOPS] ou alterações a dados) e nos limites do Site Recovery.

A pré-visualização pública do Site Recovery Deployment Planner é uma ferramenta de linha de comandos que está disponível, atualmente, apenas para o cenário de VMware para o Azure. Pode utilizar esta ferramenta para criar remotamente o perfil das VMs de VMware (sem qualquer tipo de impacto na produção) para compreender os requisitos de largura de banda e de armazenamento do Azure necessários para replicação e ativação pós-falha de teste bem-sucedidas. Pode executar a ferramenta sem instalar nenhum componente do Site Recovery no local. Contudo, para obter resultados de débito precisos, recomendamos que execute o Planner num Windows Server que cumpra os requisitos mínimos do servidor de configuração do Site Recovery que terá de, a determinada altura, implementar como um dos primeiros passos na implementação de produção.

A ferramenta disponibiliza os seguintes detalhes:

**Avaliação de compatibilidade**

* Uma avaliação de elegibilidade de VMs com base no número de discos, no tamanho do disco, em IOPS, na alteração a dados e no tipo de arranque (EFI/BIOS)
* A largura de banda de rede estimada necessária para a replicação delta

**Necessidade de largura de banda de rede vs avaliação de RPO**

* A largura de banda de rede estimada necessária para a replicação delta
* O débito que o Site Recovery consegue obter a partir do local para o Azure
* O número de VMs a colocar em lotes, com base na largura de banda estimada para concluir a replicação inicial dentro de um determinado período de tempo.

**Requisitos de infraestrutura do Azure**

* O requisito de tipo de armazenamento (conta armazenamento standard ou premium) para cada VM
* O número total de contas de armazenamento standard e premium a configurar para a replicação
* Sugestões de nomenclatura de contas de armazenamento, com base nas orientações do Armazenamento do Azure
* A colocação da conta de armazenamento para todas as VMs
* O número de núcleos do Azure a configurar antes da ativação pós-falha ou reativação pós-falha de teste na subscrição
* O tamanho recomendado da VM do Azure para cada VM no local

**Requisitos de infraestrutura no local**
* O número necessário de servidores de configuração e de servidores de processos a implementar no local

>[!IMPORTANT]
>
>Uma vez que a utilização é suscetível de aumentar ao longo do tempo, todos os cálculos da ferramenta anteriores são feitos presumindo um fator de crescimento de 30 por cento nas características das cargas de trabalho e utilizando um valor de percentil 95 de todas as métricas de criação de perfis (leitura/escrita, IOPS, alterações a dados e assim sucessivamente). Ambos os elementos (fator de crescimento e cálculo do percentil) são configuráveis. Para saber mais sobre o fator de crescimento, veja a secção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, veja a secção "Valor de percentil utilizado para o cálculo".
>

## <a name="requirements"></a>Requisitos
A ferramenta tem duas fases principais – a criação de perfis e a geração de relatórios. Também existe uma terceira opção, para calcular apenas o débito. Os requisitos para o servidor a partir do qual é iniciada a medição de criação de perfis e do débito são apresentados na tabela seguinte:

| Requisito do servidor | Descrição|
|---|---|
|Medição da criação de perfis e do débito| <ul><li>Sistema operativo: Microsoft Windows Server 2012 R2<br>(que corresponda idealmente, pelo menos, às [recomendações de tamanho do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configuração da máquina : 8 vCPus, 16 GB de RAM, 300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Microsoft Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure a partir deste servidor</li><li>Conta de armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre no disco (presumindo mil VMs com uma média de três discos cada, com perfis criados para 30 dias)</li><li>As definições de nível de estatísticas do VMware vCenter devem ser definidas ao nível 2 ou superior</li><li>Permitir porta 443: o ASR Deployment Planner utiliza esta porta para ligar ao vCenter server/anfitrião ESXi</ul></ul>|
| Geração de relatórios | Um PC Windows ou Windows Server com o Microsoft Excel 2013 ou posterior |
| Permissões de utilizador | Permissão só de leitura para a conta de utilizador utilizada para aceder ao VMware vCenter Server/anfitrião ESXi do VMware vSphere durante a criação do perfil |

> [!NOTE]
>
>A ferramenta só pode criar perfis para VMs com discos VMDK e RDM. Não pode criar perfis para VMs com discos iSCSI ou NFS. O Site Recovery suporta discos iSCSI e NFS para servidores do VMware. Contudo, uma vez que o Deployment Planner não está dentro do convidado e cria perfis apenas com os contadores de desempenho do vCenter, a ferramenta não tem visibilidade para estes tipos de discos.
>

## <a name="download-and-extract-the-public-preview"></a>Transferir e extrair a pré-visualização pública
1. Transfira a versão mais recente da [pré-visualização pública do Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).  
A ferramenta está comprimida numa pasta .zip. A versão atual só suporta o cenário de VMware para o Azure.

2. Copie a pasta .zip para o Windows Server a partir do qual quer executar a ferramenta.  
Pode executá-la no Windows Server 2012 R2 se o servidor tiver acesso à rede para ligar ao vCenter Server/anfitrião ESXi do vSphere que contém as VMs para as quais criar perfis. No entanto, recomendamos que execute num servidor cuja configuração de hardware cumpra a [orientação de tamanho do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components). Se já tiver implementado componentes do Site Recovery no local, execute a ferramenta no servidor de configuração.

 Recomendamos que a configuração do hardware seja igual à do servidor de configuração (que tem um servidor de processos incorporado) no qual vai executar a ferramenta. Esta configuração garante que o débito obtido comunicado pela ferramenta corresponde ao débito real que o Site Recovery pode obter durante a replicação. O cálculo de débito depende da largura de banda de rede disponível na configuração do servidor e do hardware (CPU, armazenamento e assim sucessivamente) do servidor. Se executar a ferramenta a partir de qualquer outro servidor, o débito é calculado a partir desse servidor para o Microsoft Azure. Além disso, uma vez que a configuração de hardware do servidor pode diferir da configuração do servidor de configuração, o débito obtido que a ferramenta comunica poderá estar incorreto.

3. Extraia a pasta .zip.  
Esta contém vários ficheiros e sub-pastas. O ficheiro executável é ASRDeploymentPlanner.exe, na pasta principal.

    Exemplo:  
    Copie o ficheiro .zip para a unidade E:\ e extraia-o.
   E:\ASR Deployment Planner-Preview_v1.2.zip

    E:\ASR Deployment Planner-Preview_v1.2\ ASR Deployment Planner-Preview_v1.2\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>Capacidades
Pode executar a ferramenta de linha de comandos (ASRDeploymentPlanner.exe) num dos três modos seguintes:

1. Criação de perfis  
2. Geração de relatórios
3. Obtenção de débito

Em primeiro lugar, execute a ferramenta no modo de criação de perfis, para recolher IOPS e alterações a dados da VM. Em seguida, execute-a para gerar o relatório de modo a localizar os requisitos de largura de banda de rede e de armazenamento.

## <a name="profiling"></a>Criação de perfis
No modo de criação de perfis, a ferramenta Deployment Planner liga-se ao vCenter Server/anfitrião ESXi do vSphere para recolher dados de desempenho da VM.

* A criação de perfis não afeta o desempenho das VMs de produção, porque não é feita nenhuma ligação direta para as mesmas. Todos os dados de desempenho são recolhidos a partir do vCenter Server/anfitrião ESXi do vSphere.
* Para garantir que a criação de perfis tem um impacto mínimo no servidor, a ferramenta consulta o vCenter Server/anfitrião ESXi do vSphere a cada quinze minutos. Este intervalo de consulta não põe em causa a precisão da criação de perfis, porque a ferramenta armazena os dados dos contadores de desempenho relativos a cada minuto.

### <a name="create-a-list-of-vms-to-profile"></a>Criar uma lista de VMs para as quais criar perfis
Em primeiro lugar, precisa de uma lista das VMs para as quais vão ser criados perfis. Pode utilizar os comandos do VMware vSphere PowerCLI no procedimento seguinte para obter todos os nomes de VMs num vCenter Server/anfitrião ESXi do vSphere. Em alternativa, pode listar, num ficheiro, os nomes amigáveis ou os endereços IP das VMs para as quais quer criar perfis manualmente.

1. Inicie sessão na VM na qual o VMware vSphere PowerCLI está instalado.
2. Abra a consola do VMware vSphere PowerCLI.
3. Confirme que a política de execução está ativada no script. Se estiver desativada, abra a consola do VMware vSphere PowerCLI no modo de administrador e ative-a, executando o comando seguinte:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Opcionalmente, o utilizador pode necessitar executar o seguinte comando, se o Connect-VIServer não for reconhecido como o nome do cmdlet.
 
            Add-PSSnapin VMware.VimAutomation.Core 

5. Para obter todos os nomes de VMs num vCenter Server/anfitrião ESXi do vSphere e armazená-los num ficheiro .txt, execute os dois comandos listados aqui.
Substitua &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo;, &lsaquo;outputfile.txt&rsaquo; pelas suas entradas.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. Abra o ficheiro de saída no Bloco de notas e, em seguida, copie os nomes de todas as VMs para as quais quer criar perfis para outro ficheiro (por exemplo, ProfileVMList.txt), com um nome de VM por linha. Este ficheiro é utilizado como entrada para o parâmetro *-VMListFile* da ferramenta de linha de comandos.

    ![Lista de nomes de VMs no Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>Começar a criar perfis
Quando tiver a lista das VMs para as quais criar perfis, pode executar a ferramenta no modo de criação de perfis. Abaixo, pode ver a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de criação de perfis.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Nome do parâmetro | Descrição |
|---|---|
| -Operation | StartProfiling |
| -Server | O nome de domínio completamente qualificado ou o endereço IP do vCenter Server/anfitrião ESXi do vSphere para cujas VMs vão ser criados perfis.|
| -User | O nome de utilizador para ligar ao vCenter Server/anfitrião ESXi do vSphere. O utilizador tem de ter, pelo menos, acesso só de leitura.|
| -VMListFile | O ficheiro que contém a lista de VMs para as quais criar perfis. O caminho do ficheiro pode ser absoluto ou relativo. Deve conter um nome/endereço IP de VM por linha. O nome da máquina virtual especificado no ficheiro deve ser igual ao nome da VM no vCenter Server/anfitrião ESXi do vSphere.<br>Por exemplo, o ficheiro VMList.txt contém as VMs seguintes:<ul><li>máquina_virtual_A</li><li>10.150.29.110</li><li>máquina_virtual_B</li><ul> |
| -NoOfDaysToProfile | O número de dias durante os quais a criação de perfis deve ser executada. Recomendamos executar a criação de perfis durante mais de 15 dias, para garantir que o padrão da carga de trabalho no seu ambiente ao longo do período especificado é respeitado e utilizado para proporcionar uma recomendação precisa |
| -Directory | (opcional) O caminho Universal Naming Convention (UNC) ou o caminho do diretório local para armazenar os dados de criação de perfis gerados durante a criação. Se não for indicado o nome de um diretório, será utilizado como diretório predefinido o diretório com o nome “ProfiledData” no caminho atual. |
| -Password | (Opcional) A palavra-passe a utilizar para ligar ao vCenter Server/anfitrião ESXi do vSphere. Se não indicar uma agora, ser-lhe-á pedida quando o comando for executado.|
| -StorageAccountName | (Opcional) O nome da conta de armazenamento utilizada para encontrar o débito alcançável para a replicação de dados no local para o Azure. Para calcular o débito, a ferramenta carrega dados de teste para esta conta de armazenamento.|
| -StorageAccountKey | (Opcional) A chave da conta de armazenamento utilizada para aceder à mesma. Aceda a o portal do Azure > Contas de armazenamento > <*Nome da conta de armazenamento*> > Definições > Chaves de Acesso > Chave1 (ou chave de acesso primária para contas de armazenamento clássicas). |
| -Ambiente | (opcional) Este é o seu ambiente da conta de Armazenamento do Azure de destino. Este pode ser um de três valores - AzureCloud, AzureUSGovernment, AzureChinaCloud. A predefinição é AzureCloud. Utilize o parâmetro quando o região do Azure de destino está na cloud do Azure US Government ou do Azure China. |


Recomendamos que crie perfis para VMs durante, pelo menos, 15 a 30 dias. Durante o período de criação de perfis, ASRDeploymentPlanner.exe permanece em execução. A ferramenta aceita a entrada da duração da criação de perfis em dias. Se quiser criar perfis por poucas horas ou minutos para um breve teste à ferramenta, na pré-visualização pública, terá de converter a duração na medida equivalente de dias. Por exemplo, para criar perfis durante 30 minutos, a entrada tem de ser 30/(60*24) = 0,021 dias. O período mínimo de criação de perfis são 30 minutos.

Durante a criação de perfis, pode, opcionalmente, transmitir o nome e a chave de uma conta de armazenamento para encontrar o débito que o Azure Site Recovery pode alcançar no momento da replicação do servidor de configuração ou do servidor de processos para o Azure. Se o nome e a chave da conta de armazenamento não forem transmitidos durante a criação de perfis, a ferramenta não calcula o débito alcançável.

Pode executar várias instâncias da ferramenta em vários conjuntos de VMs. Confirme que não existem nomes de VMs repetidos em nenhum dos conjuntos de criação de perfis. Por exemplo, se tiver criado perfis para dez VMs (VM1 a VM10) e, ao fim de alguns dias, quiser criar perfis para outras cinco (VM11 a VM15), pode executar a ferramenta noutra consola de linha de comandos para o segundo conjunto de VMs (VM11 a VM15). Assegure-se de que o segundo conjunto não tem nenhum nome de VMs da primeira instância de criação de perfis ou de que utiliza outro diretório de saída para a segunda execução. Se forem utilizadas duas instâncias da ferramenta para criar perfis para as mesmas VMs e o mesmo diretório de saída, o relatório gerado estará incorreto.

As configurações das VMs sã capturadas uma vez no início da operação de criação de perfis e armazenadas num ficheiro com o nome VMDetailList.xml. Estas informações são utilizadas quando o relatório é gerado. Não são capturas quaisquer alterações à configuração das VMs (por exemplo, um aumento no número de núcleos, discos ou NICs) desde o início até ao fim da criação de perfis. Caso a configuração de uma VM para a qual foram criados perfis tiver sofrido alterações durante a criação dos perfis, existe, na pré-visualização pública, uma solução para obter os últimos detalhes das VMs durante a geração do relatório:

* Criar uma cópia de segurança de VMdetailList.xml e eliminar o ficheiro da localização atual.
* Transmitir os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfis gera vários ficheiros no diretório de criação de perfis. Não elimine nenhum dos ficheiros, pois tal eliminação afeta a geração de relatórios.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemplo 1: criar perfis para VMs durante 30 dias e encontrar o débito no local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exemplo 2: criar perfis para VMs durante 15 dias

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>Exemplo 3: criar perfis para VMs durante uma hora, para um breve teste à ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* Se o servidor no qual a ferramenta está a ser executada for reiniciado ou falhar ou se utilizar Ctrl + C para fechar a ferramenta, os dados da criação de perfis são preservados. Contudo, esta situação pode levar à perda dos últimos 15 minutos de dados de criação de perfis. Nesse caso, terá de voltar a executar a ferramenta no modo de criação de perfis, quando o servidor for reiniciado.
>* Quando são transmitidos o nome e a chave da conta de armazenamento, a ferramenta mede o débito no último passo da criação de perfis. Se a ferramenta for fechada antes da conclusão da criação de perfis, o débito não é calculado. Para localizar o débito antes de gerar o relatório, pode executar a operação GetThroughput na consola da linha de comandos. Caso contrário, o relatório gerado não irá conter as informações de débito.


## <a name="generate-a-report"></a>Gerar um relatório
A ferramenta gera um ficheiro do Microsoft Excel com permissão para macros (ficheiro XLSM) como o resultado do relatório, que resume todas as recomendações de implementação. O nome do relatório é DeploymentPlannerReport_<*identificador numérico exclusivo*>.xlsm, que é colocado no diretório especificado.

Depois de concluída a criação de perfis, pode executar a ferramenta no modo de geração de relatórios. A tabela seguinte contém a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de geração de relatórios.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nome do parâmetro | Descrição |
|-|-|
| -Operation | GenerateReport |
| -Server |  O nome de domínio completamente qualificado ou o endereço IP do vCenter/vSphere Server (utilize o mesmo nome ou endereço IP que utilizou no momento da criação de perfis) onde estão localizadas as VMs para as quais foram criados perfis e para as quais vão ser gerados relatórios. Tenha em atenção que, se tiver utilizado um vCenter Server no momento da criação de perfis, não pode utilizar um vSphere Server para a geração de relatórios e vice-versa.|
| -VMListFile | O ficheiro que contém a lista de VMs para as quais foram criados perfis e para as quais o relatório vai ser gerado. O caminho do ficheiro pode ser absoluto ou relativo. Deve conter um nome ou endereço IP de VM por linha. Os nomes das VMs especificados no ficheiro devem ser os mesmos que os nomes das VMs no vCenter Server/anfitrião ESXi do vSphere e corresponder ao que estava a ser utilizado no momento da criação de perfis.|
| -Directory | (Opcional) O caminho UNC ou o caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado um nome, será utilizado o diretório “ProfiledData”. |
| -GoalToCompleteIR | (Opcional) O número de horas em que a replicação inicial das VMs para as quais foram criados perfis tem de ser concluída. O relatório gerado mostra o número de VMs cuja replicação inicial pode ser concluída no tempo especificado. A predefinição são 72 horas. |
| -User | (Opcional) O nome de utilizador a utilizar para ligar ao vCenter/vSphere Server. É utilizado para obter as últimas informações de configuração das VMs, como o número de discos, núcleos e NICs, para utilizar no relatório. Se não for indicado o nome, são utilizadas as informações de configuração recolhidas no início da criação de perfis. |
| -Password | (Opcional) A palavra-passe a utilizar para ligar ao vCenter Server/anfitrião ESXi do vSphere. Se a palavra-passe não for indica como parâmetro, ser-lhe-á pedida mais tarde, quando o comando for executado. |
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação pretendido, em minutos. A predefinição são 15 minutos.|
| -Bandwidth | A largura de banda em Mbps. O parâmetro a utilizar para calcular o RPO que pode ser alcançado para a largura de banda especificada. |
| -StartDate | (Opcional) A data e hora de início, em MM-DD-AAAA:HH:MM (no formato de 24 horas). *StartDate* tem de ser especificado juntamente com *EndDate*. Se StartDate for especificado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e hora de fim, em MM-DD-AAAA:HH:MM (no formato de 24 horas). *EndDate* tem de ser especificado juntamente com *StartDate*. Se EndDate for especificado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expresso em percentagem. A predefinição é 30 por cento. |
| -UseManagedDisks | (Opcional) UseManagedDisks - Sim/Não. A predefinição é Sim. O número de máquinas virtuais para a colocação de uma conta de armazenamento única ser calculada considerando se a Ativação pós-falha/Ativação pós-falha de Teste de máquinas virtuais é realizada no disco gerido, em vez do disco não gerido. |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exemplo 1: gerar o relatório com valores predefinidos quando os dados de criação de perfis estão na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exemplo 2: gerar o relatório quando os dados de criação de perfis estão num servidor remoto
Deve ter acesso de leitura/escrita no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exemplo 3: gerar um relatório com largura de banda e objetivos específicos para concluir a replicação inicial no período especificado
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exemplo 4: gerar um relatório com um fator de crescimento de 5 por cento em vez dos 30 por cento predefinidos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exemplo 5: gerar um relatório com um subconjunto de dados de criação de perfis
Por exemplo, tem 30 dias de dados de criação de perfis e quer gerar um relatório para apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Exemplo 6: gerar um relatório para um RPO de cinco minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

## <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil utilizado para o cálculo
**Que valor de percentil predefinido das métricas de desempenho recolhidas durante a criação de perfis é utilizado pela ferramenta no momento em que gera relatórios?**

A ferramenta é predefinida para os valores de percentil de 95 de IOPS de leitura/escrita, IOPS de escrita e alterações a dados recolhidas durante a criação de perfis para todas as VMs. Esta métrica garante que o pico de percentil 100 que as suas VMs poderão verificar devido a eventos temporários não é utilizado para determinar os requisitos de conta de armazenamento de destino e de largura de banda de origem. Por exemplo, um evento temporário pode consistir numa tarefa de cópia de segurança que é executada uma vez por dia, uma base de dados periódica a indexar ou uma atividade de geração de relatórios de atividades ou, ainda, outros eventos pontuais de curta duração semelhantes.

Utilizar valores de percentil 95 proporciona uma imagem verdadeira das características reais das cargas de trabalho e confere-lhe o melhor desempenho se essas cargas de trabalho forem executadas no Azure. Não prevemos que tenha de alterar este número. No entanto, se vier a alterá-lo (para o percentil 90, por exemplo), pode atualizar o ficheiro de configuração *ASRDeploymentPlanner.exe.config* na pasta predefinida e guardá-lo para gerar um relatório novo sobre os dados de criação de perfis existentes.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Considerações sobre o fator de crescimento
**Por que motivo devo ter em conta o fator de crescimento quando planear implementações?**

É fundamental ter em conta o crescimento nas características da sua carga de trabalho, presumindo um potencial aumento na utilização ao longo do tempo. Depois de a proteção estar aplicada, caso as características da sua carga de trabalho sofram alterações, não pode mudar para outra conta de armazenamento para proteção sem desativar e reativar a proteção.

Por exemplo, digamos que, hoje, a sua VM se enquadra numa conta de replicação de armazenamento standard. Ao longo dos três próximos meses, é provável que ocorram várias alterações:

* O número de utilizadores da aplicação que é executada na VM vai aumentar.
* O aumento resultante nas alterações a dados na VM vai exigir que a VM passe para o armazenamento premium, para que a replicação do Site Recovery consiga manter o ritmo.
* Consequentemente, vai ter de desativar e reativar a proteção para uma conta de armazenamento premium.

Recomendamos vivamente que considere o crescimento durante o planeamento da implementação, cujo valor predefinido é 30 por cento. Você é o especialista no que diz respeito ao padrão de utilização da sua aplicação e às projeções de crescimento, pelo que pode alterar este número em conformidade quando gera um relatório. Além disso, pode gerar vários relatórios com diferentes fatores de crescimento com os mesmos dados de criação de perfis e determinar que recomendações de armazenamento de destino e de largura de banda de origem funcionam melhor para si.

O relatório do Microsoft Excel gerado contém as seguintes informações:

* [Input (Entrada)](site-recovery-deployment-planner.md#input)
* [Recommendations (Recomendações)](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Recommendations-Bandwidth Input (Entrada de Recomendações de Largura de Banda)](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Storage Placement (VM<->Colocação de Armazenamento)](site-recovery-deployment-planner.md#vm-storage-placement)
* [Compatible VMs (VMs Compatíveis)](site-recovery-deployment-planner.md#compatible-vms)
* [Incompatible VMs (VMs Não Compatíveis)](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>Obtenção de débito

Para estimar o débito que o Site Recovery pode alcançar no local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula o débito do servidor no qual está a ser executada. Idealmente, este servidor baseia-se na orientação de tamanho do servidor de configuração. Se já tiver implementado componentes da infraestrutura do Site Recovery no local, execute a ferramenta no servidor de configuração.

Abra uma consola da linha de comandos e aceda à pasta da ferramenta Site Recovery Deployment Planner. Execute ASRDeploymentPlanner.exe com os parâmetros seguintes.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nome do parâmetro | Descrição |
|-|-|
| -Operation | GetThroughput |
| -Directory | (Opcional) O caminho UNC ou o caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado o nome de um diretório, é utilizado o diretório “ProfiledData”. |
| -StorageAccountName | O nome da conta de armazenamento utilizado para encontrar a largura de banda consumida para a replicação dos dados no local para o Azure. Para calcular a largura de banda consumida, a ferramenta carrega dados de teste para esta conta de armazenamento. |
| -StorageAccountKey | A chave da conta de armazenamento utilizada para aceder à mesma. Aceda ao portal do Azure > Contas de armazenamento > <*Nome da conta de armazenamento*> > Definições > Chaves de acesso > Chave1 (ou uma chave de acesso primário para contas de armazenamento clássicas). |
| -VMListFile | O ficheiro que contém a lista de VMs para as quais criar perfis para calcular a largura de banda consumida. O caminho do ficheiro pode ser absoluto ou relativo. Deve conter um nome/endereço IP de VM por linha. Os nomes das VMs especificados no ficheiro devem ser iguais aos nomes das VMs no vCenter Server/anfitrião ESXi do vSphere.<br>Por exemplo, o ficheiro VMList.txt contém as VMs seguintes:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Ambiente | (opcional) Este é o seu ambiente da conta de Armazenamento do Azure de destino. Este pode ser um de três valores - AzureCloud, AzureUSGovernment, AzureChinaCloud. A predefinição é AzureCloud. Utilize o parâmetro quando o região do Azure de destino está na cloud do Azure US Government ou do Azure China. |

A ferramenta cria vários ficheiros asrvhdfile<#>.vhd (em que # corresponde ao número de ficheiros) de 64 MB no diretório especificado. Para encontrar o débito, a ferramenta carrega os ficheiros para a conta de armazenamento. Depois de o débito ser medido, elimina todos os ficheiros da conta de armazenamento e do servidor local. Se a ferramenta for terminada por qualquer motivo enquanto está a calcular o débito, não elimina os ficheiros do armazenamento nem do servidor local. Tem de eliminá-los manualmente.

O débito é medido num determinado ponto no tempo e é o débito máximo que o Site Recovery pode alcançar durante a replicação, desde que todos os outros fatores permaneçam iguais. Por exemplo, se uma aplicação começar a consumir mais largura de banda na mesma rede, o débito real varia durante a replicação. Se estiver a executar o comando GetThroughput num servidor de configuração, a ferramenta não está ciente de nenhuma VM protegida nem da replicação em curso. O resultado do débito medido é diferente se a operação GetThroughput for executada quando as VMs protegidas têm elevadas alterações a dados. Recomendamos que execute a ferramenta em vários pontos no tempo durante a criação de perfis, para compreender que níveis de débito podem ser alcançados em vários momentos. No relatório, a ferramenta mostra o último débito medido.

### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Execute a ferramenta num servidor que tenha as mesmas características de armazenamento e CPU que o servidor de configuração.
>
> Para a replicação, defina a largura de banda recomendada para satisfazer o RPO durante 100 por cento do tempo. Se depois de definir a largura de banda certa, não vir um aumento no débito alcançado comunicado pela ferramenta, faça o seguinte:
>
>  1. Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Site Recovery.
>
>  2. Verifique se o cofre do Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede.
>
>  3. Verifique as características do seu armazenamento local para determinar se pode melhorar o hardware (por exemplo, passar de HDD para SSD).
>
>  4. Altere as definições do Site Recovery no servidor de processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="recommendations-with-desired-rpo-as-input"></a>Recomendações com o RPO pretendido como entrada

### <a name="profiled-data"></a>Dados de criação de perfis

![A vista de dados de criação de perfil no Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Período de dados de criação de perfis**: o período durante o qual a criação de perfis foi executada. Por predefinição, a ferramenta utiliza todos os dados de criação de perfis para o cálculo, a não ser gere o relatório para um período específico, mediante a utilização das opções StartDate e EndDate.

**Server Name (Nome do Servidor)**: o nome ou o endereço IP do VMware vCenter ou do anfitrião ESXi para cujas VMs é criado o relatório.

**RPO Pretendido**: o objetivo de ponto de recuperação para a sua implementação. Por predefinição, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na folha. Se tiver utilizado o parâmetro *DesiredRPOinMin* ao gerar o relatório, esse valor é mostrado no resultado Desired RPO (RPO Pretendido).

### <a name="profiling-overview"></a>Descrição geral da criação de perfis

![Resultados da criação de perfis no Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines (Total de Máquinas Virtuais para as quais Foram Criados Perfis)**: o número total de VMs cujos dados de criação de perfis estão disponíveis. Se VMListFile tiver nomes de VMs para as quais não foram criados perfis, essas VMs não são consideradas na geração do relatório e são excluídas da contagem total de VMs para as quais foram criados perfis.

**Compatible Virtual Machines (Máquinas Virtuais Compatíveis)**: o número de VMs que podem ser protegidas para o Azure com o Site Recovery. É o número total de VMs compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos do Azure e o número de servidores de configuração e de servidores de processos adicionais. Os detalhes de todas as VMs compatíveis estão disponíveis na secção “Compatible VMs” (“VMs Compatíveis”).

**Incompatible Virtual Machines (Máquinas Virtuais Não Compatíveis)**: o número de VMs para as quais foram criados perfis que são incompatíveis para proteção com o Site Recovery. Os motivos da incompatibilidade estão apontados na secção “Incompatible VMs” (“VMs Incompatíveis”), abaixo. Se VMListFile tiver nomes de VMs para as quais não foram criados perfis, essas VMs são excluídas da contagem de VMs incompatíveis. Estas VMs são listadas como “Data not found” (“Dados não encontrados”), na parte final da secção “Incompatible VMs”.

**Desired RPO (RPO Pretendido)**: o objetivo de ponto de recuperação pretendido, em minutos. O relatório é gerado para três valores de RPO: 15 (predefinição), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base no que selecionar na lista pendente de Desired RPO, no canto superior direito da folha. Se tiver utilizado o parâmetro *-DesiredRPO* com um valor personalizado para gerar o relatório, este valor personalizado será mostrado como a predefinição na lista pendente de Desired RPO.

### <a name="required-network-bandwidth-mbps"></a>Required network bandwidth (Mbps) (Largura de banda de rede necessária [Mbps])

![Largura de banda de rede necessária no Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100 percent of the time (Para satisfazer o RPO em 100 por cento do tempo):** é a largura de banda recomendada em Mbps a ser alocada para satisfazer o seu RPO pretendido durante 100 por cento do tempo. Esta quantidade de largura de banda tem de estar dedicada à replicação delta de estado estável de todas as VMs compatíveis, para evitar possíveis violações ao RPO.

**To meet RPO 90 percent of the time (Para satisfazer o RPO em 90 por cento do tempo)**: se não puder definir a largura de banda necessária para satisfazer o seu RPO pretendido em 100 por cento do tempo, devido aos preços da largura de banda ou a qualquer outro motivo, pode optar por utilizar uma quantidade de largura de banda inferior que possa satisfazer esse RPO durante 90 por cento do tempo. Para compreender as implicações de definir esta menor largura de banda, o relatório disponibiliza uma análise de hipóteses quanto ao número e à duração das violações de RPO que podem ser esperadas.

**Achieved Throughput (Débito Alcançado):** o débito do servidor onde executou o comando GetThroughput para a região do Microsoft Azure onde está localizada a conta de armazenamento. O número do débito indica o nível aproximado que poderá alcançar se proteger as VMs compatíveis com o Site Recovery, desde que as características de armazenamento e de rede do servidor de configuração ou do servidor de processos permaneçam iguais às do servidor de onde executou a ferramenta.

Para a replicação, deve definir a largura de banda recomendada para satisfazer o RPO durante 100 por cento do tempo. Se depois de definir a largura de banda, não vir um aumento no débito alcançado, conforme comunicado pela ferramenta, faça o seguinte:

1. Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Site Recovery.

2. Verifique se o cofre do Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede.

3. Verifique as características do seu armazenamento local para determinar se pode melhorar o hardware (por exemplo, passar de HDD para SSD).

4. Altere as definições do Site Recovery no servidor de processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se estiver a executar a ferramenta num servidor de configuração ou num servidor de processos que já tenha VMs protegidas, execute-a algumas vezes. O número do débito alcançado altera-se, consoante a quantidade de alterações a dados que estejam a ser processadas nesse momento.

Para todas as implementações empresariais do Site Recovery, recomendamos utilizar o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Required storage accounts (Contas de armazenamento necessárias)
O gráfico seguinte mostra o número total de contas de armazenamento (standard e premium) necessárias para proteger todas as VMs compatíveis. Para obter a conta de armazenamento a utilizar para cada VM, veja a secção "VM-storage placement” (“Colocação de armazenamento de VM").

![Contas de armazenamento necessárias no Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>Required number of Azure cores (Número de núcleos do Azure necessários)
Este resultado é o número total de núcleos a configurar antes da ativação pós-falha ou da ativação pós-falha de teste de todas as VMs compatíveis. Se não estiverem disponíveis núcleos suficientes na subscrição, o Site Recovery não conseguirá criar VMs no momento da ativação pós-falha de teste ou da ativação pós-falha.

![Número de núcleos do Azure necessários no Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>Required on-premises infrastructure (Infraestrutura no local necessária)
Este número é o total de servidores de configuração e de servidores de processos adicionais a configurar e que serão suficientes para proteger todas as VMs compatíveis. Consoante as [recomendações de tamanho do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components) suportadas, a ferramenta poderá recomendar servidores adicionais. A recomendação baseia-se na maior das configurações de alterações a dados por dia ou do número máximo de VMs protegidas (presumindo uma média de três discos por VM), a que for atingida primeiro no servidor de configuração ou no servidor de processos adicional. Os detalhes do número total de alterações a dados por dia e do número total de discos protegidos estão disponíveis na secção “Input” (“Entrada”).

![Infraestrutura no local necessária no Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>Análise de hipóteses
Esta análise mostra quantas violações podem ocorrer durante o período de criação de perfis se configurar uma largura de banda inferior para que o RPO pretendido seja cumprido apenas durante 90 por cento do tempo. Podem ocorrer uma ou mais violações de RPO num determinado dia. O gráfico mostra o pico de RPO do dia.
Com base nesta análise, pode decidir se o número de violações de RPO de todos os dias e a contagem de picos do RPO por dia é aceitável com a largura de banda inferior especificada. Se for aceitável, pode alocar a menor largura de banda à replicação; caso contrário, aloque a largura de banda superior, conforme sugerido, para cumprir o RPO pretendido durante 100 por cento do tempo.

![Análise de hipóteses no Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (Tamanho de lote de VMs recomendado para a replicação inicial)
Nesta secção, recomendamos definir o número de VMs que podem ser protegidas em paralelo, para concluir a replicação inicial em 72 horas com a largura de banda sugerida, de modo a cumprir o RPO pretendido durante 100 por cento. Este valor é configurável. Para alterá-lo durante a geração de relatórios, utilize o parâmetro *GoalToCompleteIR*.

Este gráfico mostra um intervalo de valores de largura de banda e a contagem do tamanho de lotes de VMs calculado para concluir a replicação inicial em 72 horas, com base no tamanho médio das VMs detetado em todas as VMs compatíveis.

Na pré-visualização pública, o relatório não especifica que VMs devem ser incluídas nos lotes. Pode utilizar o tamanho de disco apresentado na secção “Compatible VMs” para encontrar o tamanho de cada VM e selecioná-las para um lote ou pode selecionar as VMs com base nas características conhecidas da carga de trabalho. O tempo de conclusão da replicação inicial altera-se proporcionalmente, com base no tamanho de disco real das VMs, no espaço no disco utilizado e no débito de rede disponível.

![Tamanho de lote de VMs recomendado](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>Growth factor and percentile values used (Fator de crescimento e valores de percentil utilizados)
Esta secção da parte inferior da folha mostra o valor do percentil utilizado em todos os contadores de desempenho das VNs com perfis criados (a predefinição é o percentil 95) e o fator de crescimento (a predefinição é 30 por cento) utilizado em todos os cálculos.

![Growth factor and percentile values used (Fator de crescimento e valores de percentil utilizados)](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações tendo a largura de banda disponível como entrada

![Recomendações tendo a largura de banda disponível como entrada](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Poderá haver casos em que sabe que não pode definir uma largura de banda com mais de x Mbps para a replicação do Site Recovery. A ferramenta permite-lhe introduzir a largura de banda disponível (mediante a utilização do parâmetro -Bandwidth durante a geração do relatório) e obter o RPO alcançável em minutos. Com este valor de RPO alcançável, pode decidir se precisa de configurar largura de banda adicional ou se não há inconveniente em ter uma solução de recuperação após desastre com este RPO.

![RPO alcançável para largura de banda de 500 Mbps](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>Input
A folha Input (Entrada) proporciona uma descrição geral do ambiente do VMware com perfis criados.

![Descrição geral do ambiente do VMware com perfis criados](./media/site-recovery-deployment-planner/Input.png)

**Start Date** (Data de Início) e **End Date** (Data de Fim): as datas de início e fim dos dados de criação de perfis considerados para a geração de relatórios. Por predefinição, a data de início é a data em que a criação dos perfis é iniciada e a de fim é a data em que a criação para. Podem ser os valores de “StartDate” e “EndDate”, caso o relatório seja gerado com estes parâmetros.

**Total number of profiling days (Número total de dias de criação de perfis)**: o número total de dias de criação de perfis entre as datas de início e de fim para as quais o relatório é gerado.

**Number of compatible virtual machines (Número de máquinas virtuais compatíveis)**: o número total de máquinas virtuais compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas armazenamento, os núcleos do Microsoft Azure, os servidores de configuração e os servidores de processos adicionais.

**Total number of disks across all compatible virtual machines (Número total de discos em todas as máquinas virtuais compatíveis)**: o número que é utilizado como uma das entradas para decidir o número de servidores de configuração e de servidores de processos adicionais a utilizar na implementação.

**Average number of disks per compatible virtual machine (Número médio de discos por máquina virtual compatível)**: o número médio de discos calculado em todas as VMs compatíveis.

**Average disk size (GB) (Tamanho médio de discos [GB])**: o tamanho de disco médio calculado em todas as VNs compatíveis.

**Desired RPO (minutes) [RPO Pretendido (minutos)]**: o objetivo de ponto de recuperação predefinido ou o valor transmitido para o parâmetro “DesiredRPO” no momento da geração do relatório, para calcular a largura de banda necessária.

**Desired bandwidth (Mbps) (Largura de Banda Pretendida [Mbps])**: o valor que transmitiu para o parâmetro “Bandwidth” no momento da geração do relatório, para calcular o RPO alcançável.

**Observed typical data churn per day (GB) (Alterações a dados típicas registadas por dia [GB])**: a alteração a dados média registada em todos os dias de criação de perfis. Este número é utilizado como uma das entradas para decidir o número de servidores de configuração e de servidores de processos adicionais a utilizar na implementação.


## <a name="vm-storage-placement"></a>Colocação do armazenamento de VMs

![Colocação do armazenamento de VMs](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type (Tipo de Armazenamento de Disco)**: uma conta de armazenamento standard ou premium, que é utilizada para replicar todas as VMs correspondentes mencionadas na coluna **VMs to Place** (“VMs a Colocar”).

**Suggested Prefix (Prefixo Sugerido)**: o prefixo de três carateres sugerido que pode ser utilizado para dar um nome à conta de armazenamento. Pode utilizar o seu próprio prefixo, mas a sugestão da ferramenta segue a [convenção de nomenclatura de partições para contas de armazenamento](https://aka.ms/storage-performance-checklist).

**Suggested Account Name (Nome de Conta Sugerido)**: o nome da conta de armazenamento depois de incluir o prefixo sugerido. Substitua o nome dentro dos parênteses (< e >) pela sua entrada personalizada.

**Log Storage Account (Conta de Armazenamento de Registos)**: todos os registos de replicações são armazenados numa conta de armazenamento standard. Relativamente a VMs que são replicadas para uma conta de armazenamento premium, configure uma conta de armazenamento standard adicional para o armazenamento de registos. Uma conta de armazenamento de registos standard individual pode ser utilizada por várias contas de armazenamento de replicações premium. As VMs que são replicadas para contas de armazenamento standard utilizam a mesma conta de armazenamento para os registos.

**Suggested Log Account Name (Nome de Conta de Registos Sugerido)**: o nome da conta de registos de armazenamento depois de incluir o prefixo sugerido. Substitua o nome dentro dos parênteses (< e >) pela sua entrada personalizada.

**Placement Summary (Resumo da Colocação)**: um resumo da carga total de VMs na conta de armazenamento no momento da replicação e da ativação pós-falha de teste ou da ativação pós-falha. Inclui o número total de VMs mapeadas para a conta de armazenamento, o total de IOPS de leitura/escrita em todas as VMs que estão a ser colocadas nesta conta de armazenamento, o total de IOPS de escrita (replicação), o tamanho total configurado em todos os discos e o número total de discos.

**Virtual Machines to Place (Máquinas Virtuais a Colocar)**: uma lista de todas as VMs que devem ser colocadas nesta conta de armazenamento específica, para otimizar o desempenho e a utilização.

## <a name="compatible-vms"></a>Compatible VMs (VMs Compatíveis)
![Folha de cálculo do Excel de VMs compatíveis](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name (Nome da VM)**: o nome ou o endereço IP da VM que é utilizado em VMListFile quando é gerado um relatório. Esta coluna também apresenta os discos (VMDKs) que estão ligados às VMs. Para distinguir VMs do vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome de anfitrião ESXi. O anfitrião ESXi listado é aquele em que a VM foi colocada quando a ferramenta detetou durante o período de criação de perfis.

**VM Compatibility (Compatibilidade de VMs)**: os valores são **Yes (Sim)** e **Yes (Não)**\*. **Yes**\* é para instâncias nas quais a VM se enquadra no [Armazenamento Premium do Azure](https://aka.ms/premium-storage-workload). Aqui, o disco de alterações a dados ou IOPS elevados com perfis criados enquadra-se na categoria P20 ou P30, mas o tamanho do mesmo faz com que seja mapeado para P10 ou P20. A conta de armazenamento decide para que tipo de disco de armazenamento premium mapear os discos com base no tamanho destes. Por exemplo:
* < 128 GB é P10.
* 128 GB a 512 GB é P20.
* 512 GB a 1024 GB é P30.
* 1025 GB a 2048 GB é P40.
* 2049 GB a 4095 GB é P50.

Se as características da carga de trabalho de um disco o colocarem na categoria P20 ou P30, mas o tamanho do mesmo o mapear para um tipo de disco de armazenamento premium inferior, a ferramenta marca essa VM como **Yes**\*. Também lhe recomenda que altere o tamanho do disco de origem, para se enquadrar no tipo de disco de armazenamento premium aconselhado, ou que altere o tipo de disco de destino a seguir à ativação pós-falha.

**Storage Type (Tipo de Armazenamento)**: standard ou premium.

**Suggested Prefix (Prefixo Sugerido)**: o prefixo da conta de armazenamento de três carateres.

**Storage Account (Conta de Armazenamento)**: o nome que utiliza o prefixo da conta de armazenamento sugerido.

**R/W IOPS (with Growth Factor) (IOPS de R/W [com Fator de Crescimento])**: o pico de IOPS de leitura/escrita da carga de trabalho no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30 por cento). Tenha em conta que o total de IOPS de leitura/escrita de uma VM nem sempre é a soma de IOPS de leitura/escrita dos discos individuais da mesma, porque o pico destas operações da VM é o pico da soma de IOPS de leitura/escrita dos discos individuais da VM durante todos os minutos do período de criação de perfis.

**Data Churn in Mbps (with Growth Factor) (Alterações a Dados em Mbps [com Fator de Crescimento])**: o pico da taxa de alterações a dados no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30 por cento). Tenha em conta que o total de alterações a dados da VM nem sempre é a soma das alterações a dados dos discos individuais da mesma, porque o pico de alterações a dados da VM é o pico da soma das alterações a dados dos respetivos discos individuais durante todos os minutos do período de criação de perfis.

**Azure VM Size (Tamanho da VM do Azure)**: o tamanho mapeado ideal da máquina virtual dos Serviços Cloud do Azure para esta VM no local. O mapeamento baseia-se na memória da VM no local, no número de discos/núcleos/NICs e IOPS de leitura/escrita. A recomendação é sempre o tamanho de VM do Azure mais baixo que corresponda a todas as características da VM no local.

**Number of Disks (Número de Discos)**: o número total de discos (VMDKs) de máquina virtual na VM.

**Disk size (GB) [Tamanho do disco (GB)]**: o tamanho total de configuração de todos os discos na VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Cores (Núcleos)**: o número de núcleos de CPU na VM.

**Memory (MB)** : a RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de Arranque**: é o tipo de arranque da VM. Pode ser BIOS ou EFI. Atualmente o Azure Site Recovery suporta apenas o tipo de arranque BIOS. Todas as máquinas virtuais do tipo de arranque EFI estão listadas na folha de cálculo das VMs Incompatíveis.

**Tipo de SO**: é o tipo de SO da VM. Pode ser Windows ou Linux ou outro.

## <a name="incompatible-vms"></a>Incompatible VMs (VMs Não Compatíveis)

![Folha de cálculo do Excel de VMs incompatíveis](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name (Nome da VM)**: o nome ou o endereço IP da VM que é utilizado em VMListFile quando é gerado um relatório. Esta coluna também apresenta os VMDKs que estão ligados às VMs. Para distinguir VMs do vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome de anfitrião ESXi. O anfitrião ESXi listado é aquele em que a VM foi colocada quando a ferramenta detetou durante o período de criação de perfis.

**VM Compatibility (Compatibilidade de VM)**: indica a razão pela qual a VM especificada é incompatível para utilização com o Site Recovery. São descritas as razões para todos os discos incompatíveis da VM, que, com base nos [limites do armazenamento](https://aka.ms/azure-storage-scalbility-performance) publicados, podem ser as seguintes:

* O tamanho do disco é >4095 GB. Atualmente, o Armazenamento do Azure não suporta tamanhos de discos superiores a 4095 GB.
* O disco do SO é >2048 GB. Atualmente, o Armazenamento do Azure não suporta tamanhos de discos de SO superiores a 2048 GB.
* O tipo de arranque é EFI. Atualmente o Azure Site Recovery suporta apenas a máquina virtual com o tipo de arranque BIOS.

* O tamanho total da VM (replicação + ativação pós-falha de teste) excede o limite de tamanho da conta de armazenamento suportado (35 TB). Geralmente, esta incompatibilidade ocorre quando um disco individual na VM tem uma característica de desempenho que excede os limites máximos suportados pelo Azure ou o Site Recovery relativamente ao armazenamento standard. Uma instância deste género envia a VM para a zona de armazenamento premium. No entanto, o tamanho máximo suportado das contas de armazenamento premium são 35 TB e não é possível proteger VMs protegidas individuais em várias contas de armazenamento. Tenha também em atenção que, quando é executada uma ativação pós-falha de teste numa VM protegida, esta é executada na mesma conta de armazenamento na qual a replicação está em curso. Neste caso, configure duas vezes o tamanho do disco para que a replicação progrida e a ativação pós-falha de teste seja concluída em paralelo.
* O IOPS de origem excede o limite de IOPS suportado pelo armazenamento de 5000 por disco.
* O IOPS de origem excede o limite de IOPS suportado pelo armazenamento de 80 000 por VM.
* A média de alterações a dados excede o limite de alterações a dados suportado pelo Site Recovery de 10 Mbps para o tamanho de E/S médio para o disco.
* O total de alterações a dados em todos os discos na VM excede o limite máximo de alterações a dados suportado pelo Site Recovery de 54 Mbps por VM.
* A média de IOPS de escrita efetiva excede o limite de IOPS suportado pelo Site Recovery de 840 por disco.
* O armazenamento de instantâneos calculado excede o limite de armazenamento de instantâneos suportado de 10 TB.

**R/W IOPS (with Growth Factor) (IOPS de R/W [com Fator de Crescimento])**: o pico de IOPS da carga de trabalho no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30 por cento). Tenha em conta que o total de IOPS de leitura/escrita da VM nem sempre é a soma de IOPS de leitura/escrita dos discos individuais da mesma, porque o pico destas operações da VM é o pico da soma de IOPS de leitura/escrita dos discos individuais da VM durante todos os minutos do período de criação de perfis.

**Data Churn in Mbps (with Growth Factor) (Alterações a Dados em Mbps [com Fator de Crescimento])**: o pico da taxa de alterações a dados no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30 por cento). Tenha em conta que o total de alterações a dados da VM nem sempre é a soma das alterações a dados dos discos individuais da mesma, porque o pico de alterações a dados da VM é o pico da soma das alterações a dados dos respetivos discos individuais durante todos os minutos do período de criação de perfis.

**Number of Disks (Número de Discos)**: o número total de VMDKs na VM.

**Disk size (GB) [Tamanho do disco (GB)]**: o tamanho total de configuração de todos os discos na VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Cores (Núcleos)**: o número de núcleos de CPU na VM.

**Memory (MB) (Memória [MB])** : a quantidade de RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de Arranque**: é o tipo de arranque da VM. Pode ser BIOS ou EFI. Atualmente o Azure Site Recovery suporta apenas o tipo de arranque BIOS. Todas as máquinas virtuais do tipo de arranque EFI estão listadas na folha de cálculo das VMs Incompatíveis.

**Tipo de SO**: é o tipo de SO da VM. Pode ser Windows ou Linux ou outro.


## <a name="site-recovery-limits"></a>Limites do Site Recovery

**Destino do armazenamento da replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de alterações a dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MBps | 168 GB por disco
Disco P10 Premium | 8 KB | 2 MBps | 168 GB por disco
Disco P10 Premium | 16 KB | 4 MBps | 336 GB por disco
Disco P10 Premium | 32 KB ou superior | 8 MBps | 672 GB por disco
Disco premium P20 ou P30 | 8 KB  | 5 MBps | 421 GB por disco
Disco premium P20 ou P30 | 16 KB ou superior |10 MBps | 842 GB por disco

Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S. O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho. Os números anteriores pressupõem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.

Estes limites baseiam-se nos nossos testes, mas não abrangem todas as combinações de E/S de aplicações possíveis. Os resultados reais podem variar consoante a combinação de E/S da sua aplicação. Para obter os melhores resultados, mesmo após o planeamento da implementação, recomendamos-lhe que faça testes extensos às aplicações com uma ativação pós-falha de teste, para ter a perspetiva verdadeira quanto ao desempenho.

## <a name="updating-the-deployment-planner"></a>Atualizar o Deployment Planner
Para atualizar o Deployment Planner, faça o seguinte:

1. Transfira a versão mais recente do [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).

2. Copie a pasta .zip para um servidor no qual queira executá-la.

3. Extraia a pasta .zip.

4. Efetue um dos seguintes procedimentos:
 * Se a versão mais recente não contiver uma correção de criação de perfis e a criação de perfis já estiver em curso na sua versão atual da ferramenta, continue com a mesma.
 * Se a versão mais recente contiver essa correção, recomendamos-lhe que pare a criação de perfis na versão atual e que a reinicie na nova.

  >[!NOTE]
  >
  >Quando iniciar a criação de perfis com a nova versão, transmita o mesmo caminho do diretório de saída, para que a ferramenta anexe os dados da criação de perfis nos ficheiros existentes. Para gerar o relatório, vai ser utilizado um conjunto completo de dados de criação de perfis. Se transmitir um diretório de saída diferente, são criados ficheiros novos e os dados antigos não são utilizados para gerar o relatório.
  >
  >Cada Deployment Planner novo é uma atualização acumulativa do ficheiro .zip. Não tem de copiar os ficheiros mais recentes para a pasta anterior. Pode criar e utilizar uma pasta nova.


## <a name="version-history"></a>Histórico de versões

### <a name="131"></a>1.3.1
Última atualização: 19 de julho de 2017

É adicionada a nova funcionalidade a seguir:

* Adicionado suporte para discos grandes (> 1 TB) na geração de relatórios. Agora, pode utilizar o planeador de implementações para planear a replicação de máquinas virtuais com tamanhos de discos superiores a 1 TB (até 4095 GB).
Leia mais sobre o [Suporte de discos grandes no Azure Site Recovery](https://azure.microsoft.com/en-us/blog/azure-site-recovery-large-disks/)


### <a name="13"></a>1.3
Atualização: 9 de maio de 2017

É adicionada a nova funcionalidade a seguir:

* Foi adicionado suporte de disco gerido na geração de relatórios. O número de máquinas virtuais que pode ser colocado numa conta de armazenamento única é calculado com base em se o disco gerido disco está selecionado para Ativação pós-falha/Ativação pós-falha de teste.        


### <a name="12"></a>1.2
Atualização: 7 de abril de 2017

Foram adicionadas as correções seguintes:

* Verificação de tipo de arranque (BIOS ou EFI) adicionada a cada máquina virtual, para determinar se a máquina virtual é compatível ou incompatível para a proteção.
* Informações do tipo do SO adicionadas para cada máquina virtual em folhas de cálculo das VMs Compatíveis e Incompatíveis.
* A operação GetThroughput é agora suportada nas regiões US Government e China do Microsoft Azure.
* Foram adicionadas mais verificações de pré-requisitos para o vCenter e o Servidor ESXi.
* O relatório incorreto estava a ser gerado quando as definições de região estavam definidas como não sendo em inglês.


### <a name="11"></a>1.1
Atualização: 9 de março de 2017

Foram corrigidos os problemas seguintes:

* A ferramenta não consegue criar perfis para VMs se o vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões ESXi.
* A cópia e a pesquisa foram desativadas nas folhas Compatible VMs (VMs Compatíveis) e Incompatible VMs (VMs Incompatíveis).

### <a name="10"></a>1.0
Atualização: 23 de fevereiro de 2017

A pré-visualização pública do Azure Site Recovery Deployment Planner 1.0 tem os problemas conhecidos seguintes (que vão ser resolvidos em atualizações futuras):

* A ferramenta só funciona em cenários de implementações do VMware para o Azure, não do Hyper-V para o Azure. Em cenários de implementações do Hyper-V para o Azure, utilize a [ferramenta Capacity Planner do Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* A operação GetThroughput não é suportada nas regiões US Government e China do Microsoft Azure.
* A ferramenta não consegue criar perfis para VMs se o vCenter Server tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões ESXi. Nesta versão, a ferramenta ignora a criação de perfis para nomes ou endereços IP de VMs duplicados em VMListFile. A solução é criar perfis para as VMs com um anfitrião ESXi em vez do vCenter Server. Tem de executar uma instância para cada anfitrião ESXi.
