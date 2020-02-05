---
title: Gerenciar seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar seu aplicativo de IoT Central do Azure alterando o nome do aplicativo, a URL, o upload de imagem, a cópia e a exclusão de um aplicativo
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 89c4dd294cbbf0953545e1055e32adfc5f7cce28
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990786"
---
# <a name="manage-your-iot-central-application"></a>Gerenciar seu aplicativo IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como administrador, você pode gerenciar o aplicativo alterando o nome e a URL do aplicativo, carregando a imagem, também pode aprender a copiar e excluir um aplicativo no aplicativo IoT Central do Azure.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo. 

## <a name="change-application-name-and-url"></a>Alterar o nome do aplicativo e URL

Na página **Configurações do aplicativo**, você pode alterar o nome e o URL do seu aplicativo e, em seguida, selecionar **Salvar**.

![Página de configurações do aplicativo](media/howto-administer/image0-a.png)

Se o administrador criar um tema personalizado para seu aplicativo, essa página incluirá uma opção para ocultar o **nome do aplicativo** na interface do usuário. Isso será útil se o logotipo do aplicativo no tema personalizado incluir o nome do aplicativo. Para obter mais informações, consulte [Personalizar a interface do usuário IOT central do Azure](./howto-customize-ui.md).

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Nesse caso, a URL não estará mais disponível para você usar. Ao alterar a URL, a URL antiga não funcionará mais e você deverá notificar os usuários sobre a nova URL a ser utilizada.

## <a name="prepare-and-upload-image"></a>Preparar e carregar a imagem

Para alterar a imagem do aplicativo, consulte [Preparar e carregar imagens no aplicativo Microsoft IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copiar um aplicativo

É possível criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivos, histórico de dados do dispositivo e os dados de usuário. A cópia usa um plano de preços padrão para o qual você será cobrado. Você não pode criar um aplicativo que usa o plano de preços gratuito dessa maneira.

Selecione **copiar**. Na caixa de diálogo, insira os detalhes do novo aplicativo. Em seguida, selecione **copiar** para confirmar que deseja continuar. Saiba mais sobre os campos deste formulário em [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).

![Página de configurações do aplicativo](media/howto-administer/appcopy2.png)

Depois que a operação de cópia do aplicativo for realizada com sucesso, você poderá navegar para o novo aplicativo usando o link.

![Página de configurações do aplicativo](media/howto-administer/appcopy3a.png)

Copiar um aplicativo também copia a definição de regras e a ação de email. Algumas ações como fluxo, aplicativos lógicos etc. são vinculadas a regras específicas por meio da ID de regra. Quando uma regra é copiada para um aplicativo diferente, ela obtém sua própria ID de regra. Nesse caso, os usuários terão que criar uma nova ação e associar a nova regra a ela. Em geral, é uma boa ideia verificar as regras e ações para certificar-se de que elas estão atualizadas no novo aplicativo.

> [!WARNING]
> Se um painel inclui blocos que exibem informações sobre dispositivos específicos, esses blocos mostram que **o recurso solicitado não foi encontrado** no novo aplicativo. Você deve reconfigurar esses blocos para exibir informações sobre dispositivos em seu novo aplicativo.

## <a name="delete-an-application"></a>Excluir um aplicativo

Use o botão **Excluir** para excluir permanentemente seu aplicativo IoT Central. Essa ação exclui permanentemente todos os dados associados ao aplicativo.

> [!Note]
> Para excluir um aplicativo, você também deve ter as permissões para excluir recursos da assinatura do Azure escolhida ao criar o aplicativo. Para saber mais, confira [Usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Gerenciar programaticamente

Os pacotes do SDK do IoT Central Azure Resource Manager estão disponíveis para Node, Python, C#, Ruby, Java e Go. Você pode usar esses pacotes para criar, listar, atualizar ou excluir aplicativos IoT Central. Os pacotes incluem auxiliares para gerenciar a autenticação e o tratamento de erros.

Veja exemplos de como usar os SDKs do Azure Resource Manager em [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Para saber mais, confira os seguintes repositórios e pacotes do GitHub:

| Idioma | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Próximos passos
 
Agora que você aprendeu sobre como administrar seu aplicativo de IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar usuários e funções](howto-manage-users-roles.md) no Azure IOT central.