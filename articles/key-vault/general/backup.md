---
title: Backup do Azure Key Vault | Microsoft Docs
description: Use este documento para ajudar a fazer backup de um segredo, uma chave ou um certificado armazenado no Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156266"
---
# <a name="azure-key-vault-backup"></a>Backup do Azure Key Vault

Este documento mostrará como executar um backup dos segredos, das chaves e dos certificados individuais armazenados no cofre de chaves. Esse backup destina-se a fornecer uma cópia offline de todos os segredos no caso improvável de você perder o acesso ao cofre de chaves.

## <a name="overview"></a>Visão geral

O Key Vault fornece automaticamente vários recursos para manter a disponibilidade e evitar a perda de dados. Esse backup deverá ser tentado apenas se houver uma justificativa comercial crítica para manter um backup dos seus segredos. O backup de segredos no cofre de chaves pode apresentar desafios operacionais adicionais, como a manutenção de vários conjuntos de logs, permissões e backups quando os segredos expiram ou giram.

O Key Vault mantém a disponibilidade em cenários de desastre e fará automaticamente o failover das solicitações para uma região emparelhada sem exigir nenhuma intervenção por parte do usuário. Para obter mais informações, confira o link a seguir. [Recuperação de desastre do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

O Key Vault protege contra exclusão acidental e mal-intencionada de seus segredos por meio de proteção contra limpeza e exclusão reversível. Se você quiser proteção contra exclusão acidental ou mal-intencionada de seus segredos, configure a exclusão reversível e limpe os recursos de proteção no cofre de chaves. Para obter mais informações, confira o documento a seguir. [Recuperação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Limitações

No momento, o Azure Key Vault não dá suporte a um modde fazer backup de um cofre de chaves inteiro em uma operação. Qualquer tentativa de usar os comandos listados neste documento para executar um backup automatizado de um cofre de chaves não terá suporte da Microsoft nem da equipe do Azure Key Vault.

A tentativa de usar os comandos mostrados no documento abaixo para criar a automação personalizada pode resultar em erros.

* O backup de segredos com várias versões pode causar erros de tempo limite.
* O backup cria um instantâneo pontual. Os segredos podem ser renovados durante um backup, causando uma incompatibilidade de chaves de criptografia.
* Exceder os limites de serviço do cofre de chaves para solicitações por segundo fará com que o cofre de chaves seja limitado e causará falha no backup.

## <a name="design-considerations"></a>Considerações sobre o design

Quando você faz backup de um objeto armazenado no Key Vault (segredo, chave ou certificado), a operação de backup baixa o objeto como um blob criptografado. Este blob não pode ser descriptografado fora do Azure. Para obter dados utilizáveis desse blob, você deve restaurar o blob em um cofre de chaves na mesma Assinatura do Azure e Geografia do Azure.
[Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Pré-requisitos

* Nível de colaborador ou permissões mais altas em uma assinatura do Azure
* Um cofre de chave primária que contém segredos dos quais você deseja fazer backup
* Um cofre de chaves secundário em que os segredos serão restaurados.

## <a name="back-up-and-restore-with-azure-portal"></a>Fazer backup e restaurar com o portal do Azure

### <a name="back-up"></a>Fazer backup

1. Navegue até o Portal do Azure.
2. Selecione seu cofre de chaves.
3. Navegue até o objeto (segredo, chave ou certificado) do qual você deseja fazer backup.

    ![Imagem](../media/backup-1.png)

4. Selecione o objeto.
5. Selecione "Baixar Backup"

    ![Imagem](../media/backup-2.png)
    
6. Clique no botão "Baixar".

    ![Imagem](../media/backup-3.png)
    
7. Armazene o blob criptografado em uma localização segura.

### <a name="restore"></a>Restaurar

1. Navegue até o Portal do Azure.
2. Selecione seu cofre de chaves.
3. Navegue até o tipo de objeto (segredo, chave ou certificado) que você deseja restaurar.
4. Selecione "Restaurar Backup"

    ![Imagem](../media/backup-4.png)
    
5. Navegue até a localização em que você armazenou o blob criptografado.
6. Selecione "Ok".

## <a name="back-up-and-restore-with-the-azure-cli"></a>Fazer backup e restaurar com a CLI do Azure

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Próximas etapas

Ative o registro em log e o monitoramento para seu Azure Key Vault. [Logs do Cofre da Chave do Azure](https://docs.microsoft.com/azure/key-vault/general/logging)
