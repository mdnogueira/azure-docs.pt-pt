


## <a name="using-vm-extensions"></a>Utilizar extensões VM
Extensões de VM do Azure implementa comportamentos ou funcionalidades que ajudam a outros programas de trabalho em VMs do Azure (por exemplo, o **WebDeployForVSDevTest** extensão permite que o Visual Studio para o Web Deploy soluções na sua VM do Azure) ou forneça o capacidade de interagir com a VM para suportar alguns outro comportamento (por exemplo, pode utilizar as extensões de acesso de VM a partir de clientes do PowerShell, a CLI do Azure e REST para repor ou modificar os valores de acesso remoto na sua VM do Azure).

> [!IMPORTANT]
> Para obter uma lista completa das extensões pelas funcionalidades que suportam, consulte [extensões de VM do Azure e funcionalidades](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Porque cada extensão VM suporta uma funcionalidade específica, exatamente o que pode e não pode fazer com uma extensão depende a extensão. Por conseguinte, antes de modificar a sua VM, certifique-se de que leu a documentação para a extensão de VM que pretende utilizar. A remoção algumas extensões de VM não é suportada; outras pessoas têm propriedades que podem ser definidas e alterar o comportamento VM forma radicalmente.
> 
> 

As tarefas mais comuns são:

1. Localizar as extensões disponíveis
2. Atualizar extensões carregadas
3. Adicionar extensões
4. Remoção de extensões

## <a name="find-available-extensions"></a>Localizar as extensões disponíveis
Pode localizar a extensão e a utilização de informações expandidas:

* PowerShell
* Interface de linha de comandos de plataforma do Azure (CLI do Azure)
* API REST da Gestão de Serviços

### <a name="azure-powershell"></a>Azure PowerShell
Algumas extensões têm os cmdlets do PowerShell que são específicos, que poderá facilitar a configuração a partir do PowerShell; mas os seguintes cmdlets funciona para todas as extensões VM.

Pode utilizar os seguintes cmdlets para obter informações sobre as extensões disponíveis:

* Para instâncias de funções da web ou funções de trabalho, pode utilizar o [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet.
* Para as instâncias de máquinas virtuais, pode utilizar o [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) cmdlet.
  
   Por exemplo, o exemplo de código seguinte mostra como listar as informações para o **IaaSDiagnostics** extensão com o PowerShell.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Interface de linha de comandos do Azure (CLI do Azure)
Algumas extensões têm de comandos da CLI do Azure que são específicos aos mesmos (a extensão de VM de Docker é um exemplo), que poderá facilitar a respetiva configuração; mas os comandos seguintes funcionam para todas as extensões VM.

Pode utilizar o **lista de extensão de vm do azure** comando para obter informações sobre as extensões disponíveis e utilizar o **–-json** opção para apresentar todas as informações disponíveis sobre uma ou mais extensões. Se utilizar um nome de extensão, o comando devolve uma descrição JSON de todas as extensões disponíveis.

Por exemplo, o exemplo de código seguinte mostra como listar as informações para o **IaaSDiagnostics** extensão utilizando a CLI do Azure **lista de extensão de vm do azure** comando e utiliza o **–-json**  opção para devolver informações completas.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>APIs REST de Gestão de Serviço
Pode utilizar as APIs REST seguintes para obter informações sobre as extensões disponíveis:

* Para instâncias de funções da web ou funções de trabalho, pode utilizar o [extensões disponíveis lista](https://msdn.microsoft.com/library/dn169559.aspx) operação. Para listar as versões das extensões disponíveis, pode utilizar [lista as versões de extensão](https://msdn.microsoft.com/library/dn495437.aspx).
* Para as instâncias de máquinas virtuais, pode utilizar o [extensões de recursos de lista](https://msdn.microsoft.com/library/dn495441.aspx) operação. Para listar as versões das extensões disponíveis, pode utilizar [versões de extensão de recursos de lista](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Adicionar, atualizar, ou desativar extensões
As extensões podem ser adicionadas quando é criada uma instância ou podem ser adicionados a uma instância em execução. As extensões podem ser atualizadas, desativadas ou removidas. Pode efetuar estas ações utilizando cmdlets do PowerShell do Azure ou através da utilização de operações de API de REST de gestão de serviço. Os parâmetros são necessários para instalar e configurar algumas extensões. Parâmetros públicos e privados são suportados para as extensões.

### <a name="azure-powershell"></a>Azure PowerShell
A utilização de cmdlets do PowerShell do Azure é a forma mais fácil para adicionar e atualizar extensões. Quando utilizar os cmdlets de extensão, a maior parte da configuração da extensão é feita por si. Por vezes, terá de adicionar através de programação de uma extensão. Quando precisar de efetuar este procedimento, tem de fornecer a configuração da extensão.

Pode utilizar os seguintes cmdlets para saber se uma extensão requer uma configuração de parâmetros públicas e privadas:

* Para instâncias de funções da web ou funções de trabalho, pode utilizar o **Get-AzureServiceAvailableExtension** cmdlet.
* Para as instâncias de máquinas virtuais, pode utilizar o **Get-AzureVMAvailableExtension** cmdlet.

### <a name="service-management-rest-apis"></a>APIs REST de Gestão de Serviço
Ao obter uma listagem das extensões disponíveis, utilizando as APIs REST, recebe informações sobre como a extensão de ser configurada. As informações que são devolvidas poderão mostrar informações de parâmetro representadas por um esquema pública e privada esquema. Os valores de parâmetros público são devolvidos nas consultas sobre as instâncias. Os valores de parâmetros privada não são devolvidos.

Pode utilizar as APIs REST do seguinte para saber se uma extensão requer uma configuração de parâmetros públicas e privadas:

* Para instâncias de funções da web ou funções de trabalho, o **PublicConfigurationSchema** e **PrivateConfigurationSchema** elementos contêm as informações na resposta do [lista disponíveis Extensões](https://msdn.microsoft.com/library/dn169559.aspx) operação.
* Para instâncias de máquinas virtuais, o **PublicConfigurationSchema** e **PrivateConfigurationSchema** elementos contêm as informações na resposta do [lista de recursos Extensões](https://msdn.microsoft.com/library/dn495441.aspx) operação.

> [!NOTE]
> Extensões também podem utilizar as configurações que estão definidas com JSON. Quando estes tipos de extensões são utilizados, apenas o **SampleConfig** elemento é utilizado.
> 
> 

