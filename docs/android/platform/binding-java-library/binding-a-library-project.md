---
title: Associação de um projeto de biblioteca do Eclipse
description: Este tutorial explica como usar modelos de projeto Xamarin. Android para associar um projeto de biblioteca do Android do eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 78b70ce70292e589aee4a1dbe56f3765552ece7a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757712"
---
# <a name="binding-an-eclipse-library-project"></a>Associação de um projeto de biblioteca do Eclipse

_Este tutorial explica como usar modelos de projeto Xamarin. Android para associar um projeto de biblioteca do Android do eclipse._

## <a name="overview"></a>Visão geral

Ainda. Os arquivos AAR estão se tornando cada vez mais a norma da distribuição da biblioteca do Android, em alguns casos, é necessário criar uma associação para um *projeto de biblioteca do Android*. Os projetos de biblioteca do Android são projetos especiais do Android que contêm código compartilhável e recursos que podem ser referenciados por projetos de aplicativos do Android. Normalmente, você se associa a um projeto de biblioteca do Android quando a biblioteca é criada no IDE do eclipse.
Este tutorial fornece exemplos de como criar um projeto de biblioteca do Android. ZIP da estrutura de diretório de um projeto Eclipse.

Os projetos de biblioteca do Android são diferentes dos projetos comuns do Android, pois não são compilados em um APK e não são, por conta própria, implantáveis em um dispositivo. Em vez disso, um projeto de biblioteca do Android deve ser referenciado por um projeto de aplicativo do Android. Quando um projeto de aplicativo Android é compilado, o projeto de biblioteca do Android é compilado primeiro. O projeto de aplicativo do Android será então absorvido no projeto de biblioteca do Android compilado e incluirá o código e os recursos no APK para distribuição. Devido a essa diferença, a criação de uma associação para um projeto de biblioteca Android é um pouco diferente da criação de uma associação para um Java. JAR ou. Arquivo AAR.

## <a name="walkthrough"></a>Passo a passo

Para usar um projeto de biblioteca do Android em um projeto de associação Java do Xamarin. Android, primeiro é necessário criar o projeto de biblioteca do Android no Eclipse. A captura de tela a seguir mostra um exemplo de um projeto de biblioteca Android após a compilação: 

[![Exemplo de projeto de biblioteca no Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Observe que o código-fonte do projeto de biblioteca do Android foi compilado em um temporário. Arquivo JAR chamado **Android-mapviewballoons. jar**e que os recursos foram copiados para a pasta **bin/res/de defragmentação** . 

Depois que o projeto de biblioteca do Android tiver sido compilado no Eclipse, ele poderá ser associado usando um projeto de associação Java do Xamarin. Android. Primeiro a. O arquivo ZIP deve ser criado contendo as pastas **bin** e **res** do projeto de biblioteca do Android. É importante que você remova o subdiretório de processamento intermediário para que os recursos residam em **bin/res**. A captura de tela a seguir mostra o conteúdo de um desses. Arquivo ZIP: 

[![Conteúdo da biblioteca do Android Project. zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Esta. Em seguida, o arquivo ZIP é adicionado ao projeto de associação Java Xamarin. Android, conforme mostrado na seguinte captura de tela:

[![Zip adicionado ao projeto de associação Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Observe que a ação de Build do. O arquivo ZIP foi definido automaticamente como **LibraryProjectZip**.

Se houver algum. Arquivos JAR necessários para o projeto de biblioteca do Android, eles devem ser adicionados à pasta **jars** do projeto de biblioteca de associação Java e a **ação de compilação** definida como **ReferenceJar**. Um exemplo disso pode ser visto na captura de tela abaixo: 

[![Ação de compilação definida como ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Depois que essas etapas forem concluídas, o projeto de associação Java do Xamarin. Android poderá ser usado conforme descrito anteriormente neste documento.

> [!NOTE]
> Não há suporte para a compilação de projetos de biblioteca do Android em outros IDEs no momento. Outros IDEs podem não criar a mesma estrutura de diretório ou arquivos na pasta **bin** como eclipse. 

## <a name="summary"></a>Resumo

Neste artigo, percorremos o processo de vinculação de um projeto de biblioteca do Android. Criamos o projeto de biblioteca do Android no Eclipse e criamos um arquivo zip nas pastas **bin** e **res** do projeto de biblioteca do Android. Em seguida, usamos esse zip para criar um projeto de associação Java Xamarin. Android. 
