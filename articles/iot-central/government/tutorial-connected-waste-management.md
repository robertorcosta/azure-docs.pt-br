---
title: 'Tutorial: Criar um aplicativo de gestão de resíduos conectada com o Azure IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo de gestão de resíduos conectada usando os modelos de aplicativo do Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426353"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Tutorial: Criar um aplicativo de gestão de resíduos conectada no IoT Central



Este tutorial orienta você para a criação de um aplicativo de gestão de resíduos conectada com base no modelo de aplicativo **Gestão de Resíduos Conectada** do Azure IoT Central. 

Neste tutorial, você aprenderá a: 

> [!div class="checklist"]

> * Usar o modelo de **Gestão de Resíduos Conectada** do Azure IoT Central para criar seu aplicativo de gestão de resíduos conectada
> * Explorar e personalizar o painel do operador 
> * Explorar um modelo de dispositivo de lixeira conectada
> * Explorar dispositivos simulados
> * Explorar e configurar regras
> * Configurar trabalhos
> * Personalizar a identidade visual do aplicativo usando white labeling

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:
-  Uma assinatura do Azure é recomendada. Opcionalmente, você pode usar uma avaliação gratuita de sete dias. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Criar aplicativo de Gestão de Resíduos Conectada no IoT Central

Nesta seção, você usará o **modelo de gestão de resíduos conectada** do Azure IoT Central para criar seu aplicativo de gestão de resíduos conectada no IoT Central.

Para criar um aplicativo de gestão de resíduos conectada no Azure IoT Central:  

1. Navegue até o site da [página inicial do Azure IoT Central](https://aka.ms/iotcentral).

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-lo, caso contrário, entre usando uma conta Microsoft:

    ![Insira a conta da sua organização](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Clique em **Compilar** no painel esquerdo e selecione a guia**Governamental**. A página governamental exibe vários modelos de aplicativos governamentais.

    ![Criar modelos de aplicativo governamental](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecione o modelo de aplicativo **Gestão de Resíduos Conectada**. Este modelo inclui um exemplo de modelo de dispositivo de lixeira conectada, um dispositivo simulado, um painel do operador e regras de monitoramento pré-configuradas.    

2. Clique em **Criar aplicativo**, que abrirá o formulário de criação de **Novo aplicativo** com os seguintes campos:
    * **Nome do aplicativo**. Por padrão, o aplicativo usa *Gestão de resíduos conectada* seguido por uma cadeia de caracteres de ID exclusiva gerada pelo IoT Central. Opcionalmente, escolha um Nome de aplicativo amigável. Você também pode alterar o nome do aplicativo mais tarde.
    * **URL** – opcionalmente, você pode escolher a URL desejada. Você também pode alterar a URL mais tarde. 
    * Se você tiver uma assinatura do Azure, insira seu *Diretório, assinatura do Azure e Região*. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de sete dias** e preencher as informações de contato necessárias.  

    Para obter mais informações sobre assinaturas e diretórios, consulte o [criar um início rápido de aplicativo](../core/quick-deploy-iot-central.md).

5. Clique no botão **Criar** na parte inferior da página. 

    ![Página Criar Aplicativo de Resíduos Conectado do Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Informações de cobrança de Criar Conectado do Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Agora, você criou um aplicativo de gestão de resíduos conectada usando o **Modelo de gestão de resíduos conectada** do Azure IoT Central. 

Parabéns! Seu aplicativo recém-criado tem os seguintes itens pré-configurados:
* painéis de operador de exemplo;
* exemplos de modelos de dispositivo de lixeira conectada
* dispositivos simulados conectados de recipiente para lixo
* regras e trabalhos pré-configurados e
* Exemplo de identidade visual usando white labeling 

O aplicativo é seu e você pode modificá-lo a qualquer momento. Agora, vamos explorar o aplicativo e fazer algumas personalizações.  

## <a name="explore-and-customize-operator-dashboard"></a>Explorar e personalizar o painel do operador 
Depois de criar o aplicativo, você será levado ao **Painel de Gestão de resíduos conectada da Wide Waste**.

   ![Painel de Gestão de resíduos conectada](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Como construtor, você pode criar e personalizar modos de exibição no painel para uso dos operadores. Mas antes de tentar personalizar o painel, vamos explorá-lo. 

> [!NOTE]
> Todos os dados exibidos no painel são baseados em dados do dispositivo simulado, que serão explorados na próxima seção. 

O painel é composto por diferentes tipos de blocos:

* ***Bloco de imagem do sistema de coleta de lixo da Wide World Waste***: o primeiro bloco do painel é um bloco de imagem do sistema de coleta de lixo fictício "Wide World Waste". Você pode personalizar o bloco e colocar sua própria imagem ou removê-la. 

* ***Bloco de imagem de lixeira***: você pode usar blocos de imagem e conteúdo para criar uma representação visual do dispositivo que está sendo monitorado, junto com um texto descritivo. 

* ***Bloco de KPI de nível de enchimento***: o bloco exibe um valor relatado por um sensor de *nível de enchimento* em uma lixeira. O sensor de *nível de enchimento* e outros sensores como *medidor de odor* ou *peso* em uma lixeira podem ser monitorados remotamente. Um operador pode tomar medidas, como despachar um caminhão de coleta de lixo. 

*  ***Mapa da área de monitoramento de resíduos***: o mapa está usando o Azure Mapas, que pode ser configurado diretamente no Azure IoT Central. A peça de mapa está exibindo a localização do dispositivo. Tente focalizar o mapa e experimente os controles sobre ele, como ampliar, reduzir ou expandir.

     ![Mapa do painel de Gestão de resíduos conectada](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Gráfico de barras de nível de enchimento, odor e peso**: você pode visualizar um ou vários dados de telemetria de dispositivo em um gráfico de barras. Você também pode expandir o gráfico de barras.  

  ![Gráfico de barras do painel de Gestão de resíduos conectada](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Bloco de conteúdo do Field Services**: o painel inclui um link para integração com o Dynamics 365 Field Services de seu aplicativo do Azure IoT Central. Por exemplo, você pode usar o Field Services para criar tíquetes para expedição de serviços de coleta de lixo. 


### <a name="customize-dashboard"></a>Personalizar painel 

Como construtor, você pode personalizar as exibições no painel para os operadores. Você pode tentar:
1. Clicar em **Editar** para personalizar o **Painel de Gestão de resíduos conectada da Wide World**. Você pode personalizar o painel clicando no menu **Editar**. Quando o painel está no modo de **edição**, você pode adicionar novos blocos ou configurar os blocos existentes 

    ![Editar o painel](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Você também pode clicar em **+ Novo** para criar um painel e configurá-lo do zero. Você pode ter vários painéis e pode navegar entre eles no menu de painel. 

## <a name="explore-connected-waste-bin-device-template"></a>Explorar um modelo de dispositivo de lixeira conectada

Um modelo de dispositivo no Azure IoT Central define a funcionalidade de um dispositivo, que pode ser de telemetria, propriedades ou comando. Como construtor, você pode definir modelos de dispositivo que representam as funcionalidades dos dispositivos que você conectará. 
 

O aplicativo de **Gestão de resíduos conectada** tem um exemplo de modelo de dispositivo de lixeira conectada.

Para exibir do modelo de dispositivo:

1. Clique em **Modelos de dispositivo** no painel esquerdo do aplicativo no IoT Central. 

    ![Modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Na lista Modelos de dispositivo, você verá a **Lixeira Conectada**. Abra clicando no nome.

3. Familiarize-se com as funcionalidades do modelo de dispositivo. Você poderá ver que ele define sensores como *Nível de preenchimento*, *Medidor de odor*, *peso*, *localização*, entre outros.

   ![Modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Personalizando o modelo de dispositivo

Tente personalizar o seguinte:
1. Navegue até **Personalizar** no menu do modelo de dispositivo
2. Encontre o tipo de telemetria `Odor meter`
3. Atualize o **Nome de exibição** de `Odor meter` para `Odor level`
4. Você também pode tentar atualizar a unidade de medida ou configurar o *Valor Mín.* e o *Valor Máx.* .
5. **Salve** quaisquer alterações 

### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem 

1. Navegue até **Propriedade de nuvem** no menu do modelo de dispositivo
2. Adicione uma nova propriedade de nuvem clicando em **+ Adicionar Propriedade de Nuvem**. No IoT Central, você pode adicionar uma propriedade que é relevante para o dispositivo, mas que não se espera que seja enviada por ele. Por exemplo, uma propriedade de nuvem pode ser um limite de alerta específico da área de instalação, informações de ativos ou informações de manutenção, entre outras informações. 
3. **Salve** quaisquer alterações 
 
### <a name="views"></a>Exibições 
* O modelo de dispositivo de lixeira conectada tem exibições predefinidas. Explore as exibições e faça atualizações. As exibições definem como os operadores veem os dados do dispositivo e também definem as propriedades da nuvem. 

  ![Exibições do modelo de dispositivo](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicar 

* Se você fez alterações, não deixe de **Publicar** o modelo de dispositivo. 

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo 

* Selecione **+ Novo** para criar um modelo de dispositivo e siga o processo de criação. Você poderá criar um modelo de dispositivo personalizado do zero ou escolher um no Catálogo de Dispositivos do Azure. 

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

No IoT Central, você também pode criar dispositivos simulados para testar seu aplicativo e modelo de dispositivo. 

O aplicativo de **Gestão de resíduos conectada** tem dois dispositivos simulados mapeados para o modelo de dispositivo de lixeira conectada. 

### <a name="to-view-the-devices"></a>Para exibir os dispositivos:

1. Navegue até **Dispositivo** do painel esquerdo do IoT Central. 

   ![Dispositivos](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selecione e clique no dispositivo de Lixeira Conectada.  

     ![Dispositivo 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navegue até a guia **Propriedades da Nuvem** e tente atualizar o valor `Bin full alert threshold` de `95` para `100`. 
* Explore a guia **Propriedades do Dispositivo** e a guia **Painel do Dispositivo**. 

> [!NOTE]
> Observe que todas as guias foram configuradas das **Exibições de modelo de dispositivo**.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

* Você pode adicionar novos dispositivos clicando em **+ Novo** na guia **Dispositivos**. 

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você pode criar regras para monitorar automaticamente a telemetria do dispositivo e disparar ações quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email, o acionamento de uma ação do Microsoft Flow ou o acionamento de uma ação de webhook para enviar dados a outros serviços.

O aplicativo de **Gestão de resíduos conectada** tem quatro regras de exemplo.

### <a name="to-view-rules"></a>Para exibir as regras:
1. Navegue até **Regras** do painel esquerdo do IoT Central

   ![Regras](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Selecione o alerta de **Lixeira cheia**

     ![Alerta de lixeira cheia](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. O `Bin full alert` verifica quando a **Condição** `Fill level is greater than or equal to Bin full alert threshold`.

    O `Bin full alert threshold` é uma *propriedade de nuvem* definida no modelo de dispositivo `Connected waste bin`. 

Agora, vamos criar uma ação de email.

### <a name="create-an-email-action"></a>Criar uma ação de email
Para configurar uma ação de email na lista de ações da Regra:
1. Selecione **+ Email**. 
2. Insira *Alerta de pH alto* como o **Nome de exibição** amigável para a ação.
3. Insira o endereço de email associado à sua conta do Azure IoT Central em **Para**. 
4. Opcionalmente, insira uma observação a ser incluída no texto do email.
5. Selecione **Concluído** para concluir a ação.
6. Selecione **Salvar** para salvar e ativar a nova regra. 

Você deverá receber um email quando a **condição** configurada for atendida.

> [!NOTE]
> O aplicativo enviará um email sempre que uma condição for atendida. **Desabilite** a regra para parar de receber emails da regra automatizada. 
  
Para criar uma nova regra: 
1. Selecione **+Novo** em **Regras** no painel esquerdo.

## <a name="configure-jobs"></a>Configurar trabalhos

No IoT Central, você pode disparar atualizações de propriedades do dispositivo ou da nuvem em vários dispositivos. Além das propriedades, você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. O IoT Central automatiza o fluxo de trabalho para você. 

1. Acesse **Trabalhos** no painel esquerdo. 
2. Clique em **+Novo** e configure um ou mais trabalhos. 


## <a name="customize-your-application"></a>Personalizar seu aplicativo 

Como um criador, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

### <a name="to-change-the-application-theme"></a>Para alterar o tema do aplicativo:

1. Vá até **Administração > Personalizar seu aplicativo**.
3. Use o botão **Alterar** para escolher uma imagem a ser carregada como o **Logotipo do aplicativo**.
4. Use o botão **Alterar** para escolher uma imagem de **Ícone do navegador** que será exibida nas guias do navegador.
5. Você também pode substituir as **Cores do navegador** padrão adicionando códigos de cores hexadecimais HTML.

   ![Personalizar seu aplicativo no Azure IoT Central](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Você também pode alterar as imagens do aplicativo acessando **Administração > Configurações do aplicativo** e o botão **Selecionar imagem** para escolher uma imagem a ser carregada como a imagem do aplicativo.
7. Por fim, você também pode alterar o **Tema** clicando em **Configurações** na manchete do aplicativo.

  
## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando o aplicativo, exclua-o seguindo estas etapas:

1. Abra a guia Administração no painel esquerdo de seu aplicativo do IoT Central.
2. Selecione Configurações do aplicativo e clique no botão Excluir na parte inferior da página.

  

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [Conceitos da gestão de resíduos conectada](./concepts-connectedwastemanagement-architecture.md)
