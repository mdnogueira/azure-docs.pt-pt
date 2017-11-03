---
title: "Execução de local do U-SQL de escala e teste com o SDK do Azure Data Lake U-SQL | Microsoft Docs"
description: "Saiba como utilizar o SDK do Azure Data Lake U-SQL para tarefas de escala U-SQL local são executadas e teste com linha de comandos e interfaces de programação na estação de trabalho local."
services: data-lake-analytics
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: yanacai
ms.openlocfilehash: 55242bcf644ca0e7f30cfe7eada2130451c36e64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-u-sql-local-run-and-test-with-azure-data-lake-u-sql-sdk"></a>Execução de local do U-SQL de escala e teste com o SDK do Azure Data Lake U-SQL

Ao desenvolver scripts U-SQL, é comum para executar e teste U-SQL script localmente antes submetê-las com a nuvem. Data Lake do Azure fornece um pacote Nuget chamado SDK do Azure Data Lake U-SQL para este cenário, através do qual pode facilmente dimensionar a execução local do U-SQL e o teste. Também é possível integrar este teste U-SQL com o sistema de CI (integração contínua) para automatizar a compilação e testar.

Se que mais lhe interessam como manualmente local para executar e depurar o script U-SQL com ferramentas de GUI, pode utilizar as ferramentas do Azure Data Lake para Visual Studio para esse. Pode saber mais de [aqui](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>SDK de instalação do Azure Data Lake U-SQL

Pode obter o SDK do Azure Data Lake U-SQL [aqui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) no Nuget.org. E antes de o utilizar, tem de certificar-se de que tem dependências da seguinte forma.

### <a name="dependencies"></a>Dependências

O SDK do Data Lake U-SQL requer as seguintes dependências:

- [Microsoft .NET Framework 4.6 ou mais recente](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e o Windows SDK 10.0.10240.0 ou mais recente (o que é chamado CppSDK neste artigo). Existem duas formas de obter CppSDK:

    - Instalar [edição do Visual Studio Community](https://developer.microsoft.com/downloads/vs-thankyou). Terá de uma pasta de \Windows Kits\10 sob a pasta de ficheiros de programa – por exemplo, C:\Program Files (x86) \Windows Kits\10\. Também irá encontrar a versão do SDK do Windows 10 em \Windows Kits\10\Lib. Se não vir estas pastas, reinstale o Visual Studio e lembre-se de que seleciona o SDK do Windows 10 durante a instalação. Se tiver este instalado com o Visual Studio, o compilador local do U-SQL irá considerar automaticamente.

    ![Ferramentas do Data Lake para Visual Studio execução local SDK do Windows 10](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Instalar [Data Lake Tools para Visual Studio](http://aka.ms/adltoolsvs). Pode encontrar o Visual C++ e o Windows SDK prepackaged ficheiros em C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Neste caso, o compilador local do U-SQL não é possível localizar as dependências automaticamente. Tem de especificar o caminho de CppSDK para o mesmo. Pode copiar os ficheiros para outra localização ou utilizá-lo como está.

## <a name="understand-basic-concepts"></a>Compreender os conceitos básicos

### <a name="data-root"></a>Raiz dos dados

A pasta de raiz dos dados é um "armazenamento local" para a conta de computação local. É equivalente à conta do Azure Data Lake Store de uma conta de Data Lake Analytics. Mudar para uma pasta raiz de dados diferentes é semelhante à mudança para uma conta de armazenamento diferentes. Se pretender aceder a dados normalmente partilhados com pastas de raiz de dados diferente, tem de utilizar caminhos absolutos os scripts. Ou, criar ligações simbólicas de sistema de ficheiros (por exemplo, **mklink** no NTFS) na pasta raiz dos dados para que apontem para os dados partilhados.

A pasta raiz dos dados é utilizada para:

- Armazenar metadados locais, incluindo bases de dados, tabelas, as funções valorizadas por tabela (TVFs) e assemblagens.
- Procure os caminhos de entrada e de saída que são definidos como caminhos relativos em U-SQL. Utilizar caminhos relativos torna mais fácil de implementar os seus projetos de U-SQL no Azure.

### <a name="file-path-in-u-sql"></a>Caminho do ficheiro em U-SQL

Pode utilizar um caminho relativo e um caminho absoluto local scripts U-SQL. O caminho relativo é relativo ao caminho da pasta de raiz de dados especificado. Recomendamos que utilize "/" como o separador de caminho para tornar os scripts compatível com o lado do servidor. Seguem-se alguns exemplos de caminhos relativos e os respetivos caminhos absolutos equivalentes. Nestes exemplos, C:\LocalRunDataRoot é a pasta de raiz dos dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/Input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/Input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/Input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script U-SQL localmente, um diretório de trabalho é criado durante a compilação sob o diretório de execução atual. Para além das saídas de compilação, os ficheiros de tempo de execução necessário para a execução local será copiada para este diretório de trabalho de sombra de volumes. A pasta raiz do diretório de trabalho é chamada "ScopeWorkDir" e os ficheiros no diretório de trabalho são os seguintes:

|Diretório/ficheiro|Diretório/ficheiro|Diretório/ficheiro|Definição|Descrição|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Cadeia de hash de versão do tempo de execução|Cópia de sombra de volumes de ficheiros de tempo de execução necessário para a execução local|
| |Script_66AE4909AA0ED06C| |Nome do script + cadeia do caminho do script de hash|Saídas de compilação e execução do passo de registo|
| | |\_script\_.abr|Saída do compilador|Ficheiro algebra|
| | |\_ScopeCodeGen\_. *|Saída do compilador|Gerar código gerido|
| | |\_ScopeCodeGenEngine\_. *|Saída do compilador|Código nativo gerado|
| | |assemblagens referenciadas|Referência de assemblagem|Ficheiros de assemblagem referenciada|
| | |deployed_resources|Implementação de recursos|Ficheiros de implementação de recursos|
| | |XXXXXXXX.xxx[1..n]\_\*. *|Registo de execução|Registo de passos de execução|


## <a name="use-the-sdk-from-the-command-line"></a>Utilize o SDK na linha de comandos

### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comandos da aplicação do programa auxiliar

SDK directory\build\runtime, LocalRunHelper.exe é a aplicação de linha de comandos do programa auxiliar que fornece interfaces para a maioria das funções de execução local comummente utilizadas. Tenha em atenção que o comando e os comutadores de argumento diferenciam maiúsculas de minúsculas. A invocá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Executar LocalRunHelper.exe sem argumentos ou com o **ajudar** mudar para mostrar as informações de ajuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

As informações de ajuda:

-  **Comando** fornece o nome do comando.  
-  **Necessário argumento** apresenta uma lista de argumentos tem de ser fornecidos.  
-  **O argumento opcional** apresenta uma lista de argumentos são opcionais, com valores predefinidos.  Os argumentos opcionais booleanos não tem parâmetros e as respetivas appearances significam negativo no respectivo valor predefinido.

### <a name="return-value-and-logging"></a>Valor de retorno e o registo

Devolve a aplicação de programa auxiliar **0** para êxito e **-1** falha. Por predefinição, o programa auxiliar envia todas as mensagens para a consola atual. No entanto, a maioria dos comandos suporta o **- MessageOut path_to_log_file** argumento opcional que redireciona as saídas para um ficheiro de registo.

### <a name="environment-variable-configuring"></a>Configurar a variável de ambiente

Local do U-SQL executar necessidades uma raiz de dados especificada como conta de armazenamento local, bem como um caminho de CppSDK especificado para as dependências. Pode ambos definir o argumento na variável de ambiente de linha de comandos ou foi definido para os mesmos.

- Definir o **SCOPE_CPP_SDK** variável de ambiente.

    Se obtiver o Microsoft Visual C++ e o Windows SDK, instalando as ferramentas do Data Lake para Visual Studio, certifique-se de que tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definir uma nova variável de ambiente chamada **SCOPE_CPP_SDK** para apontar para este diretório. Ou copie a pasta para outra localização e especifique **SCOPE_CPP_SDK** que.

    Além de definir a variável de ambiente, pode especificar o **- CppSDK** argumento quando estiver a utilizar a linha de comandos. Este argumento substitui a variável de ambiente de CppSDK predefinido.

- Definir o **LOCALRUN_DATAROOT** variável de ambiente.

    Definir uma nova variável de ambiente chamada **LOCALRUN_DATAROOT** que aponta para a raiz dos dados.

    Além de definir a variável de ambiente, pode especificar o **- DataRoot** argumento com o caminho raiz dos dados quando estiver a utilizar uma linha de comandos. Este argumento substitui a variável de ambiente de raiz dos dados de predefinição. Tem de adicionar este argumento para cada linha de comandos que está a executar, pelo que pode substituir a variável de ambiente de raiz de dados predefinida para todas as operações.

### <a name="sdk-command-line-usage-samples"></a>Exemplos de utilização de linha de comandos do SDK

#### <a name="compile-and-run"></a>Compilar e executar

O **executar** comando é utilizado para compilar o script e, em seguida, executar resultados compilados. Os argumentos da linha de comandos são uma combinação desses de **compilar** e **executar**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Seguem-se os argumentos opcionais para **executar**:


|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-CodeBehind|Falso|O script tem CS code-behind|
|-CppSDK| |Diretório de CppSDK|
|-DataRoot| Variável de ambiente de DataRoot|DataRoot para execução predefinido à variável de ambiente 'LOCALRUN_DATAROOT' local|
|-MessageOut| |Mensagens de informações na consola para um ficheiro|
|-Paralelo|1|Executar o plano com o paralelismo especificado|
|-Referências| |Lista de caminhos para assemblagens de referência adicionais ou ficheiros de dados do code-behind, separados por ';'|
|-UdoRedirect|Falso|Gerar a configuração de redirecionamento de assemblagem Udo|
|-UseDatabase|master|Base de dados a utilizar para code-behind do registo de assemblagem temporário|
|-Verbose|Falso|Mostrar saídas de detalhado de tempo de execução|
|-WorkDir|Diretório atual|Diretório para a utilização de compilador e saídas|
|-RunScopeCEP|0|Modo de ScopeCEP para utilizar|
|-ScopeCEPTempPath|Temp|Caminho temporário a utilizar para dados de transmissão em fluxo|
|-OptFlags| |Lista separada por vírgulas de sinalizadores de otimizador|


Eis um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Para além de combinar **compilar** e **executar**, pode compilar e executar o executáveis compilados em separado.

#### <a name="compile-a-u-sql-script"></a>Compilar um script U-SQL

O **compilar** comando é utilizado para compilar um script U-SQL para executáveis.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Seguem-se os argumentos opcionais para **compilar**:


|Argumento|Descrição|
|--------|-----------|
| -CodeBehind [valor predefinido 'False']|O script tem CS code-behind|
| -CppSDK [valor predefinido ']|Diretório de CppSDK|
| -DataRoot [valor predefinido 'Variável de ambiente de DataRoot']|DataRoot para execução predefinido à variável de ambiente 'LOCALRUN_DATAROOT' local|
| -MessageOut [valor predefinido ']|Mensagens de informações na consola para um ficheiro|
| -Faz referência a [valor predefinido ']|Lista de caminhos para assemblagens de referência adicionais ou ficheiros de dados do code-behind, separados por ';'|
| -Baixa [valor predefinido 'False']|Compilação Shallow|
| -UdoRedirect [valor predefinido 'False']|Gerar a configuração de redirecionamento de assemblagem Udo|
| -UseDatabase [valor predefinido 'master']|Base de dados a utilizar para code-behind do registo de assemblagem temporário|
| -WorkDir [valor predefinido 'Diretório atual']|Diretório para a utilização de compilador e saídas|
| -RunScopeCEP [valor predefinido '0']|Modo de ScopeCEP para utilizar|
| -ScopeCEPTempPath [valor predefinido 'temp']|Caminho temporário a utilizar para dados de transmissão em fluxo|
| -OptFlags [valor predefinido ']|Lista separada por vírgulas de sinalizadores de otimizador|


Seguem-se alguns exemplos de utilização.

Compila um script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilar um script U-SQL e definir a pasta raiz dos dados. Tenha em atenção que isto irá substituir a variável de ambiente de conjunto.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilar um script U-SQL e defina um diretório de trabalho, a assemblagem de referência e a base de dados:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Executar compilados resultados

O **executar** comando é utilizado para executar resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Seguem-se os argumentos opcionais para **executar**:

|Argumento|Descrição|
|--------|-----------|
|-DataRoot [valor predefinido ']|Raiz dos dados para a execução de metadados. Assume como o **LOCALRUN_DATAROOT** variável de ambiente.|
|-MessageOut [valor predefinido ']|Mensagens na consola para um ficheiro de imagem de erro.|
|-Paralelo [valor predefinido '1']|Indicador de executar os passos de execução local gerados com o nível de paralelismo especificado.|
|-Verbose [valor 'False' predefinido]|Indicador de Mostrar saídas de detalhado de tempo de execução.|

Eis um exemplo de utilização:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Utilize o SDK com interfaces de programação

As interfaces de programação estão localizadas no LocalRunHelper.exe. Pode utilizá-los para integrar a funcionalidade do SDK do U-SQL e a arquitetura de teste do c# para dimensionar o teste de local de script U-SQL. Neste artigo, irá uso do padrão unidade teste projeto c# para mostrar como utilizar estas interfaces para testar o script U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Passo 1: Criar o projeto de teste c# unidade e a configuração

- Criar um unidade teste projeto c# através do ficheiro > novo > projeto > Visual c# > Testar > projeto de teste da unidade.
- Adicione LocalRunHelper.exe como uma referência para o projeto. O LocalRunHelper.exe está localizado em \build\runtime\LocalRunHelper.exe no pacote Nuget.

    ![SDK do Azure Data Lake U-SQL Adicionar referência](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- SDK do U-SQL **apenas** ambiente suporte x64, certifique-se de que define o destino de plataforma de compilação como x64. Pode configurar essa através da propriedade do projeto > criar > destino de plataforma.

    ![Configurar o SDK do Azure Data Lake U-SQL x64 projeto](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Certifique-se definir o seu ambiente de teste como x64. No Visual Studio, pode configurá-lo através de testes > definições de teste > predefinido arquitectura de processador > x64.

    ![Configurar o SDK do Azure Data Lake U-SQL x64 ambiente de teste](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Certifique-se copiar todos os ficheiros de dependência em NugetPackage\build\runtime\ para o projeto no diretório que é normalmente em ProjectFolder\bin\x64\Debug a trabalhar.

### <a name="step-2-create-u-sql-script-test-case"></a>Passo 2: Criar caso de teste de script U-SQL

Segue-se o código de exemplo para o teste de script U-SQL. Para fins de teste, terá de preparar os scripts, ficheiros de entrada e de ficheiros de saída esperado.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfaces de programação na LocalRunHelper.exe

LocalRunHelper.exe fornece as interfaces de programação para compilar local do U-SQL, execute, etc. As interfaces estão listadas da seguinte forma.

**Construtor**

LocalRunHelper público ([System.IO messageOutput = null])

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|messageOutput|System.IO|mensagens de saída, definido como nulo para utilizar a consola do|

**Propriedades**

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|AlgebraPath|Cadeia|O caminho do ficheiro de algebra (ficheiro algebra é um dos resultados de compilação)|
|CodeBehindReferences|Cadeia|Se o script tiver adicional code-behind referências, especifique os caminhos separados ';'|
|CppSdkDir|Cadeia|Diretório de CppSDK|
|CurrentDir|Cadeia|Diretório atual|
|DataRoot|Cadeia|Caminho da raiz de dados|
|DebuggerMailPath|Cadeia|O caminho para o depurador mailslot|
|GenerateUdoRedirect|bool|Se de que pretende gerar assemblagem ao carregar a configuração de substituição de redirecionamento|
|HasCodeBehind|bool|Se o script tiver code-behind|
|InputDir|Cadeia|Diretório de dados de entrada|
|MessagePath|Cadeia|Caminho do ficheiro de informação de mensagem|
|OutputDir|Cadeia|Diretório de dados de saída|
|Paralelismo|Int|Paralelismo para executar o algebra|
|ParentPid|Int|PID de principal no qual o serviço monitoriza para sair, definida como 0 ou negativa para ignorar|
|ResultPath|Cadeia|Caminho do ficheiro de informação de resultado|
|RuntimeDir|Cadeia|Diretório de tempo de execução|
|ScriptPath|Cadeia|Onde encontrar o script|
|Shallow|bool|Shallow compilação ou não|
|TempDir|Cadeia|Diretório temporário|
|UseDataBase|Cadeia|Especifique a base de dados a utilizar para code-behind do registo de assemblagem temporário, principal, por predefinição|
|WorkDir|Cadeia|Diretório de trabalho preferencial|


**Método**

|Método|Descrição|devolver|Parâmetro|
|------|-----------|------|---------|
|bool pública DoCompile()|Compilar o script U-SQL|VERDADEIRO com êxito| |
|bool pública DoExec()|Executar o resultado compilado|VERDADEIRO com êxito| |
|bool pública DoRun()|Execute o script U-SQL (compilação + executar)|VERDADEIRO com êxito| |
|bool pública IsValidRuntimeDir (caminho de cadeia)|Verifique se o caminho fornecido é o caminho de runtime válido|Aplica-se válido|O caminho do diretório de tempo de execução|


## <a name="faq-about-common-issue"></a>FAQ sobre o problema comum verificado

### <a name="error-1"></a>Erro 1:
E_CSC_SYSTEM_INTERNAL: Erro interno! Não foi possível carregar o ficheiro ou assemblagem 'ScopeEngineManaged.dll' ou uma das respetivas dependências. Não foi possível encontrar o módulo especificado.

Verifique o seguinte:

- Certifique-se de que tem x64 ambiente. A plataforma de destino de compilação e o ambiente de teste deve ser x64, consulte a **passo 1: criar c# unidade testar o projeto e a configuração** acima.
- Certifique-se de que copiou todos os ficheiros de dependência em NugetPackage\build\runtime\ para o projeto de diretório de trabalho.


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para registar informações de diagnóstico, consulte [aceder a registos de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [analisar registos de Web site com o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver os detalhes da tarefa, consulte [Browser de tarefa de utilização e a vista de tarefas para tarefas do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para utilizar a vista de execução de vértice, consulte [utilize a vista de execução de vértice ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
