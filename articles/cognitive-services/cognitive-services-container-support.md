---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como os contêineres do Docker podem tornar os Serviços Cognitivos mais próximos de seus dados.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219407"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte de contêiner nos Serviços Cognitivos do Azure

O suporte a contêineres no Azure Cognitive Services permite que os desenvolvedores usem as mesmas APIs ricas disponíveis no Azure e permite flexibilidade em onde implantar e hospedar os serviços que vêm com [contêineres Docker](https://www.docker.com/what-container). O suporte a contêineres está atualmente disponível para um subconjunto de Serviços Cognitivos Azure, incluindo partes de:

> [!div class="checklist"]
> * [Detector de Anomalias][ad-containers]
> * [Pesquisa Visual Computacional][cv-containers]
> * [Face][fa-containers]
> * [Reconhecimento de Formulários][fr-containers]
> * [Reconhecimento Vocal (LUIS)][lu-containers]
> * [API do Serviço de Fala][sp-containers]
> * [Análise de texto][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

O uso de contêineres é uma abordagem para distribuição de software em que um aplicativo ou serviço, incluindo as dependências e configurações, é empacotado como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres poderão ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos, quando não forem mais necessários.

Os recursos dos Serviços Cognitivos estão disponíveis no [Microsoft Azure](https://azure.microsoft.com). Entre no [portal do Azure](https://portal.azure.com/) para criar e explorar recursos do Azure desses serviços.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infra-estrutura imutável**: Habilite as equipes de DevOps para aproveitar um conjunto consistente e confiável de parâmetros conhecidos do sistema, ao mesmo tempo em que é capaz de se adaptar às mudanças. Os contêineres fornecem a flexibilidade para girar dentro de um ecossistema previsível e evitar a deriva de configuração.
- **Controle sobre dados**: Permita que os clientes escolham onde esses Serviços Cognitivos processam seus dados. Isso é essencial para clientes que não podem enviar dados para a nuvem, mas precisam acessar a tecnologia de Serviços Cognitivos. Suporte para consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelos**: fornecer aos clientes flexibilidade na versão e atualização dos modelos implantados nas soluções.
- **Arquitetura portátil**: Habilite a criação de uma arquitetura de aplicativo portátil que pode ser implantada no Azure, no local e na borda. Os contêineres podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml), nas [Instâncias de Contêiner do Azure](../container-instances/index.yml) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de produção / baixa latência**: Forneça aos clientes a capacidade de escalar para requisitos de alta taxa de execução e baixa latência, permitindo que os Serviços Cognitivos sejam executados fisicamente perto de sua lógica de aplicação e dados. Os contêineres não limitam as transações por segundo (TPS) e poderão ser escalados vertical e horizontalmente para lidar com a demanda se você fornecer os recursos de hardware necessários.
- **Escalabilidade**: Com a crescente popularidade do software de orquestração de contêineres e contêineres, como kubernetes; a escalabilidade está na vanguarda dos avanços tecnológicos. Com base em uma fundação de cluster escalável, o desenvolvimento de aplicativos atende à alta disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure fornecem o seguinte conjunto de contêineres do Docker, cada um contendo um subconjunto de funcionalidades dos serviços dos Serviços Cognitivos do Azure:

| Serviço | Nível de preços suportado | Contêiner | Descrição |
|---------|----------|----------|-------------|
|[Detector de anomalias][ad-containers] |F0, S0|**Detector de anomalias** |A API do Detector de Anomalias permite monitorar e detectar anormalidades em seus dados de série temporal com aprendizado de máquina.<br>[Solicitar acesso](https://aka.ms/adcontainer)|
|[Pesquisa Visual Computacional][cv-containers] |F0, S1|**Ler** |Extrai texto impresso de imagens de vários objetos com diferentes superfícies e planos de fundo como recibos, pôsters e cartões de visita. O contêiner Ler também *detecta texto manuscrito* em imagens e fornece suporte a PDF/TIFF/multipáginas.<br/><br/>**Importante:** O contêiner Read atualmente funciona apenas com inglês.|
|[Face][fa-containers] |F0, S0|**Face** |Detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pelo computador. Além de detectar, a Detecção Facial pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança, ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados.<br>[Solicitar acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Reconhecimento de formulário][fr-containers] |F0, S0|**Reconhecimento de Formulários** |O Form Understanding aplica a tecnologia de aprendizado de máquina para identificar e extrair pares e tabelas de valor-chave de formulários.<br>[Solicitar acesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[Luis][lu-containers] |F0, S0|**LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Carrega um modelo de reconhecimento vocal treinado ou publicado, também conhecido como um aplicativo LUIS, para um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade da API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los novamente no [portal do LUIS](https://www.luis.ai) para aumentar a precisão da previsão do aplicativo.|
|[API do Serviço de Fala][sp-containers-stt] |F0, S0|**Fala-a-texto** |Transcreve fala em tempo real contínua em texto.|
|[API do Serviço de Fala][sp-containers-cstt] |F0, S0|**Discurso personalizado para texto** |Transcreve a fala contínua em tempo real em texto usando um modelo personalizado.|
|[API do Serviço de Fala][sp-containers-tts] |F0, S0|**Texto-para-fala** |Converte o texto em fala que soa natural.|
|[API do Serviço de Fala][sp-containers-ctts] |F0, S0|**Texto-para-fala personalizado** |Converte texto em fala natural usando um modelo personalizado.|
|[Análise de texto][ta-containers-keyphrase] |F0|**Extração de Frases-Chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa". |
|[Análise de texto][ta-containers-language]|F0|**Detecção de Idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Para até 120 idiomas, detecta em qual idioma o texto de entrada está escrito e informa um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. |
|[Análise de texto][ta-containers-sentiment]|F0|**Análise de Sentimento** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analisa o texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Essa API retorna uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é a mais positiva. Os modelos de análise são pré-treinados usando um amplo corpo de texto e tecnologias de idioma natural da Microsoft. Para [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e pontuar qualquer texto bruto que você forneça, retornando diretamente os resultados ao aplicativo responsável pela chamada. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns contêineres são suportados em Serviços Cognitivos [**All-In-One oferecendo**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) chaves de recursos. Você pode criar um único recurso All-In-One de serviços cognitivos e usar a mesma chave de cobrança entre os serviços suportados para os seguintes serviços:

* Visual Computacional
* Face
* LUIS
* Análise de texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contêiner nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure estão disponíveis publicamente por meio da assinatura do Azure e as imagens do contêiner do Docker podem ser obtidas do Registro de Contêiner a Microsoft ou Hub do Docker. É possível usar o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro apropriado.

> [!IMPORTANT]
> Atualmente, você deve preencher um processo de inscrição para acessar os seguintes contêineres, nos quais você preenche e envia um questionário com perguntas sobre você, sua empresa e o caso de uso para o qual deseja implementar os contêineres. Uma vez que você tenha acesso e credenciais fornecidas, você pode então retirar as imagens do contêiner de um registro privado de contêiner hospedado pelo Registro de Contêineres do Azure.
> * [Detector de anomalias](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Face](Face/face-how-to-install-containers.md)
> * [Reconhecimento de Formulários](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Ler](computer-vision/computer-vision-how-to-install-containers.md)
> * [Discurso-a-texto e texto-para-fala](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Pré-requisitos

É necessário atender aos seguintes pré-requisitos antes de usar contêineres dos Serviços Cognitivos do Azure:

**Mecanismo do Docker**: é necessário ter o Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker em [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para dar suporte a contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml) ou [Instâncias de Contêiner do Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure.

**Familiaridade com Docker e Registro de Contêiner da Microsoft**: é necessário ter uma compreensão básica de ambos os conceitos de Docker e Registro de Contêiner da Microsoft, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos `docker`.

Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contêineres individuais podem ter seus próprios requisitos, incluindo requisitos de alocação de memória e servidor.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Próximas etapas

Conheça as [receitas de contêineres que](containers/container-reuse-recipe.md) você pode usar com os Serviços Cognitivos.

Instale e explore a funcionalidade fornecida pelos contêineres nos Serviços Cognitivos do Azure:

* [Recipientes do Detector de Anomalias][ad-containers]
* [Contêineres de Visão computacional][cv-containers]
* [Recipientes faciais][fa-containers]
* [Recipientes de reconhecimento de formulário][fr-containers]
* [Contêineres de Compreensão da Linguagem (LUIS)][lu-containers]
* [Contêineres aPI do Serviço de Fala][sp-containers]
* [Contêineres de análise de texto][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment