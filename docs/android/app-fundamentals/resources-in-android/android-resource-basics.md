---
title: Noções básicas sobre o recurso do Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: b0f747c37362997563a35d9b94f8e677d4104ee1
ms.sourcegitcommit: e7f27ba75cae5099ef053b819b84132a77d4f9e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59587758"
---
# <a name="android-resource-basics"></a>Noções básicas sobre o recurso do Android

Quase todos os aplicativos Android que algum tipo de recursos dentro deles; no mínimo, eles geralmente têm os layouts de interface do usuário na forma de arquivos XML. Quando um aplicativo xamarin. Android é criado, os recursos padrão são configuradas pelo modelo de projeto xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Arquivos de recurso](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Arquivos de recurso](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Os cinco arquivos que compõem os recursos padrão foram criados na pasta de recursos:

-  **Icon. PNG** &ndash; o ícone padrão para o aplicativo

-  **Main. axml** &ndash; o arquivo de layout de interface de usuário padrão para um aplicativo. Observe que, enquanto o Android usa o **. XML** extensão de arquivo, xamarin. Android usa o **. axml** extensão de arquivo.

-  **Strings. XML** &ndash; uma tabela de cadeia de caracteres para ajudar com a localização do aplicativo

-  **Aboutresources** &ndash; isso não é necessário e pode ser excluído com segurança. Ele apenas fornece uma visão geral de alto nível de pasta de recursos e os arquivos dentro dele.

-  **Resource.Designer.CS** &ndash; esse arquivo é automaticamente gerado e mantido pelo xamarin. Android e mantém o exclusivo IDs atribuídas a cada recurso. Isso é muito semelhante e idênticos na finalidade para o arquivo R.java que teria um aplicativo do Android escrito em Java. Ele é criado automaticamente pelas ferramentas do xamarin. Android e será regenerado de tempos em tempos.


## <a name="creating-and-accessing-resources"></a>Criando e acessando recursos

Criação de recursos é tão simple quanto adicionar arquivos para o diretório para o tipo de recurso em questão. Captura de tela abaixo mostra os recursos de cadeia de caracteres para localidades alemãs foram adicionados a um projeto. Quando **strings. XML** foi adicionado ao arquivo, o **Build Action** foi automaticamente definido como **AndroidResource** pelas ferramentas do xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Criar ação para definir como AndroidResource de strings. XML](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Criar ação para definir como AndroidResource de strings. XML](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Isso permite que as ferramentas do xamarin. Android compilar corretamente e inserir os recursos para o arquivo APK. Se, por algum motivo a **ação de compilação** não está definido como **Android recursos**, em seguida, os arquivos serão excluídos do APK e qualquer tentativa de carregar ou acessar os recursos resultará em um erro de tempo de execução e o aplicativo falhará.

Além disso, é importante observar que, embora o Android é compatível apenas com nomes de arquivo em minúsculas para itens de recurso, xamarin. Android é um pouco mais tolerante; ele dará suporte a nomes de arquivo de letras maiusculas e minúsculas. A convenção de nomes de imagem é usar letras minúsculas com sublinhados como separadores (por exemplo, **minha\_imagem\_name.png**). Observe que os nomes de recursos não podem ser processados se traços ou espaços são usados como separadores.

Depois que os recursos foram adicionados a um projeto, há duas maneiras de usá-los em um aplicativo &ndash; programaticamente (dentro do código) ou de arquivos XML.


## <a name="referencing-resources-programmatically"></a>Referenciando recursos por meio de programação

Para acessar esses arquivos por meio de programação, eles recebem uma ID de recurso exclusivo. Esta ID de recurso é um inteiro definido em uma classe especial chamada `Resource`, que é encontrado no arquivo **Resource.designer.cs**, e pode ter esta aparência:

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

Cada ID de recurso está contido dentro de uma classe aninhada que corresponde ao tipo de recurso. Por exemplo, quando o arquivo **icon. PNG** foi adicionado ao projeto, xamarin. Android atualizado o `Resource` classe, criar uma classe aninhada chamada `Drawable` dentro um constante denominada `Icon`.
Isso permite que o arquivo **icon. PNG** ao ser chamado no código como `Resource.Drawable.Icon`. O `Resource` classe deve não ser editado manualmente, pois todas as alterações são feitas a ele serão substituídas pelo xamarin. Android.

Ao fazer referência a recursos por meio de programação (código), eles podem ser acessados por meio da hierarquia de classe de recursos que usa a seguinte sintaxe:

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash; o pacote que está fornecendo o recurso e só é necessário quando os recursos de outros pacotes que estão sendo usados.

-  **ResourceType** &ndash; esse é o tipo de recurso aninhado que está dentro da classe de recurso descrita acima.

-  **Nome do recurso** &ndash; é o nome do arquivo do recurso (sem extensão) ou o valor do atributo android: o nome de recursos que estão em um elemento XML.


## <a name="referencing-resources-from-xml"></a>Fazendo referência a recursos de XML

Recursos em um arquivo XML são acessados por um seguindo uma sintaxe especial:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

-  **PackageName** &ndash; o pacote que está fornecendo o recurso e só é necessário quando os recursos de outros pacotes que estão sendo usados.

-  **ResourceType** &ndash; esse é o tipo de recurso aninhado que está dentro da classe de recurso.

-  **Nome do recurso** &ndash; é o nome do recurso (*sem* a extensão de tipo de arquivo) ou o valor da `android:name` atributo para os recursos que estão em um elemento XML.

Por exemplo, o conteúdo de um arquivo de layout **Main. axml**, são da seguinte maneira:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

Este exemplo tem um [ `ImageView` ](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) que requer um recurso desenhável denominado **sinalizador**. O `ImageView` tem sua `src` atributo definido como `@drawable/flag`. Quando a atividade é iniciado, o Android ficará dentro do diretório **recurso/Drawable** para um arquivo denominado **flag.png** (a extensão de arquivo pode ser outro formato de imagem, como **flag.jpg**) e carregue o arquivo e exibi-lo no `ImageView`.
Quando esse aplicativo é executado, ele seria algo semelhante a imagem a seguir:

![ImageView localizada](android-resource-basics-images/03-localized-screenshot.png)
