---
title: Visão geral dos Serviços de Mídia v3 do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível dos Serviços de Mídia e fornece links para artigos com mais detalhes.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c6359cf2401ff198b0242243dbf6dfdf2e35ce47
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244032"
---
# <a name="azure-media-services-v3-overview"></a>Visão geral dos Serviços de Mídia do Azure v3

Os Serviços de Mídia do Azure são uma plataforma baseada em nuvem que permite a criação de soluções que apresentem uma transmissão de vídeo com qualidade de difusão, aumentem a acessibilidade e a distribuição, analisem o conteúdo e muito mais. Se você for um desenvolvedor de aplicativos, call center, órgão governamental, empresa de entretenimento, os Serviços de Mídia podem ajudar a criar aplicativos que oferecem experiências de mídia de qualidade superior para um público amplo nos dispositivos móveis e navegadores mais populares da atualidade. 

Os SDKs dos Serviços de Mídia v3 são baseados na [Especificação de OpenAPI dos Serviços de Mídia v3 (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) com suporte.

## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Mídia?

Os Serviços de Mídia permitem criar uma série de fluxos de trabalho de mídia na nuvem. Veja a seguir alguns exemplos do que pode ser feito com os Serviços de Mídia.  

* Fornecer vídeos em vários formatos, para que eles possam ser reproduzidos em uma série de navegadores e dispositivos. Para a entrega de vídeos sob demanda e transmissões ao vivo a vários clientes (dispositivos móveis, TV, PC, etc.) o conteúdo de vídeo e áudio precisa ser codificado e entregue de forma apropriada. Para ver como entregar e transmitir esse conteúdo, confira [Início Rápido: Codificar e transmitir arquivos](stream-files-dotnet-quickstart.md).
* Transmita eventos esportivos ao vivo para um público amplo online, como partidas de futebol, basebol, esportes universitários e escolares, e muito mais. 
* Transmita reuniões públicas e eventos como fóruns, reuniões da câmara de vereadores e órgãos legislativos.
* Analise o conteúdo do áudio ou vídeo gravado. Por exemplo, para alcançar maior satisfação dos clientes, as organizações podem extrair a conversão de fala em texto e criar índices de pesquisa e painéis. Em seguida, elas podem extrair dados inteligentes sobre reclamações comuns, fontes de reclamações e outros dados relevantes.
* Crie um serviço de assinatura de vídeo e transmita conteúdo protegido por DRM quando um cliente (por exemplo, um estúdio de cinema) precisar restringir o acesso e uso de um trabalho protegido por direitos autorais.
* Forneça conteúdo offline para reprodução em aviões, trens e automóveis. Talvez um cliente precise baixar o conteúdo no telefone ou tablet para reprodução quando ele estiver desconectado da rede.
* Implemente uma plataforma de vídeo educacional de aprendizado eletrônico com os Serviços de Mídia do Azure e a [API de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para fazer a conversão de fala em texto de legenda, traduzir para vários idiomas, etc. 
* Use os Serviços de Mídia do Azure junto com a [API de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para adicionar subtítulos e legendas a vídeos para abranger um público mais amplo (por exemplo, pessoas com deficiência auditiva ou que desejam acompanhar a leitura em um idioma diferente).
* Habilite a CDN do Azure para conseguir um dimensionamento grande para lidar melhor com altas cargas instantâneas (por exemplo, o início de um evento de lançamento de produto). 

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a v3? 

Saiba como codificar e empacotar conteúdo, transmitir vídeos sob demanda por streaming, difundir ao vivo e analisar seus vídeos com os Serviços de Mídia v3. Tutoriais, referências de API e outras documentações mostram como oferecer com segurança, sob demanda e ao vivo, fluxos de áudio ou vídeos que são dimensionados para milhões de usuários.

> [!TIP]
> Antes de iniciar o desenvolvimento, examine:<br/>* [Conceitos fundamentais](concepts-overview.md) (inclui conceitos importantes: empacotamento, codificação, proteção etc.)<br/>* [Desenvolvimento com APIs dos Serviços de Mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura etc.)

### <a name="quickstarts"></a>Guia de início rápido  

Os inícios rápidos mostram instruções iniciais básicas para que os novos clientes experimentem os Serviços de Mídia rapidamente.

* [Transmitir arquivos de vídeo – .NET](stream-files-dotnet-quickstart.md)
* [Transmitir arquivos de vídeo – CLI](stream-files-cli-quickstart.md)
* [Transmitir arquivos de vídeo – Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Tutoriais 

Os tutoriais mostram os procedimentos baseados em cenário para algumas das principais tarefas dos Serviços de Mídia.

* [Codificar o arquivo remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o arquivo carregado e o fluxo de vídeo – .NET](stream-files-tutorial-with-api.md)
* [Transmitir ao vivo – .NET](stream-live-tutorial-with-api.md)
* [Analisar vídeo – .NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128 – .NET](protect-with-aes128.md)
    
### <a name="samples"></a>Exemplos

Use [este navegador de exemplo](https://docs.microsoft.com/samples/browse/?products=azure-media-services) para procurar exemplos de código dos Serviços de Mídia do Azure.

### <a name="how-to-guides"></a>Guias de instruções

Os artigos contêm exemplos de código que demonstram como concluir uma tarefa. Nesta seção, você encontrará muitos exemplos, aqui estão apenas alguns deles:

* [Criar uma conta – CLI](create-account-cli-how-to.md)
* [Acessar as APIs – CLI](access-api-cli-how-to.md)
* [Codificar com HTTPS como entrada de trabalho – .NET](job-input-from-http-how-to.md)  
* [Monitorar eventos – Portal](monitor-events-portal-how-to.md)
* [Criptografar dinamicamente com vários DRM – .NET](protect-with-drm.md) 
* [Como codificar com uma transformação personalizada – CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre conceitos fundamentais](concepts-overview.md)

