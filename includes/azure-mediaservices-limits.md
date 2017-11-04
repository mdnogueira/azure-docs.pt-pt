>[!NOTE]
>Para recursos que não está corrigidos, pode abrir um pedido de suporte para pedir o aumento das quotas. **Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

| Recurso | Limite Predefinido | 
| --- | --- | 
| Contas dos Serviços de Multimédia do Azure (AMS) numa subscrição individual | 25 (fixo) |
| Unidades Reservadas de Multimédia (RUs) por conta do AMS |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| Trabalhos por conta do AMS | 50,000<sup>(2)</sup> |
| Tarefas em cadeia por trabalho | 30 (fixo) |
| Elementos por conta do AMS | 1 000 000|
| Elementos por tarefa | 50 |
| Elementos por trabalho | 100 |
| Localizadores exclusivos associados a um elemento ao mesmo tempo | 5<sup>(4)</sup> |
| Canais em direto por conta do AMS |5|
| Programas no estado de paragem por canal |50|
| Programas no estado parado por canal |3|
| Pontos finais de transmissão em fluxo no estado em execução por conta do AMS|2|
| Unidades de transmissão em fluxo por ponto final de transmissão em fluxo |10 |
| Contas de armazenamento | 1000<sup>(5)</sup> (fixo) |
| Políticas | 1,000,000<sup>(6)</sup> |
| Tamanho dos ficheiros| Em alguns cenários, existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. <sup>7</sup> |
  
<sup>1</sup> As RUs S3 não estão disponíveis na Índia Ocidental. Os limites de RU máximas obterem repor se o cliente muda o tipo (por exemplo, a partir de S2 para S1). 

<sup>2</sup> Este número inclui trabalhos em fila, concluídos, ativos e cancelados. Não inclui trabalhos eliminados. Pode utilizar **IJob.Delete** ou o pedido HTTP **DELETE** para eliminar os trabalhos antigos.

A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Se precisar de arquivar as informações de tarefas, pode utilizar o código descrito [aqui](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> Ao fazer pedidos para listar entidades de trabalhos, é devolvido um máximo de mil por pedido. Se tiver de estar a par de todos os Trabalhos submetidos, pode utilizar “top/skip”, conforme descrito em [OData system query options (Opções de consultas de sistema OData)](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Os localizadores não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM). Para obter mais informações, veja [esta](../articles/media-services/media-services-content-protection-overview.md) secção.

<sup>5</sup> As contas de armazenamento têm de estar na mesma subscrição do Azure.

<sup>6</sup> Existe um limite de um milhão de políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Se estiver sempre a utilizar os mesmos dias, permissões de acesso, etc., deve utilizar o mesmo ID de política. Para obter informações e um exemplo, consulte [esta](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) secção.

<sup>7</sup>se estiver a carregar conteúdo para um recurso de Media Services do Azure com a tentativa de processá-la com uma dos processadores de suporte de dados no nosso serviço (ou seja, codificadores como motores codificador de multimédia Standard e o fluxo de trabalho do suporte de dados codificador Premium ou análise como enfrentam Detector), em seguida, deve ter conhecimento da restrição no tamanho máximo. 

A partir do dia 15 de Maio de 2017, o tamanho máximo suportado para um blob único 195 TB - com largers de ficheiro que este limite, a tarefa irá falhar. Estamos a trabalhar uma correção para resolver este limite. Além disso, a restrição no tamanho máximo do elemento é a seguinte.

| Tipo de Unidade Reservada de Multimédia | Tamanho máximo de entrada (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
