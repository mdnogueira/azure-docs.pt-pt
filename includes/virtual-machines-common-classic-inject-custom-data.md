


Este tópico descreve como:

* Inserir dados numa máquina virtual do Azure (VM) ao que está a ser aprovisionado.
* Obtê-lo para o Windows e Linux.
* Utilize ferramentas especiais disponíveis em alguns sistemas para detetar e processar dados personalizados automaticamente.

> [!NOTE]
> Este artigo descreve como personalizados dados podem ser injetadas através da utilização de uma VM criada com a API de gestão de serviço do Azure. Para ver como utilizar a API de gestão de recursos do Azure, consulte [o modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Dados personalizados inserirem-se para a máquina virtual do Azure
Esta funcionalidade atualmente só é suportada no [Interface de linha de comandos do Azure](https://github.com/Azure/azure-xplat-cli). Aqui criamos uma `custom-data.txt` ficheiro que contém os nossos dados, em seguida, inserir que para a VM durante o aprovisionamento. Embora possa utilizar qualquer uma das opções para o `azure vm create` comando, o seguinte demonstra uma abordagem muito básica:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Dados personalizados a utilizar na máquina virtual
* Se a VM do Azure é uma VM com base no Windows, em seguida, o ficheiro de dados personalizada é guardado `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Embora foi codificado em base64 para transferir a partir do computador local para a nova VM, este é automaticamente descodificado e pode ser aberta ou utilizada imediatamente.
  
  > [!NOTE]
  > Se o ficheiro existir, será substituído. A segurança no diretório está definida como **controlo de sistema: completo** e **administradores: completo controlo**.
  > 
  > 
* Se a VM do Azure é uma VM baseado em Linux, em seguida, o ficheiro de dados personalizados estarão localizado em um dos locais seguintes, dependendo do seu distro. Os dados podem ser codificados base64, por isso terá de descodificar primeiro os dados:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Init de nuvem no Azure
Se a VM do Azure é a partir de uma imagem Ubuntu ou CoreOS, em seguida, pode utilizar CustomData para enviar uma configuração de nuvem para a nuvem init. Ou, se o ficheiro de dados personalizada é um script, em seguida, na nuvem init pode simplesmente executá-lo.

### <a name="ubuntu-cloud-images"></a>Ubuntu nuvem imagens
Na maioria das imagens de Linux do Azure, seria editar "/ etc/waagent.conf" para configurar o disco de recursos temporário e ficheiro de comutação. Consulte [guia de utilizador do agente Linux do Azure](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações.

No entanto, nas imagens de nuvem Ubuntu, tem de utilizar init da nuvem para configurar o disco de recursos (ou seja, o disco "efémeras") e trocar de partição. Consulte a página seguinte no wiki do Ubuntu para obter mais detalhes: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Próximas etapas: nuvem init a utilizar
Para obter mais informações, consulte o [documentação de nuvem init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Adicionar referência de API de REST de gestão de serviço de função](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de linha de comandos do Azure](https://github.com/Azure/azure-xplat-cli)

