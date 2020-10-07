---
title: Conectar o computador híbrido com os servidores habilitados para Azure Arc
description: Saiba como se conectar e registrar o seu computador híbrido com os servidores habilitados para Azure Arc.
ms.topic: quickstart
ms.date: 09/23/2020
ms.openlocfilehash: b57f30821a105a99041d8187716b75096116ea8e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327877"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Início Rápido: Conectar o computador híbrido com os servidores habilitados para Azure Arc

[Os servidores habilitados para Azure Arc](../overview.md) permitem que você gerencie e controle os computadores Windows e Linux hospedados em ambientes locais, de borda e de várias nuvens. Neste guia de início rápido, você vai implantar e configurar o agente do Connected Machine no seu computador Windows ou Linux hospedado fora do Azure para gerenciamento por servidores habilitados para Arc.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* A implantação do agente do Hybrid Connected Machine dos servidores habilitados para Arc requer que você tenha permissões de administrador no computador para instalar e configurar o agente. No Linux, usando a conta raiz, e no Windows, com uma conta que é membro do grupo de Administradores Locais.

* Antes de começar, certifique-se de examinar os [pré-requisitos](../agent-overview.md#prerequisites) do agente e verificar o seguinte:

    * Se o seu computador de destino está executando um [sistema operacional](../agent-overview.md#supported-operating-systems) com suporte.

    * Se a sua conta recebe atribuição para as [funções do Azure necessárias](../agent-overview.md#required-permissions).

    * Se o computador se conectar por meio de um firewall ou de um servidor proxy para se comunicar pela Internet, verifique se as URLs [listadas](../agent-overview.md#networking-configuration) não estão bloqueadas.

    * Os servidores habilitados para Azure Arc dão suporte apenas às regiões especificadas [aqui](../overview.md#supported-regions).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registrar provedores de recursos do Azure

Os servidores habilitados do Azure Arc dependem dos seguintes provedores de recursos do Azure na sua assinatura para usar esse serviço:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Registre-os usando os seguintes comandos:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Gerar o script de instalação

O script usado para automatizar o download, a instalação e para estabelecer a conexão com o Azure Arc está disponível no portal do Azure. Para concluir o processo, faça o seguinte:

1. Inicie o serviço do Azure Arc no portal do Azure clicando em **Todos os serviços** e, em seguida, pesquisando e selecionando **Computadores – Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Pesquisar os servidores habilitados para Arc em Todos os Serviços" border="false":::

1. Na página **Computadores – Azure Arc**, selecione **Adicionar** no canto superior esquerdo ou a opção **Criar computador – Azure Arc** na parte inferior do painel central.

1. Na página **Selecionar um método**, selecione o bloco **Adicionar computadores usando o script interativo** e, em seguida, selecione **Gerar script**.

1. Na página **Gerar script**, selecione a assinatura e o grupo de recursos nos quais você deseja que o computador seja gerenciado no Azure. Selecione uma localização do Azure em que os metadados do computador serão armazenados.

1. Na página **Gerar script**, na lista suspensa **Sistema operacional**, selecione o sistema operacional no qual o script será executado.

1. Se o computador estiver se comunicando por meio de um servidor proxy para se conectar à Internet, selecione **Próximo: Servidor Proxy**.

1. Na guia **Servidor proxy**, especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usará para se comunicar com o servidor proxy. Digite o valor no formato `http://<proxyURL>:<proxyport>`.

1. Selecione **Examinar + gerar**.

1. Na guia **Examinar + gerar**, examine as informações de resumo e, em seguida, selecione **Baixar**. Se você ainda precisar fazer alterações, selecione **Anterior**.

## <a name="install-the-agent-using-the-script"></a>Instalar o agente usando o script

### <a name="windows-agent"></a>Agente do Windows

1. Faça logon no servidor.

1. Abra um prompt de comando com privilégios elevados do PowerShell de 64 bits.

1. Vá para a pasta ou o compartilhamento para o qual você copiou o script e execute-o no servidor executando o script `./OnboardingScript.ps1`.

### <a name="linux-agent"></a>Agente do Linux

1. Para instalar o agente do Linux no computador de destino que pode se comunicar diretamente com o Azure, execute o seguinte comando:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Se o computador de destino se comunicar por meio de um servidor proxy, execute o seguinte comando:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o Azure Arc

Depois de instalar o agente e configurá-lo para se conectar aos servidores habilitados para Azure Arc, acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Exiba o seu computador no [portal do Azure](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Pesquisar os servidores habilitados para Arc em Todos os Serviços" border="false":::

## <a name="next-steps"></a>Próximas etapas

Agora que você habilitou o seu computador híbrido Linux ou Windows e se conectou com êxito ao serviço, você está pronto para habilitar o Azure Policy para entender a conformidade no Azure.

Para saber como identificar o computador habilitado para servidores habilitados para Azure Arc que não tem o agente do Log Analytics instalado, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Criar uma atribuição de política para identificar recursos sem conformidade](tutorial-assign-policy-portal.md)
