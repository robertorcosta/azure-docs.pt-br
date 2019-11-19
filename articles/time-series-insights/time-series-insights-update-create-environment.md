---
title: 'Tutorial: Configurar um ambiente de Versão Prévia do Azure Time Series Insights'
description: 'Tutorial: Saiba como configurar seu ambiente na Versão Prévia do Azure Time Series Insights.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49960ae91688b97a9d38a65e1b17fd277508b996
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114578"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de Versão Prévia do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente PAYG (Pago Conforme o Uso) da Versão Prévia do Azure Time Series Insights.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um ambiente de Versão Prévia do Azure Time Series Insights.
> * Conecte o ambiente de Versão Prévia do Azure Time Series Insights a um Hub IoT.
> * Executar uma amostra do acelerador de solução para transmitir dados para o ambiente da Versão Prévia do Azure Time Series Insights.
> * Executar uma análise básica nos dados.
> * Definir uma hierarquia e um tipo de Modelo de Série Temporal e associá-los às suas instâncias.
> * Use o conector do Power BI e visualize dados no Power BI.

>[!TIP]
> Os [aceleradores de solução de IoT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções de nível empresarial pré-configuradas que você pode usar para acelerar o desenvolvimento de soluções personalizadas de IoT.

Inscreva-se em uma [assinatura do Azure gratuita](https://azure.microsoft.com/free/) se já não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* No mínimo, você deve ter a função de **Colaborador** para a assinatura do Azure. Para obter mais informações, confira [Gerenciar o acesso usando o controle de acesso baseado em função e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivo

Nesta seção, você criará três dispositivos simulados que enviam dados para uma instância do Hub IoT do Azure.

1. Acesse a [página de aceleradores de solução do IoT do Azure](https://www.azureiotsolutions.com/Accelerators). A página exibe vários exemplos pré-criados. Entre usando sua conta do Azure. Em seguida, selecione **Simulação de Dispositivo**.

   [![Página de aceleradores de solução do IoT do Azure](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Na próxima página, selecione **Experimentar agora**.

   [![Página de simulação de dispositivo](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Defina os parâmetros a seguir na página **Criar solução de Simulação de Dispositivo**:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome da implantação** | Insira um valor exclusivo para a criação de um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos. |
    | **Assinatura do Azure** | Selecione a assinatura em que você criará seu ambiente do Time Series Insights. |
    | **Localização do Azure** | Selecione a região em que você pretende armazenar seu ambiente do Time Series Insights. Observe que o simulador de dispositivo só é oferecido em um número limitado de regiões, portanto, se você não vir a região desejada, poderá optar por selecionar uma localização exclusivamente para o tutorial e, em seguida, criar um novo ambiente do TSI quando estiver pronto para passar para a próxima fase de integração.  |
    | **Opções de implantação** | Selecione **Provisionar um novo Hub IoT**. |

    1. Selecione **Criar**.
    [![Página de simulação de dispositivo](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Após cerca de 20 minutos, o acelerador de solução estará pronto.

    [![Página de simulação de dispositivo](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Criar um ambiente de PAYG em versão prévia

Esta seção descreve como criar um ambiente de visualização do Azure Time Series Insights e conectá-lo ao hub IoT criado pelo Acelerador de solução de IoT usando o [portal do Azure](https://portal.azure.com/).

1. Entre no portal do Azure usando sua conta da assinatura.

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Time Series Insights**.

   [![Selecione Internet das Coisas e, em seguida, Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. No painel **Criar ambiente do Time Series Insights**, na guia **Básico**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome exclusivo para o ambiente de Versão Prévia do Azure Time Series Insights. |
    | **Assinatura** | Insira a assinatura em que você deseja criar o ambiente de Versão Prévia do Azure Time Series Insights. É uma melhor prática usar a mesma assinatura que o restante de seus recursos de IoT criados pelo simulador de dispositivo. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo para o recurso de ambiente de Versão Prévia do Azure Time Series Insights. Um grupo de recursos é um contêiner para os recursos do Azure. É uma melhor prática usar o mesmo grupo de recursos que os outros recursos IoT criados pelo simulador de dispositivo. |
    | **Localidade** | Selecione uma região do data center para o seu ambiente de Versão Prévia do Azure Time Series Insights. Para evitar latência adicional, é melhor criar seu ambiente de Versão Prévia do Azure Time Series Insights na mesma região que o Hub IoT criado pelo simulador de dispositivo. |
    | **Camada** |  Selecione **PAYG** (*pagamento conforme o uso*). Essa é a SKU para a Versão Prévia do Azure Time Series Insights. |
    | **ID da Propriedade** | Insira um valor que identifique exclusivamente sua instância de série temporal. O valor inserido na caixa **ID da Propriedade** não poderá ser alterado posteriormente. Para este tutorial, insira **iothub-connection-device-id**. Para saber mais sobre a ID do Time Series, confira [Melhores práticas para a escolha de uma ID do Time Series](./time-series-insights-update-how-to-id.md). |
    | **Nome da conta de armazenamento** | Insira um nome global exclusivo para uma nova conta de armazenamento.|
    |**Habilitar o armazenamento warm**|Selecione **Sim** para habilitar o armazenamento warm.|
    |**Retenção de dados (em dias)**|Escolha a opção padrão de sete dias. |

    Selecione **Avançar: Origem do Evento**.

   [![Painel para criar um ambiente do Time Series Insights](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![Painel para criar um ambiente do Time Series Insights](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

1. Na guia **Origem do Evento**, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma origem do evento?** | Selecione **Sim**.|
   | **Nome** | Insira um valor exclusivo para o nome de origem do evento. |
   | **Tipo de fonte** | Selecione **Hub IoT**. |
   | **Selecione um hub** | Escolha **Selecionar existente**. |
   | **Assinatura** | Selecione a assinatura usada para o simulador de dispositivo. |
   | **Nome do Hub IoT** | Selecione o nome do hub IoT criado para o simulador de dispositivo. |
   | **Política de acesso do Hub IoT** | Selecione **iothubowner**. |
   | **Grupo de consumidores do Hub IoT** | Selecione **Novo**, insira um nome exclusivo e, em seguida, selecione **Adicionar**. O grupo de consumidores deve ser um valor exclusivo na Versão Prévia do Azure Time Series Insights. |
   | **Propriedade de carimbo de data/hora** | Esse valor é usado para identificar a propriedade **Carimbo de data/hora** em seus dados de telemetria de entrada. Para este tutorial, deixe essa caixa em branco. Esse simulador usa o carimbo de data/hora de entrada do Hub IoT, que é usado por padrão pelo Time Series Insights. |

   Selecione **Examinar + criar**.

   [![Configurar uma Fonte de Evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Selecione **Criar**.

    [![Página Revisar + Criar com o botão Criar](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Você pode ver o status da implantação:

    [![Notificação de que a implantação foi concluída](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Caso você seja um proprietário da assinatura do Azure, terá acesso ao ambiente de Versão Prévia do Azure Time Series Insights por padrão. Verifique se você tem acesso:

   1. Pesquise o grupo de recursos e selecione o ambiente da Versão Prévia do Azure Time Series Insights recém-criado. 
      [![Ambiente selecionado](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na página da Versão Prévia do Azure Time Series Insights, selecione **Políticas de Acesso a Dados**: [![Políticas de acesso de dados](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Verifique se suas credenciais estão listadas:

      [![Credenciais listadas](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se as credenciais não estiverem listadas, será necessário conceder permissão a você mesmo para acessar o ambiente selecionando Adicionar e pesquisando pelas suas credenciais. Para saber mais sobre como definir permissões, leia [Conceder o acesso a dados](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Transmitir dados

Agora que você implantou seu ambiente do Time Series Insights, comece a transmitir dados para ele para fins de análise.

1. Vá até a [página de aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/Accelerators). Encontre sua solução no painel do acelerador de solução e selecione **Iniciar**:

    [![Inicializar a solução de simulação de dispositivo](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Selecione **Ir para o acelerador de solução**.

    [![Inicializar a solução de simulação de dispositivo](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Você será redirecionado para a página **Simulação de Dispositivo IoT do Microsoft Azure**. Selecione **Nova simulação** no canto superior direito da página.

    [![Página de simulação de IoT do Azure](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. No painel **Configuração de simulação**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira um nome exclusivo para um simulador. |
    | **Descrição** | Insira uma definição. |
    | **Duração da simulação** | Defina essa opção como **Executar indefinidamente**. |
    | **Modelo do dispositivo** | Clique em + **Adicionar um tipo de dispositivo** <br />**Nome**: Insira **Elevador**. <br />**Valor**: Insira **3**. <br /> Mantenha os valores padrão restantes |
    | **Hub IoT de destino** | Defina essa opção como **Usar o Hub IoT pré-provisionado**. |

    [![Parâmetros para especificar](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecione **Iniciar simulação**.

    No painel de simulação do dispositivo, você verá **Dispositivos ativos** e **Mensagens totais**.

    [![Painel de simulação de IoT do Azure](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analisar dados

Nesta seção, você executa análise básica em seus dados de série temporal usando o [gerenciador de Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Acesse o gerenciador da Versão Prévia do Azure Time Series Insights selecionando a URL na página do recurso no [portal do Azure](https://portal.azure.com/).

    [![A URL do gerenciador da Versão Prévia do Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. No Gerenciador do Time Series Insights, você verá uma barra abrangendo a parte superior da tela. Este é seu seletor de disponibilidade. Verifique se você tem pelo menos dois minutos selecionados e, se necessário, expanda o período selecionando e arrastando os identificadores do seletor para a esquerda e para a direita.

1. Você poderá ver **Instâncias de Série Temporal** à esquerda.


    [![Lista de instâncias órfãs](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecione a primeira instância de série temporal. Em seguida, selecione **Mostrar pressão**.

    [![Instância de série temporal selecionada com o comando de menu para mostrar a pressão média](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Um gráfico da série temporal é exibido. Altere o **Intervalo** para **30s**.

    [![Gráfico de série temporal](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Repita a etapa 3 com as outras duas instâncias de série temporal de modo que você esteja vendo todas as três, conforme mostrado neste gráfico:

    [![Gráfico de todas as séries temporais](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Use o seletor de intervalo no canto superior direito. Aqui você pode selecionar horários de início e término específicos com precisão de milissegundo ou escolher entre as opções pré-configuradas, tais como a última hora. Você também poderá alterar o fuso horário padrão.

    [![Definir o intervalo de tempo como uma hora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Abaixo está uma captura de tela do painel de gráfico depois de executar a simulação por uma hora:

    [![Painel de gráfico](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplicará um modelo para estruturar seus dados. Para concluir o modelo, você definirá tipos, hierarquias e instâncias. Para saber mais sobre modelagem de dados, confira [Modelo de Série Temporal](./time-series-insights-update-tsm.md).

1. No gerenciador, selecione a guia **Modelo**:

   [![Guia Modelo no gerenciador](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Na guia **Tipos**, selecione **Adicionar**.

   [![O botão Adicionar para tipos](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Insira os parâmetros s seguir:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Insira **Elevador** |
    | **Descrição** | Insira **Esta é uma definição de tipo do Elevador** |

    [![O botão Adicionar para tipos](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Em seguida, selecione a guia **Variáveis**. [![Selecionar a guia de variáveis](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Selecione **+ Adicionar Variável** e preencha os valores a seguir para a primeira variável do tipo de Elevador. Você criará três variáveis no total.

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira **Temperatura Média**. |
    | **Tipo** | Selecione **Numérico** |
    | **Valor** | Selecione da predefinição: Selecione **temperatura (Duplo)** . <br /> Observação: Talvez demore alguns minutos até que **Valor** seja preenchido automaticamente depois que a Versão Prévia do Azure Time Series Insights começar a receber eventos.|
    | **Operação de Agregação** | Expanda **Opções Avançadas**. <br /> Selecione **AVG**. |

    [![Seleções para definição da temperatura](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Escolha **Aplicar**.

    Selecione **+ Adicionar Variável** novamente e defina os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira **Vibração Média**. |
    | **Tipo** | Selecione **Numérico** |
    | **Valor** | Selecione da predefinição: Selecione **vibração (Double)** . <br /> Observação: Talvez demore alguns minutos até que **Valor** seja preenchido automaticamente depois que a Versão Prévia do Azure Time Series Insights começar a receber eventos.|
    | **Operação de Agregação** | Expanda **Opções Avançadas**. <br /> Selecione **AVG**. |

    [![Seleções para definição da vibração](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Escolha **Aplicar**.

    Selecione **+ Adicionar Variável** novamente e defina os seguintes valores para a terceira e última variável:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira **Andar**. |
    | **Tipo** | Selecione **Categórica** |
    | **Valor** | Selecione da predefinição: Selecione **Andar (Double)** . <br /> Observação: Talvez demore alguns minutos até que **Valor** seja preenchido automaticamente depois que a Versão Prévia do Azure Time Series Insights começar a receber eventos.|
    | **Categorias** | <span style="text-decoration: underline">Rótulos </span>  - <span style="text-decoration: underline">Valores</span> <br /> Inferiores: 1,2,3,4 <br /> Intermediários: 5,6,7,8,9 <br /> Superiores: 10,11,12,13,14,15 |
    | **Categoria padrão** | Insira **Desconhecido** |

    [![Seleções para definição da vibração](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Escolha **Aplicar**. Você deve ver três variáveis criadas:

    [![Seleções para definição da vibração](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Clique em **Salvar**. Você verá o **Tipo** criado:

    [![Seleções para definição da vibração](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Selecione a guia **Hierarquias**. Selecione **+Adicionar**.

    [![Guia Hierarquias com o botão Adicionar](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. No painel **Editar Hierarquia**, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Insira **Hierarquia de Localizações**. |
   |**Níveis**| Insira **País** como o nome do primeiro nível <br> Selecione **+ Adicionar Nível** <br> Insira **Cidade** para o segundo nível e, em seguida, selecione **+ Adicionar Nível** <br> Insira **Prédio** como o nome do terceiro e último nível |

   Clique em **Salvar**.

    [![Campos de hierarquia com o botão Criar](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Você poderá ver a hierarquia criada:

    [![Informações sobre a hierarquia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Navegue até **Instâncias**. Em **Ações**, na extrema direita, selecione o ícone de lápis para editar a primeira instância com os seguintes valores:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Insira **Elevador 1**|
    | **Descrição** | Insira **Instância do Elevador 1** |

    [![Selecionando o botão Editar de uma instância](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Navegue até **Campos de Instância** e insira o seguinte:

    | Parâmetro | Ação |
    | --- | --- |
    | **Hierarquias** | Selecione **Hierarquia de Localizações** |
    | **País** | Insira **EUA** |
    | **Cidade** | Insira **Seattle** |
    | **Prédio** | Insira **Space Needle** |

    [![Selecionando o botão Editar de uma instância](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Clique em **Salvar**.

1. Repita a etapa 8 com as outras duas instâncias com os seguintes valores:

    <span style="text-decoration: underline;">Para o Elevador 2</span>:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Insira **Elevador 2**|
    | **Descrição** | Insira **Instância do Elevador 2** |
    | **Hierarquias** | Selecione **Hierarquia de Localizações** |
    | **País** | Insira **EUA** |
    | **Cidade** | Insira **Seattle** |
    | **Prédio** | Insira **Centro de Ciência do Pacífico** |

    <span style="text-decoration: underline;">Para o Elevador 3</span>:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Insira **Elevador 3**|
    | **Descrição** | Insira **Instância do Elevador 3** |
    | **Hierarquias** | Selecione **Hierarquia de Localizações** |
    | **País** | Insira **EUA** |
    | **Cidade** | Insira **Nova York** |
    | **Prédio** | Insira **Empire State Building** |

1. Navegue de volta para a guia **Analisar** para exibir o painel de gráfico. Sob **Hierarquia local**, expanda todos os níveis de hierarquia para exibir as instâncias da série temporal:

   [![A guia Analisar](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Em **Centro de Ciência do Pacífico**, selecione a instância de série temporal **Elevador 2** e selecione **Mostrar Temperatura Média**.

    [![Instância de série temporal Mostrar Temperatura Média](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Para a mesma instância, **Elevador 2**, selecione **Mostrar Andar**.

    [![Mostrar Andar na Instância de Série Temporal](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Com sua variável categórica, você pode determinar quanto tempo o elevador gastou nos andares superiores, inferiores e intermediários.

## <a name="clean-up-resources"></a>Limpar recursos

Agora que concluiu o tutorial, limpe os recursos que você criou:

1. No menu à esquerda no [portal do Azure](https://portal.azure.com), selecione **Todos os recursos** e localize o grupo de recursos do Azure Time Series Insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Excluir** ou remova cada recurso individualmente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:  

* Criar e usar um acelerador de simulação de dispositivo.
* Crie um ambiente PAYG de Versão Prévia do Azure Time Series Insights.
* Conecte o ambiente de Versão Prévia do Azure Time Series Insights a um hub IoT.
* Executar uma amostra do acelerador de solução para transmitir dados para o ambiente da Versão Prévia do Azure Time Series Insights.
* Execute uma análise básica dos dados.
* Definir uma hierarquia e um tipo de Modelo de Série Temporal e associá-los às suas instâncias.

Agora que você sabe como criar seu próprio ambiente de Versão Prévia do Azure Time Series Insights, saiba mais sobre os principais conceitos no Azure Time Series Insights.

Leia mais sobre a configuração de armazenamento do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre Modelos do Time Series:

> [!div class="nextstepaction"]
> [Modelagem de dados da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-tsm.md)
