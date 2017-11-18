
Os seguintes limites aplicam-se a cópia de segurança do Azure.

| Identificador de limite | Limite Predefinido |
| --- | --- |
| Número de servidores/máquinas que podem ser registados em relação a cada Cofre |50 para Windows Server SCDPM/cliente <br/> 200 para as VMs do IaaS |
| Tamanho de uma origem de dados para dados armazenados no armazenamento de cofre do Azure |Máx. de 54400 GB<sup>1</sup> |
| Número de cofres de cópia de segurança que podem ser criados em cada subscrição do Azure |25 (cofres de cópia de segurança) <br/> 25 cofres dos serviços de recuperação por região |
| Número de vezes que a cópia de segurança pode ser agendada por dia |3 por dia para o Windows Server/cliente Windows <br/> 2 por dia para SCDPM <br/> Uma vez por dia para as VMs do IaaS |
| Discos de dados ligados a uma máquina virtual do Azure para cópia de segurança |16 |
| Tamanho do disco de dados individuais ligado a uma máquina virtual do Azure para cópia de segurança| 1023 GB <sup>2</sup>|

* <sup>1</sup>o limite de 54400 GB não é aplicável a cópia de segurança de VM do IaaS.
* <sup>2</sup> temos um [pré-visualização privada](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) para suportar os discos não gerido até 4 TB. 

