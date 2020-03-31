---
title: Mensagens XML e arquivos planos
description: Processe, valide e transforme mensagens XML em aplicativos azure logic com pacote de integração corporativa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792141"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Mensagens XML e arquivos simples nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Nos [Aplicativos Azure Logic,](logic-apps-overview.md)você pode processar mensagens XML que você envia e recebe usando o Pacote de Integração Corporativa. Se você já usou o BizTalk Server, o Pacote de Integração Corporativa fornece recursos semelhantes para transformar e validar mensagens, trabalhar com arquivos planos e até mesmo usar o XPath para enriquecer ou extrair propriedades específicas de uma mensagem. Se você é novo neste espaço, esses recursos expandem a forma como você processa mensagens no fluxo de trabalho do seu aplicativo lógico. Por exemplo, se você tem um cenário b2B (business-to-business) e trabalha com schemas XML específicos, você pode usar o Pacote de Integração Corporativa para melhorar a forma como sua empresa processa essas mensagens.

Por exemplo, o Pacote de Integração Corporativa inclui esses recursos:

* [Validação XML](logic-apps-enterprise-integration-xml-validation.md): Valide uma mensagem XML de entrada ou saída contra um esquema específico.

* [Transformação XML](logic-apps-enterprise-integration-transform.md): Converter ou personalizar uma mensagem XML com base em suas necessidades ou nos requisitos de um parceiro usando mapas.

* [Codificação de arquivos planos e decodificação de arquivos planos:](logic-apps-enterprise-integration-flatfile.md)Codificar ou decodificar um arquivo plano.

  Por exemplo, o SAP aceita e envia arquivos IDOC no formato de arquivo simples. Muitas plataformas de integração criam mensagens XML, incluindo Aplicativos Lógicos. Portanto, você pode criar um aplicativo lógico que use o codificador de arquivo simples "converter" arquivos XML em arquivos simples.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Enriqueça uma mensagem e extraia propriedades específicas da mensagem. Então, será possível usar as propriedades extraídas para rotear a mensagem a um destino ou a um ponto de extremidade intermediário.

## <a name="sample"></a>Amostra

[Implante um aplicativo de lógica totalmente operacional](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (amostra GitHub) usando os recursos XML em Aplicativos de Lógica Do Azure.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md)
