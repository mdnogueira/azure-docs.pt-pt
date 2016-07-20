<properties 
    pageTitle="Autenticação do Windows e Servidor Multi-Factor Authentication do Azure" 
    description="Esta é a página do Multi-Factor Authentication do Azure que irá ajudar a implementar a Autenticação do Windows e o Servidor Multi-Factor Authentication do Azure." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Autenticação do Windows e Servidor Multi-Factor Authentication do Azure

A secção Autenticação do Windows permite ao administrador ativar e configurar a autenticação do Windows para uma ou mais aplicações.  Segue-se uma lista do que deve ter em conta antes de configurar a Autenticação do Windows.

-  É necessário reiniciar para aplicar o Multi-Factor Authentication do Azure aos Serviços de Terminal.
-  Se "Exigir correspondência de utilizador do Multi-Factor Authentication do Azure" estiver selecionado e não estiver na lista de utilizadores, não conseguirá iniciar sessão no computador depois de reiniciar.
-  Os IPs Fidedignos dependem do facto de a aplicação poder fornecer o IP do cliente na autenticação. Atualmente, apenas os Serviços de Terminal são suportados.  







>[AZURE.NOTE]Esta funcionalidade não é suportada para proteger os Serviços de Terminal no Windows Server 2012 R2.
 



## Para proteger uma aplicação com a Autenticação do Windows, utilize o procedimento seguinte.

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do Windows.
![Autenticação do Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Selecione a caixa de verificação Ativar autenticação do Windows. Por predefinição, esta caixa está desmarcada.
3. O separador Aplicações permite ao administrador configurar uma ou mais aplicações para a Autenticação do Windows.
4. Selecione um servidor ou uma aplicação e especifique se o servidor/aplicação está ativado. Clique em OK.
5. Clique no botão Adicionar...
6. O separador IPs Fidedignos permite ignorar sessões o Multi-Factor Authentication do Azure para as sessões do Windows com origem em IPs específicos. Por exemplo, se os empregados utilizarem a aplicação a partir do escritório e de casa, pode decidir que não pretende que os respetivos telefones toquem para o Multi-Factor Authentication do Azure enquanto estiverem no escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos.
7. Clique no botão Adicionar...
8. Selecione IP Único se gostaria de ignorar um único endereço IP.
9. Selecione Intervalo de IP se gostaria de ignorar um intervalo de IP completo. Por exemplo, 10.63.193.1-10.63.193.100.
10. Selecione Sub-rede se pretende especificar um intervalo de IPs utilizando a notação de sub-rede. Introduza o IP inicial da sub-rede e escolha a máscara de rede adequada na lista pendente. 
11. Clique no botão OK.


<!--HONumber=Jun16_HO2-->


