<properties
   pageTitle="Configurar uma ligação VPN Ponto a Site para uma Virtual Network do Azure através do portal clássico | Microsoft Azure"
   description="Ligue-se de forma segura à Virtual Network do Azure criando uma ligação VPN Ponto a Site. Instruções para VNets que foram criadas com o modelo de implementação (clássica) da Gestão de Serviço."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2016"
   ms.author="cherylmc"/>

# Configurar uma ligação VPN Ponto a Site para uma VNet através do portal clássico

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal – Clássico](vpn-gateway-point-to-site-create.md)

Uma configuração Ponto a Site permite-lhe criar uma ligação segura para a sua rede virtual a partir de um computador cliente, individualmente. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. O Ponto a Site é uma excelente solução quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. Para obter mais informações sobre ligações Ponto a Site, veja [FAQs do VPN Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Acerca das ligações em vários locais](vpn-gateway-cross-premises-options.md).

Este artigo aplica-se às ligações do VPN Gateway Ponto a Site para uma rede virtual criadas com o **modelo de implementação clássica** (Gestão de Serviço) e o portal clássico. Quando criarmos os passos para o Portal do Azure, criaremos ligações para esse artigo nesta página.

**Modelos de implementação e ferramentas para ligações Ponto a Site**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


**Acerca dos modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

![Diagrama Ponto a Site](./media/vpn-gateway-point-to-site-create/point2site.png "point-to-site")



## Acerca da criação de uma ligação Ponto a Site
 
Os seguintes passos explicam como criar uma ligação Ponto a Site segura para uma rede virtual. Embora a configuração de uma ligação Ponto a Site precise de vários passos, é uma excelente forma de ter uma ligação segura do computador para a rede virtual sem adquirir nem configurar um dispositivo VPN. 

A configuração de uma ligação Ponto a Site é apresentada em 4 secções. A ordem pela qual configura cada uma é importante, como tal, não ignore nem salte passos.


- A **Secção 1** explica como criar uma rede virtual e um gateway de VPN.
- A **Secção 2** ajuda a criar os certificados utilizados para a autenticação e a carregá-los.
- A **Secção 3** explica os passos para exportar e instalar os certificados de cliente.
- A **Secção 4** explica os passos para configurar o cliente VPN.

## Secção 1 – Criar uma rede virtual e um gateway de VPN


### Parte 1: criar uma rede virtual

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/). Tenha em atenção que estes passos utilizam o portal clássico e não o portal do Azure.

2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Na página **Detalhes da Rede Virtual**, introduza as seguintes informações e clique na seta seguinte na parte inferior direita.
    - **Nome**: atribua um nome à rede virtual. Por exemplo, “VNetEast”. Este será o nome que deverá mencionar quando implementar instâncias de PaaS e VMs nesta VNet.
    - **Localização**: a localização está diretamente relacionada com a localização física (região) onde pretende que os recursos (VMs) residam. Por exemplo, se pretender que as VMs que implementa nesta rede virtual estejam fisicamente localizadas nos EUA Leste, selecione essa localização. Não é possível alterar a região associada à rede virtual depois de a criar.

4. Na página **Servidores DNS e Conetividade VPN**, introduza as seguintes informações e clique na seta seguinte na parte inferior direita.
    - **Servidores DNS**: introduza o endereço IP e o nome do servidor DNS ou selecione um servidor DNS anteriormente registado no menu de atalho. Esta definição não cria um servidor DNS, permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual. Se pretender utilizar o serviço de resolução de nomes predefinido do Azure, deixe esta secção em branco.
    - **Configurar VPN Ponto a Site**: selecione a caixa de verificação.

5. Na página **Conetividade Ponto a Site**, especifique o intervalo de endereços IP a partir do qual os clientes VPN receberão um endereço IP quando estiverem ligados. Existem algumas regras relativamente aos intervalos de endereços que pode especificar. É muito importante verificar se o intervalo que especificou não se sobrepõe a qualquer um dos intervalos localizados na sua rede no local.

6. Introduza as seguintes informações e clique na seta seguinte.
 - **Espaço de Endereços**: inclua o IP Inicial e o CIDR (Contagem de Endereços).
 - **Adicionar espaço de endereços**: adicione o espaço de endereços apenas se for preciso para o design de redes.

7. Na página **Espaços de Endereços da Rede Virtual**, especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos (DIPS) que serão atribuídos às VMs e a outras instâncias de função que implementar nesta rede virtual. É especialmente importante selecionar um intervalo que não se sobreponha a nenhum intervalo utilizado para a rede no local. Terá de trabalhar em coordenação com o administrador de rede, que poderá ter de extrair um intervalo de endereços IP do seu espaço de endereços da rede no local para a utilização na sua rede virtual.

8. Introduza as seguintes informações e clique na marca de verificação para começar a criar a rede virtual.
 - **Espaço de Endereços**: adicione o intervalo de endereços IP interno que pretende utilizar para esta rede virtual, incluindo o IP Inicial e a Contagem. É importante selecionar um intervalo que não se sobreponha a nenhum intervalo que seja utilizado para a rede no local. Terá de trabalhar em coordenação com o administrador de rede, que poderá ter de extrair um intervalo de endereços IP do seu espaço de endereços da rede no local para a utilização na sua rede virtual.
 - **Adicionar sub-rede**: não são necessárias sub-redes adicionais, mas pode querer criar uma sub-rede separada para as VMs com DIPS estáticos. Em alternativa, pode ter as VMs numa sub-rede separada das outras instâncias de função.
 - **Adicionar sub-rede do gateway**: a sub-rede do gateway é necessária para uma VPN Ponto a Site. Clique para adicionar a sub-rede do gateway. A sub-rede do gateway é utilizada apenas para o gateway de rede virtual.

9. Após criar a rede virtual, verá **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure. Após criar a rede virtual, pode criar o gateway de encaminhamento dinâmico.

### Parte 2: criar um gateway de encaminhamento dinâmico

O tipo de gateway tem de ser configurado como dinâmico. Os gateways de encaminhamento estático não funcionam com esta funcionalidade.

1. No Portal Clássico do Azure, na página **Redes**, clique na rede virtual que acabou de criar e navegue até à página **Dashboard**.

2. Clique em **Criar Gateway**, localizado na parte inferior da página **Dashboard**. Será apresentada uma mensagem a perguntar **Pretende criar um gateway para a rede virtual “suarede”?** Clique em **Sim** para começar a criar o gateway. A criação do gateway pode demorar cerca de 15 minutos.

## Secção 2 – Gerar e carregar certificados

Os certificados são utilizados para autenticar clientes VPN para as VPNs Ponto a Site. Pode utilizar certificados gerados por uma solução de certificados de empresa, bem como certificados autoassinados. Pode carregar até 20 certificados de raiz para o Azure.

Se pretender utilizar um certificado autoassinado, os passos abaixo descrevem o processo. Se estiver a planear utilizar uma solução de certificados de empresa, os passos em cada secção serão diferentes, mas ainda terá de efetuar os seguintes passos:

### Parte 1: identificar ou gerar um certificado de raiz

Se não estiver a utilizar uma solução de certificados de raiz, terá de gerar um certificado de raiz autoassinado. Os passos nesta secção destinam-se ao Windows 8. Para obter os passos para o Windows 10, pode ver [Trabalhar com certificados de raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md).

Pode criar um certificado X.509 com a Ferramenta de Criação de Certificados (makecert.exe). Para utilizar o makecert, transfira e instale gratuitamente o [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).

1. Navegue até à pasta Visual Studio Tools e inicie a linha de comandos como Administrador.

2. O comando no exemplo seguinte cria e instala um certificado de raiz no Arquivo de certificados pessoais do computador e também cria um ficheiro correspondente *.cer* que mais tarde deverá carregar para o Portal Clássico do Azure.

3. Altere o diretório onde pretende que o ficheiro .cer esteja localizado e execute o seguinte comando, em que *RootCertificateName* é o nome que pretende utilizar para o certificado. Se executar o exemplo seguinte sem alterações, o resultado será um certificado de raiz e o ficheiro correspondente *RootCertificateName.cer*.

>[AZURE.NOTE] Uma vez que criou um certificado de raiz a partir do qual serão gerados certificados de cliente, deverá exportar este certificado, juntamente com a respetiva chave privada, e guardá-lo numa localização segura, onde possa ser recuperado.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Parte 2: carregar o ficheiro .cer do certificado de raiz para o Portal Clássico do Azure

Terá de carregar o ficheiro .cer correspondente de cada certificado de raiz para o Azure. Pode carregar até 20 certificados.

1. Quando gerou um certificado de raiz no procedimento anterior, também criou um ficheiro *.cer*. Agora, deverá carregar esse ficheiro para o Portal Clássico do Azure. Tenha em atenção que o ficheiro .cer não contém a chave privada do certificado de raiz. Pode carregar até 20 certificados de raiz.

2. No Portal Clássico do Azure, na página **Certificados** da rede virtual, clique em **Carregar um certificado de raiz**.

3. Na página **Carregar Certificado**, procure o certificado de raiz do ficheiro .cer e clique na marca de verificação.

### Parte 3: gerar um certificado de cliente

Os passos abaixo explicam como gerar um certificado de cliente a partir do certificado de raiz autoassinado. Os passos nesta secção destinam-se ao Windows 8. Para obter os passos para o Windows 10, pode ver [Trabalhar com certificados de raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md). Se estiver a utilizar uma solução de certificado de empresa, siga as diretrizes para a solução que estiver a utilizar. 

1. No mesmo computador que utilizou para criar o certificado de raiz autoassinado, abra uma janela de linha de comandos do Visual Studio como administrador.

2. Altere o diretório para a localização onde pretende guardar o ficheiro do certificado de cliente. *RootCertificateName* refere-se ao certificado de raiz autoassinado que gerou. Se executar o exemplo seguinte (alterando o RootCertificateName para o nome do seu certificado de raiz), o resultado será um certificado de cliente com o nome “ClientCertificateName” no Arquivo de certificados pessoais.

3. Escreva o seguinte comando:

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos os certificados são armazenados no Arquivo de certificados pessoais do computador. Selecione *certmgr* para verificar. Pode gerar os certificados de cliente que forem necessários com base neste procedimento. Recomendamos que crie certificados de cliente exclusivos para cada computador que pretende ligar à rede virtual.

## Secção 3 – Exportar e instalar o certificado de cliente

A instalação de um certificado de cliente em cada computador que pretende ligar à rede virtual é um passo obrigatório. Os passos abaixo descrevem como instalar manualmente o certificado de cliente.

1. É necessário instalar um certificado de cliente em cada computador que pretende ligar à rede virtual. Tal significa que, provavelmente, irá criar vários certificados de cliente e, em seguida, terá de os exportar. Para exportar os certificados de cliente, utilize o *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o *certificado de cliente* com a chave privada. Este será um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.
3. Copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Introduza a palavra-passe, quando solicitado. Não modifique a localização da instalação.

## Secção 4 – Configurar o cliente VPN

Para ligar à rede virtual, também terá de configurar o cliente VPN. O cliente exige um certificado de cliente e a configuração adequada do cliente VPN para se ligar. Para configurar o cliente VPN, execute os passos seguintes, por ordem.

### Parte 1: criar o pacote de configuração do cliente VPN

1. No Portal Clássico do Azure, na página **Dashboard** da rede virtual, navegue até ao menu de leitura rápida no canto direito e clique no pacote de VPN que diz respeito ao cliente que pretende ligar à rede virtual.

2. 
São suportados os seguintes sistemas operativos cliente:
 - Windows 7 (32 e 64 bits)
 - Windows Server 2008 R2 (apenas 64 bits)
 - Windows 8 (32 e 64 bits)
 - Windows 8.1 (32 e 64 bits)
 - Windows Server 2012 (apenas 64 bits)
 - Windows Server 2012 R2 (apenas 64 bits)
 - Windows 10

3. Selecione o pacote de transferência que corresponde ao sistema operativo cliente no qual será instalado:
 - Para clientes de 32 bits, selecione **Transferir o Pacote de VPN Cliente de 32 bits**.
 - Para clientes de 64 bits, selecione **Transferir o Pacote de VPN Cliente de 64 bits**.

4. A criação do pacote de clientes poderá demorar alguns minutos. Quando o pacote estiver concluído, poderá transferir o ficheiro. O ficheiro *.exe* que transferir pode ser armazenado com segurança no computador local.

5. Depois de gerar e transferir o pacote de clientes VPN do Portal Clássico do Azure, pode instalá-lo no computador cliente a partir do qual pretende ligar-se à rede virtual. Se planear instalar o pacote de clientes VPN em vários computadores cliente, verifique se cada um também tem um certificado de cliente instalado. O pacote de clientes VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instala software adicional.

### Parte 2: instalar o pacote de configuração de VPN no cliente e iniciar a ligação

1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual e faça duplo clique no ficheiro .exe. Depois de instalar o pacote, pode iniciar a ligação VPN.
Tenha em atenção que o pacote de configuração não está assinado pela Microsoft. Poderá pretender assinar o pacote utilizando o serviço de assinatura da sua organização ou o pacote pode ser assinado por si através da [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Pode utilizar o pacote sem assinar. No entanto, se o pacote não for assinado, será apresentado um aviso quando instalar o pacote.
2. No computador cliente, navegue até às ligações VPN e localize a ligação VPN que acabou de criar. Terá o mesmo nome que a rede virtual. Clique em **Ligar**.
3. Será apresentada uma mensagem de pop-up utilizada para criar um certificado autoassinado para o ponto final do Gateway. Clique em **Continuar** para utilizar privilégios elevados.
4. Na página de estado **Ligação**, clique em **Ligar** para iniciar a ligação.
5. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
6. Agora, está ligado à rede virtual e tem acesso total a qualquer serviço e máquina virtual alojada na rede virtual.

### Parte 3: verificar a ligação VPN

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereços de conetividade Ponto a Site que especificou quando criou a VNet. Os resultados deverão ser algo semelhante a isto:

Exemplo:



    PPP adapter VNetEast:
        Connection-specific DNS Suffix .:
        Description.....................: VNetEast
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## Passos seguintes

Pode adicionar máquinas virtuais à rede virtual. Veja [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se pretender obter mais informações sobre Redes Virtuais, veja a página [Documentação das Redes Virtuais](https://azure.microsoft.com/documentation/services/virtual-network/).



<!--HONumber=Jun16_HO2-->


