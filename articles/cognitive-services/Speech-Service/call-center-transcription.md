---
title: Transcrição do Call Center - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Um cenário comum para o discurso-texto é transcrever grandes volumes de dados de telefonia que vêm de vários sistemas, como o Interactive Voice Response (IVR). Usando o serviço de fala e o modelo de fala unificada, uma empresa pode obter transcrições de alta qualidade com sistemas de captura de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806313"
---
# <a name="speech-service-for-telephony-data"></a>Serviço de fala para dados de telefonia

Normalmente, os dados de telefonia gerados por meio de telefones fixos, celulares e rádios são de baixa qualidade e de banda estreita no intervalo de 8 KHz, o que cria desafios durante a conversão de fala em texto. Os modelos mais recentes de reconhecimento de fala do serviço de fala se destacam em transcrever esses dados de telefonia, mesmo nos casos em que os dados são difíceis de entender por um humano. Esses modelos são treinados com grandes volumes de dados de telefonia, e têm a melhor precisão de reconhecimento do mercado, mesmo em ambientes barulhentos.

Um cenário comum para a conversão de fala em texto é a transcrição de grandes volumes de dados de telefonia que podem ser recebidos de vários sistemas, como o IVR (resposta interativa de voz). O áudio que desses sistemas fornecem pode ser estéreo ou mono e bruto com pouco ou nenhum pós-processamento feito no sinal. Usando o serviço speech e o modelo de fala unificada, uma empresa pode obter transcrições de alta qualidade, quaisquer que sejam os sistemas usados para capturar áudio.

Os dados de telefonia podem ser usados para compreender melhor as necessidades de seus clientes, identificar novas oportunidades de marketing ou avaliar o desempenho dos agentes de call center. Depois que os dados são transcritos, uma empresa pode usar a saída para fins como telemetria melhorada, identificar frases-chave ou analisar o sentimento do cliente.

As tecnologias descritas nesta página são usadas pela Microsoft internamente para vários serviços de processamento de chamadas de suporte, em tempo real e no modo de lote.

Vamos rever algumas das tecnologias e recursos relacionados que o serviço Speech oferece.

> [!IMPORTANT]
> O modelo de serviço de fala unificado é treinado com dados diversos e oferece uma solução de modelo único para uma série de cenários, do Ditado à Análise de Telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia do Azure para call centers

Além do aspecto funcional dos recursos do serviço de Fala, seu principal objetivo – quando aplicado ao call center – é melhorar a experiência do cliente. Há três domínios bem definidos a este respeito:

- Análise pós-chamada, que é essencialmente o processamento em lote de gravações de chamadas após a chamada.
- Análise em tempo real, que está processando o sinal de áudio para extrair vários insights à medida que a chamada está ocorrendo (com o sentimento sendo um caso de uso proeminente).
- Assistentes de voz (bots), seja impulsionando o diálogo entre o cliente e o bot na tentativa de resolver o problema do cliente sem participação do agente, seja sendo a aplicação de protocolos de inteligência artificial (IA) para auxiliar o agente.

Um diagrama da arquitetura típica da implementação de um cenário de lote é descrito na imagem abaixo ![Arquitetura de transcrição de call center](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Componentes da tecnologia de análise de fala

Seja o domínio pós-chamada ou em tempo real, o Azure oferece um conjunto de tecnologias maduras e emergentes para melhorar a experiência do cliente.

### <a name="speech-to-text-stt"></a>STT (conversão de fala em texto)

[O discurso-para-texto](speech-to-text.md) é o recurso mais procurado em qualquer solução de call center. Como muitos dos processos de análise a jusante dependem de texto transcrito, a taxa de erro da palavra (_WER)_ é de extrema importância. Um dos principais desafios na transcrição de call center é o ruído predominante no call center (por exemplo: outros agentes falando em segundo plano), a grande variedade de localidades de idioma e dialetos, bem como a baixa qualidade do sinal de telefone real. A WER está altamente correlacionada com o quão bem os modelos acústicos e linguísticos são treinados para um determinado local, portanto, a capacidade de personalizar o modelo para o seu local é importante. Nossos modelos Unificados mais recentes da versão 4.x são a solução para a precisão e a latência de transcrição. Treinados com dezenas de milhares de horas de dados acústicos e bilhões de informações léxicas, os modelos unificados são os modelos mais precisos do mercado para transcrever dados de call center.

### <a name="sentiment"></a>Sentimento

Avaliar se o cliente teve uma boa experiência é uma das áreas mais importantes da Análise de fala quando aplicado no espaço do call center. Nossa [API de Transcrição em Lote](batch-transcription.md) oferece a análise de sentimento por enunciado. Você pode agregar o conjunto de valores obtidos como parte de uma transcrição de chamada para determinar o sentimento da chamada para os agentes e o cliente.

### <a name="silence-non-talk"></a>Silêncio (sem conversa)

Não é incomum que 35% de uma chamada de suporte consista no que chamamos de tempo sem conversa. Alguns cenários para os quais ocorre o não-talk são: agentes que procuram histórico de casos anteriores com um cliente, agentes usando ferramentas que permitem que ele acesse a área de trabalho do cliente e execute funções, clientes sentados em espera esperando por uma transferência, e assim por diante. É extremamente importante avaliar quando o silêncio está ocorrendo em uma chamada, pois há uma série de sensibilidades importantes do cliente que ocorrem em torno desses tipos de cenários e onde eles ocorrem na chamada.

### <a name="translation"></a>Tradução

Algumas empresas estão experimentando fornecer transcrições traduzidas de chamadas de suporte a idiomas estrangeiros para que os gerentes de entrega possam entender a experiência mundial de seus clientes. Nossas funcionalidades de [tradução](translation.md) são incomparáveis. Podemos traduzir áudio para áudio ou áudio para texto para um grande número de localidades.

### <a name="text-to-speech"></a>Texto em fala

A [conversão de texto em fala](text-to-speech.md) é outra área importante na implementação de bots que interagem com os clientes. O caminho típico é o que o cliente fala, sua voz é transcrita em texto, o texto é analisado quanto às intenções, uma resposta é sintetizada com base na intenção reconhecida e, em seguida, um ativo é exposto ao cliente ou uma resposta de voz sintetizada é gerada. É claro que tudo isso tem que ocorrer rapidamente – portanto, a baixa latência é um componente importante no sucesso desses sistemas.

Nossa latência de ponta a ponta é consideravelmente baixa para as diversas tecnologias envolvidas, como [Fala-a-texto,](speech-to-text.md) [LUIS,](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) [Bot Framework,](https://dev.botframework.com/) [Texto-para-fala.](text-to-speech.md)

Nossas novas vozes também são praticamente idênticas às vozes humanas. Você pode usar nossas vozes para dar ao seu bot sua personalidade única.

### <a name="search"></a>Search

Outro ponto importante da análise é identificar as interações em que uma experiência ou um evento específico tenha ocorrido. Isso é tipicamente feito com uma das duas abordagens; ou uma pesquisa ad hoc onde o usuário simplesmente digita uma frase e o sistema responde, ou uma consulta mais estruturada onde um analista pode criar um conjunto de declarações lógicas que identificam um cenário em uma chamada e, em seguida, cada chamada pode ser indexada contra esse conjunto de consultas. Um bom exemplo de pesquisa é a declaração de conformidade onipresente "esta chamada deve ser registrada para fins de qualidade... ". Muitas empresas querem ter certeza de que seus agentes estão fornecendo essa isenção de responsabilidade aos clientes antes que a chamada seja realmente gravada. A maioria dos sistemas de análise tem a capacidade de tendência dos comportamentos encontrados por algoritmos de consulta/pesquisa, e esse relatório de tendências é, em última análise, uma das funções mais importantes de um sistema de análise. Por meio do [diretório de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/directory/search/), sua solução de ponta a ponta pode ser consideravelmente aprimorada com funcionalidades de indexação e pesquisa.

### <a name="key-phrase-extraction"></a>Extração de Frases-Chave

Esta área é uma das aplicações de análise mais desafiadoras e que está se beneficiando da aplicação de IA e machine learning. O cenário principal neste caso é inferir a intenção do cliente. Por que o cliente está ligando? Qual é o problema do cliente? Por que o cliente teve uma experiência negativa? Nosso serviço de análise de [texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornece um conjunto de análises fora da caixa para atualizar rapidamente sua solução completa para extrair essas palavras-chave ou frases importantes.

Agora vamos dar uma olhada no processamento em lotes e nos pipelines em tempo real do reconhecimento de fala um pouco mais detalhadamente.

## <a name="batch-transcription-of-call-center-data"></a>Transcrição em lote de dados do call center

Para transcrever áudio em massa desenvolvemos a [API de transcrição em lote](batch-transcription.md). A API de Transcrição em Lote foi desenvolvida para transcrever grandes quantidades de dados de áudio de forma assíncrona. No que diz respeito à transcrição dos dados do call center, nossa solução é baseada nesses pilares:

- **Precisão** - Com modelos unificados de quarta geração, oferecemos qualidade de transcrição insuperável.
- **Latência** - Entendemos que ao fazer transcrições em massa, as transcrições são necessárias rapidamente. Os trabalhos de transcrição iniciados por meio da [API de Transcrição em Lote](batch-transcription.md) serão colocados na fila imediatamente e, depois que o trabalho for executado, a transcrição será realizada mais rapidamente do que a transcrição em tempo real.
- **Segurança** - Entendemos que as chamadas podem conter dados confidenciais. Tenha certeza de que a segurança é uma das nossas maiores prioridades. Nosso serviço obteve as certificações ISO, SOC, HIPAA e PCI.

Os call centers geram grandes volumes de dados de áudio diariamente. Caso sua empresa armazene dados de telefonia em um local central, como o Armazenamento do Azure, você poderá usar a [API de Transcrição em Lote](batch-transcription.md) para solicitar e receber transcrições de forma assíncrona.

Uma solução típica usa estes serviços:

- O serviço De fala é usado para transcrever fala-a-texto. Uma assinatura padrão (S0) para o serviço Speech é necessária para usar a API de transcrição em lote. Assinaturas gratuitas (F0) não funcionarão.
- O [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é usado para armazenar dados de telefonia e as transcrições retornadas pela API de Transcrição em Lote. Essa conta de armazenamento deve usar notificações, especificamente para quando novos arquivos são adicionados. Essas notificações são usadas para disparar o processo de transcrição.
- O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) é usado para criar o URI de SAS (Assinatura de Acesso Compartilhado) para cada gravação e disparar a solicitação HTTP POST a fim de iniciar uma transcrição. Além disso, o Azure Functions é usado para criar solicitações a fim de recuperar e excluir transcrições usando a API de Transcrição em Lote.

Internamente, estamos usando as tecnologias acima para dar suporte às chamadas de cliente da Microsoft no modo de lote.
![Arquitetura de lote](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transcrição em tempo real para dados do call center

Para algumas empresas, a transcrição das conversas em tempo real é obrigatória. A transcrição em tempo real pode ser usada para identificar palavras-chave e disparar pesquisas de conteúdo e de recursos relevantes para a conversa, monitorar o sentimento, melhorar a acessibilidade ou fornecer traduções para os clientes e agentes que não são falantes nativos.

Para cenários que exigem a transcrição em tempo real, recomendamos o uso do [SDK de Fala](speech-sdk.md). Atualmente, a conversão de fala em texto está disponível em [mais de 20 idiomas](language-support.md), e o SDK está disponível em C++, C#, Java, Python, Node.js, Objective-C e JavaScript. Amostras estão disponíveis em cada idioma no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Para obter as notícias e as atualizações mais recentes, confira [Notas sobre a versão](releasenotes.md).

Internamente, estamos usando as tecnologias acima para analisar em tempo real as chamadas de clientes da Microsoft à medida que acontecem, conforme ilustrado no diagrama a seguir.

![Arquitetura de lote](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Explicação sobre IVRs

O serviço Speech pode ser facilmente integrado em qualquer solução usando o [Speech SDK](speech-sdk.md) ou a [API REST](rest-apis.md). No entanto, a transcrição de call center pode exigir outras tecnologias. Normalmente, é necessária uma conexão entre um sistema IVR e o Azure. Embora não ofereçamos tais componentes, aqui está uma descrição do que uma conexão com um IVR implica.

Vários produtos de serviço de IVR ou telefonia (como Genesys ou AudioCodes) oferecem recursos de integração que podem ser aproveitados para habilitar a passagem de áudio de entrada e saída para um serviço Azure. Basicamente, um serviço personalizado do Azure pode fornecer uma interface específica para definir sessões de chamada telefônica (como Call Start ou Call End) e expor uma API de WebSocket para receber áudio de fluxo de entrada que é usado com o serviço Speech. As respostas de saída, como transcrição de conversas ou conexões com o Bot Framework, podem ser sintetizadas com o serviço de conversão de texto em fala da Microsoft e retornadas ao IVR para reprodução.

Outro cenário é a integração direta com o Protocolo de Iniciação de Sessão (SIP). Um serviço do Azure se conecta a um Servidor SIP, obtendo, portanto, um fluxo de entrada e um fluxo de saída, que é usado para as fases de conversão de fala em texto e de texto em fala. Para se conectar a um Servidor SIP, há ofertas de software comercial, como o SDK do Ozeki ou [a API de chamadas e reuniões do Teams](/graph/api/resources/communications-api-overview) (atualmente em beta), que foram projetadas para dar suporte a esse tipo de cenário para chamadas de áudio.

## <a name="customize-existing-experiences"></a>Personalizar as experiências existentes

 O serviço Speech funciona bem com modelos embutidos. No entanto, você pode querer personalizar e ajustar ainda mais a experiência para o seu produto ou ambiente. As opções de personalização vão do ajuste do modelo acústico a fontes de voz exclusivas para sua marca. Depois de construir um modelo personalizado, você pode usá-lo com qualquer um dos recursos de serviço speech em tempo real ou em lote.

| Serviço de Fala | Modelo | Descrição |
| -------------- | ----- | ----------- |
| Conversão de fala em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Crie um modelo acústico personalizado para aplicativos, ferramentas ou dispositivos usados em ambientes particulares, como um carro ou um chão de fábrica, cada um deles com condições específicas de gravação. Exemplos incluem fala com sotaque, ruídos de fundo específicos ou uso de um microfone específico para gravação. |
|                | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e da gramática específicos de um setor, por exemplo, terminologia médica ou jargão de TI. |
|                | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Com um modelo de pronúncia personalizado, você pode definir a forma fonética e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é `.txt` um arquivo de pronúncia, que é um arquivo simples. |
| Conversão de texto em fala | [Fonte de voz](how-to-customize-voice-font.md) | Fontes de voz personalizadas permitem que você crie uma voz reconhecível exclusiva para sua marca. É necessária apenas uma pequena quantidade de dados para começar a usar. Quanto mais dados que você fornecer, mais natural e humana sua fonte de voz soará. |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo está disponível no GitHub para cada um dos recursos do serviço Speech. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

- [Exemplos de conversão de fala em texto e tradução de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de Referência

- [SDK de fala](speech-sdk-reference.md)
- [SDK de Dispositivos de Fala](speech-devices-sdk.md)
- [API REST: Fala-a-texto](rest-speech-to-text.md)
- [API REST: Texto-para-fala](rest-text-to-speech.md)
- [API REST: Transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
