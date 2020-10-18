---
title: Gerenciar registros de dispositivo para o serviço de provisionamento de dispositivos no Hub IoT do Azure no portal do Azure
description: Como gerenciar registros de dispositivo para seu DPS (serviço de provisionamento de dispositivos) no portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2fbcacd7f2094f9b0b9dcea3fea4d804fd96923e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165308"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerenciar os registros de dispositivo com o Portal do Azure

Um *registro de dispositivos* cria um registro de um único dispositivo ou um grupo de dispositivos que pode em algum momento registrar com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure. O registro contém a configuração inicial desejada para os dispositivos como parte desse registro, incluindo o Hub IoT desejado. Este artigo mostra como gerenciar registros de dispositivo para o serviço de provisionamento.


## <a name="create-a-device-enrollment"></a>Criar um registro do dispositivo

Há duas maneiras em que você pode registrar seus dispositivos com o serviço de provisionamento:

* Um **Grupo de registros** é uma entrada para um grupo de dispositivos que compartilham um mecanismo de atestado comum de certificados X.509, assinados pelo mesmo certificado de autenticação, que pode ser o [certificado raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermediário](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), usado para produzir o certificado do dispositivo no dispositivo físico. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário. 

    Você pode criar um grupo de registro no portal para um grupo de dispositivos usando as etapas a seguir:

  1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.  
  1. Clique no serviço de provisionamento do dispositivo no qual você deseja registrar seu dispositivo na lista de recursos.  
  1. No serviço de provisionamento:  
     a. Clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registros**.  
     b. Clique no botão **Adicionar** na parte superior.  
     c. Quando o painel "Adicionar Grupos de Registros" for exibido, insira as informações da entrada da lista de registro.  O **Nome de Grupo** é obrigatório. Selecione também "AC ou Intermediária" para **Tipo de certificado** e carregue o **Certificado principal** raiz para o grupo de dispositivos.  
     d. Clique em **Save** (Salvar). Após a criação bem-sucedida de seu grupo de registro, você deverá ver o nome do grupo aparecer sob a guia **Grupos de Registro**.  

     [![Grupo de registro no portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Um **registro Individual** é uma entrada para um único dispositivo que pode registrar. Os registros individuais podem usar certificados x509 ou tokens de SAS (de um TPM virtual ou físico) como mecanismos de certificação. É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual como o mecanismo de Atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

    Você pode criar um registro individual no portal usando as etapas a seguir:

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    1. Clique no serviço de provisionamento do dispositivo no qual você deseja registrar seu dispositivo na lista de recursos.
    1. No serviço de provisionamento:  
       a. Clique em **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.  
       b. Clique no botão **Adicionar** na parte superior.   
       c. Quando o painel "Adicionar Registro" for exibido, insira as informações da entrada da lista de registro. Primeiro, selecione o **Mecanismo** de certificação para o dispositivo (X.509 ou TPM). A certificação X.509 exige upload do **Certificado principal** para o dispositivo. O TPM exige que você insira a **Chave de Atestado** e a **ID do Registro** para o dispositivo.  
       d. Clique em **Save** (Salvar). Após a criação bem-sucedida de seu grupo de registro, você deverá ver o dispositivo aparecer sob a guia **Registros Individuais**.  

       [![Registro individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de registro
Você pode atualizar uma entrada de registro existente no portal usando as etapas a seguir:

1. Abra o serviço de provisionamento do dispositivo no Portal do Azure e clique em **Gerenciar Registros**. 
1. Navegue até a entrada de registro que você deseja modificar. Clique na entrada, que abre informações de resumo sobre o registro do dispositivo. 
1. Nessa página, você pode modificar os itens que não sejam do tipo de segurança e credenciais, tais como o Hub IoT ao qual dispositivo deve ser vinculado, bem como a ID do dispositivo. Você também pode modificar o estado inicial do dispositivo gêmeo. 
1. Depois de concluído, clique em **Salvar** para atualizar o registro do dispositivo. 

    ![Atualizar registro no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover um registro do dispositivo
Em casos em que os dispositivos não precisam ser provisionados para nenhum Hub IoT, você poderá remover a entrada de registro relacionado no portal usando as etapas a seguir:

1. Abra o serviço de provisionamento do dispositivo no Portal do Azure e clique em **Gerenciar Registros**. 
1. Navegue até a entrada de registro que você deseja remover e selecione-a. 
1. Clique no botão **Excluir** na parte superior e, em seguida, selecione **Sim** quando a confirmação for solicitada. 
1. Depois que a ação for concluída, você verá sua entrada removida da lista de registros do dispositivo. 
 
    ![Remover registro no portal](./media/how-to-manage-enrollments/remove-enrollment.png)


