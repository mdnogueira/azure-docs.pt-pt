# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Através de gerido discos de modelos Azure Resource Manager

Este documento explica as diferenças entre discos geridos e quando utilizar modelos Azure Resource Manager para aprovisionar as máquinas virtuais. Isto irá ajudá-lo para atualizar os modelos existentes que estão a utilizar discos não geridos para gerido discos. Para referência, estamos a utilizar o [101 vm-simples windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) modelo como guia. Pode ver o modelo utilizando ambos [discos geridos pelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) e uma versão anterior utilizando [não gerido discos](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) se gostaria de diretamente compará-los.

## <a name="unmanaged-disks-template-formatting"></a>Modelo de discos não gerido formatação

Para começar, vamos veja são implementadas em discos como não geridos. Quando criar discos não geridos, tem uma conta de armazenamento para armazenar os ficheiros VHD. Pode criar uma nova conta de armazenamento ou utilizar uma que já existe. Este artigo irá mostrar como criar uma nova conta de armazenamento. Para tal, precisa de um recurso de conta de armazenamento no bloco de recursos, conforme mostrado abaixo.

```
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Dentro do objeto de máquina virtual, é necessário uma dependência na conta de armazenamento para se certificar de que foi criado antes da máquina virtual. Dentro do `storageProfile` secção, vamos, em seguida, especifique o URI completo da localização do VHD, o que faz referência a conta de armazenamento e é necessário para o disco do SO e discos de dados. 

```
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Gerido a formatação do modelo de discos

Com os discos gerida do Azure, o disco torna-se de um recurso de nível superior e já não necessita de uma conta de armazenamento criados pelo utilizador. Discos geridos pela primeira vez foram expostos no `2016-04-30-preview` versão de API, estão disponíveis em todas as versões de API subsequentes e estão agora o tipo de disco predefinido. As secções seguintes guiá-lo através das predefinições e como personalizar ainda mais os discos de detalhe.

> [!NOTE]
> É recomendado utilizar uma versão de API posterior à `2016-04-30-preview` como ocorreram alterações entre `2016-04-30-preview` e `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Disco gerido predefinições

Para criar uma VM com discos geridos, já não terá de criar o armazenamento conta recursos e pode atualizar o recurso de máquina virtual da seguinte forma. Especificamente tenha em atenção que o `apiVersion` reflete `2017-03-30` e `osDisk` e `dataDisks` Consulte já não é um URI específico para o VHD. Quando implementar sem especificar propriedades adicionais, utilizará o disco [armazenamento LRS padrão](../articles/storage/common/storage-redundancy.md). Não se for especificado nenhum nome, assume o formato `<VMName>_OsDisk_1_<randomstring>` para o disco de SO e `<VMName>_disk<#>_<randomstring>` para cada disco de dados. Por predefinição, a encriptação de disco do Azure está desativada; a colocação em cache é leitura/escrita para o disco do SO e nenhum dos discos de dados. Poderá reparar no exemplo abaixo que há uma dependência de conta de armazenamento, embora isto é apenas para armazenamento de diagnóstico e não é necessária para armazenamento em disco.

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Utilizar um recurso de nível superior de disco gerido

Como alternativa a especificar a configuração do disco no objeto de máquina virtual, pode criar um recurso de disco de nível superior e ligá-lo como parte da criação de máquina virtual. Por exemplo, podemos criar um recurso de disco da seguinte forma para utilizar como um disco de dados.

```
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dentro do objeto VM, em seguida, iremos pode referenciar este objeto de disco ligado. Especificar o ID de recurso do disco gerido foi criada no `managedDisk` propriedade permite o anexo do disco, como é criada a VM. Tenha em atenção que o `apiVersion` para a VM, o recurso está definido `2017-03-30`. Note também que criámos uma dependência no recurso de disco para garantir que é criado com êxito antes da criação da VM. 

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Criar conjuntos de disponibilidade geridos com VMs com discos geridos

Para criar gerido conjuntos de disponibilidade com VMs com discos geridos, adicione o `sku` objeto a disponibilidade do conjunto de recursos e defina o `name` propriedade `Aligned`. Isto garante que os discos para cada VM são suficientemente isolados umas das outras para evitar pontos únicos de falha. Tenha também em atenção que o `apiVersion` para o conjunto de disponibilidade recurso está definido como `2017-03-30`.

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="additional-scenarios-and-customizations"></a>Cenários adicionais e personalizações

Para obter informações completas sobre as especificações de REST API, reveja o [criar um disco gerido documentação da REST API](/rest/api/manageddisks/disks/disks-create-or-update). Encontrará noutros cenários, bem como predefinição e os valores aceitáveis que podem ser submetidos para a API através de implementações do modelo. 

## <a name="next-steps"></a>Passos seguintes

* Para modelos completos que utilizem discos geridos visite as seguintes ligações do repositório de início rápido do Azure.
    * [VM do Windows com o disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [VM com Linux com o disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Obter uma lista completa dos modelos de disco gerido](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Visite o [descrição geral do discos geridos Azure](../articles/virtual-machines/windows/managed-disks-overview.md) documento para saber mais sobre discos geridos.
* Consulte a documentação de referência de modelo para recursos da máquina virtual, visitando o [referência ao modelo Microsoft.Compute/virtualMachines](/templates/microsoft.compute/virtualmachines) documento.
* Consulte a documentação de referência de modelo para recursos de disco, visitando o [referência ao modelo Microsoft.Compute/disks](/templates/microsoft.compute/disks) documento.
 
