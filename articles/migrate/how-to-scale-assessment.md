---
title: "Dimensionar a deteção e a avaliação com o Azure migrar | Microsoft Docs"
description: "Descreve como avaliar grande número de máquinas no local com o serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Detetar e avaliar num ambiente VMware grande

Este artigo descreve como avaliar a grande número de máquinas no local com [Azure migrar](migrate-overview.md). Migrar do Azure avalia máquinas para verificar se serem adequados para a migração para o Azure e fornece estimativas de dimensionamento e custos de execução da máquina no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: precisa de, pelo menos, uma VM de VMware localizada num anfitrião ESXi ou cluster que execute a versão 5.0 ou superior. O anfitrião ou cluster tem de ser gerido pelo servidor vCenter com a versão 5.5 ou 6.0.
- **conta do vCenter**: necessita de uma conta de só de leitura com credenciais de administrador para o servidor vCenter. Migrar do Azure utiliza esta conta para detetar as VMs.
- **Permissões**: no vCenter server, necessita de permissões para criar uma VM ao importar um ficheiro. Formato de OVA.
- **Definições de estatísticas**: as definições de estatísticas para o servidor vCenter, devem ser definidas ao nível 2 ou posterior, antes de começar a implementação.

## <a name="plan-azure-migrate-projects"></a>Planear projetos migrar do Azure

Um projeto do Azure migrar pode avaliar até 1500 máquinas. Uma único deteção num projeto pode detetar até 1000 máquinas.

- Se tiver menos de 1000 máquinas para detetar, terá um único projeto com um único deteção.
- Se tiver entre máquinas 1000 e 1500, precisa de um único projeto com dois deteções no mesmo.
- Se tiver mais de 1500 máquinas, terá de criar vários projetos e realizar várias deteções, de acordo com os seus requisitos. Por exemplo:
    - Se tiver 3000 máquinas, pode configurar dois projetos com deteções de duas ou três projetos com uma deteção único.
    - Se tiver de 5000 máquinas, pode configurar quatro projetos. Duas com uma deteção de máquinas de 1500 e outra com uma deteção de 500 máquinas. Em alternativa, pode configurar cinco projetos com uma única Deteção em cada um deles. 
- Quando fizer uma deteção no migrar do Azure, pode definir o âmbito de deteção para uma pasta VMware, o Centro de dados ou o cluster.
- Para mais do que uma deteção, verifique no vCenter se as VMs que pretende detetar na pastas, centros de dados ou clusters que suportam a limitação de máquinas de 1000.
- Recomendamos que para fins de avaliação, mantenha máquinas com dependências no mesmo projeto e avaliação. Por isso, no vCenter, certifique-se que máquinas dependentes na mesma pasta, Centro de dados ou cluster para fins de avaliação.


## <a name="create-a-project"></a>Criar um projeto

Crie um projeto do Azure migrar de acordo com os seus requisitos.

1. No portal do Azure, clique em **crie um recurso**.
2. Procurar **Azure migrar**e selecione o serviço (**Azure migrar (pré-visualização)** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a subscrição do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especificar a região na qual pretende criar o projeto, em seguida, clique em **criar**. Os metadados reunidos a partir de VMs no local são armazenados nesta região.

## <a name="set-up-the-collector-appliance"></a>Configurar a aplicação de recoletor

Migrar do Azure cria uma VM no local, conhecida como dispositivo de recoletor. Esta VM Deteta as VMs de VMware no local e envia os metadados sobre-los para o serviço Azure migrar. Para configurar a aplicação do recoletor, transfira uma. OVA do ficheiro e importá-lo para o servidor vCenter no local para criar a VM.

### <a name="download-the-collector-appliance"></a>Transferir a aplicação de recoletor

Se tiver vários projetos, apenas terá de transferir a aplicação de recoletor de uma vez para o servidor vCenter. Depois de transferir e configurar a aplicação, pode executá-la para cada projeto e especifique o ID exclusivo do projeto e a chave.

1. No projeto do Azure migrar, clique em **introdução** > **detetar & avaliação** > **detetar máquinas**.
2. No **detetar máquinas**, clique em **transferir**, para transferir o. Ficheiro OVA.
3. No **copie as credenciais de projeto**, copie o ID e a chave para o projeto. Terá de estes quando configurar o recoletor.

   
### <a name="verify-the-collector-appliance"></a>Certifique-se a aplicação de recoletor

Verifique se o. Ficheiro OVA é seguro, antes de implementá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comandos de administrador.
2. Execute o seguinte comando para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de utilização:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas definições.

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>Criar o recoletor de VM

Importe o ficheiro transferido para o servidor vCenter.

1. Na consola do cliente vSphere, clique em **ficheiro** > **implementar o modelo de OVF**.

    ![Implementar OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. No Assistente de implementação de modelo OVF > **origem**, especifique a localização do ficheiro .ova.
3. No **nome** e **localização**, especifique um nome amigável para o recoletor VM e o inventário de objeto que será alojada a VM.
5. No **anfitrião/Cluster**, especifique o anfitrião ou cluster em que o recoletor VM será executado.
7. No armazenamento, especifique o destino de armazenamento para o recoletor VM.
8. No **formato de disco**, especifique o tipo de disco e o tamanho.
9. No **mapeamento da rede**, especifique a rede à qual se ligará o recoletor de VM. A rede necessita de conectividade de internet, para enviar metadados para o Azure. 
10. Reveja e confirme as definições, em seguida, clique em **concluir**.

## <a name="identify-the-key-and-id-for-each-project"></a>Identificar a chave e o ID para cada projeto

Se tiver vários projetos, certifique-se a identificar o ID e a chave de cada um deles. Tem a chave quando executa o recoletor para detetar as VMs.

1. No projeto, clique em **introdução** > **detetar & avaliação** > **detetar máquinas**.
2. No **copie as credenciais de projeto**, copie o ID e a chave para o projeto. 
    ![ID do projeto](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>vCenter nível estatísticas para recolher os contadores de desempenho
Segue-se a lista de contadores que são recolhidos durante a deteção. Os contadores fazem por predefinição disponível ao nível vários no servidor vCenter. Recomendamos que defina o nível mais elevado do comuns (nível 3) para o nível de estatísticas para que todos os contadores de são recolhidos corretamente. Se tiver vCenter definido num nível inferior, apenas alguns contadores podem obter recolhidos completamente, com o resto definido como 0. Por conseguinte, a avaliação poderá mostrar dados incompletos. A tabela abaixo também apresenta uma lista de resultados da avaliação que serão afetados se um determinado contador não é recolhido.

|Contador                                  |Nível    |Por nível de dispositivo  |Impacto de avaliação                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.Average                        | 1       |ND                |Tamanho da VM recomendada e o custo                    |
|Mem.Usage.Average                        | 1       |ND                |Tamanho da VM recomendada e o custo                    |
|virtualDisk.read.average                 | 2       |2                 |Tamanho do disco, o custo de armazenamento e o tamanho da VM         |
|virtualDisk.write.average                | 2       |2                 |Tamanho do disco, o custo de armazenamento e o tamanho da VM         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Tamanho do disco, o custo de armazenamento e o tamanho da VM         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Tamanho do disco, o custo de armazenamento e o tamanho da VM         |
|NET.Received.Average                     | 2       |3                 |Tamanho da VM e rede de custos                        |
|NET.Transmitted.Average                  | 2       |3                 |Tamanho da VM e rede de custos                        |

> [!WARNING]
> Se tiver apenas um nível mais elevado de estatísticas, irá demorar até um dia para gerar os contadores de desempenho. Por conseguinte, é recomendado que execute a deteção depois de um dia.

## <a name="run-the-collector-to-discover-vms"></a>Execute o recoletor para detetar VMs

Para cada deteção terá de efetuar, execute o recoletor para deteção de VMs no âmbito necessário. Após o outro, execute as deteções um. Não são suportadas em simultâneo deteções e cada deteção tem de ter um âmbito diferente.

1. Na consola do cliente vSphere, clique com botão direito a VM > **abrir a consola**.
2. Forneça o idioma, fuso horário e preferências de palavra-passe para a aplicação.
3. No ambiente de trabalho, clique o **executar recoletor** atalho.
4. No Recoletor para migrar do Azure, abra **definir a segurança de pré-requisitos**.
    - Aceite os termos de licenciamento e ler as informações de terceiros.
    - O recoletor verifica se a VM tem acesso à internet.
    - Se a VM acede à internet através de um proxy, clique em **as definições de Proxy**e especifique o endereço de proxy e a porta de escuta. Especificar credenciais, se o proxy tem de autenticação.
    - O recoletor verifica que o serviço de gerador de perfis do Windows está em execução. O serviço está instalado por predefinição no recoletor VM.
    - Transfira e instale o VMware PowerCLI.
. No **detetar máquinas**, efetue o seguinte procedimento:
    - Especifique o nome (FQDN) ou endereço IP do servidor vCenter.
    - No **nome de utilizador** e **palavra-passe**, especifique as credenciais da conta de só de leitura que o recoletor irá utilizar para detetar VMs no vCenter server.
    - No **âmbito de coleção**, selecione um âmbito de deteção VM. O recoletor só pode detetar a VMs dentro do âmbito especificado. Âmbito pode ser definido para uma pasta específica, o Centro de dados ou o cluster. Esta não deve conter mais de 1000 VMs. 
    - n **Categoria da etiqueta para agrupamento**, selecione **nenhum**.

        ![Selecionar âmbito](./media/how-to-scale-assessment/select-scope.png)

1. No **selecione projeto**, especifique o ID e a chave para o projeto. Se não tiver copiá-los, abra o portal do Azure do recoletor VM. No projeto **descrição geral** página, clique em **detetar máquinas**e copie os valores.  
No **deteção concluída**, monitorizar o processo de deteção e verificar esses metadados recolhidos a partir de VMs se encontra no âmbito. O recoletor fornece um período de tempo aproximado de deteção.


### <a name="verify-vms-in-the-portal"></a>Certifique-se as VMs no portal

Deteção de tempo depende quantas VMs está a detetar. Normalmente, para 100 VMs, após a executar o recoletor demora em torno de uma hora para a deteção concluir. 

1. No projeto Planeador de migração, clique em **gerir** > **máquinas**.
2. Verifique se as VMs que pretende detetar de aparecer no portal.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um grupo](how-to-create-a-group.md) para avaliação.
- [Saiba mais](concepts-assessment-calculation.md) sobre como avaliações são calculadas.