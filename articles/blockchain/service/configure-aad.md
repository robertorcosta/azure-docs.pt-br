---
title: Configure o acesso ao Azure Active Directory - Azure Blockchain Service
description: Como configurar o Azure Blockchain Service com acesso ao Azure Active Directory
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455855"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Como configurar o acesso ao Azure Active Directory para o Azure Blockchain Service

Neste artigo, você aprende como conceder acesso e se conectar aos ates do Azure Blockchain Service usando iDs de usuário, grupo ou aplicativo do Azure Active Directory (Azure AD).

O Azure AD fornece gerenciamento de identidade baseado em nuvem e permite que você use uma única identidade em toda uma empresa e acesse aplicativos no Azure. O Azure Blockchain Service é integrado ao Azure AD e oferece benefícios como federação de ID, login único e autenticação multifatorial.

## <a name="prerequisites"></a>Pré-requisitos

* [Crie um membro blockchain usando o portal Azure](create-member.md)

## <a name="grant-access"></a>Conceder acesso

Você pode conceder acesso tanto ao nível do membro quanto ao nível do nó. A concessão de direitos de acesso ao nível de membro concederá, por sua vez, acesso a todos os nós o membro.

### <a name="grant-member-level-access"></a>Acesso ao nível do membro do subsídio

Para conceder permissão de acesso ao nível de membro.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Navegar para **o controle de acesso (IAM) > adicionar > adicionar atribuição de função**.
1. Selecione a função **Desbloqueio ao acesso ao nó do membro blockchain (Preview)** e adicione o objeto Azure AD ID ao qual deseja conceder acesso. O objeto Azure AD ID pode ser:

    | Objeto Azure AD | Exemplo |
    |-----------------|---------|
    | Usuário do Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Grupo Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID do aplicativo  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Adicionar atribuição de função](./media/configure-aad/add-role-assignment.png)

1. Selecione **Salvar**.

### <a name="grant-node-level-access"></a>Acesso ao nível do nó de concessão

Você pode conceder acesso ao nível do nó navegando para a segurança do nó e clicar no nome do nó que deseja conceder acesso.

Selecione a função Desbloqueio ao acesso ao nó do membro blockchain (Preview) e adicione o objeto Azure AD ID ao qual deseja conceder acesso.

Para obter mais informações, consulte [Configurar nós de transações do Azure Blockchain Service](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Conecte-se usando o conector Blockchain Azure

Baixe ou clone o [Conector Blockchain Do GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Siga a seção quickstart no **readme** para construir o conector a partir do código-fonte.

### <a name="connect-using-an-azure-ad-user-account"></a>Conecte-se usando uma conta de usuário Azure AD

1. Execute o seguinte comando para autenticar usando uma conta de usuário Azure AD. Substitua \<o myAADDirectory\> por um domínio Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD solicita credenciais.
1. Faça login com seu nome de usuário e senha.
1. Após a autenticação bem-sucedida, seu proxy local se conecta ao seu nó blockchain. Agora você pode anexar seu cliente Geth com o ponto final local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Conecte-se usando um ID de aplicativo

Muitos aplicativos autenticam com o Azure AD usando um ID de aplicativo em vez de uma conta de usuário Azure AD.

Para se conectar ao seu nó usando um ID de aplicativo, **substitua o aadauthcode** **por aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | Descrição |
|-----------|-------------|
| inquilino-id | Domínio Azure AD, por exemplo,`yourdomain.onmicrosoft.com`
| cliente-id | ID do cliente do aplicativo registrado no Azure AD
| cliente secreto | Segredo do cliente do aplicativo registrado no Azure AD

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Como: Usar o portal para criar um aplicativo e serviço Azure AD que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Conecte um dispositivo móvel ou navegador de texto

Para um dispositivo móvel ou navegador baseado em texto onde o display pop-up de autenticação Azure AD não é possível, o Azure AD gera uma senha única. Você pode copiar a senha e prosseguir com a autenticação do Azure AD em outro ambiente.

Para gerar a senha, **substitua aadauthcode** **por aaddevice**. Substitua \<o myAADDirectory\> por um domínio Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre segurança de dados no Azure Blockchain Service, consulte [a segurança do Azure Blockchain Service](data-security.md).