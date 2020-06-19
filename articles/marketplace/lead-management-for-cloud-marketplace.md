---
title: Gerenciamento de cliente potencial para o marketplace comercial | Azure Marketplace e AppSource
description: Uma visão geral dos vários tópicos relacionados a publicação de ofertas e artefatos técnicos para o Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: f8b466dca9f3af55e3c11b39b3fbdac315af3675
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798581"
---
# <a name="lead-management-for-commercial-marketplace"></a>Gerenciamento de cliente potencial para o marketplace comercial

Os clientes são a essência de qualquer bom negócio. Na transformação das aquisições de produtos atuais, os comerciantes precisam se concentrar em se conectar diretamente com os clientes e criar uma relação. Por isso, gerar leads de alta qualidade é uma ferramenta essencial no ciclo de vendas. Depois de listar sua oferta no [Partner Center](https://partner.microsoft.com/), existem ferramentas habilitadas para que você receba informações de contato do cliente de forma programática, imediatamente depois que um cliente demonstra interesse ou implanta seu produto no marketplace. 

## <a name="what-are-leads-in-the-marketplace"></a>Quais são os clientes potenciais no marketplace?

São clientes potenciais de clientes que estão interessados ou implantando seus produtos do Marketplace. Se seu produto estiver listado no Microsoft Azure Marketplace ou AppSource, você poderá receber clientes pontenciais dos clientes depois que estiver configurado corretamente do seu CRM para às suas listagens no Partner Center. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Como conectar seu sistema CRM com o Partner Center

Para começar a receber clientes potenciais, o conector de Gerenciamento de Clientes Potenciais no Partner Center é projetado de forma que possa ser conectado com a sua informação de CRM para uma lista de sistema CRM disponível. Agora você pode aproveitar com facilidade os leads gerados pelo marketplace sem um esforço significativo de engenharia para integração a um sistema externo.

Aqui estão as instruções passo a passo sobre como se conectar a cada um dos destinos possíveis do cliente potencial:

**Dynamics CRM Online** – Veja [configuração do gerenciamento de clientes potenciais para o Dynamics 365 for Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) para obter instuções como configurar o Dynamics CRM Online para obter clientes potenciais.

**Marketo** – Veja [configuração do gerenciamento de clientes potenciais no Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) para obter instruções sobre como definir a Configuração de Clientes Potenciais do Marketo para obter clientes potenciais.

**Salesforce** – Veja [configuração do gerenciamento de clientes potenciais para Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) para obter instruções sobre como configurar sua instância da Salesforce para obter clientes potenciais.

**Tabela do Azure** – Veja [configuração do gerenciamento de clientes potenciais usando uma Tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para obter instruções sobre como configurar a conta de armazenamento do Azure para obter clientes potenciais em uma tabela do Azure.

**Ponto de extremidade HTTPS** – veja [configuração do gerenciamento de clientes potenciais usando um ponto de extremidade HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) para obter instruções sobre como configurar seu ponto de extremidade HTTPS para obter clientes potenciais.

Depois de configurar corretamente o destino do lead e acessar Publicar na oferta, validaremos a conexão e enviaremos um lead de teste. Quando você exibe a oferta antes de entrar no ar, pode também testar a conexão do lead tentando adquirir a oferta no ambiente de visualização. É importante verificar se as configurações de cliente potencial estão atualizadas para que você não perca nenhum cliente potencial. Portanto, atualize essas conexões sempre que algo for alterado do seu lado.

### <a name="what-are-the-next-steps"></a>Quais são as próximas etapas?

Quando a instalação técnica estiver em vigor, incorpore esses clientes potenciais à estratégia atual de vendas e marketing e aos processos operacionais. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Entre em contato conosco se estiver interessado em fornecer comentários e sugestões para que sua equipe de vendas tenha mais sucesso com os Clientes Potenciais do Marketplace.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a> Erros comuns de configuração de clientes potenciais durante a publicação no Partner Center

**Não foi possível salvar o cliente potencial ao Dynamics CRM. Verifique as configurações de conta do Dynamics CRM. LastCRMError: Não é possível entrar no Dynamics CRM, LastCRMException:** 

> Se a autenticação do O365 foi selecionada, verifique se a conta de usuário e senha é válida. Se AAD tiver sido selecionado, verifique a ID de locatário, ID do aplicativo e correspondências de chave secreta do aplicativo que foi configurado no AAD. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se o nome de usuário/senha da conta forem válidos, verifique se eles têm acesso ao Dynamics 365 e têm uma licença atribuída (as etapas 11 a 15 se estiver sendo usado o Microsoft Azure Active Directory ou as configurações de segurança se estiver sendo usado um usuário do Office). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. O usuário não tem permissões de criação para o atributo leadsourcecode na entidade de cliente potencial** 

> O aplicativo/usuário não tem funções de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as etapas 11 a 15 se usando o Microsoft Azure Active Directory ou as configurações de segurança se usar um usuário do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Locatário não encontrado. Esta instância pode ocorrer se não houver nenhuma assinatura ativa para o locatário.**  

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID do diretório com base nas instruções da Etapa 2 (no Azure Active Directory, [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)).

**Não foi possível salvar o cliente potencial ao Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falhou - não há função atribuída ao usuário.**  

> Resolução: Atribua a função de segurança ao gravador de cliente potencial do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) em Configurações de segurança.

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Aplicativo com identificador não foi localizado no diretório** 

> A Id do aplicativo fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 8 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: O identificador do locatário solicitado não é válido nem é um formato de domínio externo válido** 

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 2 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Erro ao validar credenciais.: Segredo do cliente inválido é fornecido.** 

> Resolução: Entre no portal do Azure, verifique se a chave do aplicativo corresponde ao que está no Partner Center. Gere senha com base na instrução na Etapa 10 (no Azure Active Directory), de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. LastCRMError: O canal de solicitação atingiu o tempo limite ao aguardar uma resposta após 00:02:00. Aumente o valor de tempo limite passado à chamada para solicitação ou aumente o valor de SendTimeout na associação. O tempo alocado para essa operação pode ter sido uma parte de um tempo limite maior.**  

> Resolução: Entre no Partner Center, verifique os Detalhes da vitrine >> Destino de cliente potencial >> URL, verifique se é uma instância válida do Dynamic CRM.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quais são os clientes potenciais e por que são importantes para mim como um publicador no Marketplace?** 

Clientes potenciais são clientes que estão implantando seus produtos usando o Marketplace. Se o seu produto estiver listado no [Azure Marketplace](https://azuremarketplace.microsoft.com) ou [AppSource](https://appsource.microsoft.com/), você poderá receber os clientes potenciais de clientes que estejam interessados em seu produto, se você configurou o destino de cliente potencial em sua oferta.  

**Onde posso obter ajuda na configuração de meu destino de cliente potencial?** 

Veja a documentação em [Obter clientes potenciais](./partner-center-portal/commercial-marketplace-get-customer-leads.md) ou envie um tíquete de suporte na seção de [Ajuda e suporte](https://aka.ms/marketplacepublishersupport). Selecione tipo de oferta e gerenciamento de clientes potenciais. 

**É necessário configurar um destino de cliente potencial para publicar uma oferta no Marketplace?**

Sim, se você estiver publicando um aplicativo SaaS do contato Me ou dos Serviços de Consultoria.  
 
**Como confirmar se a configuração de cliente potencial está correta?**

Depois de configurar sua oferta e o destino do cliente potencial, publique sua oferta. Na etapa de validação de cliente potencial, o Marketplace enviará um líder de teste para o destino do cliente potencial configurado em sua oferta. 

**Como encontrar o cliente potencial de teste?**

Pesquise “MSFT_TEST” no destino do cliente potencial, a seguir estão dados de clientes potenciais de teste de exemplo: 

company = MSFT_TEST_636573304831318844 

país: EUA 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Nome da oferta> 

oid = 00Do0000000ZHog 

telefone = 1234567890 

title = MSFT_TEST_636573304831318844 

**Eu tenho uma oferta online, mas não estou vendo os clientes potenciais?**

Cada cliente potencial terá dados passados nos campos no destino do cliente potencial selecionado, os clientes potenciais virão neste formato: **Ação de origem | Oferta** 

  *Origens*:

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Ações:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Ofertas:* :

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Aqui estão os dados de exemplo das informações do cliente*

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

O cliente potencial só fica gravado quando você seleciona o armazenamento de BLOBs do Azure como destino do lead. Alterne para a tabela do Azure para receber o cliente potencial em tempo real.

**Recebi um email do Marketplace, por que não é possível localizar o cliente potencial no meu CRM?**  

É possível que o domínio de email do usuário final é de. edu. Por motivos de privacidade, não passe dados de identificação pessoal do domínio .edu. Envie um tíquete de suporte em [Ajuda e suporte](https://aka.ms/marketplacepublishersupport).

**Configurei a Tabela do Azure/BLOB do Azure de acordo com o destino do meu cliente potencial, eu consigo ver os clientes potenciais?** 

Acesse o blob ou a tabela no portal do Azure, ou baixe e instale o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir tabelas/blobs da sua conta de armazenamento do Azure. 

**Configurei a tabela do Azure como destino de cliente potencial, posso ser notificado sempre que um novo cliente potencial é enviado pelo Marketplace?** 

Sim, siga as instruções para configurar o backup de tabelas do Azure + função sobre a documentação [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Configurei o Salesforce como destino como meu cliente potencial, por que não é possível localizar os clientes potenciais?**

Verifique se a web para o formulário de cliente potencial é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.  
 
**Houve um problema com meu destino de cliente potencial e eu me perdi de alguns clientes potenciais. É possível tê-los enviados para mim por email?** 

Devido a políticas de privacidade, não podemos compartilhar informações de clientes potenciais através de emails não seguros. 

**Configurei o Armazenamento do Microsoft Azure (BLOB/Tabela) como meu destino de cliente potencial, quanto isso custará?** 

Dados de geração de cliente potencial (<1 GB para quase todos os publicadores). O custo dependerá do número de clientes potenciais recebido, se 1.000 potenciais são recebidos em um mês, isso custará cerca de 50 centavos. 
