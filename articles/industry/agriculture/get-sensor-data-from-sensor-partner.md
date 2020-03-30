---
title: Obtenha dados do sensor dos parceiros
description: Este artigo descreve como obter dados de sensores de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384187"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obtenha dados de sensores de parceiros de sensores

O Azure FarmBeats ajuda você a trazer dados de streaming de seus dispositivos ET e sensores ioT para o Datahub. Atualmente, os seguintes parceiros de dispositivo de sensor são suportados.

  ![Parceiros farmbeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

A integração de dados do dispositivo com o Azure FarmBeats ajuda você a obter dados terrestres dos sensores IoT implantados em sua fazenda para o data hub. Os dados, uma vez disponíveis, podem ser visualizados através do acelerador FarmBeats. Os dados podem ser usados para fusão de dados e aprendizado de máquina/inteligência artificial (ML/AI) através do uso do FarmBeats.

Para iniciar o fluxo de dados do sensor, certifique-se de que o seguinte:

-  Você instalou o FarmBeats no Azure Marketplace.
-  Você decidiu sobre os sensores e dispositivos que você quer instalar em sua fazenda.
-  Se você planeja usar sensores de umidade do solo, use o mapa farmbeats soil umidade sensor Placement placement para obter uma recomendação sobre o número de sensores e onde exatamente você deve colocá-los. Para obter mais informações, consulte [Gerar mapas](generate-maps-in-azure-farmbeats.md).
- Você compra e implanta dispositivos ou sensores do seu parceiro de dispositivo em sua fazenda. Certifique-se de que você pode acessar os dados do sensor através da solução de seus parceiros de dispositivo.

## <a name="enable-device-integration-with-farmbeats"></a>Habilite a integração de dispositivos com o FarmBeats

Depois de iniciar o fluxo de dados do sensor, você pode iniciar o processo de obtenção dos dados no seu sistema FarmBeats. Forneça as seguintes informações ao provedor de dispositivos para permitir a integração ao FarmBeats:

 - Ponto de extremidade de API
 - ID do locatário
 - ID do Cliente
 - Segredo do cliente
 - String de conexão EventHub

Você pode gerar as informações acima seguindo estas etapas: (Por favor, note que essas etapas são necessárias para serem feitas no Azure para que você precise acessar a assinatura do Azure onde o FarmBeats é implantado)

1. Entrar no https://portal.azure.com/.

2. **Se você estiver na versão 1.2.7 do FarmBeats ou posterior, por favor pule as etapas 2a, 2b e 2c, e vá para a etapa 3.**. . Você pode verificar a versão FarmBeats clicando no ícone Configurações no lado superior direito da UI FarmBeats.

2a. Acesse Azure Active Directory -> Registros de aplicativos

2b. Clique no Registro do Aplicativo que foi criado como parte de sua implantação do FarmBeats. Ele terá o mesmo nome do seu data hub FarmBeats.

2c. Clique em "Expor uma API" -> Clique em "Adicionar um aplicativo cliente" e digite **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e verifique "Autorizar escopo". Isso dará acesso à CLI do Azure (Cloud Shell) para executar as etapas abaixo.

3. Abra o Azure Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal Azure.

    ![Barra de ferramentas do portal Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. Certifique-se de que o ambiente está definido como **PowerShell**. Por padrão, está definido para Bash.

    ![Configuração da barra de ferramentas PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. Vá para o seu diretório doméstico.

   ```azurepowershell-interactive 

    cd  

    ```

7. Execute o comando a seguir. Isso baixará um script para o seu diretório inicial.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. Execute o seguinte script. O script pede o ID do Inquilino que pode ser obtido na página Azure Active Directory -> Overview.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. Siga as instruções na tela para capturar os valores de **API Endpoint,** **ID do inquilino,** **ID do cliente,** **Client Secret**e **EventHub Connection String**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integre os dados do dispositivo usando as credenciais geradas

Agora você tem as seguintes informações geradas da seção anterior.
 - Ponto de extremidade de API
 - String de conexão EventHub
 - ID do Cliente
 - Segredo do cliente
 - ID do locatário
 
Você precisará fornecer isso ao seu parceiro de dispositivo para vincular farmbeats. Vá para o portal do parceiro do dispositivo para fazer o mesmo. Por exemplo, no caso de você estar usando dispositivos da Davis Instruments, Teralytic ou Pessl Instruments (Metos.at) por favor, vá para as páginas correspondentes como mencionado abaixo:

[Instrumentos Davis](https://weatherlink.github.io/azure-farmbeats/setup)

[Teralytic](https://app.teralytic.com/)

[Instrumentos pessis](https://ng.fieldclimate.com/user-api-services)

 O provedor de dispositivos confirma uma integração bem-sucedida. Após a confirmação, você pode ver todos os dispositivos e sensores no Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Ver dispositivos e sensores

Use a seção a seguir para visualizar os dispositivos e sensores da sua fazenda.

### <a name="view-devices"></a>Exibir dispositivos

Atualmente, o FarmBeats suporta os seguintes dispositivos:

- **Nó :** Um dispositivo ao qual um ou mais sensores são conectados.
- **Gateway**: Um dispositivo ao qual um ou mais nós estão conectados.

Siga estas etapas.

1. Na página inicial, selecione **Dispositivos** no menu.
  A página **Dispositivos** exibe o tipo de dispositivo, modelo, status, a fazenda em que está colocado e a última data atualizada para metadados. Por padrão, a coluna fazenda é definida como *NULL*. Você pode optar por atribuir um dispositivo a uma fazenda. Para obter mais informações, consulte [Atribuir dispositivos](#assign-devices).
2. Selecione o dispositivo para visualizar as propriedades do dispositivo, a telemetria e os dispositivos infantis conectados ao dispositivo.

    ![Página Dispositivos](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Ver sensores

Siga estas etapas.

1. Na página inicial, selecione **Sensores** no menu.
  A página **Sensores** exibe detalhes sobre o tipo de sensor, a fazenda a que está conectado, dispositivo pai, nome da porta, tipo de porta e o último status atualizado.
2. Selecione o sensor para visualizar as propriedades do sensor, alertas ativos e telemetria a partir do sensor.

    ![Página de sensores](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Atribuir dispositivos  

Depois que os dados do sensor estão fluindo, você pode atribuí-los à fazenda onde você implantou os sensores.

1. Na página inicial, selecione **Fazendas** no menu. A página da lista **Fazendas** é exibida.
2. Selecione a fazenda para a qual deseja atribuir o dispositivo e **selecione Adicionar dispositivos**.
3. A **janela Adicionar dispositivos** é exibida. Selecione o dispositivo que deseja atribuir à fazenda.

    ![Adicionar janela dispositivos](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar dispositivos**. Alternativamente, vá para o menu **Dispositivos,** selecione os dispositivos que deseja atribuir a uma fazenda e selecione **Dispositivos Associados**.
5. Na janela **Dispositivos Associados,** selecione a fazenda na lista de itens abaixo e **selecione Aplicar a todos para** associar a fazenda a todos os dispositivos selecionados.

    ![Janela Dispositivos Associados](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Para associar cada dispositivo a uma fazenda diferente, selecione a seta baixa na coluna **Atribuir à Fazenda** e selecione uma fazenda para cada linha de dispositivo.
7. Selecione **Atribuir** para concluir a atribuição do dispositivo.

### <a name="visualize-sensor-data"></a>Visualizar dados do sensor

Siga estas etapas.

1. Na página inicial, selecione **Fazendas** no menu para visualizar a página **Fazendas.**
2. Selecione a **Fazenda** para a qual deseja ver os dados do sensor.
3. No painel **da Fazenda,** você pode visualizar dados de telemetria. Você pode ver a telemetria ao vivo ou usar **o Intervalo Personalizado** para ver um intervalo de datas específico.

    ![Painel de fazenda](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Exclua um sensor

Siga estas etapas.

1. Na página inicial, selecione **Sensores** no menu para visualizar a página **Sensores.**
2. Selecione o dispositivo que deseja excluir e **selecione Excluir** na janela de confirmação.

    ![Botão Excluir](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Uma mensagem de confirmação mostra que o sensor foi excluído com sucesso.

## <a name="delete-devices"></a>Excluir dispositivos

Siga estas etapas.

1. Na página inicial, selecione **Dispositivos** no menu para exibir a página **Dispositivos.**
2. Selecione o dispositivo que deseja excluir e **selecione Excluir** na janela de confirmação.

    ![Botão Excluir](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Próximas etapas

Agora você tem dados de sensores fluindo para a instância Do Azure FarmBeats. Agora, aprenda a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para suas fazendas.
