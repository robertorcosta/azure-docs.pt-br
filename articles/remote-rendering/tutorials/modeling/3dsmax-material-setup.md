---
title: Configurar materiais de renderização baseada em física no 3DSMax
description: Explica como configurar materiais de renderização baseada em física no 3DSMax e exportá-los para o formato FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857488"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Tutorial: Configurar materiais de renderização baseada em física no 3D Studio Max

## <a name="overview"></a>Visão geral
Neste tutorial, você aprenderá como:

>[!div class="checklist"]
>
> * Atribuir materiais com iluminação avançada aos objetos na cena.
> * Processar a instanciação de objetos e materiais.
> * Exportar uma cena para o formato FBX e selecionar opções importantes.

A criação de [materiais PBR (renderização baseada em física)](../../overview/features/pbr-materials.md) no 3DSMax (3D Studio Max) é uma tarefa relativamente simples. Ela é semelhante em muitas maneiras à configuração de PBR em outros aplicativos de criação de conteúdo, como o Maya. Este tutorial é um guia para a instalação básica do sombreador de PBR e a exportação do FBX para projetos do Azure Remote Rendering.

A cena de exemplo deste tutorial contém vários objetos de caixa poligonais. Diferentes materiais, como madeira, metal, metal pintado, plástico e borracha são atribuídos a eles. Em geral, cada material contém todas ou a maioria das seguintes texturas:

* **Albedo**, que é o mapa de cores do material e também é chamado de **Difuso** ou **BaseColor**.
* **Metalicidade**, que determina se um material é metálico e quais partes dele são metálicas. 
* **Aspereza**, que determina o nível de aspereza ou suavidade de uma superfície.
Também afeta a nitidez ou o desfoque dos reflexos e dos realces em uma superfície.
* **Normal**, que adiciona detalhes a uma superfície sem precisar adicionar mais polígonos. Exemplos de detalhes podem ser furos e entalhes em uma superfície metálica ou textura em madeira.
* **Oclusão de Ambiente**, que é usada para adicionar um sombreamento suave e sombras de contato a um modelo. Trata-se de um mapa em escala de cinza que indica quais áreas de um modelo recebem iluminação total (branco) ou sombreamento total (preto).

## <a name="prepare-the-scene"></a>Preparar a cena
No **3D Studio Max**, o processo de configuração de um material PBR é descrito a seguir.

Para começar, como você verá na cena de exemplo, criamos vários objetos de caixa, cada um representando um tipo de material diferente:

>[!TIP]
>Antes de você começar a criar ativos para o ARR, vale a pena observar que ele usa **metros** para medição.  
>Portanto, recomendamos definir as **Unidades do Sistema** da cena como **Metros**. Além disso, durante a exportação, recomendamos definir as Unidades como metros nas configurações de exportação do FBX.

A imagem a seguir ilustra as etapas necessárias para definir as Unidades do Sistema como metros no 3D Studio Max. No menu principal, acesse **Personalizar** > **Configuração de Unidades** > **Configuração de Unidades do Sistema** e, no menu suspenso **Escala de Unidades do Sistema**, selecione **Metros**. 
![unidades do sistema](media/3dsmax/system-units.jpg)

Com as unidades do sistema definidas como metros, podemos começar a criar nossos modelos. Na cena de exemplo, criamos vários objetos de caixa, cada um representando um tipo de material diferente, por exemplo, metal, borracha, plástico etc. 

>[!TIP]
>Ao criar ativos, é uma boa prática nomeá-los de maneira adequada durante o processo. Isso facilitará a localização deles posteriormente se a cena tiver muitos objetos

![rename-objects](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Atribuir materiais

Com alguns objetos criados na cena, nesse caso, vários cubos, podemos iniciar a configuração de PBR:

* Na barra de ferramentas principal, clique no ícone **Editor de Material**, conforme mostrado na imagem a seguir. Clique também em **M** no teclado para abrir o editor. O editor de material tem dois modos que podem ser selecionados no menu suspenso **Modos**: o modo **Editor de material compacto** e o modo **Material esquemático**. Como essa cena é relativamente simples, usaremos o **Modo compacto**.

* No editor de material, você verá várias esferas; essas esferas são nossos materiais. Atribuiremos um desses materiais a cada objeto (caixa) na cena. Para fazer essa atribuição, primeiro, selecione um dos objetos no visor principal. Com essa seleção feita, clique na primeira esfera na janela do editor de material. Depois de receber um objeto, o material selecionado será realçado conforme mostrado na próxima imagem.

* Clique no botão **Atribuir Material à Seleção**, conforme mostrado. O material selecionado agora foi atribuído ao objeto selecionado.
![assign-material](media/3dsmax/assign-material.jpg)

No Editor de Material, você pode escolher tipos de materiais com base em uma ampla seleção, dependendo do caso de uso. Normalmente, por padrão, o tipo de material é definido como **Padrão**. Esse material é um material básico que não é adequado para a configuração de PBR; portanto, precisaremos alterar o tipo de material para um material PBR. O material preferencial do **3DSMax** para os projetos do Azure Remote Rendering é o **Material Físico**.

* No editor de material, clique na guia **Padrão** e, no navegador de material/mapa exibido, selecione **Material Físico**. Essa ação converterá o material **Padrão** atribuído em um **Material Físico** PBR.
![physical-material](media/3dsmax/physical-material.jpg)

* No editor de material, agora você verá as propriedades do material físico (veja abaixo), e podemos começar a atribuir texturas ao ativo.
![textures-list](media/3dsmax/textures-list.jpg)

Como pode ser visto na imagem acima, há uma ampla variedade de mapas e texturas que podem ser adicionados ao material. Para nossos objetivos, no entanto, usaremos apenas cinco slots de textura no material.

>[!TIP]
>É uma boa prática nomear os materiais de maneira adequada, conforme mostrado na imagem acima.

Agora podemos começar a considerar a possibilidade de atribuir texturas ao material. A maneira como você gera as texturas pode variar de acordo com a preferência ou, até mesmo, com o uso. Por exemplo, você pode estar satisfeito em usar texturas de revestimento que possam ser aplicadas a qualquer ativo ou exigir que partes específicas de um projeto/um ativo tenham o próprio conjunto personalizado de texturas. O ideal é usar texturas de revestimento genéricas obtidas online ou criá-las por conta própria em aplicativos como o **Photoshop**, o **Quixel Suite**, o **Substance Suite** etc. 

No entanto, antes de começarmos a atribuir as texturas, precisaremos considerar as coordenadas de textura de ativos (UVW). Embora, ao aplicar qualquer textura a um modelo, seja uma melhor prática garantir que o modelo tenha sido desempacotado (as texturas não serão exibidas corretamente sem o desempacotamento UV adequado), isso é importante para nossos objetivos, caso pretendamos usar um mapa de **Oclusão de Ambiente** no modelo. Ao contrário do **Sombreador Stingray** do **Maya**, o **Material Físico** do **3DSMax** não tem um slot de ambiente dedicado de **Oclusão de Ambiente**. Portanto, aplicaremos o mapa AO a outro slot e, para permitir que ele seja usado separadamente das outras texturas (texturas de revestimento, por exemplo), atribuiremos a ele um canal de mapa UVW próprio. 

Vamos começar atribuindo um **modificador de Desempacotamento de UVW** ao modelo, conforme mostrado abaixo:

* No editor de propriedades de objetos selecionados, clique na Lista de Modificadores e, no menu suspenso exibido, role a página para baixo e selecione Desempacotar UVW. Essa ação aplicará um modificador de Desempacotamento de UVW ao ativo.
![unwrap-modifier](media/3dsmax/unwrap-modifier.jpg)

* O canal do mapa é definido como um. Ele está no canal um do mapa, no qual normalmente é feito o desempacotamento principal. Em nosso caso, o objeto foi desempacotado sem nenhuma coordenada de textura sobreposta (UV).
![unwrapped-uvw](media/3dsmax/unwrapped-uvw.jpg)

A próxima etapa será criar um segundo canal de mapa UV.

* Feche o editor de UV se ele estiver aberto e, na seção do canal do menu **Editar UVs**, altere o número do canal para dois. O canal 2 do mapa é o canal esperado para mapas de Oclusão de Ambiente. 

* Na caixa de diálogo **Aviso de Alteração de Canal** exibido, você terá a opção de **Mover** o UV existente no canal 1 para o novo canal 2 ou **Abandonar** os UVs existentes, que criará um **Desempacotamento de UV** automaticamente. Apenas selecione **Abandonar** se pretender criar um **Desempacotamento de UV** para o mapa de oclusão de ambiente diferente dos UVs no canal 1 do mapa (por exemplo, se você quiser usar texturas de revestimento no canal 1). Para nossos objetivos, vamos **Mover** o UV do canal um para o canal 2, pois não precisamos editar o novo canal UV.

>[!NOTE]
>Mesmo que você tenha copiado (**Movido**) o desempacotamento de UV do canal 1 do mapa para o canal 2 do mapa, você pode fazer as edições necessárias nos novos UVs do canal sem afetar o canal do mapa original.

![channel-change](media/3dsmax/channel-change.jpg)

Com o canal do mapa criado, podemos voltar ao material físico no editor de material e começar a adicionar nossas texturas a ele. Primeiro, adicionaremos o mapa AO (**oclusão de ambiente**), pois há uma etapa adicional a ser realizada para permitir que ele funcione corretamente. Depois que o mapa AO for conectado ao material, precisaremos instruí-lo a usar o canal 2 do mapa.

* Conforme mencionado anteriormente, não há nenhum slot dedicado para mapas AO no **Material Físico do 3DSMax**. Em vez disso, nós aplicaremos o mapa AO ao slot de **Aspereza Difusa**.

* Na lista **Mapas Genéricos** do material físico, clique no slot **Sem Mapa** de **Aspereza Difusa** e carregue o mapa AO.

* Nas propriedades de texturas de AO, você verá o canal do mapa definido como **1** por padrão. Altere esse valor para **2**. Essa ação conclui as etapas necessárias para adicionar o mapa de oclusão de ambiente.

>[!IMPORTANT]
>Esta é uma etapa importante, especialmente se os UVs do canal 2 forem diferentes daqueles do canal 1, pois a AO não será mapeada corretamente com o canal incorreto selecionado.

![assign-ao-map](media/3dsmax/assign-ao-map.jpg)

Agora, lidaremos com a atribuição do mapa normal ao material PBR. Essa ação é um pouco diferente do **Maya**, pois o mapa normal não é aplicado diretamente ao slot do mapa de rugosidade (não há nenhum slot de mapa normal no **Material Físico do 3DSMax** desse tipo), mas, em vez disso, é adicionado a um modificador de mapa normal, que é conectado ao slot **normais**.

* Na seção **Mapas Especiais** das propriedades do material físico (no editor de material), clique no slot **Sem Mapa** do **Mapa de Rugosidade**. 

* No navegador de material/mapa, localize a opção **Rugosidade Normal** e clique nela. Essa ação adicionará um modificador de **Rugosidade Normal** ao material.

* No modificador de **Rugosidade Normal**, clique em **Sem Mapa** de **Normal** e localize e carregue o mapa normal.

* Verifique se o método está definido como **Tangente** (essa opção deve ser padrão) e, se necessário, alterne **Inverter Verde (Y)** .

![normal-bump](media/3dsmax/normal-bump.jpg)
![load-normal-map](media/3dsmax/load-normal-map.jpg)

Com o mapa normal atribuído corretamente, podemos continuar atribuindo as texturas restantes para concluir a configuração do material físico. Esse processo é simples, sem configurações especiais a serem consideradas. A seguinte imagem mostra o conjunto completo de texturas atribuídas ao material: ![all-textures](media/3dsmax/all-textures.jpg)

Com os materiais PBR criados e configurados, vale a pena pensar em instanciar objetos na cena. Instanciar objetos semelhantes em sua cena, como porcas, parafusos, arruelas e cavilhas, essencialmente, todos os objetos que são iguais, pode reduzir significativamente o tamanho do arquivo. As instâncias de um objeto mestre podem ter a própria escala, rotação e transformações para que possam ser colocadas conforme necessário em sua cena. No **3D Studio Max**, o processo de **instanciação** é simples.

* No visor principal, selecione os objetos que deseja exportar.

* Segure a tecla **SHIFT** e arraste os ativos para cima usando a ferramenta Transformar (mover) 

* Na caixa de diálogo **Opções de Clone** aberta, defina **Objeto** como **Instância** e clique em **OK**. 
![instance-object](media/3dsmax/instance-object.jpg)

Esta ação criará uma instância do objeto que pode ser movida, girada ou dimensionada de maneira independente de seu pai e de outras instâncias desse pai.

>[!IMPORTANT]
>No entanto, todas as alterações feitas a uma instância no modo de subobjeto serão transmitidas para todas as instâncias do objeto; portanto, se você estiver trabalhando com os componentes de objetos instanciados (vértices, faces de polígono etc.), verifique se deseja que todas as alterações feitas afetem todas essas instâncias. Lembre-se de que qualquer objeto instanciado pode ser transformado em um objeto exclusivo a qualquer momento. 

>[!TIP]
>A melhor prática referente à instanciação na cena é criá-los durante o processo, pois a substituição de **Cópias** por objetos instanciados posteriormente é extremamente difícil. 

Um aspecto final a ser levado em consideração para passarmos ao processo de exportação é como empacotar a cena/o ativo para compartilhamento. O ideal é que, se você passar o ativo para um membro do cliente ou da equipe, eles possam abrir e ver o ativo, pois ele deverá ser visto com o mínimo de confusão. Portanto, é importante manter os caminhos de textura dos ativos relativos ao arquivo de cena. Se os caminhos de textura para o ativo estiverem apontando para uma unidade local ou uma localização/um caminho absoluto, eles não serão carregados na cena se estiverem abertos em outro computador, mesmo que o arquivo **.max** esteja localizado na mesma pasta das texturas. Tornar os caminhos de textura relativos no 3D Studio Max resolve esse problema e é bem simples.

* Na barra de ferramentas principal, acesse **Arquivo** > **Referência** > **Alternância de Acompanhamento de Ativos**. 

* No navegador de acompanhamento de ativos aberto, você verá todas ou a maioria das texturas que aplicou aos materiais PBR listados na coluna **Mapas/Sombreadores**.

* Ao lado delas na coluna **Caminho Completo**, você verá o caminho do arquivo para a localização das texturas, mais provavelmente, com a localização no computador local.

* Por fim, você verá uma coluna chamada **Status**. Essa coluna indica se determinada textura foi localizada e foi ou não aplicada à cena e sinalizará essa textura com um destes termos: **OK**, **Encontrado** ou **Arquivo Ausente**. Os dois primeiros indicam que o arquivo foi encontrado e carregado, enquanto o último, obviamente, indica que o rastreador não conseguiu localizar um arquivo.
![texture-paths](media/3dsmax/texture-paths.jpg)

Você poderá observar que nem todas as texturas estão listadas no rastreador de ativos quando você o abre pela primeira vez. Não há motivo de preocupação, pois a execução do processo de localização de caminho uma ou duas vezes geralmente localiza todas as texturas da cena. O processo de localização de caminho é o seguinte: 

* Na janela do rastreador de ativos, selecione **SHIFT**+**clique** na textura superior na lista **Mapas/Sombreadores** e continue segurando a tecla SHIFT e clique na última textura da lista. Essa ação selecionará todas as texturas da lista. As texturas selecionadas agora serão realçadas em azul (confira a imagem acima).

* Clique com o botão direito do mouse na seleção e, no menu pop-up aberto, selecione **Definir Caminho**.

* Na caixa **Especificar Caminho do Ativo** aberta, selecione o caminho local para as texturas mostradas e substitua-o pelo `.\` a seguir e clique em **OK**. 

* Após um período (que varia dependendo de quantas texturas estão na cena e do tamanho da cena), o rastreador de ativos deverá se resolver conforme mostrado a seguir (veja a imagem).
![resolve-textures](media/3dsmax/resolve-textures.jpg)

Observe que a coluna **Caminho Completo** agora está em branco. Isso significa que a cena não está mais procurando encontrar as texturas relevantes em uma localização específica (absoluta), mas sempre as encontrará, desde que o arquivo max ou o arquivo FBX relacionado esteja localizado na mesma pasta das texturas. 

>[!NOTE]
>Às vezes, você pode precisar repetir esse processo algumas vezes para localizar e resolver todas as texturas e os caminhos. Não há motivo de preocupação. Basta repetir a etapa até todos os ativos relevantes serem contabilizados. Também pode acontecer de alguns arquivos não serem mais encontrados. Nesse caso, basta selecionar todos os ativos da lista e clicar em **Remover Caminhos Ausentes** (veja a imagem acima)

## <a name="fbx-export"></a>Exportação do FBX

Com o acompanhamento de ativos concluído, agora podemos passar para a exportação do FBX. Novamente, o processo é simples e pode ser feito de duas maneiras. 

>[!TIP]
>É uma boa prática que, a menos que você queira exportar toda a cena, optar por exportar somente os ativos necessários. Em cenas com uso intensivo de recursos, a exportação poderá levar muito tempo e, portanto, faz sentido exportar apenas o que você precisa
>
>Se você usou modificadores como o **Turbosmooth** ou o **Open SubDiv**, recomendamos recolhê-los antes da exportação, pois eles podem causar problemas durante o processo. Sempre salve a cena antes de fazer isso. 

* Na cena, selecione os ativos que deseja exportar e, na barra de ferramentas principal, acesse **Arquivo** > **Exportar** > **Exportar Selecionado**

* Na caixa de diálogo **Selecionar o Arquivo a ser Exportado**, digite ou selecione o nome do arquivo de saída e, nas opções **Salvar como Tipo**, selecione **Autodesk (*.fbx)** . Essa ação abrirá o menu de exportação do FBX. 

* Lembre-se de que, se você criou instâncias na cena, é importante que a opção **Preservar Instâncias** esteja ativada nas configurações de exportação do FBX. 
![fbx-export](media/3dsmax/fbx-export.jpg)

Lembre-se de que anteriormente foi mencionado que havia duas maneiras de exportar o arquivo. Se a intenção na exportação é que o FBX seja compartilhado junto com os arquivos de texturas em uma pasta/um diretório, as configurações mostradas na imagem abaixo devem ser aplicáveis e funcionar bem. Depois de selecionar as configurações, clique em **OK**.
![fbx-settings](media/3dsmax/fbx-settings.jpg)

No entanto, se você preferir não compartilhar as pastas grandes/os diretórios de texturas junto com o FBX, opte por **Inserir** as texturas no FBX. Isso significa todo o ativo, as texturas incluídas, será adicionado a um só FBX. No entanto, lembre-se de que ele combina a exportação em um só ativo e que o arquivo FBX será consideravelmente maior como resultado disso.

>[!IMPORTANT]
>Se o arquivo FBX resultante for maior que 2,4 GB, a versão mínima das configurações de exportação do FBX (veja acima) deverá ser 2016 ou mais recente. Como as versões mais recentes têm suporte a 64 bits e, portanto, dão suporte a arquivos maiores.

* Nas configurações de exportação do FBX, ative a opção **Inserir Mídia e clique em **OK** para fazer a exportação com as texturas incluídas. 

Durante a exportação para o FBX com o uso do material físico, provavelmente, você verá o seguinte pop-up de aviso depois de clicar em 'OK' na caixa de diálogo Exportar: ![export-warnings](media/3dsmax/export-warnings.jpg)

Esse aviso apenas informa ao usuário que os materiais exportados podem não ser compatíveis com outros pacotes de software. Como o material físico é compatível com o Azure Remote Rendering, não há nada com o que se preocupar. Basta clicar em **OK** para concluir o processo e fechar a janela.

## <a name="conclusion"></a>Conclusão

Em geral, esse tipo de material parece mais realista, pois se baseia na física da luz do mundo real. Ele cria um efeito de imersão adicional, em que a cena parece existir no mundo real.

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como configurar materiais com iluminação avançada para objetos em uma cena. Você também sabe como exportar os objetos para o formato FBX compatível com o Azure Remote Rendering. A próxima etapa será converter o arquivo FBX e visualizá-lo no Azure Remote Rendering.

>[!div class="nextstepaction"]
>[Início Rápido: Converter um modelo para renderização](../../quickstarts\convert-model.md)