


## Vantagens de integrar a Computação, a Rede e o Armazenamento no modelo de implementação Azure Resource Manager

O modelo de implementação Azure Resource Manager oferece a capacidade de tirar partido de modelos de aplicação previamente concebidos ou construir um modelo de aplicação para implementar e gerir recursos de computação, de rede e de armazenamento no Azure. Nesta secção, vamos explicar as vantagens de implementar recursos através do modelo de implementação Azure Resource Manager.

-   Complexidade tornada simples – criar, integrar e colaborar em aplicações complicadas que podem incluir toda a gama de recursos do Azure (por exemplo, Web Sites, SQL Database, Virtual Machines ou Virtual Networks) a partir de um ficheiro de modelo partilhável
-   Flexibilidade para ter implementações repetíveis para desenvolvimento, devOps e administradores de sistema quando utilizar o mesmo ficheiro de modelo
-   A integração profunda das extensões das VMs (Scripts Personalizados, DSC, Chef, Puppet, etc.) com o Azure Resource Manager num ficheiro de modelo permite uma orquestração fácil da configuração da instalação na VM
-   Definição de etiquetas e a propagação da faturação dessas etiquetas para os recursos de Computação, de Redes e de Armazenamento
-   Gestão de acesso simples e exata a recursos organizacionais com o Controlo de Acesso Baseado em Funções do Azure (RBAC)
-   Bloco de Atualização simplificado modificando o modelo original e implementando-o de novo


## Avanços nas APIs de Computação, de Rede e de Armazenamento no Azure Resource Manager

Para além das vantagens mencionadas acima, há alguns avanços significativos de desempenho nas APIs editadas:

-   Ativação da implementação em massa ou paralela de Virtual Machines
-   Suporte para três Domínios de Falhas em Conjuntos de Disponibilidade
-   Extensão do Script Personalizado melhorada para permitir a especificação de scripts a partir de qualquer URL personalizado acessível publicamente
- Integração de Virtual Machines com o Cofre de Chaves do Azure para armazenamento altamente seguro e implementação privada de segredos de [FIPS-validated](http://wikipedia.org/wiki/FIPS_140-2) [Hardware Security Modules](http://wikipedia.org/wiki/Hardware_security_module) (Módulos de Segurança de Hardware com Validação FIPS)
-   Fornece os blocos modulares básicos do funcionamento em rede através de APIs, para permitir que os clientes construam aplicações complicadas que incluem Interfaces de Rede, Balanceadores de Carga e Redes Virtuais
-   As Interfaces de Rede como um novo objeto permitem que a configuração de rede mais complicada possa ser suportada e reutilizada por Máquinas Virtuais
-   Os Balanceadores de Carga como um recurso de primeira classe permitem as atribuições dos Endereços IP
-   As APIs da Rede Virtual Granular permitem-lhe simplificar a gestão das Redes Virtuais individuais

## Diferenças conceptuais com a introdução de novas APIs

Nesta secção, vamos descrever algumas das mais importantes diferenças conceptuais entre as APIs baseadas em XML disponíveis atualmente e as APIs baseadas em JSON disponíveis através do Azure Resource Manager para Computação, Redes e Armazenamento.

 Item | Gestão do Serviço do Azure (baseada em XML)    | Fornecedores de Computação, de Redes e de Armazenamento (baseados em JSON)
 ---|---|---
| Serviço em Nuvem para Máquinas Virtuais |  O Serviço em Nuvem era um contentor para manter máquinas virtuais que exigiam Disponibilidade a partir de plataforma e o Balanceamento de Carga. | O Serviço em Nuvem já não é um objeto necessário para criar uma Máquina Virtual com o novo modelo. |
| Conjuntos de Disponibilidade | A Disponibilidade para a plataforma era indicada configurando o mesmo “AvailabilitySetName” nas Máquinas Virtuais. A contagem máxima de domínios de falhas era 2. | O Conjunto de Disponibilidade é um recurso exposto pelo Fornecedor Microsoft.Compute. As Máquinas Virtuais que requerem elevada disponibilidade têm de ser incluídas no Conjunto de Disponibilidade. A contagem máxima de domínios de falhas é agora 3. |
| Grupos de Afinidade | Os Grupos de Afinidade eram necessários para criar Redes Virtuais. No entanto, com a introdução das Redes Virtuais Regionais, deixaram de ser necessários. |Para simplificar, o conceito de Grupos de Afinidade não existe nas APIs expostas através do Azure Resource Manager. |
| Balanceamento de Carga    | A criação de um Serviço em Nuvem fornece um balanceador de carga implícito para as Máquinas Virtuais implementadas. | O Balanceador de Carga é um recurso exposto pelo fornecedor Microsoft.Network. A interface de rede primária das Máquinas Virtuais que precisam de balanceamento de carga deve mencionar o balanceador de carga. Os Balanceadores de Carga podem ser internos ou externos. [Saiba mais.](../articles/resource-groups-networking.md) |
|Endereço IP Virtual | Os Serviços em Nuvem obtêm um VIP (Endereço de IP Virtual) predefinido quando é adicionada uma VM a um serviço em nuvem. O Endereço IP Virtual é o endereço associado ao balanceador de carga implícito.   | O endereço IP público é um recurso exposto pelo fornecedor Microsoft.Network. O Endereço IP Público pode ser Estático (Reservado) ou Dinâmico. Os IPs Públicos Dinâmicos podem ser atribuídos a um Balanceador de Carga. Os IPs Públicos podem ser protegidos utilizando Grupos de Segurança. |
|Endereço IP Reservado|   Pode reservar um Endereço IP no Azure e associá-lo a um Serviço em Nuvem para garantir que o Endereço IP é temporário.   | Um Endereço IP Público pode ser criado no modo “Estático” e oferecer a mesma capacidade de um “Endereço IP Reservado”. De momento, os IPs Públicos Estáticos só podem ser atribuídos a um Balanceador de carga. |
|Endereço IP Público (PIP) por VM | Os Endereços IP Públicos também podem ser associados diretamente a uma VM. | O endereço IP público é um recurso exposto pelo fornecedor Microsoft.Network. O Endereço IP Público pode ser Estático (Reservado) ou Dinâmico. No entanto, atualmente só os IPs Públicos Dinâmicos podem ser atribuídos a uma Interface de Rede para obter um IP Público por VM. |
|Pontos Finais| Os Pontos Finais de Entrada tinham de ser configurados numa Máquina Virtual para estarem abertos à conectividade para determinadas portas. Um dos modos comuns de ligar a máquinas virtuais era configurando pontos finais de entrada. | As Regras NAT de Entrada podem ser configuradas em Balanceadores de Carga para obter a mesma capacidade de ativação de pontos finais em portas específicas para estabelecer ligação às VMs. |
|Nome DNS| Um serviço em nuvem teria de obter um nome DNS implícito globalmente exclusivo. Por exemplo: `mycoffeeshop.cloudapp.net`. | Os Nomes DNS são parâmetros opcionais que podem ser especificados num recurso de Endereço IP Público. O FQDN estará no formato seguinte – `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces de Rede | As Interfaces de Rede Primária e Secundária e as respetivas propriedades eram definidas como configuração de rede de uma Máquina Virtual. | A Interface de Rede é um recurso exposto pelo Fornecedor Microsoft.Network. O ciclo de vida da Interface de Rede não está associado a uma Máquina Virtual. |

## Introdução aos Modelos do Azure para Máquinas Virtuais

Pode começar com os Modelos do Azure tirando partido das várias ferramentas que temos para desenvolver e implementar na plataforma.

### Portal do Azure

O Portal do Azure vai continuar a ter a opção de implementação de Máquinas Virtuais com o modelo de implementação clássica e de Máquinas Virtuais com o modelo de implementação Resource Manager em simultâneo. O Portal do Azure vai também permitir implementações de modelos personalizados.

### Azure PowerShell

O Azure PowerShell vai ter dois modos de implementação – o modo **AzureServiceManagement** e o modo **AzureResourceManager**.  O modo AzureResourceManager passará também a conter os cmdlets para gerir Máquinas Virtuais, Redes Virtuais e Contas de Armazenamento. Pode ler mais sobre o assunto [aqui](../articles/powershell-azure-resource-manager.md).

### CLI do Azure

A Interface de Linha de Comandos do Azure (Azure CLI) vai ter dois modos de implementação – modo **AzureServiceManagement** e modo **AzureResourceManager**. O modo AzureResourceManager vai passar a conter também comandos para gerir Máquinas Virtuais, Redes Virtuais e Contas de Armazenamento. Pode ler mais sobre o assunto [aqui](../articles/xplat-cli-azure-resource-manager.md).

### Visual Studio

Com a versão mais recente do Azure SDK para Visual Studio, pode criar e implementar Máquinas Virtuais e aplicações complexas diretamente a partir do Visual Studio. O Visual Studio oferece a capacidade de implementar a partir de uma lista de modelos previamente concebidos ou a partir de um modelo em branco.

### APIs REST

Pode encontrar a documentação detalhada da API REST para Computação, Redes e Fornecedores de Recursos de Armazenamento [aqui](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Perguntas Mais Frequentes

**Posso criar uma Máquina Virtual com o novo Azure Resource Manager para implementar numa Rede Virtual ou numa Conta de Armazenamento criada com as APIs de Gestão de Serviço do Azure?**

No momento, essa ação não é suportada. Não pode utilizar as novas APIs do Azure Resource Manager para implementar uma Máquina Virtual numa Rede Virtual que foi criada com as APIs de Gestão de Serviço?

**Posso criar uma Máquina Virtual com as novas APIs do Azure Resource Manager a partir de uma imagem de utilizador que foi criada com as APIs de Gestão de Serviço do Azure?**

No momento, essa ação não é suportada. Pode, no entanto, copiar os ficheiros VHD de uma Conta de Armazenamento que foi criada com as APIs de Gestão de Serviço e copiá-los para uma nova conta criada com as novas APIs do Azure Resource Manager.

**Qual é o impacto na quota da minha subscrição?**

As quotas das Máquinas Virtuais, das Redes Virtuais e das Contas de Armazenamento criadas através das novas APIs do Azure Resource Manager são separadas das quotas que possui atualmente. Cada subscrição recebe novas quotas para criar os recursos com as novas APIs. Pode ler mais sobre as quotas adicionais [aqui](../articles/azure-subscription-service-limits.md).

**Posso continuar a utilizar os meus scripts automatizados para o aprovisionamento de Máquinas Virtuais, Redes Virtuais, Contas de Armazenamento, etc. através das novas APIs do Azure Resource Manager?**

Toda a automatização e os scripts que criou irão continuar a funcionar para as Máquinas Virtuais e Redes Virtuais existentes criadas no modo Gestão de Serviço do Azure. No entanto, os scripts têm de ser atualizados para utilizar o novo esquema para criarem os mesmos recursos através do novo modo Azure Resource Manager.

**Onde posso encontrar exemplos de modelos do Azure Resource Manager?**

Pode encontrar um conjunto abrangente de modelos iniciais em [Modelos de Início Rápido do Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).


<!--HONumber=Sep16_HO3-->


