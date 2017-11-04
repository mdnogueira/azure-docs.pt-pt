---
title: "Criar e instalar ficheiros de configuração de cliente VPN para ligações P2S RADIUS: PowerShell: Azure | Microsoft Docs"
description: "Este artigo ajuda-o a criar o ficheiro de configuração de cliente VPN para ligações ponto a Site que utilizem a autenticação RADIUS."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: cherylmc
ms.openlocfilehash: d7d2dbff4bcd0d76b56c6f142afae4ce8359bb37
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication-preview"></a>Criar e instalar ficheiros de configuração de cliente VPN para autenticação P2S RADIUS (pré-visualização)

Ficheiros de configuração de cliente VPN estão contidos num ficheiro zip. Ficheiros de configuração fornecem as definições necessárias para um cliente Windows ou Mac IKEv2 VPN nativo ligar a uma VNet através de ponto a Site. O servidor RADIUS fornece várias opções de autenticação e como tal, a configuração do cliente VPN varia de acordo com cada opção.

### <a name="workflow"></a>Fluxo de trabalho

  1. Configure o gateway de VPN do Azure para a conetividade P2S.
  2. Configure o servidor RADIUS para autenticação. 
  3. Obter a configuração de cliente VPN para a opção de autenticação à sua escolha e utilizá-la para configurar o cliente VPN no seu dispositivo Windows. Isto permite-lhe ligar-se para as VNets do Azure através de uma ligação de P2S.

>[!IMPORTANT]
>Se existirem quaisquer alterações à configuração de VPN de ponto a Site depois de gerar o perfil de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, tem de gerar e instalar uma nova configuração de cliente VPN nos seus dispositivos de utilizador.
>
>

## <a name="adeap"></a>Autenticação de nome de utilizador/palavra-passe

* **Autenticação do AD:** um cenário popular é autenticação de domínio do AD. Neste cenário, os utilizadores utilizam as respetivas credenciais de domínio para ligar a VNets do Azure. Pode criar ficheiros de configuração de cliente VPN para autenticação RADIUS AD.

* **Autenticação sem AD:** também pode configurar o cenário de autenticação RADIUS de nome de utilizador/palavra-passe sem AD.

Certifique-se de que todos os utilizadores ligados têm as credenciais de nome de utilizador/palavra-passe que podem ser autenticadas através de RADIUS. Só pode criar uma configuração para o protocolo de autenticação de nome de utilizador/palavra-passe do EAP-MSCHAPv2. '-AuthenticationMethod' está especificado como 'EapMSChapv2'.

### <a name="usernamefiles"></a>Gerar ficheiros de configuração de cliente VPN

Crie a configuração do cliente VPN utilizando o seguinte comando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Depois de executar o comando, devolverá uma ligação. Copie e cole a hiperligação para um web browser para transferir um ficheiro zipped, chamado 'VpnClientConfiguration.zip'. Deszipe o ficheiro para ver as seguintes pastas: 
 
* Pastas com o nome 'WindowsAmd64' e 'WindowsX86'. Estas pastas contêm os pacotes de instalador do Windows de 64 bits e de 32 bits, respetivamente. 
* Uma pasta denominada 'GenericDevice'. Esta pasta contém informações gerais, utilizadas para criar a sua própria configuração de cliente VPN. Ignore esta pasta.
* Se tiver sido configurada IKEv2 quando criou o gateway de rede virtual, verá uma pasta denominada 'Mac' que contém um ficheiro chamado 'mobileconfig'. Este ficheiro é utilizado para configurar os clientes Mac.

Pode obter ficheiros de configuração de cliente que já tenha criado. O cmdlet 'Get-AzureRmVpnClientConfiguration' devolve um URL (ligação) a partir de onde pode transferir o ficheiro VpnClientConfiguration.zip. Se efetuar alterações à sua configuração de P2S VPN, tal como o tipo de protocolo de VPN ou o tipo de autenticação, a configuração não atualiza automaticamente. Tem de executar o cmdlet 'New-AzureRmVpnClientConfiguration'-o para recriar a configuração.

Para obter os ficheiros de configuração de cliente gerado anteriormente, utilize o seguinte comando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="adwincli"></a>Configurar um cliente de VPN do Windows

Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador cliente Windows, desde que a versão corresponde à arquitetura do cliente. Para obter a lista de sistemas operativos cliente que são suportados, consulte a secção ponto a Site a [FAQ](vpn-gateway-vpn-faq.md#P2S).

Utilize os seguintes passos para configurar o cliente de VPN do Windows nativo para autenticação de certificados:

1. Selecione os ficheiros de configuração de cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, selecione o pacote de instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, selecione o pacote de instalador 'VpnClientSetupX86'. 
2. Faça duplo clique o pacote para o instalar. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 

### <a name="admaccli"></a>Configurar um cliente VPN de Mac (OSX)

1. Selecione o **VpnClientSetup mobileconfig** de ficheiros e enviá-lo a cada um dos utilizadores. Pode utilizar o e-mail ou outro método para efetuar este procedimento.

2. Localize o **mobileconfig** ficheiro no Mac.

  ![Localize o ficheiro de mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Faça duplo clique no perfil para instalá-lo, clique em **continuar**. O nome do perfil é igual ao nome da VNet.

  ![Instalar](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Clique em **continuar** para o remetente do perfil de confiança e prosseguir com a instalação.

  ![Continuar](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Durante a instalação do perfil, é-lhe dada a opção de especificar o nome de utilizador e palavra-passe utilizada para autenticação de VPN. Não é obrigatório para introduzir estas informações. Se for especificado, as informações são guardadas e utilizadas automaticamente quando iniciar uma ligação. Clique em **instalar** para continuar.

  ![settings](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Introduza um nome de utilizador e palavra-passe para os privilégios necessários necessários para instalar o perfil no seu computador. Clique em **OK**.

  ![nome de utilizador e palavra-passe](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Depois de instalado, o perfil é visível no **perfis** caixa de diálogo. Esta caixa de diálogo também pode ser aberta mais tarde de **preferências do sistema**.

  ! [Preferências do sistema]] (. / media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Para aceder a ligação VPN, abra o **rede** caixa de diálogo de **preferências do sistema**.

  ![Rede](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. A ligação VPN é apresentado como **IkeV2 VPN**. O nome pode ser alterado por atualizar o **mobileconfig** ficheiro.

  ![ligação](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Clique em **definições de autenticação**. Escolha **Username** na lista pendente e introduza as suas credenciais. Se introduziu as credenciais anteriormente, em seguida, **Username** automaticamente escolhida na lista pendente e o nome de utilizador e palavra-passe são pré-preenchidos. Clique em **OK** para guardar as definições. Isto leva-o novamente para a caixa de diálogo de rede.

  ![autenticar](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Clique em **aplicar** para guardar as alterações. Para iniciar a ligação, clique em **Connect**.

## <a name="certeap"></a>Autenticação de certificados
 
Pode criar ficheiros de configuração para autenticação de certificados RADIUS que utiliza o protocolo EAP-TLS de cliente VPN. Normalmente, um certificado emitido por empresarial é utilizado para autenticar um utilizador para VPN. Certifique-se de que todos os utilizadores de ligação tem um certificado instalado nos dispositivos dos utilizadores e de que o certificado pode ser validado pelo seu servidor RADIUS.
 
* '-AuthenticationMethod' for 'EapTls'.
* Durante a autenticação de certificado, o cliente valida o servidor RADIUS ao validar o respetivo certificado. -RadiusRootCert é o ficheiro. cer que contém o certificado de raiz que é utilizado para validar o servidor RADIUS.  
* Por vezes, um dispositivo Windows tem vários certificados de cliente. Durante a autenticação, isto pode resultar numa caixa de diálogo de pop-up listar todos os certificados. O utilizador, em seguida, tem de escolher o certificado a utilizar. O certificado correto pode ser filtrado, especificando o certificado de raiz ao qual o certificado de cliente deve estar ligados. '-ClientRootCert' é o ficheiro. cer que contém o certificado de raiz. É um parâmetro opcional. Se o dispositivo a partir do qual pretende ligar-se tiver apenas um certificado de cliente, em seguida, este parâmetro não tem de ser especificado.

### <a name="certfiles"></a>Gerar ficheiros de configuração de cliente VPN

Crie a configuração do cliente VPN utilizando o seguinte comando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root>
```

O resultado do cmdlet devolve uma ligação. Copie e cole a hiperligação para um web browser para transferir um ficheiro zipped, chamado 'VpnClientConfiguration.zip'. Deszipe o ficheiro para ver as seguintes pastas:

* Pastas com o nome 'WindowsAmd64' e 'WindowsX86'. Estas pastas contêm os pacotes de instalador do Windows de 64 bits e de 32 bits, respetivamente. 
* Uma pasta denominada 'GenericDevice'. Esta pasta contém informações gerais, utilizadas para criar a sua própria configuração de cliente VPN.

Pode obter ficheiros de configuração de cliente que já tenha criado. O cmdlet 'Get-AzureRmVpnClientConfiguration' devolve um URL (ligação) a partir de onde pode transferir o ficheiro VpnClientConfiguration.zip. Se efetuar alterações à sua configuração de P2S VPN, tal como o tipo de protocolo de VPN ou o tipo de autenticação, a configuração não atualiza automaticamente. Tem de executar o cmdlet 'New-AzureRmVpnClientConfiguration'-o para recriar a configuração.

Para obter os ficheiros de configuração de cliente gerado anteriormente, utilize o seguinte comando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="certwincli"></a>Configurar um cliente de VPN do Windows

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, selecione o pacote de instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, selecione o pacote de instalador 'VpnClientSetupX86'. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.
2. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.

### <a name="certmaccli"></a>Configurar um cliente de VPN de MAC

Tem de criar um perfil separado para cada dispositivo de Mac liga ao Azure VNet. Isto acontece porque estes dispositivos necessitem do certificado de utilizador para autenticação de ser especificado no perfil. O **genérico** pasta tem todas as informações necessárias para criar um perfil.

  * **VpnSettings.xml** contém definições importantes, tais como o tipo de túnel e o endereço do servidor.
  * **VpnServerRoot.cer** contém o certificado de raiz necessário para validar o gateway VPN durante a configuração de ligação de P2S.
  * **RadiusServerRoot.cer** contém o certificado de raiz necessário para validar o servidor RADIUS durante a autenticação.

Utilize os seguintes passos para configurar o cliente VPN nativo no Mac para autenticação de certificados:

1. Importar o **VpnServerRoot** e **RadiusServerRoot** raiz certificados para o Mac. Isto pode ser feito ao copiar o ficheiro de ativação pós-falha para Mac e fazer duplo clique.  
Clique em **adicionar** para importar.

  **Adicionar VpnServerRoot**

  ![Adicionar certificado](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **Adicionar RadiusServerRoot**

  ![Adicionar certificado](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Abra o **rede** diálogo em **preferências de rede** e clique em **'+'** para criar um novo perfil de ligação de cliente VPN para uma ligação de P2S para a VNet do Azure.

  O **Interface** valor é 'VPN' e **tipo de VPN** valor é 'IKEv2'. Especifique um nome para o perfil no **nome do serviço** campo, em seguida, clique em **criar** para criar o perfil de ligação de cliente VPN.

  ![Rede](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. No **genérico** pasta, do **VpnSettings.xml** ficheiro, copie o **VpnServer** valor da etiqueta. Cole este valor no **endereço do servidor** e **ID remoto** campos do perfil. Deixe o **Local ID** campo vazio.

  ![informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. Clique em **definições de autenticação** e selecione **certificado**. 

  ![definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. Clique em **selecionar...** para escolher o certificado que pretende utilizar para autenticação.

  ![certificado](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. **Escolha uma identidade** apresenta uma lista de certificados para escolher. Selecione o certificado adequado, em seguida, clique em **continuar**.

  ![identidade](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. No **Local ID** campo, especifique o nome do certificado (a partir do passo 5). Neste exemplo, é "ikev2Client.com". Em seguida, clique em **aplicar** botão para guardar as alterações.

  ![aplicar](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. No **rede** caixa de diálogo, clique em **aplicar** para guardar todas as alterações. Em seguida, clique em **Connect** para iniciar a ligação P2S a VNet do Azure.

## <a name="otherauth"></a>Outros tipos de autenticação ou protocolos

Para utilizar um tipo de autenticação diferentes (por exemplo, o OTP) e não nome de utilizador/palavra-passe ou certificados ou para utilizar um protocolo de autenticação diferentes (tais como PEAP-MSCHAPv2, em vez de EAP-MSCHAPv2), tem de criar o seu próprio perfil de configuração de cliente VPN. Para criar o perfil, terá de informações como o endereço IP do gateway de rede virtual, tipo de túnel e rotas de divisão do túnel. Pode obter estas informações através dos seguintes passos:

1. Utilize o cmdlet 'Get-AzureRmVpnClientConfiguration' para gerar a configuração de cliente VPN para EapMSChapv2. Para obter instruções, consulte [nesta secção](#ccradius) do artigo.

2. Deszipe o ficheiro VpnClientConfiguration.zip e procure a pasta de GenenericDevice. Ignore as pastas que contêm os programas de instalação do Windows para arquiteturas de 64 bits e de 32 bits.
 
3. A pasta de GenenericDevice contém um ficheiro XML denominado VpnSettings. Este ficheiro contém todas as informações necessárias.

  * VpnServer - FQDN do Gateway VPN do Azure. Este é o endereço que o cliente liga-se ao.
  * VpnType - o tipo de túnel que utilizar para ligar.
  * As rotas - as rotas que tem de configurar no seu perfil para que apenas a VNet do Azure vinculada tráfego é enviada através do túnel P2S.
  * A pasta de GenenericDevice também contém um ficheiro. cer chamado 'VpnServerRoot'. Este ficheiro contém o certificado de raiz necessário para validar o Gateway de VPN do Azure durante a configuração de ligação de P2S. Instale o certificado em todos os dispositivos que irão ligar à VNet do Azure. 
 
## <a name="next-steps"></a>Passos seguintes

Devolver o artigo para [concluir a configuração de P2S](point-to-site-how-to-radius-ps.md).
