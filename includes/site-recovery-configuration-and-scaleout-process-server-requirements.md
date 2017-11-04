| **Hardware** | |
| --- |---|
| Número de núcleos de CPU| 8 |
| RAM| 12 GB|
| Número de discos | 3 <br><br> - Disco do SO<br> - Disco de cache do servidor de processos<br> - Unidade de retenção (para reativação pós-falha)|
| Espaço livre no disco (cache do servidor de processos) | 600 GB
| Espaço livre no disco (disco de retenção) | 600 GB|
| **Software** | |
| Versão do sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016 |
| Região do sistema operativo | Inglês (en-us)|
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Funções do Windows Server | Não ative as seguintes funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V |
| Políticas de grupo| As seguintes políticas de grupo não devem estar ativadas no servidor <br> -Impedir o acesso à linha de comandos <br> -Impedir o acesso ao registo ferramentas de edição <br> -Confiar lógica para anexos de ficheiros <br> -Ativar a execução do Script <br> **Nota:** podem encontrar mais informações sobre estas políticas de grupo [aqui](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| Configuração de Service(IIS) de informação Internet | -Qualquer Web site predefinido do pré-existente <br> -Ativar [autenticação anónima](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> -Ativar [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) definição  <br> -Não existe nenhuma pré-existente websit/aplicação deve ser escutar na porta 443<br>|
| **Rede** | |
| Tipo de cartão de interface de rede | VMXNET3 |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor deve poder aceder aos seguintes URLs diretamente ou através de um servidor proxy: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (não é necessário para Servidores de Processos de Aumento Horizontal) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)|
