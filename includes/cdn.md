# <a name="using-cdn-for-azure"></a>Utilizar a CDN do Azure
Rede de entrega de conteúdos (CDN) do Azure oferece aos programadores uma solução global para a entrega de conteúdo de largura de banda alta ao colocar em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul. Para obter uma lista atual de localizações de nó da CDN, consulte [das localizações de nó do Azure CDN].

Esta tarefa inclui os seguintes passos:

* [Passo 1: Criar uma conta de armazenamento](#Step1)
* [Passo 2: Criar um novo ponto final da CDN para a sua conta de armazenamento](#Step2)
* [Passo 3: Aceder ao seu conteúdo da CDN](#Step3)
* [Passo 4: Remover o conteúdo do CDN](#Step4)

As vantagens de utilizar a CDN para colocar em cache dados do Azure incluem:

* Melhor desempenho e experiência do utilizador para os utilizadores finais que estão longe uma origem de conteúdo e estiver a utilizar aplicações onde muitas 'viagens internet' são necessários para carregar conteúdo
* Grande escala distribuída para processar melhor elevadas instantâneas, diga, no início de um evento, tais como iniciar um produto

Clientes existentes do CDN agora podem utilizar a CDN do Azure no [portal clássico do Azure]. A CDN é uma funcionalidade de suplemento à sua subscrição e tem um separado [plano de faturação].

<a id="Step1"> </a>

<h2>Passo 1: Criar uma conta de armazenamento</h2>

Utilize o procedimento seguinte para criar uma nova conta de armazenamento para uma subscrição do Azure. Uma conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o nível mais alto de espaço de nomes para aceder a cada uma dos componentes do serviço de armazenamento do Azure: BLOBs serviços, serviços da fila e serviços de tabela. Para obter mais informações sobre os serviços de armazenamento do Azure, consulte [utilizando os serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Para criar uma conta do storage, tem de ser administrador de serviço ou coadministrador da subscrição associada.

> [!NOTE]
> Para obter informações sobre como efetuar esta operação utilizando a API de gestão de serviço do Azure, consulte o [criar conta de armazenamento](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) tópico de referência.
> 
> 

**Para criar uma conta de armazenamento para uma subscrição do Azure**

1. Inicie sessão no [portal clássico do Azure].
2. No canto inferior esquerdo, clique em **novo**. No **novo** caixa de diálogo, selecione **serviços de dados**, em seguida, clique em **armazenamento**, em seguida, **criação rápida**.
   
   O **criar conta de armazenamento** é apresentada a caixa de diálogo.
   
   ![Criar conta de armazenamento][create-new-storage-account]
3. No **URL** campo, escreva um nome de subdomínio. Esta entrada pode conter de 3 a 24 letras minúsculas e números.
   
    Este valor torna-se o nome de anfitrião no URI que é utilizado para endereçar os recursos de Blob, fila ou tabela para a subscrição. Para resolver um recurso de contentor no serviço Blob, teria de utilizar um URI no seguinte formato, onde  *&lt;StorageAccountLabel&gt;*  refere-se para o valor que escreveu no **introduzir um URL**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Importante:** o URL de etiqueta formulários o subdomínio da conta do storage URI e têm de ser exclusivo entre todos os serviços alojados no Azure.
   
    Este valor é também utilizado como o nome desta conta de armazenamento no portal ou ao aceder a esta conta através de programação.
4. Do **região/grupo de afinidades** na lista pendente, selecione uma região ou a afinidade de grupo para a conta de armazenamento. Selecione um grupo de afinidade em vez de uma região se pretender que os serviços de armazenamento para estar no mesmo Datacenter com outros serviços do Windows Azure que está a utilizar. Isto pode melhorar o desempenho e são cobradas não taxas de saída.  
   
   **Nota:** para criar um grupo de afinidades, abra o **definições** área do Portal de gestão, clique em **grupos de afinidades**e, em seguida, clique em **ao adicionar um grupo de afinidade** ou **adicionar**. Também pode criar e gerir grupos de afinidades utilizando a API de gestão de serviço do Windows Azure. Para obter mais informações, consulte [operações em grupos de afinidades].
5. Do **subscrição** pendente lista, selecione a subscrição que será utilizada a conta de armazenamento com.
6. Clique em **Criar Conta do o Storage**. O processo de criação de conta do storage poderá demorar vários minutos a concluir.
7. Para verificar se a conta de armazenamento foi criada com êxito, certifique-se de que a conta é apresentado nos itens listados para **armazenamento** com um Estado de **Online**.

<a id="Step2"> </a>

<h2>Passo 2: Criar um novo ponto final da CDN para a sua conta de armazenamento</h2>

Depois de ativar o CDN acesso a uma conta de armazenamento ou serviço alojado, todos os objetos publicamente disponíveis são elegíveis para CDN edge a colocação em cache. Se modificar um objeto que está atualmente em cache na CDN, o conteúdo novo não estarão disponível através da CDN, até que a CDN atualiza o respetivo conteúdo, quando o período time-to-live de conteúdo em cache expira.

**Para criar um novo ponto final da CDN para a sua conta de armazenamento**

1. No [portal clássico do Azure], no painel de navegação, clique em **CDN**.
2. No Friso, clique em **novo**. No **novo** caixa de diálogo, selecione **serviços aplicacionais**, em seguida, **CDN**, em seguida, **criação rápida**.
3. No **domínio de origem** lista pendente, selecione o armazenamento de conta que criou na secção anterior da lista das suas contas de armazenamento disponível. 
4. Clique em de **criar** botão para criar o novo ponto final.
5. Quando o ponto final for criado, aparece uma lista de pontos finais para a subscrição. A vista de lista mostra o URL a utilizar para aceder ao conteúdo em cache, bem como o domínio de origem. 
   
    O domínio de origem é a localização a partir do qual o CDN coloca conteúdos em cache. O domínio de origem pode ser uma conta de armazenamento ou um serviço em nuvem; Para efeitos deste exemplo, é utilizada uma conta de armazenamento. Conteúdo de armazenamento é colocado em cache para servidores edge, de acordo com a uma definição de cache-control que especificar ou para a heurística de predefinida da rede colocação em cache. 

    > [AZURE.NOTE]A configuração criada para o ponto final não estará imediatamente disponível. pode demorar até 60 minutos para que o registo propagar através da rede da CDN. Os utilizadores que tentam utilizar o nome de domínio da CDN imediatamente poderão receber o código de estado 400 (pedido incorreto) até que o conteúdo está disponível através do CDN.

<a id="Step3"> </a>

<h2>Passo 3: Acesso conteúdo do CDN</h2> 

Para aceder a conteúdo em cache na CDN, utilize o URL de CDN fornecido no portal. O endereço para um blob em cache será semelhante ao seguinte:

http://&lt*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>

<h2>Passo 4: Remover o conteúdo da CDN</h2>

Se já não pretender colocar em cache um objeto na rede do Azure da entrega de conteúdos (CDN), pode efetuar um dos seguintes passos:

* Para um blob do Azure, pode eliminar o blob do contentor público.
* Pode efetuar o contentor privado em vez de público. Consulte [restringir o acesso a contentores e Blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) para obter mais informações.
* Pode desativar ou eliminar o ponto final CDN com o Portal de gestão.
* Pode modificar o serviço alojado já não responda a pedidos para o objeto.

Um objeto já em cache na CDN irá permanecer em cache até expira o período de tempo-em direto para o objeto. Quando o período de tempo-to-live expirar, a CDN irá verificar se o ponto final de CDN ainda é válido e o objeto ainda anonimamente acessível. Se não for, em seguida, o objeto serão já não ser colocadas em cache.

A capacidade de remover imediatamente conteúdo não é atualmente suportada no Portal de gestão do Azure. Contacte [suporte do Azure](https://azure.microsoft.com/support/options/) se precisar de remover o conteúdo imediatamente. 

## <a name="additional-resources"></a>Recursos adicionais
* [Como criar um grupo de afinidades no Azure]
* [Como: Gerir contas de armazenamento de uma subscrição do Azure]
* [Sobre a API de gestão de serviço]
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado]

[Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[das localizações de nó do Azure CDN]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
[portal clássico do Azure]: https://manage.windowsazure.com/
[plano de faturação]: /pricing/calculator/?scenario=full
[Como criar um grupo de afinidades no Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
[Sobre a API de gestão de serviço]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
[Como Mapear Conteúdo da CDN para um Domínio Personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
