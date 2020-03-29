---
title: Criar um serviço personalizado de voz - fala
titleSuffix: Azure Cognitive Services
description: Quando estiver pronto para carregar seus dados, vá para o portal Voz Personalizada. Crie ou selecione um projeto de Voz Personalizada. O projeto deve compartilhar a linguagem/local e as propriedades de gênero certas como os dados que você pretende usar para o seu treinamento de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717317"
---
# <a name="create-a-custom-voice"></a>Criar uma Voz Personalizada

Em [Prepare dados para Voz Personalizada,](how-to-custom-voice-prepare-data.md)descrevemos os diferentes tipos de dados que você pode usar para treinar uma voz personalizada e os diferentes requisitos de formato. Depois de preparar seus dados, você pode começar a carregá-los para o [portal De voz personalizada](https://aka.ms/custom-voice-portal), ou através da API de treinamento de voz personalizada. Aqui descrevemos os passos de treinamento de uma voz personalizada através do portal.

> [!NOTE]
> Esta página pressupõe que você leu [Comece com a Voz Personalizada](how-to-custom-voice.md) e Prepare dados para Voz [Personalizada](how-to-custom-voice-prepare-data.md)e criei um projeto de Voz Personalizada.

Verifique os idiomas suportados para voz personalizada: [idioma para personalização](language-support.md#customization).

## <a name="upload-your-datasets"></a>Upload de conjunto de dados

Quando estiver pronto para fazer upload de seus dados, vá para o [portal Voz Personalizada](https://aka.ms/custom-voice-portal). Crie ou selecione um projeto de Voz Personalizada. O projeto deve compartilhar a linguagem/local e as propriedades de gênero certas como os dados que você pretende usar para o seu treinamento de voz. Por exemplo, `en-GB` selecione se as gravações de áudio que você tem forem feitas em inglês com um sotaque britânico.

Vá para a guia **Dados** e clique **em Carregar dados**. No assistente, selecione o tipo de dados correto que corresponde ao que você preparou.

Cada conjunto de dados que você carrega deve atender aos requisitos para o tipo de dados escolhido. É importante formatar corretamente seus dados antes de serem carregados. Isso garante que os dados serão processados com precisão pelo serviço Custom Voice. Vá para [Preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md) e certifique-se de que seus dados foram formatados corretamente.

> [!NOTE]
> Os usuários de assinatura gratuita (F0) podem carregar dois conjuntos de dados simultaneamente. Os usuários de assinatura padrão (S0) podem carregar cinco conjuntos de dados simultaneamente. Se o limite for alcançado, aguarde até que pelo menos um dos conjuntos de dados conclua a importação. Em seguida, tente novamente.

> [!NOTE]
> O número máximo de conjuntos de dados permitidos para serem importados por assinatura é de 10 arquivos .zip para usuários de assinatura gratuita (F0) e 500 para usuários de assinatura padrão (S0).

Os conjuntos de dados são validados automaticamente quando você apertar o botão upload. A validação de dados inclui uma série de verificações nos arquivos de áudio para verificar o formato, tamanho e taxa de amostragem do arquivo. Corrija os erros se houver e envie novamente. Quando a solicitação de importação de dados é iniciada com sucesso, você deve ver uma entrada na tabela de dados que corresponde ao conjunto de dados que você acabou de carregar.

A tabela a seguir mostra os estados de processamento dos conjuntos de dados importados:

| Estado | Significado |
| ----- | ------- |
| Processando | Seu conjunto de dados foi recebido e está sendo processado. |
| Teve êxito | Seu conjunto de dados foi validado e agora pode ser usado para construir um modelo de voz. |
| Falhou | Seu conjunto de dados foi falhado durante o processamento devido a muitas razões, por exemplo, erros de arquivo, problemas de dados ou problemas de rede. |

Após a validação ser concluída, você pode ver o número total de expressões combinadas para cada um de seus conjuntos de dados na coluna **Enunciados.** Se o tipo de dados selecionado exigir segmentação de áudio longo, esta coluna apenas reflete as expressões que segmentamos para você com base em suas transcrições ou através do serviço de transcrição de fala. Você pode baixar ainda mais o conjunto de dados validado para visualizar os resultados detalhados das declarações importadas com sucesso e suas transcrições de mapeamento. Dica: a segmentação de áudio longo pode levar mais de uma hora para concluir o processamento de dados.

Para os conjuntos de dados en-US e zh-CN, você pode baixar ainda mais um relatório para verificar as pontuações de pronúncia e o nível de ruído de cada uma de suas gravações. A pontuação de pronúncia varia de 0 a 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade com o script. Um sotaque pesado pode reduzir a pontuação de pronúncia e afetar a voz digital gerada.

Uma SNR (relação de sinal de ruído) superior indica menor ruído no áudio. Normalmente, é possível alcançar uma SNR de 50+ com gravação em estúdios profissionais. O áudio com uma SNR abaixo de 20 pode resultar em ruído óbvio na voz gerada.

Considere regravar quaisquer enunciados com pontuações baixas de pronúncia ou relações de sinal de ruído ruins. Caso não possa regravar, existe a possibilidade de excluir esses enunciados do conjunto de dados.

## <a name="build-your-custom-voice-model"></a>Construa seu modelo de voz personalizado

Depois que seu conjunto de dados for validado, você pode usá-lo para construir seu modelo de voz personalizado.

1.  Navegue até **o treinamento personalizado de >** de voz > de texto para voz .

2.  Clique **no modelo Train**.

3.  Em seguida, digite **um Nome** e **descrição** para ajudá-lo a identificar este modelo.

    Escolha um nome com cuidado. O nome que você inserir aqui será o nome utilizado para especificar a voz na solicitação de síntese de fala como parte da entrada SSML. Apenas letras, números e alguns caracteres de \_pontuação, como -, e (', ') são permitidos. Use nomes diferentes para diferentes modelos de voz.

    Um uso comum do campo **Descrição** é registrar os nomes dos conjuntos de dados que foram usados para criar o modelo.

4.  Na página **Selecionar dados de treinamento,** escolha um ou vários conjuntos de dados que você gostaria de usar para treinamento. Verifique o número de declarações antes de enviá-las. Você pode começar com qualquer número de expressões para modelos de voz en-US e zh-CN. Para outros locais, você deve selecionar mais de 2.000 expressões para poder treinar uma voz.

    > [!NOTE]
    > Os nomes de áudio duplicados serão removidos do treinamento. Certifique-se de que os conjuntos de dados selecionados não contenham os mesmos nomes de áudio em vários arquivos .zip.

    > [!TIP]
    > O uso dos conjuntos de dados do mesmo alto-falante é necessário para resultados de qualidade. Quando os conjuntos de dados submetidos para treinamento contiverem um número total de menos de 6.000 expressões distintas, você treinará seu modelo de voz através da técnica De Síntese Estatística Paramétrica. No caso de seus dados de treinamento excederem um número total de 6.000 expressões distintas, você iniciará um processo de treinamento com a técnica de Síntese de Concatenação. Normalmente, a tecnologia de concatenação pode resultar em resultados de voz mais naturais e de maior fidelidade. [Entre em contato com a equipe de Voz Personalizada](https://go.microsoft.com/fwlink/?linkid=2108737) se você quiser treinar um modelo com a mais recente tecnologia Neural TTS que pode produzir uma voz digital equivalente às vozes neurais disponíveis [publicamente.](language-support.md#neural-voices)

5.  Clique **em Treinar** para começar a criar seu modelo de voz.

A tabela Treinamento exibe uma nova entrada que corresponde a este modelo recém-criado. A tabela também exibe o status: Processamento, Sucesso, Falha.

O status mostrado reflete o processo de conversão do seu conjunto de dados para um modelo de voz, como mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Processando | Seu modelo de voz está sendo criado. |
| Teve êxito | Seu modelo de voz foi criado e pode ser implantado. |
| Falhou | Seu modelo de voz foi falhado no treinamento devido a muitas razões, por exemplo, problemas de dados invisíveis ou problemas de rede. |

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Intervalos de tempo típicos variam de aproximadamente 30 minutos para centenas de enunciados a 40 horas para 20.000 enunciados. Uma vez que seu treinamento modelo é bem sucedido, você pode começar a testá-lo.

> [!NOTE]
> Os usuários de assinatura gratuita (F0) podem treinar uma fonte de voz simultaneamente. Os usuários de assinatura padrão (S0) podem treinar três vozes simultaneamente. Se o limite for alcançado, aguarde até que pelo menos uma das fontes de voz termine o treinamento; em seguida, tente novamente.

> [!NOTE]
> O número máximo de modelos de voz permitidos para serem treinados por assinatura é de 10 modelos para usuários de assinatura gratuita (F0) e 100 para usuários de assinatura padrão (S0).

Se você estiver usando o recurso de treinamento de voz neural, você pode selecionar para treinar um modelo otimizado para cenários de streaming em tempo real, ou um modelo neural HD otimizado para [síntese de áudio longo](long-audio-api.md)assíncrono .  

## <a name="test-your-voice-model"></a>Teste seu modelo de voz

Depois que a fonte de voz for compilada com êxito, você poderá testá-la antes de implantá-la para uso.

1.  Navegue até **o teste de > de voz personalizado > texto-para-fala**.

2.  Clique **em Adicionar teste**.

3.  Selecione um ou vários modelos que você gostaria de testar.

4.  Forneça o texto que deseja que as vozes(s) falem. Se você tiver selecionado para testar vários modelos ao mesmo tempo, o mesmo texto será usado para o teste para diferentes modelos.

    > [!NOTE]
    > O idioma do texto deve ser o mesmo idioma da fonte de voz. Somente modelos treinados com sucesso podem ser testados. Apenas o texto simples é suportado nesta etapa.

5.  Clique em **Criar**.

Depois de submeter sua solicitação de teste, você retornará à página de teste. A tabela agora inclui uma entrada que corresponde à nova solicitação e a coluna de status. Pode demorar alguns minutos para a sintetização de voz. Quando a coluna de status diz **Sucesso,** você pode reproduzir o áudio ou baixar a entrada de texto (um arquivo .txt) e a saída de áudio (um arquivo .wav), e ainda fazer uma audição para obter qualidade.

Você também pode encontrar os resultados dos testes na página de detalhes de cada modelo selecionado para testes. Vá para a guia **Treinar** e clique no nome do modelo para inserir a página de detalhes do modelo.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Crie e use um ponto final de voz personalizado

Depois de criar e testar com êxito o modelo de voz, implante-o em um ponto de extremidade personalizado de Conversão de Texto em Fala. Em seguida, você usará esse ponto de extremidade no lugar do ponto de extremidade comum ao fazer solicitações de Conversão de Texto em Fala por meio de API REST. Seu ponto final personalizado pode ser chamado apenas pela assinatura que você usou para implantar a fonte.

Para criar um novo ponto final de voz personalizado, vá para **a implantação de > de voz personalizada > texto-para-voz**. Selecione **Adicionar ponto final** e digite um **nome** e **descrição** para o seu ponto final personalizado. Em seguida, selecione o modelo de voz personalizado que você gostaria de associar a este ponto final.

Depois de clicar no botão **Adicionar,** na tabela de ponto final, você verá uma entrada para o seu novo ponto final. Pode demorar alguns minutos para instanciar um novo ponto de extremidade. Quando o status da implantação for **bem sucedido,** o ponto final está pronto para uso.

> [!NOTE]
> Os usuários de assinatura gratuita (F0) podem ter apenas um modelo implantado. Os usuários de assinatura padrão (S0) podem criar até 50 pontos finais, cada um com sua própria voz personalizada.

> [!NOTE]
> Para usar sua voz personalizada, você deve especificar o nome do modelo de voz, usar o URI personalizado diretamente em uma solicitação HTTP e usar a mesma assinatura para passar pela autenticação do serviço TTS.

Depois que seu ponto final é implantado, o nome do ponto final aparece como um link. Clique no link para exibir informações específicas do seu ponto final, como a chave de ponto final, url de ponto final e código de exemplo.

O teste online do ponto de extremidade também está disponível no portal de voz personalizada. Para testar seu ponto final, escolha **'Verificar ponto final'** na página **de detalhes endpoint.** A página de teste do ponto de extremidade é exibida. Digite o texto a ser falado (em texto simples ou [formato SSML](speech-synthesis-markup.md) na caixa de texto. Para ouvir o texto falado na fonte de voz personalizada, selecione **Reproduzir**. Este recurso de teste será cobrado contra o uso de síntese de fala personalizado.

O ponto de extremidade personalizado é funcionalmente idêntico ao ponto de extremidade padrão utilizado para solicitações de Conversão de Texto em Fala. Consulte [API REST](rest-text-to-speech.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

* [Guia: Grave suas amostras de voz](record-custom-voice-samples.md)
* [Referência de API texto-para-fala](rest-text-to-speech.md)
* [API de áudio longo](long-audio-api.md)
