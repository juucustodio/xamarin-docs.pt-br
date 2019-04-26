---
title: Posso atualizar o modelo padrão Xamarin.Forms para um pacote NuGet mais recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: e439d39dd8591cad14485e64aabab2d6016a8e27
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345899"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>Posso atualizar o modelo padrão Xamarin.Forms para um pacote NuGet mais recente?

Este guia usa o modelo de biblioteca xamarin. Forms .NET Standard como um exemplo, mas o mesmo método geral também funcionarão para o modelo de projeto compartilhado do xamarin. Forms. Este guia foi escrito com o exemplo de atualização do xamarin. Forms 1.5.1.6471 para 2.1.0.6529, mas as mesmas etapas são possíveis definir outras versões como o padrão em vez disso.

1.  Copie o modelo original `.zip` de:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  Descompacte o `.zip` para um local temporário.

3.  Altere todas as ocorrências da versão antiga do pacote de formulários para a nova versão que você deseja usar.
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Exemplo: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  Altere o elemento "nome" da janela principal [arquivo de modelo multiprojeto](https://msdn.microsoft.com/library/ms185308.aspx) (`Xamarin.Forms.PCL.vstemplate`) para torná-lo exclusivo. Por exemplo:
    > <Name>Aplicativo em branco (xamarin. Forms portátil) - 2.1.0.6529</Name>

5.  Novamente, compacte a pasta de modelo inteiro. Certifique-se de corresponder à estrutura do arquivo original do `.zip` arquivo. O `Xamarin.Forms.PCL.vstemplate` arquivo deve estar na parte superior do `.zip` arquivo, não em todas as pastas.

6.  Crie um subdiretório de "Aplicativos móveis" na sua pasta de modelos do Visual Studio por usuário:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  Copie a nova pasta zipada modelo para o novo diretório de "Aplicativos móveis".

8.  Baixe o pacote do NuGet que corresponde à versão da etapa 3. Por exemplo, [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (Consulte também [ https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) e copie-o para a subpasta apropriada da pasta de extensões do Xamarin para Visual Studio:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
