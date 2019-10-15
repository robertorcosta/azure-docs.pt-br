---
title: Tutorial de bake do Projeto Acústico do Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve a preparação de acústica com o Projeto Acústico no Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243119"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutorial de bake do Projeto Acústico do Unity
Este tutorial descreve o bake da acústica usando o Projeto Acústico no Unity.

Requisitos de software:
* [Unity 2018.2+](https://unity3d.com) para Windows ou MacOS
* [Plug-in do Projeto Acústico integrado no projeto do Unity](unity-integration.md) ou o [conteúdo de exemplo do Projeto Acústico do Unity](unity-quickstart.md)
* *Opcional:* Uma [conta do Lote do Azure](create-azure-account.md) para acelerar os bakes usando a computação em nuvem

## <a name="open-the-project-acoustics-bake-window"></a>Abra a janela do bake do Projeto Acústico
No Unity, selecione **Acústico** no menu **Janela**.

![O editor do Unity com a opção Acústico realçada no menu Janela](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
O Projeto Acústico usa uma malha de navegação para colocar pontos de investigação do ouvinte para simulação. Use o [fluxo de trabalho da malha de navegação do Unity](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html). Ou use outro pacote de modelagem 3D para criar sua própria malha.

## <a name="mark-acoustic-scene-objects"></a>Marque os objetos da cena acústica
O Projeto Acústico depende de dois tipos de objetos de cena para simulação:
- Os objetos que refletem e ocluem o som na simulação
- A malha de navegação do player que restringe pontos de investigação do ouvinte na simulação

Ambos os tipos de objetos são marcados por meio da guia **Objetos**.

Uma vez simplesmente marcar os objetos adiciona os componentes *AcousticsGeometry* ou *AcousticsNavigation* ao objeto, você também pode usar o [fluxo de trabalho do componente padrão do Unity](https://docs.unity3d.com/Manual/UsingComponents.html)para marcar ou desmarcar os objetos. Só é possível marcar terrenos e renderizadores de malha. Todos os outros tipos de objeto serão ignorados. As caixas de seleção marcam ou desmarcam todos os objetos afetados.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marcar oclusão acústica e geometria de reflexão
Abra a guia **Objetos** da janela **Acústica**. Marque todos os objetos como *Geometria Acústica*, se eles devem ocluir, refletir ou absorver som. A geometria acústica pode incluir itens como piso, paredes, tetos, janelas e vidro das janelas, tapetes e móveis grandes. Você pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelizada antes da simulação, malhas altamente detalhadas, como árvores com muitas folhas pequenas, não são mais caras para o bake do que objetos simplificados.

Não inclua elementos que não devem afetar a acústica, como malhas de colisão invisíveis.

A transformação de um objeto durante o cálculo da investigação (por meio da guia **Investigações**) é corrigida nos resultados do bake. Se um objeto marcado na cena for movido posteriormente, o cálculo da investigação e o bake precisarão ser refeitos.

### <a name="mark-the-navigation-mesh"></a>Marcar a malha de navegação
As malhas de navegação criadas por meio do fluxo de trabalho do Unity serão selecionadas pelo sistema de acústica. Para usar suas próprias malhas, marque-as na guia **Objetos**.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes da guia Objetos
As partes da página da guia (ilustradas após as descrições) são:

1. Os botões de seleção da guia (com a guia **Objetos** selecionada). Use esses botões para percorrer as várias etapas de um bake da acústica, da esquerda para a direita.
1. Uma breve descrição do que você pode fazer usando essa guia.
1. Filtros disponíveis para a janela de hierarquia. Use essas opções para filtrar a janela de hierarquia para os objetos do tipo especificado, de modo que você possa marcá-los com mais facilidade. Caso você ainda não tenha marcado nada para acústica, a seleção das duas últimas opções não mostrará nada. No entanto, essas opções ajudam você a localizar objetos depois de serem marcados.
1. Quando nenhum objeto é selecionado, esta seção mostra o status de todos os objetos na cena.
    * Total: O número total de objetos ativos e não ocultos.
    * Ignorados: O número de objetos que não são terrenos nem renderizadores de malha.
    * Malha: O número de objetos de renderizador de malha.
    * Terreno: O número de objetos de terreno.
    * Geometria: O número de objetos de malha ou terreno marcados como **Geometria Acústica**.
    * Navegação: O número de objetos de malha ou terreno marcados como **Navegação Acústica**. Esse número não inclui a NavMesh do Unity.
1. O número total de objetos "marcáveis" na cena, que são apenas terrenos e renderizadores de malha. Use as caixas de seleção para marcar (adicionar o componente apropriado a) esses objetos como geometria ou navegação para acústica.
1. Quando nada é selecionado, esta observação lembra você de selecionar objetos para marcação, se necessário. Marque também uma ou ambas as caixas de seleção para marcar todos os objetos na cena.
1. Quando objetos são selecionados, esta seção mostra o status apenas dos objetos selecionados.
1. O número total de objetos selecionados "marcáveis". Marcar ou desmarcar as caixas de seleção marca ou desmarca apenas os objetos selecionados.

Se você não tiver nada selecionado na cena, a guia **Objetos** será semelhante à imagem a seguir.

![A guia Objetos Acústicos sem nada selecionado](media/objects-tab-no-selection-detail.png)

Se você tiver algo selecionado na cena ou na janela de hierarquia, a guia será semelhante à imagem a seguir.

![A guia Objetos Acústicos com seleções](media/objects-tab-selection-detail.png)

Se alguns objetos estiverem marcados e outros não, as caixas de seleção apropriadas mostrarão um valor "misto", como a imagem a seguir.

![A guia Objetos Acústicos com uma seleção mista de ícones realçada](media/mixed-object-selection-detail.png)

Marque a caixa de seleção para marcar todos os itens. Desmarque-a para desmarcar todos os objetos.

Objetos podem ser marcados para geometria e navegação.

## <a name="select-acoustic-materials"></a>Selecionar materiais acústicos
Depois que os objetos estiverem marcados, selecione o botão **Materiais**. Em seguida, atribua materiais acústicos. O sistema de materiais do Projeto Acústico está vinculado ao sistema de materiais de visuais do Unity. Para que dois objetos tenham materiais acústicos separados, eles precisam ter materiais visuais separados.

A seleção de material acústico determina a quantidade de energia de som que é refletida novamente de cada superfície. O material acústico padrão tem absorção semelhante ao aço. O Projeto Acústico sugere materiais com base no nome do material visual. Atribua também o material acústico **Personalizado** a um material para ativar um controle deslizante de coeficiente de absorção.

O tempo de reverberação de determinado material em uma sala está relacionado inversamente ao coeficiente de absorção. A maioria dos materiais tem valores de absorção em um intervalo de 0,01 a 0,20. Os materiais que têm coeficientes de absorção fora desse intervalo são muito absorventes.

![Um grafo mostra a correlação negativa do tempo de reverberação e do coeficiente de absorção.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do guia de materiais
![A guia Materiais Acústicos no Unity](media/materials-tab-detail.png)
1. O botão **Materiais** exibe essa guia.
2. Uma breve descrição do que você pode fazer usando essa guia.
3. Quando essa caixa de seleção está marcada, somente os materiais usados por objetos marcados como **Geometria Acústica** são listados. Caso contrário, todos os materiais usados na cena são listados.
4. Use essas opções para alterar a ordem das opções em um menu na coluna **Acústica** (nº 6). Classifique os materiais acústicos por **Nome** ou por **Capacidade de Absorção**, de baixa a alta.
5. Uma lista de materiais classificados em ordem alfabética que são usados na cena. Se a caixa de seleção **Mostrar Somente Marcados** estiver marcada (nº 3), somente os materiais usados pelos objetos marcados como **Geometria Acústica** serão mostrados. Selecione um material aqui para selecionar todos os objetos na cena que usam esse material.
6. O material acústico ao qual o material da cena foi atribuído. Selecione qualquer item para alterar o material acústico atribuído a esse material da cena. Para alterar a ordem de classificação desses menus, use as opções **Classificar Acústica por** (nº 4).
7. O coeficiente de absorção acústica do material selecionado na coluna à esquerda (nº 6). Um valor igual a 0 significa perfeitamente reflexivo (sem absorção), enquanto 1 significa perfeitamente absorvente (sem reflexão). O coeficiente de absorção não pode ser alterado, a menos que o material selecionado seja **Personalizado.**
8. Para um material marcado como **Personalizado**, o controle deslizante é ativado. Mova o controle deslizante ou digite um valor para atribuir um coeficiente de absorção.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e revisar os locais de investigação do ouvinte
Depois de atribuir os materiais, alterne para a guia **Investigações**. Selecione **Calcular** para posicionar os pontos de investigação do ouvinte para simulação.

### <a name="what-the-calculate-button-does"></a>O que o botão "Calcular" faz
O botão **Calcular** usa a geometria da cena acústica selecionada para preparar a cena para simulação:

- Ele usa a geometria das malhas da cena e calcula um *volume de voxel*. O volume de voxel é um volume de toda a cena composto por pequenos "voxels" cúbicos. O tamanho de voxel é determinado pela frequência de simulação, que é controlada pela configuração **Resolução de Simulação**. Cada voxel é marcado como "aberto" ou contendo a geometria da cena. Se um voxel contém geometria, ele é marcado com o coeficiente de absorção do material atribuído a essa geometria.
- Ele usa a malha de navegação para colocar os pontos de investigação do ouvinte. O algoritmo equilibra as preocupações concorrentes da cobertura espacial, do tempo de simulação e do tamanho do arquivo. Ele visa garantir que corredores estreitos e espaços pequenos sempre tenham alguma cobertura. As contagens do ponto de investigação típico variam de 100 para cenas pequenas a alguns milhares de cenas grandes.

Dependendo do tamanho da sua cena e da velocidade da sua máquina, esses cálculos podem levar vários minutos.

### <a name="review-voxel-and-probe-placement"></a>Voxel de revisão e posicionamento da investigação
Visualize os dados de voxel e as localizações do ponto de investigação para garantir que você esteja pronto para implementar a cena. Em geral, uma malha de navegação incompleta ou ausente ou uma geometria acústica extra fica rapidamente visível na visualização. Habilite ou desabilite o posicionamento de investigação e voxel usando o menu **Utensílios**.

![O menu Utensílios no Unity](media/gizmos-menu.png)

Os voxels que contêm geometria acústica são mostrados como cubos verdes. Explore a cena e verifique se tudo o que deve ser geometria tem voxels. A câmera cena deve estar a cerca de 5 metros do objeto para os voxels mostrarem.

Se você comparar os voxels criados com resolução grossa versus resolução fina, verá que os voxels grossos serão duas vezes maiores.

![A visualização de voxels grossos no editor do Unity](media/voxel-cubes-preview.png)

Os resultados da simulação são interpolados entre os locais de ponto de investigação do ouvinte em tempo de execução. Verifique se existem pontos de investigação perto de qualquer lugar em que o player deve entrar na cena.

![A visualização de investigações no editor do Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Tenha cuidado com renomeações de cena
O nome da cena é usado para conectar a cena aos arquivos que armazenam o posicionamento do ponto de investigação e a voxelização. Se você renomear a cena depois que os pontos de investigação forem calculados, os dados de atribuição e posicionamento do material serão perdidos e deverão ser executados novamente.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do guia de testes
![A guia Investigações Acústicas no Unity](media/probes-tab-detail.png)

1. O botão **Investigações** exibe essa guia.
2. Uma breve descrição do que você pode fazer nessa guia.
3. Use essas opções para definir a resolução de simulação grossa ou fina. Grossa é mais rápida, mas há desvantagens. Para obter detalhes, confira [Resolução de bake](bake-resolution.md).
4. Especifica em que local os arquivos de dados acústicos serão colocados. Selecione o botão " **...** " para acessar um seletor de pasta. A localização padrão é *Assets/AcousticsData*. Uma subpasta *Editor* também é criada nessa localização. Para obter mais informações, confira [Arquivos de dados adicionados pelo processo de bake](#Data-Files) mais adiante neste artigo.
5. O prefixo especificado aqui é usado para nomear os arquivos de dados para esta cena. O padrão é "Acústica_ *[Nome da Cena]* ".
6. Depois que as investigações são calculadas, os controles que acabamos de descrever são desabilitados. Selecione o botão **Limpar** para apagar os cálculos e habilitar os controles, de modo que você possa fazer o cálculo novamente com novas configurações.
7. Selecione **Calcular** para voxelizar a cena e calcular as localizações dos pontos de investigação. O cálculo é feito localmente no computador. Ele precisa ser feito antes de fazer um bake.

Nesta versão do Projeto Acústico, as investigações não podem ser colocadas manualmente. Use o processo automatizado na guia **Investigações**.

Para obter mais informações sobre resolução grossa versus fina, confira [Resolução de bake](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Fazer o bake da cena usando o Lote do Azure
Faça o bake da cena em um cluster de computação na nuvem usando o serviço Lote do Azure. O plug-in do Unity do Projeto Acústico conecta-se diretamente ao Lote do Azure para criar uma instância de um cluster do Lote do Azure, gerenciá-lo e destruí-lo para cada bake. Na guia **Bake**, insira suas credenciais do Azure, selecione um tipo e um tamanho de computador de cluster e, em seguida, selecione **Bake**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes da guia Bake
![A guia Bake da Acústica no Unity](media/bake-tab-details.png)

1. O botão **Bake** exibe essa guia.
2. Uma breve descrição do que você pode fazer nessa página.
3. Insira suas credenciais do Azure nesses campos depois que a sua conta do Azure for criada. Para obter mais informações, confira [Criar uma conta do Lote do Azure](create-azure-account.md).
4. O campo da tag de imagem do Docker para o conjunto de ferramentas Acústicas.
5. Abre o portal do Azure para gerenciar suas assinaturas, monitorar o uso e exibir informações de cobrança.
6. Especifica o tipo de nó de computação do Lote do Azure a ser usado para o cálculo. O tipo de nó precisa ter o suporte da localização do datacenter do Azure. Se você não tiver certeza, mantenha-o como **Standard_F8s_v2**.
7. O número de nós a serem usados para o cálculo. Esse número afeta o tempo do bake. Ele é limitado pela alocação de núcleos do Lote do Azure. A alocação padrão só permite dois nós de 8 núcleos ou um nó de 16 núcleos, mas pode ser expandida. Para obter mais informações sobre restrições de alocação de núcleos, confira [Criar uma conta do Lote do Azure](create-azure-account.md).
8. Marque essa caixa de seleção para configurar o pool de computação para que ele use [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm um custo muito menor. Porém, eles nem sempre podem estar disponíveis ou ser tentados novamente a qualquer momento.
9. A contagem de sondas para sua cena, conforme calculada na guia **Sondas**. O número de investigações determina o número de simulações que precisam ser executadas na nuvem. Não é possível especificar mais nós do que investigações.
10. Uma estimativa do tempo que o trabalho levará para ser executado na nuvem, excluindo o tempo de inicialização do nó. Depois que o trabalho começar a ser executado, esse campo mostrará uma estimativa de quanto tempo levará até você obter os resultados.
11. A quantidade total de tempo de computação necessária para executar as simulações. Esse valor é o tempo total de computação do nó que será usado no Azure. Para obter mais informações, confira [Estimar o custo de bake do Azure](#Estimating-bake-cost) mais adiante neste artigo.
12. Esta mensagem informa o local em que os resultados do bake serão salvos quando o trabalho for concluído.
13. *(Somente uso avançado:)* Este botão força o Unity a esquecer um bake enviado. Por exemplo, se você tiver usado os resultados usando outro computador, selecione o botão **Limpar Estado** para esquecer esse trabalho. O arquivo de resultado, quando estiver pronto, *não* será baixado. *Isso não é o mesmo que cancelar o trabalho. O trabalho, se estiver em execução, continuará sendo executado na nuvem.*
14. Selecione este botão para enviar o bake para a nuvem. Enquanto um trabalho está em execução, esse botão se torna **Cancelar Trabalho**.
15. Selecione este botão para preparar o processamento da [simulação de acústica no computador](#Local-bake).
16. Essa área mostra o status do tortas. Quando o bake for concluído, ele mostrará "Baixado".

Você sempre pode obter informações completas sobre os trabalhos ativos, os pools de computação e o armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto um trabalho está em execução, o rótulo do botão **Bake** é alterado para **Cancelar Trabalho**. Use este botão para cancelar o trabalho em andamento. Você deverá confirmar essa opção. Não é possível desfazer o cancelamento de um trabalho. Quando você cancelá-lo, nenhum resultado ficará disponível, mas você ainda será cobrado por qualquer tempo de computação do Azure usado.

Depois de iniciar um bake, feche o Unity. Dependendo do projeto, do tipo de nó e do número de nós, um cozimento na nuvem pode levar várias horas. O status do trabalho de cozimento será atualizado quando você recarregar o projeto e abrir a janela Acústica. Se o trabalho for concluído, o arquivo de saída será baixado.

Por segurança, as credenciais do Azure são armazenadas no computador local e associadas ao editor do Unity. Elas só são usadas para estabelecer uma conexão segura com o Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Localizar o status de um trabalho em execução no portal do Azure

1. Localize a ID do trabalho do bake na guia **Bake**.

    ![A ID do trabalho do bake do Unity realçada na guia Bake](media/unity-job-id.png)  

2. Abra o [portal do Azure](https://portal.azure.com), acesse a conta do Lote usada para o bake e selecione **Trabalhos**.

    ![O link Trabalhos no portal do Azure](media/azure-batch-jobs.png)  

3. Pesquise a ID do trabalho na lista de trabalhos.

   ![O status do trabalho do bake realçado no portal do Azure](media/azure-bake-job-status.png)  

4. Selecione a ID do trabalho para ver o status das tarefas relacionadas e o status geral do trabalho.

   ![O status da tarefa do bake](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a> Estimar o custo de bake do Azure

Para estimar o custo de um bake, comece com o valor do **Custo de Computação Estimado**, que é uma duração. Multiplique esse valor pelo custo por hora na moeda local para o **Tipo de Nó da VM** selecionado. Observe que o resultado não incluirá o tempo do nó necessário para colocar os nós em funcionamento.

Por exemplo, digamos que você selecione **Standard_F8s_v2** para o tipo de nó, que tem um custo de US$ 0,40/h. Se o **Custo de Computação Estimado** for de 3 horas e 57 minutos, o custo estimado para execução do trabalho será de US$ 0,40 * ~4 horas = ~US$ 1,60. O custo real provavelmente será um pouco mais alto devido ao tempo extra para iniciar os nós.

Encontre o custo dos nós por hora em [Preços do Lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Selecione **Computação otimizada** ou **Computação de alto desempenho** como a categoria.)

## <a name="Local-bake"></a> Prepare sua cena no seu PC
Faça também o bake da cena em seu próprio computador. Essa método pode ser útil para fazer experimentos com a acústica em cenas pequenas antes de criar uma conta do Lote do Azure. Observe, porém, que a simulação acústica local pode levar muito tempo, dependendo do tamanho da cena.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
* Um processador x86-64 com pelo menos 8 núcleos e 32 GB de RAM
* [Hyper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) para executar o Docker

Por exemplo, em nosso teste em um computador de 8 núcleos, Intel Xeon E5-1660 @ 3 GHz e 32 GB RAM:
* Uma cena pequena com 100 investigações levou cerca de 2 horas para um bake grosso ou 32 horas para um bake fino.
* Uma cena média com 1.000 investigações levou cerca de 20 horas para um bake grosso ou 21 dias para um bake fino.

### <a name="set-up-docker"></a>Configurar o Docker
Instale e configure o Docker no computador que processará a simulação:
1. Instale o [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Abra as configurações do Docker, acesse **Avançado** e configure os recursos para, pelo menos, 8 GB de RAM. Quanto mais CPUs você puder alocar para o Docker, mais rápido o bake será concluído.  
![Configurações de exemplo do Docker](media/docker-settings.png)
1. Acesse **Unidades Compartilhadas** e ative o compartilhamento para a unidade usada no processamento.  
![Opções de unidade compartilhada do Docker](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Executar o bake local
1. Selecione o botão **Preparar Bake Local** na guia **Bake**. Em seguida, selecione uma localização de pasta para salvar os arquivos de entrada e os scripts de execução. Em seguida, você poderá executar o bake em qualquer computador, desde que ele atenda aos requisitos mínimos de hardware e você instale o Docker copiando a pasta para esse computador.
2. Para iniciar a simulação, execute o script *runlocalbake.bat* no Windows ou o script *runlocalbake.sh* no macOS. Esse script busca a imagem do Docker do Projeto Acústico com o conjunto de ferramentas necessário para o processamento da simulação e inicia a simulação.
3. Após a conclusão da simulação, copie o arquivo *.ace* resultante novamente para o projeto do Unity. Para garantir que o Unity o reconheça como um arquivo binário, acrescente ".bytes" à extensão do arquivo (por exemplo, "Scene1.ace.bytes"). Os logs detalhados da simulação são armazenados em *AcousticsLog.txt.* Se você tiver algum problema, inspecione esse arquivo para ajudar a diagnosticar o problema.

## <a name="Data-Files"></a> Arquivos de dados adicionados pelo processo de bake

Os quatro arquivos de dados a seguir são criados durante o processo de bake. Um contém os resultados da simulação e é fornecido com o seu título. Os outros armazenam dados relacionados ao editor do Unity.

Resultado da simulação:
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes*: Esse arquivo é a tabela de pesquisa do runtime. Ele contém os resultados da simulação e os elementos da cena acústica voxelizados. Altere o nome e a localização desse arquivo na guia **Investigações**.

   *Tome cuidado para não excluir o arquivo de resultado da simulação. Ele não é recuperável, exceto por fazer outro bake da cena.*

Arquivos de dados do editor:
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset*: Esse arquivo armazena os dados inseridos nos campos da interface do usuário do Acústico. Não é possível alterar o nome e a localização desse arquivo.
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox*: Esse arquivo armazena a geometria de acústica voxelizada e as propriedades de material que são calculadas quando você seleciona o botão **Calcular** na guia **Investigações**. Altere o nome e a localização desse arquivo na guia **Investigações**.
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*: Esse arquivo armazena os parâmetros de simulação que são calculados quando você seleciona **Calcular**. Altere o nome e a localização desse arquivo na guia **Investigações**.

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acústica
Arraste o pré-fabricado do **Projeto Acústico** do painel do projeto para a cena:

![O pré-fabricado do Acústico no Unity](media/acoustics-prefab.png)

Selecione o objeto de jogo **ProjectAcoustics** e acesse o painel Inspetor. Especifique a localização do resultado do bake (o arquivo .ace, em *Assets/AcousticsData*): Arraste-a para o script do Gerenciador Acústico ou selecione o botão de círculo ao lado da caixa de texto.

![O pré-fabricado do Gerenciador Acústico no Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Próximas etapas
* Explore os [controles de design do Unity](unity-workflow.md).
* Explore os [conceitos de design do Projeto Acústico](design-process.md).
