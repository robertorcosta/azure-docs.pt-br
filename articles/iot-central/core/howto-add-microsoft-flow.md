---
title: Usar o conector de IoT Central do Azure no Microsoft Flow | Microsoft Docs
description: Use o conector de IoT Central no Microsoft Flow para disparar fluxos de trabalho e criar, obter, atualizar, excluir dispositivos e executar comandos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 42e79a03f5e3ba0e3e39cd37d9911b3a150cf175
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985451"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Criar fluxos de trabalho com o conector da IoT Central no Microsoft Flow

*Este tópico aplica-se a construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Use o Microsoft Flow para automatizar fluxos de trabalho entre os vários aplicativos e serviços dos quais os usuários empresariais dependem. Usando o conector de IoT Central no Microsoft Flow, você pode disparar fluxos de trabalho quando uma regra é acionada na IoT Central. Em um fluxo de trabalho disparado por IoT Central ou qualquer outro aplicativo, você pode usar as ações no conector de IoT Central para:
- Criar um dispositivo
- Obter informações do dispositivo
- Atualizar as propriedades e configurações de um dispositivo
- Executar um comando em um dispositivo
- Excluir um dispositivo

Confira [estes modelos do Microsoft Flow](https://aka.ms/iotcentralflowtemplates) que conectam a IoT Central a outros serviços, tais como notificações móveis e o Microsoft Teams.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo criado usando um plano de preços padrão
- Uma conta pessoal ou corporativa ou de estudante da Microsoft para usar Microsoft Flow ([saiba mais sobre planos de Microsoft Flow](https://aka.ms/microsoftflowplans))
- Uma conta corporativa ou de estudante para usar o conector de IoT Central do Azure

## <a name="trigger-a-workflow"></a>Disparar um fluxo de trabalho

Esta seção mostra como disparar uma notificação móvel no aplicativo móvel do Flow quando uma regra é disparada no IoT Central. Você pode criar todo o fluxo de trabalho em seu aplicativo IoT Central usando o designer de Microsoft Flow incorporado.

1. Comece [criando uma regra na IoT Central](howto-create-telemetry-rules.md). Depois de salvar as condições de regra, selecione a **ação Microsoft Flow** como uma nova ação. Uma janela de diálogo será aberta para que você configure seu fluxo de trabalho. A conta de usuário IoT Central à qual você está conectado será usada para entrar no Microsoft Flow.

    ![Criar uma nova ação do Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Você verá uma lista de fluxos de trabalho aos quais você tem acesso e está anexado a esta regra de IoT Central. Clique em **explorar modelos** ou em **novo > criar a partir do modelo** e você pode escolher entre qualquer um dos modelos disponíveis. 

    ![Modelos de Microsoft Flow disponíveis](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Você será solicitado a entrar nos conectores no modelo escolhido. 

    > [!NOTE]
    > Para usar o conector de IoT Central do Azure, você deve entrar usando uma conta de Azure Active Directory (conta corporativa ou de estudante). Uma conta pessoal, como abc@outlook.com ou abc@live.com, não é suportada pelo conector de IoT Central do Azure.

    Depois de entrar nos conectores, você será levado ao designer para criar seu fluxo de trabalho. O fluxo de trabalho tem um gatilho da IoT Central que contém seu aplicativo e a regra já preenchidos.

1. Você pode personalizar o fluxo de trabalho Personalizando as informações passadas para a ação e adicionando novas ações. Neste exemplo, a ação é **notificações-envie-me uma notificação móvel**. Você pode incluir *conteúdo dinâmico* da regra da IoT Central, passando informações importantes como o nome do dispositivo e o carimbo de data/hora para a notificação.

    > [!NOTE]
    > Selecione a janela **Ver mais** texto no conteúdo dinâmico para obter valores de medição e propriedade que dispararam a regra.

    ![Flow editando a ação com o painel dinâmico aberto](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Quando terminar de editar sua ação, selecione **salvar**. Você será direcionado à página de visão geral do fluxo de trabalho. Aqui você pode ver o histórico de execução e compartilhá-lo com outros colegas.

    > [!NOTE]
    > Se desejar que outros usuários em seu aplicativo de Central da IoT editem essa regra, você precisará compartilhá-la com eles no Microsoft Flow. Adicione as contas do Microsoft Flow deles como proprietários em seu fluxo de trabalho.

1. Se voltar para seu aplicativo IoT Central, você verá que essa regra tem uma ação Microsoft Flow na área ações.

Você também pode criar fluxos de trabalho usando o conector de IoT Central diretamente do Microsoft Flow. Em seguida, você pode escolher a qual aplicativo IoT Central se conectar.

## <a name="create-a-device-in-a-workflow"></a>Criar um dispositivo em um fluxo de trabalho

Esta seção mostra como criar um novo dispositivo na IoT Central por meio de um botão em um dispositivo móvel usando o aplicativo móvel Microsoft Flow. Você pode usar essa ação no Flow para integrar sistemas ERP, tais como o Dynamics, com a IoT Central, criando um novo dispositivo quando um dispositivo é adicionado em outro lugar.

1. Comece criando um fluxo de trabalho em branco no Microsoft Flow.

1. Pesquise por **botão do Flow para dispositivos móveis** como um gatilho.

1. Nesse gatilho, adicione uma entrada de texto chamada **Nome do dispositivo**. Altere o texto da descrição, substituindo-o por **Insira o nome do dispositivo do seu novo dispositivo**.

1. Adicione uma nova ação. Pesquise pela ação **Azure IoT Central – Criar um dispositivo**.

1. Escolha seu aplicativo e escolha um modelo de dispositivo do qual criar um dispositivo nas listas suspensas. Você verá a ação se expandir para mostrar todas as propriedades e configurações do dispositivo.

1. Selecione o campo Nome do Dispositivo. No painel de conteúdo dinâmico, escolha **Nome do Dispositivo**. Esse valor é passado da entrada que o usuário insere por meio do aplicativo móvel e é o nome do novo dispositivo no IoT Central. Neste exemplo, o único campo obrigatório é o nome do dispositivo, indicado por um asterisco vermelho. Outro modelo de dispositivo pode ter vários campos obrigatórios que precisam ser preenchidos para criar um novo dispositivo.

    ![Painel dinâmico da ação de criar dispositivo do Flow](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Opcional) Preencha outros campos conforme considerar adequado para criar novos dispositivos.

1. Por fim, salve o fluxo de trabalho.

1. Experimente o fluxo de trabalho no aplicativo móvel do Microsoft Flow. Navegue até a guia **botões** no aplicativo. Você deve ver o Botão -> Criar um novo fluxo de trabalho do dispositivo. Insira o nome do novo dispositivo e assista enquanto ele aparece na IoT Central!

    ![Captura de tela de dispositivo móvel para criação de dispositivo do Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Atualizar um dispositivo em um fluxo de trabalho

Esta seção mostra como atualizar as configurações e propriedades de um dispositivo na IoT Central apenas pressionando um botão em um dispositivo móvel usando o aplicativo móvel Microsoft Flow.

1. Comece criando um fluxo de trabalho em branco no Microsoft Flow.

1. Pesquise por **botão do Flow para dispositivos móveis** como um gatilho.

1. Nesse gatilho, adicione uma entrada como uma entrada de texto **Local** que corresponda a uma configuração ou a propriedade que você deseja alterar. Altere o texto de descrição.

1. Adicione uma nova ação. Pesquise pela ação **Azure IoT Central – Atualizar um dispositivo**.

1. Selecione o aplicativo na lista suspensa. Agora, você precisará da ID do dispositivo existente que deseja atualizar. 

    > [!NOTE] 
    > **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.

    ![ID de IoT Central da URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Você pode atualizar o nome do dispositivo. Para atualizar qualquer uma das configurações e propriedades do dispositivo, você precisa selecionar o modelo de dispositivo do dispositivo que você deseja atualizar na lista suspensa **Modelo de Dispositivo**. O bloco de ação se expande para mostrar todas as propriedades e configurações que você pode atualizar.

    ![O fluxo de trabalho do fluxo de atualização de dispositivo](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selecione cada uma das propriedades e configurações que você deseja atualizar. No painel de conteúdo dinâmico, escolha a entrada correspondente do gatilho. Neste exemplo, o valor Localização é propagado para baixo para atualizar a propriedade Localização do dispositivo.

1. Por fim, salve o fluxo de trabalho.

1. Experimente o fluxo de trabalho no aplicativo móvel do Microsoft Flow. Navegue até a guia **botões** no aplicativo. Você deverá ver o Botão -> Atualizar um fluxo de trabalho do dispositivo. Insira as entradas e veja o dispositivo ser atualizado na IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Obter informações do dispositivo em um fluxo de trabalho

Você pode obter informações de dispositivo por sua ID usando a **IOT central do Azure-obter uma** ação de dispositivo. 
> [!NOTE] 
> **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.

Você pode obter informações como nome do dispositivo, nome do modelo de dispositivo, valores de propriedade e valores de configurações para passar para ações posteriores no fluxo de trabalho. Veja um exemplo de fluxo de trabalho que passa o valor da propriedade Customer Name de um dispositivo para o Microsoft Teams.

   ![Fluxo de trabalho do dispositivo obter fluxo](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Executar um comando em um dispositivo em um fluxo de trabalho
Você pode executar um comando em um dispositivo especificado por sua ID usando o **IOT central do Azure-executar uma** ação de comando. 

> [!NOTE] 
> **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.
    
Você pode escolher o comando a ser executado e passar os parâmetros do comando por meio desta ação. Veja um exemplo de fluxo de trabalho que executa um comando de reinicialização de dispositivo de um botão no aplicativo Microsoft Flow móvel.

   ![Fluxo de trabalho do dispositivo obter fluxo](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Excluir um dispositivo em um fluxo de trabalho

Você pode excluir um dispositivo por sua ID usando a ação **IOT central do Azure-excluir um dispositivo** . 
> [!NOTE] 
> **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.

Aqui está um fluxo de trabalho de exemplo que exclui um dispositivo apenas pressionando um botão no aplicativo móvel Microsoft Flow.

   ![Fluxo de trabalho de exclusão de dispositivo do Flow](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Solução de problemas

Se você está tendo problemas ao criar uma conexão para o conector do Azure IoT Central, aqui estão algumas dicas para ajudá-lo.

1. Contas pessoais da Microsoft (assim como os domínios @hotmail.com, @live.com, @outlook.com) não são compatíveis no momento. Você deve usar uma conta corporativa ou de estudante do Azure Active Directory (AD).

2. Para usar o conector da IoT Central no Microsoft Flow, você precisa ter entrado no aplicativo da IoT Central pelo menos uma vez. Caso contrário, o aplicativo não aparecerá nas listas suspensas de aplicativos.

3. Se você estiver recebendo um erro ao usar uma conta do Azure AD, tente abrir o Windows PowerShell e execute o commandlets a seguir como administrador.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a usar Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerenciar dispositivos](howto-manage-devices.md).

