<properties
   pageTitle="Descrição geral do Azure Resource Manager | Microsoft Azure"
   description="Descreve como utilizar o Azure Resource Manager para a implementação, a gestão e o controlo de acesso de recursos no Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="tomfitz"/>


# Descrição geral do Azure Resource Manager

 Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta do Storage e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. O Azure Resource Manager permite trabalhar com os recursos na sua solução como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação. 

## Terminologia

Se é a primeira vez que utiliza o Azure Resource Manager, existem alguns termos com os quais poderá não estar familiarizado.

- **recurso** -um item gerível que está disponível através do Azure. Alguns recursos comuns são uma máquina virtual, conta de armazenamento, aplicação web, base de dados e rede virtual, mas existem muitos mais.
- **grupo de recursos** - um contentor que retém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Veja [Grupos de recursos](#resource-groups).
- **fornecedor de recursos** - Um serviço que fornece os recursos que pode implementar e gerir através do Resource Manager. Cada fornecedor de recursos oferece operações para trabalhar com os recursos implementados. Alguns fornecedores de recursos comuns são Microsoft.Compute, que fornece o recurso de máquina virtual, o Microsoft.Storage, que fornece o recurso de conta de armazenamento, e o Microsoft.Web que fornece recursos relacionados com aplicações Web. Veja [Fornecedores de recursos](#resource-providers).
- **Modelo do Resource Manager** - Ficheiro de um JavaScript Object Notation (JSON) que define um ou mais recursos para implementar num grupo de recursos. Também define dependências entre os recursos implementados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida. Veja [Implementação de modelos](#template-deployment).
- **sintaxe declarativa** - sintaxe que permite afirmar "Aqui está o que pretendo criar" sem ter de escrever a sequência de comandos de programação para criá-la. O modelo do Resource Manager é um exemplo de sintaxe declarativa. No ficheiro, é possível definir as propriedades da infraestrutura de implementação para o Azure. 

## Vantagens da utilização do Resource Manager

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.
- Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.
- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.
- Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.
- Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.
- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.
- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a mesma etiqueta.  

O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação anterior e pretende obter informações sobre as alterações, consulte [Compreender a implementação do Resource Manager e a implementação clássica](resource-manager-deployment-model.md).

## Orientação

As seguintes sugestões ajudam a tirar o máximo partido do Resource Manager ao trabalhar com as suas soluções.

1. Defina e implemente a infraestrutura através da sintaxe declarativa nos modelos do Resource Manager, em vez de utilizar comandos imperativos.
2. Defina todos os passos de implementação e configuração no modelo. A configuração da sua solução não deverá ter quaisquer passos manuais.
3. Execute comandos imperativo para gerir os recursos, tal como para iniciar ou parar uma aplicação ou uma máquina.
4. Disponha os recursos com o mesmo ciclo de vida num grupo de recursos. Utilize etiquetas para todas as outras organizações de recursos.

Para obter mais recomendações, veja o artigo [Melhores práticas para criar modelos do Azure Resource Manager](resource-manager-template-best-practices.md).

## Grupos de recursos

Existem alguns fatores importantes a considerar ao definir o grupo de recursos:

1. Todos os recursos do grupo devem partilhar o mesmo ciclo de vida. Implemente-os, atualize-os e elimine-os em conjunto. Se um recurso, tal como um servidor de base de dados, tiver de existir num ciclo de implementação diferente, deve ser colocado noutro grupo de recursos.
2. Cada recurso só pode existir num grupo de recursos.
3. Pode adicionar ou remover um recurso de um grupo de recursos em qualquer altura.
4. Pode mover um recurso de um grupo de recursos para outro grupo. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).
4. Um grupo de recursos pode conter recursos que residem em regiões diferentes.
5. Um grupo de recursos pode ser utilizado para definir o âmbito do controlo de acesso para ações administrativas.
6. Um recurso pode interagir com recursos de outros grupos de recursos. Esta interação é comum quando os dois recursos estão relacionados mas não partilham o mesmo ciclo de vida (por exemplo, aplicações Web a ligar a uma base de dados).

Ao criar um grupo de recursos, deve fornecer uma localização para esse grupo de recursos. Pode perguntar-se, "Porque é que um grupo de recursos necessita de uma localização? E, se os recursos podem ter diferentes localizações em relação ao grupo de recursos, por que motivo é que a localização do grupo de recursos é sequer relevante?" O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

## Fornecedores de recursos

Cada fornecedor de recursos oferece um conjunto de recursos e operações para trabalhar com a área técnica. Por exemplo, se pretende armazenar chaves e segredos, trabalha com o fornecedor de recursos **Microsoft.KeyVault**. Este fornecedor de recursos oferece um tipo de recurso denominado **vaults** para criar o cofre de chaves e um tipo de recurso denominado **vaults/secrets** para criar um segredo no cofre de chaves. Fornece também operações através de [Operações de API de REST do Cofre de Chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx). Pode ligar a API de REST diretamente ou pode utilizar os [cmdlets do PowerShell do Cofre de Chaves](https://msdn.microsoft.com/library/dn868052.aspx) e a [CLI do Azure do Cofre de Chaves](./key-vault/key-vault-manage-with-cli.md) para gerir o cofre de chaves. Também pode utilizar várias linguagens de programação para trabalhar com a maioria dos recursos. Para obter mais informações, veja [SDKs e ferramentas](#sdks-and-samples). 

Para implementar e gerir a infraestrutura, é necessário saber detalhes sobre o fornecedor de recursos. É necessário saber os tipos de recursos, os números de versão das operações de API REST, as operações suportadas e o esquema para utilizar na criação de recursos. Para saber mais sobre os fornecedores de recursos suportados, veja [Fornecedores, regiões, versões de API e esquemas do Resource Manager](resource-manager-supported-services.md).

## Implementação de modelos

Com o Resource Manager, pode criar um modelo (no formato JSON) que define a implementação e configuração da sua aplicação. Ao utilizar um modelo, pode implementar repetidamente a aplicação durante todo o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente. O Azure Resource Manager analisa as dependências para garantir que os recursos são criados pela ordem correta. Para obter mais informações, consulte [Definir dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md).

Quando cria uma solução a partir do portal, esta inclui automaticamente um modelo de implementação. Não é necessário criar o modelo a partir do zero porque pode começar com o modelo para a sua solução e personalizá-lo de modo a satisfazer as suas necessidades específicas. Pode obter um modelo para um grupo de recursos existente ao exportar o estado atual do grupo de recursos ou ao visualizar o modelo utilizado para uma implementação específica. Visualizar o modelo exportado é uma forma útil de saber mais sobre a sintaxe do modelo. Para saber mais sobre como trabalhar com modelos exportados, consulte [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).

Não é necessário definir toda a infraestrutura num único modelo. Muitas vezes, faz sentido dividir os requisitos de implementação num conjunto de modelos direcionados e com uma finalidade específica. Pode reutilizar facilmente estes modelos para soluções diferentes. Para implementar uma solução específica, cria um modelo global que liga todos os modelos necessários. Para obter mais informações, consulte [Utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).

Também pode utilizar o modelo para atualizar a infraestrutura. Por exemplo, pode adicionar um recurso à solução e adicionar regras de configuração para os recursos que já estão implementados. Se o modelo especificar a criação de um novo mas esse recurso já existir, o Azure Resource Manager efetua uma atualização em vez de criar um novo recurso. O Azure Resource Manager atualiza o recurso existente para o mesmo estado que teria como novo. Em alternativa, pode especificar que o Resource Manager elimine quaisquer recursos que não estejam especificados no modelo. Para compreender as diferentes opções disponíveis durante a implementação, consulte [Implementar uma aplicação com o modelo do Azure Resource Manager](resource-group-template-deploy.md). 

Pode especificar parâmetros no modelo para permitir personalização e flexibilidade na implementação. Por exemplo, pode passar valores de parâmetros que personalizam a implementação para o seu ambiente de teste. Ao especificar os parâmetros, pode utilizar o mesmo modelo para implementar a solução em ambientes diferentes.

O Resource Manager fornece extensões para cenários em que necessita de operações adicionais como a instalação de software específico que não está incluído na configuração. Se já estiver a utilizar um serviço de gestão de configuração, como o DSC, Chef ou Puppet, pode continuar a trabalhar com esse serviço ao utilizar extensões.

Por último, o modelo torna-se parte do código fonte da sua aplicação. Pode verificá-lo no repositório de código fonte e atualizá-lo à medida que a aplicação evolui. Pode editar o modelo através do Visual Studio.

Para obter mais informações sobre como definir o modelo, consulte [Criação de Modelos do Azure Resource Manager](resource-group-authoring-templates.md).

Para obter instruções passo-a-passo sobre como criar um modelo, consulte [Instruções do Modelo do Resource Manager](resource-manager-template-walkthrough.md).

Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](solution-dev-test-environments.md). 

## Etiquetas

O Resource Manager fornece uma funcionalidade de etiquetagem que permite categorizar os recursos de acordo com os seus requisitos de gestão ou faturação. Utilize etiquetas quando tem uma coleção complexa de grupos de recursos e recursos e precisa de visualizar esses elementos da forma mais adequada para si. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento. Sem etiquetas, os utilizadores da organização podem criar vários recursos que poderá ser difícil identificar e gerir mais tarde. Por exemplo, poderá pretender eliminar todos os recursos de um projeto específico. Se esses recursos não estão marcados para o projeto, terá de encontrá-los manualmente. A etiquetagem pode ser um meio importante para reduzir os custos desnecessários associados à sua subscrição. 

Os recursos não precisam de residir no mesmo grupo de recursos para partilhar uma etiqueta. Pode criar a sua própria taxonomia de etiquetas para se certificar de que todos os utilizadores da sua organização utilizam etiquetas comuns em vez de aplicarem inadvertidamente etiquetas ligeiramente diferentes (por exemplo, “depart” em vez de “departamento”).

Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md). Pode criar uma [política personalizada](#manage-resources-with-customized-policies) que exija a adição de etiquetas aos recursos durante a implementação.

## Controlo de acesso

O Resource Manager permite controlar quem tem acesso a ações específicas para a sua organização. Integra de forma nativa o OAuth e o Controlo de Acesso Baseado em Funções (RBAC) na plataforma de gestão e aplica esse controlo de acesso a todos os serviços no seu grupo de recursos. Pode adicionar utilizadores a funções específicas da plataforma e dos recursos predefinidas e aplicar essas funções a uma subscrição, um grupo de recursos ou um recurso para limitar o acesso. Por exemplo, pode tirar partido da função predefinida denominada Contribuinte da BD do SQL que permite aos utilizadores gerir bases de dados, mas não servidores de base de dados ou políticas de segurança. Adiciona os utilizadores da sua organização que necessitam deste tipo de acesso à função Contribuinte da BD SQL e aplica a função à subscrição, ao grupo de recursos ou ao recurso.

O Resource Manager regista automaticamente as ações do utilizador para auditoria. Para obter informações sobre como trabalhar com os registos de auditoria, consulte [Auditar operações com o Resource Manager](resource-group-audit.md).

Para obter mais informações sobre o controlo de acesso baseado em funções, consulte [Controlo de Acesso Baseado em Funções do Azure](./active-directory/role-based-access-control-configure.md). O tópico [RBAC: Funções Incorporadas](./active-directory/role-based-access-built-in-roles.md) contém uma lista das funções incorporadas e das ações permitidas. As funções incorporadas incluem funções gerais, tais como Proprietário, Leitor e Contribuinte, bem como funções específicas do serviço, tais como Contribuinte de Máquina Virtual, Contribuinte de Virtual Network e Gestor de Segurança do SQL (para indicar apenas algumas das funções disponíveis).

Pode também bloquear explicitamente recursos críticos para impedir que os utilizadores os eliminem ou modifiquem. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

Para melhores práticas, consulte [Considerações de segurança para o Azure Resource Manager](best-practices-resource-manager-security.md)

## Gerir recursos com políticas personalizadas

O Resource Manager permite criar políticas personalizadas para gerir os seus recursos. Os tipos de políticas que cria podem incluir diversos cenários. Pode impor uma convenção de nomenclatura nos recursos, limitar que tipos e que instâncias de recursos pode ser implementados ou limitar as regiões que podem alojar um tipo de recurso. É possível requerer um valor de etiqueta para os recursos de modo a organizar a faturação por departamentos. Cria políticas para ajudar a reduzir os custos e manter a consistência na sua subscrição. Para obter mais informações, consulte [Utilizar a Política para gerir recursos e controlar o acesso](resource-manager-policy.md).

## Camada de gestão consistente

O Resource Manager proporciona operações compatíveis através do Azure PowerShell, da CLI do Azure para Mac, Linux e Windows, do Portal do Azure ou da API REST. Pode utilizar a interface mais adequada para si e alternar rapidamente entre as interfaces sem confusões.

Para obter informações sobre o PowerShell, consulte [Utilizar o Azure PowerShell com o Resource Manager](powershell-azure-resource-manager.md) e [Cmdlets do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Para obter informações sobre a CLI do Azure, consulte [Utilizar a CLI do Azure para Mac, Linux e Windows com a Gestão de Recursos do Azure](xplat-cli-azure-resource-manager.md).

Para obter informações sobre a API REST, consulte [Referência de API REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx). Para ver as operações REST para os recursos implementados, consulte [Utilizar o Explorador de Recursos do Azure para ver e modificar recursos](resource-manager-resource-explorer.md).

Para obter mais informações sobre como utilizar o portal, veja o artigo [Implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

O Azure Resource Manager suporta a Partilha de Recursos de Várias Origens (CORS). Com a CORS, pode chamar a API REST do Resource Manager ou a API REST de um serviço do Azure a partir de uma aplicação Web que reside num domínio diferente. Sem o suporte para CORS, o browser impediria que uma aplicação num domínio acedesse a recursos noutro domínio. O Resource Manager ativa a CORS para todos os pedidos com credenciais de autenticação válidas.

## SDKs

Os SDKs do Azure estão disponíveis para vários idiomas e plataformas.
Cada uma destas implementações de idiomas está disponível através do respetivo gestor de pacotes ecossistema de pacotes e do GitHub.

O código em cada um destes SDKs é gerado a partir de especificações de API RESTful do Azure.
Estes especificações são de fonte aberta e baseadas na especificação do Swagger 2.0.
O código SDK é gerado através de um projeto de fonte aberta denominado AutoRest.
O AutoRest transforma estas especificações de API RESTful em bibliotecas de cliente em vários idiomas.
Se pretender melhorar os aspetos do código gerado nos SDKs, o conjunto completo de ferramentas para criar os SDKs são abertos, livremente disponíveis e com base num formato de especificação de API amplamente adotado.

Aqui estão os nossos repositórios de SDKs Open Source. Agradecemos o envio de comentários, problemas e pedidos pull.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [AZURE.NOTE] Se o SDK não fornecer a funcionalidade necessária, também pode ligar para a [API de REST do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) diretamente.

## Amostras

### .NET

- [Gerir os recursos do Azure e os grupos de recursos](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Implementar uma VM com SSH através de um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java

- [Gerir os recursos do Azure](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
- [Gerir os grupos de recursos do Azure](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Implementar uma VM com SSH através de um modelo](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js

- [Gerir os recursos do Azure e os grupos de recursos](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Implementar uma VM com SSH através de um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python

- [Gerir os recursos do Azure e os grupos de recursos](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Implementar uma VM com SSH através de um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby

- [Gerir os recursos do Azure e os grupos de recursos](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
- [Implementar uma VM com SSH através de um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


Para além estes exemplos, pode procurar nos exemplos da galeria.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## Passos seguintes

- Para obter uma introdução simples sobre como trabalhar com modelos, consulte [Exportar um modelo do Azure Resource Manager a partir de recursos existentes](resource-manager-export-template.md).
- Para obter instruções mais detalhadas sobre como criar um modelo, consulte [Instruções do Modelo do Resource Manager](resource-manager-template-walkthrough.md).
- Para compreender as funções que pode utilizar num modelo, consulte [Funções de modelo](resource-group-template-functions.md)
- Para obter informações sobre como utilizar o Visual Studio com o Resource Manager, consulte [Criar e implementar grupos de recursos do Azure através do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
- Para obter informações sobre como utilizar o VS Code com o Resource Manager, veja o artigo [Trabalhar com modelos do Azure Resource Manager no Visual Studio Code](resource-manager-vs-code.md).

Eis uma demonstração em vídeo desta descrição geral:

[AZURE.VIDEO azure-resource-manager-overview]



<!--HONumber=Sep16_HO3-->


