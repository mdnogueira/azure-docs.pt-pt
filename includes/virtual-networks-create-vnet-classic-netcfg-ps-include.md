## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Como criar uma rede virtual com um ficheiro de configuração de rede a partir do PowerShell
Azure utiliza um ficheiro xml para definir todas as redes virtuais disponíveis para uma subscrição. Pode transferir este ficheiro, editá-lo para modificar ou eliminar as redes virtuais existentes e criar novas redes virtuais. Neste tutorial, saiba como transferir este ficheiro, referido como ficheiro de configuração (ou netcfg) de rede e editá-lo para criar uma nova rede virtual. Para saber mais sobre o ficheiro de configuração de rede, consulte o [esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Para criar uma rede virtual com um ficheiro netcfg através do PowerShell, execute os seguintes passos:

1. Se nunca tiver utilizado o Azure PowerShell, execute os passos do [como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) artigo, em seguida, inicie sessão no Azure e selecionar a sua subscrição.
2. A partir da consola do Azure PowerShell, utilize o **Get-AzureVnetConfig** cmdlet para transferir o ficheiro de configuração de rede para um diretório no seu computador, executando o seguinte comando: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Resultado esperado:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Abra o ficheiro que guardou no passo 2 através de qualquer aplicação de editor de XML ou de texto e procure o  **<VirtualNetworkSites>**  elemento. Se tiver quaisquer redes já criados, cada rede é apresentado como a sua própria  **<VirtualNetworkSite>**  elemento.
4. Para criar a rede virtual descrita neste cenário, adicione o seguinte XML apenas no  **<VirtualNetworkSites>**  elemento:

   ```xml
        <VirtualNetworkSite name="TestVNet" Location="East US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
   ```
   
5. Guarde o ficheiro de configuração de rede.
6. A partir da consola do Azure PowerShell, utilize o **conjunto AzureVnetConfig** cmdlet para carregar o ficheiro de configuração de rede executando o seguinte comando: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Devolveu saída:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Se **OperationStatus** não é *com êxito* no resultado devolvido, verifique o ficheiro de xml para erros e concluído passo 6 novamente.

7. A partir da consola do Azure PowerShell, utilize o **Get-AzureVnetSite** cmdlet para verificar se a nova rede foi adicionada ao executar o seguinte comando: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   O resultado devolvido (abreviado) inclui o seguinte texto:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
