---
title: 'Tutorial: Criar um aplicativo de monitoramento do consumo de água com o Azure IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo de consumo de água usando os modelos de aplicativo do Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 0b100e0bebc7c5b26449e396650da5434334075d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112627"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Tutorial: Criar um aplicativo de monitoramento do consumo de água em IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você a criar um aplicativo de monitoramento de consumo de água IoT Central do Azure usando o modelo de aplicativo de monitoramento de consumo de água IoT Central. 

Com o tutorial, você aprenderá a: 

> [!div class="checklist"]
> * Use o modelo de **Monitoramento de consumo de água do Azure IoT Central** para criar seu aplicativo de monitoramento de consumo de água
> * Explorar e personalizar o painel do operador 
> * Explorar o modelo de dispositivo
> * Explorar dispositivos simulados
> * Explorar e configurar regras
> * Configurar trabalhos
> * Personalizar a identidade visual do aplicativo usando white labeling

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:
-  Uma assinatura do Azure é recomendada. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Criar um aplicativo de monitoramento do consumo de água no IoT Central

Nesta seção, usaremos o modelo de **Monitoramento de consumo de água** do Azure IoT Central para criar seu aplicativo de monitoramento de consumo de água no IoT Central.

Para criar um novo aplicativo de monitoramento de consumo de água do Azure IoT Central:  

1. Navegue até o site da [página inicial do Azure IoT Central](https://aka.ms/iotcentral).

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-lo, caso contrário, entre usando uma conta Microsoft:

    ![Insira a conta da sua organização](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Clique em **Compilar** no painel esquerdo e selecione a guia**Governamental**. A página governamental exibe vários modelos de aplicativos governamentais.

   ![Criar modelos de aplicativo governamental](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o  modelo de aplicativo de **monitoramento de consumo de água**. Este modelo inclui um exemplo de modelo de dispositivo de consumo de água, dispositivos simulados, um painel do operador e regras de monitoramento pré-configuradas.    

2. Clique em **Criar aplicativo**, que abrirá o formulário de criação de **Novo aplicativo** com os seguintes campos:
    * **Nome do aplicativo**: como padrão, o aplicativo usa *Monitoramento do consumo da água*, seguido por uma cadeia de caracteres de ID exclusiva gerada pelo IoT Central. Opcionalmente, escolha um Nome de aplicativo amigável. Você também pode alterar o nome do aplicativo mais tarde.
    * **URL**: O IoT Central também gerará uma URL para você, com base no nome do aplicativo. Você pode optar por atualizar a URL de sua preferência. Você também pode alterar a URL mais tarde. 
    * Se você tiver uma assinatura do Azure, insira seu *Diretório, assinatura do Azure e Região*. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de sete dias** e preencher as informações de contato necessárias.  

    Para obter mais informações sobre assinaturas e diretórios, consulte o [criar um início rápido de aplicativo](../preview/quick-deploy-iot-central.md).

5. Clique no botão **Criar** na parte inferior da página. 

    ![Página Criar aplicativo do Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. Agora você criou um aplicativo de monitoramento de consumo de água usando o modelo de **monitoramento de consumo de água** do Azure IoT Central.

Parabéns! Você terminou de criar seu aplicativo de monitoramento de qualidade de água, que vem com o seguinte pré-configurado:
* painéis de operador de exemplo;
* Modelos de dispositivo de fluxo e válvula de água predefinidos de exemplo
* Fluxo de água simulado e dispositivos de válvula inteligente
* regras e trabalhos pré-configurados e
* Exemplo de identidade visual usando white labeling 

O aplicativo é seu e você pode modificá-lo a qualquer momento. Agora, vamos explorar o aplicativo e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explorar e personalizar o painel do operador 
Depois de criar o aplicativo, você é direcionado ao painel do operador de exemplo chamado **painel de monitoramento de consumo de água Wide World**.

   ![Painel de Monitoramento do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Como construtor, você pode criar e personalizar modos de exibição no painel para uso dos operadores. Mas antes de tentar personalizar o painel, vamos explorá-lo. 

> [!NOTE]
> Todos os dados exibidos no painel são baseados em dados do dispositivo simulado, que serão explorados na próxima seção. 
  
O painel é composto por diferentes tipos de blocos:

* **Bloco de imagem do sistema de coleta de lixo da Wide World Water**: o primeiro bloco do painel é um bloco de imagem do sistema de coleta de água fictício "Wide World Water". Você pode personalizar o bloco e colocar sua própria imagem ou removê-la. 

* **Bloco KPI de fluxo de água médio**: o bloco de KPI está configurado para ser exibido como um exemplo *da média nos últimos 30 minutos*. É possível personalizar os blocos de KPI e configurar um deles com tipos e intervalos de tempo diferentes.

* Em seguida, há no painel blocos de *Comando do Dispositivo* para **Fechar válvula**, **Abrir válvula** ou **Definir posição da válvula**. Ao clicar nos comandos, você será levado para a página de comando do dispositivo de dispositivo simulado. No IoT Central um *Comando* é um tipo de *recursos do dispositivo* que exploraremos mais tarde na **seção dispositivo modelo** deste tutorial.

*  **Mapa da área de distribuição de água**: o mapa está usando o Azure Mapas, que pode ser configurado diretamente no Azure IoT Central. A peça de mapa está exibindo a localização do dispositivo. Tente focalizar o mapa e experimente os controles sobre ele, como *ampliar*, *reduzir* ou *expandir*. 

    ![Mapa do painel de Monitoramento do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Gráfico de linhas média de fluxo de água** e **Condições de ambiente**: você pode visualizar um ou vários dispositivos telemetrias plotados como um gráfico de linhas em um intervalo de tempo desejado.  

* **Gráfico do mapa de calor médio da pressão média da válvula**: você pode escolher o tipo de visualização calor de dados de telemetria do dispositivo que está interessado em examinar, na distribuição em um intervalo de tempo com um índice de cores.

* **Redefinir o bloco de conteúdo de limite de alerta**: você pode incluir blocos de conteúdo de chamada de ação inserindo o link para uma página de ação. Nesse caso, redefinir o limite de alerta levará você para o aplicativo **Trabalhos** em que você pode executar atualizações para as propriedades de dispositivos, que exploraremos posteriormente na seção **configurar trabalhos** deste tutorial.

* **Blocos de propriedades**: o painel exibe **Informações operacionais de válvula**, **Limites de alerta de fluxo** e **Informações de manutenção**  que são propriedades do dispositivo.  


### <a name="customize-dashboard"></a>Personalizar painel 

Como construtor, você pode personalizar as exibições no painel para os operadores. Você pode tentar:
1. Clique em **Editar** para personalizar o **painel de monitoramento de consumo de água do Wide World**. Você pode personalizar o painel clicando no menu **Editar**. Quando o painel está no modo de **edição**, você pode adicionar novos blocos ou configurar os blocos existentes 

     ![Editar o painel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Clique em **+ Novo** para criar um novo painel e configurá-lo do zero. Você pode ter vários painéis e pode navegar entre eles no menu de painel. 

## <a name="explore-device-template"></a>Explorar o modelo de dispositivo
Um modelo de dispositivo no Azure IoT Central define a funcionalidade de um dispositivo, que pode ser de telemetria, propriedades ou comando. Como construtor, você pode definir um ou mais modelos de dispositivo no IoT Central que representam as funcionalidades dos dispositivos que você conectará. 
 

O aplicativo de **Monitoramento de consumo de água** vem com dois modelos de dispositivo de referência que representam um *medidor de fluxo* e um dispositivo de *válvula inteligente*. 

Para exibir do modelo de dispositivo:

1. Clique em **Modelos de dispositivo** no painel de navegação esquerdo de seu aplicativo no IoT Central. 
    Na lista de modelos de dispositivo, você verá dois modelos de dispositivo, o **Medidor de Fluxo** e a **Válvula Inteligente**

   ![Modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Clique no modelo de dispositivo **Medidor de fluxo** e familiarize-se com os recursos do dispositivo 

     ![Medidor de fluxo de modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Personalizando o modelo de dispositivo

Tente personalizar o seguinte:
1. Navegue até **Personalizar** no menu do modelo de dispositivo
2. Encontre o tipo de telemetria `Temperature`
3. Atualize o **Nome de exibição** de `Temperature` para `Reported temperature`
4. Atualize a unidade de medida ou configura o *Valor Mín.* e o *Valor Máx.*
5. **Salve** quaisquer alterações 

### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem 
1. Navegue até **Propriedade de nuvem** no menu do modelo de dispositivo
2. Adicione uma nova propriedade de nuvem clicando em **+ Adicionar Propriedade de Nuvem**. 
    No IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo. Por exemplo, uma propriedade de nuvem poderia ser um limite de alerta específico para a área de instalação, informações sobre ativos ou informações de manutenção etc. 
3. **Salve** quaisquer alterações 
 
### <a name="views"></a>Modos de exibição 
O modelo de dispositivo do monitor de consumo de água é fornecido com exibições predefinidas. Explore as exibições e faça atualizações. As exibições definem como os operadores veem os dados do dispositivo e também definem as propriedades da nuvem. 

  ![Exibições do modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicar 
Se você fez alterações, não deixe de **Publicar** o modelo de dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 
- Selecione **+ Novo** para criar um modelo de dispositivo e siga o processo de criação. Você poderá criar um modelo de dispositivo personalizado do zero ou escolher um no Catálogo de Dispositivos do Azure. 

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados
No IoT Central, você também pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo. O aplicativo **Monitoramento de consumo de água** tem dois dispositivos simulados mapeados para os modelos de dispositivo *Medidor de fluxo* e *Válvula Inteligente*. 

### <a name="to-view-the-devices"></a>Para exibir os dispositivos:
1. Navegue até **Dispositivo** do painel de navegação esquerdo do IoT Central. 

   ![Dispositivos](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Clique em uma **Válvula inteligente 1** 

    ![Dispositivo 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  Em **Comandos do Dispositivo** você pode ver três comandos de dispositivo, *Abrir Válvula*, *Fechar Válvula* e *Definir Posição da válvula* que são os recursos definidos no modelo do dispositivo *Válvula Inteligente*. 
4. Explore a guia **Propriedades do Dispositivo** e a guia **Painel do Dispositivo**. 

> [!NOTE]
> Observe que todas as guias foram configuradas das Exibições de modelo de dispositivo.

### <a name="add-new-devices"></a>Adicionar novos dispositivos
* Adicione novos dispositivos clicando em **+ Novo** na guia **Dispositivos**. 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você pode criar regras para monitorar automaticamente a telemetria do dispositivo e disparar ações quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email ou acionamento de uma ação do Microsoft Flow; ou o acionamento de uma ação de webhook para enviar dados a outros serviços.

O aplicativo de **Monitoramento de consumo de água**  que você criou o modelo tem três regras pré-configuradas.

### <a name="to-view-rules"></a>Para exibir as regras:
1. Navegue até **Regras** do painel de navegação esquerdo do IoT Central. 

   ![Regras](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Selecione e clique me **Alerta de pH alto**, que é uma das regras pré-configuradas no aplicativo.

     ![Alerta de pH alto](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A regra `High flow alert` está configurada para verificar se a condição `Acidity (pH)` é `greater than` `Max flow threshold`. Limite máximo de fluxo é uma propriedade de nuvem definida no modelo de dispositivo da *Válvula Inteligente* do dispositivo. O valor de `Max flow threshold` é definido por instância de dispositivo. 

Agora, vamos criar uma ação de email.

Para adicionar uma ação à regra:

1. Selecione **+ Email**. 
1. Insira *Alerta de pH alto* como o **Nome de exibição** amigável para a ação.
    * Insira o endereço de email associado à sua conta do Azure IoT Central em **Para**. 
1. Opcionalmente, insira uma observação a ser incluída no texto do email.
1. Selecione **Concluído** para concluir a ação.
1. Selecione **Salvar** para salvar e ativar a nova regra. 

Em alguns minutos, você deverá receber emails quando a **condição** configurada for atendida.

> [!NOTE]
> O aplicativo enviará um email sempre que uma condição for atendida. **Desabilite** a regra para parar de receber emails da regra automatizada. 
  
Para criar uma nova regra: 
- Selecione **+Novo** nas **Regras** do painel de navegação esquerdo.

## <a name="configure-jobs"></a>Configurar trabalhos

No IoT Central, você pode disparar atualizações de propriedades do dispositivo ou da nuvem em vários dispositivos. Além das propriedades, você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. O IoT Central automatiza o fluxo de trabalho para você. 

1. Vá até **Trabalhos no painel** de navegação esquerdo. 
2. Clique em **+Novo** e configure um ou mais trabalhos. 


## <a name="customize-your-application"></a>Personalizar seu aplicativo 
Como um criador, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

1.  Vá até **Administração > Personalizar seu aplicativo**.
3. Use o botão **Alterar** para escolher uma imagem a ser carregada como o **Logotipo do aplicativo**.
4. Use o botão **Alterar** para escolher uma imagem de **Ícone do navegador** que será exibida nas guias do navegador.
5. Você também pode substituir as **Cores do navegador** padrão adicionando códigos de cores hexadecimais HTML.

   ![Personalizar seu aplicativo no Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Você também pode alterar as imagens do aplicativo acessando **Administração > Configurações do aplicativo** e o botão **Selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo. 
2. Por fim, você também pode alterar o **Tema** clicando em **Configurações** na manchete do aplicativo. 

  
## <a name="clean-up-resources"></a>Limpar recursos

Se você não quiser continuar usando o aplicativo, exclua-o seguindo estas etapas:

1. Abra a guia Administração no painel esquerdo de seu aplicativo do IoT Central. 
2. Selecione Configurações do aplicativo e clique no botão Excluir na parte inferior da página. 


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [Conceitos de monitoramento do consumo de água](./concepts-waterconsumptionmonitoring-architecture.md)
