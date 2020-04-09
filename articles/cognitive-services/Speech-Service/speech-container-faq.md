---
title: Contêineres de serviço de fala frequentemente perguntas (FAQ)
titleSuffix: Azure Cognitive Services
description: Instale e execute recipientes de fala. a fala-para-texto transcreve fluxos de áudio para texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9b0fd89693517bdb63ba6f4265fddf2b0aa57ba5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874476"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Contêineres de serviço de fala frequentemente perguntas (FAQ)

Ao usar o serviço Speech com contêineres, conte com essa coleção de perguntas frequentes antes de escalar para apoiar. Este artigo captura questões de diferentes graus, do geral ao técnico. Para expandir uma resposta, clique na pergunta.

## <a name="general-questions"></a>Perguntas gerais

<details>
<summary>
<b>Como funcionam os recipientes de fala e como configurá-los?</b>
</summary>

**Resposta:** Ao configurar o cluster de produção, há várias coisas a considerar. Primeiro, a configuração de uma única linguagem, vários recipientes, na mesma máquina, não deve ser um grande problema. Se você está enfrentando problemas, pode ser um problema relacionado ao hardware - então primeiro olharíamos para o recurso, ou seja; Especificações de CPU e memória.

Considere por um `ja-JP` momento, o recipiente e o modelo mais recente. O modelo acústico é a peça mais exigente em termos de CPU, enquanto o modelo de linguagem exige mais memória. Quando avaliamos o uso, é preciso cerca de 0,6 núcleos de CPU para processar uma única solicitação de fala para texto quando o áudio está fluindo em tempo real (como no microfone). Se você estiver alimentando o áudio mais rápido do que em tempo real (como a partir de um arquivo), esse uso pode dobrar (núcleos de 1,2x). Enquanto isso, a memória listada abaixo é a memória de funcionamento para decodificação da fala. Ele *não* leva em conta o tamanho real do modelo de idioma, que residirá no cache de arquivos. Pois `ja-JP` isso é um adicional de 2 GB; para `en-US`, pode ser mais (6-7 GB).

Se você tem uma máquina onde a memória é escassa, e você está tentando implantar vários idiomas nela, é possível que o cache de arquivos esteja cheio, e o SISTEMA OPERACIONAL é forçado a página de modelos dentro e fora. Para uma transcrição em execução, isso pode ser desastroso, e pode levar a desacelerações e outras implicações de desempenho.

Além disso, pré-embalamos executáveis para máquinas com o conjunto de instruções [de extensão vetorial avançada (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Uma máquina com o conjunto de instruções AVX512 exigirá geração de código para esse destino, e a partir de 10 contêineres para 10 idiomas pode esgotar temporariamente a CPU. Uma mensagem como esta aparecerá nos registros do docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Finalmente, você pode definir o número de decodificadores `DECODER MAX_COUNT` que deseja dentro de um *único* recipiente usando variável. Então, basicamente, devemos começar com o seu SKU (CPU/memória), e podemos sugerir como tirar o melhor dele. Um ótimo ponto de partida está se referindo às especificações recomendadas de recursos da máquina host.

<br>
</details>

<details>
<summary>
<b>Você poderia ajudar no planejamento de capacidade e na estimativa de custos dos contêineres on-prem Speech?</b>
</summary>

**Resposta:** Para a capacidade do contêiner no modo de processamento em lote, cada decodificador poderia lidar com 2-3x em tempo real, com dois núcleos de CPU, para um único reconhecimento. Não recomendamos manter mais de dois reconhecimentos simultâneos por instância de contêiner, mas recomendamos executar mais instâncias de contêineres por razões de confiabilidade/disponibilidade, atrás de um balanceador de carga.

Embora pudéssemos ter cada instância de contêiner sendo executado com mais decodificadores. Por exemplo, podemos ser capazes de configurar 7 decodificadores por instância de contêiner em uma máquina de oito núcleos (em mais de 2x cada), produzindo throughput de 15x. Há um param `DECODER_MAX_COUNT` para estar ciente. Para o caso extremo, surgem problemas de confiabilidade e latência, com o aumento da capacidade de utilização significativamente. Para um microfone, será em 1x em tempo real. O uso geral deve ser de cerca de um núcleo para um único reconhecimento.

Para o cenário de processamento de 1 K horas/dia no modo de processamento em lote, em um caso extremo, 3 VMs poderiam lidar com isso dentro de 24 horas, mas não garantidos. Para lidar com dias de pico, failover, atualização e para fornecer backup mínimo/BCP, recomendamos 4-5 máquinas em vez de 3 por cluster, e com mais de 2 clusters.

Para hardware, usamos o VM `DS13_v2` padrão do Azure como referência (cada núcleo deve ser de 2,6 GHz ou melhor, com conjunto de instruções AVX2 ativado).

| Instância  | vCPU(s) | RAM    | Armazenamento temporário | Pague como você com a AHB | Reserva de 1 ano com AHB (% Poupança) | 3 anos reservados com AHB (% Poupança) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/hora            | $0,3528/hora (~41%)                 | $0,2333/hora (~61%)                  |

Com base na referência de design (dois clusters de 5 VMs para lidar com o processamento de 1 K horas/dia de áudio), o custo de hardware de 1 ano será:

> 2 (clusters) * 5 (VMs por cluster) * $0,3528/hora * 365 (dias) * 24 (horas) = $31K / ano

Ao mapear para a máquina física, uma estimativa geral é de 1 vCPU = 1 Núcleo de CPU Físico. Na realidade, 1vCPU é mais poderoso do que um único núcleo.

Para on-prem, todos esses fatores adicionais entram em jogo:

- Em que tipo a CPU física é e quantos núcleos nela
- Quantas CPUs funcionando juntas na mesma caixa/máquina
- Como as VMs são configuradas
- Como o hiper-threading / multi-threading é usado
- Como a memória é compartilhada
- O Sistema Operacional, etc.

Normalmente não é tão bem sintonizado quanto o Azure o ambiente. Considerando outras despesas gerais, eu diria que uma estimativa segura é de 10 núcleos de CPU físicos = 8 Azure vCPU. Embora as CPUs populares tenham apenas oito núcleos. Com a implantação on-prem, o custo será maior do que o uso de VMs do Azure. Além disso, considere a taxa de depreciação.

O custo do serviço é o mesmo do serviço online: $1/hora para o discurso-para-texto. O custo do serviço de fala é:

> $1 * 1000 * 365 = $365.000

O custo de manutenção pago à Microsoft depende do nível de serviço e do conteúdo do serviço. Ele é vários de US $ 29,99 /mês para o nível básico para centenas de milhares se o serviço no local envolvido. Um número bruto é de $300/hora para serviço/manutenção. O custo das pessoas não está incluído. Outros custos de infra-estrutura (como armazenamento, redes e balanceadores de carga) não estão incluídos.

<br>
</details>

<details>
<summary>
<b>Por que falta pontuação na transcrição?</b>
</summary>

**Resposta:** O `speech_recognition_language=<YOUR_LANGUAGE>` deve ser explicitamente configurado na solicitação se eles estiverem usando o cliente Carbon.

Por exemplo:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Esta é a saída:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Posso usar um modelo acústico personalizado e um modelo de linguagem com recipiente de fala?</b>
</summary>

Atualmente, só podemos passar um ID de modelo, seja modelo de linguagem personalizado ou modelo acústico personalizado.

**Resposta:** A decisão *de não* apoiar modelos acústicos e linguísticos simultaneamente foi tomada. Isso permanecerá em vigor, até que um identificador unificado seja criado para reduzir as quebras de API. Então, infelizmente, isso não é suportado agora.

<br>
</details>

<details>
<summary>
<b>Você poderia explicar esses erros do recipiente de fala para texto personalizado?</b>
</summary>

**Erro 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Resposta 1:** Se você está treinando com o modelo personalizado mais recente, nós atualmente não suportamos isso. Se você treinar com uma versão mais antiga, deve ser possível usar. Ainda estamos trabalhando no suporte às versões mais recentes.

Essencialmente, os recipientes personalizados não suportam modelos acústicos baseados em Halide ou ONNX (que é o padrão no portal de treinamento personalizado). Isso se deve ao não criptografado de modelos personalizados e não queremos expor os modelos ONNX, no entanto; modelos de linguagem são bons. O cliente precisará selecionar explicitamente um modelo não-ONNX mais antigo para treinamento personalizado. A precisão não será afetada. O tamanho do modelo pode ser maior (por 100 MB).

> Modelo de suporte > 20190220 (v4.5 Unificado)

**Erro 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Resposta 2:** Você precisa fornecer o nome de voz correto na solicitação, que é sensível a maiúsculas e minúsculas. Consulte o mapeamento completo do nome do serviço. Você tem `en-US-JessaRUS`que `en-US-JessaNeural` usar, como não está disponível agora na versão container do texto-para-fala.

**Erro 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Resposta 3:** Você se prepara para criar um recurso de fala, não um recurso de Serviços Cognitivos.


<br>
</details>

<details>
<summary>
<b>Quais protocolos de API são suportados, REST ou WS?</b>
</summary>

**Resposta:** Para contêineres de voz a texto e de voz personalizada, atualmente, apenas suportamos o protocolo baseado em websocket. O SDK só suporta chamar em WS, mas não REST. Há um plano para adicionar suporte ao REST, mas não ETA no momento. Consulte sempre a documentação oficial, consulte [pontos finais de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>O CentOS é suportado para contêineres de Speech?</b>
</summary>

**Resposta:** O CentOS 7 ainda não é suportado pelo Python SDK, o Ubuntu 19.04 também não é suportado.

O pacote do SDK de Fala do Python está disponível para estes sistemas operacionais:
- **Windows** - x64 e x86
- **Mac** - macOS X versão 10.12 ou posterior
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 em x64

Para obter mais informações sobre a configuração do ambiente, consulte [configuração da plataforma Python](quickstarts/setup-platform.md?pivots=programming-language-python). Por enquanto, o Ubuntu 18.04 é a versão recomendada.

<br>
</details>

<details>
<summary>
<b>Por que estou cometendo erros ao tentar chamar os pontos finais da previsão do LUIS?</b>
</summary>

Estou usando o contêiner LUIS em uma implantação de IoT Edge e estou tentando chamar o ponto final de previsão do LUIS de outro contêiner. O contêiner LUIS está ouvindo na porta 5001, e a URL que estou usando é a seguinte:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

O erro que estou recebendo é:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Vejo a solicitação nos registros de contêineres luis e a mensagem diz:

```cmd
The request path /luis//predict" does not match a supported file type.
```

O que isso significa? O que estou perdendo? Eu estava seguindo o exemplo para o Speech SDK, [daqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) O cenário é que estamos detectando o áudio diretamente do microfone do PC e tentando determinar a intenção, com base no aplicativo LUIS que treinamos. O exemplo que eu vinculei faz exatamente isso. E funciona bem com o serviço baseado em nuvem LUIS. O uso do Speech SDK parecia nos salvar de ter que fazer uma chamada explícita separada para a API de discurso para texto e, em seguida, uma segunda chamada para LUIS.

Então, tudo o que estou tentando fazer é mudar do cenário de usar LUIS na nuvem para usar o contêiner LUIS. Eu não posso imaginar se o Speech SDK funciona para um, não vai funcionar para o outro.

**Resposta:** O Speech SDK não deve ser usado contra um recipiente LUIS. Para o uso do recipiente LUIS, deve-se utilizar a API LUIS SDK ou LUIS REST. O Speech SDK deve ser usado contra um recipiente de fala.

Uma nuvem é diferente de um recipiente. Uma nuvem pode ser composta de vários recipientes agregados (às vezes chamados de micro serviços). Então há um contêiner LUIS e depois há um recipiente Speech - Dois recipientes separados. O recipiente do Discurso só fala. O contêiner LUIS só faz LUIS. Na nuvem, porque ambos os contêineres são conhecidos por serem implantados, e é um desempenho ruim para um cliente remoto ir para a nuvem, fazer a fala, voltar, depois ir para a nuvem novamente e fazer LUIS, nós fornecemos um recurso que permite que o cliente vá para o Speech, fique na nuvem, vá para LUIS e depois volte para o cliente. Assim, mesmo neste cenário o Speech SDK vai para o contêiner de nuvem Speech com áudio, e então o contêiner de nuvem Speech fala com o contêiner de nuvem LUIS com texto. O contêiner LUIS não tem nenhum conceito de aceitar áudio (não faria sentido para o contêiner LUIS aceitar streaming de áudio - LUIS é um serviço baseado em texto). Com o on-prem, não temos certeza de que nosso cliente implantou ambos os contêineres, não temos a pretensão de orquestrar entre contêineres nas instalações de nossos clientes, e se ambos os contêineres forem implantados no prem, dado que eles são mais locais para o cliente, não é um fardo ir primeiro ao SR, voltar para o cliente, e ter o cliente então pegar esse texto e ir para LUIS.

<br>
</details>

<details>
<summary>
<b>Por que estamos cometendo erros com macOS, recipiente de fala e o Python SDK?</b>
</summary>

Quando enviamos um arquivo *.wav* para ser transcrito, o resultado volta com:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Sabemos que o websocket está configurado corretamente.

**Resposta:** Se esse for o caso, então veja [este problema do GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Temos um trabalho em volta, [proposto aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

O carbono fixou isso na versão 1.8.


<br>
</details>

<details>
<summary>
<b>Quais são as diferenças nos pontos finais do recipiente Speech?</b>
</summary>

Você poderia ajudar a preencher as seguintes métricas de teste, incluindo quais funções testar e como testar as APIs SDK e REST? Especialmente, diferenças em "interativo" e "conversa", que eu não vi do doc/amostra existente.

| Ponto de extremidade                                                | Teste funcional                                                   | . | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Texto sintetizado (texto-para-fala)                                  |     | Sim      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Serviços cognitivos no ditado prem v1 websocket endpoint        | Sim | Não       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | O ponto final do websocket interativo v1 dos Serviços Cognitivos  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Os serviços cognitivos on-prem conversa v1 websocket endpoint |     |          |

**Resposta:** Esta é uma fusão de:
- Pessoas tentando o ponto final do ditado para contêineres, (não sei como conseguiram essa URL)
- O<sup>1º</sup> ponto final da festa é o de um contêiner.
- O ponto final de 1<sup>parte</sup> retornando speech.fragmenta mensagens em vez das `speech.hypothesis` mensagens os pontos finais da<sup>3ª</sup> parte retornam para o ponto final do ditado.
- O Carbono inicia `RecognizeOnce` rapidamente todo o uso (modo interativo)
- Carbono tendo uma `speech.fragment` afirmação de que para mensagens que requerem eles não são devolvidos no modo interativo.
- Carbono tendo o fogo afirma em construções de liberação (matando o processo).

A solução de solução é mudar para usar o reconhecimento contínuo em seu código, ou (mais rápido) conectar-se aos pontos finais interativos ou contínuos no contêiner.
Para seu código, defina o ponto final para <host:port>/speech/recognition/interactive/cognitiveservices/v1

Para os vários modos, consulte modos de fala - veja abaixo:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A correção adequada está vindo com o SDK 1.8, que tem suporte on-prem (escolherá o ponto final certo, então não seremos piores do que o serviço online). Enquanto isso, há uma amostra para reconhecimento contínuo, por que não apontamos para ela?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Qual modo devo usar para vários arquivos de áudio?</b>
</summary>

**Resposta:** Aqui está um [começo rápido usando Python](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python). Você pode encontrar os outros idiomas vinculados no site do docs.

Apenas para esclarecer para o interativo, conversa e ditado; esta é uma maneira avançada de especificar a maneira particular em que nosso serviço lidará com a solicitação de fala. Infelizmente, para os contêineres on-prem temos que especificar o URI completo (uma vez que inclui máquina local), de modo que essas informações vazaram da abstração. Estamos trabalhando com a equipe sdk para tornar isso mais utilizável no futuro.

<br>
</details>

<details>
<summary>
<b>Como podemos fazer benchmark de uma medida aproximada de transações/segundo/núcleo?</b>
</summary>

**Resposta:** Aqui estão alguns dos números brutos a esperar do modelo existente (mudará para melhor no que vamos enviar em GA):

- Para arquivos, o estrangulamento será no sdk de fala, em 2x. Os primeiros cinco segundos de áudio não são estrangulados. Decodificador é capaz de fazer cerca de 3x em tempo real. Para isso, o uso geral da CPU será próximo de 2 núcleos para um único reconhecimento.
- Para microfone, será em 1x em tempo real. O uso geral deve ser de cerca de 1 núcleo para um único reconhecimento.

Isso tudo pode ser verificado a partir dos registros docker. Na verdade, despejamos a linha com estatísticas de sessão e frase/enunciado, e isso inclui os números RTF.


<br>
</details>

<details>
<summary>
<b>É comum dividir arquivos de áudio em mandris para uso de recipientes speech?</b>
</summary>

Meu plano atual é pegar um arquivo de áudio existente e dividi-lo em pedaços de 10 segundos e enviá-los através do contêiner. É um cenário aceitável?  Existe uma maneira melhor de processar arquivos de áudio maiores com o contêiner?

**Resposta:** Basta usar o sdk de fala e dar-lhe o arquivo, ele vai fazer a coisa certa. Por que você precisa repassar o arquivo?


<br>
</details>

<details>
<summary>
<b>Como faço para fazer vários contêineres funcionarem no mesmo host?</b>
</summary>

O médico diz para expor um porto diferente, o que eu faço, mas o contêiner LUIS ainda está ouvindo no porto 5000?

**Resposta:** Tente. `-p <outside_unique_port>:5000` Por exemplo, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Perguntas técnicas

<details>
<summary>
<b>Como posso obter APIs não-lotes &lt;para lidar com áudio com 15 segundos de duração?</b>
</summary>

**Resposta:** Isso é no modo interativo. Se você usar ditado ou conversa, isso não é um problema.


<br>
</details>

<details>
<summary>
<b>Quais são os recursos recomendados, CPU e RAM; para 50 pedidos simultâneos?</b>
</summary>

Quantas solicitações simultâneas uma alça de 4 núcleos e 4 GB de RAM? Se tivermos que atender, por exemplo, 50 solicitações simultâneas, quantas Core e RAM são recomendadas?

**Resposta:** Em tempo real, 8 `en-US`com o nosso mais recente , por isso recomendamos usar mais contêineres docker além de 6 pedidos simultâneos. Ele fica mais louco além de 16 núcleos, e torna-se sensível ao nó de acesso à memória não uniforme (NUMA). A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada recipiente de Fala.

# <a name="speech-to-text"></a>[Conversão de fala em texto](#tab/stt)

| Contêiner      | Mínimo             | Recomendadas         |
|----------------|---------------------|---------------------|
| Conversão de fala em texto | 2 núcleos, memória de 2 GB | 4 núcleos, memória de 4 GB |

# <a name="custom-speech-to-text"></a>[Discurso personalizado para texto](#tab/cstt)

| Contêiner             | Mínimo             | Recomendadas         |
|-----------------------|---------------------|---------------------|
| Discurso personalizado para texto | 2 núcleos, memória de 2 GB | 4 núcleos, memória de 4 GB |

# <a name="text-to-speech"></a>[Conversão de texto em fala](#tab/tts)

| Contêiner      | Mínimo             | Recomendadas         |
|----------------|---------------------|---------------------|
| Conversão de texto em fala | 1 núcleo, memória de 2 GB | 2 núcleos, memória de 3 GB |

# <a name="custom-text-to-speech"></a>[Texto-para-fala personalizado](#tab/ctts)

| Contêiner             | Mínimo             | Recomendadas         |
|-----------------------|---------------------|---------------------|
| Texto-para-fala personalizado | 1 núcleo, memória de 2 GB | 2 núcleos, memória de 3 GB |

***

- Cada núcleo deve ter pelo menos 2,6 GHz ou mais rápido.
- Para arquivos, o estrangulamento será no Speech SDK, em 2x (os primeiros 5 segundos de áudio não são estrangulados).
- O decodificador é capaz de fazer cerca de 2-3x em tempo real. Para isso, o uso geral da CPU será próximo de dois núcleos para um único reconhecimento. É por isso que não recomendamos manter mais de duas conexões ativas, por instância de contêiner. O lado extremo seria colocar cerca de 10 decodificadores em 2x `DS13_V2`em tempo real em uma máquina de oito núcleos como . Para a versão de contêiner 1.3 e posterior, há `DECODER_MAX_COUNT=20`um param que você pode tentar definir .
- Para microfone, será em 1x em tempo real. O uso geral deve ser de cerca de um núcleo para um único reconhecimento.

Considere o número total de horas de áudio que você tem. Se o número for grande, para melhorar a confiabilidade/disponibilidade, sugerimos executar mais instâncias de contêineres, seja em uma única caixa ou em várias caixas, atrás de um balanceador de carga. A orquestração poderia ser feita usando Kubernetes (K8S) e Helm, ou com Docker compondo.

Como exemplo, para lidar com 1000 horas/24 horas, tentamos configurar 3-4 VMs, com 10 instâncias/decodificadores por VM.

<br>
</details>

<details>
<summary>
<b>O recipiente de fala suporta pontuação?</b>
</summary>

**Resposta:** Temos capitalização (ITN) disponível no contêiner on-prem. A pontuação é dependente da língua, e não é suportada para algumas línguas, incluindo chinês e japonês.

Temos *do* suporte implícito e básico de pontuação para os contêineres `off` existentes, mas é por padrão. O que isso significa é `.` que você pode obter `。` o personagem em seu exemplo, mas não o personagem. Para habilitar essa lógica implícita, aqui está um exemplo de como fazê-lo em Python usando nosso Speech SDK (seria semelhante em outras línguas):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Por que estou recebendo 404 erros ao tentar postar dados no contêiner de fala para texto?</b>
</summary>

Aqui está um exemplo HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Resposta:** Não suportamos a API REST em nenhum contêiner de fala para texto, apenas suportamos WebSockets através do Speech SDK. Consulte sempre a documentação oficial, consulte [pontos finais de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Ao usar o serviço de fala para texto, por que estou recebendo esse erro?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Resposta:** Isso normalmente acontece quando você alimenta o áudio mais rápido do que o recipiente de reconhecimento de fala pode levá-lo. Buffers de clientes preenchidos, e o cancelamento é acionado. Você precisa controlar a concorrência e o RTF no qual você envia o áudio.

<br>
</details>

<details>
<summary>
<b>Você poderia explicar esses erros de contêiner texto-para-fala a partir dos exemplos C++?</b>
</summary>

**Resposta:** Se a versão do contêiner for maior que 1.3, este código deve ser usado:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Os contêineres mais antigos não têm o ponto `FromHost` final necessário para o Carbono trabalhar com a API. Se os recipientes usados para a versão 1.3, este código deve ser usado:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Abaixo está um exemplo `FromEndpoint` de uso da API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A `SetSpeechSynthesisVoiceName` função é chamada porque os recipientes com um mecanismo de texto para voz atualizado exigem o nome da voz.

<br>
</details>

<details>
<summary>
<b>Como posso usar o v1.7 do Speech SDK com um recipiente de fala?</b>
</summary>

**Resposta:** Existem três pontos finais no recipiente Speech para diferentes usos, eles são definidos como modos de fala - veja abaixo:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Eles são para propósitos diferentes e são usados de forma diferente.

[Amostras de Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Para reconhecimento único (modo interativo) com um ponto final personalizado (ou seja; `SpeechConfig` com um parâmetro de `speech_recognize_once_from_file_with_custom_endpoint_parameters()`ponto final), ver .
- Para reconhecimento contínuo (modo de conversação) e basta modificar `speech_recognize_continuous_from_file()`para usar um ponto final personalizado como acima, consulte .
- Para habilitar o ditado em amostras como acima (apenas se `speech_config`você `speech_config.enable_dictation()`realmente precisar), logo após você criar , adicionar código .

Em C# para habilitar o `SpeechConfig.EnableDictation()` ditado, invoque a função.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Apis
| Linguagem | Detalhes da API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initWithEndpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Não é atualmente suportado, nem é planejado. |

<br>
</details>

<details>
<summary>
<b>Como posso usar o v1.8 do Speech SDK com um recipiente de fala?</b>
</summary>

**Resposta:** Há uma nova `FromHost` API. Isso não substitui ou modifica quaisquer APIs existentes. Ele apenas adiciona uma maneira alternativa de criar uma configuração de voz usando um host personalizado.

### <a name="fromhost-apis"></a>`FromHost`Apis

| Linguagem | Detalhes da API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Sem suporte no momento |

> Parâmetros: host (obrigatório), chave de assinatura (opcional, se você puder usar o serviço sem ele).

Formato para `protocol://hostname:port` host `:port` é onde é opcional (veja abaixo):
- Se o contêiner estiver sendo executado `localhost`localmente, o nome de hospedagem será .
- Se o contêiner estiver sendo executado em um servidor remoto, use o nome host ou o endereço IPv4 desse servidor.

Exemplos de parâmetros de host para fala-a-texto:
- `ws://localhost:5000`- conexão não segura a um contêiner local usando a porta 5000
- `ws://some.host.com:5000`- conexão não segura a um contêiner em execução em um servidor remoto

Amostras python de cima, mas `host` `endpoint`use parâmetro em vez de:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos](speech-container-howto.md)
