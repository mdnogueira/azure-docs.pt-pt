UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <endereço IP a ser utilizado para transferência de dados] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parâmetros:

* /ServerMode: obrigatório. Especifica se a configuração e os servidores de processos devem ser instalados, ou apenas o servidor de processos. Valores de entrada: CS, PS.
* InstallLocation: obrigatório. A pasta na qual os componentes são instalados.
* /MySQLCredsFilePath. Obrigatório. O caminho do ficheiro no qual as credenciais do servidor MySQL são armazenadas. O ficheiro deve estar no seguinte formato:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. Obrigatório. A localização do ficheiro de credenciais do cofre
* /EnvType. Obrigatório. O tipo de instalação. Valores: VMware, NonVMware
* /PSIP e /CSIP. Obrigatório. O endereço IP do servidor de processos e o servidor de configuração.
* /PassphraseFilePath. Obrigatório. A localização do ficheiro da frase de acesso.
* /BypassProxy. Opcional. Especifica que o servidor de configuração estabelece uma ligação ao Azure sem um proxy.
* /ProxySettingsFilePath. Opcional. Definições de proxy (o proxy predefinido necessita de autenticação ou de um proxy personalizado). O ficheiro deve estar no seguinte formato:
* [ProxySettings]
* ProxyAuthentication = "Sim/não"
* Proxy IP = "Endereço IP>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. Opcional. O número da porta para dados de replicação.
* SkipSpaceCheck. Opcional. Ignorar a verificação de espaços para a cache.
* AcceptThirdpartyEULA. Obrigatório. Aceita o EULA de terceiros.
* ShowThirdpartyEULA. Obrigatório. Apresenta o EULA de terceiros. Se for fornecido como entrada, todos os outros parâmetros são ignorados.
