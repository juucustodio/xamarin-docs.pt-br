---
title: "Noções básicas sobre o recurso de Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: fba8412c53597260744bdce443a7e993a6990672
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="android-resource-basics"></a>Noções básicas sobre o recurso de Android

Quase todos os aplicativos do Android tem algum tipo de recursos na-los; no mínimo, eles geralmente têm os layouts de interface do usuário na forma de arquivos XML. Quando um aplicativo xamarin é criado pela primeira vez, os recursos padrão são instalação pelo modelo de projeto xamarin:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Arquivos de recurso](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Arquivos de recurso](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Os cinco arquivos que compõem os recursos padrão foram criados na pasta de recursos:

-  **Icon.PNG** &ndash; o ícone padrão para o aplicativo

-  **Main.axml** &ndash; o arquivo de layout de interface de usuário padrão para um aplicativo. Observe que, enquanto o Android, usa o **. XML** xamarin de extensão de arquivo, usa o **.axml** extensão de arquivo.

-  **Strings.XML** &ndash; uma tabela de cadeia de caracteres para ajudar com a localização do aplicativo

-  **AboutResources.txt** &ndash; isso não é necessário e pode ser excluído com segurança. Ele apenas fornece uma visão geral de alto nível de pasta de recursos e os arquivos nela.

-  **Resource.Designer.CS** &ndash; esse arquivo é automaticamente gerado e mantido pelo xamarin e mantém o exclusivo IDs atribuídas a cada recurso. Isso é muito semelhante e idêntico em objetivo para o arquivo de R.java que tem um aplicativo do Android escrito em Java. Ele é criado automaticamente com as ferramentas xamarin e será regenerado de tempos em tempos.


## <a name="creating-and-accessing-resources"></a>Criando e acessando recursos

Criando recursos é tão simple quanto a adição de arquivos para o diretório para o tipo de recurso em questão. Captura de tela abaixo mostra os recursos de cadeia de caracteres para localidades alemão foram adicionados a um projeto. Quando **Strings.xml** foi adicionado ao arquivo, o **ação de compilação** foi definida automaticamente para **AndroidResource** pelas ferramentas de xamarin:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Ação de compilação para Strings.xml definido como AndroidResource](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Ação de compilação para Strings.xml definido como AndroidResource](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Isso permite que as ferramentas xamarin compilar e inserir os recursos para o arquivo APK corretamente. Se por alguma razão o **ação de compilação** não está definido como **recurso Android**, em seguida, os arquivos serão excluídos do APK e qualquer tentativa de carregar ou acessar os recursos resultará em um erro em tempo de execução e o aplicativo falhará.

Além disso, é importante observar que, enquanto o Android só dá suporte a nomes de arquivos em minúsculas para itens de recurso, xamarin é um pouco mais tolerante com; Ela dará suporte a nomes de arquivos de maiusculas e minúsculas. A convenção de nomes de imagem é usar minúsculas com sublinhados como separadores (por exemplo, **meu\_imagem\_name.png**). Observe que os nomes de recursos não podem ser processados se traços ou espaços são usados como separadores.

Depois de adicionar recursos a um projeto, há duas maneiras de usá-los em um aplicativo &ndash; programaticamente (dentro do código) ou em arquivos XML.


## <a name="referencing-resources-programmatically"></a>Referência a recursos por meio de programação

Para acessar esses arquivos por meio de programação, eles recebem uma ID de recurso exclusivo. Esta ID de recurso é um inteiro definido em uma classe especial chamada `Resource`, que se encontra no arquivo **Resource.designer.cs**, e pode ter esta aparência:

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

Cada ID de recurso está contido dentro de uma classe aninhada que corresponde ao tipo de recurso. Por exemplo, quando o arquivo **Icon.png** foi adicionado ao projeto, xamarin atualizado o `Resource` classe, criar uma classe aninhada chamada `Drawable` uma constante interna denominada `Icon`.
Isso permite que o arquivo **Icon.png** para ser referenciado em código como `Resource.Drawable.Icon`. O `Resource` classe não deve ser manualmente editada, como as alterações feitas a ele serão substituídas pelo xamarin.

Ao fazer referência a recursos programaticamente (no código), eles podem ser acessados por meio da hierarquia de classe de recursos que usa a seguinte sintaxe:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash; o pacote que está fornecendo o recurso e só é necessário quando recursos de outros pacotes que estão sendo usados.

-  **ResourceType** &ndash; esse é o tipo de recurso aninhado que está dentro da classe de recurso descrita acima.

-  **Nome do recurso** &ndash; é o nome do arquivo de recurso (sem a extensão) ou o valor do atributo android: nome de recursos que estão em um elemento XML.


## <a name="referencing-resources-from-xml"></a>Referência a recursos do XML

Recursos em um arquivo XML são acessados por um seguindo uma sintaxe especial:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash; o pacote que está fornecendo o recurso e só é necessário quando recursos de outros pacotes que estão sendo usados.

-  **ResourceType** &ndash; esse é o tipo de recurso aninhado que está dentro da classe de recurso.

-  **Nome do recurso** &ndash; é o nome do recurso (*sem* a extensão do tipo de arquivo) ou o valor da `android:name` atributo para recursos que estão em um elemento XML.

Por exemplo, o conteúdo de um arquivo de layout, **Main.axml**, são as seguintes:

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

Este exemplo tem um [ `ImageView` ](https://developer.xamarin.com/recipes/android/controls/imageview) que requer um recurso drawable chamado **sinalizador**. O `ImageView` tem seu `src` atributo definido como  **@drawable/flag** . Quando a atividade é iniciada, Android procurará no diretório **recurso/Drawable** para um arquivo denominado **flag.png** (a extensão de arquivo pode ser outro formato de imagem, como **flag.jpg**) carregar esse arquivo e exibi-lo no `ImageView`.
Quando este aplicativo é executado, ele deverá ser parecida com a imagem a seguir:

![ImageView localizada](android-resource-basics-images/03-localized-screenshot.png)

