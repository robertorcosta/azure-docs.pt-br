---
title: Referência de atributo SAP SuccessFactors | Microsoft Docs
description: Saiba quais atributos do SuccessFactors são suportados pelo provisionamento orientado por SuccessFactors-RH
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522349"
---
# <a name="sap-successfactors-attribute-reference"></a>Referência de atributo SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Entidades e atributos de sucesso suportados

A tabela abaixo captura a lista de atributos SuccessFactors suportados pelos dois aplicativos de provisionamento a seguir: 
* [Fatores de sucesso para provisionamento de usuários de diretório ativo](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Fatores de sucesso para provisionamento de usuários Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entidade SuccessFactors                  | Atributo SuccessFactors     | Tipo de operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Pessoa Perpessoa                              | personIdExterno             | Ler           |
| 2  | Pessoa Perpessoa                              | Personid                     | Ler           |
| 3  | Pessoa Perpessoa                              | perPersonUuid                | Ler           |
| 4  | PerPersonal                            | displayName                  | Ler           |
| 5  | PerPersonal                            | firstName                    | Ler           |
| 6  | PerPersonal                            | gender                       | Ler           |
| 7  | PerPersonal                            | lastName                     | Ler           |
| 8  | PerPersonal                            | middleName                   | Ler           |
| 9  | PerPersonal                            | preferredName                | Ler           |
| 10 | Usuário                                   | addressLine1                 | Ler           |
| 11 | Usuário                                   | Addressline2                 | Ler           |
| 12 | Usuário                                   | endereçoLIne3                 | Ler           |
| 13 | Usuário                                   | businessPhone                | Ler           |
| 14 | Usuário                                   | Celular                    | Ler           |
| 15 | Usuário                                   | city                         | Ler           |
| 16 | Usuário                                   | country                      | Ler           |
| 17 | Usuário                                   | custom01                     | Ler           |
| 18 | Usuário                                   | custom02                     | Ler           |
| 19 | Usuário                                   | custom03                     | Ler           |
| 20 | Usuário                                   | custom04                     | Ler           |
| 21 | Usuário                                   | custom05                     | Ler           |
| 22 | Usuário                                   | custom06                     | Ler           |
| 23 | Usuário                                   | custom07                     | Ler           |
| 24 | Usuário                                   | custom08                     | Ler           |
| 25 | Usuário                                   | personalizado09                     | Ler           |
| 26 | Usuário                                   | personalizado10                     | Ler           |
| 27 | Usuário                                   | personalizado11                     | Ler           |
| 28 | Usuário                                   | personalizado12                     | Ler           |
| 29 | Usuário                                   | personalizado13                     | Ler           |
| 30 | Usuário                                   | personalizado14                     | Ler           |
| 31 | Usuário                                   | Empid                        | Ler           |
| 32 | Usuário                                   | homePhone                    | Ler           |
| 33 | Usuário                                   | jobFamily                    | Ler           |
| 34 | Usuário                                   | apelido                     | Ler           |
| 35 | Usuário                                   | state                        | Ler           |
| 36 | Usuário                                   | timeZone                     | Ler           |
| 37 | Usuário                                   | Nome de Usuário                     | Ler           |
| 38 | Usuário                                   | Zipcode                      | Ler           |
| 39 | PorTelefone                               | areaCode                     | Ler           |
| 40 | PorTelefone                               | countryCode                  | Ler           |
| 41 | PorTelefone                               | extensão                    | Ler           |
| 42 | PorTelefone                               | phoneNumber                  | Ler           |
| 43 | PorTelefone                               | phoneType                    | Ler           |
| 44 | Pore-mail                               | emailAddress                 | Leitura, Gravação    |
| 45 | Pore-mail                               | e-mailType                    | Ler           |
| 46 | EmpEmployment                          | firstDateWorked              | Ler           |
| 47 | EmpEmployment                          | lastDateWorked               | Ler           |
| 48 | EmpEmployment                          | userId                       | Ler           |
| 49 | EmpEmployment                          | isContingentWorker           | Ler           |
| 50 | EmpJob                                 | countryOfCompany             | Ler           |
| 51 | EmpJob                                 | emplStatus                   | Ler           |
| 52 | EmpJob                                 | endDate                      | Ler           |
| 53 | EmpJob                                 | startDate                    | Ler           |
| 54 | EmpJob                                 | jobTitle                     | Ler           |
| 55 | EmpJob                                 | position                     | Ler           |
| 65 | EmpJob                                 | customString13               | Ler           |
| 56 | EmpJob                                 | Managerid                    | Ler           |
| 57 | Unidade de\.negócios empJob                   | businessUnit                 | Ler           |
| 58 | Unidade de\.negócios empJob                   | businessUnitId               | Ler           |
| 59 | Empresa EmpJob\.                        | company                      | Ler           |
| 60 | Empresa EmpJob\.                        | companyId                    | Ler           |
| 61 | EmpJob\.\.Empresa PaísDe Registro | twoCharCountryCode           | Ler           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Ler           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Ler           |
| 64 | EmpJob\.CostCenter                     | costCenterDescrição        | Ler           |
| 65 | EmpJob\.Department                     | department                   | Ler           |
| 66 | EmpJob\.Department                     | departmentId                 | Ler           |
| 67 | Divisão\.EmpJob                       | division                     | Ler           |
| 68 | Divisão\.EmpJob                       | divisionId                   | Ler           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Ler           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Ler           |
| 71 | Localização do\.EmpJob                       | Nome do local                 | Ler           |
| 72 | Localização do\.EmpJob                       | officeLocationAddress        | Ler           |
| 73 | Localização do\.EmpJob                       | officeLocationCity           | Ler           |
| 74 | Localização do\.EmpJob                       | officeLocationCustomString4  | Ler           |
| 75 | Localização do\.EmpJob                       | officeLocationZipCode        | Ler           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Ler           |
| 77 | EmpEmploymentTermination               | activeEmploymentsContagem       | Ler           |
| 78 | EmpEmploymentTermination               | mais recenteData de término        | Ler           |


## <a name="default-attribute-mapping"></a>Mapeamento de atributos padrão

A tabela abaixo fornece o mapeamento de atributos padrão entre os atributos SuccessFactors listados acima e atributos AD/Azure AD. Na lâmina "Mapeamento" do aplicativo de provisionamento Azure AD, você pode modificar esse mapeamento padrão para incluir atributos da lista acima. 

| \# | Entidade SuccessFactors                  | Atributo SuccessFactors | Mapeamento padrão de atributos AD/Azure AD   | Observação de Processamento                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Pessoa Perpessoa                              | personIdExterno         | employeeId                              | Usado como atributo correspondente                                                                   |
| 2  | Pessoa Perpessoa                              | perPersonUuid            | \[Não mapeado \- usado como âncora de origem\] | Durante a sincronização inicial, o Serviço de Provisionamento vincula a pessoaUuid ao objetoGuid existente\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | Usuário                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Usuário                                   | city                     | l                                       | NA                                                                                           |
| 8  | Usuário                                   | country                  | co                                      | NA                                                                                           |
| 9  | Usuário                                   | state                    | st                                      | NA                                                                                           |
| 10 | Usuário                                   | Nome de Usuário                 | samAccountName                          | NA                                                                                           |
| 11 | Usuário                                   | Zipcode                  | postalCode                              | NA                                                                                           |
| 12 | Pore-mail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | título                                   | NA                                                                                           |
| 14 | EmpJob                                 | Managerid                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.\.Empresa PaísDe Registro | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.Department                     | department               | department                              | NA                                                                                           |
| 17 | Divisão\.EmpJob                       | division                 | company                                 | NA                                                                                           |
| 18 | Localização do\.EmpJob                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | Localização do\.EmpJob                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsContagem   | accountEnabled                          | se activeEmploymentsCount=0, desabilite a conta\.                                           |

