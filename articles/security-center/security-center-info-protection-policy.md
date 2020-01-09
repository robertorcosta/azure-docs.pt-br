---
title: Personalizar a proteção de informações do SQL – central de segurança do Azure
description: Aprenda a personalizar as políticas de proteção de informações na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611059"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalizar a política de proteção de informações do SQL na Central de Segurança do Azure (Visualizar)
 
Você pode definir e personalizar uma política de proteção de informações do SQL para todo o seu locatário do Azure, na central de segurança do Azure.

A proteção de informações é uma funcionalidade de segurança avançada para descobrir, classificar, rotular e relatar dados confidenciais em seus recursos de dados do Azure. Descobrir e classificar seus dados mais confidenciais (negócios, financeiros, de saúde, dados pessoais etc.) pode desempenhar uma função dinâmica em sua estatura de proteção de informações organizacionais. Esse recurso pode funcionar como a infraestrutura para:
- ajudar a atender a padrões de privacidade de dados e requisitos de conformidade regulamentar
- Cenários de segurança, como monitoramento (auditoria) e alertas de acesso anormal a dados confidenciais
- Controlando o acesso e fortalecendo a segurança dos armazenamentos de dados que contêm dados altamente confidenciais
 
[A Proteção de Informações SQL](../sql-database/sql-database-data-discovery-and-classification.md) implementa esse paradigma para seus armazenamentos de dados SQL, atualmente suportados pelo Banco de Dados SQL do Azure. O SQL Information Protection descobre e classifica automaticamente dados potencialmente confidenciais, fornece um mecanismo de rotulagem para marcar persistentemente os dados confidenciais com atributos de classificação e fornece um painel detalhado mostrando o estado de classificação do banco de dados. Além disso, calcula a sensibilidade do conjunto de resultados das consultas SQL, para que as consultas que extraem dados confidenciais possam ser explicitamente auditadas e os dados possam ser protegidos. Para obter mais informações sobre a proteção de informações do SQL, consulte [classificação e descoberta de dados do banco de dados SQL do Azure](../sql-database/sql-database-data-discovery-and-classification.md).
 
O mecanismo de classificação é baseado em duas construções principais que compõem a taxonomia de classificação - **Labels** e **Information Types**.
- **Labels** - Os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- **Tipos de informações** – fornece uma granularidade adicional para o tipo de dados armazenados na coluna.
 
A Proteção de Informações vem com um conjunto integrado de rótulos e tipos de informações, que são usados por padrão. Para personalizar esses rótulos e tipos, você pode personalizar a política de proteção de informações na central de segurança.
 
## <a name="customize-the-information-protection-policy"></a>Personalizar a política de proteção de informações
Para personalizar a política de proteção de informações do seu locatário do Azure, você precisa ter [privilégios administrativos no grupo de gerenciamento de raiz do locatário](security-center-management-groups.md). 
 
1. No menu principal da central de segurança, em **higiene de segurança de recursos** , acesse **dados & armazenamento** e clique no botão proteção de informações do **SQL** .

   ![Configurar a política de proteção de informações](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na página **proteção de informações do SQL** , você pode exibir seu conjunto atual de rótulos. Estes são os principais atributos de classificação usados para categorizar o nível de sensibilidade de seus dados. A partir daqui, você pode configurar os **rótulos de proteção de informações** e **tipos de informações** para o locatário. 
 
### <a name="customizing-labels"></a>Personalizando rótulos
 
1. Você pode editar ou excluir qualquer rótulo existente ou adicionar um novo rótulo. Para editar um rótulo existente, selecione esse rótulo e clique em **Configurar**, na parte superior ou no menu de contexto à direita. Para adicionar um novo marcador, clique em **Criar marcador** na barra de menu superior ou na parte inferior da tabela de marcadores.
2. No **rótulo de confidencialidade configurar** tela, você pode criar ou alterar o nome do rótulo e a descrição. Você também pode definir se o rótulo está ativo ou desabilitado ativando/desativando o **Ativado** ativar ou desativar. Por fim, você pode adicionar ou remover tipos de informações associadas ao marcador. Quaisquer dados descobertos que correspondam a esse tipo de informação incluirão automaticamente o rótulo de sensibilidade associado nas recomendações de classificação.
3. Clique em **OK**.
 
   ![Configurar rótulo de sensibilidade](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. As etiquetas são listadas por ordem crescente de sensibilidade. Para alterar a classificação entre marcadores, arraste os rótulos para reordená-los na tabela ou use os botões **Mover para cima** e **Mover para baixo** para alterar a ordem. 
 
    ![Configurar a política de proteção de informações](./media/security-center-info-protection-policy/move-up.png)
 
5. Clique em **salvar** na parte superior da tela quando tiver terminado.
 
 
## <a name="adding-and-customizing-information-types"></a>Adicionar e personalizar os tipos de informações
 
1. Você pode gerenciar e personalizar os tipos de informações clicando em **gerenciar tipos de informações**.
2. Para adicionar uma nova **tipo de informação**, selecione **criar o tipo de informação** no menu superior. Você pode configurar um nome, descrição e pesquisar cadeias de caracteres padrão para o **tipo de informação**. As sequências padrão de pesquisa podem, opcionalmente, usar palavras-chave com caracteres curinga (usando o caractere '%'), que o mecanismo de descoberta automatizada usa para identificar dados confidenciais em seus bancos de dados, com base nos metadados das colunas.
 
    ![Configurar a política de proteção de informações](./media/security-center-info-protection-policy/info-types.png)
 
3. Você também pode configurar os **Tipos de informações** internos adicionando cadeias de padrões de pesquisa adicionais, desabilitando algumas das cadeias existentes ou alterando a descrição. Você não pode excluir **tipos de informações** internos ou editar seus nomes. 
4. **Os tipos de informação** são listados em ordem de classificação de descoberta ascendente, o que significa que os tipos mais altos na lista tentarão corresponder primeiro. Para alterar a classificação entre os tipos de informação, arraste os tipos para o ponto certo na tabela ou use os botões **Mover para cima** e **Mover para baixo** para alterar a ordem. 
5. Clique em **Okey** quando você terminar.
6. Após você gerenciar os tipos de informação, certifique-se de associar os tipos relevantes com os rótulos relevantes, clicando **configurar** para um rótulo específico e adicionar ou excluir tipos de informações conforme apropriado.
7. Certifique-se de clicar em **Salvar** na lâmina principal **Etiquetas** para aplicar todas as suas alterações.
 
Depois que a política de proteção de informações estiver totalmente definida e salva, ela será aplicada à classificação de dados em todos os bancos de dados SQL do Azure no seu locatário.
 
 
## <a name="next-steps"></a>Próximos passos
 
Neste artigo, você aprendeu a definir uma política de Proteção de Informações do SQL na Central de Segurança do Azure. Para saber mais sobre como usar o SQL Information Protection para classificar e proteger dados confidenciais em seus bancos de dados SQL, consulte [Descoberta e Classificação de Dados do Banco de Dados SQL do Azure](../sql-database/sql-database-data-discovery-and-classification.md). 

Para obter mais informações sobre políticas de segurança e segurança de dados na Central de Segurança do Azure, consulte os seguintes artigos:
 
- [Configurando políticas de segurança na Central de segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para suas assinaturas do Azure e grupos de recursos
- [Segurança de dados da Central de segurança do Azure](security-center-data-security.md): Saiba como a Central de segurança gerencia e protege os dados
