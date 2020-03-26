---
title: Tutorial para filtrar e analisar dados para implantação avançada com a computação no Azure Data Box Edge | Microsoft Docs
description: Saiba como configurar a função de computação no Data Box Edge e usá-la para fluxo de implantação avançada antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222883"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Tutorial: Transformar dados com o Azure Data Box Edge para o fluxo de implantação avançada

Este tutorial descreve como configurar uma função de computação para um fluxo de implantação avançada no dispositivo do Azure Data Box Edge. Depois de configurar a função de computação, o Data Box Edge pode transformar os dados antes de enviar para o Azure.

Computação pode ser configurada para o fluxo de implantação simples ou avançada em seu dispositivo.

|                  | Implantação simples                                | Implantação avançada                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Destinado a     | Administradores de TI                                | Desenvolvedores                            |
| Type             | Usar o serviço Data Box Edge para implantar módulos      | Usar o serviço de Hub IoT para implantar módulos |
| Módulos implantados | Single                                           | Módulos múltiplos ou encadeados           |


Esse procedimento pode levar cerca de 20 a 30 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um gatilho
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo do Data Box Edge, certifique-se de que:

- Você ativou o dispositivo do Data Box Edge conforme descrito em [Conectar, configurar e ativar o Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar a computação

Para configurar a computação no Data Box Edge, você criará um recurso do Hub IoT.

1. No portal do Azure do recurso do Data Box Edge, acesse **Visão geral**. No painel direito, no bloco **Computação**, selecione **Introdução**.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. No bloco **Configurar computação de borda**, selecione **Configurar computação**.

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Na folha **Configurar computação de borda**, insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |Hub IoT     | Escolha **Novo** ou **Existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Data Box Edge.     |
    |Nome     |Insira um nome para o recurso do Hub IoT.         |

    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecione **Criar**. A criação do recurso do Hub IoT leva alguns minutos. Depois que o recurso do Hub IoT for criado, o bloco **Configurar computação de borda** será atualizado para mostrar a configuração de computação. Para confirmar que a função de computação de borda foi configurada, selecione **Exibir configuração** no bloco **Configurar computação**.
    
    ![Introdução à computação](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. Um runtime do IoT Edge também está em execução no dispositivo do IoT Edge.

    No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar compartilhamentos

Para a implantação avançada neste tutorial, você precisará de dois compartilhamentos: um compartilhamento do Edge e outro compartilhamento local do Edge.

1. Adicione um compartilhamento do Microsoft Edge no dispositivo seguindo as seguintes etapas:

    1. No recurso do Data Box Edge, acesse **Computação de borda > Introdução**.
    2. No bloco **Adicionar compartilhamentos**, selecione **Adicionar**.
    3. Na folha **Adicionar compartilhamento**, forneça o nome do compartilhamento e selecione o tipo de compartilhamento.
    4. Para montar o compartilhamento do Microsoft Edge, marque a caixa de seleção **Usar o compartilhamento com a computação de borda**.
    5. Selecione a **Conta de armazenamento**, o **Serviço de armazenamento**, um usuário existente e, em seguida, selecione **Criar**.

        ![Adicionar um compartilhamento do Microsoft Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Após o compartilhamento do Edge ser criado, você receberá uma notificação de êxito na criação. A lista de compartilhamentos é atualizada para refletir o novo compartilhamento.

2. Adicione um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge repetindo todas as etapas da etapa anterior e marcando a caixa de seleção **Configurar como o compartilhamento local do Microsoft Edge**. Os dados no compartilhamento local permanecem no dispositivo.

    ![Adicionar um compartilhamento local do Microsoft Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na folha **Compartilhamentos**, você vê a lista de compartilhamentos atualizada.

    ![Lista atualizada de compartilhamentos](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Para exibir as propriedades do compartilhamento local recém-criado, basta selecioná-lo na lista. Na caixa **Ponto de montagem local para módulos de computação de borda**, copie o valor correspondente a esse compartilhamento.

    Você usará esse ponto de montagem local ao implantar o módulo.

    ![A caixa "Ponto de montagem local para módulos de computação de borda"](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Para exibir as propriedades do compartilhamento do Edge recém-criado, selecione-o na lista. Na caixa **Ponto de montagem local para módulos de computação de borda**, copie o valor correspondente a esse compartilhamento.

    Você usará esse ponto de montagem local ao implantar o módulo.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Adicionar um gatilho

1. Acesse **Computação de borda > Gatilhos**. Selecione **+ Adicionar gatilho**.

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Na folha **Adicionar gatilho**, adicione os valores de entrada a seguir.

    |Campo  |Valor  |
    |---------|---------|
    |Nome do gatilho     | Um nome exclusivo para o gatilho.         |
    |Tipo de gatilho     | Selecione o gatilho **Arquivo**. Um gatilho de arquivo é acionado sempre que ocorre um evento de arquivo, como uma gravação de arquivo no compartilhamento de entrada. Um gatilho agendado, por sua vez, é acionado de acordo com um agendamento definido por você. Neste exemplo, precisamos de um gatilho de arquivo.    |
    |Compartilhamento de entrada     | Selecione um compartilhamento de entrada. O compartilhamento local do Microsoft Edge é o compartilhamento de entrada, nesse caso. O módulo usado aqui move os arquivos do compartilhamento local do Microsoft Edge para um compartilhamento do Microsoft Edge, em que são carregados para a nuvem.        |

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Você será notificado depois que o gatilho for criado. A lista de gatilhos é atualizada para exibir o gatilho criado recentemente. Selecione o gatilho que você acabou de criar.

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Copie e salve a rota de exemplo. Você modificará essa rota de exemplo e a usará mais tarde no Hub IoT.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Adicionar gatilho](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Adicionar um módulo

Não há módulos personalizados neste dispositivo do Edge. Você pode adicionar um módulo personalizado ou pré-criado. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo em C# para o dispositivo Data Box Edge](data-box-edge-create-iot-edge-module.md).

Nesta seção, você adiciona um módulo personalizado ao dispositivo do IoT Edge que foi criado em [Desenvolver um módulo em C# para o Data Box Edge](data-box-edge-create-iot-edge-module.md). Esse módulo personalizado usa arquivos de um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge e move-os para um compartilhamento do Microsoft Edge (nuvem) no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem.

1. Acesse **Computação de borda > Introdução**. No bloco **Adicionar módulos**, selecione o tipo de cenário como **avançado**. Selecione **Ir para o Hub IoT**.

    ![Selecionar a implantação avançada](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. No recurso do Hub IoT, acesse **Dispositivo do IoT Edge** e, em seguida, selecione seu dispositivo do IoT Edge.

    ![Acessar o dispositivo do IoT Edge no Hub IoT](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Em **Detalhes do dispositivo**, selecione **Definir Módulos**.

    ![O link Definir Módulos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Em **Adicionar Módulos**, faça o seguinte:

    1. Insira o nome, o endereço, o nome de usuário e a senha para as configurações do registro de contêiner do módulo personalizado.
    O nome, o endereço e as credenciais listadas são usados para recuperar módulos com uma URL correspondente. Para implantar este módulo, em **Módulos de implantação**, selecione **Módulo do IoT Edge**. Este módulo do IoT Edge é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao seu dispositivo do Data Box Edge.

        ![A página Definir Módulos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Especifique as configurações para o módulo personalizado do IoT Edge. Insira os valores a seguir.
     
        |Campo  |Valor  |
        |---------|---------|
        |Nome     | Um nome exclusivo para o módulo. Esse módulo é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao Data Box Edge.        |
        |URI da imagem     | O URI da imagem para a imagem de contêiner correspondente ao módulo.        |
        |Credenciais necessárias     | Se essa opção for marcada, o nome de usuário e a senha serão usados para recuperar os módulos com uma URL correspondente.        |
    
        Na caixa **Opções de Criação de Contêiner**, insira os pontos de montagem locais dos módulos do Edge que você copiou nas etapas anteriores para o compartilhamento do Edge e o compartilhamento local do Edge.

        > [!IMPORTANT]
        > Os caminhos usados aqui são montados em seu contêiner, portanto, eles devem coincidir com o que a funcionalidade em seu contêiner espera. Se você está seguindo as instruções em [Criar um módulo personalizado](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), o código especificado nesse módulo espera os caminhos copiados. Não modifique esses caminhos.
    
        Na caixa **Opções de Criação de Contêiner**, cole o exemplo a seguir:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Forneça também todas as variáveis de ambiente usadas para seu módulo. Variável de ambiente fornecem informações opcionais que ajudam a definir o ambiente no qual o módulo é executado.

        ![A caixa Opções de Criação de Contêiner](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Se necessário, defina as configurações avançadas de runtime do Edge e, em seguida, clique em **Avançar**.

        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Em **Especificar Rotas**, defina rotas entre módulos.  
    
    ![A seção Especificar Rotas](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Você pode substituir *rota* pela cadeia de caracteres a seguir, que você copiou anteriormente. Neste exemplo, insira o nome do compartilhamento local que efetuará push de dados para o compartilhamento de nuvem. Substitua o `modulename` pelo nome do módulo. Selecione **Avançar**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![A seção Especificar Rotas](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Em **Examinar implantação**, examine todas as configurações e, em seguida, selecione **Enviar** para enviar o módulo para implantação.

    ![A página Definir Módulos](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Essa ação inicia a implantação do módulo. Depois que a implantação for concluída, o **Status de runtime** do módulo será **em execução**.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

Execute as etapas a seguir para verificar a transformação e transferência de dados para o Azure.
 
1.  No Explorador de Arquivos, conecte-se aos compartilhamentos do Microsoft Edge e local do Microsoft Edge criados anteriormente.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Adicione dados ao compartilhamento de local.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Os dados são movidos para o compartilhamento em nuvem.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Os dados são então enviados por push do compartilhamento em nuvem para a conta de armazenamento. Para exibir os dados, vá para sua conta de armazenamento e, em seguida, selecione **Gerenciador de Armazenamento**. Você pode exibir os dados carregados em sua conta de armazenamento.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Você concluiu o processo de validação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um gatilho
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

Para saber como administrar o dispositivo do Data Box Edge, confira:

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
