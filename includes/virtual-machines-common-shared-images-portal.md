---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903658"
---
## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

> [!NOTE]
> Se você se registrou para usar Galerias de Imagens Compartilhadas `Microsoft.Compute` durante a pré-visualização, talvez seja necessário recadastrar o provedor. Shell [de nuvem aberta](https://shell.azure.com/bash) e digite:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços.  Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

O exemplo a seguir cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG*.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Use o tipo **Galeria de imagens compartilhadas** na caixa de pesquisa e selecione **Galeria de imagens compartilhadas** nos resultados.
1. Na página **da galeria de imagens compartilhadas,** clique em **Criar**.
1. Selecione a assinatura correta.
1. No **grupo Resource,** selecione **Criar novo** e digite *myGalleryRG* para o nome.
1. Em **Nome,** digite *myGallery* para o nome da galeria.
1. Deixe o padrão para **Região**.
1. Você pode digitar uma breve descrição da galeria, como *minha galeria de imagens para testes.* e, em seguida, clique **em Revisar + criar**.
1. Após a validação passar, selecione **Criar**.
1. Quando a implantação estiver concluída, **selecione Ir para recurso**.
   
## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem criam um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontilhados, traços e períodos. Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [Definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Crie a definição de imagem da galeria dentro de sua galeria. Neste exemplo, a imagem da galeria é chamada *myImageDefinition*.

1. Na página da nova galeria de imagens, selecione **Adicionar uma nova definição** de imagem na parte superior da página. 
1. Para **nome de definição de imagem,** digite *myImageDefinition*.
1. Para **o sistema operacional,** selecione a opção correta com base na VM de origem.
1. Para **a geração VM,** selecione a opção com base na VM de origem. Na maioria dos casos, este será *o Gen 1*. Para obter mais informações, consulte [Suporte para VMs de geração 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. Para **o estado do sistema operacional,** selecione a opção com base na VM de origem. Para obter mais informações, consulte [Generalizado e especializado](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Para **Editor,** digite *myPublisher*. 
1. Para **Oferecer,** digite *myOffer*.
1. Para **SKU,** digite *mySKU*.
1. Quando terminar, selecione **'Revisar + criar'.**
1. Depois que a definição da imagem passar pela validação, selecione **Criar**.
1. Quando a implantação estiver concluída, **selecione Ir para recurso**.


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir de uma imagem gerenciada. Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher regiões-alvo para replicação, lembre-se de que você também precisa incluir a região *de origem* como um alvo para replicação.

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Os passos para criar uma versão de imagem são ligeiramente diferentes, dependendo se a fonte é uma imagem generalizada ou um instantâneo de uma VM especializada. 

### <a name="option-generalized"></a>Opção: Generalizado

1. Na página para a definição de sua imagem, **selecione Adicionar versão** na parte superior da página.
1. Em **Região,** selecione a região onde sua imagem gerenciada é armazenada. As versões de imagem precisam ser criadas na mesma região que a imagem gerenciada da sua criação.
1. Para **Nome,** tipo *1.0.0*. O nome da versão da imagem deve seguir *principal*. *menor*. formato *de patch* usando inteiros. 
1. Na **imagem Origem,** selecione a imagem gerenciada de origem a partir do drop-down.
1. Em **Excluir do mais recente,** deixe o valor padrão de *Não*.
1. Para **a data de fim de vida,** selecione uma data do calendário que é de alguns meses no futuro.
1. Em **Replicação,** deixe **a contagem de réplicas padrão** como 1. Você precisa replicar para a região de origem, então deixe a primeira réplica como padrão e, em seguida, escolha uma segunda região de réplica para ser *leste dos EUA*.
1. Quando terminar, selecione **Revisar + criar**. O Azure validará a configuração.
1. Quando a versão da imagem passar pela validação, selecione **Criar**.
1. Quando a implantação estiver concluída, **selecione Ir para recurso**.

Pode levar um tempo para replicar a imagem em todas as regiões alvo.

### <a name="option-specialized"></a>Opção: Especializada

1. Na página para a definição de sua imagem, **selecione Adicionar versão** na parte superior da página.
1. Em **Região,** selecione a região onde seu snapshot está armazenado. As versões de imagem precisam ser criadas na mesma região que a fonte de onde são criadas.
1. Para **Nome,** tipo *1.0.0*. O nome da versão da imagem deve seguir *principal*. *menor*. formato *de patch* usando inteiros. 
1. No **snapshot do disco DO SISTEMA**OPERACIONAL, selecione o instantâneo da VM de origem a partir do drop-down. Se a VM de origem tiver um disco de dados que você gostaria de incluir, selecione o número **lUN** correto na parte baixa e selecione o snapshot do disco de dados para **snapshot de disco de dados**. 
1. Em **Excluir do mais recente,** deixe o valor padrão de *Não*.
1. Para **a data de fim de vida,** selecione uma data do calendário que é de alguns meses no futuro.
1. Em **Replicação,** deixe **a contagem de réplicas padrão** como 1. Você precisa replicar para a região de origem, então deixe a primeira réplica como padrão e, em seguida, escolha uma segunda região de réplica para ser *leste dos EUA*.
1. Quando terminar, selecione **Revisar + criar**. O Azure validará a configuração.
1. Quando a versão da imagem passar pela validação, selecione **Criar**.
1. Quando a implantação estiver concluída, **selecione Ir para recurso**.

## <a name="share-the-gallery"></a>Compartilhe a galeria

Recomendamos que você compartilhe o acesso ao nível da galeria de imagens. O seguinte orienta você a compartilhar a galeria que você acabou de criar.

1. Abra o [portal Azure.](https://portal.azure.com)
1. No menu à esquerda, selecione **Grupos de recursos**. 
1. Na lista de grupos de recursos, selecione **myGalleryRG**. A lâmina para o seu grupo de recursos será aberta.
1. No menu à esquerda da página **myGalleryRG,** selecione **Controle de acesso (IAM)**. 
1. Em **Adicionar uma atribuição de função,** selecione **Adicionar**. O **painel adicionar um papel de atribuição** será aberto. 
1. Em **Função,** selecione **'Leitor'.**
1. Em **acesso a atribuição**de , deixe o padrão do usuário, grupo ou principal de **serviço do Azure AD**.
1. Em **Select,** digite o endereço de e-mail da pessoa que você gostaria de convidar.
1. Se o usuário estiver fora da sua organização, você verá a mensagem **Este usuário será enviado um e-mail que permite que ele colabore com a Microsoft.** Selecione o usuário com o endereço de e-mail e clique **em Salvar**.

Se o usuário estiver fora da sua organização, receberá um convite por e-mail para participar da organização. O usuário precisa aceitar o convite, então poderá ver a galeria e todas as definições de imagem e versões em sua lista de recursos.

