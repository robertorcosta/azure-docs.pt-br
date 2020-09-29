---
title: Usar contêineres de serviços cognitivas do Azure local
titleSuffix: Azure Cognitive Services
description: Saiba como usar contêineres do Docker para usar serviços cognitivas locais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 09/28/2020
ms.author: aahi
keywords: local, Docker, contêiner, kubernetes
ms.openlocfilehash: 48bfad4b101556dfcc4e57cf684341bda8063202
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461199"
---
# <a name="azure-cognitive-services-containers"></a>Contêineres de serviços cognitivas do Azure

> [!WARNING]
> Em 11 de junho de 2020, a Microsoft comunicou que não venderá tecnologia de reconhecimento facial a departamentos de polícia nos Estados Unidos até que um sólido regulamento, com base nos direitos humanos, seja aprovado. Desse modo, os clientes não poderão usar os recursos de reconhecimento facial ou a funcionalidade incluída nos Serviços do Azure, como Detecção Facial ou Video Indexer, se forem um departamento de polícia nos Estados Unidos ou permitirem o uso de desses serviços para e pela polícia.

Os serviços cognitivas do Azure fornecem vários [contêineres do Docker](https://www.docker.com/what-container) que permitem usar as mesmas APIs que estão disponíveis no Azure, no local. O uso desses contêineres oferece a flexibilidade de trazer serviços cognitivas mais perto de seus dados para fins de conformidade, segurança ou outras razões operacionais. 

Atualmente, o suporte a contêineres está disponível para um subconjunto de serviços cognitivas do Azure, incluindo partes do:

> [!div class="checklist"]
> * [Detector de Anomalias][ad-containers]
> * [Pesquisa Visual Computacional][cv-containers]
> * [Detecção Facial][fa-containers]
> * [Reconhecimento de Formulários][fr-containers]
> * [Reconhecimento Vocal (LUIS)][lu-containers]
> * [API do Serviço de Fala][sp-containers]
> * [Análise de Texto][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

O uso de contêineres é uma abordagem para distribuição de software em que um aplicativo ou serviço, incluindo as dependências e configurações, é empacotado como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres poderão ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos, quando não forem mais necessários.

Os recursos de serviços cognitivas estão disponíveis em [Microsoft Azure](https://azure.microsoft.com). Entre no [portal do Azure](https://portal.azure.com/) para criar e explorar recursos do Azure desses serviços.

## <a name="features-and-benefits"></a>Características e benefícios

- **Infraestrutura imutável**: habilite as equipes do DevOps para aproveitar um conjunto consistente e confiável de parâmetros de sistema conhecidos, além de ser capaz de se adaptar a alterações. Os contêineres fornecem a flexibilidade para dinamizar em um ecossistema previsível e evitar descompassos de configuração.
- **Controle sobre os dados**: escolha onde seus dados são processados por serviços cognitivas. Isso pode ser essencial se você não puder enviar dados para a nuvem, mas precisar de acesso a API de Serviços Cognitivos. Suporte para consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelo**: flexibilidade no controle de versão e atualização de modelos implantados em suas soluções.
- **Arquitetura portátil**: habilita a criação de uma arquitetura de aplicativo portátil que pode ser implantada no Azure, no local e na borda. Os contêineres podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml), nas [Instâncias de Contêiner do Azure](../container-instances/index.yml) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de transferência/baixa latência**: forneça aos clientes a capacidade de dimensionar para alta taxa de transferência e requisitos de baixa latência, permitindo que os serviços cognitivas sejam executados fisicamente em seus dados e lógica do aplicativo. Os contêineres não limitam as transações por segundo (TPS) e poderão ser escalados vertical e horizontalmente para lidar com a demanda se você fornecer os recursos de hardware necessários.
- **Escalabilidade**: com a popularidade cada vez maior de contêineres e software de orquestração de contêiner, como kubernetes; a escalabilidade está no Forefront de avanços tecnológicos. Criando uma base de cluster escalonável, o desenvolvimento de aplicativos atende à alta disponibilidade.

## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure fornecem o seguinte conjunto de contêineres do Docker, cada um contendo um subconjunto de funcionalidades dos serviços dos Serviços Cognitivos do Azure:

| Serviço | Tipo de preço com suporte | Contêiner | Descrição |
|--|--|--|--|
| [Detector de anomalias][ad-containers] | F0, S0 | **Condetector de anomalias** ([imagem](https://hub.docker.com/_/azure-cognitive-services-decision-anomaly-detector))  | A API do Detector de Anomalias permite monitorar e detectar anormalidades em seus dados de série temporal com aprendizado de máquina.<br>[Solicitar acesso][request-access] |
| [Pesquisa Visual Computacional][cv-containers] | F0, S1 | **Ler** | Extrai texto impresso de imagens de vários objetos com diferentes superfícies e planos de fundo como recibos, pôsters e cartões de visita. O contêiner de leitura também detecta *texto manuscrito* em imagens e fornece suporte a PDF/TIFF/várias páginas.<br/><br/>**Importante:** O contêiner de leitura atualmente funciona apenas com o inglês. |
| [Detecção Facial][fa-containers] | F0, S0 | **Detecção Facial** | Detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pelo computador. Além de detectar, a Detecção Facial pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança, ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados.<br>[Solicitar acesso][request-access] |
| [Reconhecedor de formulário][fr-containers] | F0, S0 | **Reconhecimento de Formulários** | A compreensão de formulário aplica a tecnologia de aprendizado de máquina para identificar e extrair pares de chave-valor e tabelas de formulários.<br>[Solicitar acesso][request-access] |
| [LUIS][lu-containers] | F0, S0 | **LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Carrega um modelo de reconhecimento vocal treinado ou publicado, também conhecido como um aplicativo LUIS, para um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade da API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los novamente no [portal do LUIS](https://www.luis.ai) para aumentar a precisão da previsão do aplicativo. |
| [API do Serviço de Fala][sp-containers-stt] | F0, S0 | **Conversão de fala em texto** ([imagem](https://hub.docker.com/_/azure-cognitive-services-speechservices-speech-to-text)) | Transcreve fala em tempo real contínua em texto. |
| [API do Serviço de Fala][sp-containers-cstt] | F0, S0 | **Fala personalizada para texto** ([imagem](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcreve a fala contínua em tempo real em texto usando um modelo personalizado. |
| [API do Serviço de Fala][sp-containers-tts] | F0, S0 | **Conversão de texto em fala** ([imagem](https://hub.docker.com/_/azure-cognitive-services-speechservices-text-to-speech)) | Converte o texto em fala que soa natural. |
| [API do Serviço de Fala][sp-containers-ctts] | F0, S0 | **Conversão de texto em fala** ([imagem](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-text-to-speech)) personalizada | Converte o texto em uma fala de som natural usando um modelo personalizado. |
| [API do Serviço de Fala][sp-containers-ntts] | F0, S0 | **Conversão de texto em fala neural** ([imagem](https://hub.docker.com/_/azure-cognitive-services-speechservices-neural-text-to-speech)) | Converte o texto em voz natural usando uma tecnologia de rede neural profunda, permitindo uma fala mais natural sintetizada. |
| [Análise de Texto][ta-containers-keyphrase] | F0, S | **Extração de Frases-Chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa". |
| [Análise de Texto][ta-containers-language] | F0, S | **Detecção de Idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Para até 120 idiomas, detecta em qual idioma o texto de entrada está escrito e informa um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. |
| [Análise de Texto][ta-containers-sentiment] | F0, S | **Análise de sentimento v3** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analisa o texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Esta versão da análise de sentimentos retorna rótulos de sentimentos (por exemplo, *positivos* ou *negativos*) para cada documento e frase dentro dele. |
| [Análise de Texto][ta-containers-health] | F0, S | **Análise de Texto para integridade** | Extraia e rotule informações médicas de texto clínico não estruturado. |
| [Análise espacial][spa-containers] | S0 | **Análise espacial** | Extraia e rotule informações médicas de texto clínico não estruturado. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Além disso, alguns contêineres têm suporte em chaves de recurso de oferta de serviços cognitivas [**All-in-One**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Você pode criar um único recurso All-in-One de serviços cognitivas e usar a mesma chave de cobrança entre os serviços com suporte para os seguintes serviços:

* Visual Computacional
* Face
* LUIS
* Análise de texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contêiner nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure estão disponíveis publicamente por meio da assinatura do Azure e as imagens do contêiner do Docker podem ser obtidas do Registro de Contêiner a Microsoft ou Hub do Docker. É possível usar o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro apropriado.

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

Saiba mais sobre as [receitas de contêiner](containers/container-reuse-recipe.md) que você pode usar com os serviços cognitivas.

Instale e explore a funcionalidade fornecida pelos contêineres nos Serviços Cognitivos do Azure:

* [Contêineres de detector de anomalias][ad-containers]
* [Contêineres de Pesquisa Visual Computacional][cv-containers]
* [Contêineres de face][fa-containers]
* [Contêineres do reconhecedor de formulário][fr-containers]
* [Contêineres de Reconhecimento vocal (LUIS)][lu-containers]
* [Contêineres da API do serviço de fala][sp-containers]
* [Contêineres de Análise de Texto][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u
