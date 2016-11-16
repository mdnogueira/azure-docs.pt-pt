# Descrição geral

## [Introdução](storage-introduction.md)

# Introdução

## [Guia de início rápido](storage-getting-started-guide.md)
## [Criar uma conta de armazenamento](storage-create-storage-account.md)

## Blob Storage
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Armazenamento de filas
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Armazenamento de Tabelas
### [.NET](storage-dotnet-how-to-use-tables.md)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## Armazenamento de Ficheiros
### [Windows, .NET, PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [python](storage-python-how-to-use-file-storage.md)



# Procedimento

## [Criar uma conta de armazenamento](storage-create-storage-account.md)

## Utilizar blobs
### [Descrição geral do serviço](https://msdn.microsoft.com/library/dd179376.aspx)
### [Escalão de dados frequentemente acedidos e pouco acedidos](storage-blob-storage-tiers.md)
### [Domínios personalizados](storage-custom-domain-name.md)
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
### [Descrição geral](https://msdn.microsoft.com/en-us/library/dn166972.aspx)
### [Resolução de problemas de Ficheiros do Azure](storage-troubleshoot-file-connection-problems.md)
### [Amostras](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)

## Utilizar Discos da Máquina Virtual
### Armazenamento Premium
#### [Armazenamento de elevado desempenho para cargas de trabalho de VM](storage-premium-storage.md)
#### [Migrar para o Armazenamento Premium](storage-migration-to-premium-storage.md)
#### [Conceber o elevado desempenho](storage-premium-storage-performance.md)
### Armazenamento Standard
#### [Criar cópias de segurança de discos da VM com instantâneos incrementais](storage-incremental-snapshots.md)

## Planear e conceber
### [Replicação](storage-redundancy.md)
### [Metas de escalabilidade e desempenho](storage-scalability-targets.md)
### [Lista de verificação de desempenho e escalabilidade](storage-performance-checklist.md)
### [Simultaneidade](storage-concurrency.md)

## Programar
### [Amostras](storage-samples.md)
### [Configurar cadeias de ligação](storage-configure-connection-string.md)
### [Utilizar o Emulador do Armazenamento do Azure](storage-use-emulator.md)
### [Definir e obter propriedades e metadados](storage-properties-metadata.md)

## Gerir
### [PowerShell](storage-powershell-guide-full.md)
### [CLI do Azure](storage-azure-cli.md)
### [Automatização do Azure](automation-manage-storage.md)

## Proteger
### [Guia de segurança](storage-security-guide.md)
### [Encriptação de dados inativos](storage-service-encryption.md)
### [Autenticação de chave partilhada](https://msdn.microsoft.com/library/dd179428.aspx)
### [Assinaturas de acesso partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [Tutorial: Encriptar e desencriptar blobs com o Cofre de Chaves do Azure](storage-encrypt-decrypt-blobs-key-vault.md)
### Encriptação do lado do cliente
#### [Cofre de Chaves do Azure](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [python](storage-client-side-encryption-python.md)

## Monitorizar e resolver problemas
### Métricas e registo
#### [Análise de Armazenamento](storage-analytics.md)
#### [Ativar e ver métricas](storage-enable-and-view-metrics.md)
#### [Monitorizar, diagnosticar e resolver problemas](storage-monitoring-diagnosing-troubleshooting.md)
#### [Tutorial de resolução de problemas](storage-e2e-troubleshooting.md)
### Resolver problemas de erros de eliminação do disco
#### [Numa implementação do Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [Numa implementação clássica](storage-cannot-delete-storage-account-container-vhd.md)
### [Resolver problemas de Armazenamento de ficheiros](storage-troubleshoot-file-connection-problems.md)
### [Documentação de orientação da recuperação após desastre](storage-disaster-recovery-guidance.md)

## Transferir Dados
### [Mover dados de e para o Armazenamento](storage-moving-data.md)
### [Utilitário de linha de comandos do AzCopy](storage-use-azcopy.md)
### [Serviço de Importação/Exportação para o Armazenamento de Blobs](storage-import-export-service.md)



# Referência
## [PowerShell](/powershell/storage/)
## [CLI do Azure](/cli/azure/storage)
## [Java](/java/api/)

## .NET
### [Blob, fila, tabela e ficheiro](https://msdn.microsoft.com/library/azure/mt347887.aspx)
### [Fornecedor de recursos](/dotnet/api/)
### [Movimento de dados](/dotnet/api/)

## REST
### [Blob, fila, tabela e ficheiro](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)
### [Fornecedor de recursos](/rest/api/storagerp/)
### [Importação/exportação](/rest/api/storageservices/importexport/storage-import-export-service-rest-api-reference)


# Relacionado
## Portal Clássico
### [Criar conta de armazenamento](storage-create-storage-account-classic-portal.md)
### [Ativar e ver métricas](storage-enable-and-view-metrics-classic-portal.md)
### [Monitorizar, diagnosticar e resolver problemas](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Tutorial de resolução de problemas](storage-e2e-troubleshooting-classic-portal.md)



# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Ferramentas de cliente do Armazenamento do Azure](storage-explorers.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=storage)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Explorador do Storage do Azure
### [Explorador do Armazenamento (Pré-visualização)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Gerir blobs com o Explorador do Armazenamento (Pré-visualização)](../vs-azure-tools-storage-explorer-blobs.md)

## Pacotes Nuget
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
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [Python](https://github.com/Azure/azure-storage-python)
### [iOS](https://github.com/Azure/azure-storage-ios)


<!--HONumber=Nov16_HO2-->


