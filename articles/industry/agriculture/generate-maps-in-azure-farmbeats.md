---
title: Gerar mapas
description: Este artigo descreve como gerar mapas no Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271766"
---
# <a name="generate-maps"></a>Gerar mapas

Usando o Azure FarmBeats, você pode gerar os seguintes mapas usando imagens de satélite e entradas de dados de sensores. Os mapas ajudam você a ver a localização geográfica de sua fazenda e identificar o local apropriado para seus dispositivos.

  - **Mapa de colocação do sensor**: Fornece recomendações sobre quantos sensores usar e onde colocá-los em uma fazenda.
  - **Mapa dos Índices de Satélite**: Mostra o índice de vegetação e o índice de água de uma fazenda.
  - **Mapa de calor da umidade do solo**: Mostra a distribuição da umidade do solo, fundindo dados de satélite e sensores.

## <a name="sensor-placement-map"></a>Mapa de colocação de sensores

Um mapa farmbeats sensor placement ajuda você com a colocação de sensores de umidade do solo. A saída do mapa consiste em uma lista de coordenadas para a implantação do sensor. As entradas desses sensores são usadas juntamente com imagens de satélite para gerar o mapa de calor da umidade do solo.

Este mapa é derivado segmentando o dossel como visto em várias datas ao longo do ano. Até mesmo solo nu e edifícios fazem parte do dossel. Você pode remover sensores que não são necessários no local. Este mapa é para orientação, e você pode alterar a posição e os números ligeiramente com base em seu conhecimento personalizado. Adicionar sensores não vai regredir os resultados do mapa de calor da umidade do solo, mas há uma possibilidade de deterioração na precisão do mapa térmico se o número do sensor for reduzido.

## <a name="before-you-begin"></a>Antes de começar

Conheça os seguintes pré-requisitos antes de tentar gerar um mapa de colocação de sensores:

- O tamanho da fazenda deve ser de mais de um acre.
- O número de cenas sentinelas sem nuvens deve ser superior a seis para a faixa de data selecionada.
- Pelo menos seis cenas do Sentinel sem nuvens devem ter um Índice de Vegetação de Diferença Normalizada (NDVI) maior ou igual a 0,3.

    > [!NOTE]
    > O Sentinel permite apenas dois threads simultâneos por usuário. Como resultado, os empregos ficam na fila e podem levar mais tempo para serem concluídos.

### <a name="dependencies-on-sentinel"></a>Dependências do Sentinel

As seguintes dependências pertencem ao Sentinel:

- Dependemos do desempenho do Sentinel para baixar imagens de satélite. Verifique o estado de desempenho do Sentinel e [as atividades](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)de manutenção .
- O Sentinel permite apenas dois threads de [downloads](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultâneos por usuário.
- A geração de mapas de precisão é afetada pela [cobertura do Sentinel e pela frequência de revisita.]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)

## <a name="create-a-sensor-placement-map"></a>Criar um mapa de colocação de sensores
Esta seção detalha os procedimentos para criar mapas de colocação de sensores.

> [!NOTE]
> Você pode iniciar um mapa de colocação de sensores a partir da página **Mapas** ou no menu suspenso **Gerar mapas** de precisão na página **Detalhes** da fazenda.

Siga estas etapas.

1. Na página inicial, vá para **Mapas** a partir do menu de navegação à esquerda.
2. Selecione **Criar mapas**e selecione Colocação de **sensores** no menu suspenso.

    ![Selecionar colocação do sensor](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Depois de selecionar **A colocação do sensor,** a janela **Decolocação do sensor** será exibida.

    ![Janela de colocação do sensor](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecione uma fazenda no menu suspenso da **Fazenda.**
   Para pesquisar e selecionar sua fazenda, você pode rolar na lista de baixa ou digitar o nome da fazenda na caixa de texto.
5. Para gerar um mapa para o último ano, selecione **Recomendado**.
6. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **Selecionar intervalo de datas**. Digite a data de início e término para a qual deseja gerar o mapa de colocação do sensor.
7. Selecione **Gerar mapas**.
 Uma mensagem de confirmação com detalhes do trabalho aparece.

  Para obter informações sobre o status do trabalho, consulte a seção **Exibir empregos**. Se o status do trabalho *for exibido com falha,* uma mensagem de erro detalhada será exibida na dica de ferramenta do status *Falha.* Neste caso, repita os passos anteriores e tente novamente.

  Se o problema persistir, consulte a seção [Solução de problemas](troubleshoot-azure-farmbeats.md) ou entre em contato com o [fórum Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com registros relevantes.

### <a name="view-and-download-a-sensor-placement-map"></a>Exibir e baixar um mapa de colocação de sensores

Siga estas etapas.

1. Na página inicial, vá para **Mapas** a partir do menu de navegação à esquerda.

    ![Selecione Mapas no menu de navegação à esquerda](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtro** na navegação à esquerda da janela.
  A janela **Filtro** exibe critérios de pesquisa.

    ![Janela do filtro](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione **Valores de tipo,** **data**e **nome** nos menus suspensos. Em seguida, **selecione Aplicar** para procurar o mapa que deseja visualizar.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Role a lista de mapas disponíveis usando as barras de navegação no final da página.
5. Selecione o mapa que deseja visualizar. Uma janela pop-up exibe a visualização do mapa selecionado.
6. Selecione **Download**e baixe o arquivo GeoJSON das coordenadas dos sensores.

    ![Visualização do mapa de colocação do sensor](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa dos Índices de Satélite

As seções a seguir explicam os procedimentos envolvidos na criação e visualização de um mapa de índices de satélite.

> [!NOTE]
> Você pode iniciar um mapa de índices de satélite a partir da página **Mapas** ou no menu suspenso **Gerar mapas** de precisão na página **Detalhes** da fazenda.

O FarmBeats fornece a capacidade de gerar mapas NDVI, Enhanced Vegetation Index (EVI) e Normalized Difference Water Index (NDWI) para fazendas. Esses índices ajudam a determinar como a cultura está crescendo atualmente, ou cresceu no passado, e os níveis representativos de água no solo.


> [!NOTE]
> Uma imagem sentinela é necessária para os dias para os quais o mapa é gerado.


## <a name="create-a-satellite-indices-map"></a>Criar um mapa de índices de satélite

Siga estas etapas.

1. Na página inicial, vá para **Mapas** a partir do menu de navegação à esquerda.
2. Selecione **Criar mapas**e selecione **Índices de satélite** no menu suspenso.

    ![Selecione índices de satélite no menu suspenso](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Depois de selecionar **índices de satélite,** a janela **Índices de Satélite** aparece.

    ![Janela de Índices de Satélite](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecione uma fazenda no menu suspenso.
   Para pesquisar e selecionar sua fazenda, você pode rolar na lista de paradas ou digitar o nome da fazenda.   
5. Para gerar um mapa para a última semana, selecione **Esta Semana**.
6. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **Selecionar intervalo de datas**. Digite a data de início e término para a qual deseja gerar o mapa de índices de satélite.
7. Selecione **Gerar mapas**.
    Uma mensagem de confirmação com detalhes do trabalho aparece.

    ![Mensagem de confirmação do mapa de índices de satélite](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Para obter informações sobre o status do trabalho, consulte a seção **Exibir empregos**. Se o status do trabalho *for exibido com falha,* uma mensagem de erro detalhada será exibida na dica de ferramenta do status *Falha.* Neste caso, repita os passos anteriores e tente novamente.

    Se o problema persistir, consulte a seção [Solução de problemas](troubleshoot-azure-farmbeats.md) ou entre em contato com o [fórum Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com registros relevantes.

### <a name="view-and-download-a-map"></a>Ver e baixar um mapa

Siga estas etapas.

1. Na página inicial, vá para **Mapas** a partir do menu de navegação à esquerda.

    ![Selecione Mapas](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtro** na navegação à esquerda da janela. A janela **Filtro** exibe critérios de pesquisa.

    ![Janela de filtro exibe critérios de pesquisa](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione **Valores de tipo,** **data**e **nome** nos menus suspensos. Em seguida, **selecione Aplicar** para procurar o mapa que deseja visualizar.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.

4. Role a lista de mapas disponíveis usando as barras de navegação no final da página.
5. Para cada combinação de Nome e **Data**da **Fazenda,** os três mapas a seguir estão disponíveis:
    - NDVI
    - Evi
    - RIO NDWI
6. Selecione o mapa que deseja visualizar. Uma janela pop-up exibe a visualização do mapa selecionado.
7. Selecione **Baixar** no menu suspenso para selecionar o formato de download. O mapa é baixado e armazenado em sua pasta local em seu computador.

    ![Visualização do mapa dos índices de satélite selecionados](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Mapa de calor da umidade do solo

A umidade do solo é a água que é mantida nos espaços entre partículas do solo.O mapa de calor da umidade do solo ajuda a entender os dados de umidade do solo em qualquer profundidade, em alta resolução dentro de sua fazenda. Para gerar um mapa de calor preciso e utilizável da umidade do solo, é necessária uma implantação uniforme de sensores. Todos os sensores devem ser do mesmo provedor. Diferentes provedores têm diferenças na forma como a umidade do solo é medida juntamente com diferenças na calibração. O mapa de calor é gerado para uma profundidade específica usando os sensores implantados a essa profundidade.

### <a name="before-you-begin"></a>Antes de começar

Conheça os seguintes pré-requisitos antes de tentar gerar um mapa de calor da umidade do solo:

- Pelo menos três sensores de umidade do solo devem ser implantados. Não tente criar um mapa de calor da umidade do solo antes que os sensores sejam implantados e associados à fazenda.
- A geração de um mapa de calor da umidade do solo é influenciada pela cobertura do caminho do Sentinel, cobertura de nuvens e sombra de nuvens. Pelo menos uma cena sentinela sem nuvens deve estar disponível nos últimos 120 dias, a partir do dia para o qual o mapa de calor da umidade do solo foi solicitado.
- Pelo menos metade dos sensores implantados na fazenda devem estar online e ter fluxo de dados para o datahub.
- O mapa de calor deve ser gerado usando medidas de sensores do mesmo provedor.


## <a name="create-a-soil-moisture-heatmap"></a>Crie um mapa de calor de umidade do solo

Siga estas etapas.

1. Na página inicial, vá para **Mapas** do menu de navegação à esquerda para visualizar a página **Mapas.**
2. Selecione **Criar mapas**e selecione **Umidade do Solo** no menu suspenso.

    ![Selecione umidade do solo no menu suspenso](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Depois de selecionar **umidade do solo,** a janela **de umidade do solo** aparece.

    ![Janela de umidade do solo](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecione uma fazenda no menu suspenso da **Fazenda.**
   Para pesquisar e selecionar sua fazenda, você pode rolar na lista suspenso ou digitar o nome da fazenda no menu **suspenso da fazenda Select.**
5. No **menu Seleto do sensor** de umidade do solo Medir o menu suspenso, selecione a medida do sensor de umidade do solo (profundidade) para a qual deseja gerar o mapa.
Para encontrar a medida do sensor, vá para **Os Sensores**e selecione qualquer sensor de umidade do solo. Em seguida, na seção Propriedades do **sensor,** use o valor em **Measure Name**.
6. Para gerar um mapa para **hoje** ou **esta semana,** selecione uma das opções.
7. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **Selecionar intervalo de datas**. Digite a data de início e término para a qual deseja gerar o mapa de calor da umidade do solo.
8. Selecione **Gerar mapas**.
 Uma mensagem de confirmação com detalhes do trabalho aparece.

   ![Mensagem de confirmação do mapa de umidade do solo](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Para obter informações sobre o status do trabalho, consulte a seção **Exibir empregos**. Se o status do trabalho *for exibido com falha,* uma mensagem de erro detalhada será exibida na dica de ferramenta do status *Falha.* Neste caso, repita os passos anteriores e tente novamente.

    Se o problema persistir, consulte a seção [Solução de problemas](troubleshoot-azure-farmbeats.md) ou entre em contato com o [fórum Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com registros relevantes.

### <a name="view-and-download-a-map"></a>Ver e baixar um mapa

Siga estas etapas.

1. Na página inicial, vá para **Mapas** a partir do menu de navegação à esquerda.

    ![Ir para mapas](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtro** na navegação à esquerda da janela. A janela **Filtro** é exibida de onde você pode procurar mapas.

    ![Selecione Filtro na navegação esquerda](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecione **Valores de tipo,** **data**e **nome** nos menus suspensos. Em seguida, **selecione Aplicar** para procurar o mapa que deseja visualizar. A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Selecione o ícone **Classificar** ao lado dos cabeçalhos da tabela para classificar de acordo com **Farm**, **Date**, **Created On**, **Job ID**e **Job Type**.
5. Role a lista de mapas disponíveis usando os botões de navegação no final da página.
6. Selecione o mapa que deseja visualizar. Uma janela pop-up exibe a visualização do mapa selecionado.
7. Selecione **Baixar** no menu suspenso para selecionar o formato de download. O mapa é baixado e armazenado na pasta especificada.

    ![Visualização do mapa de calor da umidade do solo](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
