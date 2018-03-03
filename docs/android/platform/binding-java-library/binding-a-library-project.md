---
title: "Associação de um projeto de biblioteca do Eclipse"
description: Este passo a passo explica como usar modelos de projeto do xamarin para associar um projeto de biblioteca Android do Eclipse.
ms.topic: article
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/14/2017
ms.openlocfilehash: 2048056415e0969e13e305b1dbba8bdb7ffabd30
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="binding-an-eclipse-library-project"></a>Associação de um projeto de biblioteca do Eclipse

_Este passo a passo explica como usar modelos de projeto do xamarin para associar um projeto de biblioteca Android do Eclipse._

<a name=overview />

## <a name="overview"></a>Visão geral

Embora. Arquivos AAR estão se tornando a norma para distribuição de biblioteca Android, em alguns casos, é necessário criar uma associação para um *projeto de biblioteca Android*. Projetos de biblioteca Android são projetos Android especiais que contêm código podem ser compartilhado e recursos que podem ser referenciados por projetos de aplicativo do Android. Normalmente, você associa a um projeto de biblioteca Android quando a biblioteca é criada no IDE do Eclipse.
Este passo a passo fornece exemplos de como criar um projeto de biblioteca Android. ZIP da estrutura de diretório de um projeto do Eclipse.

Projetos de biblioteca Android são diferentes dos projetos Android regulares que eles não são compilados em um APK e não são, por conta própria, implantado em um dispositivo. Em vez disso, um projeto de biblioteca Android deve ser referenciada por um projeto de aplicativo do Android. Quando é criado um projeto de aplicativo do Android, o projeto de biblioteca Android é compilado pela primeira vez. O projeto de aplicativo do Android será ser absorvidos para o projeto de biblioteca Android compilado e incluir o código e os recursos em APK para distribuição. Devido a essa diferença, criando uma associação de um projeto de biblioteca Android é ligeiramente diferente da criação de uma associação para um Java. JAR ou. Arquivo AAR.


<a name="Walkthrough" />

## <a name="walkthrough"></a>Passo a passo

Para usar um projeto de biblioteca Android em um projeto de vinculação do xamarin Java primeiro é necessário para compilar o projeto de biblioteca Android no Eclipse. Captura de tela a seguir mostra um exemplo de um projeto de biblioteca Android após a compilação: 

[ ![Exemplo de projeto de biblioteca no Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png)

Observe que o código-fonte do projeto de biblioteca Android foi compilado para um temporário. Arquivo JAR chamado **android mapviewballoons.jar**, e que os recursos foram copiados para o **res/bin/situação** pasta. 

Depois que o projeto de biblioteca Android foi compilado no Eclipse, ele pode, em seguida, ser associado usando um projeto de vinculação do xamarin Java. Primeiro um. Deve ser criado o arquivo ZIP que contém o **bin** e **res** pastas do projeto de biblioteca Android. É importante que você remova a intervenção **situação** subdiretório para que os recursos estejam em **bin/res**. Captura de tela a seguir mostra o conteúdo de um tal. Arquivo ZIP: 

[ ![Conteúdo da biblioteca do Android. zip do projeto](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png)

Isso. Arquivo ZIP é adicionado ao projeto de vinculação do xamarin Java, conforme mostrado na seguinte captura de tela:

[ ![ZIP adicionado ao projeto Java de associação](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png)

Observe que a ação de compilação da. Arquivo ZIP foi definido automaticamente como **LibraryProjectZip**.

Se houver algum. Arquivos JAR necessários para o projeto de biblioteca Android, eles devem ser adicionados para o **Jars** pasta do projeto de biblioteca de ligação de Java e o **ação de compilação** definida como **ReferenceJar**. Um exemplo disso pode ser visto na captura de tela abaixo: 

[ ![Ação de compilação definido como ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png)

Quando essas etapas forem concluídas, o projeto de vinculação do xamarin Java pode ser usado conforme descrito anteriormente neste documento.

> [!NOTE]
> **Observação**: não há suporte para a compilação de projetos de biblioteca Android em outros IDEs neste momento. Outros IDEs não pode criar a mesma estrutura de diretório ou arquivos de **bin** pasta do Eclipse. 

<a name="Summary" /> 

## <a name="summary"></a>Resumo

Neste artigo, percorremos o processo de associação de um projeto de biblioteca Android. Criamos o projeto de biblioteca Android no Eclipse, em seguida, criamos um arquivo zip do **bin** e **res** pastas do projeto de biblioteca Android. Em seguida, usamos essa zip para criar um projeto de vinculação do xamarin Java. 

