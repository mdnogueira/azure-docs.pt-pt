# <a name="persist-files-in-azure-cloud-shell"></a>Manter os ficheiros na Shell de nuvem do Azure
Shell de nuvem utiliza o File storage do Azure para manter os ficheiros entre sessões.

## <a name="set-up-a-clouddrive-file-share"></a>Configurar uma partilha de ficheiros clouddrive
No início inicial, Shell da nuvem pede-lhe para associar uma partilha de ficheiros novo ou existente para manter os ficheiros entre sessões.

> [!NOTE]
> Bash e PowerShell partilham a mesma partilha de ficheiros. Partilha de ficheiros só pode ser associada a montagem automática na Shell de nuvem.

### <a name="create-new-storage"></a>Criar o novo armazenamento

Quando utilizar definições básicas e selecione apenas uma subscrição, Shell da nuvem cria três recursos em seu nome na região suportada que seja nearest to:
* Grupo de recursos:`cloud-shell-storage-<region>`
* Conta de armazenamento:`cs<uniqueGuid>`
* Partilha de ficheiros:`cs-<user>-<domain>-com-<uniqueGuid>`

![A definição de subscrição](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Monta a partilha de ficheiros como `clouddrive` no seu `$Home` diretório. Esta é uma ação única e a partilha de ficheiros automaticamente monta nas sessões subsequentes. 

Bash, a partilha de ficheiros também contém uma imagem de 5 GB é criada para si que automaticamente mantém os dados no seu `$Home` diretório. 

### <a name="use-existing-resources"></a>Utilizar recursos existentes

Ao utilizar a opção avançada, pode associar recursos existentes. Quando for apresentada a linha de comandos do programa de configuração da armazenamento, selecione **mostrar as definições avançadas** para ver as opções adicionais. Os menus de lista pendente são filtrados para a região de nuvem Shell atribuída e o armazenamento localmente redundante e contas de armazenamento georredundante.

De Bash, partilhas de ficheiros existentes recebem uma imagem de 5 GB criada para si manter o `$Home` diretório.

![A definição de grupo de recursos](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
As contas de armazenamento que criou na Shell de nuvem são etiquetadas com `ms-resource-usage:azure-cloud-shell`. Se pretender não permitir aos utilizadores criar contas de armazenamento na nuvem Shell, crie um [política de recursos do Azure para etiquetas](../articles/azure-policy/json-samples.md) que são acionadas por esta tag específica.

## <a name="supported-storage-regions"></a>Regiões de armazenamento suportadas
Associadas têm de residir na mesma região que a máquina de Shell de nuvem que está a montá-los para contas de armazenamento do Azure.

Para localizar a sua região atribuído, que o utilizador pode:
* Veja a nota da caixa de diálogo "definições de armazenamento avançada"
* Consulte o nome da conta de armazenamento criado por si (ex: `cloud-shell-storage-westus`)
* Executar `env` e localize a variável`ACC_LOCATION`

Máquina de Shell de nuvem existe nas seguintes regiões:
|Área|Região|
|---|---|
|Américas|EUA oeste EUA leste, EUA Centro-Sul,|
|Europa|Europa do Norte, Europa Ocidental|
|Ásia-Pacífico|Índia Central, Sudeste asiático|

