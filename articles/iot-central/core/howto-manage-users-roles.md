---
title: Gerencie usuários e funções no aplicativo Azure IoT Central | Microsoft Docs
description: Como administrador, como gerenciar usuários e funções no aplicativo Central de IoT do Azure
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365510"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gerencie usuários e funções em seu aplicativo IoT Central

Este artigo descreve como, como administrador, você pode adicionar, editar e excluir usuários em seu aplicativo Azure IoT Central. O artigo também descreve como gerenciar funções no seu aplicativo Azure IoT Central.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Central De IoT do Azure, você será automaticamente adicionado à função **Administrador** para esse aplicativo.

## <a name="add-users"></a>Adicionar usuários

Cada usuário deve ter uma conta de usuário antes de poder entrar e acessar um aplicativo Azure IoT Central. Contas Microsoft e contas do Azure Active Directory são suportadas no Azure IoT Central. Atualmente, os grupos do Azure Active Directory não têm suporte no Azure IoT Central.

Para saber mais, confira a [Ajuda da Conta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e o [Início Rápido: Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um usuário a um aplicativo IoT Central, acesse a página **Usuários** na seção **Administração**.
    
    > [!div class="mx-imgBorder"]
    >![Gerenciar usuários](media/howto-manage-users-roles/manage-users-pnp.png)

1. Na página **Usuários**, escolha **+ Adicionar usuário** para adicionar um usuário.

1. Escolha uma função para o usuário na lista suspensa **Função**. Saiba mais sobre as funções na seção [Gerenciar funções](#manage-roles) deste artigo.

    > [!div class="mx-imgBorder"]
    >![Adicione o usuário e selecione uma função](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Um usuário que está em uma função personalizada que lhes concede a permissão para adicionar outros usuários, só pode adicionar usuários a uma função com a mesma ou menos permissões do que sua própria função.

Se um ID de usuário central de IoT for excluído do Azure Active Directory e depois for readicionado, o usuário não poderá fazer login no aplicativo IoT Central. Para reativar o acesso, o administrador da Central de IoT deve excluir e ler o usuário no aplicativo.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

Os papéis não podem ser alterados depois de serem designados. Para alterar a função atribuída a um usuário, exclua o usuário e adicione-o novamente com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas para o aplicativo IoT Central e não podem ser gerenciadas a partir do Portal Azure.

## <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **Usuários**. Em seguida, **selecione Excluir**.

## <a name="manage-roles"></a>Gerenciar funções

As funções permitem que você controle quem dentro de sua organização está autorizado a fazer várias tarefas na IoT Central. Existem três funções incorporadas que você pode atribuir aos usuários do seu aplicativo. Você também pode [criar funções personalizadas](#create-a-custom-role) se precisar de um controle mais fino.

> [!div class="mx-imgBorder"]
> ![Gerenciar a seleção de funções](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrador

Os usuários na função **Administrador** podem gerenciar e controlar cada parte do aplicativo, incluindo o faturamento.

O usuário que cria um aplicativo é automaticamente designado à função **Administrador**. Sempre deverá haver pelo menos um usuário na função **Administrador**.

### <a name="builder"></a>Construtor

Os usuários na função **Construtor** podem gerenciar cada parte do aplicativo, mas não podem fazer alterações nas guias Administração ou Exportação contínua de dados.

### <a name="operator"></a>Operador

Os usuários na função **Operador** podem monitorar a saúde e o status do dispositivo. Eles não estão autorizados a fazer alterações nos modelos de dispositivos ou administrar o aplicativo. Os operadores podem adicionar e excluir dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. 

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Se a sua solução exigir controles de acesso com grãos mais finos, você pode criar funções personalizadas com conjuntos personalizados de permissões. Para criar uma função personalizada, navegue até a página **Funções** na seção **Administração** do seu aplicativo. Em seguida, selecione **+ Nova função**e adicione um nome e uma descrição para o seu papel. Selecione as permissões que sua função requer e, em seguida, **selecione Salvar**.

Você pode adicionar usuários à sua função personalizada da mesma forma que você adiciona os usuários a uma função incorporada.

> [!div class="mx-imgBorder"]
> ![Construa uma função personalizada](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opções de função personalizadas

Quando você define uma função personalizada, você escolhe o conjunto de permissões que um usuário recebe se for um membro da função. Algumas permissões dependem de outras. Por exemplo, se você adicionar a permissão **de painel de aplicativos Update** a uma função, a permissão do painel do aplicativo **Exibir** será adicionada automaticamente. As tabelas a seguir resumem as permissões disponíveis e suas dependências, que você pode usar ao criar funções personalizadas.

#### <a name="managing-devices"></a>Gerenciando dispositivos

**Permissões de modelo de dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Gerenciar | Visualizar <br/> Outras dependências: Exibir instâncias do dispositivo  |
| Controle Total | Exibir, gerenciar <br/> Outras dependências: Exibir instâncias do dispositivo |

**Permissões de instância de dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: Exibir modelos de dispositivos e grupos de dispositivos |
| Atualizar | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos e grupos de dispositivos  |
| Criar | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos e grupos de dispositivos  |
| Excluir | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos e grupos de dispositivos  |
| Executar comandos | Atualizar, exibir <br/> Outras dependências: Exibir modelos de dispositivos e grupos de dispositivos  |
| Controle Total | Exibir, atualizar, criar, excluir, executar comandos <br/> Outras dependências: Exibir modelos de dispositivos e grupos de dispositivos  |

**Permissões de grupos de dispositivos**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: Exibir modelos de dispositivos e instâncias de dispositivos |
| Atualizar | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos e instâncias de dispositivos   |
| Criar | Exibir, Atualizar <br/> Outras dependências: Exibir modelos de dispositivos e instâncias de dispositivos   |
| Excluir | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos e instâncias de dispositivos   |
| Controle Total | Exibir, atualizar, criar, excluir <br/> Outras dependências: Exibir modelos de dispositivos e instâncias de dispositivos |

**Permissões de gerenciamento de conectividade de dispositivos**

| Nome | Dependências |
| ---- | -------- |
| Exemplo de leitura | Nenhum <br/> Outras dependências: Exibir modelos de dispositivos, grupos de dispositivos, instâncias de dispositivos |
| Gerenciar instância | Nenhum |
| Leia global | Nenhum   |
| Gerencie globalmente | Ler Global |
| Controle Total | Leia exemplo, Gerenciar instância, Leia global, Gerencie globalmente. <br/> Outras dependências: Exibir modelos de dispositivos, grupos de dispositivos, instâncias de dispositivos |

**Permissões de emprego**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Atualizar | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Criar | Exibir, Atualizar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Excluir | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Execute (executar) | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos; Atualizar instâncias do dispositivo; Executar comandos em instâncias de dispositivo |
| Controle Total | Exibir, atualizar, criar, excluir, executar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos; Atualizar instâncias do dispositivo; Executar comandos em instâncias de dispositivo |

**Permissões de regras**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: Exibir modelos de dispositivos |
| Atualizar | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos |
| Criar | Exibir, Atualizar <br/> Outras dependências: Exibir modelos de dispositivos |
| Excluir | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos |
| Controle Total | Exibir, atualizar, criar, excluir <br/> Outras dependências: Exibir modelos de dispositivos |

#### <a name="managing-the-app"></a>Gerenciando o aplicativo

**Permissões de configurações do aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Cópia | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos, grupos de dispositivos, dashboards, exportação de dados, branding, links de ajuda, funções personalizadas, regras |
| Excluir | Visualizar   |
| Controle Total | Exibir, atualizar, copiar, excluir <br/> Outras dependências: Exibir modelos de dispositivos, grupos de dispositivos, painéis de aplicativos, exportação de dados, branding, links de ajuda, funções personalizadas, regras |

**Permissões de exportação do modelo de aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Exportação | Visualizar <br/> Outras dependências: Exibir modelos de dispositivos, instâncias de dispositivos, grupos de dispositivos, dashboards, exportação de dados, branding, links de ajuda, funções personalizadas, regras |
| Controle Total | Ver, Exportar <br/> Outras dependências: Exibir modelos de dispositivos, grupos de dispositivos, painéis de aplicativos, exportação de dados, branding, links de ajuda, funções personalizadas, regras |

**Permissões de faturamento**

| Nome | Dependências |
| ---- | -------- |
| Gerenciar | Nenhum     |
| Controle Total | Gerenciar |

#### <a name="managing-users-and-roles"></a>Gerenciamento de usuários e funções

**Permissões de funções personalizadas**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum |
| Atualizar | Visualizar |
| Criar | Exibir, Atualizar |
| Excluir | Visualizar |
| Controle Total | Exibir, atualizar, criar, excluir |

**Permissões de gerenciamento de usuários**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum <br/> Outras dependências: Exibir funções personalizadas |
| Adicionar | Visualizar <br/> Outras dependências: Exibir funções personalizadas |
| Excluir | Visualizar <br/> Outras dependências: Exibir funções personalizadas |
| Controle Total | Exibir, Adicionar, Excluir <br/> Outras dependências: Exibir funções personalizadas |

> [!NOTE]
> Um usuário que está em uma função personalizada que lhes concede a permissão para adicionar outros usuários, só pode adicionar usuários a uma função com a mesma ou menos permissões do que sua própria função.

#### <a name="customizing-the-app"></a>Personalizando o aplicativo

**Permissões de painel de aplicativos**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Criar | Exibir, Atualizar |
| Excluir | Visualizar   |
| Controle Total | Exibir, atualizar, criar, excluir |

**Permissões de painéis pessoais**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Criar | Exibir, Atualizar   |
| Excluir | Visualizar   |
| Controle Total | Exibir, atualizar, criar, excluir |

**Permissões de marca, favicon e cores**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Controle Total | Exibir, Atualizar |

**Ajuda links permissões**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Controle Total | Exibir, Atualizar |

#### <a name="extending-the-app"></a>Estendendo o aplicativo

**Permissões de exportação de dados**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Atualizar | Visualizar   |
| Criar | Exibir, Atualizar  |
| Excluir | Visualizar   |
| Controle Total | Exibir, atualizar, criar, excluir |

**Permissões de token de API**

| Nome | Dependências |
| ---- | -------- |
| Visualizar | Nenhum     |
| Criar | Visualizar   |
| Excluir | Visualizar   |
| Controle Total | Exibir, criar, excluir |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como gerenciar usuários e funções em seu aplicativo Azure IoT Central, o próximo passo sugerido é aprender como [gerenciar sua conta](howto-view-bill.md).
