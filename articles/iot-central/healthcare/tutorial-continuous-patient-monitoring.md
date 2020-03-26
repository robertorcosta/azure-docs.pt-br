---
title: Criar um aplicativo de monitoramento contínuo de pacientes no Azure IoT Central | Microsoft Docs
description: Saiba como criar um aplicativo de monitoramento contínuo de pacientes usando os modelos de aplicativo do Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021280"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: Implantar e explorar um modelo de aplicativo de monitoramento contínuo de pacientes



Este tutorial mostra como você, como criador de soluções, pode começar com a implantação de um modelo de aplicativo de monitoramento contínuo de pacientes IoT Central. Você aprenderá a implantar o modelo, saberá o que está incluído como pronto para uso e o que você poderá fazer em seguida.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de aplicativo
> * Explorar o modelo de aplicativo

## <a name="create-an-application-template"></a>Criar um modelo de aplicativo

Navegue até o [site do Gerenciador de Aplicativos do Azure IoT Central](https://apps.azureiotcentral.com/). Selecione **Build** na barra de navegação à esquerda e, em seguida, clique na guia **Assistência médica**. 

>[!div class="mx-imgBorder"] 
>![Gerenciador de aplicativos de assistência médica](media/app-manager-health.png)

Clique no botão **Criar aplicativo** para começar a criar seu aplicativo e, em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft. Você será levado à página **Novo aplicativo**.

![Criar aplicativo de serviços de saúde](media/app-manager-health-create.png)

![Informações de cobrança de Criar aplicativo de serviços de saúde](media/app-manager-health-create-billinginfo.png)

Para criar seu aplicativo:

1. O Azure IoT Central sugere automaticamente um nome de aplicativo com base no modelo selecionado. Aceite esse nome ou insira seu próprio nome de aplicativo amigável, como **Monitoramento Contínuo de Pacientes**. O Azure IoT Central também gera um prefixo de URL exclusivo para você, com base no nome do aplicativo. Você terá a liberdade para alterar esse prefixo de URL para algo mais fácil de memorizar se desejar.

2. Escolha se deseja criar o aplicativo usando o plano de preços *Gratuito* ou um dos planos de preços *Standard*. Os aplicativos criados com o plano Gratuito são gratuitos por sete dias antes de expirarem e permitem o uso gratuito de até cinco dispositivos. Você pode mover um aplicativo do plano Gratuito para um plano de preços Standard a qualquer momento antes que ele expire. Se você escolher o plano Gratuito, precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft. Os aplicativos criados com um plano Standard dão suporte a até dois dispositivos gratuitos e exigem que você insira suas informações de assinatura do Azure para cobrança.

3. Selecione **Criar** no final da página para implantar o aplicativo.

## <a name="walk-through-the-application-template"></a>Explorar o modelo de aplicativo

### <a name="dashboards"></a>Painéis

Depois de implantar o modelo de aplicativo, primeiro, você aterrissará no **painel de monitoramento interno de pacientes da Lamna**. A Lamna Healthcare é um sistema hospitalar fictício que contém dois hospitais: o Woodgrove Hospital e o Burkville Hospital. Neste painel do operador do Woodgrove Hospital, você verá informações e telemetria sobre os dispositivos neste modelo, junto com um conjunto de comandos, trabalhos e ações que você poderá executar. No painel, você pode:

* Ver propriedades e dados de telemetria do dispositivo, como o **nível de bateria** ou o **status de conectividade**.

* Exibir a **planta** e a localização do dispositivo Smart Vitals Patch.

* **Provisionar novamente** o Smart Vitals Patch para um novo paciente.

* Ver um exemplo de **painel do provedor** que a equipe de atendimento de um hospital pode ver para controlar seus pacientes.

* Alterar o **status do paciente** de seu dispositivo para indicar se ele está sendo usado para um cenário interno no paciente ou remoto.

>[!div class="mx-imgBorder"] 
>![Interno no paciente da Lamna](media/lamna-in-patient.png)

Você também pode clicar em **Ir para o painel do paciente remoto** para ver o segundo painel do operador usado pelo Burkville Hospital. Esse painel contém um conjunto semelhante de ações, telemetria e informações. Além disso, você pode ver vários dispositivos sendo usados e pode **atualizar o firmware** de cada um.

>[!div class="mx-imgBorder"] 
>![Lamna remoto](media/lamna-remote.png)

Nos dois painéis, você sempre poderá voltar a esta documentação.

### <a name="device-templates"></a>Modelos de dispositivo

Se clicar na guia **Modelos de dispositivo**, você verá que há dois tipos diferentes de dispositivos que fazem parte do modelo:

* **Smart Vitals Patch**: Este dispositivo representa um adesivo que mede diferentes tipos de sinais vitais. Ele pode ser usado para monitorar pacientes dentro e fora do hospital. Se clicar no modelo, você verá que, além de enviar dados do dispositivo, como o nível da bateria e a temperatura, o patch também envia dados sobre a saúde do paciente, como a taxa respiratória e a pressão arterial.

* **Smart Knee Brace**: este dispositivo representa uma cinta de joelho que os pacientes podem usar ao se recuperar de um cirurgia de substituição do joelho. Se clicar nesse modelo, você verá funcionalidades como amplitude de movimento e aceleração, além dos dados do dispositivo.

>[!div class="mx-imgBorder"] 
>![Modelo do dispositivo Smart Vitals Patch](media/smart-vitals-device-template.png)

Se você clicar na guia **Grupos de dispositivos**, verá também que esses modelos de dispositivos têm grupos de dispositivos criados automaticamente para eles.

### <a name="rules"></a>Regras

Acessando a guia de regras, você verá três regras que existem no modelo de aplicativo:

* **Temperatura alta da cinta**: esta regra é disparada quando a temperatura do Smart Knee Brace é maior que 95&deg;F por um período de 5 minutos. Você pode usar essa regra para alertar o paciente e a equipe de atendimento e resfriar o dispositivo remotamente.

* **Detecção de queda**: esta regra será disparada se for detectada uma queda do paciente. Você pode usar essa regra para configurar uma ação para acionar uma equipe operacional para auxiliar o paciente que caiu.

* **Patch com pouca bateria**: esta regra é disparada quando o nível da bateria do dispositivo é inferior a 10%. Você pode usá-la para disparar uma notificação ao paciente para que ele carregue seu dispositivo.

>[!div class="mx-imgBorder"] 
>![Regra de temperatura alta da cinta](media/brace-temp-rule.png)

### <a name="devices"></a>Dispositivos

Clique na guia **Dispositivos** e, em seguida, selecione uma instância do **Smart Knee Brace**. Você verá que há três exibições para explorar informações sobre o dispositivo específico que você selecionou. Essas exibições são criadas e publicadas ao criar o modelo de dispositivo para seu dispositivo, o que significa que elas serão consistentes em todos os dispositivos que você conectar ou simular.

A exibição de **Painel** fornece uma visão geral da telemetria e das propriedades provenientes do dispositivo que são orientadas pelo operador.

A guia **Propriedades** permite que você edite propriedades de nuvem e as propriedades de leitura/gravação do dispositivo.

A guia **Comandos** permite que você execute comandos que foram modelados como parte de seu modelo de dispositivo.

>[!div class="mx-imgBorder"] 
>![Exibições da cinta de joelho](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não pretenda continuar usando esse aplicativo, exclua-o acessando **Administração > Configurações do aplicativo** e clicando em **Excluir**.

>[!div class="mx-imgBorder"] 
>![Excluir aplicativo](media/admin-delete.png)

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber como criar um painel de provedor que se conecta ao seu aplicativo do IoT Central.

> [!div class="nextstepaction"]
> [Criar um painel do provedor](howto-health-data-triage.md)