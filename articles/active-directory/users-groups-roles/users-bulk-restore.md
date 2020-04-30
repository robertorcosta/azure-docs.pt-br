---
title: Restauração em massa de usuários excluídos no portal de Azure Active Directory | Microsoft Docs
description: Restaurar usuários excluídos em massa no centro de administração do Azure AD no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11f35c7615135f5aa6c63d5d05898d139df61d0d
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203277"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Restauração em massa de usuários excluídos no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de restauração de usuário em massa e oferece suporte ao download de listas de usuários, grupos e membros do grupo.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV para ajudá-lo a restaurar com êxito os usuários do Azure AD em massa. O modelo CSV que você baixa pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: a primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Títulos de coluna** &lt;: o formato dos títulos de coluna é o *nome* &gt; do item [PropertyName &lt;] *necessário ou em branco*&gt;. Por exemplo, `Object ID [objectId] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas próprias entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas ou o carregamento não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas adicionais adicionadas são ignoradas e não processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-restore-users"></a>Para usuários de restauração em massa

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização do Azure AD.
1. No Azure AD, selecione **usuários** > **excluídos**.
1. Na página **usuários excluídos** , selecione **restauração em massa** para carregar um arquivo CSV válido de propriedades dos usuários a serem restaurados.

   ![Selecione o comando restauração em massa na página usuários excluídos](./media/users-bulk-restore/bulk-restore.png)

1. Abra o modelo CSV e adicione uma linha para cada usuário que você deseja restaurar. O único valor necessário é **ObjectID**. Em seguida, salve o arquivo.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-restore/upload-button.png)

1. Na página **restauração em massa** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo e clica em **Enviar**, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que restaura os usuários.
1. Quando a operação de restauração for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **resultados da operação em massa** . O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o status

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa** .

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

Em seguida, você pode verificar para ver se os usuários restaurados existem na organização do Azure AD na portal do Azure ou usando o PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Exibir usuários restaurados no portal do Azure

1. [Entre no centro de administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar**, selecione **todos os usuários** e verifique se os usuários restaurados estão listados.

### <a name="view-users-with-powershell"></a>Exibir usuários com o PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários restaurados estão listados.

## <a name="next-steps"></a>Próximas etapas

- [Usuários de importação em massa](users-bulk-add.md)
- [Exclusão de usuários em massa](users-bulk-delete.md)
- [Baixar lista de usuários](users-bulk-download.md)
