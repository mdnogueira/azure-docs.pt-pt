<properties
   pageTitle="Publicar aplicações para utilizadores individuais numa coleção do Azure RemoteApp (Pré-visualização) | Microsoft Azure"
   description="Saiba como pode publicar aplicações para utilizadores individuais, em vez de depender de grupos, no Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="05/31/2016"
   ms.author="piotrci"/>

# Publicar aplicações para utilizadores individuais numa coleção do Azure RemoteApp (Pré-visualização)

Este artigo explica como publicar aplicações para utilizadores individuais numa coleção do Azure RemoteApp. Esta é uma nova funcionalidade do Azure RemoteApp, atualmente em “pré-visualização privada” e disponível apenas para selecionar adotantes iniciais para efeitos de avaliação.

Inicialmente, o Azure RemoteApp permitia apenas uma forma de “publicar” aplicações: o administrador publicava as aplicações a partir da imagem e estas ficavam visíveis para todos os utilizadores na coleção.

Um cenário comum é incluir muitas aplicações numa única imagem e implementar uma coleção para reduzir os custos de gestão. Frequentemente, nem todas as aplicações são relevantes para todos os utilizadores – os administradores prefeririam publicar aplicações para utilizadores individuais, de modo a não serem apresentadas aplicações desnecessárias no feed de aplicações.

Tal é agora possível no Azure RemoteApp – atualmente como uma funcionalidade de pré-visualização limitada. Eis um breve resumo da nova funcionalidade:

1. Uma coleção pode ser definida num de dois modos:
 
  - o “modo de coleção” original, em que todos os utilizadores numa coleção podem ver todas as aplicações publicadas. Este é o modo predefinido.
  - o novo “modo de aplicação”, em que os utilizadores veem apenas as aplicações que lhes foram explicitamente atribuídas.

2. Neste momento, o modo de aplicação apenas pode ser ativado através de cmdlets do Azure RemoteApp PowerShell.

  - Quando definida no modo de aplicação, a atribuição de utilizadores na coleção não pode ser gerida através do Portal do Azure. A atribuição de utilizadores tem de ser gerida através de cmdlets do PowerShell.

3. Os utilizadores apenas veem as aplicações publicadas diretamente para eles. No entanto, um utilizador poderá continuar a iniciar as outras aplicações disponíveis na imagem ao aceder às mesmas diretamente no sistema operativo.
  - Esta funcionalidade não proporciona um bloqueio seguro de aplicações; apenas limita a visibilidade no feed de aplicações.
  - Caso tenha de isolar utilizadores de aplicações, deverá utilizar coleções separadas para tal.

## Como obter cmdlets do Azure RemoteApp PowerShell

Para experimentar a nova funcionalidade de pré-visualização, deverá utilizar cmdlets do Azure PowerShell. Atualmente não é possível utilizar o portal de Gestão do Azure para ativar o novo modo de publicação de aplicação.

Em primeiro lugar, certifique-se de que o [módulo Azure PowerShell](../powershell-install-configure.md) está instalado.

Em seguida, inicie a consola do PowerShell no modo de administração e execute o seguinte cmdlet:

        Add-AzureAccount

Ser-lhe-á pedido o seu nome de utilizador e a sua palavra-passe do Azure. Após iniciar sessão, poderá executar cmdlets do Azure RemoteApp com base nas suas subscrições do Azure.

## Como verificar o modo de uma coleção utilizado

Execute o seguinte cmdlet:

        Get-AzureRemoteAppCollection <collectionName>

![Verifique o modo da coleção](./media/remoteapp-perapp/araacllelvel.png)

A propriedade AclLevel pode ter os seguintes valores:

- Coleção: o modo de publicação original. Todos os utilizadores veem todas as aplicações publicadas.
- Aplicação: o novo modo de publicação. Os utilizadores apenas veem as aplicações diretamente publicadas para eles.

## Como mudar para o modo de publicação de aplicação

Execute o seguinte cmdlet:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

O estado de publicação de aplicação será conservado: inicialmente, todos os utilizadores verão todas as aplicações publicadas originais.

## Como listar utilizadores que podem ver uma aplicação específica

Execute o seguinte cmdlet:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Tal lista todos os utilizadores que podem ver a aplicação.

Nota: pode ver os aliases das aplicações (designados "alias da aplicação" na sintaxe acima) executando Get-AzureRemoteAppProgram-CollectionName<collectionName>.

## Como atribuir uma aplicação a um utilizador

Execute o seguinte cmdlet:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

O utilizador verá agora a aplicação no cliente do Azure RemoteApp e poderá estabelecer ligação à mesma.

## Como remover uma aplicação de um utilizador

Execute o seguinte cmdlet:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## Fornecer comentários
Agradecemos os seus comentários e sugestões relativamente a esta funcionalidade de pré-visualização. Preencha o [inquérito](http://www.instant.ly/s/FDdrb) para deixar a sua opinião.

## Ainda não teve oportunidade de experimentar a funcionalidade de pré-visualização?
Caso ainda não tenha participado na pré-visualização, utilize este [inquérito](http://www.instant.ly/s/AY83p) para pedir acesso.



<!--HONumber=Jun16_HO2-->


