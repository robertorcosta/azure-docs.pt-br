---
title: 'Depuração interativa: VS Code & instâncias de computação ML'
titleSuffix: Azure Machine Learning
description: Configure o VS Code Remote para depurar interativamente seu código com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169750"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Depurar interativamente em uma instância de computação de aprendizado de máquina do Azure com controle remoto de código VS

Neste artigo, você aprenderá como configurar o Visual Studio Code Remote em uma instância de computação de aprendizagem de máquina do Azure para que você possa **depurar interativamente seu código** do CÓDIGO VS. 

+ Uma instância de computação de aprendizado de [máquina do Azure](concept-compute-instance.md) é uma estação de trabalho totalmente gerenciada baseada em nuvem para cientistas de dados e fornece recursos de gerenciamento e prontidão corporativa para administradores de TI. 


+ [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) O desenvolvimento permite que você use um contêiner, uma máquina remota ou o Windows Subsystem for Linux (WSL) como um ambiente de desenvolvimento completo. 

## <a name="prerequisite"></a>Pré-requisito  

Nas plataformas Windows, você deve [instalar um cliente SSH compatível com OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se um ainda não estiver presente. 

> [!Note]
> PuTTY não é suportado no Windows, pois o comando ssh deve estar no caminho. 

## <a name="get-ip-and-ssh-port"></a>Obter porta IP e SSH 

1. Vá para o estúdio de https://ml.azure.com/Machine Learning do Azure em .

2. Selecione seu [espaço de trabalho](concept-workspace.md).
1. Clique na guia **'Instâncias** de cálculo'.
1. Na coluna URI do **aplicativo,** clique no link **SSH** da instância de computação que você deseja usar como uma computação remota. 
1. Na caixa de diálogo, tome nota do endereço IP e da porta SSH. 
1. Salve sua chave privada para o diretório ~/ssh/ no seu computador local; por exemplo, abra um editor para um novo arquivo e cole a chave em: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Janelas:** 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   A chave privada será um pouco assim:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Alterar permissões no arquivo para garantir que apenas você possa ler o arquivo.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Adicionar instância como um host 

Abra o `~/.ssh/config` arquivo (Linux) ou `C:\Users<username>.ssh\config` (Windows) em um editor e adicione uma nova entrada semelhante a esta:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Aqui alguns detalhes sobre os campos: 

|Campo|Descrição|
|----|---------|
|Host|Use a taquigrafia que você gosta para a instância de computação |
|HostName|Este é o endereço IP da instância de computação |
|Porta|Esta é a porta mostrada na caixa de diálogo SSH acima |
|Usuário|Isso precisa ser `azureuser` |
|Arquivo de identidade|Deve apontar para o arquivo onde você salvou a chave privada |

Agora, você deve ser capaz de ssh para a sua `ssh azmlci1`instância de cálculo usando a taquigrafia que você usou acima, . 

## <a name="connect-vs-code-to-the-instance"></a>Conecte o Código VS à instância 

1. [Instale o Visual Studio Code](https://code.visualstudio.com/).

1. [Instale a extensão SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Clique no ícone Remote-SSH à esquerda para mostrar suas configurações de SSH.

1. Clique com o botão direito do mouse na configuração do host SSH que você acabou de criar.

1. Selecione **Conectar ao host na janela atual**. 

A partir de agora, você está trabalhando inteiramente na instância de computação e agora você pode editar, depurar, usar git, usar extensões, etc. - assim como você pode com o seu Visual Studio Code local. 

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o Visual Studio Code Remote, você pode usar uma instância de computação como computação remota do Visual Studio Code para depurar interativamente seu código. 

[Tutorial: Treinar seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um notebook integrado.