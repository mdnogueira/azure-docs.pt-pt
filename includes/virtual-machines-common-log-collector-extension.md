
Diagnosticar problemas com um serviço em nuvem do Microsoft Azure necessita de recolher ficheiros de registo do serviço em máquinas virtuais, tal como os problemas. Pode utilizar a AzureLogCollector extensão a pedido à coleção de uma única perfom de registos de um ou mais VMs de serviço de nuvem (a partir de funções da web e funções de trabalho) e transferir os ficheiros recolhidos para uma conta de armazenamento do Azure – tudo sem remotamente iniciar sessão em qualquer um dos as VMs.

> [!NOTE]
> Podem ser localizadas http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp descrições para a maioria das informações com sessão iniciada.
> 
> 

Existem dois modos de coleção depende dos tipos de ficheiros a serem recolhidos.

* Convidado do Azure agente registos apenas (GA). Este modo da coleção inclui todos os registos relacionados com agentes convidados do Azure e outros componentes do Azure.
* Todos os registos (completa). Este modo da coleção recolherá todos os ficheiros no modo de GA mais:
  
  * registos de eventos do sistema e de aplicações
  * Registos de erros HTTP
  * Registos de IIS
  * Registos de configuração
  * outros registos de sistema

Em ambos os modos de coleção, pastas de recolha de dados adicionais podem ser especificadas através da utilização de uma coleção da estrutura seguinte:

* **Nome**: O nome da coleção, que será utilizado como o nome de subpasta dentro do ficheiro zip a serem recolhidos.
* **Localização**: O caminho para a pasta na máquina virtual onde serão recolhidos os ficheiros.
* **SearchPattern**: O padrão dos nomes dos ficheiros a serem recolhidos. Predefinição é "*"
* **Recursiva**: se os ficheiros serão recolhido recursivamente sob a pasta.

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter uma conta de armazenamento para a extensão de guardar ficheiros zip gerado.
* Tem de se certificar de que está a utilizar o Azure PowerShell Cmdlets V0.8.0 ou superior. Para obter mais informações, consulte [Azure transfere](https://azure.microsoft.com/downloads/).

## <a name="add-the-extension"></a>Adicionar a extensão
Pode utilizar [do Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) cmdlets ou [APIs REST do serviço de gestão](https://msdn.microsoft.com/library/ee460799.aspx) para adicionar a extensão de AzureLogCollector.

Para serviços em nuvem, o cmdlet Powershell do Azure existente, **conjunto AzureServiceExtension**, pode ser utilizado para ativar a extensão de instâncias de função de serviço em nuvem. Sempre que esta extensão é ativada através deste cmdlet, a recolha de registos é acionada nas instâncias de função selecionada dos direitos selecionados.

Para máquinas virtuais, o cmdlet Powershell do Azure existente, **conjunto AzureVMExtension**, pode ser utilizado para ativar a extensão em máquinas virtuais. Sempre que esta extensão é ativada através dos cmdlets, a recolha de registos é acionada em cada instância.

Internamente, esta extensão utiliza o PublicConfiguration baseados em JSON e a PrivateConfiguration. Segue-se o esquema de uma amostra JSON para a configuração pública e privada.

### <a name="publicconfiguration"></a>PublicConfiguration
    {
        "Instances":  "*",
        "Mode":  "Full",
        "SasUri":  "SasUri to your storage account with sp=wl",
        "AdditionalData":
        [
          {
                  "Name":  "StorageData",
                  "Location":  "%roleroot%storage",
                  "SearchPattern":  "*.*",
                  "Recursive":  "true"
          },
          {
                "Name":  "CustomDataFolder2",
                "Location":  "c:\customFolder",
                "SearchPattern":  "*.log",
                "Recursive":  "false"
          },
        ]
    }

### <a name="privateconfiguration"></a>PrivateConfiguration
    {

    }

> [!NOTE]
> Esta extensão não necessita de **privateConfiguration**. Apenas pode fornecer uma estrutura vazia para o **– PrivateConfiguration** argumento.
> 
> 

Pode seguir um dos dois passos seguintes para adicionar o AzureLogCollector para uma ou mais instâncias de um serviço em nuvem ou Máquina Virtual de funções selecionadas, que aciona as coleções em cada VM a executar e enviar os ficheiros recolhidos para a conta do Azure especificada.

## <a name="adding-as-a-service-extension"></a>Adicionar como uma extensão de serviço
1. Siga as instruções para ligar o Azure PowerShell à sua subscrição.
2. Especifique as instâncias, ranhura, funções e função de serviço ao qual pretende adicionar e ativar a extensão de AzureLogCollector.
   
        #Specify your cloud service name
        $ServiceName = 'extensiontest2'
   
        #Specify the slot. 'Production' or 'Staging'
        $slot = 'Production'
   
        #Specified the roles on which the extension will be installed and enabled
        $roles = @("WorkerRole1","WebRole1")
   
        #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
        $instances = @("*")
   
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
3. Especifique a pasta de dados adicionais para os quais serão recolhidos os ficheiros (este passo é opcional).
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
   
   > [!NOTE]
   > Pode utilizar o token `%roleroot%` para especificar a unidade de raiz de função, uma vez que não utiliza uma unidade fixa.
   > 
   > 
4. Forneça o nome da conta de armazenamento do Azure e a chave para o qual os ficheiros recolhidos serão carregados.
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
5. Chamada SetAzureServiceLogCollector.ps1 (incluído no final do artigo) da seguinte forma para ativar a extensão de AzureLogCollector num serviço em nuvem. Depois da execução está concluída, pode encontrar o ficheiro carregado em`https://YouareStorageAccountName.blob.core.windows.net/vmlogs`
   
        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

Segue-se a definição de parâmetros transmitidos para o script. (Isto é copiado abaixo, bem como.)

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
      [Parameter(Mandatory=$true)]
    [string]   $ServiceName,

    [Parameter(Mandatory=$false)]
    [string[]] $Roles ,

    [Parameter(Mandatory=$false)]
    [string[]] $Instances,

    [Parameter(Mandatory=$false)]
    [string]   $Slot = 'Production',

    [Parameter(Mandatory=$false)]
    [string]   $Mode = 'Full',

    [Parameter(Mandatory=$false)]
    [string]   $StorageAccountName,

    [Parameter(Mandatory=$false)]
    [string]   $StorageAccountKey,

    [Parameter(Mandatory=$false)]
    [PSObject[]] $AdditionDataLocationList = $null
    )

* *ServiceName*: O nome do serviço em nuvem.
* *Funções*: uma lista de funções, tais como "WebRole1" ou "WorkerRole1".
* *Instâncias*: uma lista de nomes de instâncias de função separados por vírgulas – utilizar a cadeia de carateres universais ("*") para todas as instâncias de função.
* *Ranhura*: nome da ranhura. "Production" ou "Transição".
* *Modo*: modo da coleção. "Completo" ou "GA".
* *StorageAccountName*: recolhidos de conta de armazenamento do nome do Azure para armazenar dados.
* *StorageAccountKey*: a chave de conta de armazenamento de nome do Azure.
* *AdditionalDataLocationList*: uma lista da estrutura seguinte:
  
      {
      String Name,
      String Location,
      String SearchPattern,
      Bool   Recursive
      }

## <a name="adding-as-a-vm-extension"></a>Adicionar como uma extensão VM
Siga as instruções para ligar o Azure PowerShell à sua subscrição.

1. Especifique o nome do serviço, VM e o modo da coleção.
   
        #Specify your cloud service name
        $ServiceName = 'YourCloudServiceName'
   
        #Specify the VM name
        $VMName = "'YourVMName'"
   
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
   
        Specify the additional data folder for which files will be collected (this step is optional).
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
2. Forneça o nome da conta de armazenamento do Azure e a chave para o qual os ficheiros recolhidos serão carregados.
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
3. Chamada SetAzureVMLogCollector.ps1 (incluído no final do artigo) da seguinte forma para ativar a extensão de AzureLogCollector num serviço em nuvem. Depois da execução está concluída, pode encontrar o ficheiro carregado em https://YouareStorageAccountName.blob.core.windows.net/vmlogs

Segue-se a definição de parâmetros transmitidos para o script. (Isto é copiado abaixo, bem como.)

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
        [Parameter(Mandatory=$true)]
      [string]   $ServiceName,

      [Parameter(Mandatory=$false)]
      [string] $VMName ,

        [Parameter(Mandatory=$false)]
      [string]   $Mode = 'Full',

      [Parameter(Mandatory=$false)]
      [string]   $StorageAccountName,

      [Parameter(Mandatory=$false)]
      [string]   $StorageAccountKey,

      [Parameter(Mandatory=$false)]
      [PSObject[]] $AdditionDataLocationList = $null
      )

* ServiceName: O nome do serviço cloud.
* VMName o nome da VM.
* Modo: Modo de coleção. "Completo" ou "GA".
* StorageAccountName: O nome da conta de armazenamento do Azure para armazenar os dados recolhidos.
* StorageAccountKey: Nome da chave de conta de armazenamento do Azure.
* AdditionalDataLocationList: Uma lista da estrutura seguinte:

```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive
      }
```

## <a name="extention-powershell-script-files"></a>Ficheiros de Script do PowerShell de extensão
SetAzureServiceLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
                  [Parameter(Mandatory=$true)]
                  [string]   $ServiceName,

                  [Parameter(Mandatory=$false)]
                  [string[]] $Roles ,

                  [Parameter(Mandatory=$false)]
                  [string[]] $Instances = '*',

                  [Parameter(Mandatory=$false)]
                  [string]   $Slot = 'Production',

                  [Parameter(Mandatory=$false)]
                  [string]   $Mode = 'Full',

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountName,

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountKey,

                  [Parameter(Mandatory=$false)]
                  [PSObject[]] $AdditionDataLocationList = $null
            )

    $publicConfig = New-Object PSObject

    if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
    {
        $instanceText = $Instances[0]
        for ($i = 1;$i -lt $Instances.Count;$i++)
        {
              $instanceText = $instanceText+ "," + $Instances[$i]
          }
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
    }
    else  #For all instances if not specified.  The value should be a space or *
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
    }

    if ($Mode -ne $null )
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }

    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }

    $ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }

    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
    "publicConfig is:  $publicConfigJSON"

    #we just provide a empty privateConfig object
    $privateconfig = "{
    }"

    if ($Roles -ne $null)
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
    }
    else
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
    }

    #
    #This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
    #
    $SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
    $SasUri = $SasUri + "&restype=container&comp=list"
    Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"


SetAzureVMLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
                  [Parameter(Mandatory=$true)]
                  [string]   $ServiceName,

                  [Parameter(Mandatory=$false)]
                  [string] $VMName ,

                  [Parameter(Mandatory=$false)]
                  [string]   $Mode = 'Full',

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountName,

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountKey,

                  [Parameter(Mandatory=$false)]
                  [PSObject[]] $AdditionDataLocationList = $null
            )

    $publicConfig = New-Object PSObject
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

    if ($Mode -ne $null )
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }

    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }

    $ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }

    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json

    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"

    #
    #we just provide a empty privateConfig object
    #
    $privateconfig = "{
    }"

    if ($VMName -ne $null )
    {
          $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
          $VM.VM.OSVirtualHardDisk.OS

          if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
          {
                Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

                #
                #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
                #the presence of SubstatusList field.
                #
                $Completed = $false
                while ($Completed -ne $true)
                {
                        $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                        $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                        if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                        {
                            Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                              $Completed = $true
                        }
                        elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                        {
                              $Completed = $false
                              Write-Output "Waiting for operation to complete..."
                        }
                        else
                        {
                              $Completed = $true
                              Write-Output "Operation completed."

                        $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                              $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                      #
                            # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                              #
                              $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                              $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                            Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                              #
                              #This is an optional step:  Remove the extension after we are done
                              #
                              Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                        }
                        Start-Sleep -s 5
                }
          }
          else
          {
              Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
          }

    }
    else
    {
      Write-Output "VM name is not specified, the extension cannot be enabled"
    }

## <a name="next-steps"></a>Passos Seguintes
Agora pode examinar ou copiar os registos de uma localização muito simple.

