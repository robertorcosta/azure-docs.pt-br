---
title: Novidades do Azure Key Vault | Microsoft Docs
description: Atualizações recentes do Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 07/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b469ea0d0a91cc4ca7f0602dffbcc63b31c60855
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588357"
---
# <a name="whats-new-for-azure-key-vault"></a>Novidades do Azure Key Vault

> [!WARNING]
> **Julho de 2020**: há duas atualizações no cofre de chaves que têm o potencial de afetar as implementações do serviço: [exclusão reversível do cofre de chaves ativada por padrão](#soft-delete-on-by-default) e [alterações do certificado TLS do Azure](#azure-tls-certificate-changes). Confira os detalhes abaixo.

Veja a seguir as novidades do Azure Key Vault. Os novos recursos e os aprimoramentos também foram anunciados no [canal do Key Vault de atualizações do Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="soft-delete-on-by-default"></a>Exclusão reversível ativada por padrão

Até o final de 2020, a **exclusão reversível estará ativada por padrão em todos os cofres de chaves**, novos e pré-existentes. Para obter todos os detalhes sobre essa possível alteração da falha, bem como as etapas necessárias para encontrar os cofres de chaves afetados e atualizá-los com antecedência, confira o artigo [A exclusão reversível será habilitada em todos os cofres de chaves](soft-delete-change.md). 

## <a name="azure-tls-certificate-changes"></a>Alterações do certificado TLS do Azure  

A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Essa alteração está sendo feita porque os Certificados de Autoridade de Certificação atuais não estão em conformidade com um dos requisitos de linha de base do fórum do navegador/da AC.

### <a name="when-will-this-change-happen"></a>Quando ocorrerá essa alteração?

- Os serviços do Azure AD (Azure Active Directory) iniciaram essa transição em 7 de julho de 2020.
- Todos os pontos de extremidade do TLS/SSL do Azure recém-criados contêm o encadeamento de certificados atualizados até as novas ACs raiz. 
- Os pontos de extremidade existentes do Azure farão a transição em fases a partir de 13 de agosto de 2020 e a concluirão até 26 de outubro de 2020.

> [!IMPORTANT]
> É possível que os clientes precisem atualizar os respectivos aplicativos após essa alteração para evitar falhas de conectividade durante a tentativa de se conectarem aos serviços do Azure. 

### <a name="what-is-changing"></a>O que está mudando?

Hoje, a maioria dos certificados TLS usados pelos serviços do Azure são encadeados à seguinte AC raiz:

| Nome comum da AC | Impressão digital (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Os certificados TLS usados pelos serviços do Azure serão encadeados a uma das seguintes ACs raiz:

| Nome comum da AC | Impressão digital (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Quando posso desativar a impressão digital intermediária antiga?

Os Certificados de Autoridade de Certificação atuais *não* serão revogados até 15 de fevereiro de 2021. Após essa data, você poderá remover as impressões digitais antigas do código.

Se essa data for alterada, você será notificado sobre a nova data de revogação.

### <a name="will-this-affect-me"></a>Isso me afetará?

Esperamos que **a maioria dos clientes do Azure não** seja afetada.  No entanto, seu aplicativo poderá ser afetado se ele especificar explicitamente uma lista de ACs aceitáveis. Essa prática é conhecida como anexação de certificado.

Estas são algumas maneiras de detectar se o seu aplicativo foi afetado:

- Pesquise o código-fonte para obter a impressão digital, o nome comum e outras propriedades de certificado de uma das ACs do TLS de TI da Microsoft encontradas [aqui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Se houver uma correspondência, seu aplicativo será afetado. Para resolver esse problema, atualize o código-fonte, incluindo as novas ACs. Como melhor prática, verifique se as ACs podem ser adicionadas ou editadas em curto prazo. Os regulamentos do setor exigem que os Certificados de Autoridade de Certificação sejam substituídos no prazo de até 7 dias e, portanto, os clientes que dependem da anexação precisam tomar uma medida rapidamente.

- Se você tiver um aplicativo que se integra às APIs do Azure ou a outros serviços do Azure e não tiver certeza se ele usa a anexação de certificado, verifique com o fornecedor do aplicativo.

- Diferentes sistemas operacionais e runtimes de linguagem que se comunicam com os serviços do Azure podem exigir etapas adicionais para a criação correta da cadeia de certificados com estas novas raízes: 
    - **Linux**: muitas distribuições exigem a adição das ACs listadas acima a /etc/ssl/certs. Para obter instruções específicas, veja a documentação da distribuição.
    - **Java**: verifique se o repositório de chaves Java contém as ACs listadas acima.
    - **Windows em execução em ambientes desconectados**: os sistemas em execução em ambientes desconectados precisarão ter as raízes listadas acima adicionadas ao repositório de Autoridades de Certificação Raiz Confiáveis e os intermediários adicionados ao repositório de Autoridades de Certificação Intermediárias.
    - **Android**: verifique a documentação do dispositivo e a versão do Android.
    - **Outros dispositivos de hardware, especialmente IoT**: entre em contato com o fabricante do dispositivo. 

- Caso você tenha um ambiente em que as regras de firewall estejam definidas para permitir chamadas de saída somente a localizações específicas de download da CRL (lista de certificados revogados) e/ou de verificação do protocolo OCSP. Você precisará permitir as seguintes URLs da CRL e do OCSP:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>Junho de 2020

O Azure Monitor para Key Vault já está em versão prévia.  O Azure Monitor fornece monitoramento abrangente de cofres de chaves, fornecendo uma visão unificada das solicitações, do desempenho, das falhas e da latência do Key Vault. Para obter mais informações, confira [Azure Monitor para Key Vault (versão prévia)](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maio de 2020

O BYOK "bring your own key" do Key Vault já está em disponibilidade geral. Confira a [especificação do BYOK do Azure Key Vault](../keys/byok-specification.md) e saiba como [Importar chaves protegidas por HSM para o Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Março de 2020

Os pontos de extremidade privados já estão disponíveis em versão prévia. O Serviço de Link Privado do Azure permite que você acesse o Azure Key Vault e os serviços de parceiros/clientes hospedados no Azure em um ponto de extremidade privado na sua rede virtual.  Saiba como [Integrar o Key Vault ao Link Privado do Azure](private-link-service.md).

## <a name="2019"></a>2019

- Versão dos SDKs do Azure Key Vault de próxima geração. Para obter exemplos de uso, confira os guias de início rápido de segredos do Azure Key Vault para [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) e [Node.js](../secrets/quick-create-node.md)
- Novas políticas do Azure para gerenciar certificados do cofre de chaves. Confira as [Definições internas do Azure Policy para o Key Vault](../policy-samples.md).
- A extensão de Máquina Virtual do Azure Key Vault já está em disponibilidade geral.  Confira [Extensão de máquina virtual do Key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md) e [Extensão de máquina virtual do Key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md).
- O gerenciamento de segredos controlado por evento para o Azure Key Vault já está disponível na Grade de Eventos do Azure. Para obter mais informações, confira [o esquema da Grade de Eventos para os eventos no Azure Key Vault](../../event-grid/event-schema-key-vault.md] e saiba como [Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure](event-grid-tutorial.md).

## <a name="2018"></a>2018

Novas integrações e novos recursos lançados neste ano:

- Integração ao Azure Functions. Para obter um exemplo de cenário que utiliza o [Azure Functions](../../azure-functions/index.yml) para operações do cofre de chaves, confira [Automatizar a rotação de um segredo](../secrets/tutorial-rotation.md). 
- [Integração ao Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Com isso, o Azure Databricks agora dá suporte a dois tipos de escopos de segredos: Com backup do Azure Key Vault e com backup do Databricks. Para obter mais informações, confira [Criar um escopo de segredo com backup do Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Novos recursos lançados neste ano:

- Chaves de conta de armazenamento gerenciadas. O recurso Chaves de Conta de Armazenamento facilitou a integração ao Armazenamento do Azure. Consulte o tópico de visão geral para saber mais, [Visão geral das Chaves de Conta de Armazenamento Gerenciado](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Exclusão reversível. O recurso de exclusão reversível aprimora a proteção de dados de cofres de chave e objetos do cofre de chaves. Para saber mais, confira o tópico de visão geral, [Visão geral da exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Novos recursos lançados neste ano:
- Gerenciamento de certificados. Adicionado como um recurso à versão GA 2015-06-01 em 26 de setembro de 2016.

A disponibilidade geral (versão 2015-06-01) foi anunciada em 24 de junho de 2015. As seguintes alterações foram feitas nessa versão: 
- Exclusão de uma chave – remoção do campo "usar".
- Obtenção de informações sobre uma chave – remoção do campo "usar".
- Importação de uma chave para um cofre – remoção do campo "usar".
- Restauração de uma chave – remoção do campo "usar".     
- Alteração de "RSA_OAEP" para "RSA-OAEP" em Algoritmos RSA. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).    
 
A segunda versão prévia (versão 2015-02-01-preview) foi anunciada em 20 de abril de 2015. Para saber mais, consulte a postagem no blog [Atualização da API REST](https://docs.microsoft.com/archive/blogs/kv/rest-api-update). As seguintes tarefas foram atualizadas:
 
- Listar as chaves em um cofre - adição de suporte a paginação para a operação.
- Listar as versões de uma chave - adição de operação para listar as versões de uma chave.  
- Listar os segredos em um cofre - adição de suporte a paginação.
- Listar as versões de um segredo - adição de operação para listar as versões de um segredo.  
- Todas as operações - adição de carimbos de data/hora de criação/atualização dos atributos.  
- Criar um segredo - adição de Content-Type aos segredos.
- Criar uma chave - adição de marcas como informações opcionais.
- Criar um segredo - adição de marcas como informações opcionais.
- Atualizar uma chave - adição de marcas como informações opcionais.
- Atualizar um segredo - adição de marcas como informações opcionais.
- Alteração do tamanho máximo para segredos de 10 K para 25 K Bytes. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
A primeira versão prévia (versão 2014-12-08-preview) foi lançada em 8 de janeiro de 2015.  

## <a name="next-steps"></a>Próximas etapas

Caso tenha outras dúvidas, entre em contato conosco por meio do [suporte](https://azure.microsoft.com/support/options/).  
