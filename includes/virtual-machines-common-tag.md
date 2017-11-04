


## <a name="tagging-a-virtual-machine-through-templates"></a>Marcação de uma Máquina Virtual através de modelos
Em primeiro lugar, vamos ver etiquetagem através de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) etiquetas, coloca nos seguintes recursos: computação (Máquina Virtual), de armazenamento (conta de armazenamento) e de rede (endereço IP público, rede Virtual e Interface de rede). Este modelo é para uma VM do Windows, mas pode ser adaptado para VMs com Linux.

Clique em de **implementar no Azure** no botão do [ligação de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Isto irá navegue para o [portal do Azure](https://portal.azure.com/) onde pode implementar este modelo.

![Implementação simples com etiquetas](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Este modelo inclui as seguintes tags: *departamento*, *aplicação*, e *criado pelo*. Pode adicionar/editar estas etiquetas diretamente no modelo se pretender que os nomes de etiqueta diferentes.

![Etiquetas do Azure num modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como pode ver, as etiquetas são definidas como pares chave/valor, separados por ponto e vírgula (:). As etiquetas tem de ser definidas neste formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Guarde o ficheiro de modelo depois de concluir a edição-lo com as etiquetas da sua preferência.

Em seguida, no **Editar parâmetros** secção, pode preencher os valores para as suas etiquetas.

![Editar etiquetas no portal do Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique em **criar** para implementar este modelo com os valores de etiqueta.

## <a name="tagging-through-the-portal"></a>Marcação através do Portal
Depois de criar os recursos com etiquetas, pode ver, adicionar e eliminar etiquetas no portal.

Selecione o ícone de etiquetas para ver as suas etiquetas:

![Ícone de etiquetas no portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicione uma nova etiqueta através do portal, definindo o seu próprio par chave/valor e guardá-lo.

![Adicionar nova etiqueta no portal do Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

A nova etiqueta deverá agora aparecer na lista de etiquetas para o seu recurso.

![Nova etiqueta guardada no portal do Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

