<properties
   pageTitle="Utilizar o SDK Java do Data Lake Store para desenvolver aplicações | Azure"
   description="Utilizar o SDK Java do Azure Data Lake Store para desenvolver aplicações"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Introdução ao Azure Data Lake Store com Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar o SDK Java do Azure Data Lake Store para criar uma conta do Azure Data Lake e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

## SDK Java do Azure Data Lake Store

As ligações seguintes dão-lhe a localização de transferência para o SDK Java do Data Lake Store e as referências do SDK Java. Para este tutorial não é necessário transferir o SDK ou seguir o documento de referência. Estas ligações são apenas para sua informação.

* O código fonte para o SDK Java para o Data Lake Store está disponível no [GitHub](https://github.com/Azure/azure-sdk-for-java).
* A Referência do SDK Java para o Data Lake Store está disponível em [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Pré-requisitos

* Java Development Kit (JDK) 8 (utilizando a versão 1.8 do Java).
* IntelliJ ou outro ambiente de desenvolvimento Java adequado. Isto é opcional mas recomendado. As instruções abaixo utilizam o IntelliJ.
* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ative a sua subscrição do Azure** para a pré-visualização pública do Data Lake Store. Veja as [instruções](data-lake-store-get-started-portal.md#signup).
* **Criar uma Aplicação do Azure Active Directory**. Existem duas formas para se autenticar com o Azure Active Directory - **interativa** e **não interativa**. Existem pré-requisitos diferentes com base na forma como se pretende autenticar.
    * **Para a autenticação interativa** - No Azure Active Directory, tem de criar uma **aplicação de Cliente Nativo**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente** e o **URI de redirecionamento** para a aplicação
        - Definir permissões delegadas

    * **Para a autenticação não interativa** (utilizada neste artigo) - No Azure Active Directory, tem de criar uma **aplicação Web**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente**, o **segredo de cliente** e o **URI de redirecionamento** para a aplicação
        - Definir permissões delegadas
        - Atribua a aplicação do Azure Active Directory a uma função. A função pode estar ao nível do âmbito a que pretende dar permissão à aplicação do Azure Active Directory. Por exemplo, pode atribuir a aplicação ao nível da subscrição ou ao nível de um grupo de recursos. 

    Veja [Criar aplicação do Active Directory e um principal de serviço com o portal](../resource-group-create-service-principal-portal.md) para obter instruções sobre como obter estes valores, definir as permissões e atribuir funções.

## Como posso autenticar com o Azure Active Directory?

O fragmento de código abaixo fornece o código para a autenticação **não interativa**, em que a aplicação fornece as suas próprias credenciais.

Precisa de conceder a permissão para a aplicação criar recursos no Azure para que este tutorial funcione. É **altamente recomendado** que apenas dê permissões de Contribuidor da aplicação a um grupo de recursos novo, não utilizado e vazio na sua subscrição do Azure para os efeitos deste tutorial.

## Criar uma aplicação Java

1. Abra o IntelliJ e crie um novo projeto Java utilizando o modelo de **Aplicação de Linha de Comandos**. Conclua o assistente para criar o projeto.

2. Clique com o botão direito no lado esquerdo do ecrã e clique em **Adicionar Suporte para Framework**. Escolha **Maven** e clique em **OK**.

3. Abra o ficheiro **"pom.xml"** recentemente criado e adicione o seguinte fragmento de texto entre a etiqueta **\</version >** e a etiqueta **\</project >**:

    >[AZURE.NOTE] Este passo é temporário até que o SDK do Azure Data Lake Store esteja disponível no Maven. Este artigo será atualizado depois de o SDK estar disponível no Maven. Todas as atualizações futuras para este SDK estarão disponíveis através do Maven.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Vá para **Ficheiro**, **Definições** e, em seguida, **Compilação, Execução e Implementação**. Expanda **Ferramentas de Criação**, **Maven** e, em seguida, expanda **Importar**. Selecione a caixa de verificação para **Importar automaticamente projetos do Maven**. Clique em **Aplicar** e, em seguida, em **OK**.

5. A partir do painel esquerdo, navegue para **src**, **main**, **java**, **\<nome do pacote>** e, em seguida, abra **Main.java** e substitua o bloco de código existente pelo seguinte código. Além disso, forneça os valores para os parâmetros realçados no fragmento de código como **localFolderPath**, **_adlsAccountName**, **_resourceGroupName** e substitua os marcadores de posição para **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** e **SUBSCRIPTION-ID**.

    Este código passa pelo processo de criação de uma conta do Data Lake Store, criar ficheiros no arquivo, concatenar ficheiros, transferir um ficheiro e, por fim, a eliminação da conta.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Execute a aplicação. Siga as instruções para executar e concluir a aplicação.

## Passos seguintes

- [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


