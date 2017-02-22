## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, pode:

* Criar uma aplicação de consola Node.js que responde a um método direto chamado pela cloud
* Acionar uma atualização de firmware simulada
* Utilize as propriedades comunicadas para ativar as consultas de dispositivo duplo, de forma a identificar os dispositivos e quando concluírem uma atualização de firmware pela última vez

1. Crie uma pasta vazia designada **manageddevice**.  Na pasta **manageddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos na pasta **manageddevice**, execute o seguinte comando para instalar o **azure-iot-device** e os pacotes SDK do Serviço **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um ficheiro **dmpatterns_fwupdate_device.js** na pasta **manageddevice**.

4. Adicione as seguintes declarações "necessárias" no início do ficheiro **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**. Substitua o marcador de posição `{yourdeviceconnectionstring}` pela cadeia de ligação que criou anteriormente uma nota na secção anterior "Criar uma identidade de dispositivo":
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função que é utilizada para atualizar propriedades comunicadas:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```
7. Adicione as seguintes funções que simulam a transferência e aplicação da imagem de firmware:
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Adicione a seguinte função que atualiza o estado de atualização de firmware através das propriedades comunicadas para **a aguardar**. Normalmente, os dispositivos são informados sobre uma atualização disponível e um administrador define as causas de política para o dispositivo começar a transferir e aplicar a atualização. Esta função é onde deve ser executada a lógica para ativar essa política. Para simplicidade, a amostra é apresentada durante quatro segundos antes de continuar a transferir a imagem de firmware:
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Adicione a seguinte função que irá atualizar o estado de atualização de firmware através das propriedades comunicadas para **transferir**. A função simula, em seguida, uma transferência de firmware e, por último, atualiza o estado de atualização de firmware para **downloadFailed** ou **downloadComplete**:
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Adicione a seguinte função que atualiza o estado de atualização de firmware através das propriedades comunicadas para **a aplicar**. A função simula, em seguida, a aplicação da imagem de firmware e, por último, atualiza o estado de atualização de firmware para **applyFailed** ou **applyComplete**:
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Adicione a seguinte função que processa o método direto **firmwareUpdate** e inicia o processo de atualização de firmware de várias fases:
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = request.payload.fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Finalmente, adicione o seguinte código que liga ao seu hub IoT:
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo MSDN [Processamento de Erros Transitórios][lnk-transient-faults].
> 
> 

<!--HONumber=Feb17_HO1-->


