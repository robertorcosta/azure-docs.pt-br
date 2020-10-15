---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89080021"
---
Se essa for a primeira vez que estiver usando o Gerenciador de Armazenamento, você precisará executar as etapas a seguir.

1. Na barra de comandos superior, acesse **Editar > Definir APIs do Azure Stack como Destino**.

    ![Configurar Gerenciador de Armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Reinicie o Gerenciador de Armazenamento para as alterações entrarem em vigor.


Siga estas etapas para se conectar à conta de armazenamento e verificar a conexão.

1. No Gerenciador de Armazenamento, selecione as contas de armazenamento. Clique com o botão direito do mouse e selecione a opção **Conectar ao Armazenamento do Azure** . 

    ![Configurar Gerenciador de Armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. Na caixa de diálogo **Conectar-se ao Armazenamento do Azure**, selecione **Usar um nome e uma chave da conta de armazenamento**.

    ![Configurar Gerenciador de Armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. Na caixa de diálogo **Conectar ao Nome e à Chave**, execute as seguintes etapas:

    1. Insira um nome de exibição para sua conta de armazenamento do Edge. 
    2. Forneça o nome da conta de armazenamento do Edge.
    3. Cole a chave de acesso obtida das APIs locais do dispositivo por meio do Azure Resource Manager.
    4. Selecione o domínio do Armazenamento como **Outros (inserir abaixo)** e forneça o sufixo do ponto de extremidade do serviço Blob no formato: `<appliance name>.<DNSdomain>`. 
    5. Marque a opção **Usar HTTP**, pois a transferência é por meio de *HTTP*. 
    6. Selecione **Avançar**.

    ![Configurar Gerenciador de Armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. Na caixa de diálogo **Resumo da Conexão**, examine as informações fornecidas. Selecione **Conectar**.

    ![Configurar Gerenciador de Armazenamento](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. A conta adicionada com êxito é exibida no painel esquerdo do Gerenciador de Armazenamento com (Externa, Outros) acrescentado ao nome. Selecione **Contêineres de Blobs** para ver o contêiner.

    ![Exibir contêineres de blobs](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

A próxima etapa a ser verificada é se a transferência de dados está realmente funcionando corretamente por essa conexão.

Execute as etapas a seguir para carregar dados em sua conta de armazenamento do Edge no dispositivo e eles serão automaticamente colocados em camadas na conta de armazenamento mapeada do Azure.

1. Selecione o contêiner para o qual você deseja carregar os dados na conta de armazenamento do Edge. Escolha **Carregar** e selecione **Carregar arquivos**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. Na caixa de diálogo **Carregar arquivos**, navegue até os arquivos e selecione os que deseja carregar. Selecione **Avançar**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Verifique se os arquivos foram carregados. Os arquivos carregados são exibidos no contêiner.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Em seguida, você se conectará à conta de Armazenamento do Azure que foi mapeada para esta conta de armazenamento do Edge. Todos os dados que são carregados na conta de armazenamento do Edge devem ser automaticamente colocados em camadas na conta de Armazenamento do Azure. 
    
    Para obter a cadeia de conexão da conta de Armazenamento do Azure, acesse **Conta de Armazenamento do Azure > Chaves de Acesso** e copie a cadeia de conexão.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Use a cadeia de conexão para anexar à conta de Armazenamento do Azure.  

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. Na caixa de diálogo **Resumo da Conexão**, examine as informações fornecidas. Selecione **Conectar**.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Você verá que os arquivos que você carregou na conta de armazenamento do Edge foram transferidos para a conta de Armazenamento do Azure.

    ![Verificar transferência de dados](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)