O serviço Azure Backup tem dois tipos de cofres – o cofre das Cópias de Segurança e o cofre dos Serviços de Recuperação. O cofre das Cópias de Segurança foi criado primeiro. Em seguida, foi criado o cofre dos Serviços de Recuperação para suportar as implementações expandidas do Resource Manager. A Microsoft recomenda a utilização de implementações Resource Manager, exceto se precisar especificamente de uma implementação Clássica.

| **Implementação** | **Portal** | **Cofre** |
| --- | --- | --- |
| Clássica |[Clássico](https://manage.windowsazure.com) |Cópia de segurança |
| Resource Manager |[Azure](https://portal.azure.com) |Serviços de Recuperação |

> [!NOTE]
> Os cofres das Cópia de Segurança não podem proteger soluções implementadas pelo Resource Manager. Contudo, pode utilizar um cofre dos Serviços de Recuperação para proteger os servidores e as VMs com implementação clássica.  
> 
> 

