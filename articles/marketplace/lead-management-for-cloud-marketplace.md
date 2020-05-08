---
title: Gerenciamento de leads para Marketplace comercial | Azure Marketplace e AppSource
description: Uma visão geral dos vários tópicos relacionados a publicação de ofertas e artefatos técnicos para o Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: c41831f528ae425b35a04503180a956f67762b70
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789837"
---
# <a name="lead-management-for-commercial-marketplace"></a>Gerenciamento de leads para o Marketplace comercial

Os clientes são a essência de qualquer bom negócio. Na transformação das aquisições de produtos atuais, os comerciantes precisam se concentrar na conexão com os clientes diretamente e na criação de uma relação. Por isso, gerar leads de alta qualidade é uma ferramenta essencial no ciclo de vendas. Depois de listar sua oferta no [Partner Center](https://partner.microsoft.com/), há ferramentas habilitadas para que você receba programaticamente as informações de contato do cliente imediatamente depois que um cliente expressa o interesse ou implanta seu produto no Marketplace. 

## <a name="what-are-leads-in-the-marketplace"></a>Quais são os clientes potenciais no marketplace?

São clientes potenciais de clientes que estão interessados ou implantando seus produtos do Marketplace. Se seu produto estiver listado no Azure Marketplace ou AppSource, você poderá receber clientes potenciais de clientes depois que ele for configurado corretamente de seu CRM para sua listagem (s) no Partner Center. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Como conectar seu sistema CRM ao Partner Center

Para começar a obter clientes potenciais, o conector de gerenciamento de leads no Partner Center foi projetado para que ele possa ser facilmente conectado com suas informações de CRM a uma lista de sistemas CRM disponíveis. Agora você pode aproveitar com facilidade os leads gerados pelo marketplace sem um esforço significativo de engenharia para integração a um sistema externo.

Aqui estão as instruções passo a passo sobre como se conectar a cada um dos destinos possíveis do cliente potencial:

**Dynamics CRM Online** -consulte [Configurar o gerenciamento de Lead para o Dynamics 365 for Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) para obter instruções sobre como configurar o Dynamics CRM Online para obter clientes potenciais.

**Marketo** -consulte [Configurar gerenciamento de leads no marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) para obter instruções sobre como configurar a configuração de cliente potencial do marketo para obter leads.

**Salesforce** -consulte [Configurar o gerenciamento de leads para o Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) para obter instruções sobre como configurar sua instância do Salesforce para obter clientes potenciais.

**Tabela do Azure** -consulte [Configurar o gerenciamento de leads usando uma tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para obter instruções sobre como configurar sua conta de armazenamento do Azure para obter clientes potenciais em uma tabela do Azure.

**Ponto de extremidade https** -consulte [Configurar o gerenciamento de Lead usando um ponto de extremidade https](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) para obter instruções sobre como configurar seu ponto de extremidade HTTPS para obter clientes potenciais.

Depois de configurar corretamente o destino do lead e acessar Publicar na oferta, validaremos a conexão e enviaremos um lead de teste. Quando você exibe a oferta antes de entrar no ar, pode também testar a conexão do lead tentando adquirir a oferta no ambiente de visualização. É importante verificar se as configurações de cliente potencial estão atualizadas para que você não perca nenhum cliente potencial. Portanto, atualize essas conexões sempre que algo for alterado do seu lado.

### <a name="what-are-the-next-steps"></a>Quais são as próximas etapas?

Depois que a configuração técnica estiver em vigor, você deverá incorporar esses leads em suas vendas atuais & estratégia de marketing e processos operacionais. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você estiver interessado em fornecer comentários e sugestões para permitir que sua equipe de vendas seja mais bem-sucedida com leads do Marketplace.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a>Erros comuns de configuração de leads durante a publicação no Partner Center

**Não foi possível salvar o cliente potencial no Dynamics CRM. Verifique as configurações de conta do Dynamics CRM. LastCRMError: não é possível entrar no Dynamics CRM, LastCRMException:** 

> Se a autenticação do O365 foi selecionada, verifique se a conta de usuário e senha é válida. Se AAD tiver sido selecionado, verifique a ID de locatário, ID do aplicativo e correspondências de chave secreta do aplicativo que foi configurado no AAD. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se o nome de usuário/senha da conta for válido, verifique se ele tem acesso ao Dynamics 365 e tem uma licença atribuída (etapas 11-15 se estiver usando Azure Active Directory ou configurações de segurança se estiver usando um usuário do Office). 

**Não foi possível salvar o cliente potencial no Dynamics CRM. O usuário não tem permissões de criação para o atributo leadsourcecode na entidade de cliente potencial** 

> O aplicativo/usuário não tem funções de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as etapas 11 a 15 se usando o Microsoft Azure Active Directory ou as configurações de segurança se usar um usuário do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: locatário não encontrado. Essa instância pode ocorrer se não houver nenhuma assinatura ativa para o locatário.**  

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a [ID do diretório](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)com base nas instruções na etapa 2 (em Azure Active Directory, daqui.

**Não foi possível salvar o cliente potencial no Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser falhou-nenhuma função está atribuída ao usuário.**  

> Resolução: atribua a função de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) em configurações de segurança.

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: o aplicativo com o identificador não foi encontrado no diretório** 

> A Id do aplicativo fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 8 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: o identificador de locatário solicitado não é válido e não é um formato de domínio externo válido** 

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 2 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: erro ao validar as credenciais.: segredo do cliente inválido fornecido.** 

> Resolução: entre no portal do Azure, verifique se a chave do aplicativo corresponde ao que está no Partner Center. Gere senha com base na instrução na Etapa 10 (no Azure Active Directory), de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial no Dynamics CRM. LastCRMError: o canal de solicitação atingiu o tempo limite ao aguardar uma resposta após 00:02:00. Aumente o valor de tempo limite passado para a chamada para solicitação ou aumente o valor de SendTimeout na associação. O tempo alocado para esta operação pode ter sido uma parte de um tempo limite maior.**  

> Resolução: entre no Partner Center, verifique os detalhes da vitrine >> URL de destino >> , verifique se é uma instância dinâmica de CRM válida.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quais são os clientes potenciais e por que são importantes para mim como um publicador no Marketplace?** 

Clientes potenciais são clientes que estão implantando seus produtos usando o Marketplace. Se o seu produto estiver listado no [Azure Marketplace](https://azuremarketplace.microsoft.com) ou [AppSource](https://appsource.microsoft.com/), você poderá receber os clientes potenciais de clientes que estejam interessados em seu produto, se você configurou o destino de cliente potencial em sua oferta.  

**Onde posso obter ajuda na configuração de meu destino de cliente potencial?** 

Você pode encontrar a documentação em [obter clientes potenciais](./partner-center-portal/commercial-marketplace-get-customer-leads.md) ou enviar um tíquete de suporte em [ajuda e suporte](https://partner.microsoft.com/support/v2/?stage=1). Selecione tipo de oferta e gerenciamento de Lead. 

**É necessário configurar um destino de cliente potencial para publicar uma oferta no Marketplace?**

Sim, se você estiver publicando um aplicativo SaaS do contato Me ou dos Serviços de Consultoria.  
 
**Como confirmar se a configuração de cliente potencial está correta?**

Depois de configurar sua oferta e o destino do cliente potencial, publique sua oferta. Na etapa de validação de cliente potencial, o Marketplace enviará um líder de teste para o destino do cliente potencial configurado em sua oferta. 

**Como encontrar o cliente potencial de teste?**

Pesquise "MSFT_TEST" em seu destino de Lead, aqui está um exemplo de dados de Lead de teste: 

company = MSFT_TEST_636573304831318844 

país: EUA 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 | \<Nome da oferta> 

oid = 00Do0000000ZHog 

telefone = 1234567890 

title = MSFT_TEST_636573304831318844 

**Tenho uma oferta ao vivo, mas não estou vendo clientes potenciais?**

Cada cliente potencial terá dados passados nos campos no destino do cliente potencial selecionado, os clientes potenciais virão neste formato: **Ação de origem | Oferta** 

  *Causas*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Ações*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Ofereça*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Aqui estão dados de exemplo das informações do cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Saiba mais em [Informações de cliente potencial](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Configurei o BLOB do Azure como meu cliente potencial de destino, por que não vejo o cliente potencial?** 

O cliente potencial só fica gravado quando você seleciona o armazenamento de BLOBs do Azure como destino do lead. Alterne para a tabela do Azure para receber o lead time em tempo real.

**Recebi um email do Marketplace, por que não é possível localizar o cliente potencial no meu CRM?**  

É possível que o domínio de email do usuário final é de. edu. Por motivos de privacidade, não passamos dados de identificação pessoal do domínio. edu. Envie um tíquete de suporte em [ajuda e suporte](https://partner.microsoft.com/support/v2/?stage=1).

**Configurei a Tabela do Azure/BLOB do Azure de acordo com o destino do meu cliente potencial, eu consigo ver os clientes potenciais?** 

Você pode acessar o BLOB ou a tabela de portal do Azure, ou pode baixar e instalar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir as tabelas/BLOBs da sua conta de armazenamento do Azure. 

**Configurei a tabela do Azure como destino de cliente potencial, posso ser notificado sempre que um novo cliente potencial é enviado pelo Marketplace?** 

Sim, siga as instruções para configurar o backup de tabelas do Azure + função sobre a documentação [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Configurei o Salesforce como destino como meu cliente potencial, por que não é possível localizar os clientes potenciais?** 

Verifique se a web para o formulário de cliente potencial é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.  
 
**Ocorreu um problema com o meu destino de Lead e eu perdi alguns clientes potenciais. Posso tê-los enviados por email?** 

Devido a políticas de privacidade, não podemos compartilhar informações de Lead por meio de emails não seguros. 

**Configurei o Armazenamento do Microsoft Azure (BLOB/Tabela) como meu destino de cliente potencial, quanto isso custará?** 

Dados de geração de cliente potencial (<1 GB para quase todos os publicadores). O custo dependerá do número de clientes potenciais recebido, se 1.000 potenciais são recebidos em um mês, isso custará cerca de 50 centavos. 
