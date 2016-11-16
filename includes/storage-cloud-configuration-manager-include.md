A [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) fornece uma classe para analisar uma cadeia de ligação a partir de um ficheiro de configuração. A [ classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analisa as definições de configuração, independentemente de a aplicação cliente estar ou não em execução no ambiente de trabalho, num dispositivo móvel, numa máquina virtual do Azure ou num serviço em nuvem do Azure.

Para mencionar o pacote CloudConfigurationManager, adicione a seguinte instrução `using` à classe:

    using Microsoft.Azure;    //Namespace for CloudConfigurationManager

Eis um exemplo que mostra como obter uma cadeia de ligação a partir de um ficheiro de configuração:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

A utilização do Gestor de Configuração do Azure é opcional. Pode também utilizar uma API,como a [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) do .NET Framework.



<!--HONumber=Nov16_HO2-->


