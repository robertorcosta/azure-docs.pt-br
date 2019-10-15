---
title: Início rápido do Projeto Acústico com o Unreal
titlesuffix: Azure Cognitive Services
description: Use o conteúdo de exemplo para experimentar os controles de design do Projeto Acústico no Unreal e Wwise e implante o Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242914"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Início rápido do Projeto Acústico Unreal/Wwise
Neste início rápido, você experimentará os controles de design do Projeto Acústico usando o conteúdo de exemplo fornecido para o Unreal Engine e o Wwise.

Requisitos de software para usar o conteúdo de exemplo:
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Baixar o pacote de exemplo
Baixe o [pacote de exemplo do Projeto Acústico Unreal e Wwise](https://www.microsoft.com/download/details.aspx?id=58090). O pacote de exemplo contém:
- Projeto Unreal Engine
- Projeto Wwise para o projeto Unreal
- Plug-in Projeto Acústico no Wwise

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurar o projeto de exemplo do Projeto Acústico
Primeiro, instale o plug-in Projeto Acústico no Wwise. Em seguida, implante os binários Wwise no projeto Unreal. Depois, ajuste o plug-in Unreal do Wwise para dar suporte ao Projeto Acústico.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Instale o plug-in Wwise do Projeto Acústico
Abra o inicializador do Wwise. Na guia **Plug-ins**, em **Instalar novos plug-ins**, selecione **Adicionar do diretório**. Escolha o diretório *AcousticsWwisePlugin\ProjectAcoustics* que foi incluído no pacote que você baixou.

![A opção para instalar o plug-in Wwise no inicializador do Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Adicione os binários de Wwise para o projeto de exemplo Unreal do Projeto Acústico
1. No inicializador do Wwise, selecione a guia **Unreal Engine**. 
1. Selecione o menu “hambúrguer” (ícone) ao lado de **Projetos recentes do Unreal Engine** e, em seguida, selecione **Procurar projeto**. Abra o arquivo *.uproject* do projeto Unreal de exemplo no pacote *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![A guia Unreal no inicializador do Wwise](media/wwise-unreal-tab.png)

3. Ao lado do projeto de exemplo do Projeto Acústico, selecione **Integrar Wwise ao Projeto**.

   ![O inicializador do Wwise mostra o projeto Acoustics Game Unreal com a opção Integrar realçada.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estender a funcionalidade de plug-in Unreal do Wwise
O plug-in Unreal do Projeto Acústico requer que o comportamento adicional seja exposto usando a API do plug-in Unreal do Wwise. Execute o arquivo de lote fornecido com o plug-in Unreal do Projeto Acústico para automatizar essas modificações.
* Dentro de *AcousticsGame\Plugins\ProjectAcoustics\Resources*, execute *PatchWwise.bat*.

    ![O script para aplicar patch do projeto Wwise realçado em uma janela do Windows Explorer](media/patch-wwise-script.png)

* Se você não tiver o SDK do DirectX instalado: Dependendo da versão do Wwise que você está usando, talvez seja preciso comentar na linha que contém `DXSDK_DIR` em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![O editor de código mostrando o "DXSDK" comentado](media/directx-sdk-comment.png)

* Se você compilar usando o Visual Studio 2019: Para contornar um erro de vinculação com o Wwise, altere manualmente o valor padrão *VSVersion* em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* para **vc150**:

    ![O editor de código com VSVersion foi alterado para "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Abra o projeto Unreal 
Quando você abrir o projeto Unreal, ele solicitará que você recompile os módulos. Selecione **Sim**.

Se abrir o projeto falhar devido a falhas de build, verifique se você instalou o plug-in Wwise do Projeto Acústico para a mesma versão do Wwise usada no projeto de exemplo do Projeto Acústico.

Se estiver usando uma versão do [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) anterior a 2019.1, você não poderá gerar bancos de som com o Projeto Acústico de exemplo. É necessário integrar o Wwise versão 2019.1 ao projeto de exemplo.

## <a name="experiment-with-project-acoustics-design-controls"></a>Fazer experiências com os controles de design do Projeto Acústico
Ouça como a cena parece selecionando o botão Reproduzir no editor do Unreal. Use as teclas W, A, S, D e o mouse para mover-se. Para ver os atalhos de teclado para controles adicionais, selecione F1.

As informações a seguir descrevem algumas atividades de design a serem experimentadas.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
Há controles de design do Projeto Acústico por fonte em cada ator de som do Unreal.

![Os controles de design de acústica do editor do Unreal](media/demo-scene-sound-source-design-controls.png)

Se o multiplicador de **Oclusão** for maior que 1 (o padrão é 1), a oclusão será exagerada. Uma configuração de menos de 1 torna o efeito da oclusão mais sutil.

Para habilitar a transmissão pela parede, mova o controle deslizante **Transmissão (dB)** para seu nível mais baixo.

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma fonte
Para alterar como a umidade muda rapidamente com a distância, use a **Distorção de Distância Perceptiva**. O Projeto Acústico computa níveis de umidade em todo o espaço por meio da simulação. Os níveis variam um pouco com a distância e fornecem indicações de distância perceptiva. Para exagerar esse efeito, aumente os níveis de umidade relacionados à distância para aumentar a distorção de distância. Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil. Você também pode fazer ajustes mais precisos a esse efeito por meio da configuração **Umidade (dB)** .

Para aumentar o tempo de decaimento por todo o espaço, ajuste a **Escala de Tempo de Decaimento**. Considere o caso em que o resultado da simulação é um tempo de decaimento de 1,5 segundo. Definir a **Escala de Tempo de Decaimento** para 2 resultará em um tempo de decaimento aplicado à fonte de 3 segundos.

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação de distância
O plug-in do mixer Wwise do Projeto Acústico respeita a atenuação baseada em distância por fonte incorporada no Wwise. Alterar essa curva alterará o nível de caminho seco. O plug-in do Projeto Acústico ajustará o nível úmido para manter a combinação da mistura de úmido/seco especificada pela simulação e controles de design.

![O painel da curva de atenuação do Wwise mostrando a atenuação atingindo zero antes do limite de simulação](media/demo-sounds-attenuation.png)

O Projeto Acústico computa em uma caixa de “região da simulação” centralizada em torno de cada localização simulada do player. Foi feito o bake dos ativos de acústica no pacote de exemplo com um raio de região de simulação de 45 metros. As atenuações foram projetadas para se ajustarem a 0 antes de 45 metros. Embora esse declínio não seja um requisito estrito, ele carrega a ressalva de que apenas a geometria dentro de 45 metros do ouvinte ocluirá os sons.

## <a name="next-steps"></a>Próximas etapas
* [Integrar o plug-in Projeto Acústico](unreal-integration.md) no seu projeto Unreal.
* [Criar uma conta do Azure](create-azure-account.md) para fazer seus próprios bakes.
