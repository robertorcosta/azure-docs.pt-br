---
title: Início Rápido do Azure – Definir e recuperar um segredo do Key Vault usando o portal do Azure | Microsoft Docs
description: Início Rápido que mostra como definir e recuperar um segredo do Azure Key Vault usando o portal do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 33842707f04e55b311aeeabe2f1bbc83204ec0c9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "83652287"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Início Rápido: definir e recuperar um segredo do Azure Key Vault usando o portal do Azure

O Azure Key Vault é um serviço de nuvem que funciona como um repositório seguro de segredos. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste início rápido, você cria um cofre de chaves e o utiliza para armazenar um segredo. Para saber mais sobre o Key Vault, analise a [Visão geral](../general/overview.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. No menu do portal do Azure ou na página **Início**, selecione **Criar um recurso**.
2. Digite **Key Vault** na caixa Pesquisar.
3. Na lista de resultados, escolha **Key Vault**.
4. Na seção Key Vault, escolha **Criar**.
5. A seção **Criar cofre de chaves** fornece as seguintes informações:
    - **Name**: um nome exclusivo é necessário. Para este início rápido, usamos **Contoso-vault2**. 
    - **Assinatura**: Escolha uma assinatura.
    - Em **Grupo de Recursos**, escolha **Criar novo** e digite um nome para o grupo de recursos.
    - No menu suspenso **Local**, escolha um local.
    - Deixe as outras opções em seus padrões.
6. Depois de fornecer as informações acima, selecione **Criar**.

Anote as duas propriedades listadas abaixo:

* **Nome do cofre**: no exemplo, é **Contoso-Vault2**. Você usará esse nome nas outras etapas.
* **URI do cofre**: no exemplo, é https://contoso-vault2.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre.

![Saída após a conclusão da criação do Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Nesse caso, adicionamos uma senha que pode ser usada por um aplicativo. A senha é chamada **ExamplePassword**, e armazenamos o valor **hVFkk965BuUv** nela.

1. Na página de propriedades do Key Vault, selecione **Segredos**.
2. Clique em **Gerar/Importar**.
3. Na tela **Criar um segredo**, escolha os seguintes valores:
    - **Opções de upload**: Manual.
    - **Name**: ExamplePassword.
    - **Valor**: hVFkk965BuUv
    - Deixe os outros valores com seus padrões. Clique em **Criar**.

Quando receber a mensagem de que o segredo foi criado com êxito, clique nele na lista. 

## <a name="retrieve-a-secret-from-key-vault"></a>Recuperar um segredo do Key Vault

Se você clicar na versão atual, poderá ver o valor especificado na etapa anterior.

![Propriedades do segredo](../media/quick-create-portal/current-version-hidden.png)

Ao clicar no botão "Mostrar o valor de segredo" no painel direito, será possível ver o valor oculto. 

![O valor do segredo apareceu](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)
