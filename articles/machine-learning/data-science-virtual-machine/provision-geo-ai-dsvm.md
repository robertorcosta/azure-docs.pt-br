---
title: 'Início Rápido: Criar uma Máquina Virtual de Ciência de Dados de IA Geográfica'
titleSuffix: Azure Data Science Virtual Machine
description: Configure e crie uma Máquina Virtual de Ciência de Dados de IA Geográfica no Azure para realizar a análise geoespacial e o aprendizado de máquina.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 61d401a543032b1a206a4477f04d7e8e209c0f2a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646602"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Início Rápido: Configurar uma Máquina Virtual de Inteligência Artificial Geográfica no Azure 

A Geo-DSVM (Máquina Virtual de Ciência de Dados de IA Geográfica) é uma extensão da popular [Máquina Virtual de Ciência de Dados do Azure](https://aka.ms/dsvm) configurada especialmente para combinar a IA com análise geoespacial. A análise geoespacial na VM é fornecida pelo [ArcGIS Pro](https://www.arcgis.com/features/index.html). A DSVM (Máquina Virtual de Ciência de Dados) permite o treinamento rápido de modelos de machine learning e até mesmo de aprendizado profundo. Para desenvolver esses modelos, ela usa dados aprimorados com informações geográficas. A Geo-DSVM é compatível apenas com a Windows 2016 DSVM. 

As ferramentas de IA incluídas na Geo-DSVM incluem o seguinte:

- Edições de GPU de estruturas de aprendizado populares, como o Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 e Chainer
- Ferramentas para adquirir e pré-processar dados textuais e de imagens
- Ferramentas para atividades de desenvolvimento, como Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter Notebooks para Python e R, IDEs para Python e R e bancos de dados SQL
- Software de área de trabalho ArcGIS Pro da Esri, com interfaces do Python e R que podem trabalhar com os dados geoespaciais dos aplicativos de IA
 

## <a name="create-your-geo-ai-data-science-vm"></a>Criar sua VM de Ciência de Dados de IA Geográfica

Para criar uma instância da VM de Ciência de Dados de IA Geográfica, siga estas etapas:

1. Acesse a listagem de máquinas virtuais no [portal do Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Selecione **Criar** na parte inferior para gerar um assistente:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. O assistente requer entradas para cada uma das quatro etapas. Para obter informações detalhadas sobre essas entradas, confira a seção a seguir.

### <a name="wizard-details"></a>Detalhes do assistente ###

**Noções básicas**:

- **Name**: O nome do servidor de ciência de dados que está sendo criado.
    
- **Nome de Usuário**: a ID de entrada da conta do administrador.
    
- **Senha**: Senha da conta do administrador.
    
- **Assinatura**: Se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado.
    
- **Grupo de recursos**: É possível criar um novo ou usar um grupo de recursos existente **vazio** do Azure na assinatura.
    
- **Localização**: Selecione o data center mais apropriado. Normalmente, é o que contém a maioria dos seus dados ou que está mais próximo de sua localização física para oferecer acesso mais rápido à rede. Se você planeja executar um aprendizado profundo na GPU, escolha um dos locais no Azure que têm as instâncias de VM da GPU da Série NC. Atualmente, esses locais são: **Leste dos EUA, Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA 2, Norte da Europa, Europa Ocidental**. Para obter a lista mais recente, verifique os [Produtos do Azure pela página da região](https://azure.microsoft.com/regions/services/) e procure **Série NC** em **Computação**. 
    
    
**Configurações**: Selecione um dos tamanhos de máquina virtual da GPU da Série NC, caso planeje executar um aprendizado profundo em uma GPU da DSVM Geográfica. Caso contrário, escolha uma das instâncias baseadas em CPU. Crie uma conta de armazenamento para sua VM. 
       
**Resumo**: Verifique se todas as informações inseridas estão corretas.
    
**Comprar**: para iniciar o processo de provisionamento, clique em **Comprar**. Um link para os termos do serviço é fornecido. A VM não tem encargos adicionais além dos custos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**. 
 
 >[!NOTE]
 > O provisionamento deve demorar cerca de 20 a 30 minutos. O status do provisionamento é exibido no Portal do Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Como acessar a Máquina Virtual de Ciência de Dados de IA Geográfica

 Depois de criar a VM, você estará pronto para começar a usar as ferramentas que estão instaladas e pré-configuradas nela. Há blocos do menu Iniciar e ícones da área de trabalho para várias das ferramentas. Você poderá acessar a VM pela Área de Trabalho Remota usando as credenciais da conta do administrador configurada anteriormente na seção **Informações básicas**.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Usando o ArcGIS Pro instalado na VM

Na Geo-DSVM, a área de trabalho do ArcGIS Pro é pré-instalada e o ambiente é pré-configurado para trabalhar com todas as ferramentas na DSVM. Quando você iniciar o ArcGIS, será solicitado que forneça credenciais de sua conta do ArcGIS. Se você já tiver uma conta do ArcGIS e as licenças do software, use suas credenciais existentes.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Caso contrário, inscreva-se para uma nova conta do ArcGIS e a licença ou obtenha uma [avaliação gratuita](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Após inscrever-se em uma conta paga ou de avaliação gratuita do ArcGIS, você poderá autorizar o ArcGIS Pro da sua conta seguindo as instruções em [Introdução ao ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Depois de entrar na área de trabalho do ArcGIS Pro com sua conta do ArcGIS, você estará pronto para começar a usar as ferramentas de ciência de dados instaladas e configuradas na VM para seus projetos de machine learning e análise geoespacial.

## <a name="next-steps"></a>Próximas etapas

Comece a usar a VM de Ciência de Dados de IA Geográfica com diretrizes do seguinte recurso:

* [Usar a VM de Ciência de Dados de IA Geográfica](use-geo-ai-dsvm.md)
