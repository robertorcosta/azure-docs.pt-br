---
title: Gerenciar fazendas
description: Descreve como gerenciar fazendas
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271714"
---
# <a name="manage-farms"></a>Gerenciar farms

Você pode gerenciar suas fazendas em Azure FarmBeats. Este artigo fornece informações sobre como criar fazendas, instalar dispositivos, sensores e drones que ajudam você a gerenciar suas fazendas.

## <a name="create-farms"></a>Criar fazendas

Use as seguintes etapas:

1. Faça login no Farm Accelerator, a página **Fazendas** é exibida.
    A página **Fazendas** exibe a lista de fazendas caso elas já tenham sido criadas em assinatura.

    Aqui está a imagem da amostra:

    ![Projeto do FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Selecione **Criar Fazenda** e forneça **Nome,** **Culturas** e **Endereço.**
3. No **Definir Limite da Fazenda**, (campo obrigatório) selecione **Marca no Mapa** ou Cole **código GeoJSON**.

Aqui estão as duas maneiras de definir um limite de fazenda:

1. **Marca no Mapa**: Use a ferramenta de controle do mapa para desenhar e marcar o limite da fazenda. Para marcar os ![limites,](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) o Project Farm Beats e marcar os limites exatos.

    ![Projeto do FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Código GeoJson**de pasta : O GeoJSON é um formato para codificação de estruturas de dados geográficos, usando A notação de objeto JavaScript (JSON). Esta opção exibe uma caixa de texto onde uma seqüência GeoJSON pode ser inserida para marcar os limites da fazenda. Você também pode criar o código GeoJSON a partir de GeoJSON.io.
Use as dicas de ferramentas para ajudar a preencher as informações.

    ![Projeto do FarmBeats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Selecione **Enviar** para criar uma fazenda. Uma nova fazenda é criada e exibida na página **Fazendas.**

## <a name="view-farm"></a>Ver fazenda

A página da lista Fazenda exibe uma lista de fazendas criadas. Selecione uma fazenda para visualizar a lista de:

 - **Contagem de** dispositivos — exibe o número e o status dos dispositivos implantados dentro da fazenda.
 - **Mapa** — mapa da fazenda com os dispositivos implantados na fazenda.
 - **Telemetria** — exibe a telemetria dos sensores implantados na fazenda.
 - **Mapas de precisão mais recentes** — exibem o mais recente mapa de índices de satélite (EVI, NDWI), mapa de calor de umidade do solo e mapa de colocação de sensores.

## <a name="edit-farm"></a>Editar fazenda

A página **Fazendas** exibe uma lista de fazendas criadas.

1.  Selecione uma fazenda para visualizar e editar a fazenda.
2.  Selecione **Editar Fazenda** para editar as informações da fazenda. Na janela Detalhes da **Fazenda,** você pode editar **Nome,** **Culturas,** **Endereço**e definir campos de Limite **de Fazenda.**

    ![Projeto do FarmBeats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Selecione **Enviar** para salvar os detalhes editados.

## <a name="delete-farm"></a>Excluir fazenda

A página **Fazendas** exibe uma lista de fazendas criadas. Use as seguintes etapas para excluir uma fazenda:

1.  Selecione uma fazenda da lista para excluir os detalhes da fazenda.
2.  Selecione **Excluir Fazenda** para excluir a fazenda.

    ![Projeto do FarmBeats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Quando você exclui uma fazenda, os dispositivos e mapas associados à fazenda não são excluídos. Qualquer informação da fazenda associada ao dispositivo e aos mapas não será relevante. Você pode continuar a ver dispositivos, telemetria e mapas do serviço FarmBeats.


## <a name="next-steps"></a>Próximas etapas

Agora que você criou sua fazenda, aprenda a [obter dados de sensores](get-sensor-data-from-sensor-partner.md) fluindo para sua fazenda.
