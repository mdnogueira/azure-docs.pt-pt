### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
O **serviço ligado do Storage do Azure** permite-lhe associar uma conta de armazenamento do Azure a um Azure data factory, utilizando o **chave da conta**, que fornece a fábrica de dados com acesso global para o armazenamento do Azure. A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do Storage do Azure.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **AzureStorage** |Sim |
| connectionString |Especificar as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. |Sim |

Consulte o seguinte artigo para obter passos para ver/copiar a chave de conta para um Storage do Azure: [ver, copiar e armazenamento voltar a gerar chaves de acesso](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

**Exemplo:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

### <a name="azure-storage-sas-linked-service"></a>Serviço ligado do Storage do Azure Sas
Uma assinatura de acesso partilhado (SAS) concede acesso delegado a recursos na sua conta de armazenamento. Permite-lhe conceder que um cliente limitada permissões para objetos na sua conta de armazenamento para um determinado período de tempo e com um conjunto especificado de permissões, sem ter de partilhar as chaves de acesso da conta. O SAS é um URI que abrange nos respetivos parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a SAS, o cliente só tem de passar a SAS para o método ou construtor adequado. Para obter informações detalhadas sobre SAS, consulte [assinaturas de acesso partilhado: compreender o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory agora só suporta **serviço SAS** , mas não a conta SAS. Consulte [tipos de assinaturas de acesso partilhado](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre estes dois tipos e de como construir. Anote o URL de SAS generable a partir do portal do Azure ou o Explorador de armazenamento é um SAS de conta, que não é suportado.
> 

O serviço SAS de armazenamento do Azure ligado permite-lhe ligar uma conta de armazenamento do Azure a um Azure data factory com uma assinatura de acesso partilhado (SAS). Fornece a fábrica de dados com acesso restrito/vínculo de tempo para recursos de todos os/específico (/ contentor do blob) no armazenamento. A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço SAS de armazenamento do Azure ligado. 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **AzureStorageSas** |Sim |
| sasUri |Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure como BLOBs, contentor ou tabela.  |Sim |

**Exemplo:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"   
        }  
    }  
}  
```

Ao criar um **URI de SAS**, considerar o seguinte:  

* Conjunto adequada de leitura/escrita **permissões** em objetos com base na forma como o serviço ligado (leitura, escrita, leitura/escrita) é utilizado na fábrica de dados.
* Definir **hora de expiração** adequadamente. Certifique-se de que o acesso a objetos de armazenamento do Azure não expira dentro do período ativo do pipeline.
* URI deve ser criado no contentor/blob correto ou com base na necessidade de nível da tabela. Um Uri de SAS para um blob do Azure permite que o serviço Data Factory aceder a esse blob específico. Um Uri de SAS a um contentor de Blobs do Azure permite que o serviço Data Factory iterar através de blobs no contentor. Se precisar de fornecer um acesso mais/menos objetos mais tarde, ou atualizar o URI de SAS, não se esqueça de atualizar o serviço ligado com o URI de novo.   

