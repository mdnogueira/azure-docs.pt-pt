---
title: "Como configurar uma aplicação de Proxy de aplicações a utilizar a delegação restrita de Kerberos | Microsoft Docs"
description: "Como configurar a delegação restrita de Kerberos para uma aplicação de Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3a768c30cb874d42d7b4fbd2eeaa6c0e23904e10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>Como configurar uma aplicação de Proxy de aplicações a utilizar a delegação restrita de Kerberos

Os métodos disponíveis para alcançar o SSO para as aplicações publicadas um pouco pode variar da aplicação para aplicações e uma das opções que o Proxy de aplicações do Azure oferece direito a Box, é a delegação restrita de Kerberos (KCD). Este é onde um anfitrião do conector é configurado para efetuar a autenticação kerberos restrita para aplicações de back-end, em nome dos utilizadores.

O procedimento para ativar o KCD real é relativamente simples e requer, normalmente, não mais do que uma compreensão geral sobre os vários componentes e fluxo de autenticação que facilita o SSO. Localizar boas origens de informações para ajudar a resolver cenários onde KCD SSO não funcionar conforme esperado, pode ser disperso.

Como tal, este artigo tenta fornecer um ponto único de referência deve ajudar a resolver problemas e automática retificar alguns dos problemas mais comuns que vemos. Ao mesmo tempo proporcionam orientação adicional para diagnosticar o mais complexa e troubled de implementação.

tenha em atenção que este artigo faz com que os seguintes pressupostos:

-   Proxy de aplicações do Azure foi implementado como por nossa [documentação](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) e geral acesso a aplicações de KCD não está a funcionar conforme esperado.

-   A aplicação de destino publicados baseia-se na implementação de IIS e da Microsoft do kerberos.

-   Os anfitriões de servidor e aplicação de residir num único domínio do Active Directory. Informações detalhadas sobre cruzada cenários de domínio e floresta podem ser encontradas na nossa [KCD documento](http://aka.ms/KCDPaper).

-   A aplicação requerente é publicada num Azure inquilino com a pré-autenticação ativado e os utilizadores devem autenticar para o Azure através da autenticação de formulários com base. Cenários de autenticação de cliente avançada não são abrangidos por este artigo, mas ser adicionados, a determinada altura no futuro.

## <a name="prerequisites"></a>Pré-requisitos

Proxy de aplicações do Azure pode ser implementado em praticamente qualquer tipo de infraestrutura ou de ambiente e as arquiteturas doubt variam de organização para organização. Uma das causas mais comuns de KCD relacionadas com problemas não o ambientes, mas em vez disso simples incorrecta configurações ou supervisão geral.

Por este motivo, é sempre nosso conselhos para iniciar, certificando-se de que cumpriu todos os pré-requisitos apresentados na nossa main [utilizando SSO KCD no artigo de Proxy de aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) antes de começar resolução de problemas.

Especialmente a secção sobre como configurar KCD no 2012R2, como esta utiliza uma abordagem fundamentalmente diferente para configurar KCD em versões anteriores do Windows, mas também ao que está a ser mindful de muitas outras considerações:

-   Não é invulgar para um servidor membro do domínio abrir uma caixa de diálogo de canal seguro com um controlador de domínio específico. Em seguida, mova para outra caixa de diálogo em qualquer momento, para que anfitriões de conector não geralmente devem ser restrita a conseguir comunicar com apenas específico site local DCs.

-   Semelhante para o ponto acima, o domínio cruzado cenários dependem referências direcionam um anfitrião do conector para controladores de domínio que pode residir fora de perímetro da rede local. Este cenário é igualmente importante certificar-se também permitir o tráfego em diante para controladores de domínio que representam noutros domínios respetivos ou falharão a delegação de outra pessoa.

-   Sempre que possível, se deve colocar quaisquer dispositivos IPS/IDS ativos entre anfitriões de conector e os controladores de domínio, como estes são, por vezes, intrusivo e interfere com o tráfego RPC de núcleo

Como gostaríamos de resolver os problemas rapidamente e efetivamente, pode demorar tempo, por isso, sempre que possível deve tentar e testar a delegação mais simples de cenários. As variáveis mais introduz, mais poderá ter de disputam com. Por exemplo, limitar os testes para um único conector pode poupar tempo útil e conectores adicionais podem ser adicionados depois dos problemas foi resolvido.

Alguns fatores ambientais podem também ser contribuir para um problema, novamente, por isso, se possível, tente e minimiza a arquitetura para bare mínimo de teste. Por exemplo, não são incomuns firewall interno mal configurada ACLs, por isso, se possível ter todo o tráfego de um conector permitido diretamente através do DC e aplicação de back-end. 

Na realidade, o melhor local absoluto para a posição conectores é tão próximos destinos de como pode ser. Ter um inline firewall DOM enfrenta testar simplesmente adiciona complexidade desnecessária e apenas pode prolongar as investigações.

Por isso, o que constitui um problema KCD mesmo assim? Assim, existe várias indicações clássicas de SSO KCD falhar e as primeira sinais de um problema normalmente manifestam-se no browser.

   ![Erro de configuração de KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autorização falhou devido a falta de permissões](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

todas as que tenha a mesma sintoma de falha ao executar o SSO e, consequentemente, a negar o acesso do utilizador para a aplicação.

## <a name="troubleshooting"></a>Resolução de problemas

A forma como os, em seguida, resolver problemas de dependem o problema e os sintomas observadas. Antes de avançar qualquer mais seria Sugerimos as hiperligações seguintes, que contêm informações úteis, que não pode ainda tem origem em:

-   [Resolver problemas de Proxy de aplicações e as mensagens de erro](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Erros de Kerberos e sintomas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Trabalhar com o SSO quando no local e na nuvem não são idênticas identidades](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Se já tem esta, extremidade, em seguida, o problema principal sem dúvida existe. Terá de aprofundar, por isso, comece por separar o fluxo em três fases distintas que pode resolver.

**Pré-autenticação de cliente** - o utilizador externo, a autenticação no Azure através de um browser.

A capacidade para pré-autenticação do Azure é imperativo para KCD SSO para a função. Isto deve ser testado e resolvido em primeiro lugar, se existirem quaisquer problemas. Tenha em atenção que a fase de pré-autenticação não possui nenhuma relação KCD ou a aplicação publicada. Deve ser bastante fácil de corrigir quaisquer discrepâncias ao sanity a verificar a conta de assunto existe no Azure e que este não está desativado/bloqueado. A resposta de erro no browser é normalmente descritiva suficiente para compreender a causa. Também pode ver a nossa outros docs de resolução de problemas para verificar se não tem a certeza.

**Serviço de delegação** - o conector do Proxy do Azure obter permissão de serviço a partir de um KDC (Centro de distribuição de Kerberos), de um kerberos em nome dos utilizadores.

As comunicações entre o cliente e o front-end do Azure externas não devem ter o nenhum efeito em KCD contratutal, exceto se certificar de que funciona. Isto é, para o serviço de Proxy do Azure pode ser fornecido com um ID de utilizador válido que ser utilizado para obter uma permissão kerberos. Sem esta KCD não é possível e irão falhar.

Como mencionado anteriormente, as mensagens de erro do browser fornecem geralmente algumas processáveis boas por que motivo coisas estão a falhar. Certifique-se anotar o ID de atividade e timestamp na resposta como esta permitem-lhe correlacionar o comportamento para reais eventos no registo de eventos de Proxy do Azure.

   ![Erro de configuração de KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

E visto das entradas correspondentes que no registo de eventos teria de ser visto como eventos 13019 ou 12027. Pode encontrar os registos de eventos do conector no **registos de serviços e aplicações** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **conector**&gt;**Admin**.

   ![Evento 13019 de registo de eventos de Proxy de aplicações](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![12027 de eventos de registo de eventos de Proxy de aplicações](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Utilizar um registo no seu DNS interno para o endereço da aplicação e não um CName

-   Voltar a confirmar se foram concedido os direitos para delegar a designado SPN de conta de destino e de que o anfitrião do conector **utilizar qualquer protocolo de autenticação** está selecionada. Isto é explicado na nossa main [artigo de configuração de SSO](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Verifique se existe apenas uma única instância o SPN na existência no AD ao emitir um **setspn - x** de uma linha de comandos em qualquer anfitrião membro de domínio

-   Verifique se a política de domínio está a ser imposta para limitar o [máximo de tamanho de tokens emitidos kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), uma vez que isto impedir o conector de obter um token se não for excessiva

Um rastreio da rede capturar as trocas entre o anfitrião do conector e um domínio KDC seria, em seguida, o passo seguinte melhores na obtenção mais baixa detalhes do nível de problemas. Pode encontrar isto em [detalhada da resolução de problemas documento](https://aka.ms/proxytshootpaper).

Se a emissão de permissões procura boa, deverá ver um evento nos registos a indicar que a autenticação falhou devido a aplicação devolver um 401. Isto indica normalmente que a aplicação de destino rejeitar o pedido de suporte, por isso, pode continuar com a próxima fase seguinte.

**Aplicação de destino** -consumidor do bilhete kerberos fornecido pelo conector do

Nesta fase é esperado que o conector ter enviado um kerberos permissão de serviço para o back-end, como um cabeçalho o primeiro pedido de aplicação.

-   Utilizar de URL interno a aplicação definido no portal, confirme que a aplicação está acessível diretamente a partir do browser no anfitrião do conector. Em seguida, pode iniciar sessão com êxito. Podem ser encontrados detalhes no fazê-lo na página de resolução de problemas de conector.

-   Ainda no anfitrião do conector, confirme que a autenticação entre o browser e a aplicação está a utilizar o kerberos, efetuando um dos seguintes:

1.  Executar as ferramentas de desenvolvimento (**F12**) no Internet Explorer ou utilizar [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) do anfitrião de conector. Aceda à aplicação utilizando o URL interno e inspecionar o oferecido cabeçalhos de autorização de WWW devolvido na resposta da aplicação, para se certificar de que quer negociar ou kerberos está presente. Um blob de kerberos subsequentes devolvido na resposta do browser para a aplicação iniciar normalmente com **YII**, pelo que este é um bom indicador de kerberos a ser na play. NTLM por outro lado inicie sempre com **TlRMTVNTUAAB**, que lê NTLMSSP quando descodificar a partir de Base64. Se vir **TlRMTVNTUAAB** no início do blob, isto significa que Kerberos **não** disponíveis. Se não vir isto, o Kerberos é provavelmente disponíveis.

  * Tenha em atenção, se utilizar o Fiddler, este método seria necessários temporariamente a desativação da proteção expandida na configuração da aplicação no IIS.

     ![Janela de inspeção de rede do browser](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Figura:* , uma vez que isto não inicia com TIRMTVNTUAAB, este é um exemplo que Kerberos está disponível. Este é um exemplo de um Blob de Kerberos não comece com YII.

2.  Remova temporariamente NTLM na lista de fornecedores na aplicação de site e acesso IIS diretamente a partir do IE no anfitrião de conector. Com o NTLM já não está na lista de fornecedores, deverá conseguir aceder à aplicação a utilizar apenas Kerberos. Se isto falhar, em seguida, que sugere que existe um problema com a configuração da aplicação e a autenticação Kerberos não está a funcionar.

Se Kerberos não estiver disponível, negociar as definições de autenticação da aplicação no IIS para se certificar de verificação é listada mais superiores, com o NTLM imediatamente por baixo. (Não negociar: kerberos ou negociar: ao PKU2U). Continue apenas se o Kerberos é funcional.

   ![Fornecedores de autenticação do Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Com o Kerberos e NTLM no local, permite agora desativar temporariamente pré-autenticação para a aplicação no portal. Ver se consegue aceder-lhe a partir da internet utilizando o URL externo. Deve ser solicitado para autenticar e devem ser capazes de fazê-lo com a mesma conta utilizada no passo anterior. Caso contrário, isto indica um problema com a aplicação de back-end e não KCD de todo.

-   Agora, volte a ativar a pré-autenticação no portal do e autenticar através do Azure ao tentar ligar-se para a aplicação através do URL externo. Se o SSO falhou, em seguida, deverá ver uma mensagem de erro proibido no browser, mais eventos 13022 no registo de:

    *Conector do Proxy de aplicações do Microsoft AAD não é possível autenticar o utilizador porque o servidor de back-end responde a tentativas de autenticação Kerberos com um erro de HTTP 401.*

    ![401 HTTTP proibido erro](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Verifique a aplicação do IIS para garantir que o conjunto aplicacional configurado está configurada para utilizar a mesma conta que o SPN foi configurado contra no AD, ao navegar no IIS, conforme ilustrado abaixo

    ![Janela de configuração de aplicação do IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Depois de conheçam a identidade, emita o seguinte a partir de uma linha de comandos para se certificar de que esta conta é sem dúvida configurada com o SPN em questão. Por exemplo, **setspn – q http/spn.wacketywack.com**

    ![Janela de comandos SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Verifique se o SPN definido contra definições da aplicação no portal é o mesmo SPN que está configurado com a conta de destino AD em utilização pelo agrupamento de aplicações da aplicação

   ![Configuração de SPN no Portal do Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Vá para o IIS e selecione o **Editor de configuração** opção para a aplicação e navegue para **system.webServer/security/authentication/windowsAuthentication** para se certificar de que **UseAppPoolCredentials** está definido como verdadeiro

   ![Opção de credencial de agrupamentos de aplicações de configuração do IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Enquanto a ser útil para melhorar o desempenho de operações do Kerberos, mantendo o modo de Kernel ativado também faz com que o pedido de suporte para o serviço pedido a desencriptar utilizando a conta de computador. Isto também é denominado o sistema Local, por isso, ter esta definido como verdadeira quebra KCD quando a aplicação fique alojada em vários servidores num farm.

-   Como uma verificação adicional, também poderá pretender desativar o **expandida** proteção demasiado. Foram encontrados cenários em que este tem foi tentativa para interromper KCD quando ativado nas configurações muito específicas, em que uma aplicação for publicada como uma subpasta do Default Web site. Este propriamente dito está configurado para autenticação anónima apenas, deixando as caixas de diálogo de todos a cinzento sugerindo objetos subordinados não seriam possível herdar as definições do Active Directory. Mas onde possível, recomendamos que seria sempre ter esta opção ativada, por isso, por todos os means testar, mas não se esqueça de restaurar este ativado.

Estas verificações adicionais devem colocados é controlado para começar a utilizar a aplicação publicada. Pode avançar e rotação de cópia de segurança adicionais conectores que também estejam configurados para delegar, mas se coisas não adicionais, em seguida, seria sugerimos uma leitura nosso instruções de técnicas mais aprofundadas [o guia completo para o Proxy da aplicação resolver problemas relacionados com o Azure AD](https://aka.ms/proxytshootpaper)

Se não estiver ainda não é possível progresso seu problema, suporte seria mais do que satisfeito para ajudá-lo e continuar a partir daqui. Criar um pedido de suporte diretamente no portal e nosso engenheiros irão entrar para si.

## <a name="other-scenarios"></a>Outros cenários

-   Proxy de aplicações do Azure pede uma permissão Kerberos antes de enviar o pedido para uma aplicação. Algumas aplicações de terceiros 3rd como o Tableau Server não como este método de autenticação e espera em vez de negociações mais convencionais seja realizada. O primeiro pedido é anónimo, permitindo que a aplicação responder com os tipos de autenticação que suporta através de um 401.

-   Duplo salto autenticação - normalmente utilizada em cenários em que uma aplicação está em camadas, com um back-end e frente end, tanto que exigem que a autenticação, como SQL Server Reporting Services.

## <a name="next-steps"></a>Passos seguintes
[Configurar a delegação restringida de kerberos (KCD) um domínio gerido](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
