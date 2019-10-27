---
title: Gerenciar usuários e funções no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar usuários e funções no aplicativo IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950087"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gerenciar usuários e funções em seu aplicativo IoT Central

Este artigo descreve como, como administrador, você pode adicionar, editar e excluir usuários em seu aplicativo de IoT Central do Azure e também como gerenciar funções em seu aplicativo de IoT Central do Azure.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo.


## <a name="add-users"></a>Adicionar usuários

Cada usuário deve ter uma conta de usuário antes de poder entrar e acessar um aplicativo Azure IoT Central. As MSAs (Contas Microsoft) e contas do Azure AD (Azure Active Directory) têm suporte no Azure IoT Central. Atualmente, os grupos do Azure Active Directory não têm suporte no Azure IoT Central.

Para saber mais, confira a [Ajuda da Conta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e o [Início Rápido: Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um usuário a um aplicativo IoT Central, acesse a página **Usuários** na seção **Administração**.

    ![Lista de usuários](media/howto-administer/image1.png)

1. Na página **Usuários**, escolha **+ Adicionar usuário** para adicionar um usuário.

1. Escolha uma função para o usuário na lista suspensa **Função**. Saiba mais sobre as funções na seção [Gerenciar funções](#manage-roles) deste artigo.

    ![Seleção de função](media/howto-administer/image3.png)

    > [!NOTE]
    >  Para adicionar usuários em massa, insira as IDs de usuário de todos os usuários que você deseja adicionar separados por ponto e vírgula. Escolha uma função na lista suspensa **Função**. Em seguida, selecione **Salvar**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não podem ser alteradas após serem atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e adicione-o novamente com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas para IoT Central aplicativo e não podem ser gerenciadas no portal do Azure.

## <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **Usuários**. Em seguida, selecione **Excluir**.

## <a name="manage-roles"></a>Gerenciar funções

As funções permitem controlar quem na sua organização pode executar várias tarefas na IoT Central. Existem três funções que você pode atribuir aos usuários do seu aplicativo.

### <a name="administrator"></a>Administrador

Os usuários na função **administrador** tem acesso a toda a funcionalidade em um aplicativo.

O usuário que cria um aplicativo é automaticamente designado à função **Administrador**. Sempre deverá haver pelo menos um usuário na função **Administrador**.

### <a name="application-builder"></a>Construtor de Aplicativos

Os usuários na função **Application Builder** podem fazer tudo em um aplicativo, exceto administrar o aplicativo. Os criadores podem criar, editar e excluir modelos de dispositivo e dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. Os desenvolvedores não terão acesso à seção **Administração** do aplicativo.

### <a name="application-operator"></a>Operador de Aplicativos

Os usuários na função **Application Operator** não podem fazer alterações nos modelos de dispositivo e não podem administrar o aplicativo. Os operadores podem adicionar e excluir dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. Os operadores não terão acesso às páginas **Application Builder** e **Administration**.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu sobre como gerenciar usuários e funções em sua IoT Central do Azure, a próxima etapa sugerida é saber mais sobre como [exibir sua fatura](howto-view-bill.md) no Azure IOT central.
