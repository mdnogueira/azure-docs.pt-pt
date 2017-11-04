O 2.0 CLI do Azure permite-lhe criar e gerir os recursos do Azure no macOS, Linux e Windows. Este artigo fornece detalhes sobre alguns dos comandos mais comuns para criar e gerir máquinas virtuais (VMs).

Este artigo requer a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). Também pode utilizar [nuvem Shell](/azure/cloud-shell/quickstart) partir do seu browser.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandos básicos do Azure Resource Manager na CLI do Azure
Para obter mais ajuda com os parâmetros da linha de comandos específicos e as opções, pode utilizar a ajuda do comando online e as opções, escrevendo `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Criar VMs
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Criar um grupo de recursos | `az group create --name myResourceGroup --location eastus` |
| Criar uma VM do Linux | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Criar uma VM do Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Gerir o estado VM
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Iniciar uma VM | `az vm start --resource-group myResourceGroup --name myVM` |
| Parar uma VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| Desalocar uma VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Reiniciar uma VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| Reimplementar uma VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Eliminar uma VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Obter as informações de VM
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Listar VMs | `az vm list` |
| Obter informações sobre uma VM | `az vm show --resource-group myResourceGroup --name myVM` |
| Obter a utilização de recursos de VM | `az vm list-usage --location eastus` |
| Obter todos os tamanhos de VM disponíveis | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Discos e imagens
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Adicionar um disco de dados a uma VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new ` |
| Remover um disco de dados de uma VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Redimensionar um disco | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Tirar um instantâneo de um disco | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Criar a imagem de uma VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Criar a VM da imagem | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais dos comandos da CLI, consulte o [criar e gerir VMs com Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md) tutorial.

