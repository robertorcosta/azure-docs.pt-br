---
title: Como executar o serviço de gerenciamento de certificados OPC Vault com segurança - Azure | Microsoft Docs
description: Descreve como executar o serviço de gerenciamento de certificados OPC Vault com segurança no Azure e revisa outras diretrizes de segurança a considerar.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271701"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Execute o serviço de gerenciamento de certificados OPC Vault com segurança

Este artigo explica como executar o serviço de gerenciamento de certificados OPC Vault com segurança no Azure e revisa outras diretrizes de segurança a serem consideradas.

## <a name="roles"></a>Funções

### <a name="trusted-and-authorized-roles"></a>Funções confiáveis e autorizadas

O microserviço OPC Vault permite que funções distintas acessem várias partes do serviço.

> [!IMPORTANT]
> Durante a implantação, o script só adiciona o usuário que executa o script de implantação como usuário para todas as funções. Para uma implantação de produção, você deve rever essa atribuição de função e reconfigurar adequadamente seguindo as diretrizes abaixo. Essa tarefa requer atribuição manual de funções e serviços no portal Deaplicações Corporativas do Azure Active Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Funções de serviço de gerenciamento de certificados

O microserviço OPC Vault define as seguintes funções:

- **Leitor**: Por padrão, qualquer usuário autenticado no inquilino tem acesso de leitura. 
  - Leia o acesso a aplicativos e solicitações de certificados. Pode listar e consultar para aplicativos e solicitações de certificados. Também informações de descoberta de dispositivos e certificados públicos são acessíveis com acesso à leitura.
- **Escritor**: A função Escritor é atribuída a um usuário para adicionar permissões de gravação para determinadas tarefas. 
  - Ler/escrever acesso a aplicativos e solicitações de certificados. Pode se registrar, atualizar e cancelar aplicativos. Pode criar solicitações de certificados e obter chaves e certificados privados aprovados. Também pode excluir chaves privadas.
- **Approver**: A função Approver é atribuída a um usuário para aprovar ou rejeitar solicitações de certificado. O papel não inclui nenhum outro papel.
  - Além da função Approver para acessar a API de microserviço Do PcO Vault, o usuário também deve ter a permissão de assinatura chave no Azure Key Vault para poder assinar os certificados.
  - A função Escritor e Aprovador deve ser atribuída a diferentes usuários.
  - O principal papel do Aprovador é a aprovação da geração e rejeição dos pedidos de certificado.
- **Administrador**: A função Administrador é atribuída a um usuário para gerenciar os grupos de certificados. O papel não suporta o papel de Aprovador, mas inclui o papel de Escritor.
  - O administrador pode gerenciar os grupos de certificados, alterar a configuração e revogar os certificados de solicitação emitindo uma nova Lista de Revogação de Certificados (CRL).
  - Idealmente, as funções de Escritor, Aprovador e Administrador são atribuídas a diferentes usuários. Para obter segurança adicional, um usuário com a função Aprovador ou Administrador também precisa da permissão de assinatura de chaves no Key Vault, para emitir certificados ou para renovar um certificado CA do Emissor.
  - Além da função de administração de microsserviços, o papel inclui, mas não se limita a:
    - Responsabilidade pela administração da implementação das práticas de segurança da AC.
    - Gestão da geração, revogação e suspensão de certificados. 
    - Gerenciamento do ciclo de vida da chave criptográfica (por exemplo, a renovação das chaves CA do emissor).
    - Instalação, configuração e manutenção dos serviços que operam o CA.
    - Operação diária dos serviços. 
    - CA e backup e recuperação de banco de dados.

### <a name="other-role-assignments"></a>Outras atribuições de função

Considere também as seguintes funções quando estiver executando o serviço:

- Empresário proprietário do contrato de aquisição de certificados com a autoridade de certificação de raiz externa (por exemplo, quando o proprietário compra certificados de uma CA externa ou opera uma CA subordinada a uma CA externa).
- Desenvolvimento e validação da Autoridade certificadora.
- Revisão dos registros de auditoria.
- O pessoal que ajuda a apoiar o CA ou gerenciar as instalações físicas e em nuvem, mas não são diretamente confiáveis para executar operações de CA, estão na função *autorizada.* O conjunto de tarefas que as pessoas na função autorizada podem executar também deve ser documentado.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Revisar associações de funções confiáveis e autorizadas trimestralmente

Revisar a adesão de funções confiáveis e autorizadas pelo menos trimestralmente. Certifique-se de que o conjunto de pessoas (para processos manuais) ou identidades de serviço (para processos automatizados) em cada função seja mantido ao mínimo.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separação de função entre solicitante de certificado e aprovador

O processo de emissão de certificados deve impor a separação de funções entre o solicitante de certificado e as funções de aprovador de certificados (pessoas ou sistemas automatizados). A emissão de certificados deve ser autorizada por uma função de aprovador de certificado que verifica se o solicitante do certificado está autorizado a obter certificados. As pessoas que possuem o cargo de aprovador de certificados devem ser uma pessoa formalmente autorizada.

### <a name="restrict-assignment-of-privileged-roles"></a>Restringir a atribuição de funções privilegiadas

Você deve restringir a atribuição de funções privilegiadas, como autorizar a adesão do grupo administradores e aprovadores, a um conjunto limitado de pessoal autorizado. Qualquer alteração de função privilegiada deve ter o acesso revogado dentro de 24 horas. Finalmente, revise as atribuições de função privilegiadas trimestralmente e remova quaisquer atribuições desnecessárias ou expiradas.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Funções privilegiadas devem usar autenticação de dois fatores

Use autenticação multifatorial (também chamada de autenticação de dois fatores) para logins interativos de Aprovadores e Administradores para o serviço.

## <a name="certificate-service-operation-guidelines"></a>Diretrizes de operação de serviço de certificado

### <a name="operational-contacts"></a>Contatos operacionais

O serviço de certificado deve ter um plano de resposta de segurança atualizado no arquivo, que contém contatos de resposta a incidentes operacionais detalhados.

### <a name="security-updates"></a>Atualizações de segurança

Todos os sistemas devem ser continuamente monitorados e atualizados com as últimas atualizações de segurança.

> [!IMPORTANT]
> O repositório GitHub do serviço OPC Vault é continuamente atualizado com patches de segurança. Monitore essas atualizações e aplique-as ao serviço em intervalos regulares.

### <a name="security-monitoring"></a>Monitoramento de segurança

Assine ou implemente o monitoramento de segurança adequado. Por exemplo, assine uma solução central de monitoramento (como o Azure Security Center ou a solução de monitoramento do Office 365) e configure-a adequadamente para garantir que os eventos de segurança sejam transmitidos para a solução de monitoramento.

> [!IMPORTANT]
> Por padrão, o serviço OPC Vault é implantado com [o Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) como uma solução de monitoramento. Adicionar uma solução de segurança como [o Azure Security Center](https://azure.microsoft.com/services/security-center/) é altamente recomendado.

### <a name="assess-the-security-of-open-source-software-components"></a>Avalie a segurança dos componentes de software de código aberto

Todos os componentes de código aberto usados dentro de um produto ou serviço devem estar livres de vulnerabilidades de segurança moderadas ou maiores.

> [!IMPORTANT]
> Durante as compilações de integração contínua, o repositório GitHub do serviço OPC Vault verifica todos os componentes em busca de vulnerabilidades. Monitore essas atualizações no GitHub e aplique-as ao serviço em intervalos regulares.

### <a name="maintain-an-inventory"></a>Manter um inventário

Mantenha um inventário de ativos para todos os hosts de produção (incluindo máquinas virtuais persistentes), dispositivos, todas as faixas internas de endereços IP, VIPs e nomes de domínio DNS públicos. Sempre que você adicionar ou remover um sistema, endereço IP do dispositivo, VIP ou domínio DNS público, você deve atualizar o inventário dentro de 30 dias.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventário da implantação padrão de produção de microserviços Azure OPC Vault 

No Azure:
- **Plano de Serviço de Aplicativo**: Plano de serviço de aplicativos para hosts de serviço. Padrão S1.
- **Serviço de aplicativo** para microserviço: o host de serviço OPC Vault.
- **Serviço de aplicativo** para aplicação de amostra: O host de aplicativo de amostra do OPC Vault.
- **Key Vault Standard**: Para armazenar segredos e chaves Azure Cosmos DB para os serviços web.
- **Key Vault Premium**: Para hospedar as chaves CA do emissor, para o serviço de assinatura e para a configuração e armazenamento do cofre das chaves privadas do aplicativo.
- **Azure Cosmos DB**: Banco de dados para solicitações de aplicativos e certificados. 
- **Insights do aplicativo**: (opcional) Solução de monitoramento para serviço web e aplicativo.
- **Registro do aplicativo Azure AD**: Um registro para o aplicativo de amostra, o serviço e o módulo de borda.

Para os serviços em nuvem, todos os nomes de host, grupos de recursos, nomes de recursos, IDs de assinatura e IDs de inquilinos usados para implantar o serviço devem ser documentados. 

No Azure IoT Edge ou em um servidor local de IoT Edge:
- **Módulo OPC Vault IoT Edge :** Para suportar uma rede de fábrica OPC UA Global Discovery Server. 

Para os dispositivos IoT Edge, os nomes de host e endereços IP devem ser documentados. 

### <a name="document-the-certification-authorities-cas"></a>Documentar as Autoridades de Certificação (CAs)

A documentação da hierarquia da CA deve conter todos os CAs operados. Isso inclui todos os CAs subordinados relacionados, CAs pai e CAs raiz, mesmo quando eles não são gerenciados pelo serviço. Em vez de documentação formal, você pode fornecer um conjunto exaustivo de todos os certificados ca não expirados.

> [!NOTE]
> O aplicativo de amostra OPC Vault suporta o download de todos os certificados usados e produzidos no serviço para documentação.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentar os certificados emitidos por todas as Autoridades de Certificação (CAs)

Forneça um conjunto exaustivo de todos os certificados emitidos nos últimos 12 meses.

> [!NOTE]
> O aplicativo de amostra OPC Vault suporta o download de todos os certificados usados e produzidos no serviço para documentação.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Documente o procedimento operacional padrão para excluir chaves criptográficas com segurança

Durante a vida de uma CA, a exclusão da chave pode acontecer apenas raramente. É por isso que nenhum usuário tem o direito de exclusão do certificado do cofre de chaves atribuído e por isso não há APIs expostas para excluir um certificado CA do Emissor. O procedimento operacional padrão manual para excluir com segurança as chaves criptográficas da autoridade de certificação só está disponível acessando diretamente o Key Vault no portal Azure. Você também pode excluir o grupo de certificados no Key Vault. Para garantir a exclusão imediata, desative a funcionalidade [de exclusão suave do Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="certificates"></a>Certificados

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Os certificados devem estar em conformidade com o perfil mínimo do certificado

O serviço OPC Vault é uma CA on-line que emite certificados de entidade final aos assinantes. O microserviço OPC Vault segue essas diretrizes na implementação padrão.

- Todos os certificados devem incluir os seguintes campos X.509, conforme especificado abaixo:
  - O conteúdo do campo de versão deve ser v3. 
  - O conteúdo do campo serialNumber deve incluir pelo menos 8 bytes de entropia obtidos de um gerador de números aleatórios aprovado pela FIPS (Federal Information Processing Standards) 140.<br>
    > [!IMPORTANT]
    > O número de série do OPC Vault é por padrão de 20 bytes, e é obtido a partir do gerador de números aleatórios criptográficos do sistema operacional. O gerador de números aleatórios é fips 140 aprovado em dispositivos Windows, mas não no Linux. Considere isso ao escolher uma implantação de serviço que use VMs Linux ou contêineres docker Linux, nos quais a tecnologia subjacente OpenSSL não é aprovada pelo FIPS 140.
  - Os campos ExclusivoID e sujeito-emissorNão devem estar presentes.
  - Os certificados de entidade final devem ser identificados com a extensão de restrições básicas, de acordo com o IETF RFC 5280.
  - O campo pathLenConstraint deve ser definido como 0 para o certificado CA emissor. 
  - A extensão de uso de chave estendida deve estar presente e deve conter o conjunto mínimo de identificadores de objetos de uso de chave estendida (OIDs). O OID anyExtendedKeyUsage (2.5.29.37.0) não deve ser especificado. 
  - A extensão do Ponto de Distribuição CRL (CDP) deve estar presente no certificado CA emissor.<br>
    > [!IMPORTANT]
    > A extensão do CDP está presente nos certificados OPC Vault CA. No entanto, os dispositivos OPC UA usam métodos personalizados para distribuir CRLs.
  - A extensão de Acesso à Informação da Autoridade deve estar presente nos certificados de assinante.<br>
    > [!IMPORTANT]
    > A extensão Authority Information Access está presente nos certificados de assinantes do OPC Vault. No entanto, os dispositivos OPC UA usam métodos personalizados para distribuir informações de CA do emissor.
- Algoritmos assimétricos aprovados, comprimentos de chave, funções de hash e modos de preenchimento devem ser usados.
  - RSA e SHA-2 são os únicos algoritmos suportados.
  - O RSA pode ser usado para criptografia, troca de chaves e assinatura.
  - A criptografia RSA deve usar apenas os modos de preenchimento OAEP, RSA-KEM ou RSA-PSS.
  - São necessários comprimentos de chave maiores ou iguais a 2048 bits.
  - Use a família SHA-2 de algoritmos hash (SHA256, SHA384 e SHA512).
  - As teclas RSA Root CA com uma vida útil típica maior ou igual a 20 anos devem ser de 4096 bits ou mais.
  - As teclas CA do emissor RSA devem ter pelo menos 2048 bits. Se a data de validade do certificado CA for após 2030, a tecla CA deve ser de 4096 bits ou mais.
- Vida útil do certificado
  - Certificados de CA raiz: O prazo máximo de validade do certificado para Os CAs raiz não deve exceder 25 anos.
  - Certificados CA do Emissor Sub CA ou on-line: O prazo máximo de validade do certificado para CAs que estejam on-line e emitam apenas certificados de assinante não deve exceder 6 anos. Para esses CAs, a chave de assinatura privada relacionada não deve ser usada por mais de 3 anos para emitir novos certificados.<br>
    > [!IMPORTANT]
    > O certificado Emissor, como é gerado no microserviço Padrão OPC Vault sem CA raiz externa, é tratado como um Sub CA on-line, com seus respectivos requisitos e vidas. A vida útil padrão é definida para 5 anos, com um comprimento de chave maior ou igual a 2048.
  - Todas as chaves assimétricas devem ter uma vida útil máxima de 5 anos, e uma vida útil recomendada de 1 ano.<br>
    > [!IMPORTANT]
    > Por padrão, as vidas dos certificados de solicitação emitidos com o OPC Vault têm uma vida útil de 2 anos, e devem ser substituídos todos os anos. 
  - Sempre que um certificado é renovado, ele é renovado com uma nova chave.
- Extensões específicas do OPC UA em certificados de instância de aplicação
  - A extensão subjectAltName inclui o aplicativo Uri e nomes de host. Estes também podem incluir endereços FQDN, IPv4 e IPv6.
  - As teclaUsage incluem digitalSignature, nonRepudiation, keyEncipherment e dataEncipherment.
  - O KeyUsage estendido inclui serverAuth e clientAuth.
  - O authorityKeyIdentifier é especificado em certificados assinados.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>As chaves e certificados da CA devem atender aos requisitos mínimos

- **Chaves privadas**: As teclas RSA devem ter pelo menos 2048 bits. Se a data de validade do certificado CA for após 2030, a tecla CA deve ser de 4096 bits ou mais.
- **Vida**útil : O prazo máximo de validade do certificado para CAs que estejam online e emitam apenas certificados de assinante não devem exceder 6 anos. Para esses CAs, a chave de assinatura privada relacionada não deve ser usada por mais de 3 anos para emitir novos certificados.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>As chaves CA são protegidas usando módulos de segurança de hardware

O OpcVault usa o Azure Key Vault Premium, e as chaves são protegidas pelo FIPS 140-2 Level 2 Hardware Security Modules (HSM). 

Os módulos criptográficos que o Key Vault usa, seja HSM ou software, são validados por FIPS. As chaves criadas ou importadas como protegidas por HSM são processadas dentro de um HSM, validadas para fips 140-2 Nível 2. As chaves criadas ou importadas como protegidas por software são processadas dentro de módulos criptográficos validados para fips 140-2 Nível 1.

## <a name="operational-practices"></a>Práticas operacionais

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentar e manter práticas operacionais padrão de PKI para inscrição de certificados

Documente e mantenha procedimentos operacionais padrão (SOPs) para como os CAs emitem certificados, incluindo: 
- Como o assinante é identificado e autenticado. 
- Como a solicitação de certificado é processada e validada (se aplicável, inclua também como as solicitações de renovação e rechavede certificado são processadas). 
- Como os certificados emitidos são distribuídos aos assinantes. 

O SOP do microserviço OPC Vault é descrito na [arquitetura OPC Vault](overview-opc-vault-architecture.md) e [gerencia o serviço de certificado satisfaz o OPC Vault](howto-opc-vault-manage.md). As práticas seguem "OPC Unified Architecture Specification Part 12: Discovery and Global Services".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentar e manter práticas operacionais padrão de PKI para revogação de certificados

O processo de revogação de certificados é descrito na [arquitetura OPC Vault](overview-opc-vault-architecture.md) e [gerenciar o serviço de certificado saqueador OPC](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Cerimônia de geração de chaves do Documento CA 

A geração de chaves do Emissor CA no microserviço OPC Vault é simplificada, devido ao armazenamento seguro no Azure Key Vault. Para obter mais informações, consulte [Gerenciar o serviço de certificado saqueador OPC](howto-opc-vault-manage.md).

No entanto, quando você está usando uma autoridade de certificação Root externa, uma cerimônia de geração de chaves da CA deve seguir os seguintes requisitos.

A cerimônia de geração de chaves da CA deve ser realizada contra um script documentado que inclua pelo menos os seguintes itens: 
- Definição de funções e responsabilidades dos participantes.
- Aprovação para a realização da cerimônia de geração de chaves da CA.
- Hardware criptográfico e materiais de ativação necessários para a cerimônia.
- Preparação de hardware (incluindo atualização de informações de ativos/configuração e aprovação).
- Instalação do sistema operacional.
- Etapas específicas realizadas durante a cerimônia de geração de chaves da CA, tais como: 
  - Instalação e configuração do aplicativo CA.
  - Geração-chave da CA.
  - Backup da chave CA.
  - Assinatura de certificado ca.
  - Importação de chaves assinadas no HSM protegido do serviço.
  - Desligamento do sistema CA.
  - Preparação de materiais para armazenamento.


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar com segurança o OPC Vault, você pode:

> [!div class="nextstepaction"]
> [Dispositivos OPC UA seguros com cofre OPC](howto-opc-vault-secure.md)
