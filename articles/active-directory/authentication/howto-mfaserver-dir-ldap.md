---
title: Autenticação LDAP e Servidor Azure MFA - Diretório Ativo do Azure
description: Implantação da Autenticação LDAP e Servidor de Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7a5400fce2fce1b75cbd579830f929fcc0d0dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454458"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação LDAP e Servidor de Autenticação Multifator do Azure

Por padrão, o Servidor de Autenticação Multifator do Azure é configurado para importar ou sincronizar usuários do Active Directory. No entanto, ele pode ser configurado para associar a diferentes diretórios LDAP, como um diretório ADAM ou controlador de domínio específico do Active Directory. Quando conectado a um diretório via LDAP, o Servidor de Autenticação Multifator do Azure pode atuar como um proxy LDAP e realizar autenticações. Ele também permite o uso da associação LDAP como um destino RADIUS para pré-autenticação dos usuários com a Autenticação do IIS ou autenticação principal no portal de usuário do Azure MFA.

Para usar a Autenticação Multifator do Azure como um proxy LDAP, insira o Servidor de Autenticação Multifator do Azure entre o cliente LDAP (por exemplo, aplicativo, dispositivo VPN) e o servidor do diretório LDAP. O Servidor de Autenticação Multifator do Azure deve ser configurado para se comunicar com servidores do cliente e com o diretório LDAP. Nessa configuração, o Servidor de Autenticação Multifator do Azure aceita solicitações LDAP dos aplicativos e servidores clientes e os encaminha para o servidor de diretório LDAP de destino para validar as credenciais principais. Se o diretório LDAP validar as credenciais primárias, a Autenticação Multifator do Azure executará a segunda verificação de identidade e enviará uma resposta de volta ao cliente LDAP. Toda a autenticação só será bem-sucedida se a autenticação do servidor LDAP e a verificação de segunda etapa forem bem-sucedidas.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o MFA Server para novas implantações. Novos clientes que gostariam de exigir autenticação multifatorial de seus usuários devem usar a Autenticação Multifatorial baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes de 1º de julho poderão baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="configure-ldap-authentication"></a>Configurar a autenticação LDAP

Para configurar a autenticação LDAP, instale o Servidor de Autenticação Multifator do Azure em um servidor Windows. Use este procedimento:

### <a name="add-an-ldap-client"></a>Adicionar um cliente LDAP

1. No Servidor de Autenticação Multifator do Azure, selecione o ícone Autenticação LDAP no menu à esquerda.
2. Verifique a **caixa de seleção Ativar autenticação LDAP.**

   ![Autenticação LDAP no servidor MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Na guia Clientes, altere a porta TCP e a porta SLS (TLS) se o serviço LDAP de autenticação multifatorial do Azure se vincular a portas não padronizadas para ouvir solicitações LDAP.
4. Se você planeja usar o LDAPS do cliente para o Servidor de Autenticação Multifatorial Do Azure, um certificado TLS/SSL deve ser instalado no mesmo servidor do MFA Server. Clique **em Procurar** ao lado da caixa de certificado SSL (TLS) e selecione um certificado para usar para a conexão segura.
5. Clique em **Adicionar**.
6. Na caixa de diálogo Adicionar Cliente LDAP, digite o endereço IP do dispositivo, do servidor ou do aplicativo que fará a autenticação para o Servidor e um nome de Aplicativo (opcional). O nome do aplicativo aparece nos relatórios da Autenticação Multifator do Azure e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
7. Marque a caixa correspondente a **Exigir correspondência de usuário de Autenticação Multifator do Microsoft Azure** se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à verificação em duas etapas. Se um número significativo de usuários ainda não tiver sido importado para o Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada. Consulte o arquivo de ajuda Servidor de MFA para obter informações adicionais sobre esse recurso.

Repita estas etapas para adicionar outros clientes LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurar a conexão do diretório LDAP

Quando a Autenticação Multifator do Azure é configurado para receber autenticações LDAP, ele deve fazer o proxy dessas autenticações para o diretório LDAP. Portanto, o guia Destino exibe apenas uma única opção esmaecida para usar um destino LDAP.

> [!NOTE]
> A integração de diretórios não é garantida para trabalhar com diretórios que não os Serviços de Domínio de Diretório Ativo.

1. Para configurar a conexão de diretório LDAP, clique no ícone **Integração do diretório.**
2. Na guia Configurações, selecione o botão de rádio **de configuração LDAP específico.**
3. Selecione **Editar...**
4. Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao diretório LDAP. As descrições dos campos estão incluídas no arquivo de ajuda do Servidor de Autenticação Multifator do Azure.

    ![Configuração LDAP de integração do diretório](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Teste a conexão LDAP clicando no botão **Teste.**
6. Se o teste de conexão LDAP foi bem sucedido, clique no botão **OK.**
7. Clique na guia **Filtros.** O Servidor está pré-configurado para carregar contêineres, grupos de segurança e usuários do Active Directory. Se estiver se associando a um diretório LDAP diferente, provavelmente será necessário editar os filtros exibidos. Clique no link **Ajuda** para obter mais informações sobre filtros.
8. Clique na guia **Atributos.** O servidor está pré-configurado para mapear atributos do Active Directory.
9. Se você estiver se associando a um diretório LDAP diferente ou para alterar os mapeamentos de atributos pré-configurados, clique no botão **Editar…**
10. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para seu diretório. Nomes de atributos podem ser digitados ou selecionados clicando no **...** ao lado de cada campo. Clique no link **Ajuda** para obter mais informações sobre atributos.
11. Clique no botão **OK**.
12. Clique no ícone **Configurações** da empresa e selecione a guia **Resolução do nome de usuário.**
13. Se estiver se conectando ao Active Directory de um servidor ingressado em domínio, deixe o botão de opção **Usar os identificadores de segurança do Windows (SIDs) para nomes de usuário correspondentes** selecionado. Caso contrário, selecione o **atributo identificador exclusivo Use LDAP para combinar** o botão de rádio nomes de usuário.

Quando o botão de opção **Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário** selecionado, o Servidor de Autenticação Multifator do Azure tenta resolver cada nome de usuário para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP é realizada nos atributos Username definidos na guia Integração de > Atributos do Diretório. Quando um usuário autentica, o nome de usuário é resolvido para o identificador exclusivo no diretório LDAP. O identificador exclusivo é usado para combinar ao usuário no arquivo de dados de Autenticação Multifator do Azure. Isso permite comparações que não diferenciam maiúsculas de minúsculas, bem como formatos de nome de usuário curtos e longos.

Depois de concluir essas etapas, o Servidor de MFA começará a escutar nas portas configuradas solicitações de acesso LDAP dos clientes configurados e atuará como proxy para essas solicitações ao diretório LDAP para autenticação.

## <a name="configure-ldap-client"></a>Configurar cliente LDAP

Para configurar o cliente LDAP, use as diretrizes:

* Configure seu dispositivo, servidor ou aplicativo para autenticar via LDAP para o Servidor de Autenticação Multifator do Azure como se fosse seu diretório LDAP. Use as mesmas configurações que normalmente usa para se conectar diretamente ao diretório LDAP, exceto o nome do servidor ou endereço IP que será o Servidor de Autenticação Multifator do Azure.
* Configure o tempo limite LDAP para 30 a 60 segundos de modo que haja tempo para validar as credenciais do usuário com o diretório LDAP, executar a verificação de segunda etapa, receber a resposta e responder à solicitação de acesso LDAP.
* Se usar o LDAPS, o aparelho ou servidor que faz as consultas LDAP deve confiar no certificado TLS/SSL instalado no Servidor de Autenticação Multifatorial Do Azure.
