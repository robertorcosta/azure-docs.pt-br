---
title: Tutorial para filtrar e analisar dados com a computação no Azure Data Box Edge| Microsoft Docs
description: Saiba como configurar a função de computação no Data Box Edge e usá-la para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277249"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Tutorial: Transformar dados com o Azure Data Box Edge

Este tutorial descreve como configurar uma função de computação no dispositivo do Azure Data Box Edge. Depois de configurar a função de computação, o Data Box Edge pode transformar os dados antes de enviar para o Azure.

Esse procedimento pode levar cerca de 10 a 15 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo do Data Box Edge, certifique-se de que:

- Você ativou o dispositivo do Data Box Edge conforme descrito em [Conectar, configurar e ativar o Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar a computação

Para configurar a computação no Data Box Edge, você criará um recurso do Hub IoT.

1. No portal do Azure do recurso do Data Box Edge, acesse Visão geral. No painel direito, no bloco **Computação**, selecione **Introdução**.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. No bloco **Configurar computação de borda**, selecione **Configurar computação**.
3. Na folha **Configurar computação de borda**, insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |Hub IoT     | Escolha **Novo** ou **Existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Data Box Edge.     |
    |NOME     |Insira um nome para o recurso do Hub IoT.         |

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecione **Criar**. A criação do recurso do Hub IoT leva alguns minutos. Depois que o recurso do Hub IoT for criado, o bloco **Configurar computação** será atualizado para mostrar a configuração de computação. Para confirmar que a função de computação de borda foi configurada, selecione **Exibir computação** no bloco **Configurar computação**.
    
    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Se a caixa de diálogo **Configurar Computação** fechar antes que o Hub IoT seja associado ao dispositivo do Data Box Edge, o Hub IoT será criado, mas não será mostrado na configuração de computação. 
    
    Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. Um tempo de execução do IoT Edge também está em execução no dispositivo do IoT Edge. No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar compartilhamentos

Para a implantação simples neste tutorial, você precisará de dois compartilhamentos: um compartilhamento do Edge e outro compartilhamento local do Edge.

1. Adicione um compartilhamento do Microsoft Edge no dispositivo seguindo as seguintes etapas:

    1. No recurso do Data Box Edge, acesse **Computação de borda > Introdução**.
    2. No bloco **Adicionar compartilhamentos**, selecione **Adicionar**.
    3. Na folha **Adicionar compartilhamento**, forneça o nome do compartilhamento e selecione o tipo de compartilhamento.
    4. Para montar o compartilhamento do Microsoft Edge, marque a caixa de seleção **Usar o compartilhamento com a computação de borda**.
    5. Selecione a **Conta de armazenamento**, o **Serviço de armazenamento**, um usuário existente e, em seguida, selecione **Criar**.

        ![Adicionar um compartilhamento do Microsoft Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se você criou um compartilhamento NFS local, use a seguinte opção de comando rsync (compartilhamento remoto) para copiar os arquivos no compartilhamento:

    `rsync <source file path> < destination file path>`

    Para obter mais informações sobre o comando rsync, acesse a [Documentação do rsync](https://www.computerhope.com/unix/rsync.htm).

    O compartilhamento do Microsoft Edge será criado, e você receberá uma notificação de êxito na criação. A lista de compartilhamento pode ser atualizada, mas você deve aguardar a criação do compartilhamento ser concluída.

2. Adicione um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge repetindo todas as etapas da etapa anterior e marcando a caixa de seleção **Configurar como o compartilhamento local do Microsoft Edge**. Os dados no compartilhamento local permanecem no dispositivo.

    ![Adicionar um compartilhamento local do Microsoft Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecione **Adicionar compartilhamentos** para ver a lista atualizada de compartilhamentos.

    ![Lista atualizada de compartilhamentos](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Você pode adicionar um módulo personalizado ou pré-criado. Não há módulos personalizados neste dispositivo do Edge. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo em C# para o dispositivo Data Box Edge](data-box-edge-create-iot-edge-module.md).

Nesta seção, você adiciona um módulo personalizado ao dispositivo do IoT Edge que foi criado em [Desenvolver um módulo em C# para o Data Box Edge](data-box-edge-create-iot-edge-module.md). Esse módulo personalizado usa arquivos de um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge e move-os para um compartilhamento do Microsoft Edge (nuvem) no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem.

1. Acesse **Computação de borda > Introdução**. No bloco **Adicionar módulos**, selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. Na folha **Configurar e adicionar módulo**, insira os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |NOME     | Um nome exclusivo para o módulo. Esse módulo é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao Data Box Edge.        |
    |URI da imagem     | O URI da imagem para a imagem de contêiner correspondente ao módulo.        |
    |Credenciais necessárias     | Se essa opção for marcada, o nome de usuário e a senha serão usados para recuperar os módulos com uma URL correspondente.        |
    |Compartilhamento de entrada     | Selecione um compartilhamento de entrada. O compartilhamento local do Microsoft Edge é o compartilhamento de entrada, nesse caso. O módulo usado aqui move os arquivos do compartilhamento local do Microsoft Edge para um compartilhamento do Microsoft Edge, em que são carregados para a nuvem.        |
    |Compartilhamento de saída     | Selecione um compartilhamento de saída. O compartilhamento do Microsoft Edge é o compartilhamento de saída, nesse caso.        |
    |Tipo de gatilho     | Selecione **Arquivo** ou **Agendamento**. Um gatilho de arquivo é acionado sempre que ocorre um evento de arquivo, como uma gravação de arquivo no compartilhamento de entrada. Um gatilho agendado é acionado de acordo com um agendamento definido por você.         |
    |Nome do gatilho     | Um nome exclusivo para o gatilho.         |
    |Variáveis de ambiente| Informações opcionais que ajudarão a definir o ambiente no qual o módulo será executado.   |

    ![Adicionar e configurar o módulo](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. O bloco **Adicionar módulo** é atualizado para indicar que o módulo foi implantado. 

    ![Módulo implantado](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

Para verificar se o módulo está em execução, faça o seguinte:

1. Selecione o bloco **Adicionar módulo**. Isso direcionará você para a folha **Módulos**. Na lista de módulos, identifique o módulo implantado. O status do tempo de execução do módulo adicionado deve ser *em execução*.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  No Explorador de Arquivos, conecte-se aos compartilhamentos do Microsoft Edge e local do Microsoft Edge criados anteriormente.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Adicione dados ao compartilhamento de local.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Os dados são movidos para o compartilhamento em nuvem.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Os dados são então enviados por push do compartilhamento em nuvem para a conta de armazenamento. Para exibir os dados, acesse o Gerenciador de Armazenamento.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Você concluiu o processo de validação.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

Para saber como administrar o dispositivo do Data Box Edge, confira:

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
