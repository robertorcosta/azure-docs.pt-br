---
title: Início Rápido do Projeto Acústico com o Unity
titlesuffix: Azure Cognitive Services
description: Use o conteúdo de exemplo para fazer experimentos com os controles de design do Projeto Acústico no Unity e implantá-los no Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243014"
---
# <a name="project-acoustics-unity-quickstart"></a>Início Rápido do Unity do Projeto Acústico
Use o conteúdo de exemplo do Projeto Acústico para Unity para fazer experimento com controles de design com suporte de simulação.

Requisitos de software:
* [Unity 2018.2+](https://unity3d.com) para Windows
* [Pacote de conteúdo de exemplo do Projeto Acústico](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de exemplo?
* Cena do Unity com geometria, fontes de som e controles de jogo
* Plug-in do Projeto Acústico
* Ativos acústicos preparados para a cena

## <a name="import-the-sample-package"></a>Importe o pacote de exemplo
Importe o pacote de exemplo para um novo projeto do Unity.
1. No Unity, acesse **Ativos** > **Importar Pacote** > **Pacote Personalizado**.

    ![As opções de Importar Pacote do Unity](media/import-package.png)  

1. Escolha **ProjectAcoustics.unitypackage**.

1. Selecione o botão **Importar** para integrar o pacote do Unity ao projeto.  
  
    ![A caixa de diálogo Importar Pacote do Unity](media/import-dialog.png)  

Para importar o pacote para um projeto existente, confira [Integração ao Unity](unity-integration.md) para obter etapas e observações adicionais.

>[!NOTE]
>Várias mensagens de erro serão exibidas no log do console depois que a importação for concluída. Continue na próxima etapa e reinicie o Unity.

## <a name="restart-unity"></a>Reinicie o Unity
A parte de bake do kit de ferramentas de acústica exige a versão de runtime do script do .NET 4.*x*. A importação do pacote atualiza as configurações do player do Unity. Reinicialização do Unity para essa configuração tenha efeito. Para verificar se a configuração entrou em vigor, abra as configurações do **Player**:

![O menu Configurações do projeto do Unity](media/player-settings.png)  

![O painel Configurações do Player do Unity com o .NET 4.x selecionado](media/net45.png)  

>[!NOTE]
>Essa captura de tela foi obtida do Unity 2018.*x*. A imagem pode ser diferente em versões mais recentes do Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Abra a janela do bake do Projeto Acústico
No Unity, selecione **Acústico** no menu **Janela**.

![O editor do Unity com a opção Acústico realçada no menu Janela](media/window-acoustics.png)

Uma janela **Acústico** flutuante será aberta. É nessa janela que você define as propriedades da simulação acústica.

![O editor do Unity com a janela Acústico aberta](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Fazer experimentos com os controles de design
Abra a cena de exemplo na pasta *ProjectAcousticsSample* e selecione o botão Reproduzir no editor do Unity. Use as teclas W, A, S, D e o mouse para mover-se. Para comparar o som da cena com e sem acústica, selecione a tecla R até o texto de sobreposição ficar vermelho e mostrar "Acústica: desabilitada". Para ver os atalhos de teclado de outros controles, selecione F1. Clique também com o botão direito do mouse para selecionar uma ação e, em seguida, clique com o botão esquerdo para realizar essa ação.

O script *AcousticsAdjust* é anexado às fontes de som na cena de exemplo. Ele habilita os parâmetros de design por origem.

![O script AcousticsAdjust do Unity](media/acoustics-adjust.png)

As seções a seguir exploram alguns dos efeitos que você pode criar usando os controles disponíveis. Para obter informações detalhadas sobre cada controle, consulte o [Tutorial de Design do Unity do Projeto Acústico](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação de distância
O processamento de sinal digital de áudio fornecido pelo plug-in spatializer do Unity no **Projeto Acústico** respeita a atenuação baseada em distância por origem interna do editor do Unity. Os controles de atenuação baseada em distância estão no componente **Fonte de Áudio**, encontrado no painel **Inspetor** das fontes de som em **Configurações de Som 3D**:

![O painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

O Projeto Acústico faz o cálculo em uma caixa da "região de simulação" centralizada em torno da localização do player. Foi feito o bake dos ativos de acústica do pacote de exemplo em um tamanho de região de simulação de 45 metros em torno do player. Portanto, a atenuação de som deve ser projetada para cair para 0 a cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
* Se o multiplicador de **Oclusão** for maior que 1 (o padrão é 1), a oclusão será exagerada. Para tornar o efeito de oclusão mais sutil, defina-o como inferior a 1.

* Para habilitar a transmissão através da parede, mova o controle deslizante **Transmissão (dB)** para longe da configuração mais baixa.

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma fonte
* Para alterar como a umidade muda rapidamente com a distância, use a **Distorção de Distância Perceptiva**. Por meio de simulação, o Projeto Acústico faz o cálculo de níveis úmidos, que fornecem indicações de distância perceptiva e variam suavemente com a distância. Aumentar a distorção de distância exagera esse efeito aumentando os níveis de umidade relacionados à distância. Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil.

   Para fazer ajustes mais refinados nesse efeito, altere a configuração **Umidade (dB)** .

* Para aumentar o tempo de decaimento em todo o espaço, ajuste a **Escala de Tempo de Decaimento**. Se o resultado da simulação para um par de localização de ouvinte/origem específico for um tempo de decaimento de 1,5 segundo e a **Escala de Tempo de Decaimento** for definida como 2, o tempo de decaimento aplicado à fonte será de 3 segundos.

## <a name="next-steps"></a>Próximas etapas
* Leia os detalhes sobre os [controles de design do Projeto Acústico baseado no Unity](unity-workflow.md).
* Explore mais os conceitos relacionados ao [processo de design](design-process.md).
* [Crie uma conta do Azure](create-azure-account.md) para explorar os processos de pré-bake e bake.
