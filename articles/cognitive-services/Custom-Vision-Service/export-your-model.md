---
title: Exporte seu modelo para celular - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como exportar seu modelo para uso na criação de aplicativos móveis ou executado localmente para classificação em tempo real.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718957"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportar seu modelo para uso com dispositivos móveis

O Serviço de Visão Personalizada permite que os classificadores sejam exportados para execução offline. É possível inserir o classificador exportado para um aplicativo e executá-lo localmente em um dispositivo para classificação em tempo real.

## <a name="export-options"></a>Opções de exportação

O Serviço de Visão Personalizada dá suporte às seguintes exportações:

* __Tensorflow__ para __Android__.
* __CoreML__ para __iOS11__.
* __ONNX__ para __Windows ML__.
* __[Kit de desenvolvedor de IA Vision](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* Um __contêiner Docker__ para arquitetura Windows, Linux ou ARM. O contêiner inclui um modelo tensorflow e um código de serviço para usar a API de visão personalizada.

> [!IMPORTANT]
> O Serviço de Visão Personalizada exporta apenas domínios __compactos__. Os modelos gerados por domínios compactos são otimizados para as restrições de classificação em tempo real em dispositivos móveis. Classificadores compilados com um domínio compacto podem ser ligeiramente menos precisos que um domínio padrão com a mesma quantidade de dados de treinamento.
>
> Para obter informações sobre como aprimorar os classificadores, consulte o documento [Aprimorar o classificador](getting-started-improving-your-classifier.md).

## <a name="convert-to-a-compact-domain"></a>Converter para um domínio compacto

> [!NOTE]
> As etapas nesta seção só se aplicam se você tiver um modelo existente que não esteja definido como domínio compacto.

Para converter o domínio de um modelo existente, tome as seguintes etapas:

1. No [site visão personalizada,](https://customvision.ai)selecione o ícone __Home__ para exibir uma lista de seus projetos.

    ![Imagem do ícone de página inicial e lista de projetos](./media/export-your-model/projects-list.png)

1. Selecione um projeto e, em seguida, selecione o ícone de __Engrenagem__ no canto superior direito da página.

    ![Imagem do ícone de engrenagem](./media/export-your-model/gear-icon.png)

1. Na seção __Domínios,__ selecione um dos domínios __compactos.__ Selecione __Salvar Alterações__ para salvar as alterações. 

    > [!NOTE]
    > Para o Vision AI Dev Kit, o projeto deve ser criado com o domínio __Geral (Compact)__ e você deve especificar a opção **Kit de desenvolvimento do Vision AI** na seção **Capacidades de exportação.**

    ![Imagem da seleção de domínios](./media/export-your-model/domains.png)

1. Na parte superior da página, selecione __Treinar__ para treinar novamente usando o novo domínio.

## <a name="export-your-model"></a>Exportar seu modelo

Para exportar o modelo após o retreinamento, use as seguintes etapas:

1. Acesse a guia **Desempenho** e selecione __Exportar__. 

    ![Imagem do ícone de exportação](./media/export-your-model/export.png)

    > [!TIP]
    > Se a entrada __Exportar__ não estiver disponível, a iteração selecionada não utilizará um domínio compacto. Use a seção __Iterações__ desta página para selecionar uma iteração que usa um domínio compacto e, em seguida, selecione __Exportar__.

1. Selecione o formato de exportação desejado e selecione __Exportar__ para baixar o modelo.

## <a name="next-steps"></a>Próximas etapas

Integre seu modelo exportado em um aplicativo explorando um dos seguintes artigos ou amostras:

* [Use seu modelo Tensorflow com Python](export-model-python.md)
* [Use seu modelo ONNX com o Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Consulte a amostra para [o modelo CoreML em um aplicativo iOS](https://go.microsoft.com/fwlink/?linkid=857726) para classificação de imagem em tempo real com swift.
* Veja a amostra do [modelo Tensorflow em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para classificação de imagem em tempo real no Android.
* Consulte a amostra para [o modelo CoreML com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para classificação de imagem em tempo real em um aplicativo IOS Xamarin.
