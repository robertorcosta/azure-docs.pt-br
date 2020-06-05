---
title: Recursos de renderização
description: Os recursos padrão do Lote do Azure são usados para executar cargas de trabalho e aplicativos de renderização. O lote inclui recursos específicos para dar suporte às cargas de trabalho de renderização.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 867dfae570a1e2006b7eea568e3450050f485d9d
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726461"
---
# <a name="azure-batch-rendering-capabilities"></a>Recursos de renderização de Lote do Azure

Os recursos padrão do Azure Batch são usados para executar cargas de trabalho e aplicativos de renderização. O lote também inclui recursos específicos para suportar as cargas de trabalho de renderização.

Para obter uma visão geral dos conceitos em lote, incluindo pools, tarefas e tarefas, consulte [este artigo](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Pools do lote

### <a name="rendering-application-installation"></a>Instalação de aplicativos de renderização

Uma imagem de VM de renderização do Azure Marketplace pode ser especificada na configuração do pool se apenas os aplicativos pré-instalados precisarem ser usados.

Há uma imagem do Windows 2016 e uma imagem do CentOS.  No [Azure Marketplace](https://azuremarketplace.microsoft.com), as imagens da VM podem ser encontradas pesquisando por 'renderização em lote'.

Para um exemplo de configuração do pool, consulte o [tutorial de renderização de CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  O portal do Azure e o Explorador de lotes fornecem ferramentas de GUI para selecionar uma imagem de VM de renderização quando você cria um pool.  Se você estiver usando uma API de lotes, especifique os seguintes valores de propriedade para [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) ao criar um pool:

| Publicador | Oferta | Sku | Versão |
|---------|---------|---------|--------|
| lote | rendering-centos73 | renderização | mais recente |
| lote | rendering-windows2016 | renderização | mais recente |

Outras opções estão disponíveis se aplicativos adicionais forem necessários nas VMs do pool:

* Uma imagem personalizada da Galeria de Imagens Compartilhadas:
  * Usando essa opção, você pode configurar sua VM exatamente com os aplicativos e as versões específicas de que você precisa. Para saber mais, confira [Criar um pool com a Galeria de Imagens Compartilhadas](batch-sig-images.md). A Autodesk e o Chaos Group modificaram o Arnold e o V-Ray, respectivamente, para validar o serviço de licenciamento do Azure Batch. Certifique-se de ter as versões desses aplicativos com esse suporte, caso contrário, o licenciamento de pagamento por uso não funcionará. As versões atuais do Maya ou do 3ds Max não exigem um servidor de licenças durante a execução sem cabeça (no modo de lote / linha de comando). Entre em contato com o suporte do Azure se você não tiver certeza de como proceder com essa opção.
* [Pacotes de aplicativos](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Empacote os arquivos do aplicativo usando um ou mais arquivos ZIP, faça o upload por meio do portal do Azure e especifique o pacote na configuração do pool. Quando as VMs do pool são criadas, os arquivos ZIP são baixados e os arquivos extraídos.
* Arquivos de recurso:
  * Os arquivos do aplicativo são carregados no armazenamento de blobs do Azure e você especifica referências de arquivos na tarefa de [início do pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Quando as VMs do pool são criadas, os arquivos de recurso são baixados em cada VM.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licenças de pagamento por uso para aplicativos pré-instalados

Os aplicativos que serão usados e terão uma taxa de licenciamento precisam ser especificados na configuração do pool.

* Especifique o `applicationLicenses` propriedade quando [criando um pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Os valores a seguir podem ser especificados na matriz de cadeias de caracteres - "vray", "arnold", "3dsmax", "maya".
* Quando você especifica um ou mais aplicativos, o custo desses aplicativos é adicionado ao custo de VMs.  Os preços dos aplicativos estão listados na [Página de preços do Lote do Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Se, em vez disso, você conectar-se a um servidor de licença para usar os aplicativos de renderização, não especifique a propriedade `applicationLicenses`.

Você pode usar o portal do Azure ou o Explorador de lotes para selecionar aplicativos e exibir os preços dos aplicativos.

Se for feita uma tentativa de usar um aplicativo, mas o aplicativo não tiver sido especificado na propriedade `applicationLicenses` da configuração do pool ou não alcançar um servidor de licença, a execução do aplicativo falhará com um erro de licenciamento e um código de saída diferente de zero.

### <a name="environment-variables-for-pre-installed-applications"></a>Variáveis de ambiente para aplicativos pré-instalados

Para poder criar a linha de comando para tarefas de renderização, o local de instalação dos executáveis do aplicativo de renderização deve ser especificado.  As variáveis de ambiente do sistema foram criadas nas imagens da VM do Azure Marketplace, que podem ser usadas em vez de precisar especificar caminhos reais.  Essas variáveis de ambiente são adicionais às [variáveis de ambiente padrão do lote](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) criadas para cada tarefa.

|Aplicativo|Executável de aplicativo|Variável de ambiente|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Grupo de caos V-Ray Standalone|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 command line|kick.exe|ARNOLD_2017_EXEC|
|Linha de comando Arnold 2018|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure VM families

Assim como acontece com outras cargas de trabalho, a renderização dos requisitos do sistema de aplicativos varia e os requisitos de desempenho variam para tarefas e projetos.  Uma grande variedade de famílias de VMs está disponível no Azure, dependendo de seus requisitos - menor custo, melhor preço / desempenho, melhor desempenho e assim por diante.
Alguns aplicativos de renderização, como o Arnold, são baseados em CPU; outros, como o V-Ray e o Blender Cycles, podem usar CPUs e / ou GPUs.
Para obter uma descrição de famílias de VM disponíveis e tamanhos de VM [Consulte tipos de VM e tamanhos](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>VMs de baixa prioridade

Assim como em outras cargas de trabalho, as VMs de baixa prioridade podem ser utilizadas em conjuntos de lotes para renderização.  As VMs de baixa prioridade têm o mesmo desempenho que as VMs dedicadas regulares, mas utilizam a capacidade excedente do Azure e estão disponíveis para um grande desconto.  A desvantagem do uso de VMs de prioridade baixa é que elas podem não estar disponíveis para serem alocadas ou podem admitir preempção a qualquer momento, dependendo da capacidade disponível. Por esse motivo, as VMs de baixa prioridade não serão adequadas para todos os trabalhos de renderização. Por exemplo, se as imagens demoram muitas horas para serem renderizadas, é provável que a renderização dessas imagens interrompidas e reiniciadas devido ao fato de as VMs estarem sendo aprovadas não seja aceitável.

Para obter mais informações sobre as características das VMs de baixa prioridade e as várias maneiras de configurá-las usando o Lote, consulte [Usar VMs de prioridade baixa com Lote](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas

Não há suporte de renderização específica é necessária para trabalhos e tarefas.  O item de configuração principal é a linha de comando da tarefa, que precisa referenciar o aplicativo necessário.
Quando as imagens de VM do Azure Marketplace são usadas, a prática recomendada é usar as variáveis de ambiente para especificar o caminho e o executável do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para exemplos de renderização de Lote, experimente os dois tutoriais:

* [Renderizar usando a CLI do Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderizar usando o Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
