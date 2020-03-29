---
title: Integração do Projeto Acústico Irreal e Wwise
titlesuffix: Azure Cognitive Services
description: Este artigo descreve a integração dos plug-ins Project Acoustics Unreal e Wwise em seu projeto.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243055"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integração do Projeto Acústico Irreal e Wwise
Este artigo descreve como integrar o pacote plug-in Project Acoustics ao seu projeto de jogo Unreal e Wwise existente.

Requisitos de software:
* [Motor Irreal](https://www.unrealengine.com/) 4.20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Plug-in Wwise para Irreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Se você estiver usando uma integração direta do Wwise SDK em vez do plug-in Wwise Unreal, consulte o plug-in Project Acoustics Unreal e ajuste as chamadas da API wwise.

Para usar o Project Acoustics com um motor de áudio diferente do Wwise, faça uma solicitação de aprimoramento no [fórum de discussão project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues). Você pode usar o plug-in Project Acoustics Unreal para consultar dados de acústica e fazer chamadas de API para o seu motor.

## <a name="download-project-acoustics"></a>Baixar Projeto Acústico
Baixe o [pacote plug-in Project Acoustics Unreal e Wwise](https://www.microsoft.com/download/details.aspx?id=58090) se você ainda não tiver.

Um plug-in unreal engine e um plug-in wwise mixer estão incluídos no pacote. O plug-in Unreal fornece integração de editor e tempo de execução. Durante a jogabilidade, o projeto Acoustics Unreal computa parâmetros como oclusão para cada objeto de jogo para cada quadro. Esses parâmetros são traduzidos em chamadas aPI wwise.

## <a name="integration-steps"></a>Etapas de integração

Siga estas etapas para instalar o pacote e implantá-lo em seu jogo.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Instale o plug-in do mixador project acoustics
1. Abra o lançador Wwise. Na **guia Plug-ins,** em **Install New Plug-ins**, **selecione Adicionar em Diretório**.

    ![Instale um plug-in no lançador Wwise](media/wwise-install-new-plugin.png)

1. Selecione o diretório *AcousticsWwisePlugin\ProjectAcoustics* que está no pacote de download. Contém o pacote plug-in do misturador Wwise.

   A Wwise instalará o plug-in. Project Acoustics deve aparecer na lista de plug-ins instalados no Wwise.  
![A lista de plug-ins instalada da Wwise após a instalação do Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Desimplante o Wwise no seu jogo
Reimplante o Wwise no seu jogo, mesmo que você já tenha integrado o Wwise. Esta etapa integra o plug-in Project Acoustics Wwise.

   > [!NOTE]
   > **Plug-in do motor:** Se você tiver o Wwise instalado como um plug-in de jogo em um projeto C++ Irreal, pule esta etapa. Se ele for instalado em vez disso como um plug-in do motor, por exemplo, porque seu projeto Unreal é apenas Blueprint, a implantação do Wwise com o plug-in do mixer é mais complexa. Crie um projeto C++ unreal vazio. Feche o editor Irreal se ele abrir e siga o procedimento restante para implantar o Wwise no projeto manequim. Em seguida, copie o plug-in Wwise implantado.
 
1. No lançador Wwise, selecione a guia **Mecanismo Irreal.** Selecione o menu "hamburger" (ícone) ao lado **de Projetos recentes do mecanismo irreal** e, em seguida, **selecione Procurar para o projeto**. Abra o arquivo Unreal project *.project* do seu jogo.

    ![A guia Unreal do lançador Wwise](media/wwise-unreal-tab.png)

1. Selecione **Integrar Wwise no Projeto** ou Modificar **Wwise no Projeto**. Esta etapa integra binários Wwise ao seu projeto, incluindo o plug-in do mixer Project Acoustics.

   > [!NOTE]
   > **Plug-in do motor:** Se você estiver usando o Wwise como um plug-in do motor e criou o projeto manequim como descrito anteriormente, copie a pasta que o Wwise implantou: *[DummyUProject]\Plugins\Wwise*. Cole-o sobre *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* é o caminho vazio do projeto C++ da Unreal, e *[UESource]* é onde as fontes do Unreal Engine estão instaladas. Depois de copiar a pasta, você pode excluir o projeto do boneco.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Adicione o plug-in Project Acoustics Unreal ao seu jogo
 
1. Copie a pasta *Unreal\ProjectAcoustics* no pacote plug-in. Crie uma nova pasta *[UProjectDir]\Plugins\ProjectAcoustics*, onde *[UProjectDir]* é a pasta de projeto do seu jogo que contém o arquivo *.uproject.*

   > [!NOTE]
   > **Plug-in do motor**: Se você estiver usando o Wwise como um plug-in do motor, você deve usar o Project Acoustics como um plug-in do motor Unreal também. Em vez do diretório de destino citado anteriormente, use *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Confirme se você vê uma pasta *Wwise* ao lado da pasta *ProjectAcoustics.* Ele contém o plug-in Wwise juntamente com binários para o plug-in do mixer que você implantou anteriormente.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estender a funcionalidade de plug-in Unreal do Wwise
O plug-in Project Acoustics Unreal requer um comportamento adicional exposto da API plug-in Wwise Unreal por [essas diretrizes](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Incluímos um arquivo em lote para automatizar o procedimento de correção.

* Dentro *do Plugins\ProjectAcoustics\Resources,* execute *PatchWwise.bat*. O exemplo a seguir, a imagem usa nosso projeto de amostra Do AcousticsGame.

    ![Uma janela do Windows Explorer com o script para corrigir Wwise destacado](media/patch-wwise-script.png)

* Se você não tiver o SDK DirectX instalado: Dependendo da versão do Wwise que você está `DXSDK_DIR` usando, talvez seja necessário comentar a linha que contém no *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![O editor de código mostrando 'DXSDK' comentou](media/directx-sdk-comment.png)

* Se você compilar usando o Visual Studio 2019: Para contornar um erro `VSVersion` de vinculação com o Wwise, altere manualmente o valor padrão no *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* para **vc150**:

    ![O editor de código que mostra "VSVersion" mudou para "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Construa o jogo e verifique se o Python está ativado

1. Compile seu jogo e certifique-se de que ele se constrói corretamente. Se ele não for construído, verifique cuidadosamente as etapas anteriores antes de continuar.

1. Abra seu projeto no Unreal Editor.

    > [!NOTE]
    > **Plug-in do motor:** Se você estiver usando o ProjectAcoustics como um plug-in do motor, certifique-se também de que ele esteja habilitado em plug-ins "embutidos".

    Você deve ver um novo modo, que indica que o Project Acoustics foi integrado.

    ![Modo acústico completo em Irreal](media/acoustics-mode-full.png)

1. Confirme se o plug-in Python para Unreal está ativado para que a integração do editor funcione corretamente.

    ![As extensões Python no editor Irreal habilitadas](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Configure seu projeto Wwise para usar o Project Acoustics

Um exemplo do projeto Wwise está incluído no download de amostras. Recomendamos que você o veja junto com estas instruções. As capturas de tela mais tarde neste artigo são deste projeto.

#### <a name="bus-setup"></a>Configuração do ônibus
O plug-in Project Acoustics Unreal procurará o plug-in da mixer `Project Acoustics Bus`associada em um barramento que tenha o nome exato . Crie um novo barramento de áudio que tenha esse mesmo nome. O plug-in do mixer pode funcionar em várias configurações. Mas, por enquanto, assumimos que ele será usado apenas para o processamento de reverb. Este ônibus levará o sinal de reverberação misto para todas as fontes que usam acústica. Pode misturar rio acima em qualquer estrutura de mistura de barramento. Um exemplo é mostrado aqui a partir do projeto de amostra Wwise que está incluído no download da amostra.

![Ônibus wwise mostrando Projeto Ônibus Acústico](media/acoustics-bus.png)

1. Defina a configuração do canal no ônibus como *1.0*, *2.0,* *4.0,* *5.1*ou *7.1*. Qualquer outra configuração resultará em nenhuma saída no ônibus.

    ![Opções de configuração de canal para Projeto Ônibus Acústico](media/acoustics-bus-channel-config.png)

1. Acesse os detalhes do Project Acoustics Bus e certifique-se de que você pode ver a guia **Plug-in do Mixer.**

    ![Wwise com a guia Plug-in mixer para o Project Acoustics Bus habilitado](media/mixer-tab-enable.png)

1. Vá para a guia **Mixer Plug-in** e adicione o plug-in do mixer de acústica do projeto ao barramento.

    ![Diagrama de como adicionar o Plug-in do Mixer de Acústica do Projeto ao barramento Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Configuração da hierarquia ator-mixer
Para melhor desempenho, o Project Acoustics aplica o processamento de sinal digital de áudio a todas as fontes simultaneamente. Então, o plug-in deve funcionar como um plug-in de batedeira. Wwise requer plug-ins do misturador para estar no barramento de saída, embora o barramento de saída geralmente carregue o sinal de saída seca. O Projeto Acústica exige que o sinal seco seja encaminhado através de ônibus `Project Acoustics Bus`aux, enquanto o sinal molhado é transportado no . O processo a seguir suporta migração gradual para esse fluxo de sinal.

Digamos que você tenha um projeto existente com uma hierarquia ator-mixer que contém *passos,* *armas*e outros no nível superior. Cada um tem um barramento de saída correspondente para sua mistura seca. Digamos que você queira migrar passos para usar a acústica. Primeiro, crie um ônibus aux correspondente para transportar o submix seco que é uma criança do ônibus de saída de passos. Por exemplo, usamos um prefixo "Seco" na imagem a seguir para organizar os ônibus, embora o nome exato não seja importante. Todos os medidores ou efeitos que você teve no ônibus passos ainda funcionarão como antes.

![Configuração recomendada de mistura seca Wwise](media/wwise-dry-mix-setup.png)

Em seguida, modifique a estrutura de saída do ônibus para o ator-mixer Footsteps da seguinte forma, com o *Project Acoustics Bus* definido como o **Barramento de Saída**, e *Dry_Footsteps* definido como um barramento aux definido pelo usuário.

![Configuração recomendada de barramento de misturador de ator Wwise](media/actor-mixer-bus-settings.png)

Agora todos os passos recebem tratamento acústico e saída seu reverb no Projeto Ônibus Acústico. O sinal seco é roteado através de Dry_Footsteps e espacializado como de costume.

Project Acoustics só se aplica a sons que têm uma localização 3D no mundo. Seguindo a [documentação wwise,](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)as propriedades de posicionamento devem ser definidas como mostrado. A configuração **de espacialização 3D** pode ser *posicionada* ou *posicionada + orientação* conforme necessário.

![Configurações recomendadas de posicionamento do Wwise Actor](media/wwise-positioning.png)

Você não pode definir **o Ônibus de Saída** para outro ônibus que se mistura rio acima em Projeto Ônibus *Acústico*. A Wwise impõe essa exigência aos plug-ins do mixer.

Se você quiser que uma criança na hierarquia ator-mixer de passos não use acústica, você pode usar "override parent" nele para optar por ele.

Se você estiver usando envios definidos pelo jogo ou definidos pelo usuário para reverb em qualquer ator-mixer no jogo, desligue-os naquele ator-mixer para evitar aplicar reverb duas vezes.

#### <a name="spatialization"></a>Espacialização
O plug-in do mixer Project Acoustics Wwise aplica o reverb de convolução por padrão, deixando a Wwise para fazer a espacialização panorâmica. Quando você usa o Project Acoustics nesta configuração padrão somente reverb, você pode usar qualquer configuração de canal e método de espacialização em sua mistura seca. Assim, você pode misturar e combinar quase qualquer espacializador com o Projeto Acústico reverb. Suas opções incluem [espacializadores binaurais baseados em Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
O Project Acoustics inclui um espacializador opcional que suporta renderização hrtf de alta resolução baseada em objetos e panorâmica. Selecione a caixa **de seleção Executar espacialização** nas configurações de plug-in do mixer e escolha entre *HRTF* ou *Panning*. Além disso, desativar aux definido pelo usuário envia para todos os ônibus secos para evitar espacializar duas vezes pelo plug-in do mixer Project Acoustics e Wwise. O modo de espacialização não pode ser alterado em tempo real porque requer uma regeneração bancária sólida. Reinicie Unreal e, em seguida, regenere bancos de som antes de selecionar reproduzir para integrar alterações de configuração de plug-in do mixer, como a configuração da caixa de **seleção Executar espacialização.**

![Configurações de espacialização do plug-in do Wwise Mixer](media/mixer-spatial-settings.png)

Infelizmente, outros plug-ins espacializadores baseados em objetos não são suportados no momento. Eles são implementados como plug-ins de mixer, e wwise não permite que vários plug-ins mixer sejam atribuídos a um único ator-mixer.  

### <a name="audio-setup-in-unreal"></a>Configuração de áudio em Unreal
1. Primeiro, você precisa assar seu nível de jogo para produzir um ativo acústico, que será colocado em *Content\Acoustics*. Consulte o [Unreal Bake Tutorial](unreal-baking.md). Alguns níveis pré-cozidos estão incluídos no pacote de amostra.

1. Crie um ator espacial acústico em sua cena. Só crie um desses atores em um nível, porque representa a acústica para todo o nível.

    ![Criação de um ator espacial de Acústica no editor da Unreal](media/create-acoustics-space.png)

1. Atribua o ativo de dados acústicos assados ao slot de dados acústico no ator espacial Acústico. Sua cena agora tem acústica!

    ![Atribuição de ativos de acústica no editor unreal](media/acoustics-asset-assign.png)

1. Adicione um ator vazio. Configurá-lo da seguinte forma.

    ![O editor da Unreal mostra o uso do componente Acústico em um ator vazio](media/acoustics-component-usage.png)

       
    <sup>1</sup> Adicione um componente de áudio acústico ao ator. Este componente adiciona a funcionalidade Project Acoustics ao componente de áudio Wwise.
        
    <sup>2</sup> A **caixa Jogar no Iniciar** é selecionada por padrão. Essa configuração desencadeia um evento Wwise associado na inicialização de nível.</li>
         
    <sup>3</sup> Use a caixa **de seleção Mostrar parâmetros de acústica** para imprimir informações de depuração na tela sobre a fonte.

    ![O painel Unreal editor Acoustics na fonte de som com valores de depuração ativados](media/debug-values.png)

    <sup>4</sup> Atribua um evento Wwise pelo fluxo de trabalho wwise usual.
       
    <sup>5</sup> Certifique-se de que **o uso do áudio espacial** está desligado. Se você usar o Project Acoustics para um determinado componente de áudio, você não poderá usar simultaneamente o motor de áudio espacial da Wwise para acústica.</li>
       
Você está pronto. Mova-se ao redor da cena e explore os efeitos acústicos!

## <a name="next-steps"></a>Próximas etapas
* Experimente o [Project Acoustics Unreal/Wwise Design Tutorial](unreal-workflow.md).
* Aprenda [a fazer bolos](unreal-baking.md) para suas cenas de jogo.
