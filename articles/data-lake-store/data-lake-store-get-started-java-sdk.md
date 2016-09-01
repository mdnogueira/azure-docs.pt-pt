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
   ms.date="08/18/2016"
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

As ligações seguintes dão-lhe a localização de transferência para o SDK Java do Data Lake Store e as referências do SDK Java. Para este tutorial, não é necessário transferir o SDK ou seguir o documento de referência. Estas ligações são apenas para sua informação.

* O código fonte para o SDK Java para o Data Lake Store está disponível no [GitHub](https://github.com/Azure/azure-sdk-for-java).
* A Referência do SDK Java para o Data Lake Store está disponível em [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Pré-requisitos

* Java Development Kit (JDK) 8 (utilizando a versão 1.8 do Java).
* IntelliJ ou outro ambiente de desenvolvimento Java adequado. Isto é opcional mas recomendado. As instruções abaixo utilizam o IntelliJ.
* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ative a sua subscrição do Azure** para a pré-visualização pública do Data Lake Store. Veja as [instruções](data-lake-store-get-started-portal.md#signup).
* **Criar uma Aplicação do Azure Active Directory**. Existem duas formas de se autenticar com o Azure Active Directory: **interativa** e **não interativa**. Existem pré-requisitos diferentes com base na forma como se pretende autenticar.
    * **Para a autenticação interativa** - No Azure Active Directory, tem de criar uma **aplicação de Cliente Nativo**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente** e o **URI de redirecionamento** para a aplicação
        - Definir permissões delegadas

    * **Para a autenticação não interativa** (utilizada neste artigo) - No Azure Active Directory, tem de criar uma **aplicação Web**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente**, o **segredo de cliente** e o **URI de redirecionamento** da aplicação
        - Definir permissões delegadas
        - Atribua a aplicação do Azure Active Directory a uma função. A função pode estar ao nível do âmbito a que pretende dar permissão à aplicação do Azure Active Directory. Por exemplo, pode atribuir a aplicação ao nível da subscrição ou ao nível de um grupo de recursos.

    Veja [Criar aplicação do Active Directory e um principal de serviço com o portal](../resource-group-create-service-principal-portal.md) para obter instruções sobre como obter estes valores, definir as permissões e atribuir funções.

## Como posso autenticar com o Azure Active Directory?

O fragmento de código abaixo fornece o código para a autenticação **não interativa**, em que a aplicação fornece as suas próprias credenciais.

Para que este tutorial funcione, tem de conceder à sua aplicação permissão para criar recursos no Azure. É **altamente recomendado** que apenas dê a esta aplicação permissões de Contribuidor para um grupo de recursos novo, não utilizado e vazio na sua subscrição do Azure para efeitos deste tutorial.

## Criar uma aplicação Java

1. Abra o IntelliJ e crie um novo projeto Java utilizando o modelo de **Aplicação de Linha de Comandos**. Conclua o assistente para criar o projeto.

2. Abra **Ficheiro** -> **Estrutura do Projeto** -> **Módulos** (em Definições do Projeto) -> **Dependências** -> **+** -> **Biblioteca** -> **De Maven**.

3. Procure os seguintes pacotes Maven e adicione-os ao projeto:

    * com.microsoft.azure:azure-mgmt-datalake-store:1.0.0-beta1.2
    * com.microsoft.azure:azure-mgmt-datalake-store-uploader:1.0.0-beta1.2
    * com.microsoft.azure:azure-client-authentication:1.0.0-beta2

4. A partir do painel esquerdo, navegue para **src**, **main**, **java**, **\<nome do pacote>** e, em seguida, abra **Main.java** e substitua o bloco de código existente pelo seguinte código. Além disso, forneça os valores dos parâmetros realçados no fragmento de código, tais como **localFolderPath**, **DATA-LAKE-STORE-NAME**, **RESOURCE-GROUP-NAME** e substitua os marcadores de posição para **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** e **SUBSCRIPTION-ID** pelas informações sobre a sua subscrição e o respetivo Azure Active Directory. Para obter informações sobre como encontrar estas informações, consulte [o guia do Azure para criar principais de serviço](../resource-group-authenticate-service-principal.md).

    Este código passa pelo processo de criação de uma conta do Data Lake Store, criar ficheiros no arquivo, concatenar ficheiros, transferir um ficheiro e, por fim, a eliminação da conta.

        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreAccountManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreFileSystemManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.store.uploader.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;

        public class Main {
            final static String ADLS_ACCOUNT_NAME = <DATA-LAKE-STORE-NAME>;
            final static String RESOURCE_GROUP_NAME = "<RESOURCE-GROUP-NAME>";
            final static String LOCATION = "East US 2";
            final static String TENANT_ID = "<TENANT-ID>";
            final static String SUBSCRIPTION_ID =  "<SUBSCRIPTION-ID>";
            final static String CLIENT_ID = "<CLIENT-ID>";
            final static String CLIENT_SECRET = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.

            private static DataLakeStoreAccountManagementClientImpl _adlsClient;
            private static DataLakeStoreFileSystemManagementClientImpl _adlsFileSystemClient;

            public static void main(String[] args) throws Exception {
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.

                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(CLIENT_ID, TENANT_ID, CLIENT_SECRET, null);
                SetupClients(creds);

                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");

                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", SUBSCRIPTION_ID));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.accounts().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.name());
                }
                WaitForNewline("Account(s) displayed.", "Uploading file.");

                // Upload a file to Data Lake Store: file1.csv
                UploadFile(localFolderPath + "file1.csv", "/file1.csv");
                WaitForNewline("File uploaded.", "Appending newline.");

                // Append newline to file1.csv
                AppendToFile("/file1.csv", "\r\n");
                WaitForNewline("Newline appended.", "Creating file.");

                // Create a new file in Data Lake Store: file2.csv
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("File created.", "Concatenating files.");

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
                _adlsClient.withSubscriptionId(SUBSCRIPTION_ID);
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
                adlsParameters.withLocation(LOCATION);

                _adlsClient.accounts().create(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME, adlsParameters);
            }

            // Create file
            public static void CreateFile(String path) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path);
            }

            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path, bytesContents, force);
            }

            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().append(ADLS_ACCOUNT_NAME, path, bytesContents);
            }

            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().concat(ADLS_ACCOUNT_NAME, destFilePath, srcFilePaths);
            }

            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().delete(ADLS_ACCOUNT_NAME, filePath);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().getFileStatus(ADLS_ACCOUNT_NAME, path).getBody().fileStatus();
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().listFileStatus(ADLS_ACCOUNT_NAME, directoryPath).getBody().fileStatuses().fileStatus();
            }

            // Upload file
            public static void UploadFile(String srcPath, String destPath) throws Exception {
                UploadParameters parameters = new UploadParameters(srcPath, destPath, ADLS_ACCOUNT_NAME);
                FrontEndAdapter frontend = new DataLakeStoreFrontEndAdapterImpl(ADLS_ACCOUNT_NAME, _adlsFileSystemClient);
                DataLakeStoreUploader uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.execute();
            }

            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, AdlsErrorException {
                InputStream stream = _adlsFileSystemClient.fileSystems().open(ADLS_ACCOUNT_NAME, srcPath).getBody();

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
                _adlsClient.accounts().delete(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME);
            }
        }


6. Execute a aplicação. Siga as instruções para executar e concluir a aplicação.

## Passos seguintes

- [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=ago16_HO4-->


