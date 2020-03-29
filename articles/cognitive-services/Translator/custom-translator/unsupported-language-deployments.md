---
title: Implantações de idiomas sem suporte - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como implantar pares de idiomas sem suporte no Azure Cognitive Services Custom Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837447"
---
# <a name="unsupported-language-deployments"></a>Implantações de linguagem sem suporte

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Com a próxima aposentadoria do Microsoft Translator Hub, a Microsoft estará desimplantando todos os modelos atualmente implantados através do Hub. Muitos de vocês têm modelos implantados no Hub cujos pares de idiomas não são suportados no Personal Translator.  Não queremos que os usuários nesta situação não tenham recurso para traduzir seu conteúdo.

Agora temos um processo que permite que você implante seus modelos sem suporte através do Tradutor Personalizado.  Esse processo permite que você continue a traduzir conteúdo usando a API V3 mais recente.  Esses modelos serão hospedados até que você escolha desimplantá-los ou o par de idiomas esteja disponível no Tradutor Personalizado.  Este artigo explica o processo de implantação de modelos com pares de idiomas sem suporte.

## <a name="prerequisites"></a>Pré-requisitos

Para que seus modelos sejam candidatos à implantação, eles devem atender aos seguintes critérios:
* O projeto contendo o modelo deve ter sido migrado do Hub para o Tradutor Personalizado usando a Ferramenta de Migração.  O processo de migração de projetos e espaços de trabalho pode ser encontrado [aqui.](how-to-migrate.md)
* O modelo deve estar no estado implantado quando a migração acontecer.  
* O par de idiomas do modelo deve ser um par de idiomas sem suporte no Personal Translator.  Pares de idiomas nos quais um idioma é suportado para ou a partir do inglês, mas o par em si não inclui inglês, são candidatos para implantações de idiomas sem suporte.  Por exemplo, um modelo Hub para um par de idiomas francês para alemão é considerado um par de idiomas sem suporte, embora o francês para o inglês e o inglês para o alemão sejam suportados par de idiomas.

## <a name="process"></a>Processo
Depois de migrar modelos do Hub que são candidatos à implantação, você pode encontrá-los indo para a página **Configurações** para o seu espaço de trabalho e rolando até o final da página onde você verá uma seção **Treinamentos do Centro de Tradutor Não Suportado.**  Esta seção só aparece se você tiver projetos que atendam aos pré-requisitos mencionados acima.

![Como migrar do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Na página de seleção Treinamentos do Centro de **Tradutor Não Suportado,** a guia **Treinamentos não solicitados** contém modelos elegíveis para implantação.  Selecione os modelos que deseja implantar e envie uma solicitação.   Antes do prazo de implantação de 30 de abril, você pode selecionar quantos modelos desejar para implantação.
 
![Como migrar do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Uma vez enviado, o modelo não estará mais disponível na guia **treinamentos não solicitados** e, em vez disso, aparecerá na guia **Treinamentos Solicitados.**  Você pode ver seus treinamentos solicitados a qualquer momento.

![Como migrar do Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>O que vem a seguir?

Os modelos selecionados para implantação são salvos assim que o Hub for desativado e todos os modelos não forem implantados.  Você tem até 24 de maio para enviar solicitações de implantação de modelos sem suporte.  Implantaremos esses modelos em 15 de junho, quando eles estarão acessíveis através da API Tradutora V3.  Além disso, eles estarão disponíveis através da API V2 até 1º de julho.  

Para mais informações sobre datas importantes na depreciação do Hub verifique [aqui](https://www.microsoft.com/translator/business/hub/).
Uma vez implantadas, serão aplicadas as taxas normais de hospedagem.  Veja [os preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para mais detalhes.  

Ao contrário dos modelos padrão de Personal Translator, os modelos Hub só estarão disponíveis em uma única região, de modo que as taxas de hospedagem de várias regiões não se aplicarão.  Uma vez implantado, você poderá desimplantar seu modelo de Hub a qualquer momento através do projeto De tradutor personalizado migrado.

## <a name="next-steps"></a>Próximas etapas

- [Treine um modelo.](how-to-train-model.md)
- Comece a usar o modelo de tradução personalizado implantado por meio da [API de Tradução de Texto V3 da Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
