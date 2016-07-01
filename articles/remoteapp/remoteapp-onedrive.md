<properties
   pageTitle="Como integrar o OneDrive para Empresas e o Azure RemoteApp | Microsoft Azure"
   description="Saiba como utilizar o OneDrive para Empresas com o Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/31/2016"
   ms.author="elizapo"/>

# Como integrar o OneDrive para Empresas e o Azure RemoteApp

Pode utilizar o OneDrive para Empresas como um repositório de ficheiros com o Azure RemoteApp. O OneDrive para Empresas é uma excelente forma de manter os ficheiros sincronizados em todos os dispositivos e áreas de trabalho. O [disco do perfil de utilizador](remoteapp-upd.md) (UPD) para um utilizador consiste num local onde os utilizadores podem armazenar os ficheiros para as aplicações do Azure RemoteApp, contudo, esses ficheiros estão apenas acessíveis através do Azure RemoteApp. Por outro lado, o OneDrive para Empresas permite ao utilizador aceder aos ficheiros em qualquer momento e lugar sem o pré-requisito de utilização do Azure RemoteApp. Este artigo abordará as versões do OneDrive para Empresas suportadas e os diferentes modos de configuração, por parte dos administradores, do OneDrive para Empresas para o Azure RemoteApp.

## São suportadas todas as versões do OneDrive?

Existem duas versões do OneDrive: o OneDrive e o OneDrive para Empresas. O Azure RemoteApp apenas suporta o OneDrive para Empresas. O OneDrive pessoal funciona, porém, não é oficialmente suportado. Além disso, apenas a versão mais recente do OneDrive para Empresas, também denominada Next Gen Sync Client, é suportada no Azure RemoteApp (e nos servidores de Terminais/RDSH/Citrix).

>[AZURE.NOTE]  O OneDrive (para consumidores/edição pessoal) não é suportado pelo Azure RemoteApp. Nem todas as versões do OneDrive para Empresas são igualmente suportadas, uma vez que não foram certificadas para funcionar no Windows Server. Apesar de o novo cliente (Next Gen Sync Client) e das versões mais antigas do Groove funcionarem, aparentemente, no Azure RemoteApp, conforme descrito em [https://support.microsoft.com/en-us/kb/2965687](https://support.microsoft.com/kb/2965687), os motores de sincronização mais antigos não terão a funcionalidade completa nos Servidores de Terminais/Citrix (Windows Server).

## Quais são as diferentes opções de configuração do OneDrive para Empresas?

- **Configuração tradicional do motor de sincronização do OneDrive para Empresas:** esta opção não é suportada, neste momento, nas implementações do Azure RemoteApp, RDSH e Citrix.
- **“Virtualização” do OneDrive para Empresas/redirecionamento a partir do cliente gordo local para a sessão:** se estiver a sincronizar o OneDrive para uma pasta no dispositivo cliente, por baixo de uma unidade, pode escolher [redirecionar](remoteapp-redirection.md) essa unidade para o Azure RemoteApp – a unidade deve ser a mesma em todos os clientes dos utilizadores que deverão ter a sincronização do OneDrive para uma pasta por baixo dessa unidade. Caso acedam ao RemoteApp a partir de outro cliente, esses ficheiros poderão, eventualmente, não estar disponíveis (solução – aqueles podem sempre aceder aos ficheiros através da versão online do OneDrive). 
- **Apresentação do OneDrive para Empresas como uma unidade no ambiente do Azure RemoteApp sem colocar em cache/sincronizar os ficheiros:** (mapear o URL http do OneDrive para Empresas para uma unidade na VM) o mapeamento do OneDrive para Empresas para a unidade de rede no ambiente RDSH é suportado. Cenários em que esta utilização é possível: 
    - Quando os clientes magros (sem armazenamento local) são utilizados para aceder ao Azure RemoteApp – a aplicação precisa de ficheiros armazenados no OneDrive para Empresas, contudo, pretende que estes "pareçam" locais e o administrador não pretende sincronizar os ficheiros para uma VM.
    - Quando existem muitos ficheiros grandes no OneDrive para Empresas selecionados para sincronização. Tendo em conta a carga de trabalho de sincronização, poderão não estar sincronizados todos os ficheiros quando o utilizador pretende utilizá-los. Além disso, caso os tamanhos totais dos ficheiros exceda 50 GB, estes não podem ser armazenados no UPD.

Nos cenários acima, os administradores podem escolher seguir as opções de mapeamento de uma unidade na VM para o URL http do OneDrive para Empresas do utilizador. Algumas opções para tal:

- Ter binários do Office na imagem e não ativar o motor de sincronização do OneDrive para Empresas.
- NÃO ter binários do Office na imagem – este caso inclui um pré-requisito – o Pacote de Experiência de Utilização do Computador. Mais especificamente, o serviço WebClient (AKA WebDAV) tem de ser instalado como parte do Pacote de Experiência de Utilização do Computador. 

### Instalar o Pacote de Experiência de Utilização do Computador no Windows Server 2012 R2
Para instalar o Pacote de Experiência de Utilização do Computador: 

1. No Gestor de Servidores, clique em **Gerir -> Adicionar Funções e Funcionalidades**.
2. Clique em **Funcionalidades** e, de seguida, em **Infraestrutura e Interfaces de Utilizador -> Experiência de Utilização do Computador**.

### Mapear uma unidade para o URL do OneDrive para Empresas

Siga as instruções do artigo de suporte: [https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
Um passo importante da configuração é certificar-se de que seleciona **manter a sessão iniciada**.

Eis as instruções detalhadas:

1.  Localize o URL da unidade. O URL utilizado para o mapeamento da unidade é o mesmo obtido quando navega para o diretório raiz no OneDrive para Empresas online. Por exemplo:
 
    https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.  Abra o URL utilizando um navegador na sessão RemoteApp e selecione **manter a sessão iniciada** antes de iniciar sessão no URL da sua conta.
3.  Abra o Explorador do Windows e efetue o mapeamento de uma unidade para o URL acima. Caso o URL não seja resolvido, pode ser utilizada a forma mais curta:
    
    https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

    Tal cria imediatamente a unidade mapeada – o aspeto é o seguinte:
 
    ![OneDrive para Empresas como uma unidade de rede mapeada](./media/remoteapp-onedrive/ra-mappeddrive.png)


<!--HONumber=Jun16_HO2-->


