---
title: "Perguntas mais frequentes de conjuntos de dimensionamento de máquina virtual do Azure | Microsoft Docs"
description: "Obtenha respostas às perguntas mais frequentes sobre os conjuntos de dimensionamento de máquina virtual."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 2f7696e207b077f8ae31751f0b6e15459aa1ed52
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Perguntas mais frequentes de conjuntos de dimensionamento de máquina virtual do Azure

Obtenha respostas às perguntas mais frequentes sobre os conjuntos de dimensionamento de máquina virtual no Azure.

## <a name="autoscale"></a>Dimensionamento Automático

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quais são as melhores práticas para o dimensionamento automático do Azure?

Para melhores práticas para o dimensionamento automático, consulte [melhores práticas para máquinas virtuais de dimensionamento automático](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Onde posso encontrar os nomes de métricos de dimensionamento automático que utiliza a métrica do anfitrião?

Para nomes de métricos de dimensionamento automático que utiliza a métrica baseada no anfitrião, consulte [suportado métricas com a monitorização do Azure](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existem os exemplos de dimensionamento automático com base num comprimento de fila e o tópico do Service Bus do Azure?

Sim. Para obter exemplos de dimensionamento automático com base num comprimento de fila e o tópico do Service Bus do Azure, consulte [métricas comuns de dimensionamento automático de Monitor de Azure](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Para uma fila de barramento de serviço, utilize o seguinte JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Para uma fila de armazenamento, utilize o seguinte JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Substitua os valores de exemplo pelo seu recurso Uniform Resource Identifiers (URIs).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Deve posso dimensionamento automático utilizando as métricas baseada no anfitrião ou uma extensão de diagnóstico?

Pode criar uma definição de dimensionamento automático numa VM a utilizar as métricas de nível de anfitrião ou métricas de com base no SO convidado.

Para obter uma lista das métricas suportadas, consulte [métricas comuns de dimensionamento automático de Monitor de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Para obter um exemplo completo para conjuntos de dimensionamento de máquina virtual, consulte [configuração avançada de dimensionamento automático através de modelos do Resource Manager para conjuntos de dimensionamento de máquina virtual](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

Este exemplo utiliza a métrica de CPU ao nível do anfitrião e uma métrica de contagem de mensagens.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Como definir a regras de alertas sobre um conjunto de dimensionamento de máquina virtual

Pode criar alertas nas métricas para conjuntos de dimensionamento de máquina virtual através do PowerShell ou a CLI do Azure. Para obter mais informações, consulte [Azure Monitor PowerShell rápido iniciar amostras](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) e [CLI de várias plataformas de Monitor de Azure rápido iniciar amostras](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId do conjunto de dimensionamento de máquina virtual tem o seguinte aspeto: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/Providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Pode escolher qualquer contador de desempenho da VM como a métrica para definir um alerta para. Para obter mais informações, consulte [métricas de SO convidado para VMs do Windows baseados no Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) e [métricas de SO convidado para VMs com Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) no [métricas comuns de dimensionamento automático Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)artigo.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Como configurar Dimensionar automaticamente conforme um conjunto utilizando o PowerShell de dimensionamento de máquina virtual?

Para configurar o dimensionamento automático numa escala de máquina virtual definido através do PowerShell, consulte a mensagem de blogue [como adicionar dimensionamento automático para um conjunto de dimensionamento da máquina virtual do Azure](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/).




## <a name="certificates"></a>Certificados

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Como posso enviar em segurança um certificado para a VM? Como aprovisionar um conjunto para executar um Web site onde vem incluído o SSL para o Web site de forma segura de uma configuração de certificado de dimensionamento de máquina virtual? (A operação de rotação comum do certificado seria quase o mesmo que uma operação de atualização da configuração.) É necessário um exemplo de como fazê-lo? 

Para enviar em segurança um certificado para a VM, pode instalar um certificado de cliente diretamente para um arquivo de certificados do Windows do Cofre de chaves do cliente.

Utilize o seguinte JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

O código suporta o Windows e Linux.

Para obter mais informações, consulte [criar ou atualizar um dimensionamento da máquina virtual definir](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Exemplo de certificado autoassinado

1.  Crie um certificado autoassinado no Cofre de chaves.

    Utilize os seguintes comandos do PowerShell:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Login-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Este comando dá-lhe a entrada para o modelo Azure Resource Manager.

    Para obter um exemplo de como criar um certificado autoassinado no Cofre de chaves, consulte [cenários de segurança de cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Altere o modelo do Resource Manager.

    Adicionar esta propriedade para **virtualMachineProfile**, como parte da máquina virtual conjunto de dimensionamento de recursos:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Pode especificar um par de chaves SSH a utilizar para autenticação SSH com uma escala de máquina virtual Linux definido a partir de um modelo do Resource Manager?  

Sim. A API REST para **osProfile** é semelhante à API de REST de VM padrão. 

Incluir **osProfile** no seu modelo:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Este bloco JSON é utilizado na [o modelo de início rápido do GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
O perfil de SO também é utilizado no [o grelayhost.json GitHub rápido iniciar modelo](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Para obter mais informações, consulte [criar ou atualizar um dimensionamento da máquina virtual definir](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Como posso remover certificados preteridos? 

Para remover certificados preteridos, remova o antigo certificado na lista de certificados do cofre. Deixe todos os certificados que pretender permanecem no seu computador na lista. Isto não remove o certificado de todas as suas VMs. Também não adiciona o certificado para novas VMs que são criadas no conjunto de dimensionamento de máquina virtual. 

Para remover o certificado de VMs existentes, escreva uma extensão de script personalizado para remover manualmente os certificados do arquivo de certificados.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Como posso inserir uma chave pública SSH para a camada SSH de conjunto de dimensionamento de máquina virtual durante o aprovisionamento Pretende armazenar os valores de chave públicos SSH no Cofre de chaves do Azure e, em seguida, utilizá-los no meu modelo do Resource Manager.

Se está a fornecer as VMs apenas com uma chave SSH pública, não precisa de colocar as chaves públicas no Cofre de chaves. As chaves públicas não são secretas.
 
Pode fornecer as chaves públicas SSH em texto simples quando criar uma VM com Linux:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
nome do elemento linuxConfiguration | Necessário | Tipo | Descrição
--- | --- | --- | --- |  ---
SSH | Não | Coleção | Especifica a configuração da chave SSH para um SO Linux
Caminho | Sim | Cadeia | Especifica o caminho de ficheiro do Linux em que as chaves SSH ou o certificado deve estar localizado
keyData | Sim | Cadeia | Especifica uma chave pública de SSH com codificação base64

Por exemplo, consulte [o modelo de início rápido do GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Quando executar `Update-AzureRmVmss` depois de adicionar mais de um certificado do Cofre de chave do mesmo, posso ver a mensagem seguinte:
 
>Update-AzureRmVmss: Segredo de lista contém instâncias repetidas de /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} < minha subscrição-id > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, que não é permitida.
 
Isto pode acontecer se tentar voltar a adicionar o mesmo cofre em vez de utilizar um novo certificado de cofre para o Cofre de origem existente. O `Add-AzureRmVmssSecret` comando não funcionar corretamente, se estiver a adicionar segredos adicionais.
 
Para adicionar mais segredos do Cofre de chave do mesmo, atualize a lista de $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Para a estrutura de entrada esperada, consulte [criar ou atualizar uma máquina virtual definir](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Encontrar o segredo no objeto do conjunto de dimensionamento de máquina virtual que está no Cofre de chaves. Em seguida, adicione uma referência de certificado (o URL e o nome de arquivo secreta) para a lista associada ao cofre.

> [!NOTE] 
> Atualmente, não é possível remover certificados dos VMs, utilizando a API de conjunto de dimensionamento de máquina virtual.
>

Novas VMs não terão o certificado antigo. No entanto, as VMs que têm o certificado e que já são implementadas terão o certificado antigo.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Pode emitir certificados para o conjunto sem fornecer a palavra-passe, quando o certificado está no arquivo de segredos de dimensionamento de máquina virtual?

Não é necessário para palavras-passe de codificar em scripts. Pode obter dinamicamente palavras-passe com as permissões que utiliza para executar o script de implementação. Se tiver um script que se move um certificado a partir da chave secreta arquivo cofre, o arquivo de secreto `get certificate` comando também produz a palavra-passe do ficheiro. pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Como é que a propriedade de segredos de virtualMachineProfile.osProfile para um dimensionamento da máquina virtual definida trabalho? Por que motivo é necessário o valor de sourceVault quando tenho de especificar o URI absoluto de um certificado utilizando a propriedade certificateUrl? 

Uma referência de certificado de gestão remota do Windows (WinRM) tem de estar presente na propriedade segredos do perfil de SO. 

O objetivo de indicar o Cofre de origem é para impor políticas de lista (ACL) de controlo de acesso que existem no modelo de serviço de nuvem do Azure de um utilizador. Se o Cofre de origem não está especificado, os utilizadores que não tem permissões para implementar ou segredos para um cofre de chaves de acesso conseguiriam através de um fornecedor de recursos de computação (CRP). As ACLs existem mesmo para recursos que não existem.

Se fornecer um ID de Cofre de origem incorreto, mas um URL do Cofre de chaves válida, é comunicado um erro ao consultar a operação.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Se adiciono segredos a um existente o dimensionamento da máquina virtual definida, são segredos injetados para VMs existentes, ou apenas para novos? 

Certificados são adicionados a todas as suas VMs, mesmo pré-existentes aqueles. Se o dimensionamento da máquina virtual definir a propriedade de upgradePolicy estiver definido como **manual**, o certificado foi adicionado à VM quando efetuar uma atualização manual da VM.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Onde colocar o certificados para VMs com Linux?

Para saber como implementar certificados para VMs com Linux, consulte [implementar certificados para as VMs a partir de um cofre de chaves gerida pelo cliente](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Como adicionar um novo certificado do cofre a para um novo objeto de certificado?

Para adicionar um certificado de cofre para um segredo existente, consulte o seguinte exemplo do PowerShell. Utilize apenas um objeto secreto.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>O que acontece aos certificados recriar uma VM?

Se criar nova imagem de uma VM, os certificados são eliminados. Elimina o SO todo disco de reprocessamento de imagem. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>O que acontece se eliminar um certificado a partir do Cofre de chaves?

Se o segredo é eliminado do Cofre de chaves e, em seguida, execute `stop deallocate` para todas as suas VMs e, em seguida, iniciá-los novamente, irá ocorrer uma falha. A falha ocorre porque o CRP tem de obter os segredos do Cofre de chaves, mas não pode ser. Neste cenário, pode eliminar os certificados do modelo de conjunto de dimensionamento de máquina virtual. 

O componente de CRP não persiste segredos do cliente. Se executar `stop deallocate` para todas as VMs no conjunto de dimensionamento de máquina virtual, é eliminado da cache. Neste cenário, os segredos são obtidos a partir do Cofre de chaves.

Não ocorrer este problema durante a ampliação porque não existe uma cópia em cache do segredo do Service Fabric do Azure (no modelo de recursos de infraestrutura único inquilino).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Por que razão tem de especificar a localização exata para o URL de certificado (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), conforme indicado na [cenários de segurança de cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
A documentação do Cofre de chaves do Azure indica se a API de REST do segredo obter deverá devolver a versão mais recente do segredo do se a versão não for especificada.
 
Método | URL
--- | ---
INTRODUÇÃO | https://mykeyvault.Vault.Azure.NET/Secrets/ {nome-segredo} / {segredo-version}? api-version = {api-version}

Substitua {*segredo-name*} com o nome e a substituir {*segredo versão*} com a versão do segredo que pretende obter. A versão secreta poderão ser excluída. Nesse caso, é obtida a versão atual.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Por que motivo é necessário especificar a versão de certificado quando utilizar o Cofre de chaves?

O objetivo do requisito do Cofre de chaves para especificar a versão do certificado é tornar claro que o utilizador que certificado implementado nas respetivas VMs.

Se criar uma VM e, em seguida, atualize o seu segredo no Cofre de chaves, o novo certificado não é transferido para as suas VMs. Mas são apresentadas as suas VMs referenciá-la e novas VMs obter o segredo novo. Para evitar isto, são necessários para fazer referência a uma versão secreta.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A minha equipa funciona com vários certificados que são distribuídos para nós como chaves públicas. cer. O que é definida a abordagem recomendada para implementar estes certificados para um dimensionamento da máquina virtual?

Para implementar a. cer definir as chaves públicas para um dimensionamento da máquina virtual, pode gerar um ficheiro. pfx que contém apenas os ficheiros. cer. Para tal, utilize `X509ContentType = Pfx`. Por exemplo, carregue o ficheiro. cer como um objeto x509Certificate2 c# ou o PowerShell e, em seguida, chame o método. 

Para obter mais informações, consulte [X509Certificate.Export método (X509ContentType, cadeia)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Não consigo ver uma opção para os utilizadores passem certificados como cadeias de base64. A maioria dos fornecedores de recursos tem esta opção.

Para emular transmissão num certificado como uma cadeia base64, pode extrair o URL com a versão mais recente num modelo do Resource Manager. Incluem a seguinte propriedade JSON no seu modelo do Resource Manager:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>É necessário que encapsular certificados em objetos JSON no cofres de chaves?

Em conjuntos de dimensionamento de máquina virtual e VMs, certificados tem de ser moldados numa objetos JSON. 

Podemos também suporta o tipo de conteúdo application/x-pkcs12. Para obter instruções sobre como utilizar o application/x-pkcs12, consulte [os certificados PFX no Cofre de chaves do Azure](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Atualmente não suportamos. cer ficheiros. Para utilizar ficheiros. cer, exportá-las em contentores. pfx.



## <a name="compliance-and-security"></a>Segurança e de conformidade

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Estão em conformidade de PCI define o dimensionamento da máquina virtual?

Os conjunto de dimensionamento de máquinas virtuais são uma fina camada de API sobreposta ao CRP. Os dois componentes fazem parte da plataforma de computação da árvore de serviço do Azure.

Numa perspetiva de conformidade, os conjuntos de dimensionamento de máquinas virtuais são uma parte fundamental da plataforma de computação do Azure. Partilham uma equipa, ferramentas, processos, metodologia de implementação, controlos de segurança, compilação just-in-time (JIT), monitorização, alertas e assim sucessivamente, com o próprio CRP. Os conjuntos de dimensionamento de máquinas virtuais estão conformes ao Payment Card Industry (PCI) porque o CRP faz parte do atestado da atual Norma de Segurança dos Dados (DSS) da PCI.

Para obter mais informações, veja o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-vm-scale-sets"></a>Does [identidade de serviço gerida do Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) trabalhar com conjuntos de dimensionamento VM?

Sim. Pode ver alguns modelos MSI do exemplo nos modelos de início rápido do Azure. Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Extensões

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Como posso eliminar uma extensão de conjunto de dimensionamento de máquina virtual?

Para eliminar uma extensão de conjunto de dimensionamento de máquina virtual, utilize o seguinte exemplo de PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Pode encontrar o valor extensionName `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-operations-management-suite"></a>Existe que um dimensionamento da máquina virtual definir exemplo de modelo que se integra com o Operations Management Suite?

Para um dimensionamento da máquina virtual definido exemplo de modelo que se integra com o Operations Management Suite, consulte o segundo exemplo na [implementar um cluster do Service Fabric do Azure e ative a monitorização utilizando a análise de registos](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Extensões aparentemente, executadas em paralelo em conjuntos de dimensionamento de máquina virtual. Isto faz com que a minha extensão de script personalizado falhou. O que posso fazer para corrigir este problema?

Para saber mais sobre a sequenciação de extensão em conjuntos de dimensionamento de máquina virtual, consulte [sequenciação extensão em conjuntos de dimensionamento de máquina virtual do Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Como repor a palavra-passe para as VMs no meu conjunto de dimensionamento de máquina virtual?

Para repor a palavra-passe para as VMs no seu conjunto de dimensionamento de máquina virtual, utilize as extensões de acesso VM. 

Utilize o exemplo do PowerShell seguinte:

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Como adicionar uma extensão para todas as VMs no meu conjunto de dimensionamento de máquina virtual?

Se a política de atualização está definida como **automática**, voltar a implementar o modelo com as novas propriedades de extensão de atualizações de todas as VMs.

Se a política de atualização está definida como **manual**, primeiro, atualize a extensão e, em seguida, atualizar manualmente todas as instâncias nas suas VMs.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Se forem atualizadas extensões associadas a um conjunto de dimensionamento de máquina virtual existente, existentes VMs afetadas? (Ou seja, serão as VMs *não* corresponde ao modelo de conjunto de dimensionamento de máquina virtual?) Ou são ignorados? Quando um computador existente é serviço-healed ou recriada, é os scripts que estão atualmente configurados no conjunto de dimensionamento de máquina virtual executado ou que são utilizados os scripts que foram configurados quando a VM foi criada pela primeira vez?

Se a definição de extensão de dimensionamento da máquina virtual é atualizar o modelo e a propriedade upgradePolicy está definida como **automática**, atualiza as VMs. Se a propriedade upgradePolicy estiver definida como **manual**, extensões sinalizadas como não corresponde ao modelo. 

Se uma VM existente healed de serviço, é apresentada como reiniciar o computador e as extensões não são novamente. Se é recriada, é como substituir o disco de SO com a imagem de origem. Qualquer especialização do modelo mais recente, tais como as extensões, são executados.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-azure-ad-domain"></a>Como aderir a um conjunto a um domínio do Azure AD de dimensionamento de máquina virtual?

Para associar um conjunto a um domínio do Azure Active Directory (Azure AD) de dimensionamento de máquina virtual, pode definir uma extensão. 

Para definir uma extensão, utilize a propriedade JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>A minha extensão de conjunto de dimensionamento da máquina virtual está a tentar instalar algo que requer uma reinicialização. Por exemplo, "commandToExecute": "powershell.exe - ExecutionPolicy irrestrito Install-WindowsFeature-nome FS-Resource-Manager – IncludeManagementTools"

Se a extensão de conjunto de dimensionamento da máquina virtual está a tentar instalar algo que requer um reinício, pode utilizar a extensão do Azure Automation configuração de estado pretendido (DSC de automatização). Se o sistema operativo Windows Server 2012 R2, o Azure obtém na configuração do Windows Management Framework (WMF) 5.0, reinícios e, em seguida, continua com a configuração. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Como posso ativar de antimalware no meu conjunto de dimensionamento de máquina virtual?

Para ativar antimalware no seu conjunto de dimensionamento da máquina virtual, utilize o seguinte exemplo de PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Tem de executar um script personalizado que está alojado numa conta do storage privada. O script é executado com êxito quando o armazenamento é público, mas quando tento utilizar uma assinatura de acesso partilhado (SAS), falhar. Esta mensagem é apresentada: "Em falta os parâmetros obrigatórios para a assinatura de acesso partilhado válida". Ligação + SAS funciona bem a partir do meu local do browser.

Para executar um script personalizado que está alojado numa conta do storage privada, configure as definições de protegidos com a chave de conta de armazenamento e o nome. Para obter mais informações, consulte [extensão de Script personalizado para Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Redes
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-will-apply-to-all-the-vm-nics-in-the-set"></a>É possível atribuir um grupo de segurança de rede (NSG) para um conjunto de dimensionamento, para que será aplicado a todos os NICs de VM no conjunto?

Sim. Um grupo de segurança de rede podem ser aplicado diretamente a uma escala definida pelo que o referenciam na secção networkInterfaceConfigurations o perfil de rede. Exemplo:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Como posso efetuar uma alternância de VIP para conjuntos de dimensionamento de máquina virtual na mesma subscrição e na mesma região?

Se tiver dois conjuntos de dimensionamento de máquina virtual com frente-ends de Balanceador de carga do Azure e estão na mesma subscrição e região, foi possível desalocar os endereços IP públicos de cada um deles e atribuir para outro. Consulte [alternância de VIP: implementação de Blue verde no Gestor de recursos do Azure](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) por exemplo. Isto implica um atraso nível embora os recursos são desalocada/alocada a rede. Uma opção mais rápida consiste em utilizar o Gateway de aplicação do Azure com dois conjuntos de back-end e uma regra de encaminhamento. Em alternativa, pode alojar a aplicação com [App service do Azure](https://azure.microsoft.com/services/app-service/) que fornece suporte para a mudança rápida de entre as ranhuras de teste e produção.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Como posso especificar um intervalo de endereços IP privados a utilizar para a atribuição de endereço IP privada estática?

Endereços IP são selecionados de sub-rede que especificar. 

O método de atribuição de endereços IP de conjunto de dimensionamento de máquina virtual está sempre "dinâmico", mas que não significa que podem alterar estes endereços IP. Neste caso, "dinâmica" apenas significa que não especifique o endereço IP num pedido PUT. Especifique estática definida com a sub-rede. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Como posso implementar um dimensionamento da máquina virtual definido como uma rede virtual do Azure existente? 

Para implementar um dimensionamento da máquina virtual definido como uma rede virtual do Azure existente, consulte [implementar um dimensionamento da máquina virtual definido como uma rede virtual existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Como adicionar o endereço IP da VM primeiro a num dimensionamento de máquina virtual definido para a saída de um modelo?

Para adicionar o endereço IP da VM primeiro um dimensionamento da máquina virtual definido para a saída de um modelo, consulte [ARM: IPs privados obter VMSS](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Pode utilizar conjuntos de dimensionamento com acelerados da rede?

Sim. Para utilizar na melhoria de redes, defina enableAcceleratedNetworking como true no seu dimensionamento definições de networkInterfaceConfigurations do conjunto. Por exemplo,
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Como posso configurar os servidores DNS utilizados por um conjunto de dimensionamento?

Para criar um conjunto com uma configuração de DNS personalizada de dimensionamento VM, adicione um pacote JSON dnsSettings para a secção de networkInterfaceConfigurations do conjunto de dimensionamento. Exemplo:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Como configurar a um conjunto para atribuir um endereço IP público para cada VM de dimensionamento?

Para criar um conjunto de dimensionamento VM que atribui um endereço IP público para cada VM, certifique-se de que a versão da API do recurso Microsoft.Compute/virtualMAchineScaleSets 2017-03-30 e adicione um _publicipaddressconfiguration_ pacote JSON para o secção de ipConfigurations do conjunto de dimensionamento. Exemplo:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Pode configurar uma escala definida para funcionar com vários Gateways de aplicação?

Sim. Pode adicionar o id de recurso para vários conjuntos de endereços de back-end de Gateway de aplicação para o _applicationGatewayBackendAddressPools_ lista o _ipConfigurations_ a secção da sua escala definida rede perfil.

## <a name="scale"></a>Escala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>No caso de que iria criar um conjunto com menos de duas VMs de dimensionamento de máquina virtual?

Uma razão para criar um conjunto com menos de duas VMs de dimensionamento de máquina virtual seria utilizar as propriedades elásticas de um conjunto de dimensionamento de máquina virtual. Por exemplo, pode implementar um conjunto com zero VMs para definir a sua infraestrutura sem pagar VM a executar os custos de dimensionamento de máquina virtual. Em seguida, quando estiver pronto para implementar as VMs, aumente a "capacidade" o dimensionamento da máquina virtual definida para a contagem de instâncias de produção.

Outro motivo, que poderá criar um conjunto com menos de duas VMs de dimensionamento de máquina virtual é se está preocupados com menos com disponibilidade de sessão através de um conjunto de disponibilidade com VMs discretas. Conjuntos de dimensionamento de máquina virtual dão-lhe uma forma para trabalhar com unidades de computação undifferentiated são fungible. Este uniformidade da é um principal diferenciador para conjuntos de dimensionamento de máquina virtual em comparação com conjuntos de disponibilidade. Muitas cargas de trabalho sem monitorização de estado não controlam unidades individuais. Se ignora a carga de trabalho, pode reduzir verticalmente a unidade de computação de um e, em seguida, aumentar verticalmente para muitos quando aumenta a carga de trabalho.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Como posso alterar o número de VMs num conjunto de dimensionamento de máquina virtual?

Para alterar o número de VMs num conjunto de dimensionamento de máquina virtual, consulte [alterar a contagem de instâncias de um conjunto de dimensionamento de máquina virtual](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Como posso definir alertas personalizados para quando determinados limiares são atingidos?

Tem alguma flexibilidade na forma como lidar com alertas de limiares especificados. Por exemplo, pode definir webhooks personalizado. O seguinte exemplo de webhook é a partir de um modelo do Resource Manager:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

Neste exemplo, um alerta vai para Pagerduty.com quando for atingido um limiar.



## <a name="patching-and-operations"></a>Aplicação de patches e operações

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Como posso criar uma escala definida num grupo de recursos existente?

Criar conjuntos de dimensionamento num recurso existente grupo ainda não é possível a partir do portal do Azure, mas pode especificar um grupo de recursos existente quando implementar uma escala definido a partir de um modelo Azure Resource Manager. Também pode especificar um grupo de recursos existente ao criar um conjunto utilizando o Azure PowerShell ou a CLI de dimensionamento.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Pode mover um conjunto para outro grupo de recursos de dimensionamento?

Sim, pode mover o conjunto de dimensionamento de recursos para uma nova subscrição ou grupo de recursos.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Como a posso atualizar os meus dimensionamento da máquina virtual definido como uma nova imagem? Como gerir a aplicação de patches?

Para atualizar o dimensionamento da máquina virtual definido como uma nova imagem e para gerir a aplicação de patches, consulte [Atualize um conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Pode utilizar a operação de recriação de imagem para repor uma VM, sem alterar a imagem? (Ou seja, pretendo que as definições de fábrica em vez de uma nova imagem de reposição uma VM.)

Sim, pode utilizar a operação de recriação de imagem para a reposição de uma VM sem alterar a imagem. No entanto, se o conjunto de dimensionamento da máquina virtual faz referência a uma imagem de plataforma com `version = latest`, a VM pode atualizar uma imagem do SO posterior quando chamar `reimage`.

Para obter mais informações, consulte [gerir todas as VMs num conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-oms-operations-management-suite"></a>É possível integrar conjuntos de dimensionamento com o Azure OMS (Operations Management Suite)?

Sim, pode ao instalar a extensão do OMS na escala definir VMs. Eis um exemplo da CLI do Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Pode encontrar o workspaceId necessária e workspaceKey no portal do OMS. Na página Descrição geral, clique no mosaico de definições. Clique no separador de origens ligadas na parte superior.

Nota: se o conjunto de dimensionamento da sua _upgradePolicy_ está definido para Manual, terá de aplicar a extensão para todas as VMs no conjunto de chamando atualização nos mesmos. Na CLI isto seria _az vmss update-instâncias_.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Como ativar diagnósticos de arranque?

Para ativar o diagnóstico de arranque, primeiro, crie uma conta de armazenamento. Em seguida, colocar este bloco JSON no seu conjunto de dimensionamento de máquina virtual **virtualMachineProfile**e atualizar o conjunto de dimensionamento de máquina virtual:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Quando é criada uma nova VM, a propriedade InstanceView da VM mostra os detalhes para a captura de ecrã e assim sucessivamente. Eis um exemplo:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Propriedades da máquina virtual

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Como posso obter informações sobre propriedades para cada VM sem fazer chamadas de várias? Por exemplo, como faria posso obter o domínio de falhas para cada uma das 100 VMs no meu conjunto de dimensionamento de máquina virtual?

Para obter informações sobre propriedades para cada VM sem fazer chamadas de várias, pode chamar `ListVMInstanceViews` efetuando uma API REST `GET` no URI do recurso seguinte:

/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} < subscription_id > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView & $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Pode passar argumentos da extensão diferentes para diferentes VMs num conjunto de dimensionamento de máquina virtual?

Não, não poder passar os argumentos da extensão diferentes para diferentes VMs num conjunto de dimensionamento de máquina virtual. No entanto, as extensões podem agir com base nas propriedades de exclusivas da VM que executem no, tais como o nome da máquina. Extensões também podem consultar os metadados de instância no http://169.254.169.254 para obter mais informações sobre a VM.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Por que motivo existem intervalos entre a minha nomes de máquina VM de conjunto de dimensionamento de máquina virtual e os IDs de VM? Por exemplo: 0, 1, 3...

Existem intervalos entre os nomes de máquina VM de conjunto de dimensionamento de máquina virtual e os IDs de VM, porque o conjunto de dimensionamento da máquina virtual **bandeira** propriedade está definida como o valor predefinido de **verdadeiro**. Se provocam um aprovisionamento estiver definido como **verdadeiro**, mais VMs que pediu são criados. VMs Extras, em seguida, são eliminadas. Neste caso, obtenha a implementação de uma maior fiabilidade, mas em detrimento da atribuição de nomes contíguo e contíguo tradução de endereços de rede (NAT) regras. 

Pode definir esta propriedade **falso**. Para conjuntos de dimensionamento pequeno máquina virtual, isto não afeta significativamente fiabilidade de implementação.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>O que é a diferença entre a eliminação de uma VM com um conjunto de dimensionamento de máquina virtual e Desalocação da VM? Quando devo escolher um detrimento dos outros?

A principal diferença entre a eliminação de uma VM com um conjunto de dimensionamento de máquina virtual e Desalocação da VM é que `deallocate` não elimina os discos rígidos virtuais (VHDs). Existem custos de armazenamento associados a execução `stop deallocate`. Poderá utilizar um para um dos seguintes motivos:

- Pretende parar a pagar os custos de computação, mas pretende manter o estado de disco das VMs.
- Pretende iniciar um conjunto de VMs mais rapidamente do que foi ampliar um conjunto de dimensionamento de máquina virtual.
  - Relacionados com este cenário, pode ter criado o seu motor de dimensionamento automático e pretender uma escala de ponto a ponto mais rápida.
- Tem um conjunto de dimensionamento de máquina virtual é unevenly distribuído por domínios de falhas ou domínios de atualização. Isto poderá ser porque tem VMs eliminado seletivamente ou porque as VMs foram eliminadas após provocam um aprovisionamento. Executar `stop deallocate` seguido `start` na máquina virtual uniformemente conjunto de dimensionamento distribui as VMs domínios de falhas ou domínios de atualização.

