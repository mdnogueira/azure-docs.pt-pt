# [Descrição geral](media-services-overview.md)
## [Conceitos](media-services-concepts.md)


# Introdução
## [Criar e gerir a conta](media-services-portal-create-account.md)
## [Configurar o ambiente de desenvolvimento](media-services-set-up-computer.md)
## Entregar vídeo a pedido
### [Portal](media-services-portal-vod-get-started.md)
### [SDK do .NET](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Realizar transmissão em direto
### [Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procedimento
## Gerir
### [Gerir pontos finais de transmissão no portal](media-services-portal-manage-streaming-endpoints.md)
### Gerir entidades
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Gerir contas com o PowerShell](media-services-manage-with-powershell.md)
### [Recortar vídeos com o Media Encoder Standard](media-services-crop-video.md)
### [Procedimento: Atualizar Serviços de Multimédia após as Chaves de Acesso ao Armazenamento Sem Interrupção](media-services-roll-storage-access-keys.md)
### [Quotas e limitações](media-services-quotas-and-limitations.md)
### Filtros
#### [Criar Filtros com o SDK .NET dos Serviços de Multimédia do Azure](media-services-dotnet-dynamic-manifest.md)
#### [Como codificar um recurso com o Media Encoder Standard](media-services-rest-encode-asset.md)
### Ligar através de programação
#### [.NET](media-services-dotnet-connect-programmatically.md)
#### [REST](media-services-rest-connect-programmatically.md)

## Carregar conteúdo
### Carregar ficheiros para uma conta
#### [Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Copiar blobs existentes](media-services-copying-existing-blob.md)

## Codificar
### [Conteúdo](media-services-encode-asset.md)
#### Codificar um ativo com o Media Encoder Standard
##### [Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
#### [Como gerar miniaturas com o Media Encoder Standard com o .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
#### [Codificação avançada](media-services-advanced-encoding-with-mes.md)
##### [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-with-premium-workflow.md)
##### [Tutoriais de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Criar Fluxos de Trabalho de Codificação Avançados com o Estruturador de Fluxos de Trabalho](media-services-workflow-designer.md)
##### [Fluxo de trabalho Premium com várias entradas](media-services-media-encoder-premium-workflow-multiplefilesinput.md)

#### Esquemas 
#####[Media Encoder Standard](media-services-mes-schema.md)
#####[Metadados de entrada](media-services-input-metadata-schema.md)
#####[Metadados de saída](media-services-output-metadata-schema.md)

#### Codificadores legados
##### [Utilizar o Empacotador de Multimédia do Azure](media-services-static-packaging.md)

### [Transmissões em direto](media-services-manage-channels-overview.md)
#### [Codificadores no local](media-services-live-streaming-with-onprem-encoders.md)
#### Tutoriais do codificador no local
##### [Portal](media-services-portal-live-passthrough-get-started.md)
##### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [Transmissão em direto com codificador em nuvem](media-services-manage-live-encoder-enabled-channels.md)
#### Tutoriais do codificador de nuvem
##### [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
##### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
#### [Configurar codificadores no local para utilização com codificadores em nuvem](media-services-live-encoders-overview.md)
#### [Lidar com operações de longa duração](media-services-dotnet-long-operations.md)
#### [Especificação de ingestão em tempo real de MP4 fragmentado](media-services-fmp4-live-ingest-overview.md)
#### [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md)

### Processamento de Multimédia
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)

### Configurar codificadores para uma transmissão em fluxo de velocidade de transmissão única
#### [Codificador em Direto Elementar](media-services-configure-elemental-live-encoder.md)
#### [Codificador de FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificador de NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificador de Wirecast](media-services-configure-wirecast-live-encoder.md)

## [Proteger](media-services-content-protection-overview.md)
### [Configurar a proteção de conteúdos no portal](media-services-portal-protect-content.md)
### [Configurar a chave não encriptada AES-128 para o fluxo](media-services-protect-with-aes128.md)
### [Encriptar o Conteúdo com Encriptação de Armazenamento através da API REST do AMS](media-services-rest-storage-encryption.md)
### [Descrição Geral do Modelo de Licença do PlayReady dos Serviços de Multimédia](media-services-playready-license-template-overview.md)
### [Entrega de licença DRM](media-services-deliver-keys-and-licenses.md)
### [Utilização de parceiros para entregar licenças do Widevine para Serviços de Multimédia do Azure](media-services-licenses-partner-integration.md)
### [Utilizar a encriptação comum dinâmica com o PlayReady e/ou o Widevine](media-services-protect-with-drm.md)
### [Utilizar Serviços de Multimédia do Azure para Transmitir o conteúdo HLS Protegido com o Apple FairPlay](media-services-protect-hls-with-fairplay.md)
### [CENC com Controlo de Acesso e Multi-DRM: Uma Estrutura de Referência e Implementação no Azure e Serviços de Multimédia do Azure](media-services-cenc-with-multidrm-access-control.md)

### Entrega de ativo
#### Configurar políticas de entrega de ativo
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Criar ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Configure a política de autorização da chave de conteúdo
#### [Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [Analisar](media-services-analytics-overview.md)
### [Processar com o Indexador 2](media-services-process-content-with-indexer2.md)
### [Processar com o Indexador](media-services-index-content.md)
### [Processar com o Hyperlapse](media-services-hyperlapse-content.md)
### [Processar com o Detetor de Rostos](media-services-face-and-emotion-detection.md)
### [Processar com o Detetor de Movimento](media-services-motion-detection.md)
### [Processar com a Redação de rostos](media-services-face-redaction.md)
### [Processar com miniaturas de vídeos](media-services-video-summarization.md)
### [Processar com o OCR](media-services-video-optical-character-recognition.md)

## Escala
### [Processamento de Multimédia](media-services-scale-media-processing-overview.md)
#### [Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
### Pontos Finais de Transmissão em fluxo
#### [Portal](media-services-portal-scale-streaming-endpoints.md)

## [Entregar conteúdo](media-services-deliver-content-overview.md)
### [Descrição geral dos filtros e dos manifestos dinâmicos](media-services-dynamic-manifest-overview.md)
### Criar filtros
#### [.NET](media-services-dotnet-dynamic-manifest.md)
#### [REST](media-services-rest-dynamic-manifest.md)
### Publicar conteúdo
#### [Portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Entregar por Transferência](media-services-deliver-asset-download.md)
### [Cenário de transmissão de ativação pós-falha](media-services-implement-failover.md)

## Consumir
### [Reproduzir multimédia com leitores existentes](media-services-playback-content-with-existing-players.md)
### [Reproduzir multimédia com Leitor de Multimédia](media-services-develop-video-players.md)
### Outras opções de reprodução
#### [Aplicação da Loja Windows de transmissão em fluxo uniforme](media-services-build-smooth-streaming-apps.md)
#### [Aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Leitores Open Source Media Framework da Adobe](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Inserir publicidade no lado do cliente](media-services-inserting-ads-on-client-side.md)

## Integrar
### [Política de Colocação em Cache de CDN na Extensão de Serviços de Multimédia](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### [Licenciamento do Kit de Migração de Cliente de Transmissão em Fluxo Uniforme da Microsoft†"](media-services-sspk.md)
### [Gerir ativos entre várias contas de Armazenamento](meda-services-managing-multiple-storage-accounts.md)
### [Utilizar o Axinom para entregar licenças do Widevine a Serviços de Multimédia do Azure](media-services-axinom-integration.md)
### [Utilizar castLabs para entregar licenças do Widevine a Serviços de Multimédia do Azure](media-services-castlabs-integration.md)
### [Descrição Geral do Modelo de Licença do Widevine](media-services-widevine-license-template-overview.md)

## Monitorizar
### Verificar progresso da tarefa
#### [REST](media-services-rest-check-job-progress.md)
#### [Portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Armazenamento de filas para monitorizar as notificações de tarefas](media-services-dotnet-check-job-progress-with-queues.md)

## Resolução de problemas
### [Perguntas mais frequentes](media-services-frequently-asked-questions.md)
### [Guia de resolução de problemas da transmissão em direto](media-services-troubleshooting-live-streaming.md)
###[Códigos de erro](media-services-error-codes.md)
###[Repetir a lógica](media-services-retry-logic-in-dotnet-sdk.md)

# Referência
## [Notas de versão](media-services-release-notes.md)
## [.NET](media-services-dotnet-how-to-use.md)
## [REST](media-services-rest-how-to-use.md)
## [Formatos e Codecs de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-premium-workflow-encoder-formats.md)
## [Formatos e Codecs do Media Encoder Standard](media-services-media-encoder-standard-formats.md)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/media-services/)
## [Comunidade dos Serviços de Multimédia do Azure](media-services-community.md)











<!--HONumber=Nov16_HO4-->


