---
title: Posso atualizar o modelo padrão Xamarin.Forms para um pacote NuGet mais recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 0c0b5e04bafc748b48ea007162cfd7277cc23752
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528346"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>Posso atualizar o modelo padrão Xamarin.Forms para um pacote NuGet mais recente?

Este guia usa o modelo de biblioteca Xamarin. Forms .NET Standard como um exemplo, mas o mesmo método geral também funcionará para o modelo de projeto compartilhado Xamarin. Forms. Este guia é escrito com o exemplo de atualização de Xamarin. Forms 1.5.1.6471 para 2.1.0.6529, mas as mesmas etapas são possíveis para definir outras versões como o padrão.

1. Copie o modelo `.zip` original de:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. Descompacte o `.zip` em um local temporário.

3. Altere todas as ocorrências da versão antiga do pacote Xamarin. Forms para a nova versão que você gostaria de usar.
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Exemplo`<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. Altere o elemento "Name" do arquivo de [modelo de vários projetos](https://msdn.microsoft.com/library/ms185308.aspx) principal (`Xamarin.Forms.PCL.vstemplate`) para torná-lo exclusivo. Por exemplo:

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. Recompacte toda a pasta de modelos. Certifique-se de corresponder à estrutura de arquivo original `.zip` do arquivo. O `Xamarin.Forms.PCL.vstemplate` arquivo deve estar na parte superior `.zip` do arquivo, não em nenhuma pasta.

6. Crie um subdiretório "aplicativos móveis" em sua pasta de modelos do Visual Studio por usuário:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. Copie a nova pasta de modelo compactado para o novo diretório "aplicativos móveis".

8. Baixe o pacote NuGet que corresponde à versão da etapa 3. Por exemplo, [http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (Veja também [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) e copie-o na subpasta apropriada da pasta de extensões do Xamarin Visual Studio:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
