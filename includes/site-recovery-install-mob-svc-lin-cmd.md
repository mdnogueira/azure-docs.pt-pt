1. Copie o instalador para uma pasta local (por exemplo, /tmp dos) no servidor que pretende proteger. Num terminal, execute os seguintes comandos:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Para instalar o serviço de mobilidade, execute o seguinte comando:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Após a conclusão da instalação, o serviço de mobilidade tem de obter registados no servidor de configuração. Execute o seguinte comando para registar o serviço de mobilidade no servidor de configuração.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Instalador do serviço de mobilidade da linha de comandos

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|-r |Obrigatório|Especifica se deve ser instalado o serviço de mobilidade (MS) ou MasterTarget(MT) deve ser instalado|MS </br> MT|
|-d |Opcional|Localização onde será instalado o serviço de mobilidade|/usr/local/ASR|
|-v|Obrigatório|Especifica a plataforma em que o serviço de mobilidade está a obter instalado </br> </br>- **VMware** : Utilize este valor se estiver a instalar o serviço de mobilidade numa VM em execução no *VMware vSphere anfitriões ESXi*, *anfitriões Hyper-V* e *Phsyical servidores* </br> - **Azure** : Utilize este valor se estiver a instalar o agente de uma VM do IaaS do Azure| VMware </br> Azure|
|-q|Opcional|Especifica a executar o programa de instalação no modo silencioso| N/D|


#### <a name="mobility-service-configuration-command-line"></a>Configuração do serviço de mobilidade da linha de comandos

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|-i |Obrigatório|IP do servidor de configuração|Qualquer endereço IP válido|
|-P |Obrigatório|Caminho completo do ficheiro do ficheiro onde está guardado o frase de acesso de ligação|Qualquer pasta válido|
