---
title: "Criar um módulo de limite de IoT do Azure com c# | Microsoft Docs"
description: "Este tutorial showcases como escrever um módulo de conversor var dados utilizando os pacotes de NuGet de limite de IoT do Azure mais recentes, Visual Studio Code e c#."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "Azure iot, tutorial, módulo, nuget, vscode, csharp, edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Criar um módulo de limite de IoT do Azure com C & #x;

Este tutorial showcases como criar um módulo para `Azure IoT Edge` utilizando `Visual Studio Code` e `C#`.

Neste tutorial, iremos guiá-lo através da configuração do ambiente e como escrever um [var](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) módulo de conversor de dados com a versão mais recente `Azure IoT Edge NuGet` pacotes. 

>[!NOTE]
Neste tutorial está a utilizar o `.NET Core SDK`, que suporta a compatibilidade de plataforma. O tutorial seguinte é escrito utilizando o `Windows 10` sistema operativo. Alguns dos comandos neste tutorial podem ser diferentes dependendo do seu `development environment`. 

## <a name="prerequisites"></a>Pré-requisitos

Nesta secção, iremos configurar o ambiente para `Azure IoT Edge` desenvolvimento do módulo. Aplica-se ambos **Windows de 64 bits** e **64-bit Linux (8 Ubuntu/Debian)** sistemas operativos.

É necessário o seguinte software:

- [Cliente de Git](https://git-scm.com/downloads)
- [SDK .NET Core](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Não é necessário clonar o repositório para este exemplo, no entanto, todos os o código de exemplo abordado neste tutorial está localizado no repositório seguinte:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Introdução

1. Instalar `.NET Core SDK`.
2. Instalar `Visual Studio Code` e `C# extension` no Marketplace de código do Visual Studio.

Visualizar estas [tutorial rápido de vídeo](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) sobre como começar a utilizar `Visual Studio Code` e `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Criar o módulo de conversor de limite de IoT do Azure

1. Inicializar uma nova `.NET Core` projeto da biblioteca c# classe:
    - Abra uma linha de comandos (`Windows + R` -> `cmd` -> `enter`).
    - Navegue para a pasta onde pretende criar o `C#` projeto.
    - Tipo **dotnet novo classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Este comando cria uma classe vazia denominada `Class1.cs` no seu diretório de projetos.
2. Navegue para a pasta em que acabamos de criar o projeto da biblioteca de classe, escrevendo **cd IoTEdgeConverterModule**.
3. Abra o projeto na `Visual Studio Code` escrevendo **código.**.
4. Depois do projeto estiver aberto `Visual Studio Code`, clique em de **IoTEdgeConverterModule.csproj** para abrir o ficheiro, conforme mostrado na imagem seguinte:

    ![Janela de edição do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Inserir o `XML` blob mostrado no seguinte fragmento de código entre o fecho `PropertyGroup` etiqueta e o fecho `Project` tag; linha seis na imagem anterior e guarde o ficheiro, premindo `Ctrl`  +  `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. Depois de guardar o `.csproj` ficheiro, `Visual Studio Code` deve solicitar-lhe com um `unresolved dependencies` diálogo como mostrado na imagem seguinte: 

    ![Diálogo de dependências de restauro do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) clique `Restore` para restaurar todas as referências as projetos `.csproj` ficheiros, incluindo o `PackageReferences` foi adicionado. 

    b) `Visual Studio Code` cria automaticamente o `project.assets.json` ficheiros nos seus projetos `obj` pasta. Este ficheiro contém informações sobre dependências do seu projeto para tornar subsequentes restauros mais rápida.
 
    >[!NOTE]
    `.NET Core Tools`são agora MSBuild baseadas em. Que significa um `.csproj` é criado o ficheiro de projeto, em vez de um `project.json`.

    - Se `Visual Studio Code` não solicita que há problema, vamos pode fazê-lo manualmente. Abra o `Visual Studio Code` integrada janela de terminal, premindo o `Ctrl`  +  `backtick` chaves ou utilizar os menus `View`  ->  `Integrated Terminal`.
    - No `Integrated Terminal` tipo janela **dotnet restauro**.
    
7. Mudar o nome de `Class1.cs` do ficheiro para `BleConverterModule.cs`. 

    a) para mudar o nome de ficheiro primeiro clique no ficheiro, em seguida, prima a `F2` chave.
    
    b) tipo no nome do novo **BleConverterModule**, como mostrado na imagem seguinte:

    ![Visual Studio Code, mudar o nome de uma classe](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Substitua o código existente no `BleConverterModule.cs` ficheiro ao copiar e colar o seguinte fragmento de código no seu `BleConverterModule.cs` ficheiro.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Guarde o ficheiro, premindo `Ctrl`  +  `S`.

10. Criar um novo ficheiro chamado `Untitled-1` , premindo o `Ctrl`  +  `N` chaves como mostrado na imagem seguinte:

    ![Visual novo ficheiro de código do Studio](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Para anular a serialização de `JSON` objeto que recebemos o simulado de `BLE` dispositivo, copie o seguinte código para o `Untitled-1` janela do editor de código de ficheiro. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Guarde o ficheiro como `BleData.cs` premindo `Ctrl`  +  `Shift`  +  `S` chaves.
    - No guardar como caixa de diálogo, no `Save as Type` menu pendente, selecione `C# (*.cs;*.csx)` como mostrado na imagem seguinte:

    ![Visual Studio Code guardar como caixa de diálogo](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Criar um novo ficheiro chamado `Untitled-1` , premindo o `Ctrl`  +  `N` chaves.

14. Copie e cole o seguinte fragmento de código para o `Untitled-1` ficheiro. Esta classe é um `Azure IoT Edge` módulo, que utilizamos para exportar os dados recebidos do nosso `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Guarde o ficheiro como `DotNetPrinterModule.cs` premindo `Ctrl`  +  `Shift`  +  `S`.
    - No guardar como caixa de diálogo, no `Save as Type` menu pendente, selecione `C# (*.cs;*.csx)`.

16. Criar um novo ficheiro chamado `Untitled-1` , premindo o `Ctrl`  +  `N` chaves.

17. Para anular a serialização de `JSON` objeto que recebemos do `BleConverterModule`, copiar e colar o seguinte código fragmento para o `Untitled-1` ficheiro. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Guarde o ficheiro como `BleConverterData.cs` premindo `Ctrl`  +  `Shift`  +  `S`.
    - No guardar como caixa de diálogo, no `Save as Type` menu pendente, selecione `C# (*.cs;*.csx)`.

19. Criar um novo ficheiro chamado `Untitled-1` , premindo o `Ctrl`  +  `N` chaves.

20. Copie e cole o seguinte fragmento de código para o `Untitled-1` ficheiro.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Guarde o ficheiro como `gw-config.json` premindo `Ctrl`  +  `Shift`  +  `S`.
    - No guardar como caixa de diálogo, no `Save as Type` menu pendente, selecione `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Para ativar a cópia do ficheiro de configuração para o diretório de saída, atualize o `IoTEdgeConverterModule.csproj` com o blob XML seguinte:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - A atualização `IoTEdgeConverterModule.csproj` deverá ser semelhante a imagem seguinte:

    ![Visual Studio Code atualizar ficheiro. csproj](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Criar um novo ficheiro chamado `Untitled-1` , premindo o `Ctrl`  +  `N` chaves.

24. Copie e cole o seguinte fragmento de código para o `Untitled-1` ficheiro.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Guarde o ficheiro como `binplace.ps1` premindo `Ctrl`  +  `Shift`  +  `S`.
    - No guardar como caixa de diálogo, no `Save as Type` menu pendente, selecione `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Compilar o projeto ao premir a `Ctrl`  +  `Shift`  +  `B` chaves. Quando criar o projeto pela primeira vez, `Visual Studio Code` pede-lhe com o `No build task defined.` diálogo como mostrado na imagem seguinte:

    ![Diálogo de tarefa de compilação do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) clique em a `Configure Build Task` botão.

    b) no `Select a Task Runner` menu de lista pendente da caixa de diálogo. Selecione `.NET Core` como mostrado na imagem seguinte: 

    ![Visual Studio Code selecionar uma caixa de diálogo tarefa](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    Clicar c) a `.NET Core` item cria o `tasks.json` ficheiros sua `.vscode` diretório e abre-se o ficheiro no `code editor` janela. Não é necessário para modificar este ficheiro, a fechar o separador.

27.  Abra o `Visual Studio Code` integrada janela de terminal, premindo o `Ctrl`  +  `backtick` chaves ou utilizar os menus `View`  ->  `Integrated Terminal` e tipo **.\binplace.ps1** para o `PowerShell` linha de comandos. Este comando copia todos os nossas dependências para o diretório de saída.

28. Navegue para o diretório de saída de projetos no `Integrated Terminal` janela escrevendo **cd.\bin\Debug\netstandard1.3**.

29. Executar o projeto de exemplo, escrevendo **. \gw.exe gw-config** para o `Integrated Terminal` linha de janela. 
    - Se seguiu os passos neste tutorial rigorosamente, deve agora estar a executar o `Azure IoT Edge BLE Data Converter Module` projeto de exemplo, como mostrado na imagem seguinte:
    
        ![Exemplo de dispositivos simulados em execução no Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Se pretende terminar a aplicação, prima a `<Enter>` chave.

>[!IMPORTANT]
Não é recomendado utilizar `Ctrl`  +  `C` para terminar a `IoT Edge` gateway application (ou seja, **gw.exe**). Como esta ação pode fazer com que o processo esteja terminado.

