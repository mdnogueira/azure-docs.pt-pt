## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Como criar uma VNet clássica a utilizar a CLI do Azure
Pode utilizar a CLI do Azure para gerir os seus recursos do Azure na linha de comandos a partir de qualquer computador com Windows, Linux ou OSX. Para criar uma VNet com a CLI do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../articles/cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure network vnet create** para criar uma VNet e uma sub-rede, como mostrado abaixo. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Resultado esperado:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **-- vnet**. Nome da VNet a ser criada. Para o nosso cenário *TestVNet*.
   * **-e (ou - espaço de endereços)**. Espaço de endereços da VNet. Para o nosso cenário *192.168.0.0*
   * **-i (ou - cidr)**. Máscara de rede no formato CIDR. Para o nosso cenário *16*.
   * **-n (ou - nome de sub-rede**). Nome da sub-rede primeiro. Para o nosso cenário *FrontEnd*.
   * **-p (ou - ip de início de sub-rede)**. Endereço IP inicial para a sub-rede ou espaço de endereços de sub-rede. Para o nosso cenário *192.168.1.0*.
   * **-r (ou - sub-rede cidr)**. Máscara de rede no formato CIDR para a sub-rede. Para o nosso cenário *24*.
   * **-l (ou --location)**. Região do Azure onde a VNet será criada. Para o nosso cenário *EUA Central*.
3. Execute o comando **azure network vnet subnet create** para criar uma sub-rede, como mostrado abaixo. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    O resultado esperado para o comando acima é o seguinte:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (ou -- vnet-name**. Nome da VNet onde a sub-rede será criada. Para o nosso cenário *TestVNet*.
   * **-n (ou --name)**. Nome da nova sub-rede. Para o nosso cenário *back-end*.
   * **-a (or --address-prefix)**. Bloco CIDR da sub-rede. Quatro nosso cenário, *192.168.2.0/24*.
4. Execute o comando **azure network vnet show** para ver as propriedades da nova VNet, como mostrado abaixo.
   
            azure network vnet show
   
    O resultado esperado para o comando acima é o seguinte:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

