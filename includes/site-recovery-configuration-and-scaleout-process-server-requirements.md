| **Hardware** | |
| --- |---|
| Número de núcleos de CPU| 8 |
| RAM| 12 GB|
| Número de discos | 3 <br><br> - Disco do SO<br> - Disco de cache do servidor de processos<br> - Unidade de retenção (para reativação pós-falha)|
| Espaço livre no disco (cache do servidor de processos) | 600 GB
| Espaço livre no disco (disco de retenção) | 600 GB|
| **Software** | |
| Versão do sistema operativo | Windows Server 2012 R2 |
| Região do sistema operativo | Inglês (en-us)|
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0 "PowerCLI 6.0")|
| Funções do Windows Server | Não ative as seguintes funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V |
| **Rede** | |
| Tipo de cartão de interface de rede | VMXNET3 |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor deve poder aceder aos seguintes URLs diretamente ou através de um servidor proxy: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (não é necessário para Servidores de Processos de Aumento Horizontal) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)|


<!--HONumber=Feb17_HO3-->


