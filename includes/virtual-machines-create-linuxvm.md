
1. Siga os passos listados em [Connect to Azure from the Azure CLI 1.0 (Ligar ao Azure a partir do CLI do Azure 1.0)](../articles/xplat-cli-connect.md) para iniciar sessão na sua subscrição do Azure.

2. Confirme que está no modo de implementação Clássica, da seguinte forma:

    ```azurecli
    azure config mode asm
    ```

3. Encontre a imagem do Linux que pretende carregar a partir das imagens disponíveis, da seguinte forma:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Numa janela da linha de comandos do Windows, utilize **find** em vez de grep.
   
4. Utilize `azure vm create` para criar uma VM com a imagem do Linux da lista anterior. Este passo cria um serviço cloud e uma conta de armazenamento. Também pode ligar esta VM a um serviço cloud existente com uma opção `-c`. Crie um ponto final SSH para iniciar sessão na máquina virtual do Linux com a opção `-e`. O exemplo seguinte cria uma VM com o nome `myVM` com a imagem `Ubuntu-14_04_4-LTS` na localização `West US` e adiciona um nome de utilizador, `ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Para uma máquina virtual do Linux, tem de fornecer a opção `-e` em `vm create`. Não é possível ativar o SSH depois de a máquina virtual ter sido criada. Para obter mais detalhes, veja [Como utilizar SSH com Linux no Azure](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Pode verificar os atributos da VM com o comando `azure vm show`. O exemplo seguinte lista informações da VM com o nome `myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. Inicie a VM com o comando `azure vm start`, da seguinte forma:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter detalhes sobre todos estes comandos de máquinas virtuais do CLI do Azure 1.0, leia [Using the Azure CLI 1.0 with the Classic deployment API (Utilizar o CLI do Azure 1.0 com a API de implementação Clássica)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).



<!--HONumber=Feb17_HO2-->


