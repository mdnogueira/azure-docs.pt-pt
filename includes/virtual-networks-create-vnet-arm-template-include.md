## <a name="download-and-understand-the-arm-template"></a>Transferir e entender o modelo ARM
Pode transferir o modelo ARM existente para criar uma VNet e duas sub-redes a partir do github, efetuar as alterações que pretender e reutilizá-lo. Para tal, siga os passos abaixo.

1. Navegue até à [página do modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Clique em **azuredeploy.json** e em **RAW**.
3. Guarde o ficheiro numa pasta local do computador.
4. Se estiver familiarizado com os modelos ARM, avance para o passo 7.
5. Abra o ficheiro que guardou e observe o conteúdo em **parâmetros**, na linha 5. Os parâmetros do modelo ARM fornecem um marcador de posição para valores que podem ser preenchidos durante a implementação.
   
   | Parâmetro | Descrição |
   | --- | --- |
   | **localização** |Região do Azure onde a VNet vai ser criada |
   | **vnetName** |Nome para a nova VNet |
   | **addressPrefix** |Espaço de endereços da VNet, no formato CIDR |
   | **subnet1Name** |Nome para a primeira VNet |
   | **subnet1Prefix** |Bloco CIDR para a primeira sub-rede |
   | **subnet2Name** |Nome para a segunda VNet |
   | **subnet2Prefix** |Bloco CIDR para a segunda sub-rede |
   
   > [!IMPORTANT]
   > Os modelos ARM mantidos no github podem ser alterados ao longo do tempo. Verifique o modelo antes de o utilizar.
   > 
   > 
6. Verifique o conteúdo em **recursos** e tenha em atenção o seguinte:
   
   * **tipo**. Tipo de recurso a ser criado pelo modelo. Neste caso, **Microsoft.Network/virtualNetworks**, que representa uma VNet.
   * **nome**. Nome do recurso. Tenha em atenção a utilização de **[parameters('vnetName')]**, que significa que o nome será fornecido como uma entrada pelo utilizador ou por um ficheiro de parâmetros durante a implementação.
   * **propriedades**. Lista de propriedades do recurso. Este modelo utiliza as propriedades do espaço de endereços e da sub-rede durante a criação da VNet.
7. Regresse à [página do modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Guarde o ficheiro numa pasta local do computador.
10. Abra o ficheiro que guardou e edite os valores dos parâmetros. Utilize os valores abaixo para implementar a VNet descrita no nosso cenário.
    
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
11. Guarde o ficheiro.



<!--HONumber=Nov16_HO2-->


