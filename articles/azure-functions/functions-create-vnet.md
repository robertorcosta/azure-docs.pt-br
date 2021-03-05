---
title: Usar pontos de extremidade privados para integrar Azure Functions a uma rede virtual
description: Um tutorial passo a passo que mostra como conectar uma função a uma rede virtual do Azure e bloqueá-la com pontos de extremidade privados
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200199"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Tutorial: integrar Azure Functions com uma rede virtual do Azure usando pontos de extremidade privados

Este tutorial mostra como usar Azure Functions para se conectar a recursos em uma rede virtual do Azure com pontos de extremidade privados. Você criará uma função com uma conta de armazenamento bloqueada por trás de uma rede virtual que usa um gatilho de fila do barramento de serviço.

> [!div class="checklist"]
> * Criar um aplicativo de funções no plano Premium
> * Criar recursos do Azure (barramento de serviço, conta de armazenamento, rede virtual)
> * Bloquear sua conta de armazenamento por trás de um ponto de extremidade privado
> * Bloquear o barramento de serviço por trás de um ponto de extremidade privado
> * Implante um aplicativo de funções com os gatilhos de barramento de serviço e HTTP.
> * Bloquear seu aplicativo de funções por trás de um ponto de extremidade privado
> * Teste para ver se seu aplicativo de funções está protegido por trás da rede virtual
> * Limpar recursos

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar um aplicativo de funções em um plano Premium

Primeiro, você cria um aplicativo de funções do .NET no [plano Premium] , pois este tutorial usará o C#. Também há suporte para outros idiomas no Windows. Este plano fornece escala sem servidor enquanto dá suporte à integração de rede virtual.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Computação** > **Aplicativo de Funções**.

1. Na página **Informações básicas**, use as configurações do aplicativo de funções conforme especificado na seguinte tabela:

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **Nome do aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de runtime** | .NET | Este tutorial usa o .NET |
    |**Região**| Região preferencial | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

1. Selecione **Avançar: Hospedagem**. Na página **Hospedagem**, insira as seguintes configurações:

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](./storage-considerations.md#storage-account-requirements). |
    |**Sistema operacional**| Windows | Este tutorial usa o Windows |
    | **[Plano](./functions-scale.md)** | Premium | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. Selecione **Premium**. Por padrão, um plano do Serviço de Aplicativo é criado. O **SKU e o tamanho** padrão é **EP1**, em que EP significa _elástico premium_. Para saber mais, confira a [lista de SKUs Premium](./functions-premium-plan.md#available-instance-skus).<br/>Ao executar funções JavaScript em um plano Premium, você deve escolher uma instância que tem menos vCPUs. Para obter mais informações, confira [Escolher planos Premium de núcleo único](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Selecione **Avançar: Monitoramento**. Na página **Monitoramento**, insira as seguintes configurações:

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Expandindo essa configuração, você pode alterar o **Novo nome do recurso** ou escolher um **Local** diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para armazenar seus dados. |

1. Selecione **Examinar + criar** para examinar as seleções de configuração do aplicativo.

1. Na página **Examinar + criar**, examine as configurações e, em seguida, selecione **Criar** para provisionar e implantar o aplicativo de funções.

1. Selecione o ícone **Notificações** no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.

1. Parabéns! Você criou com êxito seu aplicativo de funções Premium!

## <a name="create-azure-resources"></a>Criar recursos do Azure

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento separada da criada na criação inicial do seu aplicativo de funções é necessária para redes virtuais.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na nova página, procure conta de **armazenamento** e selecione **criar**

1. Na guia **noções básicas** , defina as configurações conforme especificado na tabela a seguir. O restante pode ser deixado como padrão:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | mysecurestorage| O nome da sua conta de armazenamento à qual o ponto de extremidade privado será aplicado. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que você criou seu aplicativo de funções. |

1. Selecione **Examinar + criar**. Depois de concluir a validação, selecione **Criar**.

### <a name="create-a-service-bus"></a>Criar um barramento de serviço

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na nova página, procure barramento de **serviço** e selecione **criar**.

1. Na guia **noções básicas** , defina as configurações conforme especificado na tabela a seguir. O restante pode ser deixado como padrão:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | myServiceBus| O nome do seu barramento de serviço ao qual o ponto de extremidade privado será aplicado. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que você criou seu aplicativo de funções. |
    | **Tipo de preços** | Premium | Escolha esta camada para usar pontos de extremidade privados com o barramento de serviço. |

1. Selecione **Examinar + criar**. Depois de concluir a validação, selecione **Criar**.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Os recursos do Azure neste tutorial se integram com o ou são colocados em uma rede virtual. Você usará pontos de extremidade privados para manter o tráfego de rede contido na rede virtual.

O tutorial cria duas sub-redes:
- **Default**: sub-rede para pontos de extremidade privados. Os endereços IP privados são fornecidos por essa sub-rede.
- **funções**: sub-rede para Azure Functions integração de rede virtual. Essa sub-rede é delegada para o aplicativo de funções.

Agora, crie a rede virtual para a qual o aplicativo de funções se integra.

1. No menu do portal do Azure ou na Página Inicial, selecione **Criar um recurso**.

1. Na nova página, procure **rede virtual** e selecione **criar**.

1. Na guia **noções básicas** , use as configurações de rede virtual conforme especificado abaixo:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | myVirtualNet| O nome da sua rede virtual à qual seu aplicativo de funções se conectará. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que você criou seu aplicativo de funções. |

1. Na guia **endereços IP** , selecione **Adicionar sub-rede**. Use as configurações conforme especificado abaixo ao adicionar uma sub-rede:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Captura de tela da exibição criar configuração de rede virtual.":::

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome da sub-rede** | funções | O nome da sub-rede à qual seu aplicativo de funções se conectará. | 
    | **Intervalo de endereços da sub-rede** | 10.0.1.0/24 | Observe que nosso espaço de endereço IPv4 na imagem acima é 10.0.0.0/16. Se a acima era 10.1.0.0/16, o *intervalo de endereços de sub-rede* recomendado seria 10.1.1.0/24. |

1. Selecione **Examinar + criar**. Depois de concluir a validação, selecione **Criar**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Bloquear sua conta de armazenamento com pontos de extremidade privados

Os pontos de extremidade privados do Azure são usados para se conectar a recursos específicos do Azure usando um endereço IP privado. Essa conexão garante que o tráfego de rede permaneça dentro da rede virtual escolhida e o acesso esteja disponível somente para recursos específicos. Agora, crie os pontos de extremidade privados para o armazenamento de arquivos do Azure e o armazenamento de BLOBs do Azure com sua conta de armazenamento.

1. Em sua nova conta de armazenamento, selecione **rede** no menu à esquerda.

1. Selecione a guia **conexões de ponto de extremidade privado** e selecione **ponto de extremidade privado**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Captura de tela de como navegar para criar pontos de extremidade privados para a conta de armazenamento.":::

1. Na guia **noções básicas** , use as configurações do ponto de extremidade privado, conforme especificado abaixo:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que você criou com seu aplicativo de funções. | |
    | **Nome** | ponto de extremidade de arquivo | O nome do ponto de extremidade privado para arquivos da sua conta de armazenamento. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que você criou sua conta de armazenamento. |

1. Na guia **recurso** , use as configurações do ponto de extremidade privado, conforme especificado abaixo:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAccounts | Esse é o tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que você acabou de criar |
    | **Sub-recurso de destino** | arquivo | Esse ponto de extremidade privado será usado para arquivos da conta de armazenamento. |

1. Na guia **configuração** , escolha **padrão** para a configuração de sub-rede.

1. Selecione **Examinar + criar**. Depois de concluir a validação, selecione **Criar**. Os recursos na rede virtual agora podem se comunicar com os arquivos de armazenamento.

1. Crie outro ponto de extremidade privado para BLOBs. Para a guia **recursos** , use as configurações abaixo. Para todas as outras configurações, use as mesmas configurações das etapas de criação do ponto de extremidade particular do arquivo que você acabou de seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAccounts | Esse é o tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que você acabou de criar |
    | **Sub-recurso de destino** | blob | Esse ponto de extremidade privado será usado para BLOBs da conta de armazenamento. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Bloquear o barramento de serviço com um ponto de extremidade privado

Agora, crie o ponto de extremidade privado para seu barramento de serviço do Azure.

1. Em seu novo barramento de serviço, selecione **rede** no menu à esquerda.

1. Selecione a guia **conexões de ponto de extremidade privado** e selecione **ponto de extremidade privado**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Captura de tela de como navegar até pontos de extremidade privados para o barramento de serviço.":::

1. Na guia **noções básicas** , use as configurações do ponto de extremidade privado, conforme especificado abaixo:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | SB-ponto de extremidade | O nome do ponto de extremidade privado para arquivos da sua conta de armazenamento. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que você criou sua conta de armazenamento. |

1. Na guia **recurso** , use as configurações do ponto de extremidade privado, conforme especificado abaixo:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.ServiceBus/namespaces | Esse é o tipo de recurso para o barramento de serviço. |
    | **Recurso** | myServiceBus | O barramento de serviço criado anteriormente no tutorial. |
    | **Sub-recurso de destino** | namespace | Esse ponto de extremidade privado será usado para o namespace do barramento de serviço. |

1. Na guia **configuração** , escolha **padrão** para a configuração de sub-rede.

1. Selecione **Examinar + criar**. Depois de concluir a validação, selecione **Criar**. Os recursos na rede virtual agora podem se comunicar com o barramento de serviço.

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivo

1. Na conta de armazenamento que você criou, selecione **compartilhamentos de arquivos** no menu à esquerda.

1. Selecione **+ compartilhamentos de arquivos**. Forneça **arquivos** como o nome do compartilhamento de arquivos para os fins deste tutorial.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Captura de tela de como criar um compartilhamento de arquivos na conta de armazenamento.":::

## <a name="get-storage-account-connection-string"></a>Obter cadeia de conexão da conta de armazenamento

1. Na conta de armazenamento que você criou, selecione **chaves de acesso** no menu à esquerda.

1. Selecione **Mostrar chaves**. Copie a cadeia de conexão de key1 e salve-a. Precisaremos dessa cadeia de conexão mais tarde ao definir as configurações do aplicativo.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Captura de tela de como obter uma cadeia de conexão da conta de armazenamento.":::

## <a name="create-a-queue"></a>Criar uma fila

Essa será a fila para a qual seu gatilho do barramento de serviço do Azure Functions receberá eventos.

1. No barramento de serviço, selecione **filas** no menu à esquerda.

1. Selecione **Políticas de acesso compartilhado**. Forneça a **fila** como o nome da fila para os fins deste tutorial.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Captura de tela de como criar uma fila do barramento de serviço.":::

## <a name="get-service-bus-connection-string"></a>Obter cadeia de conexão do barramento de serviço

1. Em seu barramento de serviço, selecione **políticas de acesso compartilhado** no menu à esquerda.

1. Selecione **RootManageSharedAccessKey**. Copie a **cadeia de conexão primária** e salve-a. Precisaremos dessa cadeia de conexão mais tarde ao definir as configurações do aplicativo.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Captura de tela de como obter uma cadeia de conexão do barramento de serviço.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Integrar o aplicativo de funções à sua rede virtual

Para usar seu aplicativo de funções com redes virtuais, você precisará associá-la a uma sub-rede. Usamos uma sub-rede específica para a integração de rede virtual Azure Functions e a sub rede padrão para todos os outros pontos de extremidade privados criados neste tutorial.

1. Em seu aplicativo de funções, selecione **rede** no menu à esquerda.

1. Selecione **clique aqui para configurar** em integração VNet.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Captura de tela de como navegar até a integração de rede virtual.":::

1. Selecione **Adicionar VNet**

1. Na folha que é aberta em **rede virtual**, selecione a rede virtual que você criou anteriormente.

1. Selecione a **sub-rede** que criamos anteriormente com **funções** chamadas. Seu aplicativo de funções agora está integrado à sua rede virtual!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Captura de tela de como conectar um aplicativo de funções a uma sub-rede.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Definir as configurações do aplicativo de funções para pontos de extremidade privados

1. Em seu aplicativo de funções, selecione **configuração** no menu à esquerda.

1. Para usar seu aplicativo de funções com redes virtuais, as configurações de aplicativo a seguir precisarão ser atualizadas. Selecione **+ nova configuração de aplicativo** ou o lápis por **edição** na coluna mais à direita da tabela configurações do aplicativo, conforme apropriado. Ao terminar, escolha **Salvar**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Captura de tela de como definir as configurações do aplicativo de funções para pontos de extremidade privados.":::

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | A cadeia de conexão da conta de armazenamento que você criou. Esta é a cadeia de conexão de armazenamento da [cadeia de conexão obter conta de armazenamento](#get-storage-account-connection-string). Ao alterar essa configuração, seu aplicativo de funções agora usará a conta de armazenamento seguro para operações normais em tempo de execução. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | A cadeia de conexão da conta de armazenamento que você criou. Ao alterar essa configuração, seu aplicativo de funções agora usará a conta de armazenamento seguro para arquivos do Azure, que são usados durante a implantação do. |
    | **WEBSITE_CONTENTSHARE** | files | O nome do compartilhamento de arquivos que você criou na conta de armazenamento. Essa configuração de aplicativo é usada em conjunto com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Crie uma configuração de aplicativo para a cadeia de conexão do seu barramento de serviço. Esta é a cadeia de conexão de armazenamento da [cadeia de conexão obter barramento de serviço](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Crie essa configuração de aplicativo. Um valor de 1 permite que seu aplicativo de funções seja dimensionado quando você tiver sua conta de armazenamento restrita a uma rede virtual. Você deve habilitar essa configuração ao restringir sua conta de armazenamento a uma rede virtual. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Crie essa configuração de aplicativo. Depois que seu aplicativo se integrar a uma rede virtual, ele usará o mesmo servidor DNS que a rede virtual. Essa é uma das duas configurações necessárias. o aplicativo de funções funciona com as zonas privadas do DNS do Azure e é necessário ao usar pontos de extremidade privados. Essas configurações enviarão todas as chamadas de saída do seu aplicativo para sua rede virtual. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Crie essa configuração de aplicativo. Depois que seu aplicativo se integrar a uma rede virtual, ele usará o mesmo servidor DNS que a rede virtual. Essa é uma das duas configurações necessárias. o aplicativo de funções funciona com as zonas privadas do DNS do Azure e é necessário ao usar pontos de extremidade privados. Essas configurações enviarão todas as chamadas de saída do seu aplicativo para sua rede virtual. |

1. Permanecendo na exibição de **configuração** , selecione a guia **configurações de tempo de execução de função** .

1. Defina **monitoramento de escala de tempo de execução** como **ativado** e selecione **salvar**. O dimensionamento controlado por tempo de execução permite que você conecte funções de gatilho não HTTP a serviços em execução dentro de sua rede virtual.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Captura de tela de como habilitar o dimensionamento controlado por tempo de execução para Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Implantar um gatilho do barramento de serviço e um gatilho http para seu aplicativo de funções

1. No GitHub, navegue até o repositório de exemplo a seguir, que contém um aplicativo de função com duas funções, um gatilho HTTP e um gatilho de fila do barramento de serviço.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Na parte superior da página, selecione o botão **bifurcação** para criar uma bifurcação desse repositório em sua própria conta ou organização do github.

1. Em seu aplicativo de funções, selecione **central de implantação** no menu à esquerda. Em seguida, selecione **configurações**.

1. Na guia **configurações** , use as configurações de implantação, conforme especificado abaixo:

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Origem** | GitHub | Você deve ter criado um repositório GitHub com o código de exemplo na etapa 2. | 
    | **Organização**  | myOrganization | Essa é a organização com a qual o seu repositório é verificado, geralmente sua conta. |
    | **Repositório** | myrepositório | O repositório criado com o código de exemplo. |
    | **Branch** | main | Esse é o repositório que você acabou de criar, portanto, use a ramificação principal. |
    | **Pilha de runtime** | .NET | O código de exemplo está em C#. |

1. Clique em **Salvar**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Captura de tela de como implantar código Azure Functions por meio do Portal.":::

1. A implantação inicial pode levar alguns minutos. Você verá uma mensagem de status de **êxito (ativa)** na guia **logs** quando seu aplicativo for implantado com êxito. Se necessário, atualize a página. 

1. Parabéns! Você implantou com êxito o aplicativo de funções de exemplo.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Bloquear seu aplicativo de funções com um ponto de extremidade privado

Agora, crie o ponto de extremidade privado para seu aplicativo de funções. Esse ponto de extremidade privado conectará seu aplicativo de funções de forma privada e segura à sua rede virtual usando um endereço IP privado. Para obter mais informações sobre pontos de extremidade privados, acesse a [documentação de pontos de extremidade particulares](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. Em seu aplicativo de funções, selecione **rede** no menu à esquerda.

1. Selecione **clique aqui para configurar** em conexões de ponto de extremidade privado.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Captura de tela de como navegar para um ponto de extremidade privado Aplicativo de funções.":::

1. Selecione **Adicionar**.

1. No menu que é aberto, use as configurações do ponto de extremidade privado, conforme especificado abaixo:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Captura de tela de como criar um ponto de extremidade privado Aplicativo de funções.":::

1. Selecione **OK** para adicionar o ponto de extremidade privado. Parabéns! Você protegeu com êxito seu aplicativo de funções, barramento de serviço e conta de armazenamento com pontos de extremidade privados!

### <a name="test-your-locked-down-function-app"></a>Testar o aplicativo de funções bloqueados

1. Em seu aplicativo de funções, selecione **funções** no menu à esquerda.

1. Selecione o **ServiceBusQueueTrigger**.

1. No menu à esquerda, selecione **Monitor**. Você verá que não é possível monitorar seu aplicativo. Isso ocorre porque o navegador não tem acesso à rede virtual e, portanto, não pode acessar diretamente os recursos dentro da rede virtual. Agora, demonstraremos outro método pelo qual você ainda pode monitorar sua função, Application Insights.

1. Em seu aplicativo de funções, selecione **Application insights** no menu à esquerda e selecione **exibir dados de Application insights**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Captura de tela de como exibir o Application insights para um Aplicativo de funções.":::

1. Selecione **métricas em tempo real** no menu à esquerda.

1. Abra uma nova guia. No barramento de serviço, selecione **filas** no menu à esquerda.

1. Selecione sua fila.

1. Selecione **Gerenciador do barramento de serviço** no menu à esquerda. Em **Enviar**, escolha **texto/simples** como o **tipo de conteúdo** e insira uma mensagem. 

1. Selecione **Enviar** para enviar a mensagem.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Captura de tela de como enviar mensagens do barramento de serviço usando o Portal.":::

1. Na guia com as **métricas dinâmicas** abertas, você verá que o gatilho de fila do barramento de serviço foi disparado. Se não tiver, reenvie a mensagem do **Gerenciador do barramento de serviço**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Captura de tela de como exibir mensagens usando métricas em tempo real para aplicativos de funções.":::

1. Parabéns! Você testou com êxito sua configuração de aplicativo de funções com pontos de extremidade privados!

### <a name="private-dns-zones"></a>Zonas DNS Privadas
Usar um ponto de extremidade privado para se conectar a recursos do Azure significa conectar-se a um endereço IP privado em vez do ponto de extremidade público. Os serviços existentes do Azure são configurados para usar o DNS existente para se conectar ao ponto de extremidade público. A configuração de DNS precisará ser substituída para se conectar ao ponto de extremidade privado.

Uma zona DNS privada foi criada para cada recurso do Azure configurado com um ponto de extremidade privado. Um registro de DNS A é criado para cada endereço IP privado associado ao ponto de extremidade privado.

As seguintes zonas DNS foram criadas neste tutorial:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo de funções Premium, uma conta de armazenamento e um barramento de serviço e protegeu todos os pontos de extremidade privados. Saiba mais sobre os vários recursos de rede disponíveis abaixo:

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de rede do Functions](./functions-networking-options.md)

[Plano Premium]: functions-premium-plan.md
