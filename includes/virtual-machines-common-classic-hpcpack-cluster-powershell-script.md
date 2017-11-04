



Dependendo do seu ambiente e opções, o script pode criar toda a infraestrutura de cluster, incluindo a rede virtual do Azure, as contas de armazenamento, cloud services, controlador de domínio, locais ou remotos bases de dados SQL, nó principal e nós de cluster adicionais. Em alternativa, o script pode utilizar a infraestrutura do Azure já existente e criar apenas os nós de cluster HPC.

Para obter informações gerais sobre o planeamento de um cluster HPC Pack, consulte o [avaliação do produto e planeamento](https://technet.microsoft.com/library/jj899596.aspx) e [introdução](https://technet.microsoft.com/library/jj899590.aspx) conteúdo na biblioteca do TechNet HPC Pack 2012 R2.

## <a name="prerequisites"></a>Pré-requisitos
* **Subscrição do Azure**: pode utilizar uma subscrição no serviço Global do Azure ou do Azure China. Os limites de subscrição afetam o número e tipo de nós de cluster, que pode implementar. Para informações, consulte [subscrição do Azure e limites de serviço, quotas e restrições](../articles/azure-subscription-service-limits.md).
* **Computador de cliente do Windows com o Azure PowerShell 0.8.10 ou posterior instalado e configurado**: consulte [começar com o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter instruções de instalação e os passos ligar à sua subscrição do Azure.
* **Script de implementação de HPC Pack IaaS**: transferir e Descompacte a versão mais recente do script do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo baseia-se numa versão 4.5.2 do script.
* **Ficheiro de script de configuração**: criar um ficheiro XML que utiliza o script para configurar o cluster HPC. Para obter informações e exemplos, consulte as secções mais adiante neste artigo e o ficheiro Manual.rtf que acompanha o script de implementação.

## <a name="syntax"></a>Sintaxe
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Execute o script como um administrador.
> 
> 

### <a name="parameters"></a>Parâmetros
* **ConfigFile**: Especifica o caminho do ficheiro do ficheiro de configuração para descrever o cluster HPC. Ver mais informações sobre o ficheiro de configuração neste tópico, ou no ficheiro Manual.rtf na pasta que contém o script.
* **AdminUserName**: Especifica o nome de utilizador. Se a floresta de domínio é criada pelo script, isto torna-se o nome de utilizador de administrador local para todas as VMs e o nome de administrador de domínio. Se a floresta de domínio já existir, esta ação Especifica o utilizador de domínio como o nome de utilizador de administrador local para instalar o pacote HPC.
* **AdminPassword**: Especifica a palavra-passe do administrador. Se não for especificado na linha de comandos, o script pede-lhe para introduzir a palavra-passe.
* **HPCImageName** (opcional): Especifica o nome de imagem de VM de pacote HPC utilizado para implementar o cluster HPC. Tem de ser uma imagem de Microsoft fornecidos pelo pacote HPC no Azure Marketplace. Se não for especificado (recomendado normalmente), o script escolhe o mais recente publicada [HPC Pack 2012 R2 imagem](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). Se baseia a imagem mais recente no Windows Server 2012 R2 Datacenter com o HPC Pack 2012 R2 Update 3 instalado.
  
  > [!NOTE]
  > Falha na implementação se não especificar uma imagem de HPC Pack válida.
  > 
  > 
* **LogFile** (opcional): Especifica o caminho de ficheiro de registo de implementação. Se não for especificado, o script cria um ficheiro de registo no diretório temp do computador a executar o script.
* **Force** (opcional): Suprime todos os pedidos de confirmação.
* **NoCleanOnFailure** (opcional): Especifica que não são removidas as VMs do Azure que não são implementadas com êxito. Remova manualmente estas VMs antes de executar novamente o script para continuar que a implementação, ou a implementação poderá falhar.
* **PSSessionSkipCACheck** (opcional): para cada serviço em nuvem com as VMs implementadas por este script, é automaticamente gerado um certificado autoassinado pelo Azure e todas as VMs no serviço em nuvem utilizam este certificado como predefinição remota do Windows Certificado de gestão (WinRM). Para implementar funcionalidades HPC nestas VMs do Azure, o script por predefinição instala temporariamente estes certificados no computador Local\\arquivo de autoridades de certificação de raiz fidedigna do computador cliente para suprimir o erro de segurança "AC não fidedigna" durante a execução do script. Os certificados são removidos quando o script é concluída. Se este parâmetro for especificado, os certificados não estão instalados no computador cliente e o aviso de segurança é suprimido.
  
  > [!IMPORTANT]
  > Este parâmetro não é recomendado para implementações de produção.
  > 
  > 

### <a name="example"></a>Exemplo
O exemplo seguinte cria um cluster HPC Pack utilizando o ficheiro de configuração *MyConfigFile.xml*e especifica as credenciais de administrador para instalar o cluster.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Considerações adicionais
* O script, opcionalmente, pode ativar a submissão da tarefa através do portal web HPC Pack ou a API de REST do HPC Pack.
* O script, opcionalmente, pode executar scripts de prévias e pós-configuração personalizados no nó principal se pretender instalar o software adicional ou configurar outras definições.

## <a name="configuration-file"></a>Ficheiro de configuração
O ficheiro de configuração para o script de implementação é um ficheiro XML. O ficheiro de esquema HPCIaaSClusterConfig.xsd é a pasta de script de implementação do HPC Pack IaaS. **IaaSClusterConfig** é o elemento de raiz do ficheiro de configuração, que contém os elementos subordinados descritos em detalhe no ficheiro Manual.rtf na pasta de script de implementação.

