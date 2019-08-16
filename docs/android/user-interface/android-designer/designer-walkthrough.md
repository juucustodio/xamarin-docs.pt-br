---
title: Usando o Xamarin. Designer Android
description: Este artigo é uma explicação do tutorial do Xamarin. Designer Android. Ele demonstra como criar uma interface do usuário para um pequeno aplicativo de navegador de cores; Essa interface do usuário é criada inteiramente no designer.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 71784e18b9becf9851ad9ed9d4730a82d0ff94f7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523657"
---
# <a name="using-the-xamarinandroid-designer"></a>Usando o Xamarin. Designer Android

_Este artigo é uma explicação do tutorial do Xamarin. Designer Android. Ele demonstra como criar uma interface do usuário para um pequeno aplicativo de navegador de cores; Essa interface do usuário é criada inteiramente no designer._


## <a name="overview"></a>Visão geral

As interfaces de usuário do Android podem ser criadas declarativamente usando arquivos XML ou programaticamente escrevendo código. O Xamarin. Designer Android permite que os desenvolvedores criem e modifiquem layouts declarativos visualmente, sem a necessidade de edição manual de arquivos XML. O designer também fornece comentários em tempo real que permitem que o desenvolvedor avalie as alterações da interface do usuário sem precisar reimplantar o aplicativo em um dispositivo ou em um emulador. Esses recursos do designer podem acelerar enormemente o desenvolvimento da interface do usuário do Android.
Este artigo demonstra como usar o Xamarin. Designer Android para criar visualmente uma interface do usuário.

> [!TIP]
> As versões mais recentes do Visual Studio dão suporte à abertura de arquivos .xml dentro do Android Designer.
>
> Tanto arquivos .axml quanto .xml são compatíveis com o Android Designer.

## <a name="walkthrough"></a>Passo a passo

O objetivo deste tutorial é usar o Designer Android para criar uma interface do usuário para um aplicativo de exemplo de navegador de cores. O aplicativo de navegador de cores apresenta uma lista de cores, seus nomes e seus valores RGB. Você aprenderá a adicionar widgets ao **design Surface** , bem como a formatar esses widgets visualmente. Depois disso, você aprenderá a modificar os widgets de forma interativa no **design Surface** ou usando o painel de **Propriedades** do designer. Por fim, você verá como o design se parece quando o aplicativo é executado em um dispositivo ou emulador.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Criar um novo projeto

A primeira etapa é criar um novo projeto Xamarin. Android. Inicie o Visual Studio, clique em **novo projeto...** e escolha o modelo do **aplicativo do Visual C\# > Android > Android (Xamarin)** .
Nomeie o novo aplicativo **DesignerWalkthrough** e clique em **OK**.

[![Aplicativo em branco do Android](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

Na caixa de diálogo **novo aplicativo Android** , escolha **aplicativo em branco** e clique em **OK**:

[![Selecionando o modelo de aplicativo em branco do Android](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>Adicionando um layout

A próxima etapa é criar um **LinearLayout** que conterá os elementos da interface do usuário. Clique com o botão direito do mouse em **recursos/layout** na **Gerenciador de soluções** e selecione **Adicionar > novo item...** . Na caixa de diálogo **Adicionar novo item** , selecione **layout do Android**. Nomeie o arquivo **list_item** e clique em **Adicionar**:

[![Novo layout](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

O novo layout **list_item** é exibido no designer. Observe que dois painéis são exibidos &ndash; a *design Surface* para o **list_item** está visível no painel esquerdo enquanto sua origem XML é mostrada no painel à direita. Você pode trocar as posições dos painéis **design Surface** e **Source** clicando no ícone **trocar painéis** localizado entre os dois painéis:

[![Exibição do designer](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

No menu **Exibir** , clique em **outra estrutura de tópicos do documento do Windows >** para abrir a **estrutura de tópicos do documento**. A **estrutura de tópicos do documento** mostra que o layout contém atualmente um único widget **LinearLayout** :

[![Estrutura de tópicos do documento](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

A próxima etapa é criar a interface do usuário para o aplicativo de navegador de cores `LinearLayout`dentro dele.

### <a name="creating-the-list-item-user-interface"></a>Criando a interface do usuário do item de lista

Se o painel **caixa de ferramentas** não estiver sendo exibido, clique na guia caixa de **ferramentas** à esquerda. Na **caixa de ferramentas**, role para baixo até a seção **imagens & mídia** e role para baixo até localizar `ImageView`um:

[![Localizar ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

Como alternativa, você pode inserir *ImageView* na barra de pesquisa para localizar `ImageView`:

[![Pesquisa do ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Arraste-o `ImageView` paraadesignSurface(issoseráusadoparaexibirumaamostradecornoaplicativodenavegador`ImageView` de cores):

[![ImageView na tela](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Em seguida, arraste `LinearLayout (Vertical)` um widget da **caixa de ferramentas** para o designer. Observe que um contorno azul indica os limites do adicionado `LinearLayout`. O **contorno do documento** mostra que se trata de `LinearLayout`um filho de `imageView1 (ImageView)`, localizado em:

[![Contorno azul](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Quando você seleciona o `ImageView` no designer, o contorno azul é movido para o `ImageView`surround do. Além disso, a seleção é movida `imageView1 (ImageView)` para na **estrutura de tópicos do documento**:

[![Selecionar ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Em seguida, arraste `Text (Large)` um widget da **caixa de ferramentas** para `LinearLayout`o recém-adicionado. Observe que o designer usa os destaques verdes para indicar onde o novo widget será inserido:

[![Destaques verdes](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Em seguida, adicione `Text (Small)` um widget abaixo `Text (Large)` do widget:

[![Adicionar Widget de texto pequeno](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

Neste ponto, a superfície do designer deve se parecer com a captura de tela a seguir:

[![Layout do designer](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Se os dois `textView` widgets não estiverem dentro `linearLayout1`do, você poderá arrastá `linearLayout1` -los para no contorno do **documento** e posicioná-los para que eles apareçam conforme mostrado na `linearLayout1`captura de tela anterior (recuado abaixo).


### <a name="arranging-the-user-interface"></a>Organizando a interface do usuário

A próxima etapa é modificar a interface do usuário para exibir `ImageView` a à esquerda, com os dois `TextView` widgets empilhados `ImageView`à direita do.

1. Selecione o `ImageView`.

2. Na **janela Propriedades**, insira *largura* na caixa de pesquisa e localize a **largura do layout**.

3. Altere a configuração de **largura** do `wrap_content`layout para:

![Definir conteúdo de encapsulamento](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Outra maneira de alterar a `Width` configuração é clicar no triângulo no lado direito do widget para alternar sua configuração de largura para: `wrap_content`

![Arraste para definir a largura](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Clicar no triângulo novamente retorna a `Width` configuração para `match_parent`. Em seguida, vá para o painel **estrutura de tópicos** do documento `LinearLayout`e selecione a raiz:

[![Selecionar LinearLayout raiz](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Com a raiz `LinearLayout` selecionada, retorne ao painel de **Propriedades** , digite *orientação* na caixa de pesquisa e localize a configuração de **orientação** . Altere a orientação `horizontal`para:

![Selecionar orientação horizontal](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

Neste ponto, a superfície do designer deve se parecer com a captura de tela a seguir.
Observe que os `TextView` widgets foram movidos para a direita `ImageView`do:

[![Layout do designer](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modificando o espaçamento

A próxima etapa é modificar as configurações de enchimento e margem na interface do usuário para fornecer mais espaço entre os widgets. Selecione o `ImageView` na superfície de design. No painel **Propriedades** , digite `min` na caixa de pesquisa. Insira `70dp` para a **altura mínima** e `50dp` para a **largura mín**.:

[![Definir altura e largura](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

No painel **Propriedades** , insira `padding` na caixa de pesquisa e insira `10dp` para **preenchimento**. Elas `minHeight`e as`padding`configurações adicionam preenchimento em todos os lados do eprolongamverticalmente.`ImageView` `minWidth` Observe que o layout XML muda à medida que você insere esses valores:

[![Definir preenchimento](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

As configurações de preenchimento inferior, esquerda, direita e superior podem ser definidas de forma independente inserindo valores nos campos de **preenchimento inferior**, **preenchimento esquerdo**, preenchimento à **direita**e **preenchimento superior** , respectivamente.
Por exemplo, defina o campo de **preenchimento à esquerda** como `5dp` e os campos de **preenchimento inferior**, **preenchimento direito**e **preenchimento superior** como `10dp`:

[![Configurações de preenchimento personalizado](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Em seguida, ajuste a posição do `LinearLayout` widget que contém os dois `TextView` widgets. Na **estrutura de tópicos**do documento `linearLayout1`, selecione. Na janela **Propriedades** , digite `margin` na caixa de pesquisa. Definir margem de **layout inferior**, **margem de layout à esquerda**e margem de `5dp` **layout superior** a. Definir **margem de layout** como `0dp`direita para:

[![Definir margens](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Removendo a imagem padrão

Como o `ImageView` está sendo usado para exibir cores (em vez de imagens), a próxima etapa é remover a origem da imagem padrão adicionada pelo modelo.

1. Selecione o `ImageView` na **superfície do designer**.

2. Em **Propriedades**, insira *src* na caixa de pesquisa.

3. Clique no pequeno quadrado à direita da configuração da propriedade **src** e selecione **Redefinir**:

[![Limpar a configuração de src ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Isso remove `android:src="@android:drawable/ic_menu_gallery"` do XML de origem para isso `ImageView`.

### <a name="adding-a-listview-container"></a>Adicionando um contêiner ListView

Agora que o layout **list_item** está definido, a próxima etapa é adicionar um `ListView` ao layout principal. Isso `ListView` conterá uma lista de **list_item**. 

No **Gerenciador de soluções**, abra Resources **/layout/activity_main. axml**. Na **caixa de ferramentas**, localize `ListView` o widget e arraste-o para a **design Surface**. O `ListView` no designer ficará em branco, exceto para linhas azuis que descrevem sua borda quando ela for selecionada. Você pode exibir a **estrutura de tópicos do documento** para verificar se o **ListView** foi adicionado corretamente:

[![Novo ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

Por padrão, o `ListView` recebe um `Id` valor de `@+id/listView1`.
Embora `listView1` ainda esteja selecionado na **estrutura de tópicos do documento**, abra o painel **Propriedades** , clique em **organizar por**e selecione **categoria**.
Abra **Main**, localize a propriedade **ID** e altere seu valor para `@+id/myListView`:

[![Renomear ID para mylistview](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

Neste ponto, a interface do usuário está pronta para uso.

### <a name="running-the-application"></a>Executando o aplicativo

Abra **MainActivity.cs** e substitua seu código pelo seguinte:

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

Esse código usa um adaptador `ListView` personalizado para carregar informações de cores e exibir esses dados na interface do usuário que acabou de ser criada. Para manter este exemplo curto, as informações de cor são embutidas em código em uma lista, mas o adaptador pode ser modificado para extrair informações de cor de uma fonte de dados ou para calculá-la de forma dinâmica. Para obter mais informações `ListView` sobre adaptadores, consulte [ListView](~/android/user-interface/layouts/list-view/index.md).

Crie e execute o aplicativo. A captura de tela a seguir é um exemplo de como o aplicativo aparece quando executado em um dispositivo:

[![Captura de tela final](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Criar um novo projeto

A primeira etapa é criar um novo projeto Xamarin. Android.

Inicie o Visual Studio para Mac e clique em **novo projeto...** . Escolha o modelo de **aplicativo do Android** e clique em **Avançar**:

[![Aplicativo em branco do Android](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Nomeie o novo aplicativo **DesignerWalkthrough**. Em **plataformas de destino**, selecione **mais recente e melhor** e clique em **Avançar**:

[![Nomear aplicativo](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

Na próxima tela de diálogo, clique em **criar**.

### <a name="adding-a-layout"></a>Adicionando um layout

A próxima etapa é criar um **LinearLayout** que conterá os elementos da interface do usuário.

Em Visual Studio para Mac, clique com o botão direito do mouse em **recursos/layout** no painel de **solução** e selecione **Adicionar > novo arquivo...** . Na caixa de diálogo **novo arquivo** , selecione **Android > layout**. Nomeie o arquivo **list_item** e clique em **novo**:

[![Novo layout](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Depois que esse arquivo for adicionado, o layout do novo **list_item** será exibido na **design Surface** (se você vir a mensagem, *esse projeto conterá recursos que não foram compilados com êxito, a renderização poderá ser afetada*, clique em **criar > Compilar tudo** para compilar o projeto):

[![Exibição do designer](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Clique na guia **origem** na parte inferior do designer para exibir a origem XML desse layout. Quando você clica na guia **estrutura de tópicos do documento** à direita, ela mostra que o layout contém atualmente um único widget **LinearLayout** :

[![XML do designer](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

A próxima etapa é criar a interface do usuário para o aplicativo de navegador de cores.

### <a name="creating-the-list-item-user-interface"></a>Criando a interface do usuário do item de lista

Clique na guia **Designer** na parte inferior da tela para retornar à superfície do **Designer**. No painel **caixa de ferramentas** à direita, role para baixo até a seção **imagens & mídia** e `ImageView`localize:

[![Localizar ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

Como alternativa, você pode inserir *ImageView* na barra de pesquisa para localizar `ImageView`:

[![Pesquisa do ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Arraste-o `ImageView` paraadesignSurface(issoseráusadoparaexibirumaamostradecornoaplicativodenavegadordecores`ImageView` ):

[![ImageView na tela](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Em seguida, arraste `LinearLayout (Vertical)` um widget da **caixa de ferramentas** para a **design Surface**. Observe que um contorno azul indica os limites do adicionado `LinearLayout`. A **estrutura de tópicos do documento** mostra que se trata `LinearLayout`de um filho `imageView1 (ImageView)`de, localizado abaixo:

[![Contorno azul](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Quando você seleciona o `ImageView` no designer, o contorno azul é movido para o `ImageView`surround do. Além disso, a seleção é movida `imageView1 (ImageView)` para na **estrutura de tópicos do documento**:

[![Selecionar ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Em seguida, arraste `Text (Large)` um widget da **caixa de ferramentas** para `LinearLayout`o recém-adicionado. Observe que, à medida que você arrasta o mouse para a **design Surface**, ele realça onde o novo widget será inserido.
O `Text (Large)` widget deve estar localizado dentro `linearLayout1` do, como mostrado aqui:

[![Adicionar widget de texto grande](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Em seguida, adicione `Text (Small)` um widget abaixo `Text (Large)` do widget. Neste ponto, o **design Surface** deve se parecer com a captura de tela a seguir:

[![Adicionar Widget de texto pequeno](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Se os dois `textView` widgets não estiverem dentro `linearLayout1`do, você poderá arrastá `linearLayout1` -los para na **estrutura de tópicos do documento** e posicioná-los para que eles apareçam conforme mostrado na `linearLayout1`captura de tela anterior (recuado abaixo).


### <a name="arranging-the-user-interface"></a>Organizando a interface do usuário

A próxima etapa é modificar a interface do usuário para exibir `ImageView` a à esquerda, com os dois `TextView` widgets empilhados `ImageView`à direita do.

1. Com o `ImageView` selecionado, clique na guia **Propriedades** .

2. Logo abaixo da guia **Propriedades** , clique em **layout**.

3. Role para baixo até o **modo** de exibição `Width` e altere `wrap_content`a configuração para:

[![Definir conteúdo de encapsulamento](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Outra maneira de alterar a `Width` configuração é clicar no triângulo no lado direito do widget para alternar sua configuração de largura para: `wrap_content`

[![Arraste para definir a largura](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Clicar no triângulo novamente retorna a `Width` configuração para `match_parent`. Em seguida, vá para o painel **estrutura de tópicos** do documento `LinearLayout`e selecione a raiz:

[![Selecionar LinearLayout raiz](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Com a raiz `LinearLayout` selecionada, retorne à guia **Propriedades** e clique em **widget**. Altere a `Orientation` configuração para `horizontal` conforme mostrado abaixo. Neste ponto, o **design Surface** deve se parecer com a captura de tela a seguir. Observe que os `TextView` widgets foram movidos para a direita `ImageView`do:

[![Selecionar orientação horizontal](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>Modificando o espaçamento

A próxima etapa é modificar as configurações de enchimento e margem na interface do usuário para fornecer mais espaço entre os widgets. Selecione o `ImageView` e clique na guia **layout** em **Propriedades**. `Min Width` Altere para `50dp`, o `Min Height` para eo`Padding` para .`10dp` `70dp`
Isso aplica o preenchimento em todos os lados `ImageView` do e o alonga verticalmente:

[![Definir preenchimento](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

As configurações de preenchimento superior, direito, inferior e esquerda podem ser definidas de forma independente inserindo valores `Top`nos `Right`campos `Bottom`de preenchimento `Left` ,, e, respectivamente. Por exemplo, defina o `Left` valor de preenchimento `5dp` como e `Top`os `Right`valores de `Bottom` preenchimento, e `10dp`como. Observe que a `Padding` configuração muda para uma lista separada por vírgulas desses valores:

[![Configurações de preenchimento personalizado](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Em seguida, ajuste a posição do `LinearLayout` widget que contém os dois `TextView` widgets. Na **estrutura de tópicos**do documento `linearLayout1`, selecione. No painel **Propriedades** , selecione a guia **layout** . Role para baixo até a seção **modo de exibição** e `Left`defina `Top`as `Right` `Bottom` margens,, e como `5dp`, `5dp`, `0dp`e `5dp` respectivamente:

[![Definir margens](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Removendo a imagem padrão

Como o `ImageView` está sendo usado para exibir cores (em vez de imagens), a próxima etapa é remover a origem da imagem padrão adicionada pelo modelo.

1. Selecione o `ImageView`.

2. Clique na guia **widget** em **Propriedades**.

3. Desmarque `Src` a configuração para que ela fique em branco:

[![Limpar a configuração de src ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Isso remove `android:src="@android:drawable/ic_menu_gallery"` do XML de origem para isso `ImageView`.

### <a name="adding-a-listview-container"></a>Adicionando um contêiner ListView

Agora que o layout **list_item** está definido, a próxima etapa é adicionar um `ListView` ao layout principal. Isso `ListView` conterá uma lista de **list_item**. 

No **Gerenciador de soluções**, abra Resources **/layout/Main. axml**.
Clique no `Button` widget (se houver) e exclua-o. Na **caixa de ferramentas**, localize `ListView` o widget e arraste-o para a **design Surface**.
O `ListView` no designer ficará em branco, exceto para linhas azuis que descrevem sua borda quando ela for selecionada. Você pode exibir a **estrutura de tópicos do documento** para verificar se o **ListView** foi adicionado corretamente:

[![Novo ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

Por padrão, o `ListView` recebe um `Id` valor de `@+id/listView1`.
Embora `listView1` ainda esteja selecionado na **estrutura de tópicos do documento**, abra o painel **Propriedades** , clique em **organizar por**e selecione **categoria**.
Abra **Main**, localize a propriedade **ID** e altere seu valor para `@+id/myListView`:

[![Renomear ID para mylistview](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

Neste ponto, a interface do usuário está pronta para uso.

### <a name="running-the-application"></a>Executando o aplicativo

Abra **MainActivity.cs** e substitua seu código pelo seguinte:

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

Esse código usa um adaptador `ListView` personalizado para carregar informações de cores e exibir esses dados na interface do usuário que acabou de ser criada. Para manter este exemplo curto, as informações de cor são embutidas em código em uma lista, mas o adaptador pode ser modificado para extrair informações de cor de uma fonte de dados ou para calculá-la de forma dinâmica. Para obter mais informações `ListView` sobre adaptadores, consulte [ListView](~/android/user-interface/layouts/list-view/index.md).

Crie e execute o aplicativo. A captura de tela a seguir é um exemplo de como o aplicativo aparece quando executado em um dispositivo:

[![Captura de tela final](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>Resumo

Este artigo descreveu o processo de usar o Xamarin. Designer Android no Visual Studio para criar uma interface do usuário para um aplicativo básico.
Ele demonstrou como criar a interface para um único item em uma lista e ilustra como adicionar widgets e esquematiza-las visualmente.
Ele também explicou como atribuir recursos e, em seguida, definir várias propriedades nesses widgets.
