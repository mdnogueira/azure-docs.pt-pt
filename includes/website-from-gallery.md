O Azure Marketplace disponibiliza uma vasta gama de Web Apps populares desenvolvidas pela Microsoft, empresas externas e iniciativas de software open source. As aplicações Web criadas a partir do Azure Marketplace não necessitam da instalação de qualquer software que não seja o browser utilizado para ligar ao [Portal de Pré-visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

Neste tutorial, ficará a saber:

* Como criar uma nova aplicação Web através do Azure Marketplace.
* Como implementar a aplicação Web através do Portal de Pré-visualização do Azure.

Irá criar um blogue do WordPress que utilize um modelo predefinido. A ilustração seguinte mostra a aplicação concluída:

![Blogue do WordPress][13]

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

## <a name="create-a-web-app-in-the-portal"></a>Criar uma aplicação Web no portal
1. Inicie sessão no Portal de Pré-visualização do Azure.
2. Abra o Azure Marketplace ao clicar no ícone do **Marketplace**:
   
    ![Ícone do Marketplace][marketplace]
   
    Ou ao clicar no ícone **Novo** no canto superior direito do dashboard e ao selecionar **Marketplace** na parte inferior da lista.
   
    ![Criar Nova][5]
3. Selecione **Web + Móvel**. Procure **WordPress** e clique no ícone do **WordPress**.
   
    ![WordPress na lista][7]
4. Depois de ler a descrição da aplicação WordPress, selecione **Criar**.
5. Clique em **Aplicação Web** e forneça os valores necessários para configurar a sua aplicação Web.
   
    ![configurar a sua aplicação][8]
6. Clique em **Base de Dados** e forneça os valores necessários para configurar a sua base de dados MySQL. 
   
    ![configurar a base de dados][database]
7. Forneça um nome para o novo grupo de recursos.
   
    ![Definir grupo de recursos][groupname]
8. Se necessário, clique em **SUBSCRIÇÃO** e especifique a subscrição a utilizar. 
9. Quando tiver concluído a definição da aplicação Web, clique em **Criar** e aguarde enquanto é criada a nova aplicação Web.
   
   Quando a aplicação tiver sido criada, irá ver o grupo de recursos que contém a aplicação Web e a base de dados.
   
   ![mostrar grupo][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar e gerir a aplicação Web do WordPress
1. Clique na nova aplicação Web para ver detalhes sobre a sua aplicação.
   
    ![iniciar dashboard][10]
2. Na página **Essentials**, clique em **Procurar** ou na ligação em **Url** para abrir a página de boas-vindas da aplicação Web.
   
    ![URL do site][browse]
3. Se não tiver instalado o WordPress, introduza as informações de configuração adequadas exigidas pelo WordPress e clique em **Instalar WordPress** para finalizar a configuração e abrir a página de início de sessão da aplicação Web.
4. Clique em **Iniciar Sessão** e introduza as suas credenciais.  
5. Terá uma nova aplicação Web do WordPress semelhante à aplicação Web abaixo.    
   
    ![o seu site do WordPress][13]

[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png


<!--HONumber=Jan17_HO3-->


