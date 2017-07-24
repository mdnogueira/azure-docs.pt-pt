# Descrição geral

## [Introdução](storage-introduction.md)
## [Escolher Blobs, Ficheiros ou Discos de Dados](storage-decide-blobs-files-disks.md)

# Introdução

## [Criar uma conta de armazenamento](storage-create-storage-account.md)

## Blob Storage
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [python](storage-python-how-to-use-blob-storage.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Armazenamento de filas
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [PHP](storage-php-how-to-use-queues.md)
### [python](storage-python-how-to-use-queue-storage.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Armazenamento de Tabelas
### [.NET](storage-dotnet-how-to-use-tables.md)
### [F#](/dotnet/articles/fsharp/using-fsharp-on-azure/table-storage)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [python](storage-python-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## [Armazenamento de Ficheiros](storage-files-introduction.md)
### [Portal](storage-file-how-to-use-files-portal.md)
### [.NET](storage-dotnet-how-to-use-files.md)
### [PowerShell](storage-file-how-to-use-files-powershell.md)
### [Windows](storage-file-how-to-use-files-windows.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Mac](storage-file-how-to-use-files-mac.md)  
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [python](storage-python-how-to-use-file-storage.md)
### [Criar uma partilha de ficheiros](storage-file-how-to-create-file-share.md)
### [FAQ](storage-files-faq.md)
## Armazenamento em Disco 
### [Criar uma VM com o Resource Manager e o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)
### [Criar uma VM com Linux através da CLI do Azure 2.0](../virtual-machines/linux/quick-create-cli.md)
### [Anexar um disco gerido a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md)
### [Adicionar um disco gerido a uma VM do Linux](../virtual-machines/linux/add-disk.md)
### [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)
### [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)
### [Utilizar discos geridos em modelos do Resource Manager](storage-using-managed-disks-template-deployments.md)

# Procedimento
## [Criar uma conta de armazenamento](storage-create-storage-account.md)
## Utilizar blobs
### [Descrição geral do serviço](https://msdn.microsoft.com/library/dd179376.aspx)
### [Pesquisar o armazenamento de Blobs com o Azure Search](../search/search-blob-storage-integration.md)
### [Escalão de dados frequentemente acedidos e pouco acedidos](storage-blob-storage-tiers.md)
### [Domínios personalizados](storage-custom-domain-name.md)
### [Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPs](storage-https-custom-domain-cdn.md) 
### [Acesso anónimo para blobs](storage-manage-access-to-resources.md)
### [Amostras](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## Utilizar as filas
### [Conceitos](https://msdn.microsoft.com/library/dd179353.aspx)
### [Amostras](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## Utilizar tabelas
### [Descrição geral](https://msdn.microsoft.com/library/dd179463.aspx)
### [Guia de conceção de tabelas](storage-table-design-guide.md)
### [Amostras](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## Utilizar ficheiros
### [Descrição geral](/rest/api/storageservices/File-Service-Concepts)
### [Resolução de problemas de Ficheiros do Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
### [Resolução de problemas de Ficheiros do Azure - Linux](storage-troubleshoot-linux-file-connection-problems.md)
### [Amostras](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## Utilizar discos
### [Discos e VHDs para VMs do Windows](storage-about-disks-and-vhds-windows.md)
### [Discos e VHDs para VMs do Linux](storage-about-disks-and-vhds-linux.md)
### [Descrição Geral dos Managed Disks do Azure](storage-managed-disks-overview.md)
### [Migrar VMs do Azure para os Managed Disks do Azure](../virtual-machines/windows/migrate-to-managed-disks.md)
### [Migrar do AWS e de outras plataformas para os Managed Disks](../virtual-machines/windows/on-prem-to-azure.md)
### [Perguntas Mais Frequentes acerca dos Discos de VM do IaaS do Azure ](storage-faq-for-disks.md)
### Armazenamento Premium
#### [Armazenamento Premium de elevado desempenho para Discos de VM](storage-premium-storage.md)
#### [Migrar para o Armazenamento Premium através do Azure Site Recovery](storage-migrate-to-premium-storage-using-azure-site-recovery.md)
#### [Conceber o elevado desempenho](storage-premium-storage-performance.md)
### Armazenamento Standard
#### [Armazenamento Standard económico e Discos de VM geridos e não geridos](storage-standard-storage.md)
### Utilizar discos não geridos
#### [Migrar para o Armazenamento Premium](storage-migration-to-premium-storage.md)
#### [Criar cópias de segurança de discos de VM não geridos com instantâneos incrementais](storage-incremental-snapshots.md)
## Planear e conceber
### [Replicação](storage-redundancy.md)
### [Metas de escalabilidade e desempenho](storage-scalability-targets.md)
### [Lista de verificação de desempenho e escalabilidade](storage-performance-checklist.md)
### [Simultaneidade](storage-concurrency.md)
## Programar
### Amostras
#### [.NET](storage-samples-dotnet.md)
#### [Java](storage-samples-java.md)
### [Conceber Aplicações HA com RA-GRS](storage-designing-ha-apps-with-ragrs.md)
### [Configurar cadeias de ligação](storage-configure-connection-string.md)
### [Utilizar o Emulador do Armazenamento do Azure](storage-use-emulator.md)
### [Definir e obter propriedades e metadados](storage-properties-metadata.md)
## Gerir
### [PowerShell](storage-powershell-guide-full.md)
### [CLI 2.0 do Azure](storage-azure-cli.md)
### [CLI do Azure 1.0](storage-azure-cli-nodejs.md)
### [Automatização do Azure](automation-manage-storage.md)
## Proteger
### [Guia de segurança](storage-security-guide.md)
### [Encriptação de dados inativos](storage-service-encryption.md)
### [Encriptação inativa com chaves do cliente](storage-service-encryption-customer-managed-keys.md)
### [Autenticação de chave partilhada](/rest/api/storageservices/Authentication-for-the-Azure-Storage-Services)
### [Assinaturas de acesso partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [Tutorial: Encriptar e desencriptar blobs com o Cofre de Chaves do Azure](storage-encrypt-decrypt-blobs-key-vault.md)
### [Requer transferência segura](storage-require-secure-transfer.md)
### Encriptação do lado do cliente
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [python](storage-client-side-encryption-python.md)
## Monitorizar e resolver problemas
### [Documentação de orientação da recuperação após desastre](storage-disaster-recovery-guidance.md)
### [Cópia de segurança e DR para discos IAAS](storage-backup-and-disaster-recovery-for-azure-iaas-disks.md)
### [Resolução de Problemas do Explorador de Armazenamento](storage-explorer-troubleshooting.md)
### Métricas e registo
#### [Análise de Armazenamento](storage-analytics.md)
#### [Ativar e ver métricas](storage-enable-and-view-metrics.md)
#### [Monitorizar, diagnosticar e resolver problemas](storage-monitoring-diagnosing-troubleshooting.md)
#### [Tutorial de resolução de problemas](storage-e2e-troubleshooting.md)
### Resolver problemas de erros de eliminação do disco
#### [Numa implementação do Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [Numa implementação clássica](storage-cannot-delete-storage-account-container-vhd.md)
### [Resolver problemas de Armazenamento de ficheiros](storage-troubleshoot-file-connection-problems.md)
## Transferir Dados
### [Mover dados de e para o Armazenamento](storage-moving-data.md)
### [AzCopy no Windows](storage-use-azcopy.md)
### [AzCopy no Linux](storage-use-azcopy-linux.md)
### [Utilizar o serviço Importar/Exportar](storage-import-export-service.md)
### [Utilizar a Ferramenta Importar/Exportar](storage-import-export-tool-how-to.md)
#### [Configurar a Ferramenta Importar/Exportar](storage-import-export-tool-setup.md)
#### [Preparar as unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-preparing-hard-drives-import.md)
##### [Definir propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import.md)
##### [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [Referência rápida para comandos utilizados com frequência para tarefas de importação](storage-import-export-tool-quick-reference.md)
#### [Pré-visualização da utilização da unidade para uma tarefa de exportação](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Reparação de uma tarefa de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Resolver problemas da Ferramenta Importar/Exportar](storage-import-export-tool-troubleshooting-v1.md)
#### [Formato de ficheiro do manifesto do serviço Importar/Exportar](storage-import-export-file-format-manifest.md)
#### [Formato de ficheiro dos metadados e propriedades do serviço Importar/Exportar](storage-import-export-file-format-metadata-and-properties.md)
#### [Formato de ficheiro do registo do serviço Importar/Exportar](storage-import-export-file-format-log.md)
### [Utilizar a Ferramenta Importar/Exportar (v1)](storage-import-export-tool-how-to-v1.md)
#### [Configurar a Ferramenta Importar/Exportar](storage-import-export-tool-setup-v1.md)
#### [Preparar as unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [Definir propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [Referência rápida para comandos utilizados com frequência para tarefas de importação](storage-import-export-tool-quick-reference-v1.md)
#### [Pré-visualização da utilização da unidade para uma tarefa de exportação](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Reparação de uma tarefa de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Resolver problemas da Ferramenta Importar/Exportar](storage-import-export-tool-troubleshooting-v1.md)
#### [Formato de ficheiro do manifesto do serviço Importar/Exportar](storage-import-export-file-format-manifest.md)
#### [Formato de ficheiro dos metadados e propriedades do serviço Importar/Exportar](storage-import-export-file-format-metadata-and-properties.md)
#### [Formato de ficheiro do registo do serviço Importar/Exportar](storage-import-export-file-format-log.md)
### [Utilizar a API REST do serviço Importar/Exportar do Azure](storage-import-export-using-the-rest-api.md)
#### [Criação de uma tarefa de importação](storage-import-export-creating-an-import-job.md)
#### [Criação de uma tarefa de exportação](storage-import-export-creating-an-export-job.md)
#### [Obter informações de estado para uma tarefa](storage-import-export-retrieving-state-info-for-a-job.md)
#### [Enumerar tarefas](storage-import-export-enumerating-jobs.md)
#### [Cancelar e eliminar tarefas](storage-import-export-cancelling-and-deleting-jobs.md)
#### [Fazer uma cópia de segurança dos manifestos da unidade](storage-import-export-backing-up-drive-manifests.md)
#### [Recuperação de diagnósticos e erros para tarefas de Importação/Exportação](storage-import-export-diagnostics-and-error-recovery.md)
# Referência
## [PowerShell](/powershell/module/azure.storage)
## [CLI do Azure](/cli/azure/storage)
## .NET
### [Resource Manager](/dotnet/api/microsoft.azure.management.storage)
### [Movimento de dados](/dotnet/api/microsoft.windowsazure.storage.datamovement)
### [Blobs, Filas, Tabelas e Ficheiros](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](http://azure.github.io/azure-storage-java/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [PHP](http://azure.github.io/azure-storage-php/)
## [python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](http://azure.github.io/azure-storage-ios/)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [Blobs, Filas, Tabelas e Ficheiros](/rest/api/storageservices)
### [Fornecedor de recursos](/rest/api/storagerp)
### [Importação/Exportação](/rest/api/storageimportexport)

# Relacionado
## Portal Clássico
### [Criar conta de armazenamento](storage-create-storage-account-classic-portal.md)
### [Ativar e ver métricas](storage-enable-and-view-metrics-classic-portal.md)
### [Monitorizar, diagnosticar e resolver problemas](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Tutorial de resolução de problemas](storage-e2e-troubleshooting-classic-portal.md)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/)
## [Ferramentas de cliente do Armazenamento do Azure](storage-explorers.md)
## [Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Preços](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=storage)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Explorador do Storage do Azure
### [Explorador do Armazenamento (Pré-visualização)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Gerir blobs com o Explorador do Armazenamento (Pré-visualização)](../vs-azure-tools-storage-explorer-blobs.md)
### [Utilizar o Explorador de Armazenamento (Pré-visualização) com o armazenamento de ficheiros do Azure](../vs-azure-tools-storage-explorer-files.md)

## Pacotes NuGet
### [Biblioteca de Clientes do Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Biblioteca do Movimento de Dados do Storage do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Gestor de Configuração do Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## Código de origem
### .NET
#### [Blob, fila, tabela e ficheiro](https://github.com/Azure/azure-storage-net)
#### [Movimento de dados](https://github.com/Azure/azure-storage-net-data-movement)
#### [Fornecedor de recursos](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [python](https://github.com/Azure/azure-storage-python)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [iOS](https://github.com/Azure/azure-storage-ios)
