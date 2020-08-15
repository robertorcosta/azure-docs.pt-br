---
title: Sobre a renovação de certificado Azure Key Vault
description: Sobre a renovação de certificado Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: bbd65cdfc7c8ebbc08b914158577337cc49ec887
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236056"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Sobre a renovação de certificado Azure Key Vault

O Azure Key Vault permite que você provisione, gerencie e implante facilmente certificados digitais para sua rede e habilite comunicações seguras para aplicativos. Para obter mais informações gerais sobre certificados, consulte [certificados de Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

Ter um certificado de curta duração ou aumentar a frequência da rotação do certificado limita o escopo do adversário quanto a danos.

## <a name="certificate-expiration-notifications"></a>Notificações de expiração do certificado
Primeiro, certifique-se de adicionar o contato de certificado ao seu Key Vault para ser notificado quando os certificados estiverem prestes a expirar (por exemplo, usando o PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) segundo, configure quando você deseja ser notificado sobre a expiração do certificado. Para configurar a ação de tempo de vida, siga [aqui](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)

Há três categorias de criação de certificado no Key Vault. Este guia ajudará você a entender como a renovação de certificados pode ser obtida.
-   Certificados criados com AC integrada (DigiCert ou GlobalSign)
-   Certificados criados com AC não integrada
-   Certificados autoassinados

## <a name="renewal-of-integrated-ca-certificate"></a>Renovação de certificado de autoridade de certificação integrada 
Boas notícias! Os Azure Key Vaults cuidam da manutenção de ponta a ponta de certificados emitidos pela Microsoft Trusted CAs, ou seja, DigiCert e GlobalSign. Saiba como [integrar uma AC confiável com o Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Renovação de certificado de autoridade de certificação não integrada 
O Azure Key Vault fornece aos seus usuários o benefício de importar certificados de qualquer AC para permitir que seus usuários se integrem a vários recursos do Azure e facilitem a implantação. Se você estiver preocupado em perder o controle do seu certificado expirando, ou se pior descobriu que seu certificado já expirou; em seguida, Key Vault pode ajudá-lo a se manter atualizado. Para um certificado de autoridade de certificação não integrada, o Key Vault permite que seu usuário configure notificações de email de próxima expiração. Essas notificações também podem ser definidas para vários usuários.

Agora, para renovar um certificado, é importante entender que um certificado de Azure Key Vault é um objeto com controle de versão. Se a versão atual estiver expirando, você precisará criar uma nova versão. Conceitualmente, cada nova versão seria totalmente um novo certificado composto por chave e um blob que vincula essa chave a uma identidade. Quando você usa uma autoridade de certificação não parceira, o Key Vault gerará um par chave-valor e retornará o CSR.

**Etapas a serem seguidas no portal do Azure:-**
1.  Abra o certificado que você deseja renovar.
2.  Selecione o botão **+ nova versão** na tela certificado.
3.  Selecionar **operação de certificado**
4.  Selecione **baixar CSR**. Isso baixará um arquivo. CSR em sua unidade local.
5.  Traga o CSR para sua escolha da AC para assinar a solicitação
6.  Retorne a solicitação assinada e selecione **mesclar CSR** na mesma tela de operação de certificado.

> [!NOTE]
> É importante mesclar o CSR assinado com a mesma solicitação de CSR que foi criada, caso contrário, a chave não corresponde.

As etapas são semelhantes à criação de um novo certificado e estão documentadas mais detalhadamente [aqui]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renewal-of-self-signed-certificate"></a>Renovação de certificado autoassinado

Boa notícia novamente! Os cofres de chaves do Azure também cuidam da renovação automática de certificados autoassinados para seus usuários. Para saber mais sobre como alterar a política de emissão e atualizar os atributos de ação de tempo de vida do certificado, leia mais [aqui](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

### <a name="troubleshoot"></a>Solucionar problemas
Se o certificado emitido estiver no status ' desabilitado ' na portal do Azure, prossiga para exibir a operação de certificado para exibir a mensagem de erro para esse certificado.

### <a name="frequently-asked-questions"></a>Perguntas frequentes
* Como posso testar o recurso de autorotação do certificado?
  Crie um certificado com validade de 1 mês e, em seguida, defina a ação de tempo de vida para a rotação em 1%. Essa configuração irá girar o certificado em 7,2 horas.
  
* As marcas serão replicadas após a renovação automática do certificado?
  Sim, as marcas seriam replicadas após a renovação automática.

### <a name="see-also"></a>Consulte Também
*   [Como integrar o Key Vault à autoridade de certificação DigiCert](how-to-integrate-certificate-authority.md)
*   [Tutorial: Configurar a rotação automática do certificado no Key Vault](tutorial-rotate-certificates.md)
