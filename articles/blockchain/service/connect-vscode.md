---
title: Usar o Azure Blockchain Development Kit para Ethereum – Azure Blockchain Service
description: Conectar-se a uma rede de consórcio do Azure Blockchain Service usando a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329308"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Início Rápido: usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service

Neste início rápido, você instala e usa a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code para ser anexada a um consórcio no Azure Blockchain Service. O Azure Blockchain Development Kit simplifica a maneira como você cria, conecta e implanta contratos inteligentes em razões do Ethereum. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir [Início Rápido: Criar um membro do blockchain usando o portal do Azure](create-member.md) ou [Início Rápido: Criar um membro do blockchain do Azure Blockchain Service usando a CLI do Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Extensão Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Adicione o python.exe ao caminho. Colocar o Python no caminho é um requisito para o Azure Blockchain Development Kit.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [CLI do Ganache](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verificar o ambiente do Azure Blockchain Development Kit

O Azure Blockchain Development Kit verifica se os pré-requisitos do ambiente de desenvolvimento foram atendidos. Para verificar o ambiente de desenvolvimento:

Na paleta de comandos do VS Code, escolha **Azure Blockchain: Mostrar Página de Boas-Vindas**.

O Azure Blockchain Development Kit executa um script de validação que leva cerca de um minuto para ser concluído. Você pode exibir a saída selecionando **Terminal > Novo Terminal**. Na barra de menus do terminal, selecione a guia **Saída** e **Azure Blockchain** na lista suspensa. A validação bem-sucedida tem aparência semelhante à da imagem a seguir:

![Ambiente de desenvolvimento válido](./media/connect-vscode/valid-environment.png)

 Se uma ferramenta necessária estiver ausente, uma nova guia chamada **Azure Blockchain Development Kit – Versão prévia** listará os aplicativos necessários para instalar e os links para baixar as ferramentas.

![Aplicativos necessários para o Development Kit](./media/connect-vscode/required-apps.png)

Instale pré-requisitos ausentes antes de continuar com o início rápido.

## <a name="connect-to-consortium-member"></a>Conectar-se a um membro do consórcio

Você pode se conectar a membros do consórcio usando a extensão Azure Blockchain Development Kit do VS Code. Uma vez conectado a um consórcio, você pode criar, compilar e implantar contratos inteligentes em um membro do consórcio do Azure Blockchain Service.

Se você não tem acesso a um membro do consórcio do Azure Blockchain Service, conclua o pré-requisito [Início Rápido: Criar um membro do blockchain usando o portal do Azure](create-member.md) ou [Início Rápido: Criar um membro do blockchain do Azure Blockchain Service usando a CLI do Azure](create-member-cli.md).

1. No painel do Explorer do VS Code (Visual Studio Code), expanda a extensão **Azure Blockchain Service**.
1. Selecione **Conectar-se ao Consórcio**.

   ![Conectar-se ao consórcio](./media/connect-vscode/connect-consortium.png)

    Se a autenticação do Azure for solicitada, siga os prompts para autenticar usando um navegador.
1. Escolha **Conectar-se ao consórcio do Azure Blockchain Service** no menu suspenso da paleta de comandos.
1. Escolha a assinatura e o grupo de recursos associados ao membro do consórcio do Azure Blockchain Service.
1. Escolha o consórcio na lista.

Os membros do consórcio e do blockchain são listados na barra lateral do Explorer do Visual Studio.

![Consórcio exibido no Explorer](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code para ser anexada a um consórcio no Azure Blockchain Service. Experimente o próximo tutorial para usar o Azure Blockchain Development Kit para Ethereum e Truffle para criar, implantar e executar uma função de contrato inteligente por uma transação.

> [!div class="nextstepaction"]
> [Use o Visual Studio Code para criar e implantar contratos inteligentes](send-transaction.md)