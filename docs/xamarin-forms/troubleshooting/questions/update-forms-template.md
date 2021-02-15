---
title: Posso atualizar o Xamarin.Forms modelo padrão para um pacote NuGet mais recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9dc5b8e63a35c3a0b797d0794af7a31aea4969c9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555587"
---
# <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>Posso atualizar o Xamarin.Forms modelo padrão para um pacote NuGet mais recente?

Este guia usa o Xamarin.Forms modelo de biblioteca .net Standard como um exemplo, mas o mesmo método geral também funcionará para o Xamarin.Forms modelo de projeto compartilhado. Este guia é escrito com o exemplo de atualização de Xamarin.Forms 1.5.1.6471 para 2.1.0.6529, mas as mesmas etapas são possíveis para definir outras versões como o padrão.

1. Copie o modelo original `.zip` de:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. Descompacte o `.zip` em um local temporário.

3. Altere todas as ocorrências da versão antiga do Xamarin.Forms pacote para a nova versão que você gostaria de usar.
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Exemplo: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. Altere o elemento "Name" do arquivo de [modelo de vários projetos](/visualstudio/ide/how-to-create-multi-project-templates) principal ( `Xamarin.Forms.PCL.vstemplate` ) para torná-lo exclusivo. Por exemplo:

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. Recompacte toda a pasta de modelos. Certifique-se de corresponder à estrutura de arquivo original do `.zip` arquivo. O `Xamarin.Forms.PCL.vstemplate` arquivo deve estar na parte superior do `.zip` arquivo, não em nenhuma pasta.

6. Crie um subdiretório "aplicativos móveis" em sua pasta de modelos do Visual Studio por usuário:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. Copie a nova pasta de modelo compactado para o novo diretório "aplicativos móveis".

8. Baixe o pacote NuGet que corresponde à versão da etapa 3. Por exemplo, [ https://nuget.org/api/v2/package/ Xamarin.Forms /2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (Veja também [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file) ) e copie-o na subpasta apropriada da pasta de extensões do Xamarin Visual Studio:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`