---
title: Criar usuários em massa no portal de Azure Active Directory | Microsoft Docs
description: Adicionar usuários em massa no centro de administração do Azure AD no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03f6e3d6edde51598b1d148469aceb1ff3b3d636
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203379"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Criar usuários em massa no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de criação e exclusão de usuário em massa e dá suporte ao download de listas de usuários. Basta preencher o modelo de valores separados por vírgulas (CSV) que você pode baixar no portal do Azure AD.

## <a name="required-permissions"></a>Permissões necessárias

Para criar usuários em massa no portal de administração, você deve estar conectado como administrador global ou administrador de usuários.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV de carregamento em massa para ajudá-lo a criar com êxito os usuários do Azure AD em massa. O modelo CSV que você baixa pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: a primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Títulos de coluna** &lt;: o formato dos títulos de coluna é o *nome* &gt; do item [PropertyName &lt;] *necessário ou em branco*&gt;. Por exemplo, `Name [displayName] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas próprias entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas ou o carregamento não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas adicionais adicionadas são ignoradas e não processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.

## <a name="to-create-users-in-bulk"></a>Para criar usuários em massa

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No Azure AD, selecione **usuários** > **criar em massa**.
1. Na página **criação em massa do usuário** , selecione **baixar** para receber um arquivo CSV (valores separados por vírgula) válido das propriedades do usuário e adicione adicionar usuários que você deseja criar.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-add/upload-button.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que você deseja criar. Os únicos valores necessários são **nome**, **nome principal do usuário**, **senha inicial** e **bloquear entrada (Sim/não)**. Em seguida, salve o arquivo.

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. Na página **criar usuário em massa** , em carregar o arquivo CSV, navegue até o arquivo. Quando você seleciona o arquivo e clica em **Enviar**, a validação do arquivo CSV é iniciada.
1. Depois que o conteúdo do arquivo for validado, você verá o **arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que importa os novos usuários.
1. Quando a operação de importação for concluída, você verá uma notificação do status do trabalho de operação em massa.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **resultados da operação em massa** . O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o status

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa** .

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Em seguida, você pode verificar para ver se os usuários que você criou existem na organização do Azure AD na portal do Azure ou usando o PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifique os usuários no portal do Azure

1. [Entre no centro de administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Em **Mostrar**, selecione **todos os usuários** e verifique se os usuários que você criou estão listados.

### <a name="verify-users-with-powershell"></a>Verificar usuários com o PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários que você criou estão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa para criar usuários pode ser executada por até uma hora. Isso permite a criação em massa de pelo menos 50.000 usuários.

## <a name="next-steps"></a>Próximas etapas

- [Exclusão de usuários em massa](users-bulk-delete.md)
- [Baixar lista de usuários](users-bulk-download.md)
- [Restauração de usuários em massa](users-bulk-restore.md)
