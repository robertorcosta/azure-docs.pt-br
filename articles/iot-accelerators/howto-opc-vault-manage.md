---
title: Como gerenciar o serviço de certificados OPC Vault - Azure | Microsoft Docs
description: Gerencie os certificados de CA raiz do Cofre OPC e as permissões de usuário.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203661"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Gerencie o serviço de certificado saqueador OPC Vault

Este artigo explica as tarefas administrativas para o serviço de gerenciamento de certificados OPC Vault no Azure. Ele inclui informações sobre como renovar certificados CA do Emissor, como renovar a Lista de Revogação de Certificados (CRL) e como conceder e revogar o acesso do usuário.

## <a name="create-or-renew-the-root-ca-certificate"></a>Criar ou renovar o certificado de CA raiz

Depois de implantar o OPC Vault, você deve criar o certificado DE CA raiz. Sem um certificado CA de emissor válido, você não pode assinar ou emitir certificados de solicitação. Consulte [Certificados](howto-opc-vault-secure-ca.md#certificates) para gerenciar seus certificados com vidas razoáveis e seguras. Renove um certificado CA do Emissor após metade de sua vida útil. Ao renovar, considere também que a vida útil configurada de um certificado de solicitação recém-assinado não deve exceder a vida útil do certificado CA do Emissor.
> [!IMPORTANT]
> A função administrador é necessária para criar ou renovar o certificado CA do Emissor.

1. Abra seu serviço `https://myResourceGroup-app.azurewebsites.net`de certificado em , e faça login.
2. Vá para **Grupos de Certificados**.
3. Há um grupo de certificados padrão listado. Selecione **Editar**.
4. Em **Editar detalhes do grupo de certificados,** você pode modificar o nome do assunto e a vida útil de seus certificados de CA e de aplicação. O assunto e as vidas devem ser definidos apenas uma vez antes da emissão do primeiro certificado de CA. Alterações de vida durante as operações podem resultar em vidas inconsistentes de certificados e CRLs emitidos.
5. Digite um assunto válido `CN=My CA Root, O=MyCompany, OU=MyDepartment`(por exemplo, ).<br>
   > [!IMPORTANT]
   > Se você mudar de assunto, você deve renovar o certificado Emissor, ou o serviço não assinará certificados de solicitação. O assunto da configuração é verificado em relação ao objeto do certificado emissor ativo. Se os sujeitos não corresponderem, a assinatura do certificado é recusada.
6. Selecione **Salvar**.
7. Se você encontrar um erro "proibido" neste momento, suas credenciais de usuário não terão a permissão do administrador para modificar ou criar um novo certificado raiz. Por padrão, o usuário que implantou o serviço tem funções de administrador e assinatura com o serviço. Outros usuários precisam ser adicionados às funções Approver, Writer ou Administrator, conforme apropriado no registro do aplicativo Azure Active Directory (Azure AD).
8. Selecione **Detalhes**. Isso deve mostrar as informações atualizadas.
9. Selecione **Renovar certificado CA** para emitir o primeiro certificado CA do Emissor ou para renovar o certificado Emissor. Em seguida, selecione **OK**.
10. Após alguns segundos, você verá **detalhes do certificado**. Para baixar o certificado CA mais recente e o CRL para distribuição aos seus aplicativos OPC UA, selecione **Emissor** ou **Crl**.

Agora, o serviço de gerenciamento de certificados OPC UA está pronto para emitir certificados para aplicações OPC UA.

## <a name="renew-the-crl"></a>Renovar o CRL

A renovação do CRL é uma atualização, que deve ser distribuída aos aplicativos em intervalos regulares. Os dispositivos OPC UA, que suportam a extensão CRL Distribution Point X509, podem atualizar diretamente o CRL do ponto final do microserviço. Outros dispositivos OPC UA podem exigir atualizações manuais ou podem ser atualizados usando extensões push do servidor GDS (*) para atualizar as listas de confiança com os certificados e CRLs.

No fluxo de trabalho a seguir, todas as solicitações de certificado nos estados excluídos são revogadas nos CRLs, que correspondem ao certificado CA emissor para o qual foram emitidos. O número da versão do CRL é incrementado em 1. <br>
> [!NOTE]
> Todos os CRLs emitidos são válidos até o vencimento do certificado CA emissor. Isso porque a especificação OPC UA não requer um modelo de distribuição obrigatória e determinista para crl.

> [!IMPORTANT]
> A função Administrador é necessária para renovar o CRL emissor.

1. Abra seu serviço `https://myResourceGroup.azurewebsites.net`de certificado em , e faça login.
2. Vá para a página **Grupos de Certificados.**
3. Selecione **Detalhes**. Isso deve mostrar o certificado atual e as informações de CRL.
4. Selecione **Atualizar CRL Revocação de CRL** para emitir um CRL atualizado para todos os certificados de emissor ativos no armazenamento do Cofre OPC.
5. Após alguns segundos, você verá **detalhes do certificado**. Para baixar o certificado CA mais recente e o CRL para distribuição aos seus aplicativos OPC UA, selecione **Emissor** ou **Crl**.

## <a name="manage-user-roles"></a>Gerenciar funções de usuário

Você gerencia as funções do usuário para o microserviço OPC Vault no Azure AD Enterprise Application. Para obter uma descrição detalhada das definições de função, consulte [Funções](howto-opc-vault-secure-ca.md#roles).

Por padrão, um usuário autenticado no inquilino pode fazer login no serviço como leitor. Funções privilegiadas mais altas exigem gerenciamento manual no portal Azure ou usando o PowerShell.

### <a name="add-user"></a>Adicionar usuário

1. Abra o portal do Azure.
2. Vá para **aplicativos do Azure Active Directory** > **Enterprise**.
3. Escolha o registro do microserviço OPC Vault `resourceGroupName-service`(por padrão, seu ).
4. Ir para **Usuários e Grupos**.
5. Selecione **Adicionar usuário**.
6. Selecione ou convide o usuário para a atribuição para uma função específica.
7. Selecione a função para os usuários.
8. Selecione **Atribuir**.
9. Para usuários na função Administrador ou Approver, continue a adicionar políticas de acesso ao Azure Key Vault.

### <a name="remove-user"></a>Remover usuário

1. Abra o portal do Azure.
2. Vá para **aplicativos do Azure Active Directory** > **Enterprise**.
3. Escolha o registro do microserviço OPC Vault `resourceGroupName-service`(por padrão, seu ).
4. Ir para **Usuários e Grupos**.
5. Selecione um usuário com uma função para remover e, em seguida, **selecione Remover**.
6. Para usuários removidos na função Administrador ou Approver, remova-os também das políticas do Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Adicionar política de acesso ao usuário ao Azure Key Vault

Políticas adicionais de acesso são necessárias para aprovadores e administradores.

Por padrão, a identidade do serviço tem apenas permissões limitadas para acessar o Key Vault, para evitar que operações elevadas ou alterações ocorram sem a personificação do usuário. As permissões básicas de serviço são Get and List, para segredos e certificados. Para segredos, há apenas uma exceção: o serviço pode excluir uma chave privada da loja secreta depois de ser aceita por um usuário. Todas as outras operações exigem permissões de usuário.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma função Approver, as seguintes permissões devem ser adicionadas ao Key Vault

1. Abra o portal do Azure.
2. Vá para o `resourceGroupName`seu Cofre OPC, usado durante a implantação.
3. Vá para o `resourceGroupName-xxxxx`Key Vault.
4. Vá para **Políticas de Acesso**.
5. Selecione **Adicionar nova**.
6. Pule o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **Selecionar principal**e selecione o usuário a ser adicionado ou convide um novo usuário para o inquilino.
8. Selecione as **seguintes permissões de tecla**: **Obter,** **Listar**e **Assinar**.
9. Selecione as **seguintes permissões secretas**: **Obter,** **Listar,** **Definir**e **Excluir**.
10. Selecione as **seguintes permissões de certificado**: **Obter** e **Listar**.
11. Selecione **OK**e selecione **Salvar**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma função administradora, as seguintes permissões devem ser adicionadas ao Cofre de Chaves

1. Abra o portal do Azure.
2. Vá para o `resourceGroupName`seu Cofre OPC, usado durante a implantação.
3. Vá para o `resourceGroupName-xxxxx`Key Vault.
4. Vá para **Políticas de Acesso**.
5. Selecione **Adicionar nova**.
6. Pule o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **Selecionar principal**e selecione o usuário a ser adicionado ou convide um novo usuário para o inquilino.
8. Selecione as **seguintes permissões de tecla**: **Obter,** **Listar**e **Assinar**.
9. Selecione as **seguintes permissões secretas**: **Obter,** **Listar,** **Definir**e **Excluir**.
10. Selecione as **seguintes permissões de certificado**: **Obter,** **Listar,** **Atualizar,** **Criar**e **Importar**.
11. Selecione **OK**e selecione **Salvar**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Remova a política de acesso ao usuário do Azure Key Vault

1. Abra o portal do Azure.
2. Vá para o `resourceGroupName`seu Cofre OPC, usado durante a implantação.
3. Vá para o `resourceGroupName-xxxxx`Key Vault.
4. Vá para **Políticas de Acesso**.
5. Encontre o usuário para remover e **selecione Excluir**.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar certificados e usuários do Cofre OPC, você pode:

> [!div class="nextstepaction"]
> [Comunicação segura de dispositivos OPC](howto-opc-vault-secure.md)
