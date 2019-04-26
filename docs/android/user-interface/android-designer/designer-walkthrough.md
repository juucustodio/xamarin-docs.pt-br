---
title: Usando o Designer do xamarin. Android
description: Este artigo é um passo a passo do xamarin. Android Designer. Ele demonstra como criar uma interface do usuário para um aplicativo de navegador de cor pequeno; Essa interface do usuário é criado inteiramente no Designer.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1174fe5cb417d4977fd6519086e6c4942e74c10b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61207535"
---
# <a name="using-the-xamarinandroid-designer"></a>Usando o Designer do xamarin. Android

_Este artigo é um passo a passo do xamarin. Android Designer. Ele demonstra como criar uma interface do usuário para um aplicativo de navegador de cor pequeno; Essa interface do usuário é criado inteiramente no Designer._


## <a name="overview"></a>Visão geral

Interfaces de usuário do Android podem ser criados declarativamente usando arquivos XML ou gravando o código de forma programática. O Designer do xamarin. Android permite aos desenvolvedores criar e modificar os layouts declarativos visualmente, sem a necessidade de edição manual dos arquivos XML. O Designer também fornece comentários em tempo real que permite ao desenvolvedor a avaliar as alterações de interface do usuário sem precisar reimplantar o aplicativo para um dispositivo ou em um emulador. Esses recursos de Designer podem acelerar o desenvolvimento de interface do usuário Android tremendamente.
Este artigo demonstra como usar o Designer do xamarin. Android para criar visualmente uma interface do usuário.

## <a name="walkthrough"></a>Passo a passo

O objetivo deste passo a passo é usar o Designer do Android para criar uma interface do usuário para um exemplo de aplicativo de navegador de cor. O aplicativo de navegador de cor apresenta uma lista de cores, seus nomes e valores RGB. Você aprenderá a adicionar widgets para o **superfície de Design** , bem como como dispor esses widgets visualmente. Depois disso, você aprenderá a modificar widgets interativamente na **superfície de Design** ou usando o Designer **propriedades** painel. Por fim, você verá como o design aparece quando o aplicativo é executado em um dispositivo ou emulador.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Criar um novo projeto

A primeira etapa é criar um novo projeto do xamarin. Android. Inicie o Visual Studio, clique em **novo projeto...** e escolha o **Visual C#\# > Android > aplicativo Android (Xamarin)** modelo.
Nomeie o novo aplicativo **Designer** e clique em **Okey**.

[![Aplicativo do Android em branco](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

No **novo aplicativo Android** caixa de diálogo, escolha **aplicativo em branco** e clique em **Okey**:

[![Selecionando o modelo de aplicativo do Android em branco](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>Adicionando um layout

A próxima etapa é criar uma **LinearLayout** que irá armazenar elementos de interface do usuário. Clique com botão direito **recursos/layout** na **Gerenciador de soluções** e selecione **Adicionar > Novo Item...** . No **Adicionar Novo Item** caixa de diálogo, selecione **Layout do Android**. Nomeie o arquivo **list_item** e clique em **Add**:

[![Novo layout](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

O novo **list_item** layout será exibido no Designer. Observe que dois painéis são exibidos &ndash; as *superfície de Design* para o **list_item** está visível no painel esquerdo, enquanto seu código-fonte XML é mostrado no painel à direita. Você pode trocar as posições do **superfície de Design** e **fonte** painéis clicando o **painéis de permuta** ícone localizado entre os dois painéis:

[![Exibição do Designer](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Dos **modo de exibição** menu, clique em **Other Windows > Document Outline** para abrir o **Document Outline**. O **Document Outline** mostra que o layout no momento, contém uma única **LinearLayout** widget:

[![Estrutura de tópicos do documento](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

A próxima etapa é criar a interface do usuário para o aplicativo de navegador de cor dentro deste `LinearLayout`.

### <a name="creating-the-list-item-user-interface"></a>Criando a interface de usuário do Item de lista

Se o **caixa de ferramentas** painel não estiver visível, clique no **caixa de ferramentas** guia à esquerda. No **caixa de ferramentas**, role para baixo até a **imagens e mídia** seção e role mais para baixo até localizar um `ImageView`:

[![Localize o ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

Como alternativa, você pode inserir *ImageView* na barra de pesquisa para localizar o `ImageView`:

[![Pesquisa ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Arraste esta `ImageView` na superfície de Design (isso `ImageView` será usado para exibir uma amostra de cor no aplicativo de navegador de cor):

[![ImageView na tela](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Em seguida, arraste uma `LinearLayout (Vertical)` widget do **caixa de ferramentas** no Designer. Observe que um contorno azul indica os limites de adicionado `LinearLayout`. O **Document Outline** mostra que é um filho `LinearLayout`, localizado em `imageView1 (ImageView)`:

[![Contorno azul](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Quando você seleciona os `ImageView` no Designer, o contorno azul se move para envolver o `ImageView`. Além disso, a seleção é movida para `imageView1 (ImageView)` no **Document Outline**:

[![Selecione ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Em seguida, arraste uma `Text (Large)` widget do **caixa de ferramentas** em que o recém-adicionado `LinearLayout`. Observe que o Designer usa verde realça para indicar onde o novo widget será inserido:

[![Destaques de verde](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Em seguida, adicione uma `Text (Small)` widget abaixo o `Text (Large)` widget:

[![Adicionar widget de texto pequeno](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

Neste ponto, a superfície do Designer deve ser semelhante a captura de tela a seguir:

[![Layout do Designer](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Se os dois `textView` widgets não estão dentro `linearLayout1`, você pode arrastá-los para `linearLayout1` no **Document Outline** e posicione-os para que eles apareçam como mostrado na captura de tela anterior (recuado sob `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Organizando a interface do usuário

A próxima etapa é modificar a interface do usuário para exibir o `ImageView` à esquerda, com os dois `TextView` widgets empilhada à direita do `ImageView`.

1.  Selecione o `ImageView`.

2.  No **janela de propriedades**, insira *largura* na pesquisa de caixa e localize **largura de Layout**.

3.  Alterar o **largura de Layout** definir como `wrap_content`:

![Conteúdo do conjunto de quebra automática de linha](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Outra maneira de alterar o `Width` configuração é clicar no triângulo no lado direito do widget para alternar sua configuração de largura para `wrap_content`:

![Arrastar para definir a largura](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Clique no triângulo novamente retorna o `Width` definindo como `match_parent`. Em seguida, vá para o **Document Outline** painel e selecione a raiz `LinearLayout`:

[![Selecione a raiz LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Com a raiz `LinearLayout` selecionada, retornar para o **propriedades** painel, insira *orientação* para a pesquisa de caixa e localize o **orientação** configuração. Alteração **orientação** para `horizontal`:

![Selecione a orientação horizontal](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

Neste ponto, a superfície do Designer deve ser semelhante a captura de tela a seguir.
Observe que o `TextView` widgets foram movidos para a direita do `ImageView`:

[![Layout do Designer](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modificando o espaçamento

A próxima etapa é modificar as configurações de preenchimento e margem na interface do usuário para fornecer mais espaço entre os widgets. Selecione o `ImageView` na superfície de Design. No **propriedades** painel, digite `min` na caixa de pesquisa. Insira `70dp` para **altura Mín** e `50dp` para **largura mín.**:

[![Definir altura e largura](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

No **propriedades** painel, insira `padding` na pesquisa de caixa e insira `10dp` para **preenchimento**. Eles `minHeight`, `minWidth` e `padding` configurações adicionar preenchimento ao redor de todos os lados do `ImageView` e alongá-la verticalmente. Observe que o XML de layout muda conforme você insere esses valores:

[![Definir preenchimento](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

Com o botão direito inferior, esquerda e as configurações de preenchimento superior pode ser definida independentemente inserindo valores na **preenchimento inferior**, **preenchimento esquerdo**, **Right Padding**e  **Preenchimento superior** campos, respectivamente.
Por exemplo, defina a **preenchimento esquerdo** campo `5dp` e o **preenchimento inferior**, **direita de preenchimento**, e **preenchimento superior** campos para `10dp`:

[![Configurações de preenchimento personalizado](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Em seguida, ajustar a posição do `LinearLayout` widget que contém os dois `TextView` widgets. No **Document Outline**, selecione `linearLayout1`. No **propriedades** janela, digite `margin` na caixa de pesquisa. Definir **Layout margem inferior**, **Layout margem esquerda**, e **Layout margem superior** para `5dp`. Definir **Layout margem direita** para `0dp`:

[![Definir margens](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Removendo a imagem padrão

Porque o `ImageView` está sendo usado para exibir cores (em vez de imagens), a próxima etapa é remover a origem da imagem padrão adicionada pelo modelo.

1.  Selecione o `ImageView` sobre o **superfície do Designer**.

2.  Na **propriedades**, insira *src* na caixa de pesquisa.

3.  Clique no pequeno quadrado à direita do **Src** propriedade de configuração e selecione **redefinir**:

[![Limpe a configuração de src ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Isso remove `android:src="@android:drawable/ic_menu_gallery"` da origem XML para que `ImageView`.

### <a name="adding-a-listview-container"></a>Adicionando um contêiner de ListView

Agora que o **list_item** layout é definido, a próxima etapa é adicionar um `ListView` ao layout principal. Isso `ListView` conterá uma lista de **list_item**. 

No **Gerenciador de soluções**, abra **Resources/layout/activity_main.axml**. No **caixa de ferramentas**, localize o `ListView` widget e arraste-a para o **superfície de Design**. O `ListView` no Designer, ficará em branco, exceto para as linhas azuis que sua borda de estrutura de tópicos quando ele é selecionado. Você pode exibir o **Document Outline** para verificar se o **ListView** foi adicionado corretamente:

[![Novo ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

Por padrão, o `ListView` recebe um `Id` valor `@+id/listView1`.
Embora `listView1` ainda está selecionado na **Document Outline**, abra o **propriedades** painel, clique em **Organizar por**e selecione **categoria**.
Abra **principal**, localize o **Id** propriedade e altere seu valor para `@+id/myListView`:

[![Renomeie a id para myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

Neste ponto, a interface do usuário está pronta para uso.

### <a name="running-the-application"></a>Executando o aplicativo

Abra **MainActivity.cs** e substitua o código a seguir:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

Esse código usa um `ListView` adaptador para carregar as informações de cor e exibir esses dados na interface do usuário que acabou de ser criado. Para manter a brevidade do exemplo, as informações de cores são embutido no código em uma lista, mas o adaptador pode ser modificado para extrair informações de cor de uma fonte de dados ou para calculá-lo em tempo real. Para obter mais informações sobre `ListView` adaptadores, consulte [ListView](~/android/user-interface/layouts/list-view/index.md).

Crie e execute o aplicativo. Captura de tela a seguir está um exemplo de como o aplicativo é exibido durante a execução em um dispositivo:

[![Captura de tela final](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Criar um novo projeto

A primeira etapa é criar um novo projeto do xamarin. Android.

Inicie o Visual Studio para Mac e clique em **novo projeto...** . Escolha o **aplicativo Android** modelo e clique em **próxima**:

[![Aplicativo do Android em branco](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Nomeie o novo aplicativo **Designer**. Sob **plataformas de destino**, selecione **melhores e mais recentes** e clique em **próxima**:

[![Nome de aplicativo](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

Na próxima tela da caixa de diálogo, clique em **criar**.

### <a name="adding-a-layout"></a>Adicionando um layout

A próxima etapa é criar uma **LinearLayout** que irá armazenar elementos de interface do usuário.

No Visual Studio para Mac, clique com botão direito **recursos/layout** na **solução** preenchimento e selecione **Adicionar > novo arquivo...** . No **novo arquivo** caixa de diálogo, selecione **Android > Layout**. Nomeie o arquivo **list_item** e clique em **New**:

[![Novo layout](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Depois que esse arquivo é adicionado, o novo **list_item** layout é exibido na **superfície de Design** (se você vir a mensagem, *este projeto contém recursos que não foram compilados com êxito, renderização poderá ser afetada*, clique em **compilar > compilar tudo** para compilar o projeto):

[![Exibição do Designer](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Clique o **origem** guia na parte inferior do Designer para exibir o código-fonte XML para esse layout. Quando você clica o **Document Outline** guia à direita, ele mostra que o layout no momento, contém uma única **LinearLayout** widget:

[![XML Designer](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

A próxima etapa é criar a interface do usuário para o aplicativo de navegador de cor.

### <a name="creating-the-list-item-user-interface"></a>Criando a interface de usuário do Item de lista

Clique o **Designer** guia na parte inferior da tela para retornar para o **superfície do Designer de**. No **caixa de ferramentas** painel à direita, role para baixo até a **imagens e mídia** seção e localize `ImageView`:

[![Localize o ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

Como alternativa, você pode inserir *ImageView* na barra de pesquisa para localizar o `ImageView`:

[![Pesquisa ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Arraste esta `ImageView` até o **superfície de Design** (isso `ImageView` será usado para exibir uma amostra de cor no aplicativo de navegador de cor):

[![ImageView na tela](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Em seguida, arraste uma `LinearLayout (Vertical)` widget do **caixa de ferramentas** para o **superfície de Design**. Observe que um contorno azul indica os limites de adicionado `LinearLayout`. O **Document Outline** mostra que é um filho `LinearLayout`, localizado abaixo `imageView1 (ImageView)`:

[![Contorno azul](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Quando você seleciona os `ImageView` no Designer, o contorno azul se move para envolver o `ImageView`. Além disso, a seleção é movida para `imageView1 (ImageView)` no **Document Outline**:

[![Selecione ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Em seguida, arraste uma `Text (Large)` widget do **caixa de ferramentas** em que o recém-adicionado `LinearLayout`. Observe que, quando você arrasta o mouse para o **superfície de Design**, ele destaca onde o novo widget será inserido.
O `Text (Large)` widget deve estar localizado dentro `linearLayout1` conforme visto aqui:

[![Adicionar widget de texto grande](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Em seguida, adicione uma `Text (Small)` widget abaixo o `Text (Large)` widget. Neste ponto, o **superfície de Design** deve ser semelhante à seguinte captura de tela:

[![Adicionar widget de texto pequeno](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Se os dois `textView` widgets não estão dentro `linearLayout1`, você pode arrastá-los para `linearLayout1` no **Document Outline** e posicioná-los para que eles sejam exibidos conforme mostrado na captura de tela anterior (recuados sob `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Organizando a interface do usuário

A próxima etapa é modificar a interface do usuário para exibir o `ImageView` à esquerda, com os dois `TextView` widgets empilhada à direita do `ImageView`.

1.  Com o `ImageView` selecionado, clique em de **propriedades** guia.

2.  Logo abaixo do **propriedades** , clique em **Layout**.

3.  Role para baixo até **ViewGroup** e altere o `Width` definir como `wrap_content`:

[![Conteúdo do conjunto de quebra automática de linha](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Outra maneira de alterar o `Width` configuração é clicar no triângulo no lado direito do widget para alternar sua configuração de largura para `wrap_content`:

[![Arrastar para definir a largura](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Clique no triângulo novamente retorna o `Width` definindo como `match_parent`. Em seguida, vá para o **Document Outline** painel e selecione a raiz `LinearLayout`:

[![Selecione a raiz LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Com a raiz `LinearLayout` selecionada, retornar para o **Properties** guia e clique em **Widget**. Alterar o `Orientation` definindo como `horizontal` conforme mostrado abaixo. Neste ponto, o **superfície de Design** deve ser semelhante à seguinte captura de tela. Observe que o `TextView` widgets foram movidos para a direita do `ImageView`:

[![Selecione a orientação horizontal](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>Modificando o espaçamento

A próxima etapa é modificar as configurações de preenchimento e margem na interface do usuário para fornecer mais espaço entre os widgets. Selecione o `ImageView` e clique em de **Layout** guia sob **propriedades**. Alterar o `Min Width` para `50dp`, o `Min Height` à `70dp`e o `Padding` para `10dp`.
Isso se aplica a preenchimento ao redor de todos os lados do `ImageView` e elongates-la verticalmente:

[![Definir preenchimento](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

A parte superior, direita, inferior e as configurações de preenchimento à esquerda podem ser definidas independentemente digitando os valores para o `Top`, `Right`, `Bottom`, e `Left` preenchimento campos, respectivamente. Por exemplo, defina as `Left` para o valor de preenchimento `5dp` e o `Top`, `Right`, e `Bottom` preenchimento valores a serem `10dp`. Observe que o `Padding` configuração muda para uma lista separada por vírgulas destes valores:

[![Configurações de preenchimento personalizado](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Em seguida, ajustar a posição do `LinearLayout` widget que contém os dois `TextView` widgets. No **Document Outline**, selecione `linearLayout1`. No **propriedades** painel, selecione o **Layout** guia. Role para baixo até a **ViewGroup** seção e defina as `Left`, `Top`, `Right`, e `Bottom` margens para `5dp`, `5dp`, `0dp`, e `5dp` respectivamente:

[![Definir margens](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Removendo a imagem padrão

Porque o `ImageView` está sendo usado para exibir cores (em vez de imagens), a próxima etapa é remover a origem da imagem padrão adicionada pelo modelo.

1.  Selecione o `ImageView`.

2.  Clique o **Widget** guia sob **propriedades**.

3.  Limpar o `Src` configuração de forma que ele está em branco:

[![Limpe a configuração de src ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Isso remove `android:src="@android:drawable/ic_menu_gallery"` da origem XML para que `ImageView`.

### <a name="adding-a-listview-container"></a>Adicionando um contêiner de ListView

Agora que o **list_item** layout é definido, a próxima etapa é adicionar um `ListView` ao layout principal. Isso `ListView` conterá uma lista de **list_item**. 

No **Gerenciador de soluções**, abra **Resources/layout/Main.axml**.
Clique o `Button` widget (se houver) e excluí-lo. No **caixa de ferramentas**, localize o `ListView` widget e arraste-a para o **superfície de Design**.
O `ListView` no Designer, ficará em branco, exceto para as linhas azuis que sua borda de estrutura de tópicos quando ele é selecionado. Você pode exibir o **Document Outline** para verificar se o **ListView** foi adicionado corretamente:

[![Novo ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

Por padrão, o `ListView` recebe um `Id` valor `@+id/listView1`.
Embora `listView1` ainda está selecionado na **Document Outline**, abra o **propriedades** painel, clique em **Organizar por**e selecione **categoria**.
Abra **principal**, localize o **Id** propriedade e altere seu valor para `@+id/myListView`:

[![Renomeie a id para myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

Neste ponto, a interface do usuário está pronta para uso.

### <a name="running-the-application"></a>Executando o aplicativo

Abra **MainActivity.cs** e substitua o código a seguir:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

Esse código usa um `ListView` adaptador para carregar as informações de cor e exibir esses dados na interface do usuário que acabou de ser criado. Para manter a brevidade do exemplo, as informações de cores são embutido no código em uma lista, mas o adaptador pode ser modificado para extrair informações de cor de uma fonte de dados ou para calculá-lo em tempo real. Para obter mais informações sobre `ListView` adaptadores, consulte [ListView](~/android/user-interface/layouts/list-view/index.md).

Crie e execute o aplicativo. Captura de tela a seguir está um exemplo de como o aplicativo é exibido durante a execução em um dispositivo:

[![Captura de tela final](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>Resumo

Este artigo percorreu o processo de usar o Designer do xamarin. Android no Visual Studio para criar uma interface do usuário para um aplicativo básico.
Ele demonstrou como criar a interface para um único item em uma lista e, em seguida, ele ilustrado como adicionar widgets e expô-los visualmente.
Ele também explicou como atribuir recursos e, em seguida, definir várias propriedades esses widgets.
