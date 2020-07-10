---
title: 'Tutorial: Iniciar a Leitura Avançada usando os exemplos de código do Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai configurar e executar um aplicativo de exemplo do Android que inicia a Leitura Avançada.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d847b4ab9f3c06634390e1f67dfed36df938c3ad
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049295"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-android-java-code-sample"></a>Tutorial: Iniciar a Leitura Avançada usando o exemplo de código Java para o Android

Na [visão geral](./overview.md), você aprendeu sobre o que é a Leitura Avançada e como ela implementa técnicas comprovadas para melhorar a compreensão de leitura para aprendizes do idioma, leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial aborda a criação de um aplicativo do Android que inicia a Leitura Avançada. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar e executar um aplicativo para o Android usando um projeto de exemplo
> * Adquirir um token de acesso
> * Iniciar a Leitura Avançada com o conteúdo de exemplo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Git](https://git-scm.com/)
* [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Configurar as credenciais de autenticação

1. Inicie o Android Studio e abra o projeto por meio do diretório **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) ou do diretório **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

2. Crie um arquivo chamado **env** na pasta **/assets** e adicione o trecho a seguir, fornecendo valores conforme apropriado. Certifique-se de não confirmar esse arquivo no controle do código-fonte, uma vez que ele contém segredos que não devem ser tornados públicos.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET=<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

1. Escolha um emulador de dispositivo no Gerenciador de AVD e execute o projeto.

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)
* Visualize exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)