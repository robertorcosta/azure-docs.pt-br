---
title: Licenciamento&reg; microsoft smooth streaming client porting kit
description: Saiba como licenciar&reg; o Kit de Portação de Clientes de Streaming Suave da Microsoft.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: bd77c53a195a9549f6aaad9ee2928206f5324b0c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686905"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licenciamento&reg; microsoft smooth streaming client porting kit 
## <a name="overview"></a>Visão geral
O Kit de Portabilidade de Cliente do Microsoft Smooth Streaming (**SSPK** ) é uma implementação de cliente do Smooth Streaming otimizada para ajudar fabricantes de dispositivos inseridos, operadoras de serviços móveis e a cabo, fornecedores de serviços de conteúdo, fabricantes de celulares, fornecedores de software independentes (ISVs) e fornecedores de soluções a criar produtos e serviços para transmitir conteúdo de streaming adaptativo no formato Smooth Streaming. O SSPK é uma implementação independente de dispositivo e de plataforma do cliente do Smooth Streaming que pode ser movida pelo licenciado para qualquer dispositivo e plataforma. 

A seguir, uma arquitetura de alto nível e a caixa do Kit de Portabilidade do IIS Smooth Streaming é a implementação do Cliente do Smooth Streaming fornecida pela Microsoft e inclui toda a lógica básica para a reprodução de conteúdo do Smooth Streaming. Em seguida, o conteúdo será transferido por parceiros para um dispositivo ou plataforma específicos, implementando interfaces apropriadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Descrição
O SSPK é licenciado em termos que oferecem excelente valor comercial. A licença do SSPK fornece ao setor:

* a fonte do Kit de Portabilidade de Smooth Streaming em C++ 
  * implementa a funcionalidade de Cliente do Smooth Streaming
  * adiciona a análise de formato, heurística, lógica de buffer etc.
* APIs do aplicativo Player 
  * interfaces de programação para interação com um aplicativo de player de mídia
* Interface da Camada de Abstração de Plataforma (PAL) 
  * interfaces de programação para interação com o sistema operacional (threads, sockets)
* Interface da Camada de Abstração de Hardware (HAL) 
  * interfaces de programação para interação com decodificadores A/V de hardware (decodificação, renderização)
* Interface de Gerenciamento de Direitos Digitais (DRM) 
  * interfaces de programação para tratamento de DRM por meio da Camada de Abstração de DRM (DAL)
  * O Kit de Portabilidade do Microsoft PlayReady é fornecido separadamente, mas se integra por meio dessa interface. Para saber mais sobre o licenciamento do Dispositivo Microsoft PlayReady, clique [aqui](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Amostras de implementação 
  * implementação de amostra de PAL para Linux
  * implementação de amostra de HAL para GStreamer

## <a name="licensing-options"></a>Opções de Licenciamento
O Kit de Portabilidade do cliente do Microsoft Smooth Streaming é disponibilizado para licenciados em dois contratos de licença distintas: um para o desenvolvimento de produtos provisórios de cliente de Smooth Streaming e outro para a distribuição de produtos finais de cliente de Smooth Streaming aos usuários finais.

* Para fabricantes de chipset, integradores de sistema ou fornecedores independentes de software (ISVs) que exijam um kit de portabilidade de código fonte para desenvolver produtos provisórios, uma **Licença do Produto Provisório** do Kit de Portabilidade de Cliente do Microsoft Smooth Streaming deve ser executada.
* Os fabricantes de dispositivos ou ISVs que exigem direitos de distribuição para Produtos Finais de Cliente de Smooth Streaming para usuários finais, a **Licença do Produto Final** do Kit de Portabilidade de Cliente do Microsoft Smooth Streaming deve ser executada.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Licença de Produto Provisório do Kit de Portabilidade de Cliente do Microsoft Smooth Streaming
Com esta licença, a Microsoft oferece um Kit de Portabilidade de Cliente do Smooth Streaming e os direitos de propriedade intelectual necessários para desenvolver e distribuir Produtos Provisórios de Cliente do Smooth Streaming para outros licenciados de dispositivos do Kit de Portabilidade de Cliente do Smooth Streaming que distribuem Produtos Finais de Cliente do Smooth Streaming.

#### <a name="fee-structure"></a>Estrutura de taxa
Uma taxa de licença única de US$ 50.000 fornece acesso ao Kit de Portabilidade de Cliente do Smooth Streaming. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licença de Produto Final do Kit de Portabilidade de Cliente do Microsoft Smooth Streaming
Nesta licença, a Microsoft oferece todos os direitos de propriedade intelectual necessários para receber Produtos Provisórios de Cliente do Smooth Streaming de outros licenciados de dispositivos do Kit de Portabilidade de Cliente do Smooth Streaming e para distribuir Produtos Finais de Cliente do Smooth Streaming com a marca da empresa a usuários finais.

#### <a name="fee-structure"></a>Estrutura de taxa
O Produto Final de Cliente do Smooth Streaming é oferecido em um modelo de royalty, como:

* US$ 0,10 por implementação de dispositivo fornecida
* O royalty é limitado a US$ 50.000 por ano
* Nenhum royalty para as primeiras 10.000 implementações de dispositivo por ano 

## <a name="licensing-procedure-and-sspk-access"></a>Procedimento de licenciamento e acesso do SSPK
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para todas as consultas de licenciamento.

O Portal de distribuição do SSPK pode ser acessado por licenciados provisórios registrados.

Os licenciados SSPK provisórios e [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)finais podem enviar perguntas técnicas para .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licenciados do Contrato do Produto provisório do cliente do Microsoft Smooth Streaming

* Adroit Business Solutions, Inc
* Advanced Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Purchasing Corporation
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licenciados do Contrato do Produto final do cliente do Microsoft Smooth Streaming
* Advanced Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Shenzhen
* Compal eletrônicos, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Purchasing Corporation
* Enseo, Inc.
* FilmFlex Filmes Limitados
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Orange SA
* Saffron Digital Limited
* Sagemcom Broadband SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Technicolor Delivery Technologies, SAS
* Tongfang Global Ltd.
* Top Victory Investments, Ltd.
* Toshiba Lifestyle Products & Services Corporation
* Universal Media Corporation /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

