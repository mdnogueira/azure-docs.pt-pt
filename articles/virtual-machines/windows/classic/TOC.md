# Descrição geral
## [Sobre máquinas virtuais](../../virtual-machines-windows-about.md)
## [Discos e VHDs](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [Redes virtuais](../../../virtual-network/virtual-networks-overview.md)
## [FAQ](faq.md)
## [Comparar VMs, Web sites e serviços cloud](../../../app-service-web/choose-web-site-cloud-service-vm.md)
## [Contentores](../../virtual-machines-windows-containers.md)

# Introdução
## [Criar uma VM com o portal](tutorial.md)
## [Iniciar sessão numa VM](connect-logon.md)
## [Instalar o Azure PowerShell](/powershell/azure/overview)
## [Instalar a CLI do Azure](../../../cli-install-nodejs.md)

# Procedimento

## Utilizar o Armazenamento
### [Anexar um disco de dados](attach-disk.md)
### [Desanexar um disco de dados](detach-disk.md)
### [Utilizar D: como um disco de dados](../../virtual-machines-windows-change-drive-letter.md)

## Rede
### [Como configurar pontos finais](setup-endpoints.md)
### [Ligar VMs com uma VNet ou um Serviço Cloud](connect-vms.md)
### [Ligar VNets Clássicas a VNets do Resource Manager](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [Criar um balanceador de carga](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Gerir NSGs com o Azure PowerShell](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## Implementação
### [Criar uma VM personalizada](createportal.md)
### [Criar e configurar uma VM com o Azure PowerShell](create-powershell.md)
### [Capturar uma VM do Windows](capture-image.md)
### [Criar e carregar um VHD com o PowerShell](createupload-vhd.md)
### [Automatizar a implementação de VMs do Azure com o Chef](../../virtual-machines-windows-chef-automation.md)
### [Criar e gerir VMs no Visual Studio](manage-visual-studio.md)
### [Criar uma VM para uma aplicação Web com o Visual Studio](web-app-visual-studio.md)
### [Executar uma tarefa de computação intensiva em Java](java-run-compute-intensive-task.md)
### [Aplicação Web Django Hello World](python-django-web-app.md)

## Configurar
### [Repor uma palavra-passe ou o serviço Ambiente de Trabalho Remoto](../../virtual-machines-windows-reset-rdp.md)
### [Instalar e configurar o Symantec Endpoint Protection](install-symantec.md)
### [Instalar e configurar o Trend Micro Deep Security como Serviço](install-trend.md)
### [Configurar um conjunto de disponibilidade](configure-availability.md)
### [Redimensionar uma VM do Windows criada no modelo de implementação clássica](resize-vm.md)
### [Manutenção](planned-maintenance-schedule.md)

## Gerir
### [Migrar da implementação Clássica para Resource Manager](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Gerir as VMs com o Azure PowerShell](manage-psh.md)
### [Acerca dos agentes e das extensões de VM](agents-and-extensions.md)
### [Gerir extensões de VM](manage-extensions.md)
### [Extensão de script personalizado para VMs](extensions-customscript.md)
### [Injetar dados personalizados numa VM do Azure](inject-custom-data.md)

## Planear
### [Acerca das imagens](about-images.md)
### [Tamanhos de VMs](../../virtual-machines-windows-sizes.md)
### [Manutenção planeada para VMs do Azure](../../virtual-machines-windows-planned-maintenance.md)
### [Diretrizes de implementação dos serviços de infraestrutura do Azure](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## Gerir as cargas de trabalho
### [Computação de Alto Desempenho (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [Dimensionar recursos automaticamente](hpcpack-cluster-node-autogrowshrink.md)
#### [Gerir nós de computação](hpcpack-cluster-node-manage.md)
#### [Criar um cluster](hpcpack-cluster-powershell-script.md)
#### [Configurar um cluster para executar aplicações MPI](hpcpack-rdma-cluster.md)
#### [Executar cargas de trabalho do Excel e SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [Criar o nó principal com uma imagem do Mercado](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [Submeter tarefas do local para o Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](../../workloads/oracle/oracle-considerations.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## Resolução de problemas
### [Ligações do Ambiente de Trabalho Remoto](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[Passos de resolução de problemas detalhados para problemas de ligação de ambiente de trabalho remoto](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [Acesso a uma aplicação](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [Implementação clássica ao criar uma VM nova](troubleshoot-deployment-new-vm.md)
### [Implementação clássica ao reiniciar ou redimensionar uma VM nova](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
### [Repor palavra-passe de RDP](reset-rdp.md)
### [Anexar o disco rígido virtual para resolver problemas de VMs](troubleshoot-recovery-disks-portal.md)

# Referência
## [PowerShell](/powershell/azure/overview)
## [CLI do Azure](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [Criar modelos do Resource Manager](../../../resource-group-authoring-templates.md)
## [Modelos da comunidade](https://azure.microsoft.com/documentation/templates)
## [REST de Computação](/rest/api/compute)
## [REST de Rede](/rest/api)
## [REST de Armazenamento](/rest/api/storageservices)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=compute)
## [Preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [Disponibilidade regional](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
