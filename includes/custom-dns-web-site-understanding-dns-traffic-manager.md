O sistema de nomes de domínio (DNS) é utilizado para localizar coisas na internet. Por exemplo, quando introduzir um endereço no seu browser ou clicar numa ligação numa página web, utiliza DNS traduzir o domínio para um endereço IP. O endereço IP é a ordenação dos, como uma morada, mas não é compatível muito humano. Por exemplo, é mais fácil de recordar um nome DNS como **contoso.com** que é lembrar-se de um endereço IP, tais como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS baseia-se no *registos*. Regista associar específico *nome*, tais como **contoso.com**, com um endereço IP ou outro nome DNS. Quando uma aplicação, tal como um browser, procura um nome de DNS, localiza o registo e utiliza que aponta para que o endereço. Se o valor aponta para um endereço IP, o browser utilizará esse valor. Se aponta para outro nome DNS, em seguida, a aplicação tem de repetir a resolução. Em última análise, resolução de nomes de todos os vai terminar dentro de um endereço IP.

Quando cria um Web site do Azure, é atribuído automaticamente um nome DNS para o site. Este nome assume a forma de  **&lt;yoursitename&gt;. azurewebsites.net**. Quando adicionar o seu Web site como um ponto de final do Gestor de tráfego do Azure, o seu Web site, em seguida, é acessível através de  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domínio.

> [!NOTE]
> Quando o seu Web site está configurado como um ponto final do Gestor de tráfego, que irá utilizar o **. trafficmanager.net** endereço quando criar registos DNS.
> 
> Só pode utilizar registos CNAME com o Gestor de tráfego
> 
> 

Também existem vários tipos de registos, cada um com as suas próprias funções e limitações, mas para Web sites configurados como pontos finais do Gestor de tráfego, iremos apenas mais importantes para si sobre um; *CNAME* registos.

### <a name="cname-or-alias-record"></a>Registo CNAME ou Alias
Um registo CNAME mapeia um *específico* de nomes DNS, tal como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canónico). No caso de Web sites do Azure utilizando o Gestor de tráfego, o nome de domínio canónico é o  **&lt;myapp >. trafficmanager.net** nome de domínio do perfil do Traffic Manager. Depois de criado, o CNAME cria um alias para o  **&lt;myapp >. trafficmanager.net** nome de domínio. A entrada CNAME irá resolver para o endereço IP do seu  **&lt;myapp >. trafficmanager.net** nome de domínio automaticamente, pelo que o se o endereço IP do site for alterada, não precisa de efetuar qualquer ação.

Assim que o tráfego chega ao Gestor de tráfego, em seguida, encaminha o tráfego para o seu Web site, utilizando a método balanceamento de carga que está configurado para. Este é completamente transparente para os visitantes do Web site. Apenas verão o nome de domínio personalizado no browser.

> [!NOTE]
> Alguns registrars de domínio apenas lhe permitam mapear os subdomínios quando utilizar um registo CNAME, tais como **www.contoso.com**e não raiz nomes, tal como **contoso.com**. Para obter mais informações sobre registos CNAME, consulte a documentação fornecida pela sua entidade de registo, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada de Wikipedia no registo CNAME</a>, ou o <a href="http://tools.ietf.org/html/rfc1035">IETF os nomes de domínio - implementação e a especificação de</a> documento.
> 
> 

