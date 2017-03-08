

## <a name="azure-cli-20"></a>CLI 2.0 do Azure

Assim que tiver [instalado a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2), utilize o comando `az vm image list` para ver uma lista em cache de imagens populares da VM. Por exemplo, o exemplo seguinte do comando `az vm image list -o table` apresenta:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Encontrar todas as imagens atuais

Para obter a lista atual de todas as imagens, utilize o comando `az vm image list` com a opção `--all`. Ao contrário dos comandos da CLI do Azure 1.0, o comando `az vm image list --all` devolve todas as imagens no **westus** por predefinição (a menos que especifique um determinado argumento `--location`), por isso o comando `--all` demora algum tempo a concluir. Se pretender investigar interativamente, utilize `az vm image list --all > allImages.json`, que devolve uma lista de todas as imagens atualmente disponíveis no Azure e armazena-as como um ficheiro para utilização local. 

Pode especificar uma das várias opções para restringir a pesquisa para uma localização específica, oferta, publicador ou sku, se já tiver um ou mais em mente. Se não especificar uma localização, os valores para **westus** são devolvidos.

### <a name="find-specific-images"></a>Encontrar imagens específicas

Utilize `az vm image list` com um [filtro de consulta JMESPATH](https://docs.microsoft.com/cli/azure/query-az-cli2) para encontrar informações específicas. Por exemplo, o seguinte apresenta os **sku**s que estão disponíveis para o **Debian** (lembre-se que sem o comutador `--all`, este procura apenas a cache local de imagens comuns):

```azurecli
az vm image list --query '[?contains(offer,`Debian`)]' -o table --all
```

O resultado é algo como: 
```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
  Sku  Publisher    Offer    Urn                       Version    UrnAlias
-----  -----------  -------  ------------------------  ---------  ----------
    8  credativ     Debian   credativ:Debian:8:latest  latest     Debian

<list shortened for the example>
```

Se souber onde está a implementar, pode utilizar os resultados de pesquisa de imagens gerais juntamente com os comandos `az vm image list-skus`, `az vm image list-offers`, e `az vm image list-publishers`, para encontrar exatamente o que pretende e onde pode ser implementado. Por exemplo, se a partir do exemplo anterior sabe que `credativ` tem uma oferta Debian, então pode utilizar o `--location` e outras opções para encontrar exatamente o que quer. O exemplo seguinte procura uma imagem de Debian 8 em **westeurope**:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

e o resultado é:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="azure-cli-10"></a>CLI do Azure 1.0 

> [!NOTE]
> Este artigo descreve como navegar e selecionar imagens da máquina virtual, com uma instalação da CLI do Azure 1.0 ou o Azure PowerShell que suporta o modelo de implementação Azure Resource Manager. Como pré-requisito, altere para o modo de Gestor de Recursos. Com a CLI do Azure, introduza esse modo ao escrever `azure config mode arm`. 
> 

A forma mais rápida de localizar uma imagem é chamar o comando `azure vm image list` e passar a localização, o nome do publicador (não é sensível a maiúsculas e minúsculas) e uma oferta – se souber qual é a oferta. Por exemplo, a lista seguinte é apenas um pequeno exemplo – muitas listas são bastante extensas – se souber que "Canonical" é um publicador para a oferta "UbuntuServer".

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

A coluna **Urn** será o formulário que passa para `azure vm quick-create`.

No entanto, muitas vezes ainda não sabe o que está disponível. Neste caso, pode navegar pelas imagens com o comando `azure vm image list-publishers` e especificar a localização de um centro de dados no pedido. Por exemplo, a seguir são listados todos os fabricantes de imagens em E.U.A. Oeste (passe o argumento de localização para minúsculas e remova os espaços das localizações padrão)

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Estas listas podem ser bastante extensas, pelo que a lista de exemplos anterior é apenas um fragmento. Vamos supor que reparei que o Canonical é, na verdade, um publicador de imagens em E.U.A. Oeste. Já pode encontrar as suas ofertas ao chamar `azure vm image list-offers` e passar a localização e o publicador nos pedidos, como o exemplo seguinte:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Agora sabemos que na região E.U.A. Oeste, o Canonical publica a oferta **UbuntuServer** no Azure. Mas que SKUs? Para obter esses valores, chame `azure vm image list-skus` e responda ao pedido com a localização, o publicador e a oferta que detetou.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Com estas informações, já pode encontrar exatamente a imagem que pretende ao chamar a chamada original na parte superior.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

Já pode escolher com precisão a imagem que pretende utilizar. Para criar rapidamente uma máquina virtual com as informações de URN que acabou de encontrar, ou para utilizar um modelo com essas informações URN, consulte [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Manager (Utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager)](../articles/xplat-cli-azure-resource-manager.md).

## <a name="powershell"></a>PowerShell
> [!NOTE]
> Instalar e configurar o [Azure PowerShell mais recente](/powershell/azureps-cmdlets-docs). Se estiver a utilizar os módulos do Azure PowerShell numa versão anterior à 1.0, continue a utilizar os comandos seguintes, mas primeiro tem de `Switch-AzureMode AzureResourceManager`. 
> 
> 

Ao criar uma nova máquina virtual com o Azure Resource Manager, em alguns casos tem de especificar uma imagem com a combinação das seguintes propriedades de imagem:

* Publicador
* Oferta
* SKU

Por exemplo, estes valores são necessários para o cmdlet do PowerShell `Set-AzureRMVMSourceImage` ou com um ficheiro de modelo do grupo de recursos em que tem de especificar o tipo de máquina virtual a ser criada.

Se for necessário determinar estes valores, pode navegar pelas imagens para determinar estes valores:

1. Listar os publicadores de imagem.
2. Para um determinado publicador, liste as respetivas ofertas.
3. Para uma determinada oferta, liste as respetivas SKUs.

Em primeiro lugar, liste os publicadores com os seguintes comandos:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Preencha o nome do publicador escolhido e execute os seguintes comandos:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Preencha o nome da oferta escolhida e execute os seguintes comandos:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

A partir da apresentação do comando `Get-AzureRMVMImageSku`, tem todas as informações que necessita para especificar a imagem para uma nova máquina virtual.

O seguinte mostra um exemplo completo:

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Para o publicador "MicrosoftWindowsServer":

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Para a oferta "WindowsServer":

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

A partir da lista, copie o nome do SKU escolhido e tem todas as informações para o cmdlet do PowerShell `Set-AzureRMVMSourceImage` ou para um modelo de grupo de recursos.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/