<properties
   pageTitle="FAQ sobre o Backup do Azure | Microsoft Azure"
   description="Respostas a perguntas mais frequentes sobre o serviço de cópia de segurança, o agente de cópia de segurança, a cópia de segurança e a retenção, recuperação, segurança e outras perguntas comuns sobre a Cópia de Segurança e a recuperação após desastre."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="recuperação de cópia de segurança e após desastres; serviço de cópia de segurança"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="08/21/2016"
     ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Serviço de Backup do Azure – FAQ

> [AZURE.SELECTOR]
- [FAQ sobre a Cópia de segurança para o modo Clássico](backup-azure-backup-faq.md)
- [FAQ sobre a cópia de segurança do modo Resource Manager](backup-azure-backup-ibiza-faq.md)

Este artigo é uma lista de perguntas mais frequentes (e as respetivas respostas) sobre o serviço de Backup do Azure. A nossa comunidade responde rapidamente e se uma pergunta é colocada frequentemente, adicionamo-la a este artigo. Normalmente, as respostas a perguntas fornecem referência ou informações de suporte. Pode colocar perguntas sobre o Backup do Azure na secção Disqus deste artigo ou um artigo relacionado. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Instalação e Configuração
**P1. Qual é a lista dos sistemas operativos suportados a partir dos quais posso fazer uma cópia de segurança no Azure através do Backup do Azure?** <br/>
R1. O Backup do Azure suporta a seguinte lista de sistemas operativos


| Sistema Operativo        | Plataforma           | SKU  |
| :------------- |-------------| :-----|
| Windows 8 e SPs mais recentes      | 64 bits | Enterprise, Pro |
| Windows 7 e SPs mais recentes      | 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e SPs mais recentes | 64 bits      |    Enterprise, Pro |
| Windows 10      | 64 bits | Enterprise, Pro, Home |
|Windows Server 2012 R2 SPs mais recentes| 64 bits| Standard, Datacenter, Foundation|
|Windows Server 2012 e SPs mais recentes|    64 bits| Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e SPs mais recentes  |64 bits|    Standard, Workgroup|
|Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup
|Windows Server 2012 R2 SPs mais recentes  |64 bits|    Essencial|
|Windows Server 2008 R2 SP1 |64 bits|    Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2    |64 bits|    Standard, Enterprise, Datacenter, Foundation|

**P2. Onde posso transferir o agente do Backup do Azure mais recente?** <br/>
R2. Pode transferir o agente mais recente para efetuar a cópia de segurança do Windows Server, o System Center DPM ou o cliente Windows [aqui](http://aka.ms/azurebackup_agent). Se pretender efetuar a cópia de segurança de uma máquina virtual, utilize o Agente da VM (que instala automaticamente a extensão correta). O Agente da VM já está presente nas máquinas virtuais criadas a partir da galeria do Azure.

**P3. Qual é a versão suportada do servidor SCDPM?** <br/>
R3. Recomendamos que instale o [mais recente](http://aka.ms/azurebackup_agent) agente do Backup do Azure Backup no update rollup mais recente do SCDPM (UR6 em julho de 2015)

**P4. Quando configurar o agente do Backup do Azure, é-me pedido que introduza as **credenciais do cofre**. As credenciais do cofre expiram?
R4. Sim, as credenciais do cofre expiram após 48 horas. Se o ficheiro expirar, inicie sessão no portal do Azure e transfira os ficheiros de credenciais do cofre a partir do cofre da Cópia de Segurança.

**P5. Existe algum limite no número de cofres de cópia de segurança que podem ser criados em cada subscrição do Azure?** <br/>
R5. Sim. A partir de julho de 2015, pode criar 25 cofres por subscrição. Se precisar de mais cofres, crie uma nova subscrição.

**P6. Deve considerar que um cofre é uma entidade de faturação?** <br/>
R6. Embora seja possível obter uma fatura detalhada para cada cofre, recomendamos vivamente que considere uma subscrição do Azure como uma entidade de faturação. É consistente em todos os serviços e é mais fácil de gerir.

**P7. Existem limites no número de servidores/máquinas que podem ser registados em relação a cada cofre?** <br/>
R7. Sim, pode registar até 50 máquinas por cofre. Para máquinas virtuais do IaaS do Azure, o limite é de 200 VMs por cofre. Se precisar de registar mais máquinas, crie um novo cofre.

**P8. Existem limites sobre a quantidade de dados para uma cópia de segurança a partir de um servidor/cliente do Windows ou um servidor SCDPM?** <br/>
R8. Não.

**P9. Como registar o meu servidor para outro datacenter?**<br/>
R9. Os dados de cópia de segurança são enviados para o datacenter do Serviço de Cópia de Segurança no qual está registado. A forma mais fácil de alterar o datacenter consiste em desinstalar e reinstalar o efetuar o registo num novo datacenter.

**P10. O que acontece se mudar o nome de um servidor do Windows que está a efetuar a cópia de segurança dos dados para o Azure?**<br/>
R10. Quando mudar o nome de um servidor, todas as cópias de segurança atualmente configuradas são paradas.
É necessário registar o novo nome do servidor com o cofre de Cópia de Segurança. Quando cria um novo registo, a primeira operação de cópia de segurança é uma cópia de segurança completa e não uma cópia de segurança incremental. Se precisar de recuperar dados de cópias de segurança anteriores no cofre com o nome do servidor antigo, pode recuperar esses dados utilizando a opção [**Outro servidor**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) no assistente **Recuperar Dados**.


**P11. A partir de que tipos de unidades posso criar cópias de segurança de ficheiros e pastas?** <br/>
R11. Não é possível efetuar uma cópia de segurança do seguinte conjunto de unidades/volumes:

- Suporte de dados amovível: a unidade tem de ser reportada como fixa para ser utilizada como a origem do item da cópia de segurança.
- Volumes de Só de Leitura: o volume tem de ser gravável para o serviço de cópia sombra de volumes (VSS) funcionar.
- Volumes Offline: o volume tem de estar online para que o VSS funcione.
- Partilha de rede: o volume tem de ser local no servidor para ser efetuada uma cópia de segurança utilizando a cópia de segurança online.
- Volumes protegidos de BitLocker: o volume tem de ser desbloqueado antes de a cópia de segurança ocorrer.
- Identificação do Sistema de Ficheiros: o NTFS é o único sistema de ficheiros suportado para esta versão do serviço de cópia de segurança online.

**P12. De que tipos de ficheiros e pastas posso criar cópias de segurança a partir do meu servidor?**<br/>
R12. São suportados os seguintes tipos:

- Encriptados
- Comprimidos
- Dispersos
- Comprimidos + dispersos
- Hiperligações: não suportadas, ignoradas
- Ponto de Nova Análise: não suportados, ignorados
- Encriptados + comprimidos: não suportados, ignorados
- Encriptados + dispersos: não suportados, ignorados
- Fluxo Comprimido: não suportado, ignorado
- Fluxo Disperso: não suportado, ignorado

**P13. Qual é o requisito de tamanho mínimo para a pasta da cache?** <br/>
R13. O tamanho da pasta cache determina a quantidade de dados para a cópia de segurança. A pasta cache deve ser 5% do espaço necessário para o armazenamento de dados.

**P14. Se a minha organização tiver um cofre de cópia de segurança, como posso isolar os dados de um servidor a partir de outro servidor ao restaurar os dados?**<br/>
R14. Todos os servidores registados no mesmo cofre podem recuperar os dados de cópias de segurança de outros servidores *que utilizam a mesma frase de acesso*. Se tiver servidores cujos dados de cópia de segurança pretende isolar de outros servidores na sua organização, utilize uma frase de acesso designada para esses servidores. Por exemplo, os servidores de recursos humanos podem utilizar uma frase de acesso de encriptação, os servidores de gestão de contas outra e os servidores de armazenamento uma terceira.

**P15. Posso "migrar" os meus dados da cópia de segurança entre subscrições?** <br/>
R15: Não

**P16. Posso "migrar" o meu cofre de cópia de segurança entre subscrições?** <br/>
R16: Não. O cofre é criado um nível de subscrição e não pode ser reatribuído para outra subscrição quando for criado.

**P17. O Agente do Backup do Azure funciona num servidor que utiliza a eliminação de duplicados do Windows Server 2012?** <br/>
R17: Sim. O serviço do agente converte os dados com duplicados eliminados em dados normais quando prepara a operação de cópia de segurança. Em seguida, otimiza os dados para a cópia de segurança, encripta os dados e, em seguida, envia os dados encriptados para o serviço de cópia de segurança online.

**P18. Se cancelar uma tarefa de cópia de segurança depois de ser iniciada, os dados de cópia de segurança transferidos são eliminados?** <br/>
P18: não. O cofre de cópia de segurança armazena os dados da cópia de segurança que tinham sido transferidos até ao ponto de cancelamento. O Backup do Azure utiliza um mecanismo de ponto de verificação para adicionar, ocasionalmente, pontos de verificação aos dados de cópia de segurança durante a cópia de segurança. Por existirem pontos de verificação nos dados de cópia de segurança, o processo de cópia de segurança seguinte pode validar a integridade dos ficheiros. A cópia de segurança seguinte acionada seria incremental sobre os dados de uma cópia de segurança anterior. Uma cópia de segurança incremental fornece uma melhor utilização da largura de banda para que seja necessário transferir repetidamente os mesmos dados.

**P19. Por que razão estou a ver o aviso de "As Cópias de Segurança do Azure não foram configuradas para este servidor", embora tenha agendado cópias de segurança regulares anteriormente?** <br/>
R19: Este aviso ocorre quando as definições do agendamento de cópias de segurança armazenadas no servidor local não são as mesmas que as definições armazenadas no cofre de cópia de segurança. Quando o servidor ou as definições tenham sido recuperadas para um bom estado conhecido, as agendas de cópia de segurança podem perder a sincronização. Se receber este aviso, [reconfigure a política de cópia de segurança](backup-azure-manage-windows-server.md) e, em seguida, **Execute a Cópia de Segurança Agora** para ressincronizar o servidor local com o Azure.

**P20. Que regras de firewall devem ser configuradas para o Backup do Azure?** <br/>
R20. Para uma proteção totalmente integrada de dados no local para o Azure e da carga de trabalho para o Azure, recomenda-se que autorize a firewall a comunicar com os seguintes URLs:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

**P21. Pode instalar o agente do Backup do Azure numa VM do Azure cuja cópia de segurança já foi efetuada pelo serviço de Backup do Azure através da extensão da VM?** <br/>
R21. Com certeza. O Backup do Azure fornece uma cópia de segurança ao nível da VM para VMs do Azure com a extensão da VM. Pode instalar o agente do Backup do Azure num SO de Windows Convidado para proteger ficheiros e pastas nesse SO convidado.

**P22. Posso instalar o agente do Backup do Azure numa VM do Azure para efetuar uma cópia de segurança de ficheiros e pastas presentes no armazenamento temporário fornecido pela VM do Azure?** <br/>
R22. Pode instalar o agente do Backup do Azure no SO Windows Convidado e efetuar uma cópia de segurança de ficheiros e pastas no armazenamento temporário. No entanto, tenha em atenção que as cópias de segurança falham depois de os dados de armazenamento temporário serem eliminados. Além disso, se os dados de armazenamento temporário tiverem sido eliminados, só pode restaurar para o armazenamento não volátil.

**P23. Instalei o agente do Backup do Azure para proteger os meus ficheiros e pastas. Posso agora a instalar o SCDPM para funcionar com o agente do Backup do Azure para proteger cargas de trabalho de VM/aplicações no local para o Azure?** <br/>
R23. Para utilizar o Backup do Azure com o SCDPM, recomenda-se que instale primeiro o SCDPM e só depois instale o agente do Backup do Azure. Isto assegura uma integração perfeita do agente do Backup do Azure com o SCDPM e permite proteger ficheiros/pastas, VMs e cargas de trabalho de aplicações para o Azure, diretamente a partir da consola de gestão do SCDPM. Instalar o SCDPM depois de instalar o agente do Backup do Azure para os fins mencionados acima não é aconselhado nem suportado.

**P24. Qual é o comprimento do caminho do ficheiro que pode ser especificado como parte da política do Backup do Azure com o agente do Backup do Azure?** <br/>  
R24. O agente do Backup do Azure depende do NTFS. A [especificação do comprimento do caminho do ficheiro está limitada pela API do Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Se efetuar uma cópia de segurança de ficheiros com o comprimento do caminho de ficheiro maior do que o especificado pela API do Windows, os clientes podem optar por criar cópias de segurança da pasta principal ou a unidade de disco dos ficheiros de cópia de segurança.  

**P25. Que carateres são permitidos no caminho de ficheiro da política do Backupdo Azure com o agente do Backup do Azure?** <br>  
R25. O agente do Backup do Azure depende do NTFS. Permite a [carateres suportados pelo NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) como parte da especificação do ficheiro.  

**P26. Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico?** <br/>
R26. Sim.

**P27. Pode configurar o serviço de Cópia de Segurança para enviar correio eletrónico se uma tarefa de cópia de segurança falhar?** <br/>
R27. Sim, o serviço de Cópia de Segurança tem vários alertas com base em eventos que podem ser utilizados com um script do PowerShell. Para obter uma descrição completa, consulte [Notificações de alertas](backup-azure-manage-vms.md#alert-notifications)



## Cópia de Segurança e Retenção
**P1. Existe um limite no tamanho de cada origem de dados para uma cópia de segurança?** <br/>
R1. A partir de agosto de 2015, a origem de dados de tamanho máximo para os sistemas operativos suportados é:

|S.No | Sistema operativo |  Tamanho máximo da origem de dados |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 ou superior| 54400 GB|
|2| Windows 8 ou posterior| 54400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

A tabela seguinte explica a forma como é determinado cada tamanho da origem de dados.

|   Origem de dados  |   Detalhes |
| :-------------: |:-------------|
|Volume |A quantidade de dados para a cópia de segurança a partir de único volume de um servidor ou máquina cliente|
|Máquina virtual do Hyper-V | Soma dos dados de todos os VHDs da máquina virtual para a cópia de segurança|
|Base dados do Microsoft SQL Server | Tamanho da SQL Database única para a cópia de segurança |
|Microsoft SharePoint |Soma das bases de dados de conteúdo e da configuração dentro de um farm do SharePoint para a cópia de segurança|
|Microsoft Exchange |Soma de todas as bases de dados do Exchange num servidor Exchange para a cópia de segurança|
|Estado do Sistema/BMR |Cada cópia individual da BMR ou estado do sistema da máquina para a cópia de segurança|

**P2. Existem limites no número de vezes que pode ser agendada uma tarefa de cópia de segurança por dia?**<br/>
R2. Sim, pode executar tarefas de cópia de segurança no Windows Server ou o cliente Windows três vezes por dia. Pode executar tarefas de cópia de segurança no System Center DPM até duas vezes por dia. Pode executar uma tarefa de cópia de segurança para as VMs do IaaS uma vez por dia.

**P3. Existe uma diferença entre a política de agendamento para o DPM e o Windows Server (ou seja, o Windows Server do DPM)?** <br/>
R3. Sim. Ao utilizar o DPM, pode especificar agendas diárias, semanais, mensais e anuais. O Windows Server (sem o DPM) permite-lhe especificar apenas agendas diárias e semanais.

**P4. Existe alguma diferença entre a política de retenção para o DPM e o Windows Server/cliente Windows (ou seja, no Windows Server sem DPM)?**<br/>
R4. Não, o DPM e o Windows Server/cliente Windows têm políticas de retenção diárias, semanais, mensais e anuais.

**P5. Posso configurar as minhas políticas de retenção seletivamente – por exemplo, configurar políticas de retenção semanais e diárias, mas não anuais e mensais?**<br/>
R5. Sim, a estrutura de retenção do Backup do Azure permite-lhe ter total flexibilidade na definição da política de retenção, de acordo com os seus requisitos.

**P6. Posso "agendar uma cópia de segurança" para as 18:00 e especificar "políticas de retenção" noutra hora?**<br/>
R6. Não. Só podem ser aplicadas políticas de retenção em pontos de cópia de segurança. Na imagem seguinte, a política de retenção é especificada para cópias de segurança criadas às 12:00 e 18:00. <br/>

![Agendar Cópia de Segurança e Retenção](./media/backup-azure-backup-faq/Schedule.png)
<br/>

**P7. Está agendada uma cópia incremental transferida para as políticas de retenção?** <br/>
R7. Não, a cópia incremental é enviada com base na hora referida na página da agenda da cópia de segurança. Os pontos que podem ser mantidos são determinados com base na política de retenção.

**P8. Se uma cópia de segurança for mantida durante um longo período de tempo, demora mais tempo para recuperar um ponto de dados mais antigo?** <br/>
R8. Não – o tempo para recuperar o ponto mais antigo ou mais recente é o mesmo. Cada ponto de recuperação funciona como um ponto completo.

**P9. Se cada ponto de recuperação é como um ponto completo, afeta o armazenamento de cópia de segurança faturável total?**<br/>
R9.  Os produtos de ponto de retenção de longa duração típicos armazenam cópias de segurança como pontos completos. Os pontos completos são *ineficazes* ao nível do armazenamento, mas é mais rápido e mais fácil restaurá-los. As cópias incrementais são *eficientes* ao nível do armazenamento, mas necessitam que restaure uma cadeia de dados, que tem impacto no seu tempo de recuperação. A arquitetura de armazenamento do Backup do Azure dá-lhe o melhor dos dois mundos ao armazenar da melhor maneira dados para restauros rápidos e incorrer em custos de armazenamento reduzido reduzidos. Esta abordagem de armazenamento de dados garante que a largura de banda de entrada e de saída é utilizada com eficácia. A quantidade do armazenamento de dados e o tempo necessário para recuperar os dados são mantidos num mínimo.

**P10. Existe um limite no número de pontos de recuperação que podem ser criados?**<br/>
R10. Não. Eliminamos os limites nos pontos de recuperação. Pode criar os pontos de recuperação que desejar.

**P11. Por que motivo é a quantidade de dados transferidos na cópia de segurança diferente da quantidade de dados da minha cópia de segurança?**<br/>
R11. Todos os dados da cópia de segurança são comprimidos e encriptados antes de serem transferidos. Uma vez aplicada a compressão e a encriptação, os dados no cofre de cópia de segurança são 30-40% mais reduzidos.

**P12. Existe alguma forma de ajustar a quantidade de largura de banda utilizada pelo serviço de Cópia de Segurança?**<br/>
R12. Sim, utilize a opção **Alterar Propriedades** no Agente do Backup para ajustar a largura de banda. Ajuste a quantidade de largura de banda e as horas em que utiliza essa largura de banda. Consulte o artigo [Limitação da Rede](../backup-configure-vault.md#enable-network-throttling) para obter mais informações.

**P13. A minha largura de banda da Internet está limitada à quantidade de dados de que preciso para a cópia de segurança. Existe alguma forma de mover dados para uma determinada localização com um encaminhamento de rede grande e enviar esses dados para o Azure?** <br/>
R13. Pode criar cópias de segurança de dados no Azure através do processo de cópia de segurança online standard ou pode utilizar o serviço Importar/Exportar do Azure para transferir dados para o armazenamento de blobs no Azure. Não existem formas adicionais de obter a data da cópia de segurança para o armazenamento do Azure. Para obter informações sobre como utilizar o serviço Importar/Exportar do Azure com o Backup do Azure, consulte o artigo [Fluxo de trabalho da Cópia de Segurança Offline](backup-azure-backup-import-export.md).


## Recuperação
**P1. Quantas recuperações posso efetuar nos dados da cópia de segurança para o Azure?**<br/>
R1. Não existe limite no número de recuperações do Backup do Azure.

**P2. É necessário pagar o tráfego de saída do datacenter do Azure durante as recuperações?**<br/>
R2. Não. As recuperações são gratuitas e não lhe é cobrado o tráfego de saída.

## Segurança
**P1. Os dados enviados para o Azure são encriptados?** <br/>
R1. Sim. Os dados são encriptados na máquina SCDPM/cliente/servidor no local utilizando AES256 e os dados são enviados através de uma ligação HTTPS segura.

**P2. Os dados da cópia de segurança no Azure também são encriptados?**<br/>
R2. Sim. Os dados enviados para o Azure permanecem encriptados (inativos). A Microsoft não desencripta os dados da cópia de segurança em momento algum.

**P3. Qual é o comprimento mínimo da chave de encriptação utilizado para encriptar os dados da cópia de segurança?** <br/>
R3. A chave de encriptação deve ter pelo menos 16 carateres.

**P4. O que acontece se perder a chave de encriptação? Posso recuperar os dados (ou) pode a Microsoft recuperar os dados?** <br/>
R4. A chave utilizada para encriptar os dados da cópia de segurança está presente apenas no local do cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se o cliente perder a chave, a Microsoft não pode recuperar os dados da cópia de segurança.

**P5. Os dados da cópia de segurança das VMs do Azure estão encriptados?** <br/>
R5. O Backup do Azure baseia-se nas definições de encriptação da VM do Azure para a encriptação das cópias de segurança. Se a VM for encriptada com o Azure Disk Encryption, o Backup do Azure utilizará essa encriptação para proteger os dados. 
 

## Cache da cópia de segurança

**P1. Como posso alterar a localização da cache especificada para o agente do Backup do Azure?**<br/>
R1. Percorra sequencialmente a lista com marcas abaixo para alterar a localização da cache.
- Pare o motor de Cópia de Segurança executando o seguinte comando numa linha de comandos elevada:

  ```PS C:\> Net stop obengine```

- Não mova os ficheiros. Em vez disso, copie a pasta do espaço na cache para outra unidade com espaço suficiente. O espaço da cache original pode ser removido após confirmar que as cópias de segurança estão a trabalhar com o novo espaço da cache.

- Atualize as seguintes entradas de registo com o caminho para a nova pasta de espaço da cache.<br/>

|Caminho do registo | Chave do Registo | Valor |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Nova localização da pasta cache* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Nova localização da pasta cache* |

- Reinicie o motor de Cópia de Segurança executando o seguinte comando numa linha de comandos elevada:

  ```PS C:\> Net start obengine```

  Assim que a criação da cópia de segurança tiver sido concluída com êxito na nova localização da cache, pode remover a pasta cache original.

**P2. Onde posso colocar a pasta cache para o Agente do Backup do Azure para funcionar como esperado?**<br/>
R2. As seguintes localizações para a pasta cache não são recomendadas:

- Suporte de Dados Amovível ou Partilha de Rede: a pasta cache tem de ser local no servidor que necessita da cópia de segurança utilizando a cópia de segurança online. As localizações de rede ou suportes de dados amovíveis como unidades USB não são suportadas.
- Volumes Offline: a pasta cache tem de estar online para a cópia de segurança esperada utilizando o Agente do Backup.

**P3. Existem atributos da pasta cache que não são suportados?**<br/>
R3. Não são suportados os seguintes atributos ou as respetivas combinações para a pasta cache:

- Encriptados
- Duplicados eliminados
- Comprimidos
- Dispersos
- Ponto de Reanálise

Recomenda-se que a pasta cache e os metadados de VHD não tenham os atributos acima para um funcionamento esperado do agente do Backup do Azure.



<!--HONumber=ago16_HO4-->


