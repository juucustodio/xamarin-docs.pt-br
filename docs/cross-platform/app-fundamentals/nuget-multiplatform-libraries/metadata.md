---
title: Editando metadados do NuGet
description: Este documento descreve como usar as opções de projeto para editar metadados do NuGet para bibliotecas multiplataforma. Ele aborda os metadados obrigatórios e opcionais.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: a3e1e8d1be1f84f707c80c42adb6b8d1e3f234a9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457010"
---
# <a name="editing-nuget-metadata"></a>Editando metadados do NuGet

_Usar as opções de projeto para editar metadados do NuGet para bibliotecas multiplataforma_

Tipos de projeto de biblioteca (como PCL ou .NET Standard ou o novo tipo de projeto NuGet) têm uma seção **pacote NuGet** na janela **Opções do projeto** .

A seção de **metadados** configura os valores usados no arquivo de [manifesto do pacote NuGet **. nuspec** ](/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informações necessárias

A guia **geral** contém quatro campos que devem ser inseridos para gerar um pacote NuGet:

[![Janela de metadados necessária do pacote NuGet](metadata-images/metadata-general-sml.png)](metadata-images/metadata-general.png#lightbox)

- **ID** – o identificador do pacote, que deve ser exclusivo no NuGet.org (ou onde quer que o pacote seja distribuído). Siga este [guia](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e use apenas caracteres que sejam válidos em uma URL (sem espaços e evite a maioria dos caracteres especiais).
- **Versão** – escolha um número de versão consistente com [as regras de controle de versão do NuGet](/nuget/create-packages/dependency-versions).
- **Autores** – lista de nomes separados por vírgulas.
- **Descrição** – visão geral dos recursos do pacote que são exibidos quando os usuários estão selecionando o pacote.

> [!NOTE]
> Lembre-se de incrementar o número de versão ao criar novas versões para distribuição para o NuGet ou outros usuários.

Para obter mais informações, consulte a [referência de elementos necessários](/nuget/schema/nuspec#required-metadata-elements) para obter mais informações, bem como essas instruções detalhadas sobre como [escolher um identificador de pacote exclusivo e definir o número de versão](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e [definir um tipo de pacote](/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Todos os campos nessa guia devem ser inseridos; caso contrário, uma mensagem de erro será exibida: _"o projeto não tem metadados NuGet, portanto, um pacote NuGet não será criado. Os metadados do pacote NuGet podem ser especificados na seção de metadados em opções do projeto "_

## <a name="optional-metadata"></a>Metadados opcionais

A guia **detalhes** contém campos opcionais a serem incluídos no arquivo de manifesto do pacote NuGet.

[![Janela de metadados opcionais do pacote NuGet](metadata-images/metadata-detail-sml.png)](metadata-images/metadata-detail.png#lightbox)

Consulte a [referência de elementos opcionais](/nuget/schema/nuspec#optional-metadata-elements) para obter mais informações sobre os campos obrigatórios e opcionais.

> [!NOTE]
> Se o pacote NuGet estiver sendo distribuído no [NuGet.org](https://www.nuget.org) , é recomendável fornecer o máximo possível de informações.

## <a name="related-links"></a>Links Relacionados

- [Referência de. nuspec](/nuget/schema/nuspec#general-form-and-schema)