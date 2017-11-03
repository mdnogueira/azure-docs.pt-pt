---
title: "Referência das definições de roaming do Windows 10 | Microsoft Docs"
description: "Uma lista completa de todas as definições que serão movidos ou uma cópia de segurança no Windows 10."
services: active-directory
keywords: roaming de estado empresarial, na nuvem do windows
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 5082ed8d2f41e72fa979b978e2ac0b0840fdcdac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-10-roaming-settings-reference"></a>Referência das definições de roaming do Windows 10
Segue-se uma lista completa de todas as definições que serão movidos ou uma cópia de segurança no Windows 10. 

## <a name="devices-and-endpoints"></a>Dispositivos e os pontos finais
Consulte a tabela seguinte para obter um resumo dos dispositivos e tipos de conta que são suportados por uma cópia de segurança, a sincronização e restaurar framework no Windows 10.

| Tipo de conta e a operação | Ambiente de trabalho | Telemóvel |
| --- | --- | --- |
| Azure Active Directory: sincronização |Sim |Não |
| Azure Active Directory: cópia de segurança/restauro |Não |Não |
| Conta Microsoft: sincronização |Sim |Sim |
| Conta Microsoft: cópia de segurança/restauro |Não |Sim |

## <a name="what-is-backup"></a>O que é a cópia de segurança?
Em geral, sincronizar as definições do Windows por predefinição, mas algumas definições são apenas cópias de segurança, como a lista de aplicações instaladas no dispositivo. Cópia de segurança é para dispositivos móveis apenas e não se encontra atualmente disponíveis para utilizadores de Roaming de estado empresarial. Cópia de segurança utiliza uma conta Microsoft e armazena as definições e dados da aplicação no OneDrive. Se um utilizador desativa a sincronização no dispositivo com a aplicação de definições, os dados de aplicação que normalmente sincroniza-se torna-se cópia de segurança apenas. Dados de cópia de segurança só podem ser acedidos através da operação de restauro durante a experiência de primeira execução de um novo dispositivo. As cópias de segurança podem ser desativadas através de definições do dispositivo e podem ser geridas e eliminadas através da conta do OneDrive.

## <a name="windows-settings-overview"></a>Descrição geral das definições do Windows
Os seguintes grupos de definições estão disponíveis para os utilizadores finais Ativar/desativar a sincronização de definições em dispositivos Windows 10.

* Tema: ambiente de trabalho em segundo plano, o mosaico de utilizador, posição de barra de tarefas, etc. 
* Definições do Internet Explorer: histórico de navegação escritas URLs, Favoritos, etc. 
* As palavras-passe: [cacifo de credenciais do Windows](https://technet.microsoft.com/library/jj554668.aspx), incluindo perfis Wi-Fi 
* Preferências de idioma: dicionário de ortografia definições de idioma do sistema 
* Facilidade de acesso: Narrador, o teclado no ecrã, Lupa 
* Outras definições do Windows: consulte os detalhes de definições do Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)

Sincronização de grupo (Favoritos, lista de leitura) do contorno browser definição pode ser ativado ou desativado pelos utilizadores finais através do browser Edge opção do menu de definições.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-sync-content.png)

## <a name="windows-settings-details"></a>Detalhes de definições do Windows
A tabela seguinte, outras entradas na coluna grupo de definições de refere-se às definições que podem ser desativadas acedendo às definições > contas > sincronize as suas definições > definições de outras janelas. 

Internas entradas na coluna grupo de definições de fazer referência a definições e aplicações que só podem ser desativadas da aplicação em si ou através da desativação de sincronização para o dispositivo completo utilizando a gestão de dispositivos móveis (MDM) ou definições de política de grupo.
As definições que não sejam acedidas remotamente ou sincronização não irão pertencer a um grupo.

| Definições | Ambiente de trabalho | Telemóvel | Grupo |
| --- | --- | --- | --- |
| **Contas**: imagem de conta |Sincronização |X |Tema |
| **Contas**: outras definições de conta |X |X | |
| **Avançadas de banda larga móvel**: nome de rede (permite a deteção automática de hotspots Wi-Fi móveis através de Bluetooth) de partilha de ligação de Internet |X |X |Palavras-passe |
| **Os dados da aplicação**: aplicações individuais podem sincronizar os dados |cópia de segurança da sincronização |cópia de segurança da sincronização |Interno |
| **Lista de aplicações**: lista de aplicações instaladas |X |cópia de segurança |Outros |
| **Bluetooth**: todas as definições de Bluetooth |X |X | |
| **Linha de comandos**: definições de "Predefinições" de linha de comandos |Sincronização |X | |
| **Credenciais**: cacifo de credenciais |Sincronização |Sincronização |palavra-passe |
| **Data, hora e região**: tempo automático (sincronização de hora da Internet) |Sincronização |Sincronização |idioma |
| **Data, hora e região**: relógio de 24 horas |Sincronização |X |idioma |
| **Data, hora e região**: data e hora |Sincronização |X |idioma |
| **Data, hora e região**: fuso horário | |X |idioma |
| **Data, hora e região**: hora de Verão |Sincronização |X |idioma |
| **Data, hora e região**: país/região |Sincronização |X |idioma |
| **Data, hora e região**: primeiro dia da semana |Sincronização |X |idioma |
| **Data, hora e região**: formato de região (região) |Sincronização |X |idioma |
| **Data, hora e região**: curto data |Sincronização |X |idioma |
| **Data, hora e região**: longa data |Sincronização |X |idioma |
| **Data, hora e região**: curto período de tempo |Sincronização |X |idioma |
| **Data, hora e região**: longa tempo |Sincronização |X |idioma |
| **Personalização de ambiente de trabalho**: tema de ambiente de trabalho (em segundo plano, cor do sistema, sons de sistema predefinido, proteção de ecrã) |Sincronização |X |Tema |
| **Personalização de ambiente de trabalho**: slideshow imagem de fundo |Sincronização |X |Tema |
| **Personalização de ambiente de trabalho**: definições de barra de tarefas (posição, ocultar automática, etc.) |Sincronização |X |Tema |
| **Personalização de ambiente de trabalho**: iniciar o esquema do ecrã |X |cópia de segurança | |
| **Dispositivos**: partilhado impressoras se ligar à |X |X |outro |
| **Edge browser**: lista de leitura |Sincronização |Sincronização |Interno |
| **Edge browser**: Favoritos |Sincronização |Sincronização |Interno |
| **Edge browser**: principais sites <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: escreveu URLs <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: definições de barra de Favoritos <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: Mostrar botão home <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: bloquear pop-ups <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: perguntar o que fazer com cada transferência <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: oferecem guardar as palavras-passe <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: enviar not track pedidos <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: guardar entradas do formulário <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: Mostrar sugestões de pesquisa e o site como posso tipo <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: preferência de cookies <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: permitir que os sites guardar licenças de suporte de dados protegidos no meu dispositivo <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Edge browser**: a definição de leitor de ecrã <sup> [[1]](#footnote-1)</sup> |Sincronização |Sincronização |Interno |
| **Alto contraste**: ou desativar |Sincronização |X |Facilidade de acesso |
| **Alto contraste**: definições de tema |Sincronização |X |Facilidade de acesso |
| **Internet Explorer**: Abra separadores (URL e título) |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: lista de leitura |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: escritas de URLs |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: histórico de navegação |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: Favoritos |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: excluídos URLs |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: home pages de |Sincronização |Sincronização |Internet Explorer |
| **Internet Explorer**: sugestões de domínio |Sincronização |Sincronização |Internet Explorer |
| **Teclado**: os utilizadores podem ativar/terminar a sessão o teclado no ecrã |Sincronização |X |Facilidade de acesso |
| **Teclado**: Ativar temporária Sim (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Teclado**: Ativar chaves de filtro (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Teclado**: Ativar chaves de ativação/desativação (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Internet Explorer**: domínio idiomas: chinês (CHS) QWERTY - ativar aprendizagem automática |Sincronização |X |Idioma |
| **Idioma**: QWERTY CHS - candidato dinâmica Ativar classificação |Sincronização |X |Idioma |
| **Idioma**: CHS QWERTY - char-set chinês simplificado |Sincronização |X |Idioma |
| **Idioma**: CHS QWERTY - char-set chinês tradicional |Sincronização |X |Idioma |
| **Idioma**: QWERTY CHS - pinyin difusa |Sincronização |cópia de segurança |Idioma |
| **Idioma**: QWERTY CHS - pares difusa |Sincronização |cópia de segurança |Idioma |
| **Idioma**: QWERTY CHS - pinyin completa |Sincronização |X |Idioma |
| **Idioma**: QWERTY CHS - pinyin duplo |Sincronização |X |Idioma |
| **Idioma**: CHS QWERTY - ler correção automática |Sincronização |X |Idioma |
| **Idioma**: CHS QWERTY - C/I comutador chave, shift |Sincronização |X |Idioma |
| **Idioma**: QWERTY CHS - C/I comutador chave, Ctrl |Sincronização |X |Idioma |
| **Idioma**: CHS WUBI - modo de entrada de carácter único |Sincronização |X |Idioma |
| **Idioma**: CHS WUBI - Mostrar o restante codificação dos candidatos |Sincronização |X |Idioma |
| **Idioma**: CHS WUBI - aviso sonoro quando a codificação de 4 é inválido |Sincronização |X |Idioma |
| **Idioma**: CHT Bopomofo - incluem CJK Ext-A |Sincronização |X |Idioma |
| **Idioma**: IME japonês - preditiva palavras escrevendo e personalizadas |Sincronização |Sincronização |Idioma |
| **Idioma**: coreano (KOR) IME |X |X |Idioma |
| **Idioma**: reconhecimento de escrita manual |X |X |Idioma |
| **Idioma**: perfil de idioma |Sincronização |cópia de segurança |Idioma |
| **Idioma**: spellcheck - misspellings autocorrect e realce |Sincronização |cópia de segurança |Idioma |
| **Idioma**: lista de teclados |Sincronização |cópia de segurança |Idioma |
| **Bloquear o ecrã**: todas as definições do ecrã de bloqueio |X |X | |
| **Lupa**: ou desativar (Ativar/desativar principal) |X |X |Facilidade de acesso |
| **Lupa**: Ativar ou desativar a cor de inversion (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Lupa**: controlo - siga o foco do teclado |Sincronização |X |Facilidade de acesso |
| **Lupa**: controlo - siga o cursor do rato |Sincronização |X |Facilidade de acesso |
| **Lupa**: iniciar quando os utilizadores iniciarem sessão (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Rato**: Altere o tamanho do cursor do rato |Sincronização |X |outro |
| **Rato**: alterar a cor do cursor do rato |Sincronização |X |outro |
| **Rato**: todas as outras definições |X |X | |
| **O Narrador**: início rápido |Sincronização |X |Facilidade de acesso |
| **O Narrador**: os utilizadores podem alterar o Narrador falando rápida |Sincronização |X |Facilidade de acesso |
| **O Narrador**: os utilizadores podem ativar ou desativar a Narrador ao ler as sugestões de itens comuns (por predefinição) |Sincronização |X |Facilidade de acesso |
| **O Narrador**: os utilizadores podem ativar ou desativar se estes ouvir os carateres escritos (em por predefinição) |Sincronização |X |Facilidade de acesso |
| **O Narrador**: os utilizadores podem ativar ou desativar se estes ouvir os palavras escritas (em por predefinição) |Sincronização |X |Facilidade de acesso |
| **O Narrador**: ter cursor de inserção seguir Narrador (por predefinição) |Sincronização |X |Facilidade de acesso |
| **O Narrador**: Ativar o realce visual de cursor Narrador (por predefinição) |Sincronização |X |Facilidade de acesso |
| **O Narrador**: reproduzir ajudas de áudio (por predefinição) |Sincronização |X |Facilidade de acesso |
| **O Narrador**: Ativar chaves no teclado touch quando a comparação de precisão sua mas (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Facilidade de acesso**: definir a espessura do cursor intermitente |Sincronização |X |Facilidade de acesso |
| **Facilidade de acesso**: remover imagens em segundo plano (desativado por predefinição) |Sincronização |X |Facilidade de acesso |
| **Capacidade e a suspensão**: todas as definições |X |X | |
| **Iniciar personalização ecrã**: acentos cor (apenas telemóvel) |X |Sincronização |Tema |
| **Escrever**: dicionário ortografia |Sincronização |cópia de segurança |Idioma |
| **Escrever**: autocorrect mal escritos word |Sincronização |cópia de segurança |Idioma |
| **Escrever**: realce palavras com erros ortográficos |Sincronização |cópia de segurança |Idioma |
| **Escrever**: Mostrar sugestões de texto, como posso de tipo |Sincronização |cópia de segurança |Idioma |
| **Escrever**: adicionar um espaço depois devo escolher uma sugestão de texto |Sincronização |cópia de segurança |Idioma |
| **Escrever**: adicionar um período após posso double-toque a barra de espaço |Sincronização |cópia de segurança |Idioma |
| **Escrever**: capitalize a primeira letra de cada frase |Sincronização |cópia de segurança |Idioma |
| **Escrever**: utilizar todas as letras maiúsculas quando posso double-toque tecla shift |Sincronização |cópia de segurança |Idioma |
| **Escrever**: reproduzir sons chaves como posso de tipo |Sincronização |cópia de segurança |Idioma |
| **Escrever**: dados de personalização de touch teclado |Sincronização |cópia de segurança |Idioma |
| **Wi-Fi**: perfis de Wi-Fi (apenas WPA) |Sincronização |Sincronização |Palavras-passe |

###### <a name="footnote-1"></a>Nota de rodapé 1
Versão de SO de criadores de Windows Update (criar 15063) mínima suportada. 

## <a name="related-topics"></a>Tópicos relacionados
* [Descrição geral de roaming de estado do Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
* [Ativar roaming no Azure Active Directory de estado empresarial](active-directory-windows-enterprise-state-roaming-enable.md)
* [As definições e roaming FAQ de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Política de grupo e definições de MDM para sincronização de definições](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
