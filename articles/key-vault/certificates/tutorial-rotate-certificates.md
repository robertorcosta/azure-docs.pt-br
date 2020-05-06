---
title: Tutorial – Atualizar a frequência de rotação automática do certificado no Key Vault | Microsoft Docs
description: Tutorial mostrando como atualizar a frequência de rotação automática de um certificado no Azure Key Vault usando o portal do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107553"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Tutorial: Configurar a rotação automática do certificado no Key Vault

O Azure Key Vault permite que você provisione, gerencie e implante certificados digitais com facilidade. Eles podem ser certificados SSL/TLS públicos e privados assinados por uma Autoridade de Certificação ou um certificado autoassinado. O Key Vault também pode solicitar e renovar certificados por meio de parcerias com autoridades de certificação, fornecendo uma solução robusta para o gerenciamento do ciclo de vida do certificado. Neste tutorial, você atualizará os atributos do certificado: período de validade, frequência de rotação automática, AC. Para saber mais sobre o Key Vault, analise a [Visão geral](../general/overview.md).

Este tutorial mostra como:

> [!div class="checklist"]
> * Gerenciar um certificado usando o portal do Azure
> * Adicionar conta do provedor de Autoridade de Certificação
> * Atualizar o período de validade do certificado
> * Atualizar a frequência de rotação automática do certificado
> * Atualizar os atributos do certificado usando o Azure PowerShell


Antes de começar, leia [Conceitos básicos do Key Vault](../general/basic-concepts.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

Crie ou selecione o Key Vault existente para executar operações. [(Etapas para criar um cofre de chaves).](../quick-create-portal.md) No exemplo, o nome do cofre é **Example-Vault**. 

![Saída após a conclusão da criação do Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Criar um certificado no Key Vault

Criar ou importe um certificado no cofre. [(Etapas para criar um certificado no cofre de chaves).](../quick-create-portal.md) Nesse caso, trabalhamos no certificado chamado **ExampleCertificate**.

> [!NOTE]
> No Azure Key Vault, os atributos de ciclo de vida de um certificado podem ser atualizados no momento da criação do certificado e depois dela. 
## <a name="updating-certificates-life-cycle-attributes"></a>Atualizar atributos de ciclo de vida do certificado

Um certificado criado no Key Vault pode ser 
- um certificado autoassinado
- um certificado criado com uma AC (Autoridade de Certificação) parceira do Key Vault
- um certificado com uma Autoridade de Certificação que não é parceira do Key Vault

No momento, as seguintes Autoridades de Certificação são provedores parceiros do Key Vault:
- DigiCert – o Key Vault oferece certificados TLS/SSL OV com a DigiCert.
- GlobalSign – o Key Vault oferece certificados TLS/SSL OV com a GlobalSign.

O Azure Key Vault faz a rotação automática de certificados por meio de parcerias com autoridades de certificação. Por meio dessa parceria estabelecida, o Key Vault solicita e renova certificados automaticamente. Portanto, a **funcionalidade de rotação automática não é aplicável a certificados criados com ACs que não são parceiras do Key Vault.** 

> [!NOTE]
> Um administrador de conta de um provedor de AC cria credenciais a serem usadas pelo Key Vault para criar, renovar e usar certificados TLS/SSL por meio do Key Vault.
![Autoridade de certificação](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Atualizar atributos de ciclo de vida do certificado no momento da criação do certificado

1. Na página de propriedades do Key Vault, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. Na tela **Criar um certificado**, atualize os seguintes valores:
    

    - **Período de Validade**: digite o valor (em meses). A criação de certificados de curta duração é uma prática de segurança recomendada. Por padrão, o valor da validade de um certificado recém-criado é de 12 meses.
    - **Tipo de Ação de Tempo de Vida**: selecione a ação de renovação automática e alerta do certificado. De acordo com a seleção, atualize "tempo de vida do percentual" ou "número de dias antes da expiração". Por padrão, a renovação automática de um certificado é definida em 80% do tempo de vida dele.<br> No menu suspenso, selecione a opção:

    |  Renovar automaticamente em um determinado momento| Enviar um email para todos os contatos em um determinado momento |
    |-----------|------|
    |Selecionar esta opção ATIVARÁ a rotação automática | A seleção dessa opção não realizará a rotação automática, só alertará os contatos|
        


4. Clique em **Criar**.

![Ciclo de vida do certificado](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Atualizar atributos de ciclo de vida do certificado armazenado

1. Selecione o Key Vault.
2. Na página de propriedades do Key Vault, selecione **Certificados**.
3. Selecione o certificado que você deseja atualizar. Nesse caso, trabalharemos no certificado chamado **ExampleCertificate**.
4. Selecione **Política de Emissão** na barra de menus superior.

![Propriedades do certificado](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. Na tela **Política de Emissão**, atualize os seguintes valores:
- **Período de Validade**: atualize o valor (em meses)
- **Tipo de Ação de Tempo de Vida**: selecione a ação de renovação automática e alerta do certificado. De acordo com a seleção, atualize o "tempo de vida do percentual" ou "número de dias antes da expiração". 

![Propriedades do certificado](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Clique em **Save**.

> [!IMPORTANT]
> Alterar o Tipo de Ação de Tempo de Vida de um certificado registrará as modificações para os certificados existentes imediatamente.


### <a name="updating-certificates-attributes-using-powershell"></a>Atualizar os atributos do certificado usando o PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Para modificar a política de renovação de uma lista de certificados, insira File.csv contendo VaultName,CertName <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Saiba mais sobre os parâmetros [aqui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você atualizou o ciclo de vida de um certificado. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

Leia mais sobre [Gerenciar a criação de certificados no Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Examine a [Visão geral do Azure Key Vault](../general/overview.md)