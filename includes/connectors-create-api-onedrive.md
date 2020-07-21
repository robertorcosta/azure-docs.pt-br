---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524193"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta do [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Para poder usar sua conta do OneDrive em um aplicativo lógico, autorize-o a se conectar à sua conta do OneDrive.  Você pode fazer isso de forma fácil usando seu aplicativo lógico no portal do Azure. 

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do OneDrive:

1. Crie um aplicativo lógico. No designer de Aplicativos Lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e então digite "onedrive" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
   ![Uma caixa de diálogo intitulada "mostrar APIs gerenciadas da Microsoft" tem uma caixa de pesquisa que contém "onedrive". Abaixo, está uma lista de quatro gatilhos. Primeiro, na lista, é "OneDrive-quando um arquivo é criado", que é selecionado.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se você não tiver criado quaisquer conexões OneDrive anteriormente, será solicitado a entrar usando suas credenciais do OneDrive:  
   ![Uma caixa de diálogo intitulada "OneDrive-quando um arquivo é criado" tem um botão rotulado "entrar".](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **Entrar** e insira seu nome de usuário e senha. Selecione **entrar**:  
   ![Uma caixa de diálogo intitulada "entrar" instrui você a "usar seu conta Microsoft". Ele tem duas caixas de texto rotuladas como "email ou telefone" e "senha". ela também tem uma caixa de seleção rotulada como "Mantenha-me conectado" e um botão rotulado como "entrar".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do OneDrive e usá-los. 
4. Selecione **Sim** para autorizar o aplicativo lógico para usar sua conta do OneDrive:  
   ![Uma caixa de diálogo intitulada "permitir que este aplicativo acesse suas informações?" solicita permissão para fazer as quatro coisas a seguir: 1) "entrar automaticamente", 2) "acessar seus endereços de email", 3) "acessar suas informações a qualquer momento" e 4) "acessar arquivos do OneDrive". Há um botão "Sim" para conceder permissão e um botão "não" para negar. Há um link para alterar essas permissões de aplicativo.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observe que a conexão foi criada. Agora, continue com as outras etapas no seu aplicativo lógico:  
   ![Uma caixa de diálogo intitulada "quando um arquivo é criado" tem uma caixa de texto intitulada "pasta" com um botão procurar associado.](./media/connectors-create-api-onedrive/onedrive-5.png)

